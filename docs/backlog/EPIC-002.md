# EPIC-002 — Backend : SoT équipements (Module 70)

| Attribut | Valeur |
|----------|--------|
| **Phase** | P1 |
| **Statut** | À faire |
| **Dépôts** | `hestia` (core / API) |
| **Prérequis** | EPIC-001 (contrat sync minimal) |
| **Bloque** | EPIC-003, EPIC-004, EPIC-008 |

## Sources

- [70-cycle-vie-equipements.md](../equipements/70-cycle-vie-equipements.md) — **SoT normative**
- [ADR-005](../adr/ADR-005-cycle-vie-equipements.md)
- [ADR-004](../adr/ADR-004-mise-en-service-equipements.md)
- [Glossaire](../gouvernance/GLOSSAIRE.md) — `hestia_device_id`
- [architecture-domotique.md](../architecture/architecture-domotique.md) §7.1

## Objectif

Implémenter côté Serveur Hestia la **source de vérité** du parc d’équipements : identité, états, métadonnées, bindings, remplacements, reprises.

## Features

### F-007 — Entité `Equipment` + `hestia_device_id`

**User Stories**

- US-002.1 En tant que Backend, j’attribue un `hestia_device_id` immuable à l’admission.
- US-002.2 En tant que système, je n’attribue jamais d’id à l’état `detected` seul.

**Tâches techniques**

- Modèle de données `Equipment`
- Création à `detected` → `pending_provisioning`
- API CRUD contrainte (écritures métier)

### F-008 — Machine d’états Module 70

**User Stories**

- US-002.3 En tant que Backend, je n’autorise que les transitions documentées.
- US-002.4 En tant qu’Admin/Agent, je vois `validation_status`, `sync_status`, erreurs.

**Tâches techniques**

- États : detected, pending_provisioning, provisioned, synced, active, offline, replaced, deleted, …
- Attributs transverses
- Validation des transitions interdites

### F-009 — Ancre physique & protocol_bindings

**User Stories**

- US-002.5 En tant que Backend, je corrèle par ancre physique sans en faire la clé métier.
- US-002.6 En tant que système, j’accepte `protocol` + bindings sans champ Z2M obligatoire au cœur.

**Tâches techniques**

- Champs `physical_anchor`, `protocol`, `protocol_bindings`, `ha_bindings`
- Gestion doublons d’ancre (§6.6 Module 70)

### F-010 — Nom logique SoT Backend + propagation

**User Stories**

- US-002.7 En tant qu’Admin, le nom logique est écrit dans le Backend puis propagé.
- US-002.8 En tant que système, HA/Z2M ne sont jamais SoT du nom métier.

**Tâches techniques**

- SoT nom logique
- File `pending_ops` pour rename différé
- Option admin rename `entity_id` (v1)

### F-011 — Remplacement deux fiches

**User Stories**

- US-002.9 En tant qu’Admin, je remplace un équipement : prédécesseur `replaced`, nouveau `hestia_device_id`.

**Tâches techniques**

- `predecessor_id` / `successor_id`
- Interdiction de réutiliser `hestia_device_id`

### F-012 — Reprises

**User Stories**

- US-002.10 En tant que nœud, je survit aux pannes courant / MQTT / réinstall HA-Z2M selon §6.8.

**Tâches techniques**

- Comportements normatifs reprises Module 70
- Tests de non-régression documentaires → scénarios

## Critères de done Epic

- API Backend conforme ADR-005 / Module 70.
- Aucune écriture métier directe Agent → SoT sans Backend.
