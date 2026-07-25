# EPIC-001 — Agent métier : observation & synchronisation

| Attribut | Valeur |
|----------|--------|
| **Phase** | P0 |
| **Statut** | Livré |
| **Clôture** | 2026-07-25 — [REPORT-CLOTURE-EPIC-001](execution/REPORT-CLOTURE-EPIC-001.md) |
| **Dépôts** | `hestia-agent`, `hestia` (API contrat) |
| **Bloque** | EPIC-002, EPIC-004, EPIC-011 |

## Sources

- [ADR-020](../adr/ADR-020%20-%20Positionnement%20de%20Hestia%20vis-%C3%A0-vis%20de%20Home%20Assistant.md) — responsabilités Agent
- [ADR-018](../adr/ADR-018-architecture-domotique-agent-passerelle.md)
- [architecture-domotique.md](../architecture/architecture-domotique.md) §2, §11, §12
- [Module 70](../equipements/70-cycle-vie-equipements.md) §1.1 — Agent = observation, réplique, transitions techniques
- [FUNCTIONAL-VISION](../vision/FUNCTIONAL-VISION.md) §9, §12, §15
- [Constitution](../constitution/HESTIA%20-%20DOCUMENT%20DE%20RÉFLEXION%20ARCHITECTURALE.md) — responsabilités composants
- [Glossaire](../gouvernance/GLOSSAIRE.md) — Agent ≠ métier familial

## Objectif

Faire évoluer `hestia-agent` au-delà de la V1 infrastructure : collecter, normaliser, filtrer et synchroniser vers le Serveur — **sans** logique d’accompagnement familial.

## Features

### F-001 — Collecte événements HA / MQTT

**User Stories**

- US-001.1 En tant qu’Agent, je reçois les événements pertinents depuis Home Assistant.
- US-001.2 En tant qu’Agent, je peux m’abonner aux topics MQTT locaux nécessaires.

**Tâches techniques**

- Contrat d’intégration HA (événements / états)
- Abonnements MQTT locaux
- Journalisation / health des flux

### F-002 — Normalisation vers modèle Hestia

**User Stories**

- US-001.3 En tant que Serveur, je reçois des événements dans un format Hestia stable, indépendant de HA.

**Tâches techniques**

- Schéma d’événement normalisé
- Mapping HA entity → ancre / type signal

### F-003 — Filtrage des informations pertinentes

**User Stories**

- US-001.4 En tant qu’Agent, je ne remonte que les signaux retenus (pas le bruit brut).

**Tâches techniques**

- Règles de sélection techniques (pas métier familial)
- Alignement avec MODELE-INFORMATION « Sélection »

### F-004 — Sync Agent ↔ Serveur (événements)

**User Stories**

- US-001.5 En tant que Serveur, je reçois la télémétrie du nœud de façon sécurisée.
- US-001.6 En tant qu’Agent, je réessaie l’envoi en cas d’échec temporaire.

**Tâches techniques**

- Endpoint API / canal sécurisé
- Retry / backoff minimal
- Horodatage + identifiant d’événement pour dédup (architecture-domotique §12)

### F-005 — Réplique locale Agent

**User Stories**

- US-001.7 En tant que nœud, je continue à enregistrer localement si le VPS est injoignable.

**Tâches techniques**

- Stockage local des événements en attente
- Réconciliation à la reprise

### F-006 — Transitions techniques auto

**User Stories**

- US-001.8 En tant que parc, un équipement `active`/`synced` passe `offline` après silence (`OFFLINE_GRACE`) puis revient si heartbeat.

**Tâches techniques**

- Heartbeat / silence
- Transitions `active`↔`offline`, `synced`↔`offline` (Module 70)

## Critères de done Epic

- Un événement SNZB (ou équivalent) traverse HA → Agent → API Serveur.
- Aucune règle « signification familiale » dans l’Agent.
- Health Agent inchangé / étendu sans casser V1 infra.
- Runtime nœud : **Python 3** (stdlib `json`/`uuid`) fourni par l’Installer pour la normalisation (L3).

**Statut done :** satisfaits à la clôture (2026-07-25) — détails et écarts dans [REPORT-CLOTURE-EPIC-001](execution/REPORT-CLOTURE-EPIC-001.md) et [CHECKLIST](execution/CHECKLIST-EPIC-001.md).

## Dossier d’exécution

→ [execution/EXEC-EPIC-001.md](execution/EXEC-EPIC-001.md) · [CHECKLIST](execution/CHECKLIST-EPIC-001.md) · [TESTPLAN](execution/TESTPLAN-EPIC-001.md) · [Clôture](execution/REPORT-CLOTURE-EPIC-001.md)
