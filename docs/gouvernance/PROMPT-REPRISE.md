Reprise du projet HESTIA.

Avant toute action :

1. Lire intégralement [PROJECT-STATE.md](PROJECT-STATE.md).
2. Considérer ce document comme la référence opérationnelle du projet.
3. Ne pas refaire d’audit global.
4. **UI-001 est officiellement clos** (VALIDÉ AVEC RÉSERVES). Ne pas le rouvrir sauf anomalie critique.
5. Prochain chantier produit possible : **UI-002** — Consolidation ergonomique de l’administration des nœuds — uniquement si explicitement demandé.
6. Signaler uniquement les divergences factuelles éventuelles.

Contexte figé (2026-07-30) :

- **AUTO-002 CLÔTURÉ** (lots A→F) — suivi [`execution/AUTO-002.md`](../backlog/execution/AUTO-002.md).
- **UI-001 CLÔTURÉ** (lots B→G) — VALIDÉ AVEC RÉSERVES (Tech Committee). Réserves → backlog **UI-002** ; aucun retour sur UI-001 hors anomalie critique.
- **ADR-021 Accepté** — `node_id` permanent ; token unique par nœud ; hash-only ; hostname/display_name hors auth ; présence ≠ lifecycle.
- **Token global ingest : supprimé définitivement** (F6) — ne pas le réintroduire.
- **ADR-023 Accepté** — `hestia-ws-relay` = transport ; aucune logique métier / identité.
- SHA hestia tip UI-001 : `aa6f3ad` (SW PWA `hestia-v0.8.33`).
- Lire la section **INFRASTRUCTURE** de PROJECT-STATE.md avant toute action SSH/déploiement : VPS = `/var/www/hestia` ; nœud = `/opt/hestia` ; hostname `hestia` = mini-PC, pas le VPS.

À la fin de la session :

- mettre à jour [PROJECT-STATE.md](PROJECT-STATE.md) ;
- régénérer ce PROMPT-REPRISE.md.
