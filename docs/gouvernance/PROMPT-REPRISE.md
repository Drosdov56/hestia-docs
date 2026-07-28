Reprise du projet HESTIA.

Avant toute action :

1. Lire intégralement [PROJECT-STATE.md](PROJECT-STATE.md).
2. Considérer ce document comme la référence opérationnelle du projet.
3. Ne pas refaire d'audit global.
4. Reprendre uniquement le chantier demandé : **prochaine étape produit = AUTO-002F** (non démarré tant que non demandé explicitement).
5. Signaler uniquement les divergences factuelles éventuelles.

Contexte figé (2026-07-28 soir) :

- **AUTO-002E clôturé et validé terrain** (parc, heartbeat, fiche, relay, appariement, terminal, reconnexion, cycle de vie sessions).
- AUTO-002A → AUTO-002E livrés ; ADR-023 accepté.
- `hestia-ws-relay` actif sur le VPS (WSS same-origin via Apache).
- Ne pas remettre de logique métier dans le relay.
- Lire la section **INFRASTRUCTURE** de PROJECT-STATE.md avant toute action SSH/déploiement : VPS = `/var/www/hestia` ; nœud = `/opt/hestia` ; hostname `hestia` = mini-PC, pas le VPS.
- SHA de référence : hestia `6a451f9` · agent `cc9e315` · installer `c7460ba` · docs `339ae0d`.
- SW PWA courant : `hestia-v0.8.27`.
- Epic future notée seulement : **UI-001** (ergonomie admin nœuds) — ne pas démarrer sans demande.

À la fin de la session :

- mettre à jour [PROJECT-STATE.md](PROJECT-STATE.md) ;
- régénérer ce PROMPT-REPRISE.md.
