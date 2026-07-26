---
name: domain-modeling
description: "Build and sharpen a project's domain model. Use when the user wants to pin down domain terminology, record an architectural decision (ADR), or when another skill needs to maintain the domain model. Reads/writes CONTEXT.md and adr/ via the project's wiki/content API."
---

# Domain Modeling

Actively build and sharpen the project's domain model as you design. Challenge terms, invent edge-case scenarios, and write the glossary and decisions down the moment they crystallise via the project's wiki/content API.

## Where the model lives

Check the project's AGENTS.md for the wiki location. Typically:

```
wiki/contexts/
  CONTEXT-MAP.md ← cross-domain dependency map
  {domain}/
    CONTEXT.md ← glossary + bounded context
    CHANGELOG.md ← what changed and when
    adr/ ← architecture decisions
    specs/ ← promoted feature specs
```

Discover existing domains by reading CONTEXT-MAP.md (if it exists) via the project's content API. Read the project's AGENTS.md for the content API URL and auth configuration.

## During the session

### Challenge against the glossary
When the user uses a term that conflicts with `CONTEXT.md`, call it out: "Your glossary defines X as Y, but you seem to mean Z — which is it?"

### Sharpen fuzzy language
When the user uses vague terms, propose a precise canonical term.

### Discuss concrete scenarios
Stress-test domain relationships with specific scenarios that probe edge cases.

### Cross-reference with code
When the user states how something works, check whether the code agrees. Surface contradictions.

### Update CONTEXT.md inline
When a term is resolved, update `CONTEXT.md` right there via the content API. Don't batch — capture as they happen.

`CONTEXT.md` is a glossary, nothing else. No implementation details, no specs.

### Offer ADRs sparingly
Only create an ADR when all three are true:
1. **Hard to reverse** — meaningful cost to change later
2. **Surprising without context** — future reader will wonder why
3. **Real trade-off** — genuine alternatives existed and you picked one

ADR format:
```markdown
# ADR-NNNN: Title

**Status:** proposed | accepted | superseded
**Date:** YYYY-MM-DD

## Context
## Decision
## Consequences
```

### When an ADR is created — auto-update CONTEXT.md + CHANGELOG (MANDATORY)

Creating an ADR is a domain event. You MUST also:

1. **Update CONTEXT.md** — add a reference to the new ADR in the "Architecture Decisions" table (or create the table if it doesn't exist):
   ```
   | ADR | Title | Status |
   |-----|-------|--------|
   | [ADR-NNNN](adr/{filename}.md) | {title} | accepted |
   ```

2. **Update CHANGELOG.md** — append an entry:
   ```
   ## {YYYY-MM-DD}
   - **ADR-NNNN**: {title} accepted. {one-line summary}.
   ```
   Create CHANGELOG.md if it doesn't exist for the domain.

3. **Verify** — after writing, fetch the CONTEXT.md and CHANGELOG via the content API to confirm the updates are present.

This applies whether ADRs are created during a grill-with-docs session, a devk documentation step, or a standalone domain-modeling session. **Never create an ADR without updating CONTEXT.md and CHANGELOG.**
