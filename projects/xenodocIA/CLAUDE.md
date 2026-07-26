# XenodocIA

Internal Jira + Confluence tool for CineViewHos. Django 5.2 backend, React frontend.

> **Canonical config**: `.opencode/AGENTS.md` — read that first for tracker, toolchain, workflow, and rules.
> **Rules**: `.opencode/rules/` — architecture and diagnostics rules.
> **Skills**: `.opencode/skills/` — project-specific skills (devk, diagnosing-bugs, domain-modeling, code-review, etc.).

## Quick Reference

### Running
```bash
make run-dev    # Django :8002 + Vite :5174
make test       # pytest (backend)
make migrate    # apply DB migrations
```

### Golden Rules
1. **Non-trivial change → create a ticket first.** Use `/create-ticket`.
2. **All file ops through services layer** — never read/write `thoughts/` directly.
3. **Ticket ID in commits**: `type(scope): description [{TICKET-ID}]`.
4. **Every bug → full diagnosing-bugs pipeline → post-mortem.** Never skip.
5. **Every architectural decision → ADR.** When hard to reverse + surprising + real trade-off.

### Skills (project-specific overrides)
| Command | What it does |
|---------|-------------|
| `/devk` | Development router (SDD pipeline) |
| `/create-ticket` | Create ticket via API |
| `/update-status` | Move ticket to new status |
| `/add-comment` | Append comment (decisions, post-mortems) |
| `/promote` | Promote spec → wiki page |
| `/diagnosing-bugs` | 7-phase bug diagnosis + post-mortem |
| `/domain-modeling` | Build glossary + create ADRs |
| `/grill-with-docs` | Stress-test plans + domain docs |
| `/code-review` | Two-axis review (standards + spec) |

### Hybrid Storage
| Data | Where |
|------|-------|
| Ticket metadata | Postgres |
| Ticket content | `thoughts/tickets/YYYY-MM/{ID}/*` |
| Wiki content | `thoughts/wiki/{scope}/{slug}/*` |

All file I/O through `apps/tickets/services.py` or `apps/wiki/services.py`.
