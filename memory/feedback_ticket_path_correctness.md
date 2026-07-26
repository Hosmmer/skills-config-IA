---
name: feedback-ticket-path-correctness
description: "Tickets must be created inside thoughts/tickets/YYYY-MM/KM-N/, never directly under thoughts/tickets/KM-N/"
metadata: 
  node_type: memory
  type: feedback
  originSessionId: 9c468eb2-6b4b-4d41-b8bd-88b9bd7b73f5
---

⚠️ UPDATED 2026-07-06: after the S3 cutover ([[project-thoughts-moved-to-s3]]) there is no local ticket folder to `mkdir`/`Write`. The path is still `tickets/YYYY-MM/KM-N/` but it is now a **content-API path**, written via `PUT /api/content/` `{"path": "tickets/YYYY-MM/KM-N/description.md", ...}`, not a disk path. The rule below still holds for the *shape* of the path (always include YYYY-MM), just applied to the API path.

Always use the path `tickets/YYYY-MM/KM-N/`, never directly `tickets/KM-N/`.

**Why:** The skill explicitly says the path is `thoughts/YYYY-MM/{TICKET_ID}/`. Creating at the wrong level pollutes the directory structure and requires manual cleanup. The user was very angry about this — do NOT repeat it.

**How to apply:** Before writing any ticket file, verify the target path includes the year-month subdirectory. The correct absolute path on this machine is always `C:\platform\xenodocIA\thoughts\tickets\YYYY-MM\KM-N\`. The API's `description_body` field does NOT control the file path — always write the `description.md` file directly to the correct disk path. Never trust the API to place the file correctly.
