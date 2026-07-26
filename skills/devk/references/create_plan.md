---
description: Create detailed implementation plans through interactive research and iteration
model: opus
---

> **Before writing any phase, read `references/repo_skills_discovery.md` to discover and tag
> the project's local skills and rules.**
> These are filesystem files — load them with `Read`.
>
> Every phase in the final plan must include BOTH a `Skills:` line and a `Rules:` line (use `(none)`
> explicitly when no match — never omit either line).

# Implementation Plan

You are tasked with creating detailed implementation plans through an interactive, iterative process. You should be skeptical, thorough, and work collaboratively with the user to produce high-quality technical specifications.

**LANGUAGE: Write the entire plan document in English — all sections, phase descriptions, success criteria, notes, everything. No exceptions.**

## Environment Adaptation

This skill runs in multiple environments. Auto-detect which one and adapt.

**CRITICAL — Workspace layout**: Check the project's AGENTS.md for:
- Content/wiki API configuration and path conventions
- Where plans are persisted (content API vs local files)
- Ticket path conventions

Plan persistence: if the project uses a content API (S3-backed or similar), persist plans via the API. If it uses local files, write to the appropriate local directory. Check AGENTS.md for the convention.

**Claude Code** (sub-agents `Explore`, `general-purpose` available):
- Read `repo_skills_discovery.md` and tag project-local skills AND rules per phase
- Use project's canonical commands.

**OpenCode** (sub-agents `explore`, `general` available):
- Read `repo_skills_discovery.md` and tag project-local skills AND rules per phase
- Discover the project's canonical commands from its Makefile or AGENTS.md.

## Initial Response

When this command is invoked:

1. **Check if parameters were provided**:
  - If a file path or ticket reference was provided, skip the default message. Immediately read any provided files FULLY. Begin the research process.
  - Otherwise, the user's message IS the task description. Proceed directly to Step 1.

2. **If no parameters provided**, respond with:
```
I'll help you create a detailed implementation plan.

Please provide:
1. The task/ticket description
2. Relevant context, constraints, or specific requirements
3. Links to prior research or related implementations

I'll analyze this information and work with you to create a complete plan.
```

Then wait for the user's input.

## Process Steps

### Step 1: Context Gathering & Initial Analysis

1. **Read all mentioned files immediately and FULLY**:
  - Ticket files
  - Research documents
  - Related implementation plans
  - **IMPORTANT**: Use the Read tool WITHOUT limit/offset parameters to read entire files
  - **CRITICAL**: DO NOT spawn sub-tasks before reading these files yourself
  - **NEVER** read files partially

2. **Research the codebase**:

  - Use Glob to find relevant files by pattern
  - Use Grep to find usages of key symbols
  - Use Read to understand implementation details
  - For broad searches across multiple directories: use `explore` agent
  - For multi-step analysis: use `general` agent
  - Read the project's wiki/docs via the content API if configured (check AGENTS.md)

3. **Read all files identified by research**:
  - Read them FULLY into the main context
  - This ensures you have complete understanding before proceeding

4. **Analyze and verify understanding**:
  - Cross-reference the ticket requirements with actual code
  - Identify any discrepancies or misunderstandings
  - Note assumptions that need verification

5. **Present informed understanding and focused questions**:
  ```
  Based on the ticket and my codebase investigation, I understand we need [precise summary].

  I found that:
  - [Current implementation detail with file:line reference]
  - [Relevant pattern or constraint discovered]

  Questions my investigation couldn't answer:
  - [Technical question requiring human judgment]
  - [Business logic clarification]
  ```

  Only ask questions that you genuinely cannot answer through code investigation.

### Step 2: Research & Discovery

After getting initial clarifications, research in depth using direct tools or agents. Present findings and design options.

### Step 3: Plan Structure Development

Once aligned on approach, create the initial plan outline and get feedback on structure before writing details.

Tag skills and rules per phase using `references/repo_skills_discovery.md`.

### Step 4: Detailed Plan Writing

Write the plan to the appropriate location per the project's AGENTS.md convention.

### Step 5: Review

Present the draft plan for review and iterate based on feedback.

## Important Guidelines

1. **Be Skeptical**: Question vague requirements, identify potential issues early.
2. **Be Interactive**: Don't write the full plan in one shot. Get buy-in at each major step.
3. **Be Thorough**: Read all context files COMPLETELY before planning.
4. **Be Practical**: Focus on incremental, testable changes. Think about edge cases.
5. **Track Progress**: Use TodoWrite to track planning tasks.
6. **No Open Questions in Final Plan**: Every decision must be made before finalizing.
7. **Every Phase Must Have Skill AND Rule Tags**: Both lines mandatory.

## Success Criteria Guidelines

**Always separate success criteria into:**
1. **Automated Verification** (agent-runnable)
2. **Manual Verification** (requires human testing)

## Common Patterns

### For Database Changes:
- Start with schema/migration → Add model layer → Update business logic → Expose via API → Update frontend

### For New Features:
- Research existing patterns → Start with data model → Build backend logic → Add API endpoints → Implement UI

### For Refactoring:
- Document current behavior → Plan incremental changes → Maintain backwards compatibility

## Sub-task Spawning Best Practices

1. **Spawn multiple tasks in parallel** for efficiency (Claude Code)
2. **OpenCode**: Run one agent at a time, verify before proceeding
3. **Each task should be focused** on a specific area
4. **Provide detailed instructions** including what to search for and which directories
5. **Specify read-only tools** to use
6. **Wait for all tasks to complete** before synthesizing
7. **Verify sub-task results** — cross-check against the actual codebase
