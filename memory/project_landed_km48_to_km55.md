---
name: project-landed-km48-to-km55
description: "Features merged to main across repos as of 2026-07-06: KM-48 halvable pizza, KM-49 POS bug fixes, KM-50 featured products, KM-51 deactivate categories, KM-52 xenodocIA prod deploy, KM-53/54 S3 cutover, KM-55 roadmap"
metadata: 
  node_type: memory
  type: project
  originSessionId: 057c2856-3459-4650-a8ef-b2b82111fafd
---

> **Note:** "devbookIA" replaces "devplaybook" as the internal toolkit name.

Landed on `main` across the platform as of 2026-07-06 (this sync):

**Product / POS (backend + frontend):**
- **KM-48 — Half-and-half pizza (pizza por mitades)**: full flow, backend + frontend + docs. Uses separate `is_halvable` flag, NOT `is_portionable` — see [[pizza-halves-reuses-portionable]]. Frontend adds `HalfSheet.tsx` + `HalfScreen`/OrderScreen wiring; backend adds migration `0020_halvable_pizza`, comanda printing support.
- **KM-49 — POS bug fixes**: modifier resolution, variant pagination, `waiter_name` in order serializer (frontend reads waiter_name from API response when creating orders).
- **KM-50 — Featured products (Destacados)**: `is_featured` + `display_order` on `PosMenuItem`; frontend "Destacados" view.
- **KM-51 — Deactivate/reactivate product categories**: `active` field on `ProductCategory` (migration `0021_productcategory_active`); frontend category active/inactive toggle UI (Activate/Deactivate modals, `useCategoryActiveToggle`).

**xenodocIA (tooling/infra):**
- **KM-52 — xenodocIA prod deploy**: prod CD pipeline, whitenoise for Django admin static, React 19 peer-dep fixes, node:20-alpine base image. Prod is `REMOVED_LEGACY_URL
- **KM-53 — thoughts/ → S3 cutover**: 287 files migrated; S3 is sole source of truth; `thoughts/` untracked from git. See [[project-thoughts-moved-to-s3]].
- **KM-54 — generic content-tree endpoint** + devbookIA migration off local thoughts/. `GET /api/content/tree/?prefix=...`. See [[project-ticket-creation-via-api-s3]].
- **KM-55 — Roadmap sidebar**: screen listing planning Projects.

**How to apply:** These are done — don't re-plan them. If touching POS pizza, featured products, or category activation, the models/endpoints already exist on main.
