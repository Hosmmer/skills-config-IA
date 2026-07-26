---
name: project-lineitem-softdelete-pending
description: LineItem hard-delete means deleted items/rounds vanish on table switch — soft-delete needed
metadata: 
  node_type: memory
  type: project
  originSessionId: 75870430-c0ae-4b89-9275-dfc22de0fb50
---

LineItem soft-delete is pending. Currently `remove_line` calls `line.delete()` and `delete_round` calls `LineItem.objects.filter(...).delete()` — both hard deletes. When the user switches tables or reloads, the React POS state is reset and deleted items/rounds are gone from the UI with no trace.

**Why:** The user confirmed the scope: React state only holds deleted item markers (`deleted: true`) in memory during the active session. Switching mesa clears the state and the history is lost. The fix requires soft-delete so the backend persists the deleted state and the frontend can restore it on reload.

**How to apply:** When this work is requested, implement:
1. Migration: `is_deleted = BooleanField(default=False)` on `LineItem`
2. `remove_line` → `line.is_deleted = True; line.save()` instead of `line.delete()`
3. `delete_round` → `LineItem.objects.filter(...).update(is_deleted=True)` instead of `.delete()`
4. `recalc_totals_from_lines` → exclude `is_deleted=True`
5. `OrderRoundSerializer.get_round_lines` → include `is_deleted` field in response
6. Frontend `reloadRounds` / table open → map `is_deleted: true` → `deleted: true` on the item
