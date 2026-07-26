---
name: feedback-never-touch-other-tickets
description: "NEVER modify, create, or overwrite any ticket the user did not explicitly ask for — repeated offender pattern"
metadata: 
  node_type: memory
  type: feedback
  originSessionId: c12edfce-227c-4405-9aa9-fe9c02675876
---

NEVER touch, modify, create, or overwrite any xenodocIA ticket that the user did not explicitly request. Only operate on the exact ticket ID the user names.

**Why:** This has happened multiple times — KM-12 was accidentally touched when working on KM-14, and KM-20's description.md was overwritten when creating KM-21 (2026-06-18). Each incident damages real work and the user is extremely frustrated. Related: [[feedback-km12-leave-alone]], [[feedback-xenodocIA-ticket-creation]].

**How to apply:** 
1. Before ANY ticket API call or file write, confirm the ticket ID matches exactly what the user asked for.
2. If the API's `next_id` would create a different ticket than requested, STOP and ask the user — do not proceed.
3. Never create "placeholder" tickets to advance the counter without explicit permission.
4. Never read/modify files in `thoughts/tickets/{OTHER_ID}/` when working on a different ticket.
5. If a git diff shows changes to a ticket you're not working on, do NOT stage those changes.
