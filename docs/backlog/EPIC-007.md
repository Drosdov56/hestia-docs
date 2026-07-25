# EPIC-007 — Décision métier

| Attribut | Valeur |
|----------|--------|
| **Phase** | P6 |
| **Statut** | À faire |
| **Dépôts** | `hestia` (Serveur) |
| **Prérequis** | EPIC-006 |
| **Bloque** | EPIC-010, EPIC-013 |

## Sources

- [MODELE-DECISION](../modeles/MODELE-DECISION.md)
- [Constitution](../constitution/HESTIA%20-%20DOCUMENT%20DE%20RÉFLEXION%20ARCHITECTURALE.md) — Décision n°9 (trois natures) ; cycle Décision/Action
- [ADR-020](../adr/ADR-020%20-%20Positionnement%20de%20Hestia%20vis-%C3%A0-vis%20de%20Home%20Assistant.md) — Serveur = signification
- [FUNCTIONAL-VISION](../vision/FUNCTIONAL-VISION.md) §6 — IA consomme, ne précède pas

## Objectif

Transformer des informations utiles en conclusions, alertes ou recommandations **explicables**, indépendamment du moteur (règles / IA / hybride).

## Features

### F-026 — Chaîne contexte → corrélation → décision

**User Stories**

- US-007.1 En tant que Serveur, je décide à partir d’informations utiles contextualisées, jamais de signaux bruts.

**Tâches techniques**

- Pipeline MODELE-DECISION
- Entrées = sorties MODELE-INFORMATION uniquement

### F-027 — Trois natures de décisions

**User Stories**

- US-007.2 En tant que système, je distingue décisions immédiates, contextualisées, et tournées vers le futur.

**Tâches techniques**

- Modèle de natures (Constitution)
- Routage / priorisation

### F-028 — Explicabilité

**User Stories**

- US-007.3 En tant qu’humain, je comprends pourquoi Hestia a conclu X.

**Tâches techniques**

- Structure d’explication (preuves, confiance)
- Affichage Hub / notification

## Critères de done Epic

- Au moins un scénario de décision explicable en production PoC+.
- Aucune décision métier dans l’Agent ou les Apps.
