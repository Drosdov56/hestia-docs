Reprise du projet HESTIA.

Avant toute action :

1. Lire intégralement [PROJECT-STATE.md](PROJECT-STATE.md).
2. Considérer ce document comme la référence opérationnelle du projet.
3. Ne pas refaire d’audit global.
4. **UI-001** et **UI-002** sont officiellement clos. Ne pas les rouvrir sauf anomalie critique.
5. **EPIC-002 est officiellement clos** (SoT équipements Module 70, lots A→D). Ne pas le rouvrir sauf anomalie critique.
6. **Aucun chantier actif.** Le prochain développement repart depuis cet état stabilisé — uniquement sur demande explicite.
7. Signaler uniquement les divergences factuelles éventuelles.

Contexte figé (2026-07-31) :

- **AUTO-002 CLÔTURÉ** (lots A→F) — suivi [`execution/AUTO-002.md`](../backlog/execution/AUTO-002.md).
- **UI-001 CLÔTURÉ** (lots B→G) — VALIDÉ AVEC RÉSERVES (Tech Committee).
- **UI-002 CLÔTURÉ** (lots A→D) — consolidation ergonomique admin nœuds ; tip `f7c481d` ; SW PWA `hestia-v0.8.37`.
- **EPIC-002 CLÔTURÉ** (lots A→D) — SoT équipements ; tip `c73a7d5` ; F-007→F-012 ; suivi [`execution/EXEC-EPIC-002.md`](../backlog/execution/EXEC-EPIC-002.md).
- **ADR-021 Accepté** — `node_id` permanent ; token unique par nœud ; hash-only ; hostname/display_name hors auth ; présence ≠ lifecycle.
- **Token global ingest : supprimé définitivement** (F6) — ne pas le réintroduire.
- **ADR-023 Accepté** — `hestia-ws-relay` = transport ; aucune logique métier / identité.
- **ADR-005 / Module 70** — registre équipements Backend opérationnel (identité, états, nom logique, remplacement, reprises).
- Lire la section **INFRASTRUCTURE** de PROJECT-STATE.md avant toute action SSH/déploiement : VPS = `/var/www/hestia` ; nœud = `/opt/hestia` ; hostname `hestia` = mini-PC, pas le VPS.

À la fin de la session :

- mettre à jour [PROJECT-STATE.md](PROJECT-STATE.md) ;
- régénérer ce PROMPT-REPRISE.md.
