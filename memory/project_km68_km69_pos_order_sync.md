---
name: project_km68_km69_pos_order_sync
description: "KM-68 (abdo, merged→main) fixed POS order-state corruption; KM-69 (hosmmer) is the residual frontend stale-list sync layer on top"
metadata: 
  node_type: memory
  type: project
  originSessionId: fa95ce20-a422-4154-b320-4d3621873421
---

Production incident on business 9a98743c-c1dc-42bf-a84e-b412397493db, table 1: cancelled/charged takeaway orders stayed visible; new orders showed stale items.

**KM-68** (owner abdo, status `ready-to-deploy` = merged to `origin/main` via PR #198 `d447988`, NOT yet deployed): backend order-lifecycle fixes — orphaned order on unresolved table id → 400; unified cancel onto `/checkout/` (idempotent, reverts inventory) replacing legacy `/cancel/`; rounds/cart reset on new delivery; shared `loadOrderRounds()`; delivery card stale $0 total; checkout header shows real order_number. Its branches live remote/on abdo's machine, NOT in local repos. **Never touch KM-68.**

**KM-69** (created 2026-07-07, owner hosmmer, in-progress, labels POS + REMOVED_LEGACY): residual FRONTEND stale-list sync bug on top of KM-68. Root cause: POS mobile screens all mounted at once (slide via CSS, never remount), so TakeawayScreen order-list queries never refetch. Fixes in branch `fix/km69-order-list-sync` (based off origin/main so it CONTAINS KM-68): OrderScreen.handleCheckoutDone invalidates pos-takeaway-orders/pos-delivery-orders-mobile/pos-digital-orders-mobile/pos-tables; TakeawayScreen cancel refetches; DesktopLayout Anular+Facturar onDone add activeTlOrder/dlOptOrder branches; TablesScreen drops `tables.length>0` guard on SET_TABLES. Backend needs no change. Typecheck clean.

xenodocIA API note: auth header is `Token <token>` NOT `Bearer` (mbird.CineViewHos.com). Ticket POST requires board=1, project=1. See [[reference_mockingbird_api_access]].
