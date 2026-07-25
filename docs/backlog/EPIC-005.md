# EPIC-005 — Hub familial & notifications

| Attribut | Valeur |
|----------|--------|
| **Phase** | P4 |
| **Statut** | À faire |
| **Dépôts** | `hestia` (`client/`, `android/`, API) |
| **Prérequis** | EPIC-004 |
| **Bloque** | EPIC-006 |

## Sources

- [ADR-020](../adr/ADR-020%20-%20Positionnement%20de%20Hestia%20vis-%C3%A0-vis%20de%20Home%20Assistant.md) — Apps = présentation
- [ADR-018](../adr/ADR-018-architecture-domotique-agent-passerelle.md) — modules `home` / `cameras`
- [FUNCTIONAL-VISION](../vision/FUNCTIONAL-VISION.md) §9, §11, §12
- [architecture-domotique.md](../architecture/architecture-domotique.md) §2, §8
- [ecosysteme.md](../ecosysteme/ecosysteme.md)
- [Glossaire](../gouvernance/GLOSSAIRE.md) — Applications Hestia

## Objectif

Faire des applications Hestia la **seule** expérience utilisateurs finaux pour consulter l’information utile et recevoir des notifications — sans logique métier dans le client.

## Features

### F-020 — Affichage Hub (module home)

**User Stories**

- US-005.1 En tant qu’utilisateur, je consulte les informations utiles sur le Hub / module home.
- US-005.2 En tant qu’utilisateur, je n’accède jamais à l’UI Home Assistant.

**Tâches techniques**

- Brancher module `home` (catalogue) sur API réelle
- Affichage temps réel / rafraîchissement
- PWA + Android WebView

### F-021 — Notifications utilisateurs

**User Stories**

- US-005.3 En tant qu’utilisateur / proche, je peux recevoir une notification liée à une information utile.

**Tâches techniques**

- Canal notification Serveur → Apps
- Règles d’émission minimales (post-PoC)
- Pas d’interprétation dans l’App

## Critères de done Epic

- Information PoC visible durablement dans le Hub.
- Notification optionnelle démontrable.
