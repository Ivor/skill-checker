Help the user understand an existing skill check mapping in their skill-checker.json configuration.

## Steps:

1. **Read the current configuration:**
   - Load `.claude/hooks/skill-checker.json`
   - If it doesn't exist, inform the user and suggest `/setup-skill-checker`

2. **List available mappings:**
   - Show all configured mappings with their skill names
   - Ask the user which mapping they want explained (or explain all if they prefer)

3. **For each mapping to explain, break down:**

   **a) skill**
   - What skill will be required when this mapping matches

   **b) tool_matcher**
   - Explain the regex pattern in plain language
   - List specific tools that would match (e.g., "This matches Write, Edit, or MultiEdit tools")
   - Give examples of tools that would NOT match

   **c) file_patterns** (if present)
   - Explain each regex pattern in plain language
   - Give concrete examples of file paths that WOULD match
   - Give concrete examples of file paths that would NOT match
   - Note: patterns use OR logic (any pattern matching triggers the check)

   **d) tool_input_matcher** (if present)
   - Explain the regex pattern in plain language
   - Give examples of tool inputs that would and wouldn't match

4. **Provide a summary:**
   - In one sentence, describe when this skill will be required
   - Example: "The `liveview-templates` skill will be required when editing any `.heex` file or any `.ex` file inside a `/live/` directory"

5. **Offer next steps:**
   - Ask if they want to modify this mapping
   - Ask if they want to add additional patterns
   - Ask if they want to remove this mapping
