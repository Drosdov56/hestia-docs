# EPIC-014 — Multi-logement / multi-nœud (hors v1)

| Attribut | Valeur |
|----------|--------|
| **Phase** | Hors v1 |
| **Statut** | Hors v1 — architecture compatible seulement |
| **Dépôts** | futurs |
| **Prérequis** | EPIC-002, EPIC-011 ; décision produit explicite |

## Sources

- [FUNCTIONAL-VISION](../vision/FUNCTIONAL-VISION.md) §7, §14 — anticiper sans développer prématurément
- [architecture-domotique.md](../architecture/architecture-domotique.md) §9, §12 — un foyer / un nœud / un VPS en v1
- [Module 70](../equipements/70-cycle-vie-equipements.md) §11 — multi-* hors périmètre v1
- [ADR-005](../adr/ADR-005-cycle-vie-equipements.md) — v1 mono-nœud
- [ecosysteme.md](../ecosysteme/ecosysteme.md)

## Objectif

Conserver la **compatibilité architecturale** avec plusieurs logements / mini-PC / familles, **sans** implémenter en v1.

## Features

### F-045 — Identifiants multi-nœud / multi-logement

**User Stories**

- US-014.1 (futur) En tant que plateforme, chaque mini-PC a un identifiant unique et une config téléchargée depuis le VPS.

**Tâches techniques (non v1)**

- Introduire `home_id` / multi-node **uniquement** après décision produit
- Ne pas casser le modèle mono-nœud actuel
- ADR dédiée obligatoire avant développement

## Critères de done Epic (quand activé)

- ADR multi-logement acceptée.
- Migration depuis v1 mono-nœud planifiée.

## Interdiction v1

Aucun développement de cette Epic tant que le PoC et le parcours Admin mono-foyer ne sont pas validés (FUNCTIONAL-VISION §7).
