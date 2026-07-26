---
name: cineviewhos-deploy
description: Deploy the project (backend + frontend, or individual components) to STG and PROD with full PR/commit audit. Use when user asks to deploy, or mentions deploying to staging/production.
---

# Deploy Skill

## Quick Start

Ask which components to deploy (backend, frontend, or specific services). Then audit all pending commits/PRs and deploy with tags.

---

## Workflow: Standard Deploy

### 1. Audit Phase
For each repo to deploy:

```bash
git fetch --tags -q
LAST_PROD=$(git tag --sort=-version:refname | grep -E '^v[0-9]+\.[0-9]+\.[0-9]+$' | head -1)
echo "Last prod: $LAST_PROD"
git log --oneline origin/main ^$LAST_PROD
```

**Critical checks:**
- [ ] No commits should be skipped or hidden
- [ ] Verify `origin/main` HEAD is a **merge commit** (e.g., `Merge pull request #NNN`)
- [ ] List ALL PRs merged since last prod tag
- [ ] Check for migrations: `git log --name-only origin/main ^$LAST_PROD | grep migration`

**Gate:** If migrations exist and require special handling, notify the appropriate person.

### 2. Tag Creation Phase

**Always tag from `origin/main` explicitly** to avoid raw commits:

```bash
git tag v1.1.NNN-stg origin/main && git push origin v1.1.NNN-stg
git tag v1.0.NNN origin/main && git push origin v1.0.NNN
```

**Version scheme:**
- STG: `v1.1.NNN-stg` (increment NNN from last STG tag)
- PROD: `v1.0.NNN` (increment NNN from last PROD tag)

### 3. CI Monitoring Phase

Monitor CI runs until completion. Check the project's AGENTS.md for CI configuration and monitoring commands.

**Success:** All CI runs show `completed/success`

---

## Workflow: Docker/Container Deploy

For projects deployed via containers:

### 1. Get Latest Tag & Commit

```bash
git fetch --tags -q
LAST_PROD=$(git tag --sort=-version:refname | grep -E '^v[0-9]+\.[0-9]+\.[0-9]+$' | head -1)
LAST_SHA=$(git rev-parse $LAST_PROD)
git log --oneline origin/main ^$LAST_PROD
```

### 2. Audit & Tag

- [ ] Review all pending commits/PRs (no skips)
- [ ] No migrations requiring special handling
- [ ] Tag from `origin/main`

```bash
git tag v1.X.Y origin/main && git push origin v1.X.Y
```

### 3. Trigger CI

Pushing tag triggers CI build. Monitor via the project's CI commands.

### 4. Deploy (Once CI passes)

Reference the project's deploy runbook (check AGENTS.md or the wiki for the deploy procedure). Run the deploy commands per the project's configuration.

---

## Red Flags

- **Immutable tag error:** Commit SHA already deployed. Verify `origin/main` has NEW commits.
- **Raw commit tags:** Tag points to non-merge commit. Check: `git cat-file -p $TAG | grep '^parent' | wc -l` — should be 2+ for merge commits.
- **Missing PRs in audit:** Always use `git log --oneline origin/main ^$LAST_PROD` to list ALL commits. Cross-check with `gh pr list --state merged --limit 20`.
- **Migrations without proper authorization:** Stop and notify before tagging if migrations detected.

---

## Checklist

- [ ] Audited all pending commits/PRs (none skipped)
- [ ] Verified `origin/main` HEAD is merge commit
- [ ] Checked for migrations (handle per project policy)
- [ ] Tagged from `origin/main` explicitly (not local branch)
- [ ] All CI passed
- [ ] Verified PRs/features deployed match what user expected
