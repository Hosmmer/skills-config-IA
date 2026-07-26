# Skill: /promote

Promote a merged XenodocIA ticket spec to a living wiki page.

## When to use

Run this after a ticket's PR is merged to main. The ticket must have a spec (check `has_spec` on the ticket — see Step 2).

## Trigger

`/promote` or `/promote XE-NNN`

## Environment

- `XenodocIA_URL` — base URL (default: production, `https://mbird.CineViewHos.com` — override to `http://localhost:8002` only when working on XenodocIA's own code locally)
- `XenodocIA_API_TOKEN` — Bearer token (read from `.env` if present)

## Steps

### 1. Identify the ticket

If the user passed a ticket ID (e.g., `XE-2`), use it directly.

If no ID was given, call `GET $XenodocIA_URL/api/tickets/` and list recent tickets so the user can pick one. Show: ID, title, status, `has_spec`, `wiki_page`.

### 2. Fetch ticket details

```
GET $XenodocIA_URL/api/tickets/{ticket_id}/
Authorization: Token {token}
```

Check:
- `wiki_page` is null — if not null, say "Already promoted to wiki: {wiki_page.title}" and stop.
- `has_spec` is true — if false, say "No spec file found. Add a spec at `thoughts/{ticket_id}/*_spec.md` first." and stop.

### 3. Show spec preview

Call `GET $XenodocIA_URL/api/tickets/{ticket_id}/thoughts/` and show the first 20 lines of `spec.content` so the user can confirm it's the right content.

### 4. Confirm domain and title

Present:
- **Suggested domain**: infer from ticket title/labels, or the first label that matches a known domain (`pos`, `accounting`, `inventory`, `products`, `ecommerce`, `cineviewhos`).
- **Suggested title**: ticket title, or a cleaned version without prefixes like `[XE-NNN]`.

Ask the user to confirm or adjust both. One confirmation prompt, not two.

Known domains:
| Key | Label |
|-----|-------|
| `pos` | POS |
| `accounting` | Accounting |
| `inventory` | Inventory |
| `products` | Products |
| `ecommerce` | Ecommerce |
| `cineviewhos` | CineViewHos |

### 5. Call promote endpoint

```
POST $XenodocIA_URL/api/tickets/{ticket_id}/promote/
Authorization: Token {token}
Content-Type: application/json

{
  "domain": "{domain}",
  "title": "{title}"
}
```

### 6. Report result

On success (201):
- "Promoted! Wiki page created: **{title}** in the **{domain}** domain."
- "The spec is now a living doc at: `thoughts/wiki/contexts/{domain}/specs/{slug}.md`"
- Offer to update `{domain}/CONTEXT.md` if this decision should be reflected in the glossary.

On error (400 already promoted): show the existing wiki page link.
On error (404 no spec): prompt to add a spec file first.
