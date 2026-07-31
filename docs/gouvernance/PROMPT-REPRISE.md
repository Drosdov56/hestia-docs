Reprise du projet HESTIA.

Avant toute action :

1. Lire intégralement [PROJECT-STATE.md](PROJECT-STATE.md).
2. Considérer ce document comme la référence opérationnelle du projet.
3. Ne pas refaire d’audit global.
4. **UI-001 est officiellement clos** (VALIDÉ AVEC RÉSERVES). Ne pas le rouvrir sauf anomalie critique.
5. **UI-002 est officiellement clos**. Ne pas le rouvrir sauf anomalie critique.
6. **Aucun chantier actif.** Ne pas inventer ni démarrer de prochain chantier sans demande explicite.
7. Signaler uniquement les divergences factuelles éventuelles.

Contexte figé (2026-07-31) :

- **AUTO-002 CLÔTURÉ** (lots A→F) — suivi [`execution/AUTO-002.md`](../backlog/execution/AUTO-002.md).
- **UI-001 CLÔTURÉ** (lots B→G) — VALIDÉ AVEC RÉSERVES (Tech Committee).
- **UI-002 CLÔTURÉ** (lots A→D) — consolidation ergonomique admin nœuds ; tip `f7c481d` ; SW PWA `hestia-v0.8.37`.
- **ADR-021 Accepté** — `node_id` permanent ; token unique par nœud ; hash-only ; hostname/display_name hors auth ; présence ≠ lifecycle.
- **Token global ingest : supprimé définitivement** (F6) — ne pas le réintroduire.
- **ADR-023 Accepté** — `hestia-ws-relay` = transport ; aucune logique métier / identité.
- Lire la section **INFRASTRUCTURE** de PROJECT-STATE.md avant toute action SSH/déploiement : VPS = `/var/www/hestia` ; nœud = `/opt/hestia` ; hostname `hestia` = mini-PC, pas le VPS.

À la fin de la session :

- mettre à jour [PROJECT-STATE.md](PROJECT-STATE.md) ;
- régénérer ce PROMPT-REPRISE.md.
