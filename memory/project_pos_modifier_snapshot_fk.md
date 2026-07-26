---
name: project_pos_modifier_snapshot_fk
description: "POS 500 \"Cannot assign CategoryModifier ... must be a Modifier instance\" — LineItemModifier FK bug and its fix"
metadata: 
  node_type: memory
  type: project
  originSessionId: f5258bd6-b25a-42e2-96f4-de40a624fec5
---

El POS backend maneja **3 tipos de modificador** en paralelo: `products.Modifier` (own, por template, único con `is_flavor_group`/`tier`), `products.CategoryModifier` (por categoría, su grupo NO tiene is_flavor_group), y `ExtensionModifier`. `LineItemModifier.modifier` es FK **solo a `products.Modifier`** (null=True).

**Bug (Sentry 500, KM-73):** `PosOrderService._add_halvable_line` / `_add_portionable_line` / `update_line` asignaban `modifier=modifier` DIRECTO al FK. Si un sabor/mitad/porción venía de un CategoryModifier/ExtensionModifier → `ValueError: Cannot assign "CategoryModifier ..." must be a Modifier instance` → 500. Intermitente porque depende de si el producto tiene sus sabores como grupo de categoría vs grupo propio.

**El fix ya existía y no se usaba:** `PosOrderService._modifier_snapshot(modifier)` devuelve `(group_name, modifier_fk)` donde modifier_fk=None salvo que sea un `products.Modifier` real. TODO `LineItemModifier.objects.create` debe pasar por él. Se corrigieron los ~6 puntos en pos_order_service.py.

**Capas del fix (KM-73, frontend+backend, all local, 222 tests pos verdes):**
1. Servicio: todos los creates de LineItemModifier usan `_modifier_snapshot`.
2. Vista `PosOrderLinesView.post`: `halves` y `portions` se resuelven con `_resolve_modifiers` (los 3 tipos), no solo `Modifier.objects.filter` (que daba 400 "not found" para flavors de categoría).
3. Frontend OrderScreen: el catch de guardar ronda ahora muestra el `detail` del backend, no un genérico.

Nota: la validación `half.group.is_flavor_group` en `_add_halvable_line` usa getattr(...,False); un CategoryModifier no la pasa (su grupo no tiene el campo) → daría 400 limpio, no 500. Si se requiere que flavors de categoría funcionen como mitades, es decisión de producto aparte.

Regla general: cuando un modelo tiene FK a UN tipo pero el dominio maneja varios tipos "hermanos", nunca asignar el objeto crudo — siempre pasar por el resolvedor de snapshot. Related: [[project_km73_pos_cancelled_guard]], [[project_pizza_halves_reuses_portionable]]
