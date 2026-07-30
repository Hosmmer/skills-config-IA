---
name: grill-with-docs
description: Grilling session that challenges your plan against the existing domain model, sharpens terminology, and records hard decisions as ADRs. Use when user wants to stress-test a plan against their project's language and documented decisions.
---

<what-to-do>

Interview me relentlessly about every aspect of this plan until we reach a shared understanding. Walk down each branch of the design tree, resolving dependencies between decisions one-by-one. For each question, provide your recommended answer.

Ask the questions one at a time, waiting for feedback on each question before continuing.

If a question can be answered by exploring the codebase, explore the codebase instead.
If a question can be answered by reading the project's rules (check the project's AGENTS.md for the rules directory location), read them instead — they encode the team's codified design decisions and are the source of truth, not the user's memory.

</what-to-do>

<supporting-info>

## Wiki structure

Check the project's AGENTS.md for wiki location. The typical structure:

```
wiki/
├── contexts/
│   ├── CONTEXT-MAP.md          ← exists when multiple domains are in play
│   └── {domain}/
│       ├── CONTEXT.md          ← domain glossary
│       └── adr/                ← domain-specific decisions
│           ├── 0001-slug.md
│           └── 0002-slug.md
```

Create files lazily — only when you have something to write. If no `CONTEXT.md` exists for the domain, create it when the first term is resolved. If no `adr/` dir exists, create it when the first ADR is needed.

## During the session

### Challenge against the glossary

When the user uses a term that conflicts with the existing language in `CONTEXT.md`, call it out immediately. "Your glossary defines 'cancellation' as X, but you seem to mean Y — which is it?"

### Consult project rules

If a question can be answered by reading the project's rules (check the project's AGENTS.md for the rules directory location), read them instead of asking the user. Rules encode the team's codified design decisions — they are the source of truth, not the user's memory.

- **Resolve the right rules directory for the repo** — check AGENTS.md for the convention.
- **Read architecture rules first** when the question is about *where logic lives*, *how layers separate*, *which file pattern to follow*.
- **Quote the rule** when answering — "Per `01-architecture.md` line 42, validation goes in the service, not the serializer. OK?"
- **Flag conflicts**: if a rule contradicts something the user just said, surface it: "Your rule says X, but you just said Y — which is right?"
- If the rule is silent on the question, then ask the user.

### Cross-reference with rules

When the user states how something should work, check whether the rules agree. Architecture rules are not "examples" or "guidelines" — they are the team's codified decisions.

### Sharpen fuzzy language

When the user uses vague or overloaded terms, propose a precise canonical term. "You're saying 'account' — do you mean the Customer or the User? Those are different things."

### Flag UI/UX-affecting decisions in isolation

When a decision in this session would change what the user sees or how they interact (a button added/removed/moved, a flow altered, default behavior changed, copy changed) — even if it surfaces as a side effect of answering a different question — stop and raise it as its own explicit callout, separate from whatever question is in progress:

> **UI/UX CHANGE**: [screen/component] — [before] → [after]. Do you approve this specific change?

A "yes" to a broader design question never counts as approval for a UI/UX change buried inside it. Wait for an explicit answer to the callout itself before moving on.

### Discuss concrete scenarios

When domain relationships are being discussed, stress-test them with specific scenarios. Invent scenarios that probe edge cases and force the user to be precise about the boundaries between concepts.

### Cross-reference with code

When the user states how something works, check whether the code agrees. If you find a contradiction, surface it: "Your code cancels entire Orders, but you just said partial cancellation is possible — which is right?"

### Offer ADRs sparingly

Only offer to create an ADR when all three are true:

1. **Hard to reverse** — the cost of changing your mind later is meaningful
2. **Surprising without context** — a future reader will wonder "why did they do it this way?"
3. **The result of a real trade-off** — there were genuine alternatives and you picked one for specific reasons

If any of the three is missing, skip the ADR. ADRs go in the domain's `adr/` directory. Use the format in [ADR-FORMAT.md](./ADR-FORMAT.md).

</supporting-info>
