---
name: feedback-skill-files-always-reread
description: Always re-read skill reference files fresh — never use cached content from earlier in the conversation
metadata: 
  node_type: memory
  type: feedback
  originSessionId: c250dd80-a994-4eab-ac6f-9f2ee2f2f609
---

> **Note:** "devbookIA" replaces "devplaybook" as the internal toolkit name.

When executing any skill, always re-read ALL referenced files (SKILL.md and any references/*.md) at the moment of use — never rely on content already loaded in context from earlier in the conversation.

**Why:** Skills are updated daily. Using a cached version of a skill file causes wrong behavior that the user has to catch and correct. This happened with `generate_spec.md` — the old version was in context and was used instead of the updated one after a `/gpmain` pull.

**How to apply:**
1. Every time a skill step says "read references/X.md", issue a fresh Read tool call for that file, even if it was read earlier in the same conversation. Treat skill files as always potentially stale in context.
2. Follow every step in the skill file literally and in order — do not skip, reorder, or assume steps are optional.
3. Skills can change between sessions. Always do a fresh `/gpmain` (or at minimum re-read the SKILL.md) before executing a skill that was run in a previous session.
4. Symlinks for skills (`C:\Users\hosmm\.claude\skills\<skill-name>`) may become stale when new skills are added to devbookIA. After any `/gpmain`, verify that all skill folders in devbookIA have a corresponding symlink. If a symlink is missing, create a directory junction: `cmd /c mklink /J "C:\Users\hosmm\.claude\skills\<skill>" "C:\Users\hosmm\.claude\skills\devbookIA\<skill>"`
