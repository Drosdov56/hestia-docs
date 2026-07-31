# EXEC-EPIC-002

Statut :
**TERMINÉ**

Date d'ouverture :
2026-07-31

Date de clôture :
2026-07-31

Verdict :
**TERMINÉ** — lots A→D livrés et validés sur `hestia`.

---

## Références

- EPIC-002.md
- ADR-004
- ADR-005
- 70-cycle-vie-equipements.md

---

## Objectif

Suivre l'exécution technique de l'EPIC-002.

Ce document complète EPIC-002.md.

Il ne remplace ni la spécification fonctionnelle, ni les ADR.

---

## Découpage d'exécution

| Lot | Objet | Features | Statut | SHA (`hestia`) |
|-----|-------|----------|--------|----------------|
| **A** | Fondation `Equipment` + identité + ancre / bindings | F-007, F-009 | **Terminé** | `153dff4` |
| **B** | Machine d'états Module 70 | F-008 | **Terminé** | `b59ed07` |
| **C** | Nom logique SoT + `pending_ops` | F-010 | **Terminé** | `58f5755` |
| **D** | Remplacement deux fiches + reprises | F-011, F-012 | **Terminé** | `c73a7d5` |

Dépôt cible : `hestia` (API / core). Hors périmètre Epic : UI Admin complète (EPIC-003), Installer, logique métier Agent → SoT.

Tip code : `c73a7d5` (Lot D).

---

### Lot A — Fondation `Equipment` — TERMINÉ

**SHA :** `153dff4` — `feat(epic-002): implémenter la fondation SoT des équipements`

Persistance `Equipment`, `hestia_device_id` à l’admission, ancre / bindings, API admin. Tests : `test_equipment_lot_a.sh`.

---

### Lot B — Machine d’états — TERMINÉ

**SHA :** `b59ed07` — `feat(epic-002): implémenter la machine d'états des équipements`

`EquipmentStateMachine` source unique ; transitions via `POST .../transition`. Tests : `test_equipment_lot_b.sh`.

---

### Lot C — Nom logique SoT + `pending_ops` — TERMINÉ

**SHA :** `58f5755` — `feat(epic-002): implémenter le nom logique et les opérations différées`

SoT `logical_name` / `technical_slug` ; file `pending_ops` sans exécution auto. Tests : `test_equipment_lot_c.sh`.

---

### Lot D — Remplacement + reprises — TERMINÉ

**SHA :** `c73a7d5` — `feat(epic-002): implémenter le remplacement et la reprise des équipements`

Wizard deux fiches ; reprises Module 70 §6.8. Tests : `test_equipment_lot_d.sh`.

---

## Règles

- un lot = un objectif ;
- un lot = un commit dédié ;
- validation indépendante de chaque lot ;
- aucun mélange de développements ;
- ne pas rouvrir ADR-004 / ADR-005 / Module 70.

---

## Journal

### 2026-07-31

Ouverture officielle de l'exécution de l'EPIC-002.

Découpage A→D fixé (F-007→F-012).

Lots A→D implémentés et poussés sur `hestia` (`153dff4` … `c73a7d5`).

Clôture documentaire officielle — verdict **TERMINÉ**.

---

## Clôture

| Attribut | Valeur |
|----------|--------|
| Verdict | **TERMINÉ** |
| Date | 2026-07-31 |
| Dépôt code | `hestia` tip `c73a7d5` |
| Features | F-007 → F-012 validées |
| Critères de done | API conforme ADR-005 / Module 70 ; écritures métier Backend uniquement |
| Suite produit | EPIC-003 / EPIC-004 débloqués côté dépendances — **ne pas démarrer sans demande explicite** |
