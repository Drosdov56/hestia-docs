# EPIC-012 — Référentiels capteurs

| Attribut | Valeur |
|----------|--------|
| **Phase** | P8 |
| **Statut** | À faire |
| **Dépôts** | `hestia-docs` (référentiels), `hestia` / `hestia-agent` (consommation) |
| **Prérequis** | EPIC-003 ou EPIC-004 |

## Sources

- [MODELE-CAPTEUR](../modeles/capteurs/MODELE-CAPTEUR.md)
- [SNZB-06P24](../modeles/capteurs/SNZB-06P24.md)
- [architecture-domotique.md](../architecture/architecture-domotique.md) §3
- [FUNCTIONAL-VISION](../vision/FUNCTIONAL-VISION.md) §15
- [Module 70](../equipements/70-cycle-vie-equipements.md) — distinct du référentiel doc

## Objectif

Documenter et exploiter les capacités matérielles selon le contrat MODELE-CAPTEUR, en sélectionnant ce qui entre dans le pipeline information.

## Features

### F-041 — Contrat documentaire MODELE-CAPTEUR

**User Stories**

- US-012.1 En tant qu’équipe, tout nouveau capteur a une fiche conforme au modèle.

**Tâches techniques**

- Checklist / template de fiche
- Revue conformité MODELE-CAPTEUR

### F-042 — Exploitation SNZB-06P24

**User Stories**

- US-012.2 En tant que produit, j’exploite les capacités du SNZB pertinentes pour Hestia (présence, etc.).
- US-012.3 En tant qu’équipe, je trace capacités matérielles vs capacités retenues.

**Tâches techniques**

- Cartographie capacités SNZB → signaux retenus
- Intégration PoC / production
- Mise à jour fiche selon terrain

## Critères de done Epic

- Fiche SNZB à jour et consommée par le pipeline.
- Processus clair pour le prochain capteur (ex. ouverture).
