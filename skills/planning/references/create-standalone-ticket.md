# Create Standalone Ticket(s)

For tickets that don't belong to any planning project — just work that needs to land in the backlog directly.

Check the project's AGENTS.md for the tracker API and content API configuration.

## Step 1 — Collect the tickets
Get title + brief description for each ticket from the user. Infer `type` and `priority` from the description; default `priority: medium` if nothing suggests otherwise.

No project or phase file is involved. Board and project are required fields on the ticket schema — fill them in from whatever boards/projects exist in the system.

## Step 2 — Determine the Ticket IDs
Call the tracker API to get existing tickets. Collect every existing ID and find the highest number. **Never trust a cached `next_id` alone** — cross-check it against the actual max ticket id.

Confirm with the user once for the batch.

## Step 3 — Create each ticket via the API
For each ticket:
1. POST to the tracker API.
2. PUT description via the content API.

3b. **Assignee** — ask the user, once for the batch.
3c. **Auto-tag** — infer domain/repo labels and apply them.

## Step 4 — No phase file to update
Standalone tickets have no phase file to update — skip that step.

## Step 5 — Confirm
Report each ticket.

## Step 6 — No push needed
Everything was persisted directly via the API calls.
