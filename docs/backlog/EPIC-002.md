# EPIC-002 — Backend : SoT équipements (Module 70)

| Attribut | Valeur |
|----------|--------|
| **Phase** | P1 |
| **Statut** | **CLÔTURÉ** (2026-07-31) |
| **Dépôts** | `hestia` (core / API) |
| **Prérequis** | EPIC-001 (contrat sync minimal) |
| **Bloque** | EPIC-003, EPIC-004, EPIC-008 |
| **Exécution** | [`execution/EXEC-EPIC-002.md`](execution/EXEC-EPIC-002.md) |

## Sources

- [70-cycle-vie-equipements.md](../equipements/70-cycle-vie-equipements.md) — **SoT normative**
- [ADR-005](../adr/ADR-005-cycle-vie-equipements.md)
- [ADR-004](../adr/ADR-004-mise-en-service-equipements.md)
- [Glossaire](../gouvernance/GLOSSAIRE.md) — `hestia_device_id`
- [architecture-domotique.md](../architecture/architecture-domotique.md) §7.1

## Objectif

Implémenter côté Serveur Hestia la **source de vérité** du parc d’équipements : identité, états, métadonnées, bindings, remplacements, reprises.

## Features — implémentées et validées

| Feature | Contenu | Lot | Commit (`hestia`) |
|---------|---------|-----|-------------------|
| **F-007** | Entité `Equipment` + `hestia_device_id` | A | `153dff4` |
| **F-009** | Ancre physique & `protocol_bindings` / `ha_bindings` | A | `153dff4` |
| **F-008** | Machine d’états Module 70 | B | `b59ed07` |
| **F-010** | Nom logique SoT + `pending_ops` | C | `58f5755` |
| **F-011** | Remplacement deux fiches | D | `c73a7d5` |
| **F-012** | Reprises §6.8 | D | `c73a7d5` |

### F-007 — Entité `Equipment` + `hestia_device_id` ✅

- Modèle de données `Equipment`
- Création à `detected` → `pending_provisioning`
- API CRUD contrainte (écritures métier)

### F-008 — Machine d’états Module 70 ✅

- États normatifs + attributs transverses
- Validation des transitions interdites

### F-009 — Ancre physique & protocol_bindings ✅

- `physical_anchor`, `protocol`, `protocol_bindings`, `ha_bindings`
- Gestion doublons d’ancre (§6.6)

### F-010 — Nom logique SoT Backend + propagation ✅

- SoT nom logique
- File `pending_ops` (sans exécution automatique Agent)
- Option admin `align_entity_ids` (v1)

### F-011 — Remplacement deux fiches ✅

- `predecessor_id` / `successor_id`
- Interdiction de réutiliser `hestia_device_id`

### F-012 — Reprises ✅

- Comportements normatifs Module 70 §6.8
- Tests API de non-régression

## Critères de done Epic — atteints

- API Backend conforme ADR-005 / Module 70.
- Aucune écriture métier directe Agent → SoT sans Backend.
