---
name: project-xenodocIA-dev-hotreload
description: make run-devw now runs Vite dev + hot-reload so local xenodocIA always shows current code
metadata: 
  node_type: memory
  type: project
  originSessionId: 8d682b0e-9760-44f5-af07-c23763a9391e
---

Problema (2026-07-13): `localhost:5174/board/1` mostraba código viejo. Causa: el `docker-compose.yml` base sirve el frontend como build ESTÁTICO de nginx (`RUN npm run build` → `dist/` en la imagen). `make run-devw` hacía `docker compose up -d` que reusaba la imagen construida (era del 9-jun), nunca recompilaba. NO era git — main estaba al día.

Fix (modo dev con hot-reload):
- Nuevo `frontend/Dockerfile.dev`: corre Vite dev (`npm run dev --host`), no nginx. CMD hace `npm ci` si falta vite en el volumen.
- Nuevo `docker-compose.dev.yml` (overlay): bind-mount `./frontend:/app` + volumen NOMBRADO `frontend_node_modules:/app/node_modules`; backend con `./backend:/app` + `runserver` (autoreload). Fija `DJANGO_SETTINGS_MODULE=...development` y `DJANGO_ALLOWED_HOSTS=localhost,127.0.0.1,backend` (el Dockerfile backend hardcodea ENV=production y Vite proxy usa changeOrigin → Host `backend`).
- `frontend/vite.config.ts`: proxy `/api` target = `process.env.VITE_API_TARGET ?? 127.0.0.1:8002` (en Docker = http://backend:8002); + `server.watch.usePolling=true` (bind mounts Windows no emiten inotify).
- `frontend/.dockerignore`: excluye node_modules/dist/.git.
- `Makefile` run-devw: `docker compose -f docker-compose.yml -f docker-compose.dev.yml up -d --build`.

**Why:** build estático nginx = código congelado; Vite dev + volumen montado = siempre el código actual con HMR.
**How to apply:** para desarrollo local usar `make run-devw`. `make run` (base) sigue siendo el build de producción nginx.

Segundo síntoma (mismo día): "no veo ningún ticket" en el board. Causa: migración `tickets.0007_ticket_order` (y projects/roadmap) SIN aplicar en la DB local → la API 500 con `ProgrammingError: column tickets_ticket.order does not exist` → board vacío. La DB SÍ tenía 34 tickets; era el schema desincronizado del código. Fix inmediato: `docker exec xenodocIA-backend-1 python manage.py migrate`. Fix permanente: el `command` del backend en docker-compose.dev.yml ahora es `sh -c "migrate --noinput && runserver"` → auto-migra en cada boot. Lección: traer código nuevo con migraciones NO las aplica solo; por eso se auto-migra al arrancar.

Gotcha esbuild: el error `Host version 0.27.7 does not match binary 0.28.1` venía del `frontend/node_modules` de WINDOWS (binario win32-x64) filtrándose al contenedor Linux vía bind mount. Solución: borrar `frontend/node_modules` de Windows + recrear el volumen nombrado para que `npm ci` corra 100% en Linux. El `.dockerignore` previene la recontaminación. Ver [[project_repo_paths]] y [[project_km68_km69_pos_order_sync]] (mbird auth Token).
