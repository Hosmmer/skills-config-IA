---
description: Document a completed ticket in the project's wiki after QA passes
---

# Document

Run this after QA passes. This is the wiki promotion step for a completed ticket.

---

## Before executing anything — present a documentation plan

Scan the spec and produce a documentation plan. Present it to the human
and **wait for explicit approval before writing a single file.**

The plan must cover:

- **Terms** — which terms you'll add or update in CONTEXT.md, and why each qualifies. If none, say so explicitly.
- **UX doc** — which file you'll update or create, and which sections change. If none, say so explicitly.
- **Spec** — the target path and whether the frontmatter is complete.
- **ADR** — whether any decision passes the three-question test. If none, say so explicitly.
- **CHANGELOG entry** — draft it inline so the human can catch phrasing issues.

If the human corrects or expands the plan, update it and re-present before proceeding.

---

## Execution

Once approved, promote the ticket to the project's wiki. Check the project's AGENTS.md for the wiki promotion workflow and content API configuration.
