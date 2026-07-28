# Backlog officiel Hestia

**Statut :** Impératif de pilotage du développement  
**Mission :** IMP-000  
**Date :** 2026-07-25  
**SoT documentaire :** [hestia-docs](https://github.com/Drosdov56/hestia-docs) — [DECISION-0001](../gouvernance/DECISION-0001-DOCUMENTATION.md)

Ce dossier est le **plan d’implémentation** dérivé exclusivement de la documentation de référence.  
Aucune Epic sans source documentaire.

**Clôture :** [EPIC-001](EPIC-001.md) = **Livré** (2026-07-25) — [rapport de clôture](execution/REPORT-CLOTURE-EPIC-001.md).

## Entrées

| Document | Rôle |
|----------|------|
| [BACKLOG.md](BACKLOG.md) | Inventaire Epics / Features / User Stories |
| [ROADMAP.md](ROADMAP.md) | Ordre d’implémentation |
| [DEPENDANCES.md](DEPENDANCES.md) | Graphe des dépendances |
| [EPIC-001.md](EPIC-001.md) … [EPIC-014.md](EPIC-014.md) | Détail par Epic |
| [execution/](execution/) | Dossiers d’exécution produit (EPIC clôturées, validations terrain, lots AUTO ouverts) |

## Hiérarchie

```text
Epic → Feature → User Story → Tâches techniques
```

## Prérequis déjà livrés (hors backlog)

Documentés comme **faits** dans FUNCTIONAL-VISION — ne pas réouvrir :

| Élément | Source |
|---------|--------|
| Installer v1.0.0 / L8 | FUNCTIONAL-VISION §3, §9 |
| HA · Mosquitto · Zigbee2MQTT | FUNCTIONAL-VISION §9 |
| INT-001 (HA ↔ MQTT) | FUNCTIONAL-VISION §3 |
| Agent infra V1 | FUNCTIONAL-VISION §9 ; ADR-020 / ADR-018 |

## Règles

1. Toute implémentation démarre depuis une User Story de ce backlog.
2. Les invariants de la Constitution et les ADR gelés ne sont pas renegociables ici.
3. Archive / audits = non sources de nouvelles fonctionnalités.
4. Hors v1 explicitement marqué (EPIC-013) : architecture compatible, pas de développement prématuré (FUNCTIONAL-VISION §7, §14).
