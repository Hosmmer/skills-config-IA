# Skills Config IA

Repositorio central de configuracion, skills, reglas y memoria del flujo de trabajo de IA para los proyectos CineViewHos y XenodocIA.

## Estructura

```
skills-config-IA/
├── README.md                   ← este archivo
├── opencode.jsonc              ← config de opencode (schema, version)
├── skills/                     ← 24 skills globales de opencode
│   ├── devk/                   ← pipeline SDD (spec-driven development)
│   │   ├── SKILL.md
│   │   └── references/         ← 11 documentos del pipeline
│   ├── promote/                ← promocion de specs a wiki
│   ├── domain-modeling/        ← modelado de dominios y ADRs
│   ├── diagnosing-bugs/        ← pipeline de diagnostico de bugs (7 fases)
│   ├── planning/               ← planificacion de proyectos y fases
│   ├── grill-with-docs/        ← grill contra el modelo de dominio existente
│   ├── create-ticket/          ← creacion de tickets en XenodocIA
│   ├── create-ticket-backlog/  ← creacion de tickets en backlog
│   ├── create-ticket-roadmap/  ← creacion de tickets en roadmap
│   ├── add-comment/            ← agregar comentarios a tickets
│   ├── update-status/          ← actualizar estado de tickets
│   ├── to-spec/                ← convertir conversacion en spec
│   ├── to-tickets/             ← descomponer plan en tickets
│   ├── gcpush/                 ← commit + push con mensaje auto-generado
│   ├── gpmain/                 ← sync a main branch
│   ├── gpr/                    ← crear Pull Request
│   ├── code-review/            ← revision de codigo (2 ejes)
│   ├── codebase-design/        ← diseño de modulos profundos
│   ├── grill-me/               ← entrevista relentlessly sobre un diseño
│   ├── handoff/                ← compactar conversacion para otro agente
│   ├── park/                   ← capturar idea y rutearla
│   ├── tdd/                    ← test-driven development (red-green-refactor)
│   ├── cineviewhos-deploy/     ← deploy a STG y PROD
│   └── write-a-skill/          ← crear nuevas skills
├── rules/                      ← reglas globales
│   ├── domain-skills.md        ← auto-carga de skills por path
│   └── workflow.md             ← kanban, tickets, git, pre-push checks
├── memory/                     ← 38 archivos de memoria aprendida (Claude)
│   ├── feedback_*.md           ← reglas aprendidas de feedback del usuario
│   ├── project_*.md            ← estado y convenciones del proyecto
│   └── reference_*.md          ← referencias de API y configuracion
├── manifests/                  ← manifiestos de proyectos
│   ├── cineviewhos-agents.md   ← AGENTS.md de CineViewHos
│   ├── xenodocia-agents.md     ← AGENTS.md de XenodocIA
│   └── xenodocia-claude.md     ← CLAUDE.md de XenodocIA
├── claude/
│   └── settings.json           ← config de Claude (modelo, permisos)
└── projects/                   ← skills y reglas especificas por proyecto
    ├── cineviewhos/
    │   ├── AGENTS.md           ← manifiesto del proyecto
    │   ├── rules/              ← reglas del proyecto
    │   └── skills/             ← 7 skills locales (overrides)
    └── xenodocIA/
        ├── AGENTS.md           ← manifiesto del proyecto
        ├── CLAUDE.md           ← referencia rapida Claude
        ├── rules/              ← reglas del proyecto
        ├── skills/             ← 11 skills locales + claude
        ├── agents/             ← skills externas (ui-ux-pro-max)
        └── skills-lock.json    ← lock de skills externas
```

## Flujo de trabajo

### Skills: donde viven

| Tipo | Ubicacion | Proposito |
|------|-----------|-----------|
| **Global** | `skills/` | Skills genericas que aplican a cualquier proyecto |
| **Repo-local** | `projects/{proyecto}/skills/` | Overrides con configuracion especifica del proyecto |
| **Externas** | `projects/xenodocIA/agents/skills/` | Skills de terceros (GitHub) |

### Resolucion de skills

1. Skill global cargada primero (define el flujo de trabajo)
2. Skill repo-local la extiende (agrega configuracion del proyecto)
3. Domain skills cargadas por path (definido en `rules/domain-skills.md`)

### Memoria

Los archivos en `memory/` son el conocimiento acumulado de sesiones de Claude:
- **feedback_***: Reglas que el usuario ha enseñado (no grey UI, no emojis, surgical scope, etc.)
- **project_***: Estado actual del proyecto, convenciones, paths de repos
- **reference_***: Referencias tecnicas (API access, ticket numbering)

## Actualizacion

Cada vez que se modifica una skill, regla, o se aprende una nueva regla:

```bash
cd ~/.config/opencode
git add -A
git commit -m "descripcion del cambio"
git push
```

## Recuperacion

En una maquina nueva:

```bash
# Clonar en ~/.config/opencode
git clone https://github.com/Hosmmer/skills-config-IA.git ~/.config/opencode

# Las skills y reglas se cargan automaticamente desde aqui
# Los proyectos/ deben copiarse a sus respectivos .opencode/ si se necesita
```

## Skills clave

### devk (Spec-Driven Development Pipeline)

Pipeline completo de desarrollo:
1. Research → 2. Grill → 3. Spec → 4. Plan → 5. Implement → 6. QA → 7. Document → 8. Push/PR

### promote

Promueve specs de tickets completados a la wiki. Actualiza CONTEXT.md y CHANGELOG.md.

### domain-modeling

Construye y afina el modelo de dominio. Al crear un ADR, actualiza automaticamente CONTEXT.md y CHANGELOG.md.

### diagnosing-bugs

Pipeline de 7 fases para diagnosticar bugs: feedback loop → reproducir → hipotesis → instrumentar → fix → limpiar → documentar.

## Reglas de dominio (aprendidas)

- No grey UI (btn-secondary, text-muted, #94A3B8, #8E8E93 prohibidos)
- No emojis en UI (SVG o bi bi-* icons)
- Surgical scope: tocar solo lo pedido
- Documentation MANDATORY after QA (CONTEXT.md + CHANGELOG.md)
- Free input para campos extensibles (nunca dropdowns hardcodeados)
- Especificacion y plan requieren aprobacion explicita
- QA human-led (guiar, nunca auto-ejecutar y auto-reportar)
- Tickets: solo operar sobre el ticket que el usuario nombra
