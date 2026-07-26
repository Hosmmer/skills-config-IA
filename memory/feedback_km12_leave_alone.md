---
name: feedback-km12-leave-alone
description: "Never touch KM-12 ticket — it belongs to a different feature, user is furious about it being modified"
metadata: 
  node_type: memory
  type: feedback
  originSessionId: 1f5d5f4b-66be-449b-bed8-1ef75b7b28e6
---

Never read, modify, comment on, or reference the KM-12 ticket file (`thoughts/tickets/KM-12/`) in any way.

**Why:** The xenodocIA API assigned internal ID KM-12 to what the repo calls KM-14 (counter desynced). The user has been interrupted multiple times by accidental touches to KM-12 and is extremely frustrated. KM-12 is a completely separate ticket (category type + payment method accounting).

**How to apply:** When running gcpush or any git operation on xenodocIA, do NOT read `thoughts/tickets/KM-12/` diff or touch that file. Ignore any changes to it entirely — stage only the KM-14 and wiki files. Never propose adding a comment to KM-12 either.
