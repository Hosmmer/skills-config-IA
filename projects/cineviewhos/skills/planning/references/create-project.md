# Create a Project

Full pipeline for a new planning initiative.

Check the project's AGENTS.md for the tracker API and content API configuration.

## Step 1 — Describe
Ask the user: "Describe the initiative — what do you want to build and why."
Listen for: name, motivation, rough scope, affected modules.

## Step 2 — Research
Read via the API:
- CONTEXT-MAP.md (if it exists) — which domains are involved
- Any existing CONTEXT.md for affected domains
- Existing projects to avoid scope overlap

Summarize findings to the user before grilling.

## Step 3 — Grill
Invoke the `grill-with-docs` skill.
Goal: lock down name, slug, products, themes, the opportunity statement, phases (max 4), scope per phase, and initial ProjectItems.
Challenge against existing glossary terms.
See `references/file-formats.md` for what each field means.

For the Goal and Opportunity Statement, use the grill checklist in `references/goals-and-outcomes.md`.

## Step 4 — Draft
Propose the full file set:
- README.md (name, goal, opportunity statement, resources checklist, slug, status, products, themes, phases table)
- phase-1.md (scope + items table)
- phase-2.md … phase-N.md

Show drafts inline in the conversation. Do NOT write anything yet.

## Step 5 — Review
Ask: "Do you approve this project as-is, or should we adjust something?"
Iterate until approved.

## Step 6 — Write
Only after explicit approval, PUT via the content API for each file.
No folder needs to be created first — PUT on a path creates any missing parents implicitly.

## Step 7 — Commit (optional)
Ask: "Want to commit any Phase 1 items to the backlog now?"
If yes → follow `references/commit-item.md`
