---
name: feedback_anular_not_cancelar
description: "In the POS, order voiding is ALWAYS \"Anular orden\" — never \"Cancelar\". They are different concepts to the user."
metadata: 
  node_type: memory
  type: feedback
  originSessionId: fa95ce20-a422-4154-b320-4d3621873421
---

En el POS CineViewHos, anular una orden (para llevar, domicilio, mesa) SIEMPRE se llama **"Anular orden"** en la UI — nunca "Cancelar" / "Cancelar domicilio" / "Sí, cancelar".

**Why:** para el usuario "anular" y "cancelar" son conceptos DISTINTOS. "Cancelar" suena a cerrar/descartar un formulario; "anular" es el acto de negocio de dejar la orden sin efecto (revierte inventario, libera mesa). Mezclarlos confunde.

**How to apply:** títulos y botones de ese flujo → "Anular orden" / "Sí, anular orden" / mensajes "Orden anulada" / "Error al anular". El botón "No, volver" (para cerrar el confirm) sí está bien. Aplica a takeaway, domicilios y mesas por consistencia. Relacionado con [[feedback_surgical_scope_no_broad_changes]] y el flujo de [[project_km68_km69_pos_order_sync]].
