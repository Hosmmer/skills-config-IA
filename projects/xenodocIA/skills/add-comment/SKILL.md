# add-comment

Add a comment to a XenodocIA ticket.

## Usage

```
/add-comment <TICKET_ID> <comment text>
```

## What this skill does

1. Read `XenodocIA_URL` and `XenodocIA_API_TOKEN` from environment. `XenodocIA_URL` defaults to production, `https://mbird.CineViewHos.com` (XE-53) — override to `http://localhost:8002` only when working on XenodocIA's own code locally.
2. POST to `{XenodocIA_URL}/api/tickets/{TICKET_ID}/comments/` with the comment body.
3. The comment is written to `tickets/YYYY-MM/{TICKET_ID}/comments/NNN.json` in the configured storage backend (S3 in production, local disk otherwise — XE-53).
4. Print confirmation with the comment ID.

## Request

```
POST {XenodocIA_URL}/api/tickets/{TICKET_ID}/comments/
Authorization: Token {XenodocIA_API_TOKEN}
Content-Type: application/json

{
  "content": "<comment text>",
  "type": "note"
}
```

## Expected output

```
Comment added to XE-42 (comment #3)
```

## Use cases

- Documenting a decision mid-implementation: `/add-comment XE-42 "Decided to use select_for_update instead of Redis lock — simpler and sufficient for our scale"`
- Leaving a progress note: `/add-comment XE-42 "Auth flow done. Starting on token refresh."`
- Recording a blocker: `/add-comment XE-42 "Blocked on DB migration approval from Jorge"`

## Notes

- The comment `type` field is optional (defaults to null). Pass `"note"` for regular notes, `"decision"` for architecture decisions.
- Comments are append-only. There is no edit or delete endpoint.
