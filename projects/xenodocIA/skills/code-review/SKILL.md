---
name: code-review
description: Two-axis review of xenodocIA code changes. Standards axis: does it follow architecture rules + service-layer patterns? Spec axis: does it faithfully implement the ticket? Use when reviewing a branch, PR, or WIP changes in xenodocIA.
---

# Code Review — XenodocIA

Two-axis review. Load the global `code-review` skill first. This file adds xenodocIA-specific standards.

## Architecture Standards (xenodocIA-specific)

Check every file in the diff against these rules from `.opencode/rules/01-architecture.md`:

### Backend

1. **Service layer**: All `thoughts/` I/O through `apps/tickets/services.py` or `apps/wiki/services.py` — never direct filesystem in views/serializers
2. **Layer separation**: models → serializers → services → views. No business logic in views or serializers.
3. **Storage**: Uses `default_storage` — never hardcodes `open()`, `os.path`, or S3 paths
4. **Ticket IDs**: Uses `select_for_update()` — never generates IDs manually
5. **Status history**: Status changes go through `perform_update()` — always records `TicketStatusHistory`
6. **Search vectors**: Updated by `post_save` signal — never touched directly
7. **Path derivation**: File paths derived from ticket ID — never stored in model fields
8. **Content API paths**: Must pass `storage_paths.py` validators — no path traversal

### Frontend

1. **API client**: All HTTP calls through `src/api/` functions — never raw fetch in components
2. **State**: Zustand stores in `src/store/` — no ad-hoc component state for shared data
3. **Components**: shadcn/ui components used — no reinvention of basic UI elements
4. **Auth**: Token in `Authorization` header via `src/api/client.ts` — never hardcoded

### Both

1. **No hardcoded URLs** — use env vars or config
2. **No console.log left behind** — only intentional logging
3. **Error handling** — all API calls handle 401/403/5xx
4. **PR scope**: Does the PR match the ticket scope? No scope creep.

## Spec Alignment

For each ticket referenced in commits:
1. All acceptance criteria from the spec are implemented
2. No scope creep (code not asked for in the spec)
3. Implementation matches the plan phases

## Common Smells in xenodocIA

- Direct `open()` or `os.path` on `thoughts/` files → should use services
- `ticket.status = "x"` without `save()` and `TicketStatusHistory` → use `perform_update()`
- `board.next_id` used for ID generation → never use this, always compute from existing tickets
- Raw SQL for search → use `search_vector` + GinIndex
- `Authorization: Bearer` header → must be `Authorization: Token`
