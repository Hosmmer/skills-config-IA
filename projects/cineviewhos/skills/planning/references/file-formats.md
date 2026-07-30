# File Formats

## README.md

```markdown
---
slug: project-slug
status: draft | active | done
products:
  - Product-Name
themes:
  - Theme Name
---

# Project Name

## Goal
One sentence — the Impact, as a headline. The business result nobody on the team controls
directly. "Reduce support costs," not "build X."

## Opportunity Statement

### Problem context
The problematic behavior happening today — what people do now that causes the problem.

### Impact
The same Impact as Goal, spelled out: why it matters right now, what it's costing. Not a
different concept from Goal — the expanded version instead of the headline.

### Desired outcome
One sentence describing the behavior that needs to change (the Outcome). Attach a metric if
you can, and check whether that metric could be gamed without the behavior actually changing.

See `references/goals-and-outcomes.md` for the full framework and the grill checklist.

## Resources
- [ ] PRD/spec
- [ ] Loom Video
- [ ] Design file

## Phases

| # | status | scope |
|---|--------|-------|
| 1 | active | What Phase 1 delivers |
| 2 | planned | What Phase 2 delivers |
| 3 | planned | What Phase 3 delivers |
```

**themes:** free text for now — no controlled registry exists yet. Ask the user which theme(s) apply.

**products:** product/domain names, one per bounded context — not git repo names.

## phase-N.md

```markdown
---
phase: N
status: active | planned | completed
---

# Phase N — Short scope title

Scope: one or two sentences describing what this phase delivers.

## Items

| id | title | type | priority | ticket |
|------|-------|-------|----------|--------|
| PI-1 | ... | story | high | — |
| PI-2 | ... | task | medium | {TICKET_ID} |
```

**Types:** epic · story · task · bug · subtask
**Priorities:** critical · high · medium · low
**Ticket column:** `—` = not committed · ticket ID = committed to backlog
