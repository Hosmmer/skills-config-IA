# Writing Goals & Outcomes

Framework for thinking through a project's `Goal` and `Opportunity Statement` (see
`references/file-formats.md`). Based on *Outcomes Over Output* (Joshua Seiden) and
*Working Backwards* ch. 6 (Bryar & Carr). Use this during Step 3 (Grill) of
`create-project.md` — it's what keeps a project's Goal from being a vague wish and its
Desired outcome from being a restated feature list.

## The chain: Impact → Outcome → Output

| Level | Definition | Who controls it |
|-------|------------|------------------|
| **Impact** | The business result you're going after. | Nobody directly — it's the north star. |
| **Outcome** | A **change in human behavior** (customer, user, employee) that drives the Impact. | Nobody directly, but it's observable and measurable. |
| **Output** | The feature/module/fix that gets built, believed to cause the Outcome. | The team — this is what a project delivers. |

> "An Outcome is a change in human behavior that drives business results."

Test for a real Outcome: can you observe someone **doing** something (or doing it less)? If
the sentence describes a feeling or a system state instead of a behavior, it's not an
outcome yet — keep pushing.

## Applying this today

- **Goal** — the Impact, as a one-line headline.
- **Impact** (in the Opportunity Statement) — the *same* Impact, spelled out: why it matters
  right now, what it's costing, quantified if you can. Not a different concept from Goal,
  just the expanded version instead of the headline.
- **Problem context** — the behavior happening today that's causing the problem. This is the
  diagnosis that Impact is describing the cost of.
- **Desired outcome** — the Outcome: the behavior that needs to change.

## The three magic questions

1. **What behaviors of our users drive the results the business cares about?** → the Outcome.
2. **How do we get people to do more of that behavior?** → features, modules, tools → the Output.
3. **How do we know we're right?** → the metrics/tests used to track progress.

## A useful lens (not a rule): Input metrics vs Output metrics

From *Working Backwards*: **Output Metrics** are the result you ultimately want (revenue,
NPS, retention, CSAT) — you can't influence them directly. **Input Metrics** are the actions
you do control, hoping they move the Output Metric.

## Iterate the metric until it can't be gamed

Amazon's example, refined several times:

1. Number of detail pages → gamed by adding pages nobody views
2. Number of detail pages **viewed** → gamed by adding out-of-stock items
3. % of viewed pages where the product **is in stock** → gamed by items in stock but slow to ship
4. % of viewed pages in stock **and shipping within 2 days** → became "Fast Track In Stock"

## Example (generic)

```
Impact: Reduce costs
Outcome: Fewer people calling technical support
Output: Improved usability of the confusing product features
```

```
Goal: Reduce support costs.

Opportunity Statement:
  Problem context: Customers call technical support a lot because several
  product features are confusing to use.
  Impact: High support costs driven by high call volume.
  Desired outcome: Fewer customers calling support because the confusing
  features stopped being confusing.
```

## Anti-patterns to check for in the grill

- **A Goal that's actually an Output.** "Build a dashboard" is a feature, not a goal.
- **An objective with no identifiable behavior behind it.** Keep pushing until you have a specific behavior change.
- **A Desired outcome with no way to verify it.** A behavior-change sentence with nothing measurable attached can't be tracked.

## Grill checklist

1. What's the Goal? (one sentence, the Impact, a business result the team doesn't control)
2. What's the problematic behavior happening today? → Problem context
3. Why does this matter right now — what's it costing? → Impact
4. What behavior needs to change? → Desired outcome
5. Is there a way to know if that change is happening? If so, could someone game that metric without the real behavior changing?
