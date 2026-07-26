# XenodocIA

Project manifest — internal Jira + Confluence tool for CineViewHos.

## Repos

| Path | Stack |
|------|-------|
| `backend/` | Django 5.2 + DRF + PostgreSQL |
| `frontend/` | React 18 + Vite + TypeScript |

## Tracker (self-hosted)

| Key | Value |
|-----|-------|
| type | xenodocia |
| url | `http://localhost:8002` (env `XENODOCIA_URL`) |
| auth | `Authorization: Token {XENODOCIA_API_TOKEN}` |
| tickets_api | `{url}/api/tickets/` |
| content_api | `{url}/api/content/` |
| content_tree | `{url}/api/content/tree/?prefix=...` |
| comments_api | `{url}/api/tickets/{id}/comments/` |
| promote_api | `{url}/api/tickets/{id}/promote/` |
| ticket_id_format | `{BOARD_PREFIX}-{NNN}` (e.g. XE-042) |

## Wiki (hybrid: Postgres metadata + thoughts/ files)

| Key | Value |
|-----|-------|
| backend | local-files |
| storage | `thoughts/` directory (S3 in production via `default_storage`) |
| contexts_path | `thoughts/wiki/contexts/{domain}/` |
| tickets_path | `thoughts/tickets/YYYY-MM/{TICKET_ID}/` |
| adr_path | `thoughts/wiki/contexts/{domain}/adr/` |
| specs_path | `thoughts/wiki/contexts/{domain}/specs/` |
| description_file | `description.md` |
| spec_suffix | `_spec.md` |
| plan_suffix | `_plan.md` |
| comments_dir | `comments/NNN.json` |

## Toolchain

| Key | Command | Workdir |
|-----|---------|---------|
| fe/typecheck | `npx tsc --noEmit` | `frontend/` |
| fe/build | `npm run build` | `frontend/` |
| fe/dev | `npm run dev` | `frontend/` (Vite :5174) |
| be/check | `python manage.py check` | `backend/` |
| be/test | `pytest` | `backend/` |
| be/lint | `ruff check` | `backend/` |
| be/migrations | `python manage.py makemigrations` | `backend/` |
| dev | `make run-dev` | root (Django :8002 + Vite :5174) |

## Workflow

| Key | Value |
|-----|-------|
| kanban | backlog → todo → in-progress → qa → ready-to-deploy → done |
| ai_transitions | in-progress, qa, ready-to-deploy |
| valid_statuses | backlog, todo, in-progress, qa, ready-to-deploy, done, roadmap |
| valid_types | epic, story, task, bug, subtask |
| valid_priorities | critical, high, medium, low |
| branch_format | `{type}/{TICKET-ID-slug}` (type: feat\|fix) |
| pr_format | `{TICKET-ID}: description` |
| commit_format | `{type}(scope): description [{TICKET-ID}]` |

## Architecture Rules

Read `.opencode/rules/01-architecture.md` for full rules.

Key constraints:
- All `thoughts/` I/O through `apps/tickets/services.py` or `apps/wiki/services.py`
- Ticket IDs use `select_for_update()` on Board (atomic)
- Status changes always record `TicketStatusHistory`
- Search vectors updated by `post_save` signal, never directly
- File paths derived from ticket ID, never stored in models
- `Board.next_id` is unreliable — always compute from existing tickets, get human confirmation

## Process Rules

### Bug Documentation (MANDATORY — never skip)

Every bug in xenodocIA must follow the full diagnosis pipeline:
1. Ticket created with `type: bug` + `description.md`
2. `/diagnosing-bugs` skill loaded — 7-phase pipeline
3. Feedback loop built and confirmed red-capable
4. Root cause documented in ticket comments
5. Regression test added (or seam absence documented)
6. Post-mortem comment: symptom, root cause, fix, guard
7. If architecture prevented a proper test seam → new refactor ticket

### ADR Enforcement (MANDATORY — never skip)

Every architectural decision in xenodocIA MUST produce an ADR when ALL three are true:
1. Hard to reverse (meaningful cost to change later)
2. Surprising without context (future reader would wonder "why?")
3. Real trade-off (genuine alternatives existed)

ADR path: `thoughts/wiki/contexts/xenodocIA/adr/NNNN-slug.md`
Link from the originating ticket. Number sequentially.

### Ticket Creation Rules
- Every non-trivial change → ticket first (`/create-ticket`)
- `description.md` mandatory — always PUT via content API
- No em dashes in API titles (corrupts to `?`). Use `:` instead.
- ID confirmed by human — never trust `Board.next_id` or API response
- Ticket ID in all commits: `[{TICKET-ID}]`

### QA Rules
- QA human-led — guide, never self-execute
- Every bug fix must be verified manually before closing

## Skills

- **Global** — `~/.config/opencode/skills/{name}/SKILL.md` (project-agnostic, reads config from this AGENTS.md)
- **Repo-local** — `.opencode/skills/{name}/SKILL.md` (xenodocIA-specific overrides)
- **Domain loading** — `.opencode/rules/domain-skills.md` controls which skills auto-load

### Skill Resolution Order
1. Global skill loaded first (defines workflow)
2. Repo-local override extends it (xenodocIA-specific config: tracker URLs, architecture constraints, ADR enforcement)
3. Domain skills load per path match

### Available Skills (repo-local)
| Skill | Purpose |
|-------|---------|
| `devk` | Development router (SDD pipeline + architecture constraints) |
| `diagnosing-bugs` | 7-phase bug pipeline → post-mortem → ADR → refactor ticket |
| `domain-modeling` | CONTEXT.md updates, ADR creation, term sharpening |
| `grill-with-docs` | Stress-test plans against domain model + architecture rules |
| `code-review` | Two-axis review: 8 backend rules + 4 frontend rules |
| `create-ticket` | Ticket creation via API (computes next ID, human confirmation) |
| `add-comment` | Comments: decisions, progress notes, blockers, post-mortems |
| `promote` | Spec → wiki page promotion + ADR/CHANGELOG offer |
| `update-status` | Kanban status transitions (backend records history) |

### Domain Skills (auto-loaded)
| Paths | Skills |
|-------|--------|
| `backend/**/*` | `django-cineviewhos` + `backend-testing` |
| `frontend/**/*` | `react-CineViewHos` + `tailwind-design` + `frontend-testing` + `ui-ux-pro-max` |
| Bug reports | `diagnosing-bugs` (always) |
| Any architecture | `codebase-design`, `domain-modeling` |

## Storage Notes

- `thoughts/` is tracked by git (local files in dev, S3 in production)
- `THOUGHTS_DIR` env var points to the `thoughts/` folder
- `ticket_dir()` scans `tickets/????-??/` to find existing ticket dirs
- Content API is the generic read/write interface (`GET/PUT /api/content/`)
