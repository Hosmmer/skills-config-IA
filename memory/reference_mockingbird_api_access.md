---
name: reference-xenodocIA-api-access
description: "xenodocIA prod API access: URL is REMOVED_LEGACY_URL token in env var MOCKINGBIRD_API_TOKEN — token is for xenodocIA ONLY"
metadata: 
  node_type: memory
  type: reference
  originSessionId: 057c2856-3459-4650-a8ef-b2b82111fafd
---

Acceso a la API de xenodocIA de producción (tickets, wiki, planning — ver [[project-thoughts-moved-to-s3]]):

- **URL**: `REMOVED_LEGACY_URL (env var `MOCKINGBIRD_URL`)
- **Token**: en la env var `MOCKINGBIRD_API_TOKEN` (guardada en `~/.claude/settings.json` → `env`, no en texto plano). Header: `Authorization: Token {token}` (prefijo "Token ", NO "Bearer").
- El `.env` local de `C:\platform\xenodocIA` tiene un token DISTINTO que solo sirve para el xenodocIA local (`http://localhost:8002`) — no vale para producción.

**IMPORTANTE — alcance del token:** este token es **exclusivamente para xenodocIA** (su API de contenido/tickets). NO es credencial de git, ni de los repos REMOVED_LEGACY / REMOVED_LEGACY, ni de ningún otro servicio. Usarlo solo para llamadas a `mbird.CineViewHos.com`.

**How to apply:** Para consultar/crear tickets o leer wiki/planning, leer `MOCKINGBIRD_URL` y `MOCKINGBIRD_API_TOKEN` del entorno; nunca hardcodear el valor. Es un secreto — no imprimirlo en logs ni pegarlo en archivos versionados. Para el flujo de creación de tickets ver [[project-ticket-creation-via-api-s3]].
