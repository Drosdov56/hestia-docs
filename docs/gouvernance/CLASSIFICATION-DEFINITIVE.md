# Classification documentaire définitive — Phase 1

**Date :** 2026-07-25  
**Sources :** `ecosystem-documentation-audit.md`, `architecture-rationale-audit.md`  
**Statut :** Définitive pour la migration vers `hestia-docs`

Les audits existants sont **suffisants** : cartographie des 3 dépôts, recouvrements, divergences terminologiques, historiques. Complément ici : **affectation cible** (commune / locale / historique).

---

## 1. Documentation commune → `hestia-docs`

| Document source | Dépôt d’origine | Cible `hestia-docs` | Rôle |
|-----------------|-----------------|---------------------|------|
| `HESTIA - DOCUMENT DE RÉFLEXION ARCHITECTURALE.md` | hestia | `docs/constitution/` | **Constitution** — SoT conceptuelle unique |
| `ecosysteme.md` | hestia | `docs/ecosysteme/` | Carte multi-dépôts |
| `FUNCTIONAL-VISION.md` | installer | `docs/vision/` | Vision « comment ça marche » (dérivée) |
| `ARCHITECTURE-CONCEPTUELLE.md` | installer | `docs/modeles/` | Carte des modèles |
| `MODELE-INFORMATION.md` | installer | `docs/modeles/` | Spécialisation information |
| `MODELE-DECISION.md` | installer | `docs/modeles/` | Spécialisation décision |
| `MODELE-IDENTITE.md` | installer | `docs/modeles/` | Spécialisation identité personne |
| `MODELE-HABITAT.md` | installer | `docs/modeles/` | Spécialisation habitat |
| `MODELE-FOYER.md` | installer | `docs/modeles/` | Spécialisation foyer |
| `MODELE-CAPTEUR.md` | installer | `docs/modeles/capteurs/` | Contrat doc capteur |
| `SNZB-06P24.md` | installer | `docs/modeles/capteurs/` | Référentiel matériel |
| `70-cycle-vie-equipements.md` | installer | `docs/equipements/` | SoT équipement |
| `architecture-domotique.md` | hestia | `docs/architecture/` | Flux / comportement passerelle |
| `ADR-020` (HA) | hestia | `docs/adr/` | ADR écosystème |
| `ADR-018` (agent passerelle) | hestia | `docs/adr/` | ADR écosystème |
| `ADR-004` / `ADR-005` | installer | `docs/adr/` | ADR équipements (écosystème) |
| Audits documentaires | hestia | `docs/audits/` | Preuves d’audit |

---

## 2. Documentation locale (reste dans le dépôt)

### `hestia`

- `architecture.md`, `CONTEXTE-PROJET.md`, `getting-started.md`, `deployment.md`
- ADR produit locaux : 014, 015, 016, 017, 019
- `produit/` : specs UI, notifications, catalogue, backlog, registre-adr, README
- Stubs de renvoi pour les documents migrés

### `hestia-installer`

- `ARCHITECTURE.md`, `INSTALL.md`, `ROADMAP.md`, `BACKLOG.md`, `INDEX.md`, `WORKFLOW.md`, `CHANGELOG.md`, `ETAT-PROJET.md`
- Plans L4/L5/L6, `PLAN-FINALISATION-V1.md`
- ADR installateur : 001, 002, 003, 006, 007, 008
- `implementation/`, `audit/`, `releases/`, `modules/`
- Conception locale restante (ex. `50-mqtt.md`, `INT-001-…`)
- Stubs de renvoi pour les documents migrés

### `hestia-agent`

- `README.md`, `docs/ARCHITECTURE.md` (contrat V1) — avec renvoi constitution

---

## 3. Documentation historique → `hestia-docs/docs/archive/`

| Document | Origine | Traitement |
|----------|---------|------------|
| `brief-demarrage-hub-familial.md` | hestia | Archivé (non normatif) |
| `architecture-reference-v1.md` | hestia | Archivé (partiellement obsolète) |

Les plans / rapports clôturés Installer restent **locaux** (preuves d’implémentation), hors constitution.

---

## 4. Règles de gouvernance (rappel)

1. **Une seule constitution :** le document de réflexion architecturale.  
2. **Une seule définition officielle par concept** — voir `GLOSSAIRE.md`.  
3. Les MODELE-* et la vision fonctionnelle sont des **dérivés / spécialisations**, pas des secondes fondations.  
4. Ne jamais supprimer ADR, historique technique, ni invariants de la constitution.
