# Diagnostics Rules — Bug + ADR Enforcement

These rules are **mandatory**. Never skip them. Every bug gets documented end-to-end. Every architectural decision gets an ADR.

## Bug Documentation Pipeline (MANDATORY)

When a bug is reported or discovered in xenodocIA, the full diagnosing-bugs pipeline MUST be executed. No exceptions.

### 1. Ticket Creation
- Type: `bug`
- Priority: assessed (critical/high/medium/low)
- `description.md` with clear symptom description
- Title format: `{symptom}: {affected component}` (e.g. "500 error on ticket creation: content API timeout")

### 2. Diagnosing Pipeline
Load `/diagnosing-bugs` skill. Execute all phases:
1. **Feedback loop** — Build a tight, red-capable repro command. Must be deterministic, fast, agent-runnable.
2. **Reproduce + minimise** — Shrink to smallest load-bearing scenario.
3. **Hypothesise** — 3-5 ranked, falsifiable hypotheses. Show to user before testing.
4. **Instrument** — One variable at a time. Tag debug logs: `[DEBUG-xxxx]`.
5. **Fix + regression test** — Write test BEFORE fix. If no correct seam exists, document it.
6. **Cleanup + post-mortem** — Remove all `[DEBUG-xxxx]` tags. State correct hypothesis in commit.
7. **Document** — Post-mortem on the ticket.

### 3. Post-Mortem Format
Posted as a comment on the bug ticket:
```markdown
## Post-Mortem
- **Symptom**: [what the user saw]
- **Root cause**: [which hypothesis was correct, why it happened]
- **Fix**: [what was changed, file:line]
- **Regression guard**: [test name or "no correct seam — documented in {ADR}"]
- **Seam quality**: [good / poor — explanation]
- **Refactor recommended?**: [yes – {new ticket} / no]
```

### 4. Completion Gate
The bug fix is NOT complete until:
- [ ] Post-mortem comment exists on the ticket
- [ ] All `[DEBUG-xxxx]` tags removed from codebase
- [ ] Regression test passes (or seam absence is documented in ADR)
- [ ] If seam was poor → new refactor ticket created

## ADR Enforcement (MANDATORY)

Every architectural decision in xenodocIA MUST produce an ADR when ALL three conditions are met:
1. **Hard to reverse** — changing your mind later has meaningful cost
2. **Surprising without context** — a future reader would wonder "why did they do it this way?"
3. **Real trade-off** — there were genuine alternatives with different pros/cons

### ADR Template

```
thoughts/wiki/contexts/xenodocIA/adr/{NNNN}-{slug}.md
```

```markdown
# ADR-{NNNN}: {Title}

**Date**: YYYY-MM-DD
**Status**: proposed | accepted | deprecated | superseded
**Ticket**: {TICKET_ID}

## Context
[What problem are we solving? Why now?]

## Decision
[What did we decide? Be specific.]

## Alternatives Considered
- **Option A**: [description] — pros/cons
- **Option B**: [description] — pros/cons

## Consequences
[What becomes easier? What becomes harder? What should we watch for?]
```

### When to create an ADR
- New architectural pattern (e.g., "we now validate X before Y")
- Storage/model change that affects multiple apps
- Auth/permission scheme change
- API contract change
- Infrastructure decision (S3 vs local, caching strategy, etc.)

### When to skip
- One-line fixes, typos, config changes
- Decisions fully captured in code comments or commit messages
- Decisions that don't meet the 3-question test

### ADR linking
- Every ADR MUST link to the ticket that spawned it
- The ticket description MUST be updated with a link to the ADR
- ADRs are numbered sequentially (check `thoughts/wiki/contexts/xenodocIA/adr/` for the next number)

## Ticket Documentation Rules

### Every ticket MUST have
- `description.md` with clear summary (not generic, not copy-pasted)
- H1 formatted as `# {TICKET_ID} — {Title}`
- For features: spec + plan files (created via devk pipeline)
- For bugs: post-mortem comment after fix

### Spec and Plan Files
- Naming: `YYYY-MM-DD-feature-name_spec.md`, `YYYY-MM-DD-feature-name_plan.md`
- Location: `thoughts/tickets/YYYY-MM/{TICKET_ID}/`
- Both must exist before code is written (Simple changes excepted)
- Created via content API (`PUT /api/content/`)

### Decision Comments
- Use `/add-comment XE-NNN "decision: ..."` to record decisions
- Comment type: `"decision"` for architectural choices
- Comment type: `"note"` for progress updates, blockers, discoveries
