---
name: project-debug-toolbar-slow-api
description: BFF local slow endpoints (>7s) were caused by Django Debug Toolbar instrumenting every API call
metadata: 
  node_type: memory
  type: project
  originSessionId: 8d682b0e-9760-44f5-af07-c23763a9391e
---

Síntoma reportado (2026-07-13): los endpoints del BFF local tardaban >7s SOLO en el backend (el front no), mientras al jefe (Mac/Linux) le corría rápido. "Antes iba bien, se puso lento hace unas semanas."

Causa raíz: `django-debug-toolbar` con `DEBUG_TOOLBAR_CONFIG["SHOW_TOOLBAR_CALLBACK"] = lambda request: True` en [config/settings.py]. Ese `lambda: True` forzaba al toolbar a instrumentar y perfilar CADA request de la API JSON (todas las queries SQL + timing), añadiendo ~8s por llamada. Medido: `/business/` con toolbar avg 9137ms → sin toolbar min 15ms; `/pos/tables/` 608ms → 7ms.

Fix aplicado: reemplazar el `lambda: True` por `_show_debug_toolbar(request)`. El toolbar sigue disponible para el navegador pero no toca la API. No se desinstaló nada.

REGRESIÓN (2026-07-13, misma fecha, más tarde): el fix se PERDIÓ (SHOW_TOOLBAR_CALLBACK volvió a `lambda: True`) — probablemente al cambiar de rama sin commitear. Además el fix v1 dependía del header `X-Requested-With`, que AXIOS NO MANDA, así que ni cubría el POS. Reventó feo: `/pos/tables/` = 30.937ms (30s!), navegador con "Puesto en cola: 1,6 min" y 500 solicitudes encoladas. Fix v2 (robusto): `_show_debug_toolbar` devuelve False para cualquier path que empiece con rutas de API (`/api/`,`/pos/`,`/business/`,`/orders/`,`/products/`,`/catalogs/`,`/users/`,`/ecommerce/`,`/accounting/`,`/loyalty/`,`/integrations/`,`/notifications/`,`/whatsapp/`,`/subscriptions/`,`/localizations/`) SIN depender de headers; solo páginas HTML del navegador se instrumentan. Medido: `/pos/tables/` 30937ms → 11ms. runserver SÍ es multi-thread (12 concurrentes en 711ms, no serializa) — el encolado era 100% culpa del toolbar. IMPORTANTE: este fix NO está commiteado; se vuelve a perder al cambiar de rama. Recomendar commit.

**Why:** El toolbar profila cada query/template en cada request; en API JSON es puro overhead porque la respuesta nunca muestra el HTML del toolbar.
**How to apply:** Si un endpoint del BFF local va lento pero la DB es rápida (37MB, queries <1ms) y solo pasa en el backend, revisar primero DEBUG_TOOLBAR_CONFIG antes de culpar al puente Docker/Windows de archivos.

Nota: el puente de archivos Windows↔Docker (código en C:\platform\ no en WSL2) SÍ es lento para I/O de archivos (46s escribir 2000 archivos vs 66ms nativo), pero NO era la causa de la lentitud de endpoints. Ver [[project_repo_paths]].
