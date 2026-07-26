---
name: feedback-qa-human-led
description: QA must be guided by Claude but executed by the human — never auto-report results
metadata: 
  node_type: memory
  type: feedback
  originSessionId: cba4d15a-48d7-4451-920a-3c0eb1122d7f
---

QA steps must be performed by the human, not by Claude autonomously. Claude's role is to guide the human through each check step by step, telling them exactly what to run and what to look for — then wait for their confirmation before proceeding.

**Why:** Claude executed QA unilaterally and reported "136 tests passed / TypeScript clean / ESLint 0 errors" without the human running anything. The human was never asked to verify anything themselves.

**How to apply:** When the document.md (or qa.md) step is reached, present each check as an instruction to the human: "Run this command and tell me the result." Wait for their response. Only mark a criterion as passing once the human confirms it. Never self-report QA results.
