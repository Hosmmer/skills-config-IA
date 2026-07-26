---
name: code-review
description: Two-axis review of the diff since a fixed point (commit, branch, tag, or merge-base). Standards axis: does the code follow this repo's coding standards? Spec axis: does it faithfully implement the originating ticket/spec? Use when user wants to review a branch, PR, or WIP changes.
---

# Code Review

Two-axis review of the diff between `HEAD` and a fixed point. Both axes run as **parallel sub-agents** so neither pollutes the other.

- **Standards** — does the code follow this repo's documented coding standards?
- **Spec** — does the code faithfully implement the originating ticket/spec?

## 1. Pin the fixed point

Whatever the user said: commit SHA, branch, tag, `main`, `HEAD~N`. If unspecified, ask.

```
git diff <fixed>...HEAD          (three-dot = merge-base)
git log <fixed>..HEAD --oneline
```

Confirm the ref resolves and diff is non-empty. Fail here, not inside sub-agents.

## 2. Find the spec

Look for the originating spec, in order:

1. Ticket references in commit messages (`[XE-123]`, `Closes #45`)
2. Fetch from XenodocIA: `GET {XENODOCIA_URL}/api/content/?path=thoughts/tickets/{TICKET_ID}/spec.md`
3. Path the user passed
4. If nothing found, ask. If no spec exists, skip the Spec axis.

## 3. Find the standards

Read the project's rules:
- `.opencode/rules/` — project rules
- `.opencode/AGENTS.md` — architecture rules, domain rules, feedback rules

On top of whatever the repo documents, apply the **smell baseline** below. Two rules:
- **The repo overrides.** A documented repo standard always wins.
- **Always a judgement call.** Each smell is a heuristic, never a hard violation.

### Smell baseline

| Smell | What it is | Fix |
|---|---|---|
| **Mysterious Name** | Function/variable/type name doesn't reveal what it does | Rename it; if no honest name fits, the design is murky |
| **Duplicated Code** | Same logic shape appears in >1 hunk or file | Extract the shared shape, call from both |
| **Feature Envy** | Method reaches into another object's data more than its own | Move the method onto the data it envies |
| **Data Clumps** | Same few fields/params keep travelling together | Bundle into one type, pass that |
| **Primitive Obsession** | String/primitive standing in for a domain concept | Give the concept its own small type |
| **Repeated Switches** | Same switch/if-cascade on same type recurs across the change | Replace with polymorphism or one shared map |
| **Shotgun Surgery** | One logical change forces scattered edits across many files | Gather what changes together into one module |
| **Divergent Change** | One file edited for several unrelated reasons | Split so each module changes for one reason |
| **Speculative Generality** | Abstractions/params/hooks added for needs the spec doesn't have | Delete it, inline back until a real need shows |
| **Message Chains** | Long `a.b().c().d()` navigation caller shouldn't depend on | Hide the walk behind one method on the first object |
| **Middle Man** | Class/function that mostly just delegates onward | Cut it, call the real target |
| **Refused Bequest** | Subclass/implementer ignores or overrides most of what it inherits | Drop inheritance, use composition |

## 4. Spawn both sub-agents in parallel

Send a single message with two `Task` tool calls using `general` subagent type.

### Standards sub-agent prompt

Include:
- Full diff command and commit list
- List of standards-source files found
- The smell baseline table from above (pasted in full)
- Brief: "Report per file/hunk: (a) every place the diff violates a documented standard — cite the rule; (b) any baseline smell — name it and quote the hunk. Distinguish violations from judgement calls. Documented repo standards override the baseline. Skip anything tooling already enforces. Under 400 words."

### Spec sub-agent prompt

Include:
- Diff command and commit list
- Fetched spec content (or note if missing)
- Brief: "Report: (a) requirements the spec asked for that are missing or partial; (b) behaviour in the diff that wasn't asked for (scope creep); (c) requirements implemented but looking wrong. Quote the spec line for each finding. Under 400 words."

If spec is missing, skip the Spec sub-agent and note it in the report.

## 5. Aggregate

Present the two reports under `## Standards` and `## Spec` headings. Do **not** merge or rerank.

End with: total findings per axis, worst issue in each.

## Why two axes

A change can pass one axis and fail the other:
- Code that follows every standard but implements the wrong thing → **Standards pass, Spec fail.**
- Code that does exactly what the ticket asked but breaks conventions → **Spec pass, Standards fail.**

Reporting them separately stops one axis from masking the other.
