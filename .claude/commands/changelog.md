---
allowed-tools: Bash(git:*), Read, Edit, Write, Glob
description: Update [Unreleased] section in changelog with recent changes
argument-hint:
model: claude-sonnet-4-5
# Metadata (ignored by Claude Code, but useful for humans)
author: Daniel Mejta <daniel@wpify.io>
created: 2025-10-15
updated: 2025-10-15
---

# Changelog Manager

Intelligently analyzes uncommitted changes and git history since the last tag, categorizes them according to Keep a Changelog format, and updates the `[Unreleased]` section in your project's changelog file.

## What This Command Does

1. Locates your changelog file (searches for `CHANGELOG.md`, `CHANGELOG.txt`, `CHANGELOG`, etc. in project root)
2. Analyzes both staged and unstaged changes
3. Reviews all commits since the most recent git tag
4. Automatically categorizes changes into Keep a Changelog categories
5. Merges new entries with existing `[Unreleased]` section (no duplicates)
6. Creates the `[Unreleased]` section if it doesn't exist

## Usage

```bash
/changelog
```

No arguments needed - the command is fully automatic!

## Keep a Changelog Categories

Changes are automatically categorized into:

- **Added** - New features, capabilities, or functionality
- **Changed** - Modifications to existing functionality
- **Deprecated** - Features marked for future removal
- **Removed** - Deleted features or capabilities
- **Fixed** - Bug fixes and corrections
- **Security** - Security-related changes

## Step-by-Step Instructions

### 1. Locate Changelog File

Search the project root for common changelog filenames:
- `CHANGELOG.md`
- `CHANGELOG.txt`
- `CHANGELOG`
- `CHANGELOG.rst`
- `changelog.md`
- `Changelog.md`

Use Glob tool to find: `CHANGELOG*` and `changelog*` patterns in the current directory (not subdirectories).

**If no changelog exists:**

First, check if git tags exist:
```bash
git tag --list | head -20
```

**Case A: No changelog, no tags (First version)**

Create a new `CHANGELOG.md` file in the project root with proper Keep a Changelog structure:

```markdown
# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

### Added
- Initial version

```

Then proceed with the analysis.

**Case B: No changelog, but tags exist (Generate from git history)**

Generate a complete changelog from git history:

1. Get all tags sorted by version (chronologically):
```bash
git tag --sort=-version:refname
```

2. For each tag (newest first), analyze commits:
```bash
# For each tag, get commits between it and the previous tag
git log <previous-tag>..<current-tag> --oneline --no-merges

# For the oldest tag, get commits from beginning:
git log <oldest-tag> --oneline --no-merges
```

3. Build changelog structure:
   - Start with standard Keep a Changelog header
   - Add `[Unreleased]` section with changes since last tag (if any)
   - Add section for each tag as `## [version] - YYYY-MM-DD`
   - Get tag date: `git log -1 --format=%ai <tag>`
   - Categorize commits for each version using the same heuristics (section 4)
   - Order versions from newest to oldest

4. Create `CHANGELOG.md` with complete history:

```markdown
# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]
[Changes since last tag]

## [1.2.0] - 2024-03-15
[Categorized changes]

## [1.1.0] - 2024-02-01
[Categorized changes]

## [1.0.0] - 2024-01-15
[Categorized changes]
```

5. After creating the changelog from history, continue with normal flow to update [Unreleased] section

Then proceed with the analysis.

### 2. Gather Git Information

Run these commands to understand the repository state:

```bash
# Check if in a git repository
git rev-parse --is-inside-work-tree

# Get the most recent tag
git describe --tags --abbrev=0 2>/dev/null

# If no tags exist, note this for "First version" handling
```

### 3. Collect All Changes

#### A. Uncommitted Changes (Staged + Unstaged)

```bash
# Get status summary
git status --short

# Get detailed diff of all changes (staged and unstaged)
git diff HEAD
```

#### B. Commits Since Last Tag

If a tag exists:
```bash
# Get commit messages since last tag
git log $(git describe --tags --abbrev=0)..HEAD --oneline --no-merges

# Get detailed diff since last tag
git diff $(git describe --tags --abbrev=0)..HEAD
```

If NO tags exist:
```bash
# Get all commits
git log --oneline --no-merges

# Note: This is the first version
```

### 4. Analyze and Categorize Changes

Use the following heuristics to categorize changes automatically:

