---
allowed-tools: Bash(git:*), TodoWrite
description: Analyze changes and create intelligent commit with all unstaged files
argument-hint: "[commit-type]"
model: claude-sonnet-4-5
# Metadata (ignored by Claude Code, but useful for humans)
author: Daniel Mejta <daniel@wpify.io>
created: 2025-10-15
updated: 2025-10-15
---

# Intelligent Git Commit

Analyzes all uncommitted changes in the repository and creates a comprehensive, well-structured commit message that accurately describes the changes. Automatically stages and commits all modified files.

## Usage Examples

### Basic usage (auto-detect commit type):
```bash
/commit
```

### Specify commit type:
```bash
/commit feature
/commit fix
/commit refactor
```

## Workflow

### 1. Assess Current State
First, check the repository status to understand what changes need to be committed:
- Run `git status` to see all untracked and modified files
- Run `git diff` to examine unstaged changes
- Run `git diff --staged` to examine already staged changes
- Run `git log -3 --oneline` to understand recent commit style

### 2. Analyze Changes
Carefully review all changes to understand:
- **What changed**: Specific files, functions, components modified
- **Why it changed**: The purpose and context of the changes
- **Impact**: How these changes affect the system
- **Type of change**: Feature, fix, refactor, docs, test, style, chore

### 3. Generate Commit Message
Create a commit message following these best practices:

#### Structure:
```
<type>(<scope>): <subject>

<body>

<footer>
```

#### Guidelines:
- **Subject line** (first line):
  - Maximum 72 characters
  - Start with type: feat, fix, refactor, docs, test, style, chore, perf
  - Include scope if changes are localized (e.g., `feat(auth):` or `fix(api):`)
  - Use imperative mood ("add" not "added" or "adds")
  - Don't end with a period

- **Body** (optional, for complex changes):
  - Separate from subject with blank line
  - Explain what and why, not how
  - Wrap at 72 characters
  - Use bullet points for multiple changes

- **Footer** (when applicable):
  - Breaking changes: start with `BREAKING CHANGE:`
  - Issue references: `Fixes #123` or `Closes #456`

#### Message Quality Checks:
- Is it clear what changed without looking at the code?
- Does it explain the motivation for the change?
- Is it comprehensive without being excessively long?
- Does it follow the project's existing commit style?

### 4. Stage and Commit
- Stage all files: `git add -A`
- Create the commit with the generated message
- Verify success with `git status`

### 5. Error Handling

#### No changes to commit:
If `git status` shows no changes, inform the user that there are no uncommitted changes to commit.

#### Pre-commit hook failures:
If the commit fails due to pre-commit hooks:
1. Check what the hook modified
2. If files were auto-fixed (formatting, linting):
   - Review the changes: `git diff`
   - Re-stage the files: `git add -A`
   - Retry the commit with the same message
3. If hooks report errors that need manual fixing:
   - Report the specific errors to the user
   - Ask if they want you to attempt to fix them

#### Large number of changes:
If there are changes across many unrelated areas:
- Suggest breaking into multiple logical commits
- Ask user preference: single comprehensive commit or multiple focused commits

## Examples of Good Commit Messages

### Feature Addition:
```
feat(auth): add OAuth2 integration with Google

- Implement OAuth2 flow with Google provider
- Add token refresh mechanism
- Update user model to store OAuth credentials
- Add configuration for OAuth client ID/secret

Closes #234
```

### Bug Fix:
```
fix(api): resolve race condition in data synchronization

The previous implementation could cause duplicate entries when
multiple requests arrived simultaneously. This fix adds proper
locking mechanism to ensure atomic operations.

Fixes #567
```

### Refactoring:
```
refactor(components): simplify state management in Dashboard

- Replace class components with functional components and hooks
- Consolidate redundant state variables
- Extract complex logic into custom hooks
- Improve type definitions
```

### Multiple Small Changes:
```
chore: update dependencies and fix linting issues

- Bump React from 18.2.0 to 18.3.0
- Update ESLint configuration for new rules
- Fix all existing linting warnings
- Update snapshot tests
```

## Performance Considerations

- For repositories with many files, use `git status --porcelain` for faster parsing
- Limit diff context to relevant changes when analyzing large files
- Consider using `git diff --stat` first for an overview of changes

## Important Notes

1. **Never commit sensitive information**: Check for API keys, passwords, tokens
2. **Respect .gitignore**: Ensure ignored files aren't accidentally included
3. **Follow project conventions**: Adapt to existing commit message style
4. **Be honest about changes**: Don't overstate or understate the impact
5. **Include context**: Future developers (including yourself) will thank you

## Edge Cases

- **Empty repository**: First commit should be clear it's initial
- **Merge commits**: Generally avoid committing during merges
- **Work in progress**: If forced to commit incomplete work, mark as WIP
- **Reverts**: Use standard revert format: `revert: <original subject>`

Remember: A good commit message explains not just what changed, but why it changed. The code shows how it changed.
