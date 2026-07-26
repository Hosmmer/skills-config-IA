---
name: feedback-ticket-creation-just-do-it
description: "When user says create ticket KM-N, just create it at that number — no counter checks, no placeholders, no questions"
metadata: 
  node_type: memory
  type: feedback
  originSessionId: 7758bb14-1eb6-4ff0-88b4-c94779690a2c
---

## SUPERSEDED (2026-07-06) by the S3 cutover — see [[project-ticket-creation-via-api-s3]]

⚠️ The filesystem-only rule below is OUTDATED. After KM-53/54 ([[project-thoughts-moved-to-s3]]) there is NO local `thoughts/tickets/` folder — content lives in S3 and is reached only through the xenodocIA API. Ticket creation now uses `POST /api/tickets/` + `PUT /api/content/`, but with the SAME safeguards that motivated the old rule: ignore the API's `next_id` and returned `id`, derive the ID from `GET /api/tickets/` max+1, and **the human confirms the ID before proceeding**. Follow [[project-ticket-creation-via-api-s3]] instead of the steps below.

### (Historical) old filesystem-only approach — no longer valid

1. Scan filesystem for highest KM-N: `ls thoughts/tickets/backlog/ thoughts/tickets/????-??/`
2. Propose `next = highest + 1` to the user. Wait for confirmation.
3. `mkdir thoughts/tickets/YYYY-MM/KM-N/`
4. Write `thoughts/tickets/YYYY-MM/KM-N/description.md` directly to disk
5. Done. No API calls. Ever.

### Even when /devk or /create-ticket skill is invoked

The `/create-ticket` skill still mentions the API — **ignore that part**. The filesystem-only approach overrides the skill instructions. The skill file has been updated to reflect this but may not always be re-read fresh.

**Why:** The user has been burned 5+ times (KM-20, KM-35, KM-36, etc.) by the API creating tickets at the wrong ID and destroying existing description.md files. The API counter will never be fixed. The user is furious about this recurring mistake.

**How to apply:** Any time ticket creation is needed — whether from /devk, /create-ticket, or conversational request — go filesystem-only. If tempted to call the API, stop and don't.

Related: [[feedback-xenodocIA-ticket-creation]], [[feedback-never-touch-other-tickets]]
