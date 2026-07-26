---
name: promote
description: Promote a merged ticket spec to a living wiki page. Reads wiki config from .opencode/AGENTS.md. Use when user wants to promote a ticket to the wiki or runs /promote.
---

# promote

Promote a completed ticket's spec to the project wiki.

## Configuration

Read `.opencode/AGENTS.md` → **Wiki** section for:
- Wiki backend type (content-api or local)
- Path conventions (contexts, specs, adr directories)
- Content API URL if applicable

## Steps

### 1. Read the spec and current wiki state

Fetch the ticket's spec via the tracker. Identify:
- Domain the spec belongs to
- Key terms defined
- Architectural decisions made
- Reusable patterns documented

Also fetch the current CONTEXT.md and CHANGELOG.md for the affected domain(s) via the content API.

### 2. Present a promotion plan (MANDATORY — never skip)

Show what will be created/updated. ALL four items are mandatory for every promotion:

- **Spec** — Promoted to `specs/` directory
- **ADR** — Any decisions that pass the 3-question test (hard to reverse, surprising, real trade-off). Write to `adr/` directory.
- **CONTEXT.md** — Update with new entities, API endpoints, ADR references, frontend components. NEVER leave CONTEXT.md stale after promoting a spec.
- **CHANGELOG.md** — ALWAYS update with date, ticket ID, and summary of changes. Create the file if it doesn't exist.

Wait for user approval before writing.

### 3. Execute (HARD RULES — never skip any)

Write/update ALL files via the wiki backend (content API or local filesystem):

1. Copy spec to `wiki/contexts/{domain}/specs/{name}.md`
2. Write each ADR to `wiki/contexts/{domain}/adr/`
3. **Update CONTEXT.md** — add new entities, endpoints, ADR links, and component references
4. **Update CHANGELOG.md** — append entry with date, ticket ID, summary
5. If new domain: update CONTEXT-MAP.md and create domain label via `POST /api/labels/`

### 4. Verify (MANDATORY)

After execution, verify via the content API tree endpoint:
```
GET /api/content/tree/?prefix=wiki/contexts/{domain}/
```

Confirm:
- CONTEXT.md contains the new entities, endpoints, and ADR references
- CHANGELOG.md has the new entry
- ADR files are present in adr/ directory
- Spec file is present in specs/ directory

List every file created or modified with its path. If any file is missing or stale, fix it before declaring done.
