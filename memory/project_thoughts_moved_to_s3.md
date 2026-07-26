---
name: project-thoughts-moved-to-s3
description: "KM-53/54: xenodocIA thoughts/ (tickets, wiki, planning) moved to S3, accessed only via xenodocIA content API — no local thoughts/ checkout anymore"
metadata: 
  node_type: memory
  type: project
  originSessionId: 057c2856-3459-4650-a8ef-b2b82111fafd
---

> **Note:** "devbookIA" replaces "devplaybook" as the internal toolkit name.

As of 2026-07-05/06 (KM-53 cutover + KM-54), xenodocIA's `thoughts/` content (tickets, wiki, planning) is stored in **S3 and is the sole source of truth** (ADR-0004). It is NOT git-tracked anymore — `thoughts/tickets/`, `thoughts/wiki/`, `thoughts/planning/` are in `.gitignore`. Local files stay on disk as historical record up to the cutover commit but must not be treated as live state.

**All access goes through the xenodocIA content API — never Read/Glob/mkdir/mv on local `thoughts/` paths:**
- Read any file: `GET {MOCKINGBIRD_URL}/api/content/?path=<relative-path>` → `{path, content}`
- Write any file: `PUT {MOCKINGBIRD_URL}/api/content/` body `{"path": "...", "content": "..."}`
- List a tree (wiki/planning/tickets with no Postgres backing): `GET {MOCKINGBIRD_URL}/api/content/tree/?prefix=planning/projects` (generalizes the old `wiki/tree/`)
- Tickets have a Postgres backing too: list via `GET /api/tickets/`, backlog via `GET /api/tickets/?status=backlog`.
- `MOCKINGBIRD_URL` defaults to production `REMOVED_LEGACY_URL only override to `http://localhost:8002` when working on xenodocIA's own code. Auth header is `Authorization: Token {MOCKINGBIRD_API_TOKEN}` (prefix "Token ", NOT "Bearer").

devbookIA (devk/planning/park skills) was retargeted in KM-54 to use these endpoints instead of local file ops. Always re-read the skill references fresh — see [[feedback-skill-files-always-reread]].

**Why:** This obsoletes the old "write ticket folders directly to disk" workflow. There is no `thoughts/tickets/YYYY-MM/` local checkout to `mkdir`/`Write` into anymore.

**How to apply:** For any ticket/wiki/planning read or write, use the content API path (`tickets/YYYY-MM/{ID}/description.md`, etc.), not a local filesystem path. See [[project-ticket-creation-via-api-s3]] for the updated ticket-creation flow.
