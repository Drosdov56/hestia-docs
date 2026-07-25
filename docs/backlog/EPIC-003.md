# EPIC-003 — Hestia Admin : assistant de mise en service (UX-003)

| Attribut | Valeur |
|----------|--------|
| **Phase** | P2 |
| **Statut** | À faire |
| **Dépôts** | `hestia` (PWA Admin), `hestia-agent` (exécutions techniques) |
| **Prérequis** | EPIC-002 |
| **Bloque** | Parcours parc complet ; facilite EPIC-012 |

## Sources

- [ADR-004](../adr/ADR-004-mise-en-service-equipements.md) — Phase 2 complète
- [ADR-005](../adr/ADR-005-cycle-vie-equipements.md)
- [Module 70](../equipements/70-cycle-vie-equipements.md)
- [FUNCTIONAL-VISION](../vision/FUNCTIONAL-VISION.md) §3, §9, §11
- [ARCHITECTURE-CONCEPTUELLE](../modeles/ARCHITECTURE-CONCEPTUELLE.md) — pas de MODELE-EQUIPEMENT

## Objectif

Livrer l’assistant de mise en service : Hestia = seule couche visible pour l’administrateur ; jamais HA/Z2M pour la MS métier.

## Features

### F-013 — Checks techniques multi-couches

**User Stories**

- US-003.1 En tant qu’Admin, je vois la joignabilité Zigbee / MQTT / découverte HA / entités.

**Tâches techniques**

- Orchestration checks via Agent
- `validation_status` ok/failed

### F-014 — Rapport de validation fonctionnelle

**User Stories**

- US-003.2 En tant qu’Admin, j’obtiens un rapport avant toute saisie métier.

**Tâches techniques**

- Génération / affichage rapport par équipement

### F-015 — Collecte infos métier Admin

**User Stories**

- US-003.3 En tant qu’Admin, je saisis nom logique, pièce, zone, catégorie, options.

**Tâches techniques**

- Formulaires Admin
- Validation champs obligatoires (Module 70)
- Persistance Backend

### F-016 — Admission detected → pending_provisioning

**User Stories**

- US-003.4 En tant qu’Admin, j’admets un équipement détecté et crée la fiche + `hestia_device_id`.

**Tâches techniques**

- UI file des `detected`
- Appel Backend création Equipment
- Suite jusqu’à `provisioned` → `synced` → `active`

### F-017 — Appairage / permit-join via Agent

**User Stories**

- US-003.5 En tant qu’Admin, je déclenche l’appairage sans ouvrir l’UI Z2M.

**Tâches techniques**

- Commandes Agent → Z2M
- Feedback état appairage

## Critères de done Epic

- Un capteur réel peut être mis en service bout-en-bout via Admin.
- Aucune étape métier n’exige l’UI HA ou Z2M.
