Reprise du projet HESTIA.

Avant toute action :

1. Lire intégralement [PROJECT-STATE.md](PROJECT-STATE.md).
2. Considérer ce document comme la référence opérationnelle du projet.
3. Ne pas refaire d’audit global.
4. **UI-001** et **UI-002** sont officiellement clos. Ne pas les rouvrir sauf anomalie critique.
5. **EPIC-002 est officiellement clos** (SoT équipements Module 70, lots A→D). Ne pas le rouvrir sauf anomalie critique.
6. **EPIC-003 est officiellement clos** (assistant de mise en service UX-003, lots A→D, F-013→F-017). Ne pas le rouvrir sauf anomalie critique.
7. **EPIC-004 est officiellement clos** (PoC événement → information utile, lots A→D, F-018 / F-019). Ne pas le rouvrir sauf anomalie critique.
8. **EPIC-005 est officiellement clos** (Hub familial & notifications, lots A→D, F-020 / F-021). Ne pas le rouvrir sauf anomalie critique.
9. **Aucun chantier actif.** La prochaine reprise part du **prochain EPIC du backlog** (**EPIC-006** — pipeline information) — uniquement sur demande explicite.
10. Signaler uniquement les divergences factuelles éventuelles.

Contexte figé (2026-07-31) :

- **AUTO-002 CLÔTURÉ** (lots A→F) — suivi [`execution/AUTO-002.md`](../backlog/execution/AUTO-002.md).
- **UI-001 CLÔTURÉ** (lots B→G) — VALIDÉ AVEC RÉSERVES (Tech Committee).
- **UI-002 CLÔTURÉ** (lots A→D) — consolidation ergonomique admin nœuds ; tip `f7c481d` ; SW PWA `hestia-v0.8.37`.
- **EPIC-002 CLÔTURÉ** (lots A→D) — SoT équipements ; tip `c73a7d5` ; F-007→F-012 ; suivi [`execution/EXEC-EPIC-002.md`](../backlog/execution/EXEC-EPIC-002.md).
- **EPIC-003 CLÔTURÉ** (lots A→D) — Assistant mise en service ; tip `b93d209` ; F-013→F-017 ; SW `hestia-v0.8.41` ; suivi [`execution/EXEC-EPIC-003.md`](../backlog/execution/EXEC-EPIC-003.md).
- **EPIC-004 CLÔTURÉ** (lots A→D) — PoC information utile ; tip `2fd0cce` ; F-018 / F-019 ; suivi [`execution/EXEC-EPIC-004.md`](../backlog/execution/EXEC-EPIC-004.md).
- **EPIC-005 CLÔTURÉ** (lots A→D) — Hub familial & notifications ; tip `0a564b0` ; F-020 / F-021 ; SW `hestia-v0.8.44` ; suivi [`execution/EXEC-EPIC-005.md`](../backlog/execution/EXEC-EPIC-005.md).
- **Hub familial opérationnel** · contrat `hub.v1` · notifications in-app · bridge Android.
- **Premier flux fonctionnel complet** : événement métier → information utile → Hub → notification optionnelle.
- Parcours MS : détection → admission → validation → mise en service → appairage ; intégration `hestia-agent` opérationnelle.
- **ADR-021 Accepté** — `node_id` permanent ; token unique par nœud ; hash-only ; hostname/display_name hors auth ; présence ≠ lifecycle.
- **Token global ingest : supprimé définitivement** (F6) — ne pas le réintroduire.
- **ADR-023 Accepté** — `hestia-ws-relay` = transport ; aucune logique métier / identité.
- **ADR-004 / ADR-005 / Module 70** — mise en service Admin + registre équipements Backend opérationnels.
- Lire la section **INFRASTRUCTURE** de PROJECT-STATE.md avant toute action SSH/déploiement : VPS = `/var/www/hestia` ; nœud = `/opt/hestia` ; hostname `hestia` = mini-PC, pas le VPS.

À la fin de la session :

- mettre à jour [PROJECT-STATE.md](PROJECT-STATE.md) ;
- régénérer ce PROMPT-REPRISE.md.
