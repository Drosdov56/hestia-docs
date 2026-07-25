# EPIC-008 — Habitat & Foyer

| Attribut | Valeur |
|----------|--------|
| **Phase** | P7 |
| **Statut** | À faire |
| **Dépôts** | `hestia` (Serveur, Admin) |
| **Prérequis** | EPIC-002 |
| **Bloque** | EPIC-009 ; enrichit EPIC-003 (pièce/zone) |

## Sources

- [MODELE-HABITAT](../modeles/MODELE-HABITAT.md)
- [MODELE-FOYER](../modeles/MODELE-FOYER.md)
- [ARCHITECTURE-CONCEPTUELLE](../modeles/ARCHITECTURE-CONCEPTUELLE.md)
- [Module 70](../equipements/70-cycle-vie-equipements.md) — localisation ≠ définition équipement
- [ADR-004](../adr/ADR-004-mise-en-service-equipements.md) — collecte pièce / zone

## Objectif

Représenter l’environnement physique et le foyer indépendamment des capteurs et des technologies.

## Features

### F-029 — Structure habitat

**User Stories**

- US-008.1 En tant qu’Admin, je déclare habitation / niveaux / pièces / zones / ouvertures.

**Tâches techniques**

- Modèle de données habitat
- API + UI Admin structure

### F-030 — Localisation des équipements

**User Stories**

- US-008.2 En tant qu’Admin, je place un équipement dans une pièce sans redéfinir l’équipement.

**Tâches techniques**

- Lien Equipment → nœud spatial
- Indépendance au remplacement matériel (MODELE-HABITAT)

### F-031 — Personnes, rôles, relations du foyer

**User Stories**

- US-008.3 En tant qu’Admin, je déclare les membres du foyer, rôles et relations.
- US-008.4 En tant que système, le foyer survit à un déménagement d’habitat.

**Tâches techniques**

- Modèle foyer (personnes, animaux, rôles, relations)
- API + UI Admin

## Critères de done Epic

- Habitat et foyer persistés sans aucun capteur.
- MS équipement peut référencer une pièce valide.
