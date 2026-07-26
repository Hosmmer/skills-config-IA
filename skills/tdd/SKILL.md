---
name: tdd
description: "Test-driven development with red-green-refactor loop. Use when the user wants to build features or fix bugs test-first, mentions 'red-green-refactor', or wants integration tests. Adapts to the project's current stack — check the project's AGENTS.md for test runner commands and testing conventions."
---

# Test-Driven Development

TDD is the red → green loop. This skill makes that loop produce tests worth keeping: what a good test is, where tests go, anti-patterns, and the rules of the loop. Consult every section before and during the loop, not after.

When exploring the codebase, read the project's domain contexts (CONTEXT.md) if they exist so test names and interface vocabulary match the project's domain language, and respect ADRs in the area you're touching.

## Stack-specific

Check the project's AGENTS.md for the current stack, test runner commands, and testing conventions. Look for testing skill files (e.g., `.opencode/skills/testing/SKILL.md`) for project-specific patterns.

### Backend

- Framework: check AGENTS.md (e.g., `pytest` + `pytest-django` for Django, `pytest` for FastAPI, etc.)
- Test location: check AGENTS.md for the test directory convention
- Run commands: check AGENTS.md or the project's Makefile/package.json for canonical test commands
- See the project's testing skill file for patterns

### Frontend

- Framework: check AGENTS.md (e.g., `Vitest` + `React Testing Library`, `Jest`, etc.)
- Test location: check AGENTS.md for conventions (co-located `*.test.ts(x)` or `src/__tests__/`)
- Run commands: check AGENTS.md or package.json scripts
- See the project's testing skill file for patterns

## What a good test is

Tests verify behavior through public interfaces, not implementation details. Code can change entirely; tests shouldn't. A good test reads like a specification — "user can login with valid credentials" tells you exactly what capability exists — and survives refactors because it doesn't care about internal structure.

## Seams — where tests go

A **seam** is the public boundary you test at: the interface where you observe behavior without reaching inside. Tests live at seams, never against internals.

**Test only at pre-agreed seams.** Before writing any test, write down the seams under test and confirm them with the user. No test is written at an unconfirmed seam. You can't test everything — agreeing the seams up front is how testing effort lands on the critical paths.

Ask: "What's the public interface, and which seams should we test?"

## Anti-patterns

- **Implementation-coupled** — mocks internal collaborators, tests private methods, or verifies through a side channel (querying the database instead of using the interface). The tell: the test breaks when you refactor but behavior hasn't changed.
- **Tautological** — the assertion recomputes the expected value the way the code does (`expect(add(a, b)).toBe(a + b)`), so it passes by construction and can never disagree with the code. Expected values must come from an independent source of truth — a known-good literal, a worked example, the spec.
- **Horizontal slicing** — writing all tests first, then all implementation. Bulk tests verify _imagined_ behavior. Work in **vertical slices** instead — one test → one implementation → repeat, each test a **tracer bullet** responding to what the last cycle taught you.

## Rules of the loop

- **Red before green.** Write the failing test first, then only enough code to pass it. Don't anticipate future tests or add speculative features.
- **One slice at a time.** One seam, one test, one minimal implementation per cycle.
- **Refactoring is not part of the loop.** It belongs to the review stage (see the `code-review` skill), not the red → green implementation cycle.
- **Run the test after every change.** Fast feedback is the point.
