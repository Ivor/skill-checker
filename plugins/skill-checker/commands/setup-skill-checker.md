Help the user set up or review their skill-checker.json configuration file.

## Steps:

1. Check if `.claude/hooks/skill-checker.json` already exists in the current project

2. **If it exists:**
   - Read and display the current configuration
   - Explain what each mapping does (which skills are required for which tools/files)
   - Offer to help them add, modify, or remove mappings

3. **If it doesn't exist:**
   - Create the `.claude/hooks/` directory if needed
   - Copy the example config from the plugin's config-templates directory
   - Create it at `.claude/hooks/skill-checker.json`
   - Show them the new config and explain what each mapping does
   - Offer to customize it for their project's needs

Be helpful and guide them through the process step by step.
