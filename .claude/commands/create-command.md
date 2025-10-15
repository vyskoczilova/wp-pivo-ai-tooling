---
allowed-tools: Bash(mkdir:*), Write, Edit, Read, LS, WebFetch
description: Create custom Claude Code slash commands with intelligent scaffolding
argument-hint: "[path/command-name] [<instructions>]"
model: claude-opus-4-1
# Metadata (ignored by Claude Code, but useful for humans)
author: Karolína Vyskočilová <karolina@kybernaut.cz>
created: 2025-09-01
updated: 2025-10-15
---

# Create Custom Slash Command

This command intelligently crafts custom Claude Code slash commands based on your specifications, following best practices and current documentation standards.

## Workflow

### 1. Parse Arguments
- **$1**: Target path and command name (e.g., `myproject/format-code`)
- **$2**: Natural language instructions describing the command's purpose and behavior

### 2. Analyze Requirements

Before creating the command, analyze the provided instructions to determine:

1. **Core Functionality**: What should the command accomplish?
2. **Required Tools**: Which tools are necessary? Consider:
   - File operations: `Write`, `Edit`, `Read`, `LS`
   - System operations: `Bash`, `RunCommand`
   - Web operations: `WebFetch`, `WebSearch`
   - Language-specific: `Python`, `Node`, etc.
3. **Arguments**: What parameters should the command accept?
4. **Model Selection**: If not specified, omit (uses default)
5. **Security Considerations**: Any restrictions needed?
6. If anything from the user input and intention is unclear or needs more specification ask before crafting the command.

### 3. Fetch Latest Documentation

Check https://docs.claude.com/en/docs/claude-code/slash-commands#custom-slash-commands (or even better, if available use context7 library anthropics/claude-code) for the most current:
- Syntax requirements
- Available tools list
- Best practices
- Security guidelines

### 4. Generate Command Structure

Create a well-structured command file with:

#### Header Format
```yaml
---
allowed-tools: precisely-scoped-tools
description: clear-actionable-description
argument-hint: "[descriptive-parameter-hints]"
model: only-if-explicitly-specified
# Metadata (ignored by Claude Code, but useful for humans)
author: git-user-name <git-user-email>
created: current-date
updated: current-date
---
```

Important:

- Make sure you have both opening and closing --- markers.
- YAML is very sensitive to indentation. Ensure you're using consistent spacing (typically 2 spaces, never tabs).
- Every key in YAML needs a colon and space after it.
- Use comma-separated list for allowed-tools, no brackets needed
- Wrap argument-hint in quotes when using brackets: "[message] [--flag]"

#### Body Structure
- **Purpose Statement**: Clear explanation of what the command does
- **Usage Examples**: Both correct and incorrect usage patterns
- **Step-by-Step Instructions**: Detailed workflow with error handling
- **Performance Considerations**: Optimization tips
- **Security Notes**: Any important restrictions or validations

### 5. Validation Checklist

Before saving, validate:
- [ ] Tools are minimally scoped (avoid wildcards unless necessary)
- [ ] Description is under 100 characters and action-oriented
- [ ] Argument hints clearly indicate required vs optional parameters
- [ ] Instructions include error handling for common failure cases
- [ ] Examples demonstrate both simple and complex use cases

### 6. Save Command

1. Extract path and filename from $1:
   - If $1 contains `/`: split into path and command-name
   - If no `/`: use `.claude/commands/` as default path
2. Ensure `.claude/commands/` directory exists at specified location
3. Get git user information for author field
4. Create `[command-name].md` file with generated content including metadata:
   - author: [git-user-name] <[git-user-email]>
   - created: [current-date]
   - updated: [current-date]
5. Verify file was created successfully

## Examples

### Good Example - Creating a "format-python" command:
```bash
/create-command "format-python" "Create a command that formats Python files using Black and isort, with optional type checking using mypy"
```

This would generate:
```markdown
---
allowed-tools: Python(black:format), Python(isort:sort), Python(mypy:typecheck), Read, Write
description: Format and optionally type-check Python files
argument-hint: <file-or-directory> [--check-types]
# Metadata (ignored by Claude Code, but useful for humans)
author: Karolína Vyskočilová <karolina@kybernaut.cz>
created: 2025-09-25
updated: 2025-09-25
---

# Format Python Code

Formats Python files using Black and isort, with optional type checking.

## Usage
`/format-python main.py --check-types`
`/format-python src/`

## Instructions
1. Identify target files (single file or directory)
2. Run Black for code formatting
3. Run isort for import sorting
4. If --check-types flag present, run mypy
5. Report any issues found
...
```

### Bad Example - Vague instructions:
```bash
/create-command "do-stuff" "make it work better"
```
This lacks specificity and would require clarification.

## Error Handling

- If $1 is missing: Prompt for command name
- If $2 is missing: Prompt for instructions
- If instructions are vague: Ask clarifying questions
- If unsure about tool requirements: Suggest options with explanations
- If command already exists: Offer to update or create with different name

## Performance & Maintenance Notes

- Prefer specific tool permissions over wildcards
- Include version compatibility notes when relevant
- Add update instructions if command depends on external services
- Consider command composability with other slash commands
- Document any assumptions or limitations clearly