# Dev Skill: Research Phase

Research before planning or implementing. DO NOT plan or implement during this phase. You're gathering information, not making decisions.

## Environment-specific instructions

### Claude Code
- Spawn `Explore` and/or `general-purpose` sub-agents in parallel.
- Each agent should focus on a specific aspect (models, services, views, tests).
- Wait for ALL agents to finish before synthesizing.

### OpenCode
- Use Glob to find files by pattern.
- Use Grep to find usages of specific symbols (fields, classes, functions, `related_name` values).
- Use Read to understand implementation details in the most relevant files.
- For broad searches crossing multiple directories, use the `explore` agent with thoroughness "medium" or "very thorough".
- For multi-step analysis, use the `general` agent.
- DO NOT use sub-agents for simple searches that Glob + Grep can resolve in one call.

## Wiki research (do this FIRST — before touching the codebase)

Check the project's AGENTS.md for the wiki location and content API configuration. The wiki may be stored via a content API (S3-backed or similar), or as local files.

### Step 1 — Read the context map
If the project uses a wiki with domain contexts, always start there to understand domain boundaries. Read CONTEXT-MAP.md if it exists.

### Step 2 — Read the domain context
If the task touches a known domain, read its `CONTEXT.md`. This gives you the bounded context, canonical terminology, and architectural constraints. A 404/missing means it doesn't exist yet — it will be created during the grill-with-docs session.

### Step 3 — Search for relevant wiki docs
Browse the wiki tree for `specs/` and `adr/` subdirectories relevant to the feature area.

## Research checklist

- [ ] Find the definition of the model/type being changed
- [ ] Find all files that reference the target symbol (field, class, function)
- [ ] Identify which references need changes and which are safe
- [ ] Find the serializer for any model being changed
- [ ] Find the views or services that use the model
- [ ] Find tests related to the area
- [ ] Find migrations that reference the field/table
- [ ] Identify conventions and patterns used in neighboring code (naming, imports, structure)

## Output format

After researching, present findings in this structure:

```
### Current state
- `file:line` — what it does, how it works
- `file:line` — pattern or convention to follow

### Files that need changes
- `file:line` — what change is needed
- `file:line` — what change is needed

### Confirmed safe files (no changes)
- `file:line` — why it's safe

### Design options (maximum 2)
1. Option A — pros/cons in 1 line
2. Option B — pros/cons in 1 line

Recommendation: [option] because [reason].

### Open questions (only what code can't answer)
- [Question requiring human judgment or business knowledge]
```

DO NOT include questions you can answer by reading more code. If you have a doubt, read the code first.
