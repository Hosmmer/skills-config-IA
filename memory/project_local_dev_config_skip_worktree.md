---
name: project-local-dev-config-skip-worktree
description: Local-only dev config files hidden from Git via skip-worktree / info-exclude — never PR them
metadata: 
  node_type: memory
  type: project
  originSessionId: 8d682b0e-9760-44f5-af07-c23763a9391e
---

El jefe (2026-07-13) quiere que varios arreglos de config de desarrollo local vivan SOLO en su máquina: funcionan local pero Git debe ignorarlos, sin PR. Aplicado así:

**Ocultos con `git update-index --skip-worktree`** (rastreados; flag `S` en `git ls-files -v`):
- REMOVED_LEGACY: `config/settings.py` (fix Debug Toolbar, ver [[project_debug_toolbar_slow_api]])
- REMOVED_LEGACY: `vite.config.ts` (devOptions.enabled:false → PWA/service worker off en dev, quita spam Workbox "No route found")
- xenodocIA: `Makefile` + `frontend/vite.config.ts` (ver [[project_mockingbird_dev_hotreload]])

**Excluidos vía `.git/info/exclude`** (nuevos, no rastreados, exclude es local y no se comparte):
- xenodocIA: `docker-compose.dev.yml`, `frontend/.dockerignore`, `frontend/Dockerfile.dev`

**Why:** son ajustes de entorno Windows/Docker local que no deben tocar el repo del equipo.
**How to apply:**
- Revertir (volver a ver/commitear): `git update-index --no-skip-worktree <archivo>`.
- GOTCHA: si un compañero modifica uno de estos archivos rastreados y el jefe hace pull, Git puede fallar por conflicto con la versión skip-worktree. Hay que `--no-skip-worktree`, hacer stash/pull, y re-aplicar.
- Los archivos del POS (TabBar, MenuScreen, TakeawayScreen, DesktopLayout, POSContext, menuModals, OrderTypeSelectSheet) NO se ocultaron — son trabajo real del jefe, se commitean normal.
