---
name: project-km32-bulk-import
description: "KM-32: Bulk Product Import/Export via Excel — landed 2026-06-25 across backend, frontend, xenodocIA"
metadata: 
  node_type: memory
  type: project
  originSessionId: 04c37937-2257-43c6-a30a-81080735cf54
---

> **Note:** "devbookIA" replaces "devplaybook" as the internal toolkit name.

KM-32 — Bulk Product Import/Export. Landed on main across all repos on 2026-06-25. Jefe's ticket.

### What it does
Businesses can import/export their entire product catalog (templates + variants + initial stock) via a single Excel (.xlsx) file with two sheets: `templates` and `variants`. Supports upsert (create or update). Processing runs async via Celery.

### Changes by repo

**REMOVED_LEGACY:**
- New: `apps/products/importers/product_importer.py` — ProductImporter class (699 lines)
- New migration: `0019_add_variant_description.py` — adds `ProductProduct.description` field
- Modified: `apps/products/views.py` — new endpoints `POST /products/import/`, `GET /products/import-template/`
- Modified: `apps/products/serializers.py`, `apps/products/urls.py`
- Modified: `apps/importer/tasks.py`

**REMOVED_LEGACY:**
- New: `catalog-admin/components/ProductImportModal.tsx` — import UI modal
- Modified: `catalog-admin/components/TemplateDetailPanel.tsx` — variant description modal
- Modified: `catalog-admin/components/TemplatesPanel.tsx`
- Modified: `catalog-admin/core/_models.ts`, `catalog-admin/core/_requests.ts`

**xenodocIA:**
- New: `thoughts/tickets/2026-06/KM-32/` — spec + plan + description
- New: `wiki/contexts/products/specs/KM-32-bulk-product-import-export.md`
- New: `wiki/contexts/products/adr/0003-multi-sheet-excel-bulk-product-import.md`
- New: `wiki/contexts/products/adr/0004-upsert-attribute-combination-variant-matching.md`
- Updated: `wiki/contexts/products/CHANGELOG.md`

**devbookIA:**
- Modified: `devk/references/generate_plan_from_spec.md` — minor fixes
- Modified: `devk/references/implement_plan.md` — lint command discovery fix
- New: `gpr/SKILL.md` — CI verification + auto-merge step added to PR flow

**Why:** Closes the gap between existing customer/order import and the product domain. Enables catalog seeding for new businesses and mass updates for existing ones.

**How to apply:** The products domain now has import/export endpoints. New ADRs explain the multi-sheet Excel design and upsert-by-attribute-combination matching strategy. [[project-ticket-numbering]]
