---
name: project-km76-pos-ux-batch
description: "KM-76: lote de mejoras POS (sync polling 3s, X accesibles 44x44, Ver Orden domicilios, mesero movil, dropdown perfil) — frontend only"
metadata: 
  node_type: memory
  type: project
  originSessionId: ceb23d02-0909-4ff7-be81-6c3db5617b3e
---

KM-76 (2026-07-08, asignado a hosmmer): lote de 5 mejoras POS en `REMOVED_LEGACY`, **solo frontend**, aplicado en móvil (`POSLayout` + `screens/*`) y desktop (`desktop/DesktopLayout.tsx`, ~3200 líneas). Spec/plan en xenodocIA `tickets/2026-07/KM-76/`.

Fases implementadas:
1. **Sync tiempo real = polling react-query** (NO websockets: backend en AWS Lambda/Mangum no sostiene conexiones). Constante única `core/_constants.ts` → `POS_LIST_POLL_MS = 10000` (evolución: 6s→3s→10s; 3s generaba demasiadas peticiones). CLAVE: cada lista gateada a su pantalla activa (`refetchInterval: screen===N ? POLL : false`) — sin gatear, con 5 listas montadas eran ~78 requests simultáneas. Solo la lista visible poolea; al manipular (abrir/liberar/cobrar/crear) hay invalidación inmediata, así que el intervalo solo cubre la sync entre usuarios concurrentes. `refetchInterval` + `refetchOnWindowFocus:true` en las 5 listas. OJO: delivery usa keys DISTINTOS por layout (`pos-delivery-orders` desktop vs `pos-delivery-orders-mobile` móvil). NUNCA polar el detalle de orden en edición (pisa cartItems/rounds).
2. **X de modales = componente `ModalCloseButton`** (hitbox 44×44 WCAG, círculo visual ~30px, variantes dark/light) + fix clase `.pos-sheet-close` en pos.css (cubre HalfSheet/ModifierSheet/PaymentSheet/SessionPanel/SplitBillSheet de una).
3. **Cierre de modales al navegar**: desktop `closeAllMenuModals()` en efecto keyed `[dtView, state.screen]`; móvil efectos por pantalla keyed `state.screen`. EXCLUIR sheets de caja (SessionOpenSheet gate, SessionPanel, NewCashMovementModal). Registro en `core/menuModals.ts` + test.
4. **"Ver Orden" en domicilios** (`DeliveryOptionsModal`): abre gestión/detalle con rondas (`goTo(3)`), NO checkout. Desktop `openDeliveryOrderDetail()` factorizado.
5. **Mesero en móvil**: acción `SET_ACTIVE_ORDER_WAITER` + state `activeOrderWaiter` en POSContext; chip en header `OrderScreen` (`.pos-order-waiter-chip`).
6. **Dropdown perfil** (`ProfileMenu.tsx` + `useClickAway.ts`): pill → menú con Ir al Dashboard (`/${businessId}/dashboard`) + Cerrar Sesión (`useAuth().logout` de `auth/core/Auth`). Reemplaza pill estático en NavBar (móvil) y `pos-dsk-top-right` (desktop).

Comandos frontend: `npx tsc --noEmit`, `npm run lint` (eslint `--max-warnings 0`, pero hay ~142 warnings pre-existentes en DesktopLayout — comparar contra baseline con git stash), `npx vitest run <path>`.
