---
name: create-ticket-roadmap
description: Create a ticket directly in the roadmap (status: roadmap) for future features and ideas. Routes through a quick interview, drafts the ticket, gets approval, publishes via the tracker API, and syncs the roadmap wiki. Use when user wants to capture a future idea, plan a feature, or runs /create-ticket-roadmap.
---

# Create Ticket Roadmap

Capture a future feature idea, publish it to the roadmap, and keep the wiki in sync.

## Configuration

Read the project's `.opencode/AGENTS.md` for tracker config:
- `XENODOCIA_URL` — base URL
- `XENODOCIA_API_TOKEN` — auth token (header: `Authorization: Token {token}`)
- Tickets API: `{XENODOCIA_URL}/api/tickets/`
- Content API: `{XENODOCIA_URL}/api/content/`

Use Python `urllib` (no dependencies). Never hardcode URLs or tokens.

## Flow

### 1. Interview (3 questions)

Ask the user:
1. **What** — describe la feature o idea en una frase
2. **Who** — para quien es esta feature? (admin, usuario final, ambos)
3. **Why** — por que es importante para CineViewHos?

### 2. Draft the ticket

Based on answers, draft:
- **Title**: concise, descriptive (max 200 chars)
- **Type**: `epic` (multi-feature initiative) or `story` (single feature)
- **Description**: formato markdown con `## Contexto`, `## Funcionalidad principal`, `## Criterios de aceptacion` (3-5 bullets cada uno)
- **Priority**: `high`, `medium`, o `low`

Show the draft to the user. Wait for approval.

### 3. Determine next ticket ID

```
GET {XENODOCIA_URL}/api/tickets/
```

Find the max numeric ID, propose next (e.g. XE-015). Confirm with user.

### 4. Create the ticket

```
POST {XENODOCIA_URL}/api/tickets/
Headers: Authorization: Token {XENODOCIA_API_TOKEN}
Body: { title, type: "story"|"epic", status: "roadmap", priority, board, project, description_body }
```

Board ID and project ID from AGENTS.md or from GET `/api/boards/` if unknown.

### 5. Sync the roadmap wiki (MANDATORY)

After creating the ticket, rebuild the roadmap page with ALL roadmap tickets:

**5a. Fetch all roadmap tickets from the DB**
```
GET {XENODOCIA_URL}/api/tickets/?status=roadmap
```

**5b. Build markdown summary** listing each ticket with ID, title, type, priority, and key bullet points from the description.

**5c. Write via content API**
```
PUT {XENODOCIA_URL}/api/content/
Body: { "path": "planning/projects/cineviewhos/roadmap.md", "content": "<markdown>" }
```

**5d. Ensure WikiPage DB record** exists for `roadmap` slug (POST if missing).

### 6. New domain? → scaffold context (conditional)

If the ticket introduces a domain that doesn't exist yet in `wiki/contexts/`:
- Create `wiki/contexts/{domain}/CONTEXT.md` with minimal glossary (3-5 key terms)
- Update `wiki/contexts/CONTEXT-MAP.md` adding the new domain with its CONTEXT link
- Create WikiPage DB records for both

Existing domains are NOT modified here — their CONTEXT.md reflects implemented state, not plans.

### 7. New labels? → create them (conditional)

If the ticket needs labels that don't exist in the tracker:
```
POST {XENODOCIA_URL}/api/labels/
Body: { "name": "...", "color": "#...", "category": "domain"|"type" }
```

### 8. Verify

GET the ticket back by ID. Print confirmation:
```
Created: {TICKET_ID}
Status: roadmap
Title: {title}
Wiki synced: planning/projects/cineviewhos/roadmap.md
```

## Error handling

- 401/403 → "Auth failed. Check XENODOCIA_API_TOKEN."
- 400 → Print validation errors from response
- Connection refused → "Is XenodocIA running? Try `make up`."
