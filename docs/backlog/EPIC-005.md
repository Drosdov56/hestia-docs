# EPIC-005 — Hub familial & notifications

| Attribut | Valeur |
|----------|--------|
| **Phase** | P4 |
| **Statut** | **TERMINÉ / CLÔTURÉ** (2026-07-31) |
| **Dépôts** | `hestia` (`client/`, `android/`, API) |
| **Prérequis** | EPIC-004 |
| **Bloque** | EPIC-006 |
| **Exécution** | [`execution/EXEC-EPIC-005.md`](execution/EXEC-EPIC-005.md) |
| **Tip code** | `0a564b0` (`hestia`) |

## Sources

- [ADR-020](../adr/ADR-020%20-%20Positionnement%20de%20Hestia%20vis-%C3%A0-vis%20de%20Home%20Assistant.md) — Apps = présentation
- [ADR-018](../adr/ADR-018-architecture-domotique-agent-passerelle.md) — modules `home` / `cameras`
- [FUNCTIONAL-VISION](../vision/FUNCTIONAL-VISION.md) §9, §11, §12
- [architecture-domotique.md](../architecture/architecture-domotique.md) §2, §8
- [ecosysteme.md](../ecosysteme/ecosysteme.md)
- [Glossaire](../gouvernance/GLOSSAIRE.md) — Applications Hestia

## Objectif

Faire des applications Hestia la **seule** expérience utilisateurs finaux pour consulter l’information utile et recevoir des notifications — sans logique métier dans le client.

## Features — implémentées et validées

| Feature | Contenu | Lot | Commit (`hestia`) |
|---------|---------|-----|-------------------|
| **F-020** | Affichage Hub (module home) | A, B, D | `1bc2fb2` · `ae5717c` · `0a564b0` |
| **F-021** | Notifications utilisateurs | C, D | `b2fdc01` · `0a564b0` |

### Lots A→D

| Lot | Objet | Commit (`hestia`) |
|-----|-------|-------------------|
| **A** | Contrat API Hub utilisateur | `1bc2fb2c7c6ea2a596af3e1177bd10bf4ca5912b` |
| **B** | Affichage Hub / module home (PWA) | `ae5717c80f994a685ede404352d7c6355462d3ce` |
| **C** | Notifications in-app | `b2fdc016c46b50075b28772c4a9fb3a6b379b50b` |
| **D** | Bridge Android WebView | `0a564b025b5633a9cee2cf77f83d2590e8245735` |

### F-020 — Affichage Hub (module home) ✅

**User Stories**

- US-005.1 En tant qu’utilisateur, je consulte les informations utiles sur le Hub / module home.
- US-005.2 En tant qu’utilisateur, je n’accède jamais à l’UI Home Assistant.

**Livré**

- Contrat API Hub `hub.v1` (consultation durable)
- Module `home` branché sur l’API réelle
- Affichage / rafraîchissement PWA
- Surface Android WebView (PWA + bridge)

### F-021 — Notifications utilisateurs ✅

**User Stories**

- US-005.3 En tant qu’utilisateur / proche, je peux recevoir une notification liée à une information utile.

**Livré**

- Canal notification Serveur → Apps (`notifications.v1`, in-app)
- Bridge Android pour présentation native (sans logique métier client)
- Pas d’interprétation métier dans l’App

## Critères de done Epic — atteints

- Information PoC visible durablement dans le Hub.
- Notification optionnelle démontrable (in-app + bridge Android).
