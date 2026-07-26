---
name: feedback-xenodocIA-ticket-creation
description: "Rules for creating xenodocIA tickets correctly — encoding, deduplication, and title correctness"
metadata: 
  node_type: memory
  type: feedback
  originSessionId: 1f5d5f4b-66be-449b-bed8-1ef75b7b28e6
---

## Rules for xenodocIA ticket creation

### 1. Never use em dash (—) in ticket titles
The xenodocIA API corrupts the em dash character (U+2014) when sent via curl or Python urllib. Use a colon `:` or hyphen `-` instead in ticket titles.

**Why:** The server's encoding pipeline mangles `—` to `?` silently, corrupting stored data.

**How to apply:** Before POSTing or PATCHing a ticket title, replace all `—` with `:` or `-`.

### 2. Always use Python urllib, never curl, for ticket API calls
curl on Windows has encoding issues with non-ASCII characters. Python urllib with explicit UTF-8 encoding works reliably.

**Why:** curl with special chars caused `utf-8 codec can't decode byte` errors from the server.

**How to apply:** Always use the pattern:
```python
import urllib.request, json
data = json.dumps({'title': title}).encode('utf-8')
req = urllib.request.Request(url, data=data, method='POST', headers={...})
```

### 3. Check board next_id before creating tickets — if mismatch, STOP and ask the user
Before creating a ticket that must have a specific ID (e.g. KM-21), GET /api/boards/ and check `next_id`. If `next_id` does not match the requested ID, **STOP and tell the user**. Do NOT create placeholder tickets, do NOT create the ticket at the wrong ID, do NOT proceed. The user decides how to fix the counter.

**Why:** Creating a ticket when next_id doesn't match overwrites an existing ticket's description.md (KM-20 incident on 2026-06-18: user asked for KM-21, next_id was 20, API created KM-20 and overwrote its description.md). Previously KM-14/KM-12 desync caused weeks of confusion.

**How to apply:** Before /create-ticket: (1) GET /api/boards/ and check `next_id`, (2) if `next_id != requested_id`, STOP and report the mismatch to the user, (3) NEVER create the ticket anyway hoping to fix it later — the damage (overwriting description.md) is immediate and destructive.

### 4. Ticket files ALWAYS go in thoughts/tickets/{ID}/ — never in thoughts/{ID}/

All ticket files (description.md, spec, plan) must be created at:
`C:\platform\xenodocIA\thoughts\tickets\{ID}\`

**Never** create them at `C:\platform\xenodocIA\thoughts\{ID}\` (missing the `tickets/` subfolder).

**Why:** The xenodocIA server reads ticket files from `thoughts/tickets/{ID}/`. Files placed directly in `thoughts/{ID}/` are invisible to the UI and cause confusion.

**How to apply:** Every Write or mkdir for a ticket must use the path `C:\platform\xenodocIA\thoughts\tickets\{ID}\`. Double-check the path before writing — no exceptions.

### 5. Always write description.md to thoughts/tickets/{ID}/ immediately after creating a ticket

Every new ticket must have `C:\platform\xenodocIA\thoughts\tickets\{ID}\description.md` written right after creation — even when running outside the xenodocIA repo. The `description` API field is read-only; the only way to set it is by writing this file directly.

Format:
```
# {ID} — {Full Title}

{One paragraph describing what the feature does. Unique to this ticket.}

Spec: [contexts/{domain}/specs/{slug}.md](../../wiki/contexts/{domain}/specs/{slug}.md)
```

Never leave description.md as just `# {title}` with no body — that is the broken server default.

### 5b. Never PATCH description via API

The `description` field in the xenodocIA API is **read-only** — it reads from `thoughts/tickets/{ID}/description.md` on the filesystem. PATCHing `description` via the API silently does nothing.

**Why:** `TicketSerializer` declares `description` as `read_only_fields` and uses `SerializerMethodField` backed by `services.read_description(obj.id)`.

**How to apply:** To set or update a ticket description, write directly to `C:\platform\xenodocIA\thoughts\tickets\{ID}\description.md`. Format: H1 title on line 1 (`# KM-N — Full Title`), then a paragraph explaining what the feature does, then a Spec link if one exists in the wiki.

### 6. Descriptions must be unique — each ticket describes its own feature only

Each `description.md` must describe only that ticket's feature. Never copy the title of another ticket or use a generic heading. Always cross-reference the correct spec from `thoughts/wiki/contexts/{domain}/specs/`.

**Why:** KM-4 had "Edit Payment Method" (wrong ticket's content), KM-6 and KM-8 both had "POS Desktop — Enviar a cocina" (duplicated title from a different ticket), KM-10 had KM-11's title.

**How to apply:** Before writing a description, verify: (1) the ticket ID matches the spec, (2) the title matches RELEASE-QUEUE.md, (3) no other ticket has the same description text.

### 7. Always verify titles against RELEASE-QUEUE.md and CHANGELOG.md
After creating tickets, cross-check titles in xenodocIA against `thoughts/wiki/products/pos/RELEASE-QUEUE.md`. Titles can drift when placeholders get wrong titles during counter advancement.

**Why:** KM-10 ended up with KM-11's title because a placeholder was created with the wrong title during counter advancement.

**How to apply:** After any ticket creation session, list all tickets and compare against RELEASE-QUEUE to catch mismatched titles early.
