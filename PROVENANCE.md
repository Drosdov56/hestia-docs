# Provenance Git — migration vers hestia-docs

> **Document historique de migration.**  
> Il décrit la provenance initiale des fichiers au moment de la création de `hestia-docs` ; il ne définit pas l’état documentaire courant.

Les fichiers de ce dépôt ont été **copiés** depuis les dépôts sources à la date de migration.
L’historique complet de chaque fichier reste consultable dans le dépôt d’origine (`git log --follow -- <chemin>`).

## Commits sources (HEAD au moment de la copie)

| Dépôt | HEAD |
|-------|------|
| hestia | `5dc635f17242f72874f40b0a6c6b1b70d5a8db2d` |
| hestia-installer | `e865b3e66d8342e20cd8507f9d80febefff35643` |
| hestia-agent | `02245f2c176f9e3dee2e5e67425fbba7f322e5ea` |

## Correspondance des chemins

| Cible hestia-docs | Origine |
|-------------------|---------|
| `docs/constitution/HESTIA - DOCUMENT DE RÉFLEXION ARCHITECTURALE.md` | `hestia/docs/…` |
| `docs/ecosysteme/ecosysteme.md` | `hestia/docs/ecosysteme.md` |
| `docs/architecture/architecture-domotique.md` | `hestia/docs/produit/architecture-domotique.md` |
| `docs/adr/ADR-020…` / `ADR-018…` | `hestia/docs/adr/` |
| `docs/archive/*` | `hestia/docs/produit/` |
| `docs/audits/*` | `hestia/docs/reports/` |
| `docs/vision/FUNCTIONAL-VISION.md` | `hestia-installer/docs/FUNCTIONAL-VISION.md` |
| `docs/modeles/*` | `hestia-installer/docs/conception/` |
| `docs/equipements/70-cycle-vie-equipements.md` | `hestia-installer/docs/conception/` |
| `docs/adr/ADR-004…` / `ADR-005…` | `hestia-installer/docs/ADR/` |

Dans les dépôts d’origine, les chemins migrés sont remplacés par des **stubs de renvoi** (le contenu intégral reste dans l’historique Git local).
