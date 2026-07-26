# Memory Index

> **Note:** "devbookIA" replaces "devplaybook" as the internal toolkit name.

- [Skill selection: read full list before invoking](feedback_skill_selection.md) — Always scan the entire skills list; never stop at the first partial match
- [Skill files: always re-read fresh](feedback_skill_files_always_reread.md) — Skills update daily; re-read every references/*.md file at moment of use, never trust cached context
- [CineViewHos repo paths on this machine](project_repo_paths.md) — Repos at C:\platform\, REMOVED_LEGACY absent, devbookIA at C:\Users\hosmm\.claude\skills\devbookIA
- [grill-with-docs vs grill-me: never substitute](feedback_grill_skill_selection.md) — devk SDD pipeline requires grill-with-docs exactly; grill-me is a different, simpler skill
- [QA must be human-led, not auto-reported](feedback_qa_human_led.md) — Guide the human through each QA step; never self-execute and self-report results
- [xenodocIA API access](reference_mockingbird_api_access.md) — prod URL mbird.CineViewHos.com + token in env MOCKINGBIRD_API_TOKEN; token is for xenodocIA ONLY, not git/other repos
- [thoughts/ moved to S3 (KM-53/54)](project_thoughts_moved_to_s3.md) — tickets/wiki/planning now in S3, accessed only via xenodocIA content API; no local thoughts/ checkout
- [Ticket creation via API + S3](project_ticket_creation_via_api_s3.md) — POST /api/tickets/ IS used now; ID from GET /api/tickets/ max+1 human-confirmed; description via PUT /api/content/
- [Landed KM-48→KM-55](project_landed_km48_to_km55.md) — halvable pizza, POS bug fixes, featured products, deactivate categories, xenodocIA prod deploy, S3 cutover, roadmap
- [KM-56 Kanban workflow](project_km56_kanban_workflow.md) — 6 columns (qa + ready-to-deploy), skill-driven transitions, auto-done via CD; deploy-ordering + test-auth gotchas
- [Ticket numbering and paths](project_ticket_numbering.md) — UPDATED: tickets now in S3 via API, path tickets/YYYY-MM/KM-N/ is a content-API path not disk
- [Never touch tickets the user didn't ask for](feedback_never_touch_other_tickets.md) — Only operate on the exact ticket ID the user names; stop if next_id mismatches
- [KM-12: never touch it](feedback_km12_leave_alone.md) — KM-12 is a different ticket; API counter desynced — never read/modify/comment on thoughts/tickets/KM-12/
- [xenodocIA ticket creation rules](feedback_mockingbird_ticket_creation.md) — No em dash in titles (use :), always use Python urllib not curl, check next_id before creating, verify titles vs RELEASE-QUEUE
- [Ticket path: always under YYYY-MM](feedback_ticket_path_correctness.md) — path is now a content-API path tickets/YYYY-MM/KM-N/ (post-S3); always include YYYY-MM
- [KM-32: Bulk Product Import/Export](project_km32_bulk_import.md) — Excel import/export for product catalog, landed 2026-06-25 across all repos
- [Ticket creation: just do it, skip API](feedback_ticket_creation_just_do_it.md) — SUPERSEDED by S3 cutover; see project_ticket_creation_via_api_s3 (API now used, human still confirms ID)
- [No grey buttons or text ever](feedback_no_grey_ui.md) — Grey (btn-secondary, text-muted, #94A3B8, etc.) is forbidden in UI; always use a colored variant
- [One open order per table](project_one_open_order_per_table.md) — POS invariant (KM-46/ADR-0006); resolve open order from backend before charging, never trust cached orderId
- [LineItem soft-delete pending](project_lineitem_softdelete_pending.md) — Deleted items/rounds are React-only; lost on table switch. Soft-delete deferred
- [Pizza por mitades = flag separado is_halvable](project_pizza_halves_reuses_portionable.md) — NO reutiliza is_portionable (decisión revertida 2026-07-02); flujo/precio propios, mutuamente excluyente con por-porciones
- [Fetch xenodocIA antes de numerar ticket](feedback_fetch_before_ticket_number.md) — El jefe crea tickets en remoto; siempre git pull antes de proponer número (KM-47 ya era suyo)
- [No quemar enums cuando el modelo es dinámico](feedback_no_hardcoded_enums_when_dynamic.md) — Si un campo es escalar abierto para escalar sin migración, la UI debe ser input libre / lista data-driven, nunca un select con opciones fijas
- [Cambios quirúrgicos: no ampliar alcance sin preguntar](feedback_surgical_scope_no_broad_changes.md) — Si piden algo específico, tocar SOLO eso; nunca selectores CSS amplios ni cambios extra sin preguntar primero
- [KM-68 / KM-69 POS order sync](project_km68_km69_pos_order_sync.md) — KM-68 (abdo, merged not deployed) fixed backend order corruption; KM-69 (hosmmer) is residual frontend stale-list sync; mbird auth header is `Token` not Bearer, POST needs board+project
- ["Anular" no es "Cancelar"](feedback_anular_not_cancelar.md) — En el POS, anular una orden SIEMPRE se llama "Anular orden" en la UI, nunca "Cancelar"; son conceptos distintos para el usuario
- [KM-73 POS bugs + guard cancelled 3 capas](project_km73_pos_cancelled_guard.md) — Bug recurrente cancelled/500 cuya causa #1 era fixes nunca desplegados; blindaje frontend assertOrderOpen + backend add_line valida estado + deploy
- [POS 500 CategoryModifier FK](project_pos_modifier_snapshot_fk.md) — LineItemModifier.modifier FK solo acepta products.Modifier; halves/portions con CategoryModifier reventaban; todo create debe pasar por _modifier_snapshot
- [KM-76 lote UX POS](project_km76_pos_ux_batch.md) — sync polling 3s, X 44x44 ModalCloseButton, cierre modales al navegar, Ver Orden domicilios, mesero móvil, dropdown perfil; todo frontend
- [Sheets POS altos: hide-bars + sticky](feedback_pos_fullheight_sheets_hide_bars.md) — sheets con overflow usan .pos-sheet--hide-bars + header sticky + lista interna scroll; números reales de mesas no +N
- [Nunca emojis en UI, iconos reales](feedback_no_emoji_use_real_icons.md) — Emojis rompen en algunos dispositivos (causa del bug KM-91); usar siempre SVG inline o bi bi-* , nunca 📝/✏/▸
- [BFF local lento por Debug Toolbar](project_debug_toolbar_slow_api.md) — Endpoints >7s solo en backend: SHOW_TOOLBAR_CALLBACK lambda:True instrumentaba cada API call; fix = saltar AJAX/JSON
- [xenodocIA dev hot-reload](project_mockingbird_dev_hotreload.md) — make run-devw servía build estático viejo; ahora Vite dev + volumen montado = código actual siempre; gotcha esbuild win32 node_modules
- [Config dev local oculta de Git](project_local_dev_config_skip_worktree.md) — settings.py/vite.config.ts/Makefile en skip-worktree + 3 archivos xenodocIA en info/exclude; nunca PR; los del POS sí se commitean
- [feedback_bug_reports_trigger_diagnosing_bugs.md](feedback_bug_reports_trigger_diagnosing_bugs.md) — ANY bug report (however small, wherever raised in convo) triggers full devk diagnosing_bugs.md pipeline before touching code — no inline "quick fixes"
- [POS menu display invariant](project_pos_menu_display_invariant.md) — POS menu (incl. Destacados tab) must always reflect what's configured in Admin; no acceptable divergence
