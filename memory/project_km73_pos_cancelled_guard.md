---
name: project_km73_pos_cancelled_guard
description: "KM-73 POS bug batch; the recurring \"cancelled/500 al agregar productos\" bug and its 3-layer fix"
metadata: 
  node_type: memory
  type: project
  originSessionId: f5258bd6-b25a-42e2-96f4-de40a624fec5
---

KM-73 (creado 2026-07-08, in-progress, asignado hosmmer, label POS) agrupa varios bugs de POS UI/UX + estabilidad en REMOVED_LEGACY/pos-operator.

**El bug recurrente estrella** ("No se pueden agregar productos a una orden en estado 'cancelled'", a veces 400 a veces 500) que "siempre vuelve": la causa #1 real es que **los fixes nunca se desplegaban** — quedaban sin commitear en el working tree local mientras prod corría un bundle viejo. Siempre verificar `git log -S "<símbolo del fix>"` antes de creer que un fix está en prod.

**Blindaje de 3 capas (defensa en profundidad):**
1. Frontend: `assertOrderOpen(orderId)` + `OrderNotOpenError` en pos-operator/core/_api.ts. Se llama antes del loop de `addOrderLine` en los **4** flujos: POSLayout, OrderScreen, DesktopLayout, TablesScreen.handleInvoiceOrder. (Antes solo TablesScreen.handleTableClick tenía guard vía [[project_one_open_order_per_table]].)
2. Backend: `PosOrderService.add_line()` valida `order.state != OPEN` DENTRO del servicio (no solo en la vista PosOrderLinesView.post). Devuelve tupla (None, error). Test: test_add_line_rejects_non_open_order.
3. Deploy real (commit→PR→merge→deploy) — la parte que faltaba siempre.

Guards puros testeados en pos-operator/store/POSContext.tsx: `isAdoptableOrderState` (adopta orden solo si state==='open'), `isBackendLineId` (nunca mandar id local `<uuid>-<ts>-saved` al DELETE → evitaba 500), `variantDisplayName` (quita el marcador "(base)" → evita "((base))" en checkout). Tests en __tests__/posReducer.test.tsx.

El backend guarda `product_name_snapshot=variant.template.name` (nombre limpio); el "((base))" era 100% frontend componiendo `${name} (${sv.name})` con sv.name==="(base)".

Related: [[project_km68_km69_pos_order_sync]], [[project_lineitem_softdelete_pending]], [[feedback_qa_human_led]]
