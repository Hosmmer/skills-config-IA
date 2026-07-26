---
name: feedback-no-grey-ui
description: ZERO grey AND zero opacity/muting on UI text. text-muted BANNED, opacity<1 on text BANNED, all grey classes/hexes BANNED. User is furious about repeats.
metadata:
  node_type: memory
  type: feedback
  originSessionId: 75870430-c0ae-4b89-9275-dfc22de0fb50
---

NEVER use grey OR faded/muted text in UI code. Two banned things treated as the SAME sin: (1) grey colors, (2) `opacity` < 1 on text or any dimming that makes text look faded. Both "arruinan mi ux". This has come up MANY times and the user is furious about repeats ("ese puto text-muted no lo quiero mas", "esa mierda de opacidad", "el gris es un color que no me sirve"). Treat any grey OR opacity-on-text as a hard bug.

**Also banned — never dim text:**
- `opacity: 0.x` on text elements (spans, divs, labels) or any `style={{ opacity: ... }}` that fades a label
- `bg-opacity`, faded/washed-out colors used to make text "secondary"
- Any "subtle" look achieved by lowering contrast. Use a full-strength real color instead (a darker/lighter solid hex), NEVER opacity.

**Banned — never write these:**
- `text-muted`, `text-secondary` (the worst offenders — appear everywhere in this Metronic codebase; strip them out even in table headers, hints, placeholders, labels)
- `btn-light-secondary`, `btn-secondary`, `btn-light` (grey buttons)
- Inline grey hex: `#94A3B8`, `#6c757d`, `#6B7A92`, `#8A97A9`, `#A0AEC0`, any `#9xx`/`#Axx` grey, `color: 'grey'/'gray'`
- Any disabled-looking muted style on interactive elements

**Use instead:** a real color. For secondary/subtle text use a soft tint of a brand/context color (e.g. amber section → `#B45309` for its text), never grey. Buttons: `btn-primary`, `btn-light-primary`, `btn-light-info`, colored variants with borders.

**CSS-VARIABLE GREY IS STILL GREY (KM-59, 2026-07-06 — user furious again):** The grep for `text-muted`/hex does NOT catch grey hidden behind CSS custom properties. In `pos-operator/pos.css` these tokens ARE grey and are BANNED on any text/badge/subtitle the user reads: `--pos-t2: rgba(60,60,67,0.55)`, `--pos-t3`, `--pos-fill`/`--pos-fill2: rgba(120,120,128,…)`, `--pos-res-txt: #636366`, `--pos-res-bg`. I shipped a modifier group badge/subtitle using `.opt { background: var(--pos-fill); color: var(--pos-t2) }` and it rendered as the exact grey the user has banned repeatedly. Fix used: `var(--pos-act-bg)` + `var(--pos-act-txt)` (#1E3A5F blue) for badges, and `#3B5B7F` for the subtitle. When touching ANY pos.css class, resolve every `var(--pos-*)` to its value and reject it if it's grey/rgba-of-grey — do not trust that a token name is safe.

**Safe POS blue replacements (use these instead of grey in pos.css):** `--pos-navy: #1E3A5F`, `--pos-navy-mid: #2E5080` (this is the one to use for secondary text like modifier option prices, product price, progress labels, note labels — it's already used for the selected-option price), `#3B5B7F` (subtitles), `#7A93B3` (placeholders — NOT grey `#A0AEC0`/`#C7C7CC`). `--pos-act-bg`/`--pos-act-txt` for info badges; `req-done` green (`#1A7A35`) for a satisfied/complete state. In KM-59 I converted `.pos-mod-opt-price`, `.pos-mod-prod-price`, `.pos-mod-progress-label`, `.pos-note-label`, and the note `::placeholder` from `--pos-t2`/hex-grey to these blues.

**How to apply — MANDATORY:** After editing ANY .tsx/UI file OR any CSS, before saying it's done, `grep` the file for `text-muted|text-secondary|btn-secondary|btn-light\b|opacity|bg-opacity` AND grey hex codes AND grey CSS vars (`--pos-t2|--pos-t3|--pos-fill|--pos-res`), and replace/remove them ALL on user-facing text — even pre-existing ones in code I touched. Do NOT copy grey classes, grey CSS vars, or opacity from neighboring code. The CineViewHos frontend is full of `text-muted` and faded styles by default; that is NOT a reason to keep them. For a "softer" secondary label use a solid mid-tone brand color at full opacity, never grey and never opacity.

**Consistency — REUSE existing styles, don't invent colors:** When building a new UI element (accordion, badge, group header, button), find the analogous existing component and copy its exact color/size/weight. Example (KM-48): the cart group header must match `ModifiersAccordion`'s "Adiciones" heading — teal `#0F766E`, `fontSize: 15, fontWeight: 800`. The user demands visual consistency ("mira el color que usa X, utilizalo tambien"). Before styling anything, grep for a sibling component that already does the same thing and match it, rather than picking arbitrary hexes. See related brand rule [[project-pizza-halves-reuses-portionable]] uses `bi-*` icons + colored badges.

**One explicit exception granted (KM-102, 2026-07-16):** the "‹ Cambiar tamaño" back-arrow icon button in `ShopHalfSheet.tsx`/`ShopPortionSheet.tsx` (ecommerce shop, editing a halvable/portionable cart line) uses `background: '#E5E5E5'` with a black `#000` arrow icon — user explicitly requested grey here twice after I flagged the conflict with this rule. This is a narrow, confirmed exception for that one icon-only button, not a reopening of the general rule. Do not extend grey to any other button/text without the same explicit re-confirmation.
