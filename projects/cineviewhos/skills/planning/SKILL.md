---
name: planning
description: Plans and manages Projects, Phases, and ProjectItems. Use when starting a new initiative, defining what to build next, committing planned items to the backlog, or viewing the roadmap and backlog. Runs upstream of /devk — creates the work that devk executes.
---

# Planning

Route planning tasks based on intent. All writing follows: **draft → show → approve → write**.

## Environment

Check the project's AGENTS.md for the content/wiki API and tracker API configuration.
Content may be stored via an API (S3-backed or similar). Every path below is a **logical path**, not a filesystem location unless the project uses local files.

Read: `GET {TRACKER_URL}/api/content/?path=<path>`
Write: `PUT {TRACKER_URL}/api/content/` with `{"path": "<path>", "content": "..."}`
List directory: `GET {TRACKER_URL}/api/content/tree/?prefix=<prefix>`
Auth: check AGENTS.md for the header format (`Token` vs `Bearer`).

Never use filesystem operations on paths that look like content/wiki paths — they go through the API.

## Paths

### 1. New project
User wants to plan a new initiative (new feature, new app, new module).
→ Read `references/create-project.md`

### 2. Update existing project
User wants to add items, change phase status, update scope, or commit an existing ProjectItem to the backlog.
→ Read the project's files via the content API, show current state, propose changes, get approval, write.
  For the "commit a PI-N" case specifically → read `references/commit-item.md`.

### 3. Set of tickets standalone
User wants new ticket(s) created that are not tied to any planning project.
→ Read `references/create-standalone-ticket.md`

If it's not obvious from context which of #2 or #3 applies, ask.

## Views (read-only)

### View roadmap
→ List projects via the content API, display projects + phases by status.

### View backlog
→ `GET {TRACKER_URL}/api/tickets/?status=backlog`, display ticket ID + title.

## File locations

| Artifact | Path |
|----------|------|
| Project overview | `planning/projects/{slug}/README.md` |
| Phase items | `planning/projects/{slug}/phase-N.md` |
| Committed ticket | ticket with `status=backlog`, addressed by ID |

## Golden rule

Draft → Show to human → Approve → Write.
Never write a file without human approval. Never commit an item without knowing the target Board.
