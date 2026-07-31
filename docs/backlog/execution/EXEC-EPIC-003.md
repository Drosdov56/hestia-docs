# EXEC-EPIC-003

Statut :
OUVERT

Date d'ouverture :
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

| Lot | Objet | Features | Statut |
|-----|-------|----------|--------|
| **A** | File `detected` + admission Admin | F-016 | À faire |
| **B** | Checks techniques multi-couches + rapport | F-013, F-014 | À faire |
| **C** | Saisie métier + chemin jusqu'à `active` | F-015 | À faire |
| **D** | Appairage / permit-join via Agent | F-017 | À faire |

Dépôts cibles : `hestia` (PWA Admin + API) · `hestia-agent` (exécutions techniques).  
Réutiliser les API EPIC-002 (`/admin/equipment`, transitions, logical-name) — ne pas les redéfinir.

---

### Lot A — Admission (F-016)

**Objectif**  
Permettre à l’Admin d’admettre un équipement détecté et de créer la fiche Backend + `hestia_device_id`.

**Périmètre**
- UI file des observations `detected` (buffer Agent / inventaire) ;
- appel Backend d’admission (API EPIC-002) ;
- affichage fiche en `pending_provisioning`.

**Hors lot A** : checks UX-003 complets ; formulaires métier ; permit-join.

**Dépendances** : EPIC-002.

---

### Lot B — Checks + rapport (F-013, F-014)

**Objectif**  
Orchestrer les checks multi-couches (Zigbee / MQTT / découverte HA / entités) et produire un rapport avant saisie métier.

**Périmètre**
- orchestration checks via Agent ;
- mise à jour `validation_status` (ok / failed) ;
- génération / affichage du rapport de validation fonctionnelle.

**Hors lot B** : saisie nom / pièce / zone ; permit-join.

**Dépendances** : Lot A.

---

### Lot C — Infos métier + mise en service (F-015)

**Objectif**  
Collecter les infos métier Admin et conduire la fiche jusqu’à l’exploitation (`provisioned` → `synced` → `active`) via la machine d’états EPIC-002.

**Périmètre**
- formulaires : nom logique, pièce, zone, catégorie, options ;
- validation champs obligatoires (Module 70) ;
- persistance Backend (logical-name / métadonnées) ;
- transitions métier jusqu’à `active` (API existantes).

**Hors lot C** : appairage radio / permit-join.

**Dépendances** : Lots A, B.

---

### Lot D — Appairage via Agent (F-017)

**Objectif**  
Déclencher l’appairage (permit-join) sans ouvrir l’UI Z2M.

**Périmètre**
- commandes Admin → Agent → Z2M ;
- feedback d’état d’appairage ;
- intégration dans le parcours de mise en service.

**Hors lot D** : UI Z2M / HA ; refactor global hors UX-003.

**Dépendances** : Lots A–C (parcours MS cohérent).

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

Découpage A→D proposé (F-013→F-017).

Aucun développement réalisé.

---

## Clôture

À compléter lors de la fin de l'EPIC.
