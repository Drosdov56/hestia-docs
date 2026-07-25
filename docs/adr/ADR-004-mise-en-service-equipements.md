# ADR-004 — Mise en service des équipements Hestia

| Attribut | Valeur |
|----------|--------|
| **Statut** | Accepté |
| **Date** | 2026-07-21 |
| **Portée** | Frontière Hestia Installer / Hestia Admin — cycle de vie des équipements après installation plateforme |
| **Références** | [ADR-003](../../../hestia-installer/docs/ADR/ADR-003-architecture-reseau-pile-domotique.md), [ADR-005](ADR-005-cycle-vie-equipements.md), [INT-001](../../../hestia-installer/docs/BACKLOG.md#int-001--intégration-mqtt-home-assistant-exigence-majeure), [UX-003](../../../hestia-installer/docs/BACKLOG.md#ux-003--assistant-de-mise-en-service) |

## Contexte

La validation terrain a démontré que :

- Home Assistant permet la **découverte technique** des équipements (via MQTT / Zigbee2MQTT) ;
- l'**interface Home Assistant** n'est pas adaptée au processus utilisateur souhaité par Hestia ;
- l'appairage et la découverte technique interviennent **avant** que l'utilisateur connaisse le contexte métier des équipements.

Demander à l'utilisateur de mettre en service un équipement via Home Assistant ou Zigbee2MQTT contredit l'expérience cible : Hestia est la **seule couche visible** pour l'administrateur du nœud.

## Décision

La **mise en service des équipements** devient une responsabilité de **Hestia (mode administrateur)**, et **non** de Hestia Installer.

Hestia Installer s'arrête à la **mise en place de la plateforme technique**. Hestia Admin prend le relais pour la **mise en service fonctionnelle** de chaque équipement.

## Architecture retenue — trois phases

```
┌─────────────────────────────────────────────────────────────────┐
│ Phase 1 — Hestia Installer                                      │
│   Installation plateforme · config composants · validation infra │
│   → FIN DE RESPONSABILITÉ INSTALLEUR                            │
└────────────────────────────┬────────────────────────────────────┘
                             ↓
┌─────────────────────────────────────────────────────────────────┐
│ Phase 2 — Assistant de mise en service (Hestia Admin)           │
│   Par équipement : validation technique → infos métier → config │
└────────────────────────────┬────────────────────────────────────┘
                             ↓
┌─────────────────────────────────────────────────────────────────┐
│ Phase 3 — Exploitation (Hestia)                                 │
│   Données HA · Z2M transparent · aucune interaction directe Z2M  │
└─────────────────────────────────────────────────────────────────┘
```

### Phase 1 — Hestia Installer

**Responsable :** `hestia-installer` (modules 00–60, `70-hestia-agent` déploiement seul, 90, [INT-001](../../../hestia-installer/docs/BACKLOG.md#int-001--intégration-mqtt-home-assistant-exigence-majeure)).

| Responsabilité | Description |
|----------------|-------------|
| Installation de la plateforme | Conteneurs HA, Mosquitto, Zigbee2MQTT, Agent |
| Configuration automatique | Composants, credentials, intégration MQTT HA (INT-001) |
| Validation de l'infrastructure | Probes, idempotence, rapport d'installation |
| Fin de responsabilité | L'installeur **ne met pas en service** les équipements individuels |

L'installeur peut permettre la **découverte et l'appairage technique** (ex. via Zigbee2MQTT), mais **aucune information métier** n'est collectée et **aucun renommage fonctionnel** n'est imposé.

### Phase 2 — Assistant de mise en service (Hestia Admin)

**Responsable :** Hestia — profil **administrateur** (Agent Hestia, backend, UI admin).

Pour **chaque nouvel équipement détecté**, l'assistant exécute le workflow suivant :

#### Étape 2.1 — Vérifications automatiques

| Contrôle | Description |
|----------|-------------|
| Communication Zigbee | Équipement joignable via la passerelle |
| Communication MQTT | Messages présents sur le broker |
| Découverte Home Assistant | Device / entités visibles côté HA |
| Disponibilité des entités | Entités exploitables (état, capteurs, actionneurs) |

#### Étape 2.2 — Rapport de validation fonctionnelle

Génération d'un **rapport de validation fonctionnelle** par équipement, synthétisant les contrôles techniques réussis ou en échec, **avant** toute saisie métier.

#### Étape 2.3 — Collecte des informations métier

Demande à l'administrateur :

| Information | Exemples |
|-------------|----------|
| Nom logique | « Capteur température salon » |
| Pièce | salon, chambre parentale |
| Zone | rez-de-chaussée, étage |
| Catégorie | capteur température, interrupteur, prise |
| Options spécifiques | selon type d'équipement |

#### Étape 2.4 — Enregistrement configuration Hestia

Persistance du modèle fonctionnel dans Hestia (backend / Agent), avec propagation des identifiants logiques vers les backends techniques si nécessaire.

### Phase 3 — Exploitation

**Responsable :** Hestia (applications, automatisations, UI utilisateur).

| Principe | Description |
|----------|-------------|
| Source de données | Hestia exploite les données provenant de **Home Assistant** |
| Transparence Z2M | **Zigbee2MQTT reste totalement transparent** pour l'utilisateur |
| Pas d'interaction Z2M | L'utilisateur **n'utilise jamais** Zigbee2MQTT pour l'exploitation courante |
| Pas de mise en service via HA | L'utilisateur **n'utilise jamais** Home Assistant pour mettre un équipement en service |

## Principes directeurs

1. **Home Assistant est un backend technique** — découverte et télémétrie, pas interface de mise en service.
2. **Zigbee2MQTT est transparent** — passerelle invisible pour l'utilisateur final et l'administrateur en exploitation.
3. **Mise en service = Hestia Admin uniquement** — assistant guidé, jamais HA ni Z2M.
4. **Installation ≠ mise en service** — deux processus distincts, deux responsables distincts.
5. **Identifiants techniques vs logiques** — l'installeur laisse les identifiants techniques ; Hestia Admin assigne les identifiants logiques.

## Conséquences

### Pour Hestia Installer

- ne pas implémenter de workflow de renommage métier des équipements ;
- ne pas exposer Zigbee2MQTT frontend comme outil de mise en service utilisateur ;
- documenter clairement la **fin de responsabilité** à l'issue de la Phase 1 ;
- [INT-001](../../../hestia-installer/docs/BACKLOG.md#int-001--intégration-mqtt-home-assistant-exigence-majeure) reste dans le périmètre installeur (connexion plateforme HA ↔ MQTT).

### Pour Hestia (Admin / Agent / backend)

- implémenter l'**Assistant de mise en service** ([UX-003](../../../hestia-installer/docs/BACKLOG.md#ux-003--assistant-de-mise-en-service)) ;
- orchestrer les vérifications techniques multi-couches (Zigbee, MQTT, HA) ;
- posséder le **modèle fonctionnel** du logement et des équipements ;
- propager les identifiants logiques si requis — **mécanisme gelé** : [ADR-005](ADR-005-cycle-vie-equipements.md), [Module 70 §5](../equipements/70-cycle-vie-equipements.md#module-70-renommage).

### Pour Home Assistant et Zigbee2MQTT

- restent des **composants techniques** de la pile ;
- HA : discovery MQTT, entités, états ;
- Z2M : appairage Zigbee, pont MQTT ;
- aucun n'est une **interface de mise en service** pour l'utilisateur Hestia.

## Cohérence avec les ADR existants

| ADR / entrée | Relation |
|--------------|----------|
| [ADR-001](../../../hestia-installer/docs/ADR/ADR-001-architecture-installeur.md) | Conforme — l'installeur ne déborde pas sur la logique métier Hestia |
| [ADR-003](../../../hestia-installer/docs/ADR/ADR-003-architecture-reseau-pile-domotique.md) | Conforme — HA et Z2M restent backends techniques en `network=host` |
| [ADR-005](ADR-005-cycle-vie-equipements.md) | Complète — modèle d'identité, états, renommage |
| [INT-001](../../../hestia-installer/docs/BACKLOG.md#int-001--intégration-mqtt-home-assistant-exigence-majeure) | Phase 1 — connexion plateforme, pas mise en service équipements |
| [UX-003](../../../hestia-installer/docs/BACKLOG.md#ux-003--assistant-de-mise-en-service) | Phase 2 — implémentation fonctionnelle de cette ADR |

## Évolutions différées

| Sujet | Statut |
|-------|--------|
| Modèle de données, identité, machine d'états, renommages | **Gelé / clôturé** — [ADR-005](ADR-005-cycle-vie-equipements.md), [conception Module 70](../equipements/70-cycle-vie-equipements.md) |
| UI administrateur de l'assistant | Différé — implémentation [UX-003](../../../hestia-installer/docs/BACKLOG.md#ux-003--assistant-de-mise-en-service) |
| Protocoles au-delà de Zigbee (Bluetooth, Wi-Fi) | Différé — modèle extensible prévu dans Module 70 (adaptateurs) |

## Références

- [ARCHITECTURE.md](../../../hestia-installer/docs/ARCHITECTURE.md#mise-en-service-des-équipements-adr-004)
- [ADR-005 — Cycle de vie des équipements](ADR-005-cycle-vie-equipements.md)
- [conception/70-cycle-vie-equipements.md](../equipements/70-cycle-vie-equipements.md)
- Validation terrain mini-PC — découverte HA vs processus utilisateur
- [BACKLOG UX-003](../../../hestia-installer/docs/BACKLOG.md#ux-003--assistant-de-mise-en-service)
