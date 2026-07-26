---
name: grill-with-docs
description: Grilling session that challenges your plan against the existing domain model, sharpens terminology, and records hard decisions as ADRs. Use when user wants to stress-test a plan against their project's language and documented decisions.
---

Run a grilling session using the `grill-me` skill pattern. Additionally, use the `domain-modeling` skill to:

- Challenge every term against the project's CONTEXT.md glossary
- Propose precise canonical names for fuzzy or overloaded terms
- Stress-test domain relationships with concrete edge-case scenarios
- Cross-reference stated behavior against the actual codebase
- Update CONTEXT.md inline when terms resolve
- Offer ADRs when a decision is: hard to reverse, surprising without context, and the result of a real trade-off

## Before the session

Read `.opencode/AGENTS.md` for:
- **Wiki** section — where docs live (content API or local paths)
- **Rules** section — codified design decisions that override user memory
- **Architecture Rules** — where logic lives, how layers separate

If a question can be answered by reading the project's rules, read them instead of asking the user. Rules are the source of truth.

## During the session

- Ask one question at a time. Wait for feedback before continuing.
- If a question can be answered by exploring the codebase, explore it.
- Flag UI/UX-affecting decisions as isolated callouts — a "yes" to a broader question never counts as UI/UX approval.
- When the user uses a term that conflicts with CONTEXT.md, call it out immediately.

## After the session

All wiki updates happen in the Document step of the pipeline, not during this session. Do NOT write to the wiki here — just capture decisions to be recorded later.
