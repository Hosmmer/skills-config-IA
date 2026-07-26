# Routing Phase

After the user story is approved, determine the destination.

Check the project's AGENTS.md for the content API and tracker API configuration.
Everything goes through the project's APIs.

## Step 1 — Scan projects

List existing projects via the content API. Classify each:
- **Active** — has at least one phase with `status: active`
- **Roadmap** — all phases are `planned`

If no projects exist, skip to Route C (standalone).

## Step 2 — Suggest a match

Show a menu of projects (active and roadmap) plus standalone/future options.

## Step 3 — Execute route

### A — Active project
1. Find the active phase file for that project
2. Determine next item ID
3. Determine next ticket ID (check the tracker API, not a cached counter)
4. Create ticket via the tracker API, write description via the content API
5. Update the phase file, adding the item
6. Report the result

### B — Roadmap project
1. Ask which phase this goes to
2. Determine next item ID
3. Update the phase file — no ticket created yet
4. Report: item added to roadmap, ticket will be created when phase activates

### C — Backlog standalone
No planning project is involved. Create a standalone ticket:
1. Determine next ticket ID, confirm with user
2. Create ticket via the tracker API, write description
3. Report: ticket created in backlog standalone.

### D — Future, no project
Ask: "Do you want to create a project for this now, or send it to the backlog standalone?"

- **New project** → invoke `/planning` passing the captured title + user story as context
- **Backlog standalone** → follow Route C

## After creating a ticket

When a route above creates a ticket:
1. **Assignee** — ask the user if they want to assign now or later.
2. **Auto-tag** — infer domain/repo labels and apply them (if the project's tracker supports labels).

## No push needed

Every write is persisted directly by the API call — there are no local files to commit or push.
