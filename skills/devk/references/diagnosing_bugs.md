# Diagnosing Bugs

The canonical diagnosing-bugs pipeline lives in the global skill:
`~/.config/opencode/skills/diagnosing-bugs/SKILL.md`

When the devk router classifies a task as a bug fix, load the `diagnosing-bugs` skill. Do NOT duplicate the 7-phase pipeline here — the skill is the single source of truth.

After the diagnosing-bugs pipeline completes and QA passes, run `/gcpush` + `/gpr`.
