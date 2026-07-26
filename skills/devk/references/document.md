---
description: Document a completed ticket in the project's wiki after QA passes
---

# Document

Run this after QA passes. This is the wiki promotion step for a completed ticket.

---

## Before executing anything — present a documentation plan

Scan the spec and produce a documentation plan. Present it to the human
and **wait for explicit approval before writing a single file.**

The plan must cover:

### 1. Spec Promotion

Which spec files to promote from ticket to wiki, with source and target paths:

```
| From | To |
|------|----|
| tickets/{YYYY-MM}/{TICKET_ID}/spec.md | wiki/contexts/{domain}/specs/{name}.md |
```

Also note what the spec covers (models, endpoints, migrations, frontend pages, acceptance criteria).

### 2. ADRs to Record

For each ADR, provide:

```
**ADR-NNNN: {title}**
- **Contexto**: What situation triggered this decision (codebase state, ticket requirements)
- **Trade-off**: Options considered and their pros/cons
- **Decision**: What was chosen and why
```

Only record ADRs that pass the three-question test:
- Is it hard to reverse later?
- Is it surprising without context?
- Was there a real trade-off involved?

If no ADR is needed, state it explicitly.

### 3. CONTEXT.md Updates

Show the specific changes to each domain's CONTEXT.md:

```
**File**: wiki/contexts/{domain}/CONTEXT.md
- Add entities: {list of new models with their file locations}
- Add relationships: {FK/M2M relationships with constraints}
- Add API endpoints: {new routes under /api/ and /api/admin/}
- Add ADR references: {link to new ADRs in the Architecture Decisions table}
```

### 4. CHANGELOG.md Update (MANDATORY)

Every domain touched MUST have its CHANGELOG.md updated with:
- Date, ticket ID, summary of what changed
- Reference to new ADRs or specs

```
**File**: wiki/contexts/{domain}/CHANGELOG.md
- {date}: **{TICKET_ID}**: {summary}. ADR-NNNN accepted.
```

If CHANGELOG.md doesn't exist for the domain, create it.

### 5. CONTEXT-MAP.md Check

If the ticket introduces a NEW domain (not just modifying an existing one), update CONTEXT-MAP.md:
- Add row to the dominios table
- Create the domain label via `POST /api/labels/`

### 6. Output Format

Present it like this:

```
## Documentation Plan — {TICKET_ID}

### 1. Spec Promotion
| From | To |
|------|----|
| ... | ... |

### 2. ADRs to Record
**ADR-NNNN: {title}**
- Contexto: ...
- Trade-off: ...
- Decision: ...

### 3. CONTEXT.md Updates
**File**: ...
Cambios: ...

### 4. CHANGELOG Updates
**File**: wiki/contexts/{domain}/CHANGELOG.md
Cambios: ...

Approve this plan?
```

---

## Execution (HARD RULES)

Once approved, execute ALL actions via the content API. **NEVER skip any step:**

1. Copy spec to wiki specs path
2. Write each ADR to `wiki/contexts/{domain}/adr/`
3. Update CONTEXT.md with new entities, endpoints, and ADR references
4. **Update CHANGELOG.md** — append entry with date, ticket ID, and summary
5. If new domain: update CONTEXT-MAP.md and create domain label

**Verification**: After execution, run `GET /api/content/tree/?prefix=wiki/contexts/` and confirm:
- CONTEXT.md contains the new entities/endpoints
- CHANGELOG.md has the new entry
- ADRs are in the correct domain's adr/ directory

Then proceed to gcpush → gpr.
