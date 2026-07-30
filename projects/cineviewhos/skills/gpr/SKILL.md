---
name: gpr
description: Creates a GitHub Pull Request for the current git repo. Auto-generates PR title and body from the branch diff. Asks which repos (if multi-repo), then creates PR. Use when user runs /gpr or asks to open a PR, create a pull request, or submit changes for review.
---

# gpr

Creates Pull Requests. For multi-repo projects, check the project's AGENTS.md for the repo layout.

## Repo detection

If the project has multiple repos (monorepo or platform), present a menu of repos. Otherwise, use the current git repo directly.

---

## Step 1 — Determine which repos

Single repo: use the current repo directly.
Multi-repo: present a menu and wait for the user's answer.

---

## Step 2 — Standard PR flow

### 2a. Pre-flight check

```bash
git branch --show-current
git log origin/main..HEAD --oneline
```

- Branch is `main` → abort: "Can't create a PR from main. Create a branch first."
- No commits ahead of main → skip: "No new commits ahead of main."

### 2b. Generate PR content

```bash
git diff origin/main...HEAD --stat
git log origin/main..HEAD --pretty=format:"%s"
```

Generate:
- **Title**: Conventional Commits style, ≤72 chars (`feat(scope): summary`)
- **Body**:

```markdown
## Summary
- <bullet points from commits>

## Test plan
- [ ] <relevant test steps>
```

### 2c. Create PR

```bash
gh pr create \
  --repo <owner>/<repo> \
  --base main \
  --title "<title>" \
  --body "<body>"
```

Run repos in parallel if more than one was selected.

### 2d. CI verification + merge

After the PR is created, wait for CI and handle the result:

- **CI passes** → merge immediately: `gh pr merge <PR_URL> --merge`
- **CI fails** → read the failure logs, fix the code, commit, push. Then re-check CI and merge when it passes.
- **CI still pending after 5 minutes** → report to the user.
- **No CI configured** → skip CI check, merge immediately.

### 2e. Update ticket status (if tracker configured)

Only after merge succeeds, move the associated ticket to the post-merge status (check the project's AGENTS.md for the tracker API and workflow conventions).

---

## Step 3 — Summary

Print the result for each repo:

| Repo | Branch | PR | Merged |
|------|--------|----|--------|
| backend | feat/add-endpoint | https://github.com/... | — |
| frontend | feat/update-ui | https://github.com/... | merged |

---

## Error handling

- **`gh` not authenticated**: print `gh auth login` and stop.
- **Branch has no upstream**: push first with `git push --set-upstream origin <branch>`, then retry.
- **PR already exists**: print the existing PR URL and skip.
- **Auto-merge fails** (branch protection not enabled): warn the user and leave the PR open for manual merge.
