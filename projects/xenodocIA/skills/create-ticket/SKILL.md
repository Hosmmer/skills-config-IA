# create-ticket

Create a XenodocIA ticket via the API.

## Usage

```
/create-ticket <title> [--type task|story|bug|epic|subtask] [--priority medium|high|low|critical] [--board <id>] [--project <id>] [--description "..."]
```

## What this skill does

1. Read `XenodocIA_URL` and `XenodocIA_API_TOKEN` from environment (or `.env` file at the repo root). `XenodocIA_URL` defaults to production, `https://mbird.CineViewHos.com` (XE-53).
2. If board/project IDs are not provided, call `GET /api/boards/` to list available boards and pick the first one. Then pick the first project linked to that board.
3. **IGNORE `Board.next_id` from `/api/boards/` entirely** — that counter can drift from reality (has historically undercounted, and can also overcount if a ticket was ever deleted). The ticket ID to use is always determined by calling `GET /api/tickets/`, collecting every existing `id`, and adding 1 to the highest number found. **The user must confirm this ID before proceeding** — this confirmation step is the real safeguard, independent of whichever counter drifted.
4. POST to `{XenodocIA_URL}/api/tickets/` with the token in the `Authorization: Token {XenodocIA_API_TOKEN}` header. Always include `description_body` in the POST body. **Do NOT use the `id` returned by the API** — always use the human-confirmed ID for all `path` values and references.
5. Print the human-confirmed ticket ID (e.g. `XE-46`), NOT the API-assigned `id` if it differs.
6. **Always** persist the description via `PUT {XenodocIA_URL}/api/content/` with `{"path": "tickets/YYYY-MM/{TICKET_ID}/description.md", "content": "..."}` using the human-confirmed ID — regardless of whether `--description` was passed. YYYY-MM is the current year-month (e.g. `2026-07`). The `description` field in the API is read-only (backed by this content); the only way to set it is via this PUT.

## Description format

The `description.md` file must follow this format:

```markdown
# {TICKET_ID} — {Title}

{One paragraph explaining what the feature does, written in plain language. Must be unique to this ticket — never copy another ticket's description.}

Spec: [contexts/{domain}/specs/{slug}.md](../../wiki/contexts/{domain}/specs/{slug}.md)
```

- The H1 must include the ticket ID: `# XE-42 — My Feature`
- The body must describe this ticket's specific feature, not a generic placeholder
- The Spec link is optional — only include it if a spec file already exists
- **Never** leave the description as just `# {title}` with no body — that is the broken default

## Request body

```json
{
  "title": "<title>",
  "type": "<type>",
  "priority": "<priority>",
  "board": <board_id>,
  "project": <project_id>
}
```

## Expected response

HTTP 201 with a Ticket object. Print:

```
Created ticket: XE-42
URL: {XenodocIA_URL}/tickets/XE-42
```

## Error handling

- 401/403 → "Authentication failed. Check XenodocIA_API_TOKEN."
- 400 → Print the validation errors from the response body.
- Connection refused → "Cannot reach XenodocIA at {XenodocIA_URL}. Is the server running?"

## Environment variables

| Variable | Description |
|---|---|
| `XenodocIA_URL` | Base URL — defaults to production, `https://mbird.CineViewHos.com` (XE-53). Override to `http://localhost:8002` only when working on XenodocIA's own code locally. |
| `XenodocIA_API_TOKEN` | Personal API token from Settings → API Tokens |

## Implementation notes

- Use Python `urllib` — **never curl** (encoding issues with non-ASCII characters on Windows).
- The token is a UUID string, not a JWT.
- Do NOT hardcode any URL or token.
- **Never use em dash (—) in titles sent via the API** — it corrupts to `?`. Use `:` or `-` instead in the API title field. The `description.md` file can use em dash freely (written directly to disk).
- The `description` field in the API response is **read-only** — it is served from `tickets/YYYY-MM/{TICKET_ID}/description.md` in the configured storage backend (S3 in production, local disk otherwise — XE-53). Writing `description_body` in the POST body tells the server to initialize that content. Always `PUT` it explicitly afterward via the content API to ensure the correct content — do not rely on `description_body` alone.
- After creating the ticket, confirm: ticket ID, title, and that the description was persisted (`GET` it back to verify).
- **CRITICAL — ID source of truth**: Both `Board.next_id` (from `/api/boards/`) and the `id` in the ticket-creation response can be wrong relative to what the user actually wants (drifted counter, or a gap from a deleted ticket). The ticket ID is always the one the human explicitly confirmed in Step 3. If the API returns `XE-36` but the user confirmed `XE-46`, use `XE-46` everywhere — `path` values, description content, spec references, all outputs.
