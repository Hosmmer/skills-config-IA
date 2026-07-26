---
name: project-repo-paths
description: CineViewHos platform repo locations on this Windows machine and devbookIA path
metadata: 
  node_type: memory
  type: project
  originSessionId: 49b46818-2e91-44a7-97ae-f0145d38e5fe
---

> **Note:** "devbookIA" replaces "devplaybook" as the internal toolkit name.

CineViewHos platform repos are at `C:\platform\`:
- `C:\platform\REMOVED_LEGACY`
- `C:\platform\REMOVED_LEGACY`
- `C:\platform\xenodocIA`
- `REMOVED_LEGACY` does NOT exist on this machine

devbookIA is at `C:\Users\hosmm\.claude\skills\devbookIA`

**Why:** Skill uses `~/Projects/CineViewHos/platform` (Unix paths for abdobarrera's Mac), but this machine (hosmm, Windows) has repos at `C:\platform`.

**How to apply:** When running gpmain, gcpush, gpr or any skill that references repo paths, use `C:\platform\<repo>` instead of the Unix paths in the skill definition. Skip REMOVED_LEGACY since it doesn't exist here.
