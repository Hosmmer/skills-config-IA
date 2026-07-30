---
description: QA verification after implementation — before documentation
---

# QA

You are the human's QA partner. QA has two steps: first the AI verifies what it can automatically, then the human verifies manually. Both must pass before documentation.

## When this runs

- **After every phase** that has manual verification steps in the plan
- **Before the Document step (step 9)** in the SDD pipeline
- QA is NOT optional — never skip it

## Step 1: Automated verification (AI)

Run every automated success criterion from the plan for the current phase(s):

- [ ] Tests pass: run the test command from the plan
- [ ] Linting passes: run the lint command (check AGENTS.md or the plan)
- [ ] Type checking passes if applicable (e.g., `tsc --noEmit`)
- [ ] Any other automated checks listed in the plan's success criteria

Fix every failure before presenting Step 2 to the human. If a check can't run (missing dependency, broken environment), flag it explicitly.

## Step 2: Manual verification (Human)

Present the manual verification checklist to the human in this format:

```
Phase [N] Complete - Ready for Manual Verification

Automated verification passed:
- [List automated checks that passed, with results]

Please perform the manual verification steps listed in the plan:
- [ ] [Manual verification item 1]
- [ ] [Manual verification item 2]
...

Let me know when manual testing is complete so I can proceed.
```

**When executing multiple phases consecutively**: consolidate all manual verification from every phase into a single combined checklist at the end. Do NOT skip Step 2 — it is never optional. Wait for human confirmation before proceeding to Documentation.

## Scope

| Ticket type | Method |
|-------------|--------|
| Backend-only | Admin panel or API testing |
| Includes frontend | Frontend UI |

## Steps for the human

1. Start the dev server if not running
2. Go through every acceptance criterion in the spec — perform the action, verify the result
3. Check the edge cases listed in the spec (errors, nulls, boundary values)
4. Verify no regressions in adjacent areas

## Report format

```
QA — [Admin Panel | Frontend UI]

[OK] [criterion] — [what you did and saw]
[FAIL] [criterion] — FAILED: [what went wrong]
```

Fix every failure before proceeding to documentation. Do NOT check off manual verification items in the plan until the human confirms them.

## Scope changes discovered during QA

When the human discovers during manual verification that the spec is missing something or the scope needs adjustment, do NOT silently fix it mid-QA. Instead:

1. **Update the spec** — add the new/modified acceptance criteria and business rules
2. **Update the plan** — add the new phases or steps needed to implement the changes
3. **Implement** — make the code changes
4. **Re-run QA** — go back to Step 1 (automated) and Step 2 (manual) for the new scope

```
QA found scope gap → Update spec → Update plan → Implement → QA again
```

This keeps the spec and plan as the authoritative source of truth. Never let the code drift from what the spec says.
