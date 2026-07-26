---
name: project-km56-kanban-workflow
description: KM-56 adds Kanban states qa + ready-to-deploy (renames review→qa) and skill-driven transitions; auto-done via CD. Prod deploy required before the new states work.
metadata: 
  node_type: memory
  type: project
  originSessionId: 057c2856-3459-4650-a8ef-b2b82111fafd
---

> **Note:** "devbookIA" replaces "devplaybook" as the internal toolkit name.

KM-56 (in progress on branch `feat/km56-kanban-qa-ready-to-deploy` across xenodocIA + devbookIA) extends the xenodocIA Kanban to 6 columns:

`backlog → todo → in-progress → qa → ready-to-deploy → done`

- Backend: renamed `STATUS_REVIEW`→`STATUS_QA`, added `STATUS_READY_TO_DEPLOY`; migration `0004_kanban_qa_ready_to_deploy` (reversible, migrates review→qa in tickets + history).
- Frontend: 6 columns, each a distinct non-grey color (backlog=rose, todo=blue, in-progress=amber, qa=purple, ready-to-deploy=indigo, done=green).
- Skills: `devk` moves a ticket to `in-progress` on open (from backlog or todo) and to `qa` at the QA step; `gpr` moves it to `ready-to-deploy` after a successful merge to main; `update-status` lists the 6 states.
- Transition ownership: **abdo** owns `backlog→todo` (manual) and used to own `ready-to-deploy→done`. The **AI/skills** own `→in-progress`, `→qa`, `→ready-to-deploy`.
- Auto-done: `ready-to-deploy → done` is now automated by the **prod CD pipeline** (`CD_mockingbird_prod.yml`, triggered by a `vX.Y.Z` tag). After deploy it POSTs to `POST /api/tickets/mark-deployed/` which bulk-moves all `ready-to-deploy` tickets to `done`. Best-effort (a failed call warns but doesn't fail the deploy). Needs GitHub Secrets `MOCKINGBIRD_URL` + `MOCKINGBIRD_API_TOKEN` (deploy-bot user) — abdo must create these.

**Deploy-ordering gotcha (reusable lesson):** a ticket that introduces a new status/enum value **cannot use that value in production until it is itself deployed** — prod's backend rejects the new value with HTTP 400 until then. So dogfooding a status transition on the live ticket is a post-deploy step, not something to force mid-implementation. Validate new enum values against the local (Docker) backend running the new code.

**Test gotcha found in xenodocIA:** the DRF config only enables `BearerTokenAuthentication` (no SessionAuthentication), so tests using `client.force_login()` get 403. Authenticate tests via a real `APIToken` + `HTTP_AUTHORIZATION: Token {uuid}` header instead (see the `token_client` fixture added to `apps/tickets/tests/test_api.py`). Several pre-existing tests were broken this way and were fixed in KM-56.

See [[project-thoughts-moved-to-s3]], [[reference-xenodocIA-api-access]].
