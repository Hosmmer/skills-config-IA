---
name: devk
description: "Developer Skills Router. Use when starting a development task — classifies complexity (simple change, bug fix, feature/refactor) and routes through the right workflow (SDD pipeline: Research → Grill-with-docs → Spec → Plan → Implement → QA → Document → Push & PR)."
---

# Developer Skills Router

Route development tasks through the right workflow based on complexity and environment.

## Execution Contract (mandatory)

This skill is a **binding contract**, not a reference. When loaded, the agent MUST:

- **Follow steps in order.** Pipeline order is not a suggestion. No skipping, reordering, or "shortcutting" without explicit user approval.
- **Read referenced files before acting.** When a step says "Read `references/X.md`", read it BEFORE doing the step. Never act from memory or assumption.
- **Get explicit approval at every gate.** Spec, plan, and each phase transition require user approval before proceeding.
- **Never write code before the plan is approved.** The Golden Rule has no exceptions.
- **Never declare done before QA passes AND documentation is complete.**
- **Surface deviations immediately.** If reality contradicts the plan, stop and present the issue. Do not silently adapt.
- **Log the workflow.** State which step you are on, what you read, and what comes next. The user should never have to ask "where are we?".

Treat any attempt to skip a step as a bug in the agent's behavior, not a feature.

## Environment Detection (automatic — NEVER ask the user)

Detect your environment by checking which tools are available. Don't ask.

Read the project's AGENTS.md for:
- The ticket tracker URL and API configuration
- The content/wiki API conventions
- Domain skills location (check `references/repo_skills_discovery.md`)
- Project rules location
- Test/lint commands

## Session Opening (when no argument is passed)

If `/devk` is invoked without arguments, ask:

> **What do you want to work on today?**
> - A backlog ticket
> - A ticket in progress (give me the ID)
> - A new task (describe it)

If the user already passed an argument, skip this question and go directly to Input Detection.

## Input Detection (do this BEFORE task classification)

Check what the user passed as input. If it matches a known artifact, jump directly to the right pipeline stage — do NOT run earlier steps.

| User input | Action |
|------------|--------|
| Ticket ID or number (e.g. `{TICKET_ID}`, `42`, "el ticket 42") | **Open for work** — normalize to the ticket ID format. Fetch the ticket from the project's tracker API to verify it exists. If 404 → *"I couldn't find {TICKET_ID}. Is the ID correct?"* and wait. Otherwise show the title/description. **Opening a ticket with `/devk` means starting work on it, so move it to `in-progress` automatically from either `backlog` or `todo`.** Do NOT ask for confirmation of the move; just do it and inform the user. If the ticket is already at or past `in-progress`, leave its status untouched and just proceed — never move it backwards. **Auto-assign:** if the project's tracker supports assignees, opening a ticket with `/devk` also assigns it to the current user automatically (no prompt). If the ticket already has an assignee, leave it untouched (do not reassign). Then continue with Task Classification. |
| Ticket ID that already has a spec | **Spec shortcut** — ask: *"I see you have the spec for {TICKET_ID}. Want me to generate the implementation plan?"* If yes → read `references/generate_plan_from_spec.md` and proceed. Skip Research, Grill-me, Generate Spec, and Review Spec steps entirely. |
| Ticket ID that already has a plan | **Plan shortcut** — ask: *"Ready to start implementing {TICKET_ID}?"* If yes → read `references/implement_plan.md` and proceed. Skip everything up to Execute Implementation. |
| Anything else | Fall through to Task Classification below. |

## Task Classification (do this FIRST — before reading any reference)

Classify the task by complexity and load ONLY the corresponding reference:

### 1. Simple change
≤ 3 files, no new business logic, no new endpoints, no data migration complexity.

→ Read `references/simple_change.md`

*Examples: rename a field, change FK to OneToOneField, add a validation, fix a typo, add a field to serializer, adjust a filter.*

### 2. Bug fix
Fix broken behavior. Follow a structured diagnosis loop — from building a tight feedback loop that reproduces the bug, through falsifiable hypotheses, to a regression-test-gated fix.

→ Read `references/diagnosing_bugs.md`

*Structured 7-phase diagnosis: Build feedback loop → Reproduce + minimise → Hypothesise → Instrument → Fix + regression test → Cleanup + post-mortem → Document. Skip phases only when explicitly justified. Research (`references/research.md`) is embedded within the hypothesis phase — loaded when you need to explore the codebase. After QA passes, run `/gcpush` + `/gpr` (Document is Phase 7, not a separate step).*

### 3. Feature / Refactor
New capability, architectural change, or multi-file coordination with new logic.

→ **Spec Driven Development (SDD) pipeline:**

**Entry points (explicit indicators):**
- **No ticket provided**: Full SDD pipeline from scratch (will create a ticket via `/create-ticket` when reaching Generate Spec)
- **Ticket already has a spec**: Generate plan from spec → Review → Implement
- **Ticket already has a plan**: Implement directly

**Full SDD Pipeline:**

1. **Research** — Read `references/research.md`. Investigate the wiki and codebase, understand current state. Check the project's AGENTS.md for wiki location.
2. **Grill-with-docs** — Load the `grill-with-docs` skill. Interview the user about requirements one question at a time. Challenge against the domain glossary. Do NOT update `CONTEXT.md` during this phase — wiki docs are updated in the Document step (step 9). Prior research ensures the grill is informed by code reality, not assumptions.
3. **Generate Spec** — Read `references/generate_spec.md`. Generate formal specification document from research + grill findings. Publish via the project's tracker/content API.
4. **Review & Edit Spec** — User reviews and edits spec. Iterate until specification is approved.
5. **Generate Plan** — Read `references/generate_plan_from_spec.md`. Generate implementation plan from approved spec.
6. **Review & Approve Plan** — User reviews plan. Iterate until plan is approved.
7. **Execute Implementation** — Read `references/implement_plan.md`. Execute phase by phase.
8. **QA** — **First, move the ticket to `qa` automatically** and inform the user — no confirmation prompt. Then read `references/qa.md`. All criteria must pass before proceeding.
9. **Document** — Read `references/document.md`. Promote the ticket to the project's wiki (CONTEXT.md, specs/, CHANGELOG, release queue if commercial). Record hard decisions as ADRs in the domain's `adr/` directory.
10. **Push & PR** — `/gcpush` then `/gpr`.

## Golden rule (applies to ALL paths)

```
Research → Present findings + options → Get explicit approval → Implement
```

**For Feature/Refactor with SDD:**
```
Research → Grill-with-docs → Generate Spec → Approve Spec → Generate Plan → Approve Plan → Implement → QA → Document → /gcpush + /gpr
```

**For Bug Fix:**
```
Build feedback loop → Reproduce + minimise → Hypothesise → Instrument → Fix + regression test → Cleanup + post-mortem → Document → /gcpush + /gpr
```

**Never write code before the user approves the plan (or spec, if generating plan next).**
**Never push before QA passes and documentation is complete.**

If the user has to ask "where's the spec?" or "where's the plan?" — you failed the workflow.
