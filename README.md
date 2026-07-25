# hestia-docs

Documentation de **référence** de l’écosystème Hestia.

**Dépôt officiel :** https://github.com/Drosdov56/hestia-docs

Ce dépôt est la **seule source de vérité** pour la documentation transverse (constitution, vision, modèles conceptuels, carte écosystème, ADR transverses, équipements).

Gouvernance permanente : **[DECISION-0001](docs/gouvernance/DECISION-0001-DOCUMENTATION.md)**.

| Dépôt | Contenu documentaire |
|-------|----------------------|
| **[hestia-docs](https://github.com/Drosdov56/hestia-docs)** (ici) | Documentation commune / conceptuelle |
| [hestia](https://github.com/Drosdov56/hestia) | Documentation **applicative** locale |
| [hestia-installer](https://github.com/Drosdov56/hestia-installer) | Documentation de l’**installeur** |
| [hestia-agent](https://github.com/Drosdov56/hestia-agent) | Documentation de l’**agent** |

## Entrée

→ **[docs/INDEX.md](docs/INDEX.md)**

## Constitution

Le document de réflexion architecturale est la **Constitution** de l’écosystème.  
Aucune décision architecturale, aucun invariant, aucun concept fondateur ne doit y être modifié sans gouvernance explicite.

→ [docs/constitution/](docs/constitution/)

## Niveaux documentaires

| Niveau | Rôle | Exemples |
|--------|------|----------|
| 0 — Constitution | Pourquoi / invariants | Document de réflexion architecturale |
| 1 — Décisions | ADR transverses + DECISION-0001 | ADR-020, ADR-018, ADR-004, ADR-005 |
| 2 — Cartes | Navigation / écosystème | `ecosysteme.md`, `ARCHITECTURE-CONCEPTUELLE.md` |
| 3 — Modèles | Spécialisations conceptuelles | MODELE-* |
| 4 — Vision opérationnelle | Comment ça marche (PoC / phases) | FUNCTIONAL-VISION |
| 5 — Archive | Historique non normatif | `docs/archive/` |

## Glossaire

Une définition officielle par concept : **[docs/gouvernance/GLOSSAIRE.md](docs/gouvernance/GLOSSAIRE.md)**.

## Backlog (pilotage développement)

→ **[docs/backlog/README.md](docs/backlog/README.md)**
