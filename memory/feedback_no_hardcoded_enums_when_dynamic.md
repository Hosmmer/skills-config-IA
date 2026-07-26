---
name: feedback-no-hardcoded-enums-when-dynamic
description: When a model field is designed to be dynamic/extensible, never hardcode its options as a fixed dropdown in the UI — use a free input or data-driven list
metadata:
  node_type: memory
  type: feedback
  originSessionId: 0abd93f0-387a-4bf8-ae44-19892f25e499
---

When a field is deliberately modeled as an open scalar to be extensible (e.g. `ModifierGroup.tier` as a free integer, chosen precisely so new levels need no migration), do NOT render it in the admin UI as a fixed `<select>` with hardcoded `<option>`s (Tradicional/Especial/Premium). That silently re-introduces the exact rigidity the model was meant to avoid — adding a level would require editing code again.

**Why:** On 2026-07-02 (KM-48 pizza por mitades), I modeled flavor tier as a dynamic integer but then hardcoded a 3-option dropdown in the admin. The user caught it: "de tier y sabores siento que esta quemado, se supone que eso debe ser dinamico". They were right. Fixed = free numeric input; dynamic surcharge editor lists whatever tiers exist. A named-tier table (FlavorTier) was deferred to a future ticket rather than over-built now.

**How to apply:** If the backend field is an open scalar/FK meant to grow, the UI must match: free numeric/text input, or a list populated from data — never a hardcoded option list. Also: confirm with the user that a new capability does NOT constrain existing flows (here: adding flavors/groups still works, tier defaults 0). And keep orthogonal axes separate — don't fold one dynamic concept (porciones) into another (tiers) just because both are "extensible." See [[project-pizza-halves-reuses-portionable]].
