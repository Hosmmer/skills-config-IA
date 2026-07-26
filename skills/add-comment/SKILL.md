---
name: add-comment
description: Add a comment to a ticket via the project's tracker API. Reads tracker config from .opencode/AGENTS.md. Use when user wants to add a comment to a ticket or runs /add-comment.
---

# add-comment

Add a comment to a ticket.

## Configuration

Read `.opencode/AGENTS.md` → **Tracker** section for API URL and auth.

## Steps

1. Fetch the ticket to verify it exists: `GET {tickets_api}{ticket_id}/`
2. If tracker has a comments API: `POST {tickets_api}{ticket_id}/comments/`
3. If using content API: `PUT {content_api}` with `{"path": "tickets/{id}/comments/NNN.json", "content": "..."}`
4. Confirm: "Comment added to {TICKET_ID}"

## Error handling

- 404 → "Ticket {ID} not found. Check the ID."
- 401/403 → "Auth failed. Check tracker token."
