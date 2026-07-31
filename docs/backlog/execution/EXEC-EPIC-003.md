# EXEC-EPIC-003

Statut :
**TERMINÉ**

Date d'ouverture :
2026-07-31

Date de clôture :
2026-07-31

---

## Références

- EPIC-003.md
- ADR-004
- ADR-005
- 70-cycle-vie-equipements.md
- FUNCTIONAL-VISION.md (§3, §9, §11)
- ARCHITECTURE-CONCEPTUELLE.md

---

## Objectif

Suivre l'exécution technique de l'EPIC-003 (assistant de mise en service UX-003).

Ce document complète EPIC-003.md.

Il ne remplace ni la spécification fonctionnelle, ni les ADR.

Hestia Admin = seule couche visible pour la mise en service métier ; jamais HA / Z2M pour les étapes métier.

---

## Prérequis

- **EPIC-002 CLÔTURÉ** — SoT `Equipment`, machine d'états, nom logique, remplacement / reprises (tip `c73a7d5`).
- EPIC-001 livré (observation / sync Agent).

---

## Découpage d'exécution

| Lot | Objet | Features | Statut | Commit (`hestia`) |
|-----|-------|----------|--------|-------------------|
| **A** | Détection et admission | F-016 | **Terminé** | `499e535` |
| **B** | Validation technique | F-013, F-014 | **Terminé** | `22f2bf85ee35069e434022763cb21c2f00748a31` |
| **C** | Mise en service | F-015 | **Terminé** | `ac22e512790701ffc75cd0eb8f8bb0d92d0a02e2` |
| **D** | Appairage | F-017 | **Terminé** | `b93d2096fc3e6c633e3f9c30262f643554b8ef29` |

Dépôts cibles : `hestia` (PWA Admin + API) · `hestia-agent` (exécutions techniques).  
Réutiliser les API EPIC-002 (`/admin/equipment`, transitions, logical-name) — ne pas les redéfinir.

---

### Lot A — Détection et admission

**Objectif**  
Découvrir les équipements détectés, permettre l’admission par l’administrateur, créer la fiche `Equipment` et l’identité SoT (`hestia_device_id`).

**Fonctionnalités couvertes**
- **F-016** — Admission `detected` → `pending_provisioning`
- découverte des équipements détectés ;
- admission par l’administrateur ;
- création de la fiche Equipment ;
- création de l’identité SoT.

**Dépendances** : EPIC-002 (API admission / SoT). Aucun lot EPIC-003 préalable.

**Critères de validation**
- la file des équipements détectés est consultable côté Admin ;
- l’admission crée une fiche Backend avec `hestia_device_id` ;
- l’état initial SoT est `pending_provisioning` ;
- aucune écriture métier hors Backend.

**État** : **Terminé** — commit `499e535`

---

### Lot B — Validation technique

**Objectif**  
Exécuter les contrôles multi-couches et produire un rapport de validation (erreurs et avertissements) avant la saisie métier.

**Fonctionnalités couvertes**
- **F-013** — Checks techniques multi-couches
- **F-014** — Rapport de validation fonctionnelle
- contrôles multi-couches ;
- rapport de validation ;
- synthèse des erreurs et avertissements.

**Dépendances** : Lot A.

**Critères de validation**
- les checks Zigbee / MQTT / découverte HA / entités sont orchestrés via l’Agent ;
- `validation_status` reflète ok / failed ;
- un rapport est affiché avant toute saisie métier ;
- les erreurs et avertissements sont synthétisés de façon lisible.

**État** : **Terminé** — commit `22f2bf85ee35069e434022763cb21c2f00748a31`

---

### Lot C — Mise en service

**Objectif**  
Collecter les informations métier et conduire la fiche jusqu’à l’exploitation en appliquant strictement la machine d’états Module 70.

**Fonctionnalités couvertes**
- **F-015** — Collecte infos métier Admin
- saisie des informations métier ;
- progression `provisioned` → `synced` → `active` ;
- application stricte de la machine d’états Module 70.

**Dépendances** : Lots A, B ; machine d’états EPIC-002 (F-008).

**Critères de validation**
- saisie nom logique, pièce, zone, catégorie, options avec validation Module 70 ;
- persistance Backend (SoT nom logique / métadonnées) ;
- transitions uniquement via la machine d’états documentée ;
- un équipement peut atteindre `active` sans UI HA / Z2M.

**État** : **Terminé** — commit `ac22e512790701ffc75cd0eb8f8bb0d92d0a02e2`

---

### Lot D — Appairage

**Objectif**  
Orchestrer le permit-join via `hestia-agent`, avec une UX entièrement pilotée par Hestia.

**Fonctionnalités couvertes**
- **F-017** — Appairage / permit-join via Agent
- orchestration permit-join ;
- dialogue avec hestia-agent ;
- aucune exposition de Zigbee2MQTT ou Home Assistant dans l’interface ;
- UX entièrement pilotée par Hestia.

**Dépendances** : Lots A–C (parcours de mise en service cohérent).

**Critères de validation**
- l’Admin déclenche l’appairage sans ouvrir l’UI Z2M ni HA ;
- les commandes transitent Admin → Agent → passerelle ;
- le feedback d’état d’appairage est visible dans Hestia ;
- aucune étape métier n’exige l’UI HA ou Z2M (critère de done Epic).

**État** : **Terminé** — commit `b93d2096fc3e6c633e3f9c30262f643554b8ef29`

---

## Règles

- un lot = un objectif ;
- un lot = un commit dédié (petit nombre de commits si nécessaire, jamais de mélange entre lots) ;
- validation indépendante de chaque lot ;
- aucun mélange de développements ;
- ne pas rouvrir ADR-004 / ADR-005 / Module 70 / EPIC-002 ;
- aucune étape métier n’exige l’UI HA ou Z2M.

---

## Journal

### 2026-07-31

Ouverture officielle de l'exécution de l'EPIC-003.

Découpage A→D fixé officiellement (F-013→F-017) :
- A détection / admission ;
- B validation technique ;
- C mise en service ;
- D appairage.

Lots A→D implémentés et poussés sur `hestia` (`499e535` … `b93d209`).

Clôture documentaire officielle — verdict **TERMINÉ**.

---

## Clôture

| Attribut | Valeur |
|----------|--------|
| Verdict | **TERMINÉ** |
| Date | 2026-07-31 |
| Dépôt code | `hestia` tip `b93d209` |
| Features | F-013 → F-017 validées |
| Critères de done | Assistant bout-en-bout via Admin ; aucune étape métier n’exige l’UI HA ou Z2M |
| Parcours | détection → admission → validation → mise en service → appairage |
| Intégration Agent | Orchestration opérationnelle (checks, permit-join) via `hestia-agent` |
| Suite produit | EPIC-004 / EPIC-012 facilités côté dépendances — **ne pas démarrer sans demande explicite** |
