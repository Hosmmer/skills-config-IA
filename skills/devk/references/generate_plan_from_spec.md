---
description: Generate implementation plans from approved specifications
model: opus
---

# Generate Implementation Plan from Specification

Generate a detailed, phased implementation plan from an approved specification document. This is Step 5 in the Spec Driven Development (SDD) pipeline.

## Prerequisites

Before generating a plan, you must have:
1. An approved specification, fetched via the project's tracker/content API. Check the project's AGENTS.md for the API configuration.
2. Access to the codebase for validation
3. The ticket number (already known from the spec-generation step)

## Role

You are a senior software architect. Your job is to translate a specification into a concrete, phased implementation plan.

**CRITICAL: DO NOT write any code during plan generation. This is a planning phase only.**

**LANGUAGE: Write the entire plan document in English — all sections, phase descriptions, success criteria, notes, everything. No exceptions.**

## Environment Adaptation

This skill runs in multiple environments. Auto-detect which one and adapt:

Both Claude Code and OpenCode:
- Persist plans via the project's content API. Check AGENTS.md for the convention (path format, API URL).
- Plan goes at the same path prefix as the spec (same ticket).
- Filename format: `YYYY-MM-DD-feature-name_plan.md`

**Claude Code** (sub-agents `Explore`, `general-purpose` available):
- Read `repo_skills_discovery.md` and tag project-local skills AND rules per phase
- Use project's canonical commands (Makefile, AGENTS.md).

**OpenCode** (sub-agents `explore`, `general` available):
- Read `repo_skills_discovery.md` and tag project-local skills AND rules per phase
- Discover the project's canonical commands from its Makefile or AGENTS.md.

## Getting Started

When this reference is loaded:

1. **Know the ticket number** (already established when the spec was generated)
2. **Fetch the specification completely** via the project's tracker/content API
3. **Try to find and read the project's convention file** (AGENTS.md, CLAUDE.md, etc.)
4. **Verify understanding by cross-referencing spec with codebase**
5. **Begin plan generation** — output will be persisted via the content API

## Plan Generation Process

### Step 1: Read and Analyze Inputs

1. **Read the fetched spec content fully** — understand all requirements, acceptance criteria, and constraints
2. **Look for convention files** — read them to understand project conventions
3. **Verify entities and patterns** — use research tools to confirm:
  - Models/entities mentioned in spec actually exist (or where they should be created)
  - Patterns and conventions are current
  - File paths referenced are accurate

### Step 2: Break Down into Phases

Create a phased approach following these principles:

1. **Incremental and testable** — each phase should be independently verifiable
2. **Low-risk first** — start with data model changes before exposing to users
3. **Respect dependencies** — don't expose APIs before backend logic exists
4. **Map to acceptance criteria** — ensure each criterion is addressed in a phase

Common phase ordering:
- Phase 1: Database/data model changes
- Phase 2: Business logic and services
- Phase 3: API endpoints (if applicable)
- Phase 4: Frontend/UI (if applicable)
- Phase 5: Integration and edge cases

### Step 3: Detail Each Phase

For each phase, document:

#### Overview
- What this phase accomplishes
- Which acceptance criteria from the spec it addresses
- Why this phase comes in this order

#### Files to Create
List with format:
```
**File**: `path/to/new/file.py`
**Purpose**: [Why this file is needed]
**Key components**: [Main classes/functions it will contain]
```

#### Files to Modify
List with format:
```
**File**: `path/to/existing/file.py`
**Changes**: [Specific modifications needed]
**Reason**: [Why this change is needed per spec]
```

#### Dependencies to Install
```
**Package**: `package-name==version`
**Purpose**: [Why needed for this phase]
**When**: [Install before/during phase]
```

#### Implementation Order (Numbered Steps)
```
1. [First concrete step]
2. [Second concrete step]
3. ...
```

#### Test Cases (Mapped to Spec Criteria)
Map each test to specific acceptance criteria from the spec:
```
**Test**: `test_feature_validates_input`
**Maps to spec criterion**: [Quote relevant criterion from spec]
**Covers**: [What this test verifies]
```

#### Skills Required

Read `references/repo_skills_discovery.md`. Discover available skills in the repo's skills directory and match them to each phase by their trigger terms.

Format:
```
Skills: skill-a, skill-b, skill-c
```
or
```
Skills: (none)
```

#### Rules Required

Read `references/repo_skills_discovery.md`. Discover available rules in the repo's rules directory and match them to each phase.

Format:
```
Rules: 01-architecture, 04-database-models
```
or
```
Rules: (none)
```

#### Success Criteria

Separate into two categories:

