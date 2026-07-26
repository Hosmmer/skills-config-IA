---
name: diagnosing-bugs
description: Diagnosis pipeline for xenodocIA bugs. Mandatory 7-phase loop: feedback loop → reproduce → hypothesise → instrument → fix → cleanup → document. Use when user reports a bug or says "diagnose"/"debug this"/"broken"/"error"/"crash" in xenodocIA context.
---

# Diagnosing Bugs — XenodocIA

Load the global `diagnosing-bugs` skill first (`~/.config/opencode/skills/diagnosing-bugs/SKILL.md`). This file adds xenodocIA-specific rules.

## Mandatory Execution

**Every bug in xenodocIA MUST go through this pipeline. No exceptions.** The global skill defines the 7 phases. This file enforces xenodocIA-specific documentation requirements.

## XenodocIA-Specific Phase Details

### Phase 1 — Feedback Loop

Preferred approaches for xenodocIA (Django + React):
1. **Pytest** — fastest feedback. Test the Django service/view that fails.
2. **Curl against `localhost:8002`** — reproduce API bugs.
3. **Django shell** (`make shell`) — instant REPL for model/service logic.
4. **Browser + Playwright** — for frontend-only bugs on `localhost:5174`.
5. **`make run-dev`** — always start the dev server before debugging.

### Phase 4 — Instrumentation

For Django backend:
- Use `print()` or `logger.debug()` with `[DEBUG-xxxx]` prefix
- Check `django.db.connection.queries` for SQL inspection
- Use `breakpoint()` for interactive debugging

For React frontend:
- `console.log` with `[DEBUG-xxxx]` prefix
- React DevTools for component state
- Network tab for API call inspection

### Phase 5 — Fix + Regression Test

Test seams in xenodocIA:
- **Backend**: unit test at service layer (import service, call function, assert result) — preferred
- **Backend**: integration test at view level (APIClient, assert status/response)
- **Frontend**: Vitest + React Testing Library

If a correct seam doesn't exist for the bug → that IS the finding. Document it and recommend a refactor ticket.

### Phase 7 — Document (CRITICAL — never skip)

#### 7.1 Ticket Post-Mortem (MANDATORY)

Post as a comment on the bug ticket using `/add-comment`:
```markdown
## Post-Mortem
- **Symptom**: [user-visible behavior]
- **Root cause**: [which Phase 3 hypothesis was correct — `file:line`]
- **Fix**: [what changed — `file:line`]
- **Regression guard**: [test name + path | "no correct seam — ADR-{NNNN}"]
- **Seam quality**: [good | poor — why]
- **Prevention**: [what would have caught this earlier?]
```

#### 7.2 ADR (if applicable)

If the bug revealed an architectural weakness that requires a new decision:
- Create ADR in `thoughts/wiki/contexts/xenodocIA/adr/NNNN-{slug}.md`
- Link it from the post-mortem
- ADR template and rules in `.opencode/rules/02-diagnostics.md`

#### 7.3 Refactor Ticket (if applicable)

If the architecture prevented a proper test seam:
- Create a new ticket: `type: task`, priority based on severity
- Title: `Improve testability of {component}: add seam for {scenario}`
- Link from the post-mortem

## Completion Gate

The fix is NOT done until ALL are true:
- [ ] Post-mortem comment exists on the bug ticket
- [ ] All `[DEBUG-xxxx]` tags removed (`grep -r "DEBUG-" backend/ frontend/`)
- [ ] Regression test passes (or seam absence documented in ADR)
- [ ] If seam was poor → new refactor ticket created + ADR if applicable
- [ ] Original repro no longer reproduces
