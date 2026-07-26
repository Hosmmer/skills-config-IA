---
description: Auto-load domain-specific skills when working on xenodocIA code.
globs:
  - "backend/**/*"
  - "frontend/**/*"
alwaysApply: true
---

# Domain Skills — XenodocIA

Load the appropriate domain skill before writing or modifying code.

## Frontend

Matches: `frontend/**/*`

| Skill | When |
|-------|------|
| `react-CineViewHos` | Always — React 18 + Vite + TypeScript patterns |
| `tailwind-design` | Always — UI design system |
| `frontend-testing` | Writing/changing tests |

## Backend

Matches: `backend/**/*`

| Skill | When |
|-------|------|
| `django-cineviewhos` | Always — Django + DRF service-layer patterns |
| `backend-testing` | Writing/changing tests |

## Architecture & Design

Matches: any file

| Skill | When |
|-------|------|
| `codebase-design` | Designing new modules, interfaces, or seams |
| `domain-modeling` | Working with xenodocIA terminology, CONTEXT.md, or ADRs |

## Diagnostics (auto-load on bug reports)

| Skill | When |
|-------|------|
| `diagnosing-bugs` | Any bug report, "fix this", "broken", "error", "crash" |
