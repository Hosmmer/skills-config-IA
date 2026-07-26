---
name: feedback-surgical-scope-no-broad-changes
description: When asked for a SPECIFIC change, touch ONLY that. Never widen scope (broad CSS selectors, refactors, unrelated fixes) without asking first.
metadata:
  node_type: memory
  type: feedback
  originSessionId: 7122b9fb-a9ae-4e5b-9a60-caea483ae41f
---

When the user asks for a **specific, scoped** change, make it **surgical** — touch ONLY the exact element/thing named. Do NOT widen the blast radius, and do NOT make any adjacent/extra change without asking the user first.

**Why:** The user is explicit and repeatedly firm about this. In KM-59 they asked to restyle only the "Requerido"/"Grupo de sabores" checkboxes; I used a broad CSS selector `.mg-panel .form-check-input` that also hit the "Disponible" `form-switch` toggles and turned them into radio-button-looking circles. The user was annoyed: *"si te dije solo especificamente algo porque moviste lo demas, no vuelvas hacer eso"* and *"no vuelvas tomar esa decision sin preguntarmelo"*. Broad selectors that sweep sibling elements are exactly the failure to avoid.

**How to apply:**
- Prefer the **narrowest possible selector / edit**. For CSS, scope to the exact class you were asked about; exclude siblings (e.g. `.form-check:not(.form-switch)`) rather than styling all `.form-check-input`. Better still, target a purpose-specific class.
- Before shipping a styling/refactor change, mentally list every element the selector/edit will hit. If it touches anything beyond what was asked, either narrow it or **ask first**.
- If you think an adjacent improvement is worth doing (another grey nearby, a related refactor), **propose it and wait** — do not just do it. The user decides scope, not me.
- After a scoped change, re-audit the diff (`git diff`) and confirm nothing outside the requested target moved. Report exactly what you touched.

This is the same discipline as [[feedback-never-touch-other-tickets]] but for code scope, and it pairs with [[feedback-no-grey-ui]] (whose broad grep/fix must still stay within the elements in scope).
