# EPIC-013 — Intelligence artificielle transversale

| Attribut | Valeur |
|----------|--------|
| **Phase** | P10 |
| **Statut** | À faire (après PoC) |
| **Dépôts** | `hestia` (Serveur) ; ADR IA à créer dans hestia-docs |
| **Prérequis** | EPIC-006, EPIC-007 ; formalisation ADR |

## Sources

- [FUNCTIONAL-VISION](../vision/FUNCTIONAL-VISION.md) §6
- [Constitution](../constitution/HESTIA%20-%20DOCUMENT%20DE%20RÉFLEXION%20ARCHITECTURALE.md) — Décision n°8 (deux niveaux d’IA)
- [MODELE-INFORMATION](../modeles/MODELE-INFORMATION.md) — IA ne définit pas le modèle
- [MODELE-DECISION](../modeles/MODELE-DECISION.md) — IA possible comme moteur, pas comme SoT conceptuelle
- [ADR-020](../adr/ADR-020%20-%20Positionnement%20de%20Hestia%20vis-%C3%A0-vis%20de%20Home%20Assistant.md) — futures capacités IA côté Serveur
- [DECISION-0001](../gouvernance/DECISION-0001-DOCUMENTATION.md) — ADR transverse dans hestia-docs

## Objectif

Introduire l’IA comme **capacité transversale** (perception vs compréhension), sans module isolé et sans précéder la preuve de valeur.

## Features

### F-043 — IA perception vs compréhension

**User Stories**

- US-013.1 En tant que système, je distingue aides à la perception et aides à la compréhension (Constitution).

**Tâches techniques**

- Rédiger ADR IA (hestia-docs)
- Cartographier points d’insertion dans le cycle cognitif

### F-044 — Enrichissement progressif des modules

**User Stories**

- US-013.2 En tant que produit, l’IA enrichit anomalies, corrélation, aide à la décision, explications — progressivement.

**Tâches techniques**

- Premiers cas d’usage post-PoC
- Garde-fous : explicabilité, pas de SoT concurrente aux modèles

## Critères de done Epic

- ADR IA acceptée dans hestia-docs.
- Au moins un enrichissement non bloquant pour le PoC historique.
