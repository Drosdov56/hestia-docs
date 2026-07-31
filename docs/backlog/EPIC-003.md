# EPIC-003 — Hestia Admin : assistant de mise en service (UX-003)

| Attribut | Valeur |
|----------|--------|
| **Phase** | P2 |
| **Statut** | **CLÔTURÉ** (2026-07-31) |
| **Dépôts** | `hestia` (PWA Admin), `hestia-agent` (exécutions techniques) |
| **Prérequis** | EPIC-002 |
| **Bloque** | Parcours parc complet ; facilite EPIC-012 |
| **Exécution** | [`execution/EXEC-EPIC-003.md`](execution/EXEC-EPIC-003.md) |
| **Tip code** | `b93d209` (`hestia`) |

## Sources

- [ADR-004](../adr/ADR-004-mise-en-service-equipements.md) — Phase 2 complète
- [ADR-005](../adr/ADR-005-cycle-vie-equipements.md)
- [Module 70](../equipements/70-cycle-vie-equipements.md)
- [FUNCTIONAL-VISION](../vision/FUNCTIONAL-VISION.md) §3, §9, §11
- [ARCHITECTURE-CONCEPTUELLE](../modeles/ARCHITECTURE-CONCEPTUELLE.md) — pas de MODELE-EQUIPEMENT

## Objectif

Livrer l’assistant de mise en service : Hestia = seule couche visible pour l’administrateur ; jamais HA/Z2M pour la MS métier.

## Features — implémentées et validées

| Feature | Contenu | Lot | Commit (`hestia`) |
|---------|---------|-----|-------------------|
| **F-016** | Admission `detected` → `pending_provisioning` | A | `499e535` |
| **F-013** | Checks techniques multi-couches | B | `22f2bf85ee35069e434022763cb21c2f00748a31` |
| **F-014** | Rapport de validation fonctionnelle | B | `22f2bf85ee35069e434022763cb21c2f00748a31` |
| **F-015** | Collecte infos métier Admin | C | `ac22e512790701ffc75cd0eb8f8bb0d92d0a02e2` |
| **F-017** | Appairage / permit-join via Agent | D | `b93d2096fc3e6c633e3f9c30262f643554b8ef29` |

### F-013 — Checks techniques multi-couches ✅

- Orchestration checks via Agent
- `validation_status` ok/failed

### F-014 — Rapport de validation fonctionnelle ✅

- Génération / affichage rapport par équipement

### F-015 — Collecte infos métier Admin ✅

- Formulaires Admin
- Validation champs obligatoires (Module 70)
- Persistance Backend

### F-016 — Admission detected → pending_provisioning ✅

- UI file des `detected`
- Appel Backend création Equipment
- Suite jusqu’à `provisioned` → `synced` → `active`

### F-017 — Appairage / permit-join via Agent ✅

- Commandes Agent → passerelle (via `hestia-agent`)
- Feedback état appairage dans Hestia

## Critères de done Epic — atteints

- Un capteur réel peut être mis en service bout-en-bout via Admin.
- Aucune étape métier n’exige l’UI HA ou Z2M.
