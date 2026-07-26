---
name: feedback-skill-selection
description: Always read the full skills list before invoking any skill — never stop at the first match
metadata: 
  node_type: memory
  type: feedback
  originSessionId: c250dd80-a994-4eab-ac6f-9f2ee2f2f609
---

Before invoking any skill, scan the ENTIRE available-skills list top to bottom. Never stop at the first partial match.

**Why:** Invoked `grill-me` without noticing that a more specific variant (`grill-with-docs`) could exist. The user had to correct this after the fact.

**How to apply:** When a skill is needed, read every skill name in the system-reminder list, pick the most specific match, then invoke. If two candidates exist, prefer the more specific one.
