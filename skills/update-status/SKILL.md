---
name: update-status
description: Update a ticket's status via the project's tracker API. Reads tracker config and kanban columns from .opencode/AGENTS.md. Use when user wants to change ticket status or runs /update-status.
---

# update-status

Update a ticket's status.

## Configuration

Read `.opencode/AGENTS.md` → **Tracker** and **Workflow** sections for:
- API URL, auth header
- Valid kanban status columns
- Status update endpoint

## Steps

1. **Fetch current status**: `GET {tickets_api}{ticket_id}/`
2. **Show transition**: Print `{old_status} → {new_status}`
3. **Validate**: New status must be a valid column from the kanban. Read AGENTS.md workflow section.
4. **Update**: `PATCH {tickets_api}{ticket_id}/` with `{"status": "{new_status}"}`
5. **Confirm**: Print old → new status.

## Error handling

- 404 → "Ticket {ID} not found."
- Invalid status → List valid columns from AGENTS.md and ask user to pick.
- 401/403 → "Auth failed."
