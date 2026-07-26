---
name: project-ticket-numbering
description: "CineViewHos ticket paths now use monthly folders: thoughts/tickets/2026-06/KM-N/ — and numbering rules"
metadata: 
  node_type: memory
  type: project
  originSessionId: cba4d15a-48d7-4451-920a-3c0eb1122d7f
---

> **Note:** "devbookIA" replaces "devplaybook" as the internal toolkit name.

⚠️ UPDATED 2026-07-06: tickets are no longer local files. After KM-53/54 ([[project-thoughts-moved-to-s3]]) they live in S3, reached via the xenodocIA API. The month-folder path (`tickets/YYYY-MM/KM-N/`) survives only as a content-API path, not a disk path. Numbering is now `GET /api/tickets/` max+1, human-confirmed — see [[project-ticket-creation-via-api-s3]].

xenodocIA tickets are organized by month:
- **Main tickets** — `tickets/YYYY-MM/KM-N/` (content-API path; was local `thoughts/tickets/2026-06/KM-N/` before the S3 cutover)
- **KM-150+** — separate series (historically `thoughts/shared/plans/KM-150/`)

As of 2026-06-25:
- KM-33 = POS Mobile: Ajustes UX ordenes Para llevar y Domicilio (in progress)
- KM-32 = Bulk Product Import/Export (jefe's ticket, done)
- KM-30 = POS menu query optimization
- KM-29 = POS Mobile UX Overhaul
- next_id = 34 (check API to confirm)

**Why:** Tickets were reorganized from flat `thoughts/tickets/KM-N/` to monthly `thoughts/tickets/2026-06/KM-N/`. The devbookIA references were updated accordingly.

**How to apply:** When creating or referencing tickets, use `thoughts/tickets/2026-06/KM-N/` path. Before creating a new ticket, call `GET {MOCKINGBIRD_URL}/api/boards/` and use the `next_id` field. The spec/plan file references in description.md should use relative paths from the new location.
