# Dev Skill: Generate Specification

Generate a formal specification document from research findings and user clarifications. This is Step 3 in the Spec Driven Development (SDD) pipeline.

## Prerequisites

Before generating a spec, you must have completed:
1. **Research** — Understanding of current codebase state, patterns, and constraints
2. **Grill-me** — Clarification of requirements, business rules, and user expectations
3. **Architecture rules loaded** — Read the target repo's architecture rules so the spec respects the team's layer-separation decisions.

## Role

You are a senior product analyst. Your job is to translate technical research and user requirements into a clear, actionable specification document.

**CRITICAL: DO NOT write any code during spec generation. This is a documentation phase only.**

**LANGUAGE: Write the entire spec document in English — titles, sections, acceptance criteria, business rules, everything. No exceptions.**

## Specification Template

Generate a Markdown specification covering these sections:

### 1. Feature Summary
- 1-2 paragraph overview of what this feature does and why it exists
- Include the business value or problem being solved
- Reference any research findings that informed this feature

### 2. Data Model / Entities Involved

List all data models/entities that will be:
- Created (new models)
- Modified (existing models with changes)
- Referenced (used but not changed)

For each entity, specify:
- Entity name and file location (if exists)
- Key fields/properties
- Relationships to other entities
- Any database constraints
- **Which layer it belongs to** (per the architecture rules loaded in Step 0.5)

### 3. Business Rules and Constraints

Document all business logic, validation rules, and constraints:
- Input validation rules
- Business logic conditions
- Authorization/permission rules
- Data integrity constraints
- Any codebase conventions that must be followed (discovered during research)

### 4. Acceptance Criteria (Testable)

Write specific, testable acceptance criteria:
- Use "Given/When/Then" format where appropriate
- Each criterion should be verifiable through testing
- Cover both happy path and edge cases
- Map to specific user interactions or system behaviors

### 5. UI/UX Impact

List every screen, component, button, or user-facing flow this feature touches or changes. If nothing in the UI changes, state that explicitly: "No UI/UX impact — backend only."

**This section requires separate, explicit approval — never bundled into the general "does the spec look good?" review.**

### 6. Out of Scope

Explicitly list what this feature will NOT do.

### 7. Open Questions

List any questions that still need answers before implementation.

## Content API Path and Naming

Check the project's AGENTS.md for the content API configuration. Persist specs via the content API with the appropriate path convention.

## Generation Process

### Step 0: Get Ticket Number

Determine the ticket number:
1. Call the tracker API to list existing tickets and find the highest number.
2. Propose the next number to the user.
3. Wait for explicit confirmation (or override from the user).

### Step 0.5: Load Architecture Rules

The spec describes **what** the feature does, but architecture rules dictate **where each piece lives**. Read the architecture rules for the target repo (check AGENTS.md for the rules directory).

If a rule contradicts something the user said during grill-me, surface the conflict before drafting.

### Step 1: Synthesize Research and Grill Findings

Review everything you learned:
- Current codebase patterns and conventions (from research phase)
- User requirements and clarifications (from grill-me phase)
- Technical constraints and existing implementations

### Step 2: Draft and Persist the Specification

1. **Draft the spec** following the template above.

2. **Determine description directory** — XenodocIA stores `description.md` in different directories based on ticket status:
   - `status: backlog` → description at `tickets/backlog/{TICKET_ID}/description.md`
   - `status: in-progress` or later → description at `tickets/YYYY-MM/{TICKET_ID}/description.md`
   
   Check the ticket status via `GET /api/tickets/{TICKET_ID}/` and check where description lives via `GET /api/content/tree/?prefix=tickets/backlog/{TICKET_ID}` and `GET /api/content/tree/?prefix=tickets/{YYYY-MM}/{TICKET_ID}`.

3. **Persist spec files** via the content API:
   - **Canonical**: `tickets/YYYY-MM/{TICKET_ID}/spec.md`
   - **Required for has_spec**: `tickets/YYYY-MM/{TICKET_ID}/YYYY-MM-DD-feature-name_spec.md` — the XenodocIA ticket API scans for `*_spec.md` files to set `has_spec: true`. A bare `spec.md` will NOT set `has_spec: true`.
   - **CRITICAL — if description lives in `tickets/backlog/`**: ALSO write the `YYYY-MM-DD-feature-name_spec.md` to the **same directory as the description** (`tickets/backlog/{TICKET_ID}/YYYY-MM-DD-feature-name_spec.md`). XenodocIA scans for `*_spec.md` relative to the description's directory. If the description is in `backlog/` and the `*_spec.md` is only in `YYYY-MM/`, `has_spec` will remain `false`.

4. **Verify**: after writing, check that `GET /api/tickets/{TICKET_ID}/` returns `has_spec: true`. If not, the `*_spec.md` file was NOT written to the same directory as the description. Fix immediately — re-write to the description's directory.

### Step 3: Present to User

Present the spec and get approval. If Section 5 (UI/UX Impact) is non-empty, ask about it as its own isolated message.

### Step 4: Iterate Based on Feedback

Be ready to add missing criteria, clarify rules, adjust scope, and resolve questions.

## Important Guidelines

1. **Be Precise**: Use specific terminology from the codebase.
2. **Be Complete**: Every acceptance criterion should be testable.
3. **Be Realistic**: Base the spec on actual codebase patterns.
4. **Be Clear About Trade-offs**: Document which approach was chosen and why.
5. **Reference the Code**: When describing entities, reference actual files.
6. **Separate What from How**: The spec describes WHAT, not HOW.

## Anti-patterns (what to NEVER do)

- Writing code or implementation details in the spec
- Being vague about acceptance criteria
- Ignoring constraints discovered during research
- Creating specs without completing research and grill-me first
- Leaving critical decisions as open questions that could be resolved now
- Bundling a UI/UX change into the general spec review

## Next Steps

After the spec is approved:
1. Proceed to **Generate Plan** phase (read `references/generate_plan_from_spec.md`)
2. The plan will be persisted alongside the spec
3. Implementation will verify against the acceptance criteria in this spec
