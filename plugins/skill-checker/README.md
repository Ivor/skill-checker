# Skill Checker Plugin

A Claude Code hook that enforces skill activation before using specific tools. This ensures Claude automatically loads required skills (like `elixir-best-practices`, `test-debugger`, `liveview-templates`) before editing files or running commands.

## What It Does

The skill-checker hook intercepts tool usage and checks if required skills are loaded in the conversation. If a tool use matches your configured mappings but required skills are not active, the hook:

1. **Denies the tool execution**
2. **Prompts Claude to load the missing skills**
3. **Allows Claude to retry after loading skills**

This prevents Claude from working on code without the proper context and best practices loaded.

## ⚠️ Required Configuration File Location

**CRITICAL**: After installing this plugin, you MUST create a configuration file at:

```
<your-project-root>/.claude/hooks/skill-checker.json
```

This file defines which skills are required for your project. Each project needs its own config file because different projects have different requirements.

**Quick Setup**:

```bash
# Run these commands in your project root directory
mkdir -p .claude/hooks
cp ~/.claude/plugins/marketplaces/skill-checker-marketplace/plugins/skill-checker/config-templates/skill-checker.example.json .claude/hooks/skill-checker.json
# Edit the file to customize for your project
```

Without this file, the hook will not enforce any skill requirements (fail-open behavior for safety).

## Installation

### Step 1: Add the Marketplace and Install the Plugin

```bash
# Add the skill-checker marketplace from GitHub
claude plugin marketplace add https://github.com/Ivor/skill-checker

# Install the skill-checker plugin
claude plugin install skill-checker@skill-checker-marketplace
```

### Step 2: Configure for Your Project

**IMPORTANT**: This plugin requires project-specific configuration. After installation, you must create a `skill-checker.json` file in your project's `.claude/hooks/` directory.

**Quick Setup** - Run the setup command in your project:

```bash
/skill-checker:setup-skill-checker
```

Or manually:

1. Create the hooks directory if it doesn't exist:

   ```bash
   mkdir -p .claude/hooks
   ```

2. Copy the example config from this plugin's `config-templates/` directory or create your own:

   ```bash
   cp ~/.claude/plugins/marketplaces/skill-checker-marketplace/plugins/skill-checker/config-templates/skill-checker.example.json .claude/hooks/skill-checker.json
   ```

3. Edit `.claude/hooks/skill-checker.json` to match your project's needs (see Configuration section below)

## Configuration

The `skill-checker.json` file defines mappings between tool usage patterns and required skills.

### Configuration Structure

```json
{
  "mappings": [
    {
      "skill": "skill-name",
      "tool_matcher": "ToolName|AnotherTool",
      "tool_input_matcher": "optional-regex-pattern",
      "file_patterns": [".*\\.ext$", "^path/.*\\.ex$"]
    }
  ]
}
```

### Configuration Fields

- **`skill`** (required): The name of the skill to require
- **`tool_matcher`** (required): Regex pattern matching tool names (e.g., `"Write|Edit|MultiEdit"`)
- **`tool_input_matcher`** (optional): Regex pattern matching tool input JSON (e.g., `"mix test"`)
- **`file_patterns`** (optional): Array of **regex patterns** matching file paths (e.g., `[".*\\.heex$", ".*/live/.*\\.ex$"]`)

### Example Configurations

#### Elixir Project

```json
{
  "mappings": [
    {
      "skill": "liveview-templates",
      "tool_matcher": "Write|Edit|MultiEdit",
      "file_patterns": [".*\\.heex$", ".*/live/.*\\.ex$"]
    },
    {
      "skill": "elixir-best-practices",
      "tool_matcher": "Write|Edit|MultiEdit",
      "file_patterns": ["^lib/.*\\.ex$", ".*/test/.*\\.exs$"]
    },
    {
      "skill": "test-debugger",
      "tool_matcher": "Bash",
      "tool_input_matcher": "mix test"
    }
  ]
}
```

#### JavaScript/TypeScript Project

```json
{
  "mappings": [
    {
      "skill": "react-best-practices",
      "tool_matcher": "Write|Edit|MultiEdit",
      "file_patterns": ["^src/.*\\.(tsx|jsx)$"]
    },
    {
      "skill": "typescript-patterns",
      "tool_matcher": "Write|Edit|MultiEdit",
      "file_patterns": ["^src/.*\\.tsx?$"]
    }
  ]
}
```

#### MCP Tool Enforcement

```json
{
  "mappings": [
    {
      "skill": "tidewave",
      "tool_matcher": "mcp__tidewave__get_logs"
    }
  ]
}
```

## How It Works

### Pattern Matching

1. **Tool Matcher**: Uses regex to match tool names exactly as they appear (case-sensitive)

   - `"Write"` - matches only the Write tool
   - `"Write|Edit"` - matches either Write or Edit
   - `"Bash"` - matches the Bash tool
   - `"mcp__.*"` - matches any MCP tool

2. **Tool Input Matcher** (optional): Uses regex to match the JSON representation of tool inputs

   - Useful for matching specific commands: `"mix test"`, `"npm run"`
   - Matches against the entire tool input JSON string

3. **File Patterns** (optional): Uses regex patterns to match file paths
   - `.*\.ex$` - all .ex files at any level
   - `^lib/.*\.ex$` - .ex files only under lib/ directory
   - `.*/test/.*\.exs$` - .exs files in any test directory

   **Note**: Patterns are matched against the relative path from your project root. Remember to escape special regex characters like `.` with `\.`

### Conversation Continuation

The hook intelligently handles conversation continuation:

- When a conversation is resumed, only skills loaded **after** the continuation are checked
- This prevents false positives from skills loaded in previous conversation sessions

### Fail-Open Safety

The hook is designed to fail-open (allow tool use) if:

- The config file is missing or malformed
- `jq` is not installed
- The transcript is unavailable
- Any other error occurs

This ensures Claude can continue working even if the hook encounters issues.

## Troubleshooting

### Hook Not Working

1. **Check config file exists**:

   ```bash
   ls -la .claude/hooks/skill-checker.json
   ```

2. **Validate JSON syntax**:

   ```bash
   jq '.' .claude/hooks/skill-checker.json
   ```

3. **Check debug logs**:
   ```bash
   tail -f /tmp/skill-checker-debug.log
   ```

### Skills Not Being Detected

- Ensure skill names in your config exactly match the skill names used in your Skills directory
- Check that skills are being loaded AFTER conversation continuation (if applicable)
- Review debug logs to see what skills were detected

### Hook Allowing Tool Use When It Shouldn't

- Verify your `tool_matcher` patterns are case-sensitive and exact
- Check that file `patterns` are using proper glob syntax
- Test patterns match relative paths from your project root

## Requirements

- **jq**: Required for JSON parsing. Install with:
  - macOS: `brew install jq`
  - Linux: `apt-get install jq` or `yum install jq`

## Development

### Project Structure

```
skill-checker/
├── .claude-plugin/
│   └── plugin.json          # Plugin manifest
├── hooks/
│   ├── hooks.json           # Hook configuration
│   └── skill-checker.sh     # Hook script
├── config-templates/
│   └── skill-checker.example.json  # Example config
└── README.md                # This file
```

### Testing

To test the hook:

1. Create a test project with `.claude/hooks/skill-checker.json`
2. Configure a simple mapping (e.g., require a skill for editing .txt files)
3. Try editing a file without loading the skill - it should be blocked
4. Load the skill and retry - it should succeed

## License

MIT

## Support

For issues, questions, or contributions, please open an issue on the plugin repository.
