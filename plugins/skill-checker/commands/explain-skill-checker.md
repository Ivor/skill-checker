Help the user learn how the skill-checker plugin works and how to configure it.

## Steps:

1. **Explore the plugin installation directory** at `~/.claude/plugins/` to find the skill-checker plugin files

2. **Locate key files:**
   - The hook implementation script (`.sh`) - this contains all the matching logic
   - Any example/template configuration files to understand the expected format

3. **Read the example configuration** to learn the JSON structure and available fields

4. **Read the hook script** to understand:
   - How mappings are evaluated (what gets matched against what)
   - Where project-level config should be placed
   - Default/fallback behavior

5. **Check the current project** for existing configuration at `.claude/hooks/`

6. **Synthesize findings** - combine the schema from examples with the logic from the script to form a complete picture

## Key Insight

Plugin behavior is defined in the plugin installation, but project-specific mappings are configured per-project in `.claude/hooks/`.

Present your findings clearly to the user with examples of how to add new mappings.
