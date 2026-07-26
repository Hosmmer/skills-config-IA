# Park

Capture a ticket idea before it's lost. Three questions, one user story, one destination.

## Flow

1. **Interview** — 3 focused questions (who, what, why) → `references/interview.md`
2. **Draft** — User story + 2–3 acceptance criteria → show, get approval
3. **Route** — Identify destination → `references/routing.md`
4. **Write** — Create or update files based on route

## Destinations

| Route | What gets created |
|-------|-------------------|
| Backlog standalone | Ticket via the API (`status: backlog`), description via content API |
| Backlog + active project | Same + update phase file with ticket ID |
| Roadmap project | New PI-N row in phase file — no ticket yet |
| Future, no project | User chooses: new project (→ `/planning`) or backlog standalone |

## Golden rule

Draft → Show to human → Approve → Write.
