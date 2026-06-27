---
name: Sync Workspace
description: Lint markdown files, organize folders, and commit/push changes to git. Run this when the user types /lint or /sync.
---

# Instruction

The user wants to keep their Second Brain repository clean, linted, and backed up to Git. This skill performs validation and synchronization.

## Workflow

1. **Check Configuration**:
   - Read `.agents/config.json` to get settings like `github_remote_url`, `auto_sync_enabled`, and `commit_prefix`.
   - If the repository isn't initialized (`.git` folder doesn't exist) or remote is missing, use the URL from the config file to run `git init` and `git remote add origin <url>`.

2. **Linting Check**:
   - Check if there are any improperly formatted markdown files in `/wiki`, `/snippets`, or `/patterns` (e.g., missing YAML frontmatter, broken links). 
   - Propose fixes if anything is glaringly wrong.

3. **Git Status & Auto-Commit**:
   - Run `git status` to see what has changed.
   - If there are changes, prepare an appropriate commit message summarizing the changes, using the `commit_prefix` from config (e.g., `[Brain Sync] added 3 wiki entries`).
   - **Crucial**: Ask the user for permission BEFORE running `git commit` and `git push`. They might want to postpone it, review the message, or they might just want to keep the repository strictly **Local Only** for privacy.
   - If the user approves, run:
     - `git add .`
     - `git commit -m "<your summary>"`
     - `git push -u origin main` (or whatever the active branch is)

4. **User Options**:
   - The user can postpone the sync, trigger it manually later, or decline pushing if they want to keep data purely local.
