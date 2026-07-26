---
name: project-ticket-creation-via-api-s3
description: "Updated ticket-creation flow after KM-53/54: POST /api/tickets/ IS used now, but ID always comes from GET /api/tickets/ max+1 confirmed by the human; description PUT to content API"
metadata: 
  node_type: memory
  type: project
  originSessionId: 057c2856-3459-4650-a8ef-b2b82111fafd
---

Since the S3 cutover ([[project-thoughts-moved-to-s3]]), ticket creation goes through the API — there is no local `thoughts/tickets/` folder to `mkdir`/`Write` anymore. The `/create-ticket` skill (xenodocIA/.claude/skills/create-ticket) now encodes the exact safeguards the user demanded, so the old "NEVER touch the API" rule is superseded by "use the API, but never trust its counter or returned id":

1. Determine the ID by `GET /api/tickets/`, take max numeric `id` + 1. **IGNORE `Board.next_id`** from `/api/boards/` AND **ignore the `id` returned by the POST** — both can drift (undercount, or overcount from a deleted ticket).
2. **The human must confirm the ID before proceeding.** This confirmation is the real safeguard.
3. `POST /api/tickets/` with `Authorization: Token {TOKEN}` (prefix "Token ", not "Bearer"). Use the human-confirmed ID everywhere — `path` values, references, output — NOT the API-returned id.
4. Persist the description via `PUT /api/content/` `{"path": "tickets/YYYY-MM/{ID}/description.md", "content": "..."}` (current year-month). The API `description` field is read-only; this PUT is the only way to set it.

Still-valid rules carried over (see [[feedback-xenodocIA-ticket-creation]]): use Python `urllib` never curl; never em dash in the API title (use `:`/`-`); descriptions unique per ticket with H1 `# KM-N — Title`; verify titles against RELEASE-QUEUE.

**Why:** KM-53/54 made S3 the source of truth and there's no local folder to write. The user's hard-won safeguards (confirm ID with human, ignore drifted counters, don't overwrite) are now baked into the skill, so calling the API is safe *as long as those steps are followed*.

**How to apply:** Follow the create-ticket skill fresh (re-read it — it updates). Never skip the human ID confirmation. Never use the POST's returned id. See [[feedback-ticket-creation-just-do-it]] which is now partially outdated (filesystem-only no longer applies).