#### Keywords in Commit Messages
- **Added**: "add", "new", "introduce", "implement", "create", "feature"
- **Fixed**: "fix", "bug", "resolve", "patch", "correct", "repair"
- **Changed**: "update", "modify", "change", "improve", "refactor", "enhance"
- **Removed**: "remove", "delete", "drop"
- **Deprecated**: "deprecate", "obsolete"
- **Security**: "security", "vulnerability", "CVE", "XSS", "SQL injection"

#### File and Code Patterns
- New files → likely **Added**
- Deleted files → likely **Removed**
- Test files → can be grouped or mentioned as "Add tests for X"
- Documentation files → "Update documentation"
- Configuration changes → **Changed** (unless clearly a new feature)
- Dependency updates → **Changed** (or **Security** if security-related)

#### Best Practices
- Make entries human-readable (not just commit messages)
- Group related changes together
- Focus on WHAT changed from user/developer perspective, not HOW
- Be concise but informative
- Use consistent verb tense (past tense: "Added X", "Fixed Y")

### 5. Read Existing Changelog

Use Read tool to get the current changelog contents.

Parse the existing content to:
- Find the `[Unreleased]` section (if it exists)
- Extract existing entries by category
- Preserve formatting and structure

### 6. Merge Changes

**Important:** Avoid duplicate entries!

Compare new entries with existing ones:
- Check for semantic similarity (not just exact string match)
- If an entry is substantially similar, skip it
- If it adds new information, merge or keep both

**If [Unreleased] section doesn't exist:** Create it with proper Keep a Changelog structure:

```markdown
## [Unreleased]

### Added
- [entries]

### Changed
- [entries]

### Fixed
- [entries]
```

**If [Unreleased] section exists:** Merge new entries into existing categories, maintaining alphabetical or logical order.

### 7. Format and Update

Follow Keep a Changelog format strictly:

- Use `##` for version headers
- Use `###` for category headers
- Use `-` for bullet points
- Add blank lines between sections
- Keep categories in order: Added, Changed, Deprecated, Removed, Fixed, Security

### 8. Write Updated Changelog

Use Edit tool to update the changelog file (preferred), or Write tool if creating new file.

Ensure:
- Proper markdown formatting
- Consistent indentation
- No trailing whitespace
- Blank line at end of file

### 9. Special Cases

#### No Tags (First Version)
If `git describe --tags` fails, this is the first version:
- Add a note: "- Initial release" or "- First version"
- List all major features as **Added**

#### No Changes Found
If there are no uncommitted changes AND no commits since last tag:
- Report: "No changes detected since last tag"
- Do not modify the changelog

#### Empty Commits
If commits exist but have no substantive changes (e.g., only whitespace):
- Note this to the user
- Ask if they want to add a manual entry

#### Merge Conflicts
If the [Unreleased] section has unusual formatting:
- Do your best to preserve existing format
- Add new entries clearly separated
- Warn the user about potential formatting inconsistencies

## Error Handling

- **Not a git repository:** Inform user and exit
- **Cannot find/create changelog:** Report specific error
- **Git commands fail:** Show error message and suggest checking git installation
- **Malformed changelog:** Attempt to fix or ask user for guidance
- **Permission denied:** Report file permission issues

## Example Output Messages

After successful execution:

```
✓ Found CHANGELOG.md
✓ Analyzed 15 uncommitted changes
✓ Reviewed 8 commits since tag v1.2.0
✓ Added 12 new entries to [Unreleased] section
✓ Merged with 3 existing entries (no duplicates)

Updated categories:
  - Added (5 entries)
  - Changed (3 entries)
  - Fixed (4 entries)

Your changelog has been updated!
```

## Performance Notes

- For repositories with extensive history, this command may take a few seconds
- Large diffs are summarized to avoid overwhelming the changelog
- If analysis takes too long, consider splitting into smaller commits

## Keep a Changelog Reference

This command follows [Keep a Changelog 1.1.0](https://keepachangelog.com/en/1.1.0/) guidelines:

- Changelogs are for humans, not machines
- There should be an entry for every version
- Same types of changes should be grouped
- Versions and sections should be linkable
- The latest version comes first
- The release date of each version is displayed
- Semantic Versioning is followed

## Tips for Best Results

1. **Write descriptive commit messages** - Better commits → better changelog entries
2. **Use conventional commits** - Format like "feat: add feature" helps categorization
3. **Review before committing** - Check the [Unreleased] section before creating a release
4. **Run regularly** - Keep your changelog up to date throughout development
5. **Manual refinement** - After running, you can manually refine entries for clarity
