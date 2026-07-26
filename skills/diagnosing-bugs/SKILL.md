---
name: diagnosing-bugs
description: Diagnosis loop for hard bugs and performance regressions. Use when the user says 'diagnose'/'debug this', or reports something broken/throwing/failing/slow. Triggers the full 7-phase pipeline: feedback loop → reproduce → hypothesise → instrument → fix → cleanup → document.
---

# Diagnosing Bugs

A discipline for hard bugs. Skip phases only when explicitly justified.

Before exploring, read `CONTEXT.md` if it exists to understand the relevant domain.

## Phase 1 — Build a feedback loop

**This is the skill.** Everything else is mechanical. A **tight** pass/fail signal that goes red on *this* bug will find the cause.

Try in this order:

1. **Failing test** at the seam that reaches the bug
2. **Curl / HTTP script** against a dev server
3. **CLI invocation** with fixture input, diff stdout
4. **Headless browser** (Playwright) — assert DOM/console/network
5. **Replay captured trace** — saved request/payload through code path
6. **Throwaway harness** — minimal subset exercising the bug path
7. **Property/fuzz loop** — 1000 random inputs looking for failure
8. **Bisection harness** — automate `git bisect run`
9. **Differential loop** — same input, old vs new version
10. **HITL bash script** — structured human-driven loop

Once you have a loop, **tighten it**: faster, sharper assertions, more deterministic. A 30s flaky loop is barely useful; a 2s deterministic one is a superpower.

**Completion**: one command, already run, that is red-capable (catches THIS bug), deterministic, fast (<5s), and agent-runnable.

If you can't build a loop, stop. Don't proceed to hypothesise. List what you tried. Ask for environment access, captured artifacts, or permission for production instrumentation.

## Phase 2 — Reproduce + minimise

Run the loop. Watch it go red — the *user's* symptom, not a nearby failure.

Shrink to **smallest scenario that still goes red**. Cut inputs, callers, config one at a time. Done when every remaining element is load-bearing.

## Phase 3 — Hypothesise

Generate **3–5 ranked, falsifiable hypotheses** before testing any:

> "If X is the cause, then changing Y makes the bug disappear / changing Z makes it worse."

Show the ranked list to the user before testing. They may re-rank instantly. Don't block on it — proceed if AFK.

## Phase 4 — Instrument

One variable at a time. Map each probe to a hypothesis from Phase 3.

1. Debugger/REPL if available — one breakpoint beats ten logs
2. Targeted logs at boundaries that distinguish hypotheses
3. Never "log everything and grep"

Tag every debug log: `[DEBUG-xxxx]`. Cleanup = one grep.

## Gate — Get explicit approval before fixing

After Phase 4 (instrumentation confirms the root cause), **stop and present findings to the user**. Use the `question` tool or ask for a simple yes/no. DO NOT touch code until receiving a "yes" or equivalent.

Present:
- Confirmed hypothesis (root cause)
- Files to modify and what will change in each
- No more than 2 approaches if multiple options exist

```
I recommend X because [brief reason]. Shall I proceed with the fix?
```

## Phase 5 — Fix + regression test

Write the regression test **before the fix** — only if a correct seam exists.

A correct seam exercises the *real bug pattern* at the call site. If no correct seam exists, that itself is the finding — flag it.

1. Turn minimised repro into a failing test
2. Watch it fail
3. Apply the fix
4. Watch it pass
5. Re-run the Phase 1 loop against the original scenario

## Phase 6 — Cleanup + post-mortem

- [ ] Original repro no longer reproduces
- [ ] Regression test passes (or seam absence documented)
- [ ] All `[DEBUG-...]` instrumentation removed
- [ ] Throwaway prototypes deleted
- [ ] Correct hypothesis stated in commit/PR message

**Ask: what would have prevented this bug?** Recommend architectural changes after the fix, not before.

## Phase 7 — Document

1. **Ticket post-mortem**: symptom, root cause, fix, regression test, seam quality
2. **Wiki**: promote reusable findings (new concepts, ADRs, patterns)
3. **Next-step**: if architecture prevented a proper test seam, recommend a refactor ticket
