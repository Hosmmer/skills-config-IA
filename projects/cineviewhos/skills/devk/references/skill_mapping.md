# Skill Mapping

> **DEPRECATED** — This file is no longer used for plan generation.
>
> The new flow reads `references/repo_skills_discovery.md` instead, which
> discovers project-local skills from the repo's own skills directory
> at plan time. Stop reading this file and read `repo_skills_discovery.md`.

Lookup table for tagging `Skills:` lines on plan phases. Read this during the
planning phase (per `create_plan.md`) before finalizing any phase.

## How to use this file

For each phase in a plan:

1. Identify the **primary stack area** the phase touches (backend, frontend, or cross-cutting).
2. Start with that area's **Default** skills.
3. Add any **Conditional** skills whose triggers match what the phase does.
4. Always consider the **Cross-cutting** section — those skills apply regardless of stack.
5. Emit a single line: `Skills: skill-a, skill-b, skill-c`
6. If genuinely nothing applies (e.g. a pure docs phase), emit `Skills: (none)` explicitly — never omit the line.

Keep tags minimal. Tagging every skill on every phase defeats the purpose.

---

## Backend (any language/framework)

### Default (every backend phase)

- `backend-code-style`
- `backend-error-handling`

### Conditional

Add when the phase matches the trigger:

| Skill | Trigger |
|---|---|
| `backend-testing` | Writing or restructuring tests |
| `backend-async` | Async/await, background tasks |
| `backend-data-modeling` | New domain types, models, dataclasses |
| `backend-database` | ORM, migrations, raw SQL |
| `backend-security` | Authn/authz, input validation, untrusted data |
| `backend-performance` | Performance-sensitive path, profiling |
| `backend-observability` | Logging, tracing, structured logs |
| `backend-dependency-management` | Package changes, version bumps |
| `backend-project-layout` | Creating new modules/packages |
| `backend-cli` | Building CLI commands |
| `backend-api` | API endpoints, serializers, routing |
| `backend-documentation` | Writing docstrings, API docs |

---

## Frontend (any framework)

### Default (every frontend phase)

- `frontend-best-practices`
- `frontend-patterns`

### Conditional

Add when the phase matches the trigger:

| Skill | Trigger |
|---|---|
| `frontend-hooks` | Custom hooks, state management |
| `frontend-state-management` | Store, Context, state patterns |
| `frontend-forms` | Form handling, validation |
| `frontend-data-fetching` | API calls, TanStack Query, SWR |
| `frontend-api-routes` | API routes, route handlers |
| `frontend-auth` | Auth, middleware, session handling |
| `frontend-performance` | Image optimization, lazy loading, bundle size |
| `frontend-design` | Visual/UX changes, component design, layout |
| `frontend-typescript` | Strict types, generics |
| `frontend-testing` | Component/hook/e2e testing |
| `frontend-accessibility` | a11y, ARIA, keyboard navigation |

---

## Cross-cutting (any stack)

| Skill | Trigger |
|---|---|
| `pre-mortem` | Shared mutable state, ordering dependencies, new resource lifecycles |
| `security-review` | User input, authentication, permissions, sensitive data |

---

## When to update this file

- New skill installed → add it with a clear trigger
- Skill consistently over-tagged → tighten its trigger
- Skill consistently under-tagged → broaden its trigger or move to Default
- New stack area introduced → add a new section
