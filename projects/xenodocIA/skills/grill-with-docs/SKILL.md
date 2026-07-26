---
name: grill-with-docs
description: Grilling session for xenodocIA that challenges plans against the domain model, sharpens terminology, and records ADRs. Use when stress-testing a plan or design for xenodocIA.
---

# Grill With Docs — XenodocIA

Load the global `grill-with-docs` skill first. Additionally, use `domain-modeling` skill to:

- Challenge every term against `thoughts/wiki/contexts/xenodocIA/CONTEXT.md`
- Propose precise canonical names for fuzzy terms
- Stress-test domain relationships with edge cases
- Cross-reference statements against the actual codebase
- Update CONTEXT.md inline when terms resolve
- Create ADRs when the 3-question test passes

## Before the Session

Read from `.opencode/AGENTS.md`:
- **Architecture Rules** — `.opencode/rules/01-architecture.md`
- **Diagnostics Rules** — `.opencode/rules/02-diagnostics.md`
- **Process Rules** — ticket creation, QA, ADR enforcement

## During the Session

- Ask one question at a time. Wait for feedback.
- If a question can be answered by reading the codebase, explore it.
- If a question can be answered by reading the rules, read them instead of asking.
- Flag UI/UX changes as isolated callouts — a "yes" to a broader question never counts as UI/UX approval.
- When the user uses a term that conflicts with CONTEXT.md, call it out immediately.

## Architecture-Specific Challenge Points

For xenodocIA, always grill on:
1. **Storage decisions** — Does this data belong in Postgres or `thoughts/`?
2. **Service layer boundaries** — Is the logic in the right layer (models/serializers/services/views)?
3. **API contract changes** — Does this change break existing API consumers?
4. **Thoughts path conventions** — Does the new feature respect `tickets/YYYY-MM/` and `wiki/` path conventions?
5. **Migration impact** — Does this require data migration? Can it be done without downtime?

## Outcome

After the session, all decisions ready for the Document step of the SDD pipeline. ADRs and CONTEXT.md updates captured inline, not deferred.
