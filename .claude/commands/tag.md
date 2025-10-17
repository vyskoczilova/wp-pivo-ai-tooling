---
allowed-tools: Bash(git:*), Read, Edit, Write, Glob, Grep
description: Create git tag with intelligent semver bump
argument-hint: "[patch|minor|major]"
model: claude-sonnet-4-5
# Metadata (ignored by Claude Code, but useful for humans)
author: Daniel Mejta <daniel@wpify.io>
created: 2025-10-15
updated: 2025-10-15
---

# Create Git Tag with Intelligent Semver Bump

Creates a git tag with automatic or manual semantic version bump. Analyzes commit messages and file changes to intelligently determine the appropriate version increment (patch, minor, or major).

## Usage

```bash
# Auto-detect version bump based on changes
/tag

# Explicitly specify version bump
/tag patch
/tag minor
/tag major
```

## Instructions

### 1. Preliminary Checks

Before proceeding, verify the repository state:

- Check if there are uncommitted changes with `git status --short`
- If uncommitted changes exist:
  - Check if changes are ONLY in changelog files (CHANGELOG.md, CHANGELOG.txt, etc.)
  - If only changelog is modified: proceed (it will be committed as part of the release)
  - If other files are modified: warn the user and stop
- Retrieve the latest tag with `git describe --tags --abbrev=0` (or similar command)
- If no tags exist, ask the user which level to create:
  - `patch` → Create tag `0.0.1` (or `v0.0.1` if using v prefix)
  - `minor` → Create tag `0.1.0` (or `v0.1.0` if using v prefix)
  - `major` → Create tag `1.0.0` (or `v1.0.0` if using v prefix)
- Determine if tags use `v` prefix (e.g., `v1.2.3`) or not (e.g., `1.2.3`)

### 2. Determine Version Bump Strategy

**If user provided semver parameter (`patch`, `minor`, or `major`):**
- Use the specified bump level directly
- Skip analysis and proceed to version calculation

**If no parameter provided (auto-detect mode):**
- Get the list of commits since the last tag: `git log <last-tag>..HEAD --oneline`
- Get the list of changed files: `git diff <last-tag>..HEAD --name-status`
- Analyze both commit messages and file changes to determine bump level:

  **Major bump indicators:**
  - Commit messages containing: `BREAKING CHANGE`, `breaking:`, `major:` suffix (conventional commits)
  - Significant architecture changes (e.g., moving core files, renaming major modules)
  - Changes to API contracts, database schema migrations
  - Updates to major dependencies (package.json with major version bumps)

  **Minor bump indicators:**
  - Commit messages containing: `feat:`, `feature:`, `minor:`
  - New files added (especially in `src/`, `lib/`, feature directories)
  - New API endpoints or public functions
  - New dependencies added

  **Patch bump indicators:**
  - Commit messages containing: `fix:`, `bugfix:`, `patch:`, `chore:`, `docs:`
  - Only modifications to existing files (no new features)
  - Documentation changes, test updates, code style fixes
  - Small tweaks or bug fixes

- **Default to patch** if analysis is uncertain or changes are minimal

### 3. Calculate New Version

Based on the determined bump level and current version:

- Parse the current version (e.g., `1.2.3` or `v1.2.3`)
- Apply the bump:
  - **patch**: `1.2.3` → `1.2.4`
  - **minor**: `1.2.3` → `1.3.0` (reset patch to 0)
  - **major**: `1.2.3` → `2.0.0` (reset minor and patch to 0)
- Preserve the `v` prefix if it was present in the previous tag

### 4. Summarize Analysis (Auto-detect only)

If auto-detection was used, provide a clear summary of the reasoning:

```
Creating new tag: v1.3.0 (minor bump)

Reasoning:
- Found 3 commits since v1.2.5
- Detected new feature additions:
  - Added new API endpoint in src/api/users.js
  - New component: src/components/UserProfile.jsx
- Commit messages indicate feature work:
  - "feat: add user profile page"
  - "feat: implement user API endpoints"

This qualifies as a MINOR version bump (new backward-compatible features).
```

### 5. Update Changelog (if exists)

Before creating the tag, update the changelog file if it exists:

**Step A: Locate Changelog File**

Use Glob to search for changelog files in the project root (not subdirectories):
```bash
# Search for common changelog filenames
CHANGELOG.md, CHANGELOG.txt, CHANGELOG, CHANGELOG.rst, changelog.md, Changelog.md
```

**Step B: Update Changelog Content (if found)**

If a changelog file exists:

1. Read the changelog file using Read tool

2. Check if `[Unreleased]` section exists and contains entries:
   - If section doesn't exist or is empty: skip changelog update
   - If section has entries: proceed with update

3. Get today's date in YYYY-MM-DD format

