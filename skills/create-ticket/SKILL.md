---
name: create-ticket
description: Create a ticket via the project's tracker API. Reads tracker config from .opencode/AGENTS.md. Use when user wants to create a ticket or runs /create-ticket.
---

# create-ticket

Create a ticket via the project's configured tracker.

## Configuration (read first)

Read `.opencode/AGENTS.md` for the **Tracker** section. Extract:

- `url` — base URL
- `auth` — header format (e.g. `Authorization: Token {ENV_VAR}`)
- `tickets_api` — endpoint path (e.g. `{url}/api/tickets/`)
- `content_api` — if using a content API for descriptions
- `types`, `priorities`, `statuses` — if listed

If AGENTS.md is missing or has no Tracker section, ask: "Where should I create tickets? (URL + auth)"

## Steps

### 1. Gather required info

Minimum: **title** + **description**. Description is MANDATORY — never create a ticket without one.

Optional (from user or AGENTS.md defaults):
- Type: task, story, bug, epic, subtask
- Priority: low, medium, high, critical
- Status: backlog, todo (first column from kanban)
- Board/project ID (if multi-board tracker)

### 2. Determine next ticket ID

If the tracker uses sequential IDs:
- `GET {tickets_api}` to list existing tickets
- Find max ID, propose next
- Wait for user confirmation before creating

### 3. Create the ticket

```
POST {tickets_api}
Headers: {auth}
Body: { title, type, priority, status, board/project }
```

### 4. Save description

If tracker has a content API: `PUT {content_api}` with `{"path": "tickets/{id}/description.md", "content": "..."}`

If tracker embeds descriptions in the ticket body: include in the POST.

### 5. Output

```
Created: {TICKET_ID}
URL: {tracker_url}/tickets/{TICKET_ID}
Title: {title}
Status: {status}
```

## Error handling

- 401/403 → "Auth failed. Check tracker token in AGENTS.md or .env."
- 400 → Print validation errors from response body.
- Connection refused → "Cannot reach tracker at {url}. Is it running?"
- Duplicate title → Warn user, ask to confirm or rename.

## Implementation

- Use Python `urllib` (available everywhere, no dependencies).
- Never hardcode URLs, tokens, or API paths.
- After creating, verify the ticket is reachable (GET it back).
