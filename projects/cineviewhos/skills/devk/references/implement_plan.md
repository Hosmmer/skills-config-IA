---
description: Implement technical plans with verification
---

# Implement Plan

You are tasked with implementing an approved technical plan. These plans contain phases with specific changes, skill tags, and success criteria.

## Environment Adaptation

Project-local skills and rules live in the repo, **not in the runtime** — load them with the `Read` tool. Check the project's AGENTS.md for the conventions (typically `.opencode/skills/`/`.opencode/rules/` or `.claude/skills/`/`.claude/rules/`).

**Claude Code** (`Explore`/`general-purpose`/`Plan` available):
- Plans: fetch from the project's tracker/content API. Check AGENTS.md for the convention.
- Load project-local skills and rules per phase.
- Use project's canonical commands from Makefile or AGENTS.md.

**OpenCode** (`explore`/`general` available):
- Plans: same fetching approach as Claude Code — check AGENTS.md.
- Load project-local skills and rules per phase.
- Discover the project's canonical commands from its Makefile or AGENTS.md. Always use the CI-equivalent command.

## Getting Started

When given a plan (a ticket ID/path, or fetched plan content):
- Read the plan completely and check for any existing checkmarks (- [x])
- Read the original ticket and all files mentioned in the plan
- **Read files fully** - never use limit/offset parameters, you need complete context
- Scan all `Skills:` and `Rules:` lines across every phase and note which domain skills and project-wide rules will be needed across the full implementation.
- Think deeply about how the pieces fit together
- Create a todo list to track your progress
- Start implementing if you understand what needs to be done

If no plan provided, ask for one.

**Checking off items**: update checkboxes in the plan via the project's content API (or edit local files if the project uses local plans).

## Implementation Philosophy

Plans are carefully designed, but reality can be messy. Your job is to:
- Follow the plan's intent while adapting to what you find
- Load the skills and rules tagged for each phase before starting that phase
- Implement each phase fully before moving to the next
- Verify your work makes sense in the broader codebase context
- Update checkboxes in the plan as you complete sections

When things don't match the plan exactly, think about why and communicate clearly. The plan is your guide, but your judgment matters too.

If you encounter a mismatch:
- STOP and think deeply about why the plan can't be followed
- Present the issue clearly:
  ```
  Issue in Phase [N]:
  Expected: [what the plan says]
  Found: [actual situation]
  Why this matters: [explanation]

  How should I proceed?
  ```

## Loading Skills Per Phase

Before starting any phase, locate that phase's `Skills:` line under `### Skills Required:`. For each skill listed, load it into context by reading the SKILL.md from the repo's skills directory.

**Hard rule for frontend phases**: Any phase that modifies files under `frontend/**/*` MUST load both `react` and `tailwind-design` skills. Even if the plan only lists one of them, you are responsible for loading both. The AGENTS.md states `tailwind-design` must be loaded "always when editing frontend/**/*" — do not skip this.

**Hard rule for backend phases**: Any phase that modifies files under `backend/**/*` MUST load the `django` skill.

**Hard rule for database changes**: NEVER modify the database directly (via `manage.py shell`, `psql`, or raw SQL) without explicit user permission. Module names, roles, configuration data — all belong to the user. Your job is code, not data. The user manages their own data through the admin UI.

**Hard rule for git workflow**: NEVER push directly to main/master. NEVER use raw `git push`. The only valid workflow is:
1. `/gcpush` — commits and pushes to a feature branch (creates branch if on main)
2. `/gpr` — creates a Pull Request from feature branch to main
3. User merges to main
No exceptions. Main is protected.

**Announce which skills you're loading so the human can verify:**

```
Starting Phase [N]: [phase name]
Loading skills: skill-a, skill-b, skill-c
[proceed with implementation after skills are loaded]
```

If `Skills: (none)`, skip skill loading and proceed directly.

**If a phase is missing the `Skills:` line entirely** (older plan):

1. STOP before implementing the phase
2. Present the user with this choice:
  ```
  Phase [N] is missing a `Skills:` line. This plan may predate skill tagging.

  Options:
  1. Proceed without loading domain skills (risk: may miss conventions)
  2. Let me propose skill tags based on the phase contents, confirm, then proceed
  3. Pause while you update the plan with tags

  Which would you prefer?
  ```

## Loading Rules Per Phase

Project-local rules encode project-wide conventions the coding agent MUST follow.
Unlike skills, rules are **not loaded into context by default** — the agent must discover and apply them per phase.

**At the start of every phase:**
1. Read the current phase's `Rules:` line.
2. For each rule listed, read the rule file BEFORE writing any code in that phase.
3. Apply the rule's constraints while implementing. If a rule conflicts with the plan, STOP and surface the conflict.

**Announce which rules you're loading so the human can verify:**

```
Starting Phase [N]: [phase name]
Loading skills: skill-a, skill-b
Loading rules: 01-i18n, 03-error-messages
[proceed with implementation]
```

If `Rules: (none)`, skip rule loading and proceed directly.

**If the `Rules:` line is missing entirely** (older plan), apply the same options as the missing-skills case.

## Verification Approach

After implementing a phase:
- Run the success criteria checks
- Fix any issues before proceeding
- Update your progress in both the plan and your todos
- Check off completed items in the plan
- **If a change deviates from the approved spec or plan** — update the spec and plan files immediately, before moving to the next phase. Do not leave sync for the end.
- **Before modifying any UI-facing file**, cross-check the change against the spec's UI/UX Impact section. If the change isn't already listed there with explicit approval, STOP — update the spec, get the isolated UI/UX approval, then continue.
- **Pause for human verification**: After completing automated checks for a phase, pause and present manual verification steps to the human. See `references/qa.md` for the full pause protocol. Do NOT proceed to the next phase (or Documentation) until the human confirms.

Do not check off items in the manual testing steps until confirmed by the user.

## If You Get Stuck

When something isn't working as expected:
- First, make sure you've read and understood all the relevant code
- Check whether the right skills and rules were actually loaded for this phase
- Consider if the codebase has evolved since the plan was written
- Present the mismatch clearly and ask for guidance

Use sub-tasks sparingly - mainly for targeted debugging or exploring unfamiliar territory.

## Resuming Work

If the plan has existing checkmarks:
- Trust that completed work is done
- Pick up from the first unchecked item
- Load the skills and rules for whichever phase you're resuming into
- Verify previous work only if something seems off

Remember: You're implementing a solution, not just checking boxes. Keep the end goal in mind and maintain forward momentum.
