---
name: project-pizza-halves-reuses-portionable
description: "Pizza por mitades = flag NUEVO is_halvable, separado y mutuamente excluyente de is_portionable (NO lo reutiliza — decisión revertida 2026-07-02)"
metadata:
  node_type: memory
  type: project
  originSessionId: 0abd93f0-387a-4bf8-ae44-19892f25e499
---

Feature "pizza por mitades" (en grill/spec desde 2026-07-02). **CORRECCIÓN 2026-07-02:** una versión previa de esta memoria decía reutilizar `is_portionable` con `total_portions=2`. El usuario decidió lo CONTRARIO en el grill: es un flag NUEVO `is_halvable` en `ProductTemplate`, **separado y mutuamente excluyente** de `is_portionable`. Cada uno tiene su lógica aparte. Razón del usuario: "algunas pizzas tendrán la lógica de porciones y otras la de mitades — lo mejor es separarlo".

**Decisiones del grill (todas confirmadas por el usuario):**
- Flag `is_halvable` en `ProductTemplate`, XOR con `is_portionable` (los checks en admin se auto-excluyen).
- Pantalla POS nueva **HalfSheet** (2 selectores: Mitad 1 / Mitad 2), separada del PortionSheet. Cada selector muestra sabores en 2 secciones: "Tradicionales" y "Especiales" (badge especial con icono `bi-*`, NUNCA emoticón — ver [[feedback-no-grey-ui]] estilo).
- Sabores = 2 `ModifierGroup` con `is_flavor_group=True` ("Tradicionales", "Especiales"). El PortionSheet actual hace `find(g => g.is_flavor_group)` (asume UNO); HalfSheet debe leer AMBOS.
- Tiers de sabor via `ModifierGroup.tier` (entero LIBRE: 0=tradicional/sin recargo, 1=especial, N+). Escala sin migración — descartado `is_special_group` booleano (no escala). El usuario NO quiere un flag por tier NI una lista quemada de niveles: en el admin el tier es un INPUT NUMÉRICO libre, no un `<select>` con opciones fijas. Tabla `FlavorTier` con nombres/colores = ticket futuro (no en KM-48). Porciones NO se unifican con tiers (ejes distintos). Crear sabores/grupos sigue igual, tier default 0, no afecta flujos existentes.
- Precio = `precio_variante_tamaño + recargo_combinación + Σ(modificadores_globales)`. Recargo (BR-4): trad+trad (tier 0+0) = **0, solo precio del tamaño**; 1 mitad tier>0 = `per_half`; 2 mitades mismo tier>0 = `both_cap` (TOPE FIJO, no 2×per_half). Mediana: per_half=6.000, both_cap=10.000. Small/pequeña/super small: per_half=5.000, both_cap=10.000.
- Recargos en tabla nueva `VariantTierSurcharge(variant, tier, per_half, both_cap)` — reemplaza los campos fijos que se habían pensado en `ProductProduct`. Config por (tamaño, tier) desde admin. Filas faltantes = 0. Tiers mixtos >0 distintos (esp+premium) = fuera de scope.
- Bordes: mismo sabor en ambas mitades permitido; las 2 mitades FÍSICAS son obligatorias (botón Agregar deshabilitado hasta ambas), pero cualquier combinación de tipos es libre; modificadores globales igual que hoy.
- Comanda: `Mitad 1: X / Mitad 2: Y` dinámico y LITERAL — NO merge de modifiers en líneas `is_halvable` (dos mitades del mismo sabor deben imprimirse por separado).
- Editar item (pencil) abre HalfSheet precargado; mismo `PATCH /lines/{id}/` que porciones.
- Ambos checks (porciones + mitades) van también en `TemplateCreatePanel.tsx` (hoy no tiene ninguno), mutuamente excluyentes.

**Ticket:** KM-48 (KM-47 lo tomó el jefe = FRP print tunnel). **ESTADO: implementado, QA pasado, documentado (2026-07-03).** ADRs escritos: products/adr/0005 (halvable separado de portionable) y 0006 (tier escalar vs booleano). Doc UX con esquema de creación: pos/ux/product-creation-flow.md. Specs en wiki/contexts/{pos,products}/specs/KM-48. Falta solo: /gcpush + /gpr. Ver [[project-repo-paths]], [[feedback-fetch-before-ticket-number]].
