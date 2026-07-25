# EPIC-011 — Résilience hors-ligne & administration distante

| Attribut | Valeur |
|----------|--------|
| **Phase** | P9 |
| **Statut** | À faire |
| **Dépôts** | `hestia-agent`, `hestia`, `hestia-installer` (ops) |
| **Prérequis** | EPIC-001, EPIC-002 |

## Sources

- [architecture-domotique.md](../architecture/architecture-domotique.md) §7.2, §7.3, §12
- [ADR-018](../adr/ADR-018-architecture-domotique-agent-passerelle.md) — principes non négociables
- [ADR-020](../adr/ADR-020%20-%20Positionnement%20de%20Hestia%20vis-%C3%A0-vis%20de%20Home%20Assistant.md) — autonomie Agent
- [Module 70](../equipements/70-cycle-vie-equipements.md) — réplique, pending_ops, reprises
- [Constitution](../constitution/HESTIA%20-%20DOCUMENT%20DE%20RÉFLEXION%20ARCHITECTURALE.md) — résilience / souveraineté
- [ecosysteme.md](../ecosysteme/ecosysteme.md)

## Objectif

Garantir autonomie du nœud sans Internet, synchronisation sans perte, administration distante sans intervention physique.

## Features

### F-037 — File d’attente offline + dédup

**User Stories**

- US-011.1 En tant que nœud, je file les événements hors-ligne puis synchronise sans doublon.

**Tâches techniques**

- Queue locale durable
- Horodatage + UUID d’événement (dédup) — *identifiant d’événement, pas `hestia_device_id`*
- Retry / backoff longue coupure

### F-038 — Autorité config VPS vs télémétrie nœud

**User Stories**

- US-011.2 En tant que système, la config maison descend du VPS ; la télémétrie remonte du nœud.

**Tâches techniques**

- Canaux descendants (config, règles, seuils)
- Canaux montants (événements)
- Pas d’algorithme de fusion symétrique (architecture-domotique §12)

### F-039 — Réinstallation nœud / récupération config

**User Stories**

- US-011.3 En tant qu’Admin, je réinstalle un nœud et récupère la configuration maison depuis le VPS.

**Tâches techniques**

- Parcours Installer + auth VPS
- Re-déploiement Agent
- Restauration config

### F-040 — Mises à jour à distance

**User Stories**

- US-011.4 En tant qu’Admin, j’administre HA, Agent, config, certificats à distance.

**Tâches techniques**

- Leviers Installer / supervision systemd
- Pas d’auto-update Agent hors Installer (ecosysteme)

## Critères de done Epic

- Coupure Internet simulée : pas de perte d’événements après reprise.
- Réinstall nœud documentée et testée.
