---
name: to-spec
description: "Turn the current conversation into a spec and publish it to the project's ticket tracker as a ticket thought. No interview — just synthesis of what you've already discussed."
---

This skill takes the current conversation context and codebase understanding and produces a spec. Do NOT interview the user — just synthesize what you already know.

## Process

1. Explore the codebase to understand current state. Read the project's domain contexts (CONTEXT.md) if they exist — use the domain glossary throughout the spec, respect existing ADRs.

2. Sketch the seams at which you'll test the feature. Prefer existing seams. Use the highest seam possible. Fewer seams = better.

3. Check with the user that these seams match their expectations.

4. Write the spec using the template below.

5. Publish to the project's ticket tracker via the content API. If no ticket exists yet, create one first via `/create-ticket`. Check the project's AGENTS.md for the tracker URL and content API conventions.

## Spec template

```markdown
# {TICKET_ID} - {Feature name}

## Problem Statement
The problem from the user's perspective.

## Solution
The solution from the user's perspective.

## User Stories
A LONG numbered list:
1. As an <actor>, I want <feature>, so that <benefit>
2. ...

## Implementation Decisions
- Modules that will be built/modified
- Interfaces of those modules
- Technical clarifications
- Architectural decisions
- Schema changes
- API contracts

No specific file paths or code snippets unless a prototype encoded a decision precisely.

## Testing Decisions
- What makes a good test (test external behavior, not implementation details)
- Which modules will be tested
- Prior art for the tests in the codebase

## Out of Scope
What's NOT included.

## Further Notes
Any additional context.
```