4. Transform the changelog:
   - Replace `## [Unreleased]` with `## [<new-version>] - <today's-date>`
   - Add a new empty `## [Unreleased]` section at the top (after the main header)

   Example transformation:
   ```markdown
   # Changelog

   ## [Unreleased]

   ### Added
   - New feature X

   ### Fixed
   - Bug fix Y

   ## [1.2.0] - 2024-01-15
   ```

   Becomes:
   ```markdown
   # Changelog

   ## [Unreleased]

   ## [1.3.0] - 2024-03-20

   ### Added
   - New feature X

   ### Fixed
   - Bug fix Y

   ## [1.2.0] - 2024-01-15
   ```

5. Write the updated changelog using Edit or Write tool

**Step C: Commit Changelog Changes**

After updating the changelog:

```bash
# Stage the changelog file
git add <changelog-filename>

# Commit with release message
git commit -m "chore: release version <new-version>"
```

**Important Notes:**
- This commit will be the one that the tag points to
- The changelog must be committed BEFORE creating the tag
- If git commit fails, stop and report the error (don't create the tag)

**Step D: If No Changelog Exists**

If no changelog file is found:
- Log: "No changelog file found, skipping changelog update"
- Proceed directly to tag creation

### 6. Create Git Tag

- Create the tag with: `git tag <new-version>`
- Optionally add a simple message: `git tag -a <new-version> -m "Release <new-version>"`
- **DO NOT push the tag** - let the user verify and push manually
- Confirm tag creation: `git tag -l <new-version>`

### 7. Final Output

Provide clear next steps:

**If changelog was updated:**
```
✓ Updated CHANGELOG.md (v1.3.0 - 2024-03-20)
✓ Committed changelog changes (commit: a1b2c3d)
✓ Tag v1.3.0 created successfully

To push the changes and tag to remote:
  git push origin main  # or your branch name
  git push origin v1.3.0

To push all tags:
  git push --tags
```

**If no changelog existed:**
```
✓ Tag v1.3.0 created successfully

To push the tag to remote:
  git push origin v1.3.0

To push all tags:
  git push --tags
```

## Error Handling

- **No git repository**: Stop and inform user
- **Uncommitted changes**:
  - If only changelog is modified: proceed (will be committed as part of release)
  - If other files are modified: warn user and suggest committing or stashing first
- **Invalid semver parameter**: Show valid options (`patch`, `minor`, `major`)
- **No changes since last tag**: Warn user and ask if they still want to create a tag
- **Tag already exists**: Stop and inform user, suggest deleting old tag or using different version
- **Changelog update errors**:
  - **[Unreleased] section not found**: Skip changelog update, proceed with tagging, inform user
  - **[Unreleased] section is empty**: Skip changelog update, proceed with tagging
  - **Malformed changelog**: Attempt to update anyway, warn user to verify manually
  - **Cannot write changelog**: Stop and report error, do not proceed with tagging
  - **Git commit fails**: Stop and report error, do not create tag, suggest checking git status

## Examples

### Example 1: Auto-detect with feature additions and changelog update

```bash
$ /tag

Creating new tag: v1.5.0 (minor bump)

Reasoning:
- Found 5 commits since v1.4.2
- New files added:
  - src/Features/ChatIntegration.php
  - assets/app/components/ChatWidget.jsx
- Commit messages:
  - "feat: add AI chatbot integration"
  - "feat: implement chat UI component"
  - "fix: resolve chat message formatting"

This qualifies as a MINOR version bump (new features added).

✓ Updated CHANGELOG.md (v1.5.0 - 2024-03-20)
✓ Committed changelog changes (commit: f8a9b2c)
✓ Tag v1.5.0 created successfully

To push the changes and tag to remote:
  git push origin main
  git push origin v1.5.0
```

### Example 2: Manual patch bump

```bash
$ /tag patch

Creating new tag: v1.4.3 (patch bump - manually specified)

✓ Tag v1.4.3 created successfully
```

### Example 3: No changelog file exists

```bash
$ /tag minor

Creating new tag: v0.2.0 (minor bump - manually specified)

No changelog file found, skipping changelog update

✓ Tag v0.2.0 created successfully

To push the tag to remote:
  git push origin v0.2.0
```

### Example 4: Empty [Unreleased] section

```bash
$ /tag

Creating new tag: v1.4.3 (patch bump)

Reasoning:
- Found 2 commits since v1.4.2
- Commit messages:
  - "fix: correct typo in documentation"
  - "chore: update dependencies"

This qualifies as a PATCH version bump (bug fixes and maintenance).

[Unreleased] section in CHANGELOG.md is empty, skipping changelog update

✓ Tag v1.4.3 created successfully
```

### Example 5: First tag in repository

```bash
$ /tag

No existing tags found. Which version level should I create?
- patch: Creates 0.0.1
- minor: Creates 0.1.0
- major: Creates 1.0.0

Please run: /tag <level>
```

## Performance Considerations

- For repositories with many commits, limit analysis to the most recent 50-100 commits
- Cache file diff results to avoid repeated git operations
- Skip binary files and large assets when analyzing changes

## Security Notes

- Never push tags automatically - always let user review first
- Verify tag doesn't already exist before creating
- Ensure working directory is clean before proceeding
