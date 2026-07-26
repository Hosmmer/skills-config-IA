---
name: devk
description: Development router. Classifies tasks by complexity and routes through the right workflow (simple change, bug fix, or spec-driven feature). Use when user runs /devk or asks to start working on a development task.
---

# DevK — Development Router

Route development tasks through the right workflow.

## Configuration

Before anything else, read the project manifest at `.opencode/AGENTS.md`. Extract:

- **Tracker**: URL, auth header, ticket API, content API, kanban columns
- **Toolchain**: lint, test, build, typecheck commands per repo
- **Workflow**: branch naming, PR format, commit format, CI checks
- **Rules**: domain rules, architecture rules, process rules

If AGENTS.md is missing, tell the user: "No `.opencode/AGENTS.md` found. Run setup first."

## Execution Contract

This is a **binding contract**. The agent MUST:

- Follow steps in order — no skipping, no "shortcutting"
- Read referenced files BEFORE acting — never from memory
- Get explicit approval at every gate (spec, plan, phase transitions)
- Never write code before the plan is approved
- Never declare done before QA passes AND docs are complete
- **All ticket types** require QA → Document → ready-to-deploy. No ticket moves to `ready-to-deploy` without docs.
- Surface deviations immediately — don't silently adapt
- Log progress — user should never ask "where are we?"

## Session Opening (no argument)

```
What do you want to work on?
- A ticket ID (I'll fetch it)
- A new task (describe it)
```

## Input Detection

| Input | Action |
|-------|--------|
| Ticket ID | Fetch from tracker. **Move to `in-progress`**. Auto-assign if tracker supports it. Show title + description. Classify. |
| Ticket with existing spec | "Want me to generate the implementation plan?" → `references/generate_plan_from_spec.md` |
| Ticket with existing plan | "Ready to implement?" → `references/implement_plan.md` |
| Other / new task | Classify below ↓ |

## Task Classification

### Simple Change
≤3 files, no new business logic, no new endpoints, no data migrations.

→ `references/simple_change.md`

### Bug Fix
Broken behavior traced to specific code.

→ `references/diagnosing_bugs.md` (7-phase: feedback loop → reproduce → hypothesise → instrument → fix → cleanup → document). After QA: `/gcpush` + `/gpr`.

### Feature / Refactor
New capability, architectural change, or multi-file coordination.

→ **Spec-Driven Development**:

1. **Research** — `references/research.md`. Wiki first, then codebase.
2. **Grill** — Load `grill-with-docs` skill. Interview, sharpen terminology, record ADRs.
3. **Spec** — `references/generate_spec.md`. Publish via tracker's content API.
4. **Review Spec** — User approves. Iterate.
5. **Plan** — `references/generate_plan_from_spec.md`. Publish alongside spec.
6. **Review Plan** — User approves. Iterate.
7. **Implement** — `references/implement_plan.md`. Phase by phase. Verify with toolchain commands. After all phases, present the QA checklist.
8. **QA** — Move ticket to `qa`. Read `references/qa.md`. Human-led verification. **DO NOT skip this step.** Wait for human to confirm all items pass.
9. **Document** — AFTER QA passes. Read `references/document.md`. Present documentation plan (specs to promote, ADRs to record, CONTEXT.md updates). **Get explicit approval BEFORE writing.** Then execute — promote to wiki, record ADRs.
10. **Push & PR** — `/gcpush` then `/gpr`. After merge to main, move ticket to `ready-to-deploy`.

## Gate Enforcement (HARD RULES)

- **NEVER move ticket to `ready-to-deploy` before Step 9 (Document) is complete.**
- **NEVER skip Step 9 (Document).** After QA passes, the next action is ALWAYS documentation — not push, not deploy.
- **NEVER write documentation files before the human approves the documentation plan.**
- The ticket status flow MUST be: `in-progress` → `qa` → (wait for human) → Document → Push/PR → `ready-to-deploy`

## Golden Rule

```
Research → Present options → Get approval → Implement
Never skip gates. Never push before QA + docs.
```
