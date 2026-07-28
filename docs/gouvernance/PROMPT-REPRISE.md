Reprise du projet HESTIA.

Avant toute action :

1. Lire intégralement [PROJECT-STATE.md](PROJECT-STATE.md).
2. Considérer ce document comme la référence opérationnelle du projet.
3. Ne pas refaire d'audit global.
4. Reprendre directement le chantier actif (section « État global ») : **AUTO-002F**.
5. Signaler uniquement les divergences factuelles éventuelles.

Contexte figé :

- AUTO-002A → AUTO-002E livrés ; ADR-023 accepté.
- `hestia-ws-relay` actif sur le VPS (WSS same-origin via Apache).
- Ne pas remettre de logique métier dans le relay.
- Lire la section **INFRASTRUCTURE** de PROJECT-STATE.md avant toute action SSH/déploiement : VPS = `/var/www/hestia` ; nœud = `/opt/hestia` ; hostname `hestia` = mini-PC, pas le VPS.

À la fin de la session :

- mettre à jour [PROJECT-STATE.md](PROJECT-STATE.md) ;
- régénérer ce PROMPT-REPRISE.md.
