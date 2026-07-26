---
name: pos-menu-display-invariant
description: POS must always display the menu items configured in Admin — including Destacados
metadata: 
  node_type: memory
  type: project
  originSessionId: 69c721e9-5c86-466a-9c5d-4b517ad6c5a7
---

The POS menu (public/customer-facing product listing, including the "★ Destacados" tab) must display the products/state configured in the Admin panel. Whatever is toggled in Admin (featured flag, active flag, menu price, order) is expected to show up in the POS menu — there is no acceptable divergence between what Admin configures and what POS renders.

**Why:** Stated directly by the user while diagnosing the KM bug where toggling ★ (destacado) in Admin's product table doesn't change the "Destacados" tab in the POS menu ([[project_pos_menu_display_invariant]] bug context, reported 2026-07-16). This is being treated as a correctness invariant, not just a one-off symptom.

**How to apply:** When diagnosing or fixing any bug involving Admin-configured product/menu attributes (featured, active, price, order, category) not reflecting in the POS menu, treat "POS shows what Admin configured" as the ground-truth expected behavior — the fix must close that gap, not just patch the specific field involved. Also relevant to future features that add new per-product Admin toggles: they must be wired into whatever query/cache the POS menu reads from.
