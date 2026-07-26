---
name: devk
description: Development router for XenodocIA — extends the global devk skill with xenodocIA-specific tracker, naming, and pipeline details. Use when user runs /devk or asks to start working on a xenodocIA development task.
---

# DevK — XenodocIA

Load the global `devk` skill first (`~/.config/opencode/skills/devk/SKILL.md`). This file provides xenodocIA-specific overrides.

## Tracker Configuration

XenodocIA IS the tracker. When working on xenodocIA's own code:
- **Tickets API**: `{XENODOCIA_URL}/api/tickets/`
- **Content API**: `{XENODOCIA_URL}/api/content/`
- **Auth**: `Authorization: Token {XENODOCIA_API_TOKEN}` (NOT Bearer)
- **Wiki paths**: `thoughts/wiki/contexts/{domain}/` (local files, git-tracked)
- **Ticket paths**: `thoughts/tickets/YYYY-MM/{TICKET_ID}/` (local files, git-tracked)

## Ticket File Naming

`scan_thoughts()` scans for `*_spec.md` and `*_plan.md`. Always use date prefix:
```
thoughts/tickets/YYYY-MM/{TICKET_ID}/YYYY-MM-DD-name_spec.md
thoughts/tickets/YYYY-MM/{TICKET_ID}/YYYY-MM-DD-name_plan.md
thoughts/tickets/YYYY-MM/{TICKET_ID}/description.md
```

Create all three via content API (`PUT /api/content/`) or write directly to `thoughts/`.

## Kanban

Columns: backlog → todo → in-progress → qa → ready-to-deploy → done
AI transitions: in-progress, qa, ready-to-deploy

## Git

- Branch: `feat/{TICKET-ID-slug}` or `fix/{TICKET-ID-slug}`
- PR: `{TICKET-ID}: description`
- Commit: `type(scope): description [{TICKET-ID}]`

## CI Checks

```
frontend/ → npx tsc --noEmit && npm run build
backend/  → python manage.py check && ruff check && pytest
```

## Additional Pipeline Steps (xenodocIA-specific)

### After Implementation: QA + Docs
- Every feature promotes its spec to `contexts/xenodocIA/specs/` via `/promote`
- Every architectural decision gets an ADR in `contexts/xenodocIA/adr/`
- ADRs are mandatory when the 3-question test passes (hard to reverse, surprising, real trade-off)

### After Bug Fix: Post-Mortem
- Post-mortem comment on the bug ticket (symptom, root cause, fix, guard)
- If architecture prevented a test seam → new refactor ticket + ADR

## Architecture Constraints (ALWAYS FOLLOW)

Read `.opencode/rules/01-architecture.md` before any code change:
- All `thoughts/` I/O through services layer (never direct filesystem)
- Ticket IDs via `select_for_update()` on Board
- Status changes always record `TicketStatusHistory`
- Search vectors via `post_save` signal only
- Backend: models → serializers → services → views (no business logic in views)
- Use `default_storage` for all file I/O (S3-compatible)
