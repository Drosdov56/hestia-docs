Reprise du projet HESTIA.

Avant toute action :

1. Lire intégralement [PROJECT-STATE.md](PROJECT-STATE.md).
2. Considérer ce document comme la référence opérationnelle du projet.
3. Ne pas refaire d’audit global.
4. **Aucun chantier AUTO-002 restant.** Prochain produit possible : **UI-001** — uniquement si explicitement demandé.
5. Signaler uniquement les divergences factuelles éventuelles.

Contexte figé (2026-07-29) :

- **AUTO-002 CLÔTURÉ** (lots A→F) — suivi [`execution/AUTO-002.md`](../backlog/execution/AUTO-002.md).
- **AUTO-002E** validé terrain (2026-07-28 soir) ; **AUTO-002F** (identité) livré F1→F6.
- **ADR-021 Accepté** — `node_id` permanent ; token unique par nœud ; hash-only ; hostname/display_name hors auth ; présence ≠ lifecycle.
- **Token global ingest : supprimé définitivement** (F6) — ne pas le réintroduire.
- **ADR-023 Accepté** — `hestia-ws-relay` = transport ; aucune logique métier / identité.
- SHA applicatifs de clôture : hestia `24945e2` · agent `a749a3f` · installer `0b7d002`.
- Lire la section **INFRASTRUCTURE** de PROJECT-STATE.md avant toute action SSH/déploiement : VPS = `/var/www/hestia` ; nœud = `/opt/hestia` ; hostname `hestia` = mini-PC, pas le VPS.
- SW PWA courant : `hestia-v0.8.27`.
- Epic suivante notée : **UI-001** — ne pas démarrer sans demande.

À la fin de la session :

- mettre à jour [PROJECT-STATE.md](PROJECT-STATE.md) ;
- régénérer ce PROMPT-REPRISE.md.
