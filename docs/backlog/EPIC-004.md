# EPIC-004 — PoC : événement → information utile

| Attribut | Valeur |
|----------|--------|
| **Phase** | P3 |
| **Statut** | À faire |
| **Dépôts** | `hestia-agent`, `hestia` |
| **Prérequis** | EPIC-001, EPIC-002 (équipement connu) |
| **Bloque** | EPIC-005, EPIC-006 |

## Sources

- [FUNCTIONAL-VISION](../vision/FUNCTIONAL-VISION.md) §3, §12, §15 — **cible PoC**
- [MODELE-INFORMATION](../modeles/MODELE-INFORMATION.md)
- [Constitution](../constitution/HESTIA%20-%20DOCUMENT%20DE%20RÉFLEXION%20ARCHITECTURALE.md) — cycle Observation→… ; information utile
- [Glossaire](../gouvernance/GLOSSAIRE.md) — mapping cycle / information utile
- [architecture-domotique.md](../architecture/architecture-domotique.md) §11
- [SNZB-06P24](../modeles/capteurs/SNZB-06P24.md) — scénario matériel de référence

## Objectif

Démontrer le **bénéfice utilisateur** : un événement réel devient **une** information claire pour un humain (notification optionnelle). Pas une démo d’administration.

## Features

### F-018 — Chaîne minimale Capteur → Hub (1 type)

**User Stories**

- US-004.1 En tant qu’utilisateur, je vois le résultat d’un événement de présence (SNZB) dans Hestia.

**Tâches techniques**

- Brancher un seul type d’événement de bout en bout
- Chemin Agent → Backend → surface Hub minimale

### F-019 — Formulation d’une information utile

**User Stories**

- US-004.2 En tant qu’humain, je comprends l’information sans jargon technique HA.

**Tâches techniques**

- Gabarit d’information utile (texte / structure minimale)
- Pas de moteur décisionnel complexe (reporté EPIC-007)
- Pas d’IA requise (FUNCTIONAL-VISION §6)

## Critères de done Epic

- Scénario PoC §15 reproductible sur nœud L8.
- Valeur utilisateur validée avant généralisation Admin multi-équipements / IA.
