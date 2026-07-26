---
name: feedback-fetch-before-ticket-number
description: Always git fetch/pull xenodocIA before proposing a new ticket number — the jefe creates tickets remotely that the local filesystem doesn't show
metadata:
  node_type: memory
  type: feedback
  originSessionId: 0abd93f0-387a-4bf8-ae44-19892f25e499
---

Before proposing or creating any new ticket number, ALWAYS `git fetch origin` (and pull main) in `C:\platform\xenodocIA` first. The jefe creates tickets on remote branches that the local filesystem scan does not see.

**Why:** On 2026-07-02, a local scan showed KM-46 as highest, so KM-47 looked free. But the jefe had already pushed `feat/km-47-frp-print-tunnel-wiki` (FRP print tunnel, printbridge domain). Creating KM-47 locally would have collided with / corrupted his ticket. The user caught it by telling me to fetch first.

**How to apply:** Ticket numbering flow becomes: (1) `git fetch origin && git pull origin main` in xenodocIA, (2) scan `thoughts/tickets/backlog/ thoughts/tickets/????-??/` AND check remote branches (`git branch -r | grep km-`), (3) propose `highest + 1`, (4) wait for user confirmation. Still filesystem-only for creation — never call the API. See [[feedback-ticket-creation-just-do-it]], [[project-ticket-numbering]].
