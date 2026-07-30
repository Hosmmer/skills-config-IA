---
name: gcpush
description: Commits and pushes changes in the current git repo. Auto-generates a commit message from the diff. If on main, creates a feature branch before committing. Use when user runs /gcpush or asks to commit and push changes.
---

# gcpush

Commits and pushes changes in the current git repo.

**HARD RULE: NEVER push directly to main/master. NEVER use raw `git push`. ALWAYS use this skill workflow. Main is protected — all changes go through feature branches and PRs.**

## Configuration

This skill works with the current git repo (detected from cwd). For multi-repo setups, check the project's AGENTS.md for the repo layout.

## Steps

### 1. Check status
```bash
git status --short --branch
```
Skip if there are no changes.

### 2. Validate branch
If the current branch is `main` or `master`, create a new branch before continuing:
```bash
git checkout -b <auto-name>
```
Auto-name format: `feat/<short-slug-from-diff>` — derive it from the staged/unstaged changes (e.g. `feat/add-product-endpoint`, `feat/update-login-form`). Keep it under 40 chars, kebab-case.

### 3. Stage all changes
```bash
git add -A
```

### 4. Generate commit message
Read `git diff --cached` and write a message in Conventional Commits format:
- Format: `<type>(<optional scope>): <summary>`
- Types: `feat`, `fix`, `refactor`, `docs`, `style`, `test`, `chore`
- Summary: imperative mood, ≤72 chars, no trailing period

### 5. Commit
```bash
git commit -m "<generated message>"
```

### 6. Push
```bash
git push origin <branch>
```
If no upstream exists (new branch): `git push --set-upstream origin <branch>`

## Output

Show a summary:
| Branch | Commit | Status |
|--------|--------|--------|
| feat/add-endpoint | feat(api): add product endpoint | ✓ pushed |

## Error handling

- **Dirty merge conflict**: stop, report, let user resolve.
- **Auth / push failure**: print the error, suggest checking SSH keys/credentials.
- **Nothing to commit after staging**: mark as skipped.
