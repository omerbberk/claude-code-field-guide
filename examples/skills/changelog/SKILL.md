---
name: changelog
description: Update CHANGELOG.md from git commits since the last release tag.
  Use when preparing a release or when asked to update the changelog.
---

# Changelog updater

Update the project's CHANGELOG.md from git history.

## Steps

1. Find the latest release tag: `git describe --tags --abbrev=0`.
   If there are no tags, use the first commit as the starting point.
2. List commits since then: `git log <tag>..HEAD --oneline --no-merges`.
3. Group the changes under these headings (omit empty ones):
   - **Added** — new features
   - **Changed** — changes to existing behavior
   - **Fixed** — bug fixes
   - **Removed** — removed features
4. Skip noise: version bumps, formatting-only commits, CI tweaks, typo fixes
   in comments.
5. Write entries as user-facing sentences, not commit messages.
   ("Added dark mode toggle to settings" — not "feat: darkmode v2 wip").
6. Insert the new section at the top of CHANGELOG.md under `## [Unreleased]`,
   creating the file with a standard Keep a Changelog header if it doesn't exist.
7. Show the generated section and wait for approval before saving.

## Arguments

If arguments are given (e.g. `/changelog v2.1.0`), use that ref instead of the
latest tag as the starting point.
