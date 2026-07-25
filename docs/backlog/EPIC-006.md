# EPIC-006 — Pipeline information & mémoire

| Attribut | Valeur |
|----------|--------|
| **Phase** | P5 |
| **Statut** | À faire |
| **Dépôts** | `hestia` (Serveur) |
| **Prérequis** | EPIC-005 |
| **Bloque** | EPIC-007, EPIC-010, EPIC-013 |

## Sources

- [MODELE-INFORMATION](../modeles/MODELE-INFORMATION.md)
- [Constitution](../constitution/HESTIA%20-%20DOCUMENT%20DE%20RÉFLEXION%20ARCHITECTURALE.md) — Décisions n°3, n°4, n°5 ; cycle cognitif
- [Glossaire](../gouvernance/GLOSSAIRE.md)
- [ARCHITECTURE-CONCEPTUELLE](../modeles/ARCHITECTURE-CONCEPTUELLE.md)

## Objectif

Généraliser la transformation Observation → information utile : sélection, typologie, identité d’information, mémoire utile vs technique.

## Features

### F-022 — Sélection / typologie d’informations

**User Stories**

- US-006.1 En tant que Serveur, je classe les informations selon la typologie du modèle.

**Tâches techniques**

- Implémenter typologie MODELE-INFORMATION
- Pipeline Capteur→…→Information utile généralisé

### F-023 — Identité d’une information

**User Stories**

- US-006.2 En tant que système, chaque information utile a une identité stable (Constitution Décision n°5).

**Tâches techniques**

- Fiche d’identité d’information
- Traçabilité amont (sources / équipements)

### F-024 — Mémoire utile (sélection / durée)

**User Stories**

- US-006.3 En tant que Serveur, je ne mémorise que ce qui mérite de l’être, avec durée adaptée.

**Tâches techniques**

- Politiques de rétention « mémoire utile »
- Purge / archivage

### F-025 — Mémoire technique vs utile

**User Stories**

- US-006.4 En tant que système, je distingue mémoire technique (télémétrie) et mémoire utile (sens).

**Tâches techniques**

- Deux stockages / politiques (Constitution Décision n°4)
- Pas de confusion dans les API Apps

## Critères de done Epic

- Plusieurs types d’événements produisent des informations typées.
- Politique de mémoire documentée et appliquée.
