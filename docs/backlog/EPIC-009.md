# EPIC-009 — Identité des personnes

| Attribut | Valeur |
|----------|--------|
| **Phase** | P7 |
| **Statut** | À faire |
| **Dépôts** | `hestia` (Serveur) |
| **Prérequis** | EPIC-008 |
| **Bloque** | Scénarios EPIC-010 (intrusion / identification) |

## Sources

- [MODELE-IDENTITE](../modeles/MODELE-IDENTITE.md)
- [Glossaire](../gouvernance/GLOSSAIRE.md) — identité personne ≠ équipement
- [ARCHITECTURE-CONCEPTUELLE](../modeles/ARCHITECTURE-CONCEPTUELLE.md)
- [MODELE-DECISION](../modeles/MODELE-DECISION.md) — confiance / explicabilité
- [architecture-domotique.md](../architecture/architecture-domotique.md) §4 — « membre de la famille identifié »

## Objectif

Représenter les personnes et établir des hypothèses / conclusions d’identification à partir de preuves — sans confondre détection et identité.

## Features

### F-032 — Preuves ≠ identité ≠ décision

**User Stories**

- US-009.1 En tant que système, une détection n’est jamais une identité.
- US-009.2 En tant que Serveur, je stocke preuves, hypothèses et conclusions séparément.

**Tâches techniques**

- Modèle données identité personne
- Lien foyer (EPIC-008)

### F-033 — Fusion / confiance d’identification

**User Stories**

- US-009.3 En tant que système, je fusionne des preuves avec un niveau de confiance explicite.

**Tâches techniques**

- Mécanisme de confiance
- Sorties consommables par MODELE-DECISION / INFORMATION

## Critères de done Epic

- Aucune confusion `hestia_device_id` / identité personne.
- Au moins un chemin preuve → conclusion documenté.
