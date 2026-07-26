---
name: gpmain
description: Syncs the current git repo (or all repos in the project) to the latest main branch. Use when user runs /gpmain or asks to sync repos, pull main, or update repos.
---

# gpmain

Ensures the current repo is on `main` and up to date. For multi-repo projects, check the project's AGENTS.md for the repo layout.

## Steps

For each repo (single or multi):

1. Check current branch:
   ```
   git -C <repo_path> branch --show-current
   ```

2. If not on `main`, switch to it:
   ```
   git -C <repo_path> checkout main
   ```

3. Pull latest from origin:
   ```
   git -C <repo_path> pull origin main
   ```

Run repos in parallel for speed (if multi-repo).

## Output

After finishing, show a concise table:

| Repo | Was on | Action | Result |
|------|--------|--------|--------|
| backend | main | pull | ✓ up to date |
| frontend | feature/xyz | checkout + pull | ✓ updated |

If any step fails (dirty working tree, merge conflict, auth error), stop for that repo, report the error clearly, and continue with the remaining repos.
