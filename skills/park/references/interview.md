# Interview Phase

Ask exactly 3 questions, one at a time. Wait for each answer before the next.

## Question 1 — Who
"Who needs this? (cashier, cook, admin, developer...)"

## Question 2 — What
"What exactly do they want to be able to do?"
Challenge vague answers. If the initial title is already precise, confirm instead of re-asking.

## Question 3 — Why
"Why does it matter? What problem does it solve today?"

---

## Draft the user story

From the 3 answers, generate:

```markdown
# {Refined title}

As a {who}, I want {what}, so that {why}.

## Acceptance criteria

- [ ] {derived from the "what" — the happy path}
- [ ] {derived from the "why" — the problem it solves}
- [ ] {edge case or error handling if obvious from context}
```

Rules:
- Max 3 ACs — only what's obvious from the interview, never invented
- Title specific enough that a future dev understands without context

Show draft and ask: "Do you approve this ticket, or should we adjust something?"
Iterate until approved, then proceed to routing.
