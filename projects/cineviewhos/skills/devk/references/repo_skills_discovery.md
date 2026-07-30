# Repo Skills Discovery

When generating an implementation plan, discover the target repo's own skills and rules — **do not use `skill_mapping.md`** (the old global generic mapping). Files live in the repo's rules/skills directory (check AGENTS.md for the convention — typically `.opencode/skills/` and `.opencode/rules/`, or `.claude/skills/` and `.claude/rules/`), and are loaded with the `Read` tool.

## Step 1 — Determine which repo's rules/skills directory to use

Each repo has its own skills directory. Use the one that matches the repo you are implementing in — **do not search from a parent directory**.

Your cwd should already be inside the correct repo. Confirm with:

```bash
ls .opencode/
```

or check the project's AGENTS.md for the rules directory convention.

If no rules/skills directory exists, skip to [Fallback](#fallback).

## Step 2 — Discover skills

```bash
find .opencode/skills -name "SKILL.md" 2>/dev/null
```

(or equivalent for `.claude/skills/`). For each found `SKILL.md`, read its frontmatter (`name`, `description`, `triggers` fields). **Read the frontmatter only** — do not load the full skill yet. The `triggers` field tells you when the skill applies.

## Step 3 — Discover rules

```bash
ls .opencode/rules/ 2>/dev/null
```

Read each rule file's filename and first heading to understand when it applies.

## Step 4 — Match skills to plan phases

For each plan phase:
1. Read the phase description (what it does: models, views, API endpoints, React components, tests, etc.)
2. Check each skill's `triggers` field — does a trigger term match what this phase touches?
3. Check each rule filename/heading — does it apply to what this phase touches?
4. Tag matching skills using their **directory name** under the skills directory

**Output format** — include both a `Skills:` line and a `Rules:` line:
```
Skills: django ← backend-only phase
Rules: 01-i18n, 03-error-messages
```

Skills use directory names from the skills directory. Rules use filenames from the rules directory (without `.md`). Both lines are mandatory — if a phase touches no local skill, emit `Skills: (none)`; if it touches no local rule, emit `Rules: (none)`. Never omit a line and never invent names.

## Fallback — No local skills or rules found {#fallback}

If the skills directory does not exist or contains no `SKILL.md` files:
- **Do NOT fall back to `skill_mapping.md`** — global generic skills are not project-specific
- Tag all phases with `Skills: (none)`
- Note in the plan preamble: "No project-local skills found — phases tagged as (none)"

If the rules directory does not exist or is empty:
- Tag all phases with `Rules: (none)`
- Note in the plan preamble: "No project-local rules found — phases tagged as (none)"

## During Implementation (how `implement_plan.md` loads skills and rules)

When `implement_plan.md` loads skills per phase, it reads from the repo's skills directory:

Where `{skill-name}` is the directory name from the `Skills:` tag.

When it loads rules per phase, it reads from the repo's rules directory:

Where `{rule-name}` is the filename from the `Rules:` tag without the `.md` extension.

Both are read on demand with the `Read` tool — files live in the repo, not in the runtime.
