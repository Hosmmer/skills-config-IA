---
name: feedback_no_emoji_use_real_icons
description: Never use emojis in the UI; always use real icons (SVG / icon font)
metadata: 
  node_type: memory
  type: feedback
  originSessionId: 85b98e5f-50b9-4c73-9325-de7099f7c356
---

Nunca usar emojis en la UI de CineViewHos. Siempre usar iconos reales: SVG inline o la fuente de iconos que ya usa el proyecto (Bootstrap Icons `bi bi-*` en el admin; SVG inline en el POS). Esto incluye caracteres tipo ✏ ▸ ▾ 📝 — todos fuera.

**Why:** El renderizado de emojis depende del dispositivo/OS. En KM-91 el bug real de "las notas no se ven en algunos dispositivos" era que el botón de nota usaba el emoji 📝 (`font-size:22px`) y en varios POS Android/tablets ese glyph no existe → el botón salía vacío/invisible. Un SVG siempre renderiza igual en todos lados. Además al jefe le desagradan los emojis en la UI.

**How to apply:** Cuando agregues o toques un elemento de UI con un icono, usá SVG inline (como los `<svg>` de las flechas/tacho en `pos-operator`) o `bi bi-*` en el admin. Nunca un emoji literal en el JSX ni en comandos de impresión que dependan de fuente. Para flechas de accordion, usá un chevron SVG, no ▸/▾. Relacionado con [[feedback_no_grey_ui]] y [[feedback_surgical_scope_no_broad_changes]].
