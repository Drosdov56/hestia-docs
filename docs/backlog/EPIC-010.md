# EPIC-010 — Corrélation multi-signaux & caméras

| Attribut | Valeur |
|----------|--------|
| **Phase** | P8 |
| **Statut** | À faire |
| **Dépôts** | `hestia`, éventuellement `hestia-agent` (flux caméra technique) |
| **Prérequis** | EPIC-006, EPIC-007 ; EPIC-009 pour scénarios familiaux |
| **Bloque** | — |

## Sources

- [architecture-domotique.md](../architecture/architecture-domotique.md) §3–5, §11
- [ADR-018](../adr/ADR-018-architecture-domotique-agent-passerelle.md)
- [MODELE-DECISION](../modeles/MODELE-DECISION.md) — corrélation
- [MODELE-INFORMATION](../modeles/MODELE-INFORMATION.md)
- [FUNCTIONAL-VISION](../vision/FUNCTIONAL-VISION.md) §6 — corrélation d’événements (vision IA, sans imposer IA)

## Objectif

Ne jamais décider sur un seul capteur : corréler plusieurs signaux ; caméras = confirmation uniquement.

## Features

### F-034 — Corrélation multi-capteurs

**User Stories**

- US-010.1 En tant que Serveur, je combine présence + ouverture (+ autres) avant conclusion.

**Tâches techniques**

- Moteur de corrélation (règles d’abord)
- Scénario référence intrusion probable (§4 architecture-domotique)

### F-035 — Caméras en confirmation

**User Stories**

- US-010.2 En tant que système, la caméra confirme, elle ne détecte pas seule.
- US-010.3 En tant qu’utilisateur, le module cameras affiche le rôle de confirmation.

**Tâches techniques**

- Intégration flux / événements caméra via HA/Agent
- Branchement module `cameras` du catalogue
- Chaîne Radar → Hestia → Caméra → Analyse → Notification

### F-036 — Capteur ouverture porte/fenêtre

**User Stories**

- US-010.4 En tant que foyer, un second capteur d’ouverture valide ouverture/fermeture et notifications.

**Tâches techniques**

- Mise en service (EPIC-003)
- Scénarios ouverture + notification

## Critères de done Epic

- Scénario multi-signaux démontré.
- Caméra non utilisée comme unique détecteur.
