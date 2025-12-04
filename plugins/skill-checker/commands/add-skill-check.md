Help the user add a new skill check mapping to their skill-checker.json configuration.

## Steps:

1. **Check prerequisites:**
   - Verify `.claude/hooks/skill-checker.json` exists (if not, suggest running `/setup-skill-checker` first)
   - Read the current configuration to see existing mappings

2. **Discover available skills:**
   - List all available skills the user has installed
   - Compare against skills already configured in skill-checker.json
   - Show which skills are NOT yet configured (these are candidates for new mappings)

3. **Guide the user through creating a new mapping:**

   Ask them to choose or specify:

   **a) skill** (required)
   - Which skill should be required?

   **b) tool_matcher** (required)
   - What tool(s) should trigger this check?
   - Common patterns: `Write|Edit|MultiEdit` for file editing, `Bash` for commands, `mcp__*` for MCP tools
   - Ask: "What tool operations should require this skill?"

   **c) file_patterns** (optional)
   - Should this only apply to specific files or directories?
   - Ask: "Describe which files this should apply to (e.g., 'all .heex files', 'files in the lib/workers directory')"
   - Help convert their description into regex patterns

   **d) tool_input_matcher** (optional)
   - Should this only apply when certain content is in the tool input?
   - Ask: "Should this only trigger for specific commands or content? (e.g., 'only when running mix test')"
   - Help convert their description into a regex pattern

4. **Build the regex patterns:**
   - Based on user's plain-language descriptions, construct appropriate regex
   - Explain what the regex will match
   - Show examples of paths/inputs that would and wouldn't match

5. **Add the mapping:**
   - Show the complete mapping object before adding
   - Add it to the skill-checker.json file
   - Confirm the addition was successful

Remember: file_patterns and tool_input_matcher are optional - only include them if the user needs to narrow down when the skill is required.
