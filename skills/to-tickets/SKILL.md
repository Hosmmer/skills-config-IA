---
name: to-tickets
description: "Break a plan, spec, or conversation into tracer-bullet tickets, each declaring its blocking edges. Publishes to the project's ticket tracker. Use when the user wants to decompose work into tickets with dependency ordering."
---

# To Tickets

Break a plan, spec, or conversation into **tickets** — tracer-bullet vertical slices, each declaring the tickets that **block** it.

## Process

### 1. Gather context
Work from whatever is already in the conversation. If the user passes a reference (spec path, ticket ID), fetch it from the project's ticket tracker. Check the project's AGENTS.md for the tracker URL.

### 2. Explore the codebase (optional)
Understand current state. Read the project's domain contexts (CONTEXT.md) if they exist, respect ADRs. Look for prefactoring opportunities: "Make the change easy, then make the easy change."

### 3. Draft vertical slices
Break work into **tracer bullet** tickets:

- Each slice cuts a narrow but COMPLETE path through every layer (schema → API → UI → tests)
- A completed slice is demoable on its own
- Each slice fits in a single fresh context window
- Prefactoring goes first

Give each ticket its **blocking edges** — tickets that must complete before it can start. A ticket with no blockers can start immediately.

**Wide refactors** are the exception to vertical slicing. A wide refactor is one mechanical change whose blast radius fans across the whole codebase — renaming a column, retyping a shared symbol, changing a FK relationship. A single edit would break thousands of call sites; no vertical slice can land green alone.

Use **expand–contract**:

1. **Expand ticket** — add the new form beside the old so nothing breaks. Both coexist, CI stays green.
2. **Migrate tickets** — move callers over in batches sized by blast radius (per app, per directory). Each batch is its own ticket, blocked by the expand, keeping CI green because the old form still exists.
3. **Contract ticket** — delete the old form once no caller remains. Blocked by every migrate batch.

Example from CineViewHos: `LineItemModifier.modifier` FK migration → `_modifier_snapshot`. Expand adds the snapshot field, migrate batches update serializers/views one app at a time, contract drops the old FK.

When even the batches can't stay green alone, keep the sequence but let them share an integration branch that all block a final `integrate-and-verify` ticket — green is promised only there.

### 4. Quiz the user
Present the breakdown as a numbered list. For each ticket:

- **Title**: short descriptive name
- **Blocked by**: which tickets gate it
- **What it delivers**: end-to-end behaviour

Ask: Does granularity feel right? Are blocking edges correct? Merge or split?

### 5. Publish to the project's ticket tracker
Create each ticket via `POST {TRACKER_URL}/api/tickets/` in dependency order (blockers first) so blocking edges can reference real ticket IDs. For each ticket:

1. Create ticket with title, type, priority, board, project
2. Write `description.md` via the content API
3. Set `blocked_by` field with ticket IDs of blocking tickets
4. Apply `ready-for-agent` status

Work the **frontier** — any ticket whose blockers are all done.

### Ticket template

```markdown
# {TICKET_ID} — {Ticket title}

**What to build:** end-to-end behaviour from user's perspective — not layer-by-layer.

**Blocked by:** ticket IDs or "None — can start immediately"

**Acceptance criteria:**
- [ ] Criterion 1
- [ ] Criterion 2
```

Avoid specific file paths or code snippets — they go stale fast. Exception: prototype snippets encoding decisions precisely.
