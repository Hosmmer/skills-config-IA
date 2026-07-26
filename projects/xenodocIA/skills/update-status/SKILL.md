# update-status

Update the status of a XenodocIA ticket.

## Usage

```
/update-status <TICKET_ID> <new_status>
```

Valid statuses: `backlog`, `todo`, `in-progress`, `qa`, `ready-to-deploy`, `done`

## What this skill does

1. Read `XenodocIA_URL` and `XenodocIA_API_TOKEN` from environment. `XenodocIA_URL` defaults to production, `https://mbird.CineViewHos.com` (XE-53) — override to `http://localhost:8002` only when working on XenodocIA's own code locally.
2. PATCH `{XenodocIA_URL}/api/tickets/{TICKET_ID}/` with `{ "status": "<new_status>" }`.
3. Print the old and new status.
4. The backend automatically records the change in `TicketStatusHistory`.

## Request

```
PATCH {XenodocIA_URL}/api/tickets/{TICKET_ID}/
Authorization: Token {XenodocIA_API_TOKEN}
Content-Type: application/json

{ "status": "<new_status>" }
```

## Expected output

```
Updated XE-42: todo → in-progress
```

## Error handling

- 404 → "Ticket {TICKET_ID} not found."
- 400 → "Invalid status. Valid values: backlog, todo, in-progress, qa, ready-to-deploy, done"
- 401/403 → "Authentication failed. Check XenodocIA_API_TOKEN."

## Notes

- Fetch the current status first with `GET /api/tickets/{TICKET_ID}/` so you can report the old status in the output.
- The status change is automatically logged in the ticket's history tab in the UI.
