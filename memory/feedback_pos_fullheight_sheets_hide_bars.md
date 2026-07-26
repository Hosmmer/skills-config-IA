---
name: feedback-pos-fullheight-sheets-hide-bars
description: "POS: sheets móviles altos deben ocultar NavBar/TabBar con .pos-sheet--hide-bars; header sticky + lista interna con scroll; números reales de mesas, no +N"
metadata: 
  node_type: memory
  type: feedback
  originSessionId: ceb23d02-0909-4ff7-be81-6c3db5617b3e
---

Cuando un bottom-sheet del POS móvil tiene mucho contenido (ej. OccupiedTableSheet con muchas mesas unidas), el contenido crecía y **tapaba el header y los botones** porque el sheet entero scrolleaba y el NavBar (arriba) lo cortaba.

**Solución correcta (patrón del repo):**
- Añadir la clase `pos-sheet--hide-bars` al div `.pos-sheet` → oculta NavBar y TabBar mientras está abierto (ya existe en pos.css: `.pos-root:has(.pos-sheet--hide-bars.show) .pos-nav-bar/.pos-tab-bar { display:none }`). Mismo mecanismo que los sheets de pago/cobrar.
- Con las barras ocultas, subir `max-height` a `calc(100dvh - 12px)` (usar `dvh`, no `vh`, por la barra del navegador móvil).
- **Header sticky** (`position:sticky; top:0`) para que quede fijo.
- La sub-lista larga (ej. mesas unidas) con **su propio scroll** (`max-height` + `overflow-y:auto`), para que nunca empuje header ni botones.

**Why:** el jefe pidió explícitamente "que aparezca por la parte de abajo tomando lo del menú por encima para tener más espacio" y confirmó "quedó perfecto".

**Otros detalles que pidió en el mismo sheet:**
- Mostrar los **números reales** de las mesas anexadas (`Mesas 1, 2` con `groupedTables.map(t => t.code).join(', ')`), NUNCA un contador tipo "Mesa 1 +1".
- Nada de gris: "Desunir" → rojo (`#DC2626` sobre `#FEF2F2`), "Ver orden" → navy (`#1E3A5F` sobre `#EEF4FB`), disabled → navy atenuado (no `#94A3B8`), total en verde. Ver [[feedback-no-grey-ui]].

**How to apply:** para cualquier sheet POS que pueda desbordar, usar hide-bars + header sticky + lista interna con scroll acotado; verificar en móvil real.
