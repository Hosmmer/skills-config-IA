# Architecture Rules

## Hybrid storage — never bypass the services layer

All reads and writes to the `thoughts/` directory MUST go through:
- `apps/tickets/services.py` for ticket content (description, comments, spec, plan)
- `apps/wiki/services.py` for wiki pages

**Never** read or write files directly from views, serializers, signals, or management commands.

```python
# WRONG
with open(f"thoughts/{ticket_id}/description.md") as f:
    content = f.read()

# CORRECT
from apps.tickets.services import read_description
content = read_description(ticket_id)
```

## Ticket ID generation — always atomic

Ticket IDs (`{BOARD_PREFIX}-{N}`) are generated using `select_for_update()` on the Board row.
Never generate an ID by hand or outside of `TicketCreateSerializer.create()`.

## Status changes — always record history

When changing ticket status, create a `TicketStatusHistory` row. This is handled
automatically in `TicketViewSet.perform_update()`. If you bypass the viewset (e.g.
in management commands or tests), create the history row manually.

## Search vector — let the signal handle it

The `search_vector` field on `Ticket` is updated by a `post_save` signal in `TicketsConfig.ready()`.
Never update it directly. If you need to rebuild all vectors (e.g. after a data migration):

```python
from django.contrib.postgres.search import SearchVector
from apps.tickets.models import Ticket
Ticket.objects.update(
    search_vector=SearchVector('title', weight='A') + SearchVector('type', weight='C')
)
```

## File paths — derive, never store

No model stores file paths. The path to any ticket's content is always derived from the ticket ID:
- Description: `{THOUGHTS_DIR}/tickets/YYYY-MM/{TICKET_ID}/description.md`
- Comments: `{THOUGHTS_DIR}/tickets/YYYY-MM/{TICKET_ID}/comments/NNN.json`
- Spec: `{THOUGHTS_DIR}/tickets/YYYY-MM/{TICKET_ID}/*_spec.md` (scanned, not stored)
- Plan: `{THOUGHTS_DIR}/tickets/YYYY-MM/{TICKET_ID}/*_plan.md` (scanned, not stored)