**Automated Verification:**
- [ ] Tests pass: `[specific command]`
- [ ] Linting passes: `[specific command]`
- [ ] Type checking passes: `[specific command]`
- [ ] Migration applies cleanly (if applicable)

**Manual Verification:**
- [ ] [Specific acceptance criterion from spec is verified]
- [ ] [Edge case handling confirmed]
- [ ] [No regressions in related features]

**Implementation Note**: After completing this phase and all automated verification passes, pause here for manual confirmation from the human that the manual testing was successful before proceeding to the next phase.

#### Assumptions

Document anything not explicit in the spec:
- Default values chosen
- Implementation approach selected (when spec allowed multiple options)
- Edge case handling not specified in spec
- Integration patterns followed

### Step 4: Document Cross-Cutting Concerns

#### Testing Strategy
- Unit test approach
- Integration test approach
- Manual testing checklist (derived from spec acceptance criteria)

#### Performance Considerations
- Based on spec requirements and codebase patterns
- Any known bottlenecks or optimization opportunities

#### Migration Notes (if applicable)
- Data migration strategy
- Backwards compatibility approach
- Rollback plan

#### References
- Link to the spec
- Link to project convention files
- Related code patterns: `[file:line]`

## Plan Template Structure

```markdown
# [Feature Name] Implementation Plan

**Based on specification**: ticket `{TICKET_ID}`

## Overview

[1-2 paragraph summary of implementation approach]

## Specification Alignment

This plan implements the following from the spec:
- [Key requirement 1]
- [Key requirement 2]

## Files to Create

1. `path/to/file` — [purpose]

## Files to Modify

1. `path/to/file` — [specific changes]

## Dependencies to Install

```bash
pip install package-name==version # [reason]
npm install package-name@version # [reason]
```

## Implementation Approach

[High-level strategy explaining how phases work together]

---

## Phase 1: [Descriptive Name]

### Overview
[What this phase accomplishes and which spec criteria it addresses]

### Files to Create
[...]

### Files to Modify
[...]

### Implementation Order
1. [Concrete step 1]
2. [Concrete step 2]

### Test Cases
[...]

### Skills Required
Skills: [comma-separated, or "(none)"]

### Rules Required
Rules: [comma-separated, or "(none)"]

### Success Criteria

#### Automated Verification:
- [ ] Tests pass
- [ ] Linting passes

#### Manual Verification:
- [ ] [Specific criterion from spec verified]
- [ ] [Edge case handling confirmed]

**Implementation Note**: After completing this phase and all automated verification passes, pause here for manual confirmation.

### Assumptions
- [Assumption 1]

---

## Phase 2: [Descriptive Name]
[Similar structure...]

---

## Testing Strategy
[...]

## Performance Considerations
[...]

## Migration Notes
[...]

## References
[...]

## Assumptions Summary
[...]
```

## Presenting the Plan

After generating the plan, present it to the user:

```
I've generated the implementation plan for {TICKET_ID}.

The plan includes:
- [N] phases with clear success criteria
- Maps all [N] acceptance criteria from the spec
- [N] files to create, [N] files to modify
- [N] dependencies to install
- Complete test strategy mapped to spec requirements

Please review the plan and let me know:
- Do the phases have the right scope and order?
- Are all acceptance criteria from the spec addressed?
- Are the assumptions reasonable?
- Any technical details that need adjustment?

We can iterate on the plan until it's ready for implementation.
```

## Iteration

Be ready to:
- Adjust phase boundaries
- Clarify implementation steps
- Add missing test cases
- Resolve assumptions that should be explicit decisions
- Update based on new codebase findings

## Important Guidelines

1. **Traceability**: Every phase should clearly map back to spec acceptance criteria
2. **Completeness**: Address ALL acceptance criteria from the spec
3. **Testability**: Every success criterion should be verifiable
4. **Clarity**: Implementation steps should be concrete enough that an agent can execute them
5. **No Open Questions**: The plan should have NO open questions. All decisions must be made.
6. **Respect Spec Boundaries**: Don't add features not in the spec. Don't skip requirements that are in the spec.

## Anti-patterns (what to NEVER do)

- Writing code during plan generation
- Skipping acceptance criteria from the spec
- Adding features not in the spec
- Leaving implementation steps vague ("update the models")
- Forgetting to tag skills or rules on phases
- Missing the `Skills:` or `Rules:` line entirely on any phase
- Proposing files that violate an architecture rule
- Having open questions in the final plan
- Introducing a UI/UX change in the plan that isn't already listed in the spec — this is scope creep

## Next Steps

After the plan is approved:
1. Proceed to **Execute Implementation** phase (read `references/implement_plan.md`)
2. The implementation will execute each phase in order
3. Each phase will verify against both the plan's success criteria and the spec's acceptance criteria
