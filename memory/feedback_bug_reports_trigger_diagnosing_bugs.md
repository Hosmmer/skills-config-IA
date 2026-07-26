---
name: feedback_bug_reports_trigger_diagnosing_bugs
description: "ANY bug report, however small or wherever raised in conversation, must trigger the full devk diagnosing_bugs.md pipeline before touching code"
metadata: 
  node_type: memory
  type: feedback
  originSessionId: 7b742b49-2246-44b7-9a22-db2d74ddc179
---

> **Note:** "devbookIA" replaces "devplaybook" as the internal toolkit name.

ANY bug report — no matter how small, and regardless of where in the conversation it's raised — triggers the full devk SDD pipeline via `references/diagnosing_bugs.md` (path: `C:\Users\hosmm\.claude\skills\devbookIA\devk\references\diagnosing_bugs.md`) before touching any code. Never apply an inline "quick fix" to a reported bug.

**Why:** El jefe (user) explicitly instructed this — quick inline fixes skip the diagnosis rigor the devk pipeline is designed to enforce, and he wants that rigor applied every time, not just for bugs surfaced through the formal /devk entry point.

**How to apply:** The moment any message (from the user, in a ticket, mid-implementation, anywhere) describes something behaving incorrectly, stop and load `references/diagnosing_bugs.md` from the devk skill before writing or editing any code. This applies even if the fix looks trivial or obvious. See [[feedback_skill_files_always_reread]] — re-read the file fresh each time rather than relying on cached knowledge of its contents.
