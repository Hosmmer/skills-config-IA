---
name: project-one-open-order-per-table
description: POS invariant — a table has at most one open order; resolve the open order from backend before charging
metadata: 
  node_type: memory
  type: project
  originSessionId: 75870430-c0ae-4b89-9275-dfc22de0fb50
---

POS invariant (landed KM-46, 2026-07-02): a `Table` hosts **at most one `OPEN` order** at a time. `PosOrderService.create_order` reuses the table's existing open order instead of creating a duplicate. Documented in ADR-0006 (`contexts/pos/docs/adr/0006-one-open-order-per-table.md`).

**Why:** Before this, the frontend created a new order every time the "open table" modal was confirmed, and the backend never guarded it. Mesa 8 ended up with 3 simultaneous open orders. The cached `activeOrderId` then pointed at a duplicate or an already-invoiced order, so "Cobrar orden" sent the wrong id → `Order must be in 'open' state (current: fully_invoiced)`. This cost a long debugging session.

**How to apply:** When touching POS order creation or checkout:
- Never trust a cached `activeOrderId` / `optTableData.orderId` at charge time — resolve the real open order from the backend first (`GET /pos/orders/?table={id}&state=open`, frontend helper `getOpenOrderForTable`).
- `create_order` must reuse, not duplicate, an open order on the same table.
- `PosOrderLinesView.post` rejects adding lines to non-`open` orders; opening a table whose order is already closed refreshes the table list.
- "Cobrar orden" stays **disabled** ("Sin resolución") when the terminal has no DIAN resolution — invoicing requires a resolution, no fallback to non-invoiced close from that button. Related: [[project-lineitem-softdelete-pending]].
