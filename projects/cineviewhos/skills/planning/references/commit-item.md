# Commit a ProjectItem to the Backlog

Turns a ProjectItem (PI-N) into a real Ticket with a Board ID.

Check the project's AGENTS.md for the tracker API and content API configuration.

## Step 1 — Locate the item
Fetch the phase file via the content API. Show the items table. Confirm which PI-N the user wants to commit.

## Step 2 — Determine the Ticket ID
Call the tracker API to get existing tickets. Collect every existing ID and find the highest number. **Never trust a cached `next_id` counter** — it can drift from reality.

Confirm with the user: "The next available ID is {TICKET_ID} in board {board_name}. Confirm?"

## Step 3 — Create the ticket via the API
1. POST to the tracker API with title, type, priority, board, project.
2. PUT content via the content API for `description.md`.

The new ticket starts in `status: backlog` by default.

3b. **Assignee** — ask the user if they want to assign now or later.
3c. **Auto-tag** — infer domain/repo labels and apply them (if the project's tracker supports labels).

## Step 4 — Update the phase file
Fetch, update the item's ticket column, PUT back.

## Step 5 — Confirm
Report the result.

## Step 6 — No push needed
Everything was persisted directly via the API calls.
