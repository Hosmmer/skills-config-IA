---
name: feedback-grill-skill-selection
description: "When devk instructs to run grill-with-docs, always invoke that exact skill — never substitute grill-me"
metadata: 
  node_type: memory
  type: feedback
  originSessionId: cba4d15a-48d7-4451-920a-3c0eb1122d7f
---

> **Note:** "devbookIA" replaces "devplaybook" as the internal toolkit name.

When `devk` SKILL.md says "Load the `grill-with-docs` skill", invoke `grill-with-docs` — not `grill-me`.

**Why:** Both skills exist in `~/.claude/skills/devbookIA/`. `grill-me` is a simpler variant; `grill-with-docs` is the full version that updates CONTEXT.md and ADRs inline. They are different skills. Selecting `grill-me` was a mistake caught by the user.

**How to apply:** Read the full skill name from the devk instruction literally. Do not match on partial keywords ("grill"). `grill-with-docs` is the correct skill for the SDD Feature pipeline.
