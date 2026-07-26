---
name: domain-modeling
description: Build and sharpen xenodocIA's domain model. Challenges terms against the glossary, invents edge-case scenarios, and writes CONTEXT.md and ADRs. Use when working with xenodocIA terminology, domain context, or architectural decisions.
---

# Domain Modeling — XenodocIA

Load the global `domain-modeling` skill first. This file adds xenodocIA-specific paths and rules.

## Wiki Structure

```
thoughts/wiki/
  contexts/
    CONTEXT-MAP.md              ← multi-domain overview
    xenodocIA/
      CONTEXT.md                ← xenodocIA domain glossary
      adr/                      ← architectural decisions
        0001-devk-pipeline.md
      specs/                    ← promoted feature specs
    movies/
      CONTEXT.md
      adr/
    auth/
      CONTEXT.md
```

## ADR Rules (MANDATORY)

Create an ADR when ALL three are true:
1. **Hard to reverse** — meaningful cost to change later
2. **Surprising without context** — future reader wonders "why?"
3. **Real trade-off** — genuine alternatives with different pros/cons

ADR path: `thoughts/wiki/contexts/xenodocIA/adr/{NNNN}-{slug}.md`

### ADR Template
```markdown
# ADR-{NNNN}: {Title}

**Date**: YYYY-MM-DD
**Status**: accepted
**Ticket**: {TICKET_ID}

## Context
[What problem are we solving?]

## Decision
[What did we decide? Be specific.]

## Alternatives Considered
- **Option A**: pros/cons
- **Option B**: pros/cons

## Consequences
[What becomes easier? Harder? What to watch for?]
```

### When to update CONTEXT.md
- A new canonical term is defined
- An existing term's meaning changes
- A term is deprecated

CONTEXT.md entries follow this format:
```markdown
### {Term}
{One-paragraph definition. No implementation details.}
```

### Cross-reference with code
When the user states how something works, verify against the actual code. If contradictory, surface it:
"Your code in `tickets/services.py:45` does X, but you're saying Y — which is right?"

### After the session
All wiki updates (CONTEXT.md, ADRs) are done inline during this session. Do NOT defer to a later step.
