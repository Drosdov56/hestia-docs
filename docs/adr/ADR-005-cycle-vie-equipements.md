# ADR-005 — Cycle de vie des équipements Hestia

| Attribut | Valeur |
|----------|--------|
| **Statut** | Accepté — **gelé / clôturé** (revue + contrôle documentaire final 2026-07-21) |
| **Date** | 2026-07-21 |
| **Portée** | Modèle d'identité, machine d'états, métadonnées, renommage, reprises et responsabilités — parc d'équipements |
| **Références** | [ADR-004](ADR-004-mise-en-service-equipements.md), [conception Module 70](../equipements/70-cycle-vie-equipements.md), [UX-003](../../../hestia-installer/docs/BACKLOG.md#ux-003--assistant-de-mise-en-service), [INT-001](../../../hestia-installer/docs/BACKLOG.md#int-001--intégration-mqtt-home-assistant-exigence-majeure) |

## Contexte

[ADR-004](ADR-004-mise-en-service-equipements.md) a figé la frontière **Installation / Mise en service / Exploitation**.

Une première rédaction du Module 70 a ensuite été **revue de façon critique** : contradictions d'identité, chevauchements de responsabilités, absences de scénarios de reprise et couplage excessif à Zigbee2MQTT ont été corrigés dans la spécification normative.

## Décision

Le **Module 70 — Cycle de vie des équipements** est l'architecture fonctionnelle du parc. Spécification normative : [conception/70-cycle-vie-equipements.md](../equipements/70-cycle-vie-equipements.md).

| Composant | Rôle |
|-----------|------|
| **Backend Hestia** | **Source de vérité** du modèle fonctionnel |
| **Hestia Admin** | Décisions métier et UX-003 |
| **Agent Hestia** | Observation, réplique locale, sync, transitions techniques auto |
| **`70-hestia-agent.sh`** | Déploie l'Agent — **aucune** logique métier |

### Décisions structurantes gelées

1. **`hestia_device_id`** — identifiant métier immuable ; créé à `detected` → `pending_provisioning` ; **jamais** réutilisé au remplacement (modèle deux fiches).
2. **Ancre physique** — IEEE (Zigbee) ou équivalent protocole ; corrélation technique, pas clé métier.
3. **Nom logique** — SoT Backend Hestia ; propagation contrôlée vers passerelle / HA ; jamais l'inverse en écriture.
4. **HA entity_id (v1)** — liés tels que découverts ; rename entity_id uniquement via option admin explicite ; display names alignés par défaut.
5. **Machine d'états** — neuf états ; transitions autorisées **et** interdites documentées ; `synced`↔`offline` inclus.
6. **Autorité** — Admin décide le métier ; Agent exécute le technique et les transitions auto (`active`↔`offline`) ; Backend persiste la vérité.
7. **Adaptateurs protocole** — `protocol` + `protocol_bindings` ; pas de champ Z2M obligatoire au modèle cœur.
8. **Reprises** — panne courant, MQTT, réinstall HA/Z2M, sauvegardes, changement coordinateur : comportements normatifs dans la spécification §6.8.
9. **v1 mono-nœud** — un HA, une instance Z2M (si Zigbee), un logement logique par déploiement ; multi-* hors périmètre.

## Conséquences

- Installer : Phase 1 uniquement (dont déploiement Agent + INT-001).
- Développement Agent / Admin / Backend : strictement conforme à la spécification Module 70.
- Modules 40 / 50 / 60 : non remis en cause (ADR-003 inchangé).

## Cohérence

| Référence | Relation |
|-----------|----------|
| [ADR-001](../../../hestia-installer/docs/ADR/ADR-001-architecture-installeur.md) | Conforme |
| [ADR-003](../../../hestia-installer/docs/ADR/ADR-003-architecture-reseau-pile-domotique.md) | Conforme |
| [ADR-004](ADR-004-mise-en-service-equipements.md) | Complète les différés d'ADR-004 |
| Modules 40 / 50 / 60 | Consommation, pas redéfinition |

## Références

- [conception/70-cycle-vie-equipements.md](../equipements/70-cycle-vie-equipements.md)
- [ARCHITECTURE.md](../../../hestia-installer/docs/ARCHITECTURE.md#module-70--cycle-de-vie-des-équipements-adr-005)
- [BACKLOG UX-003](../../../hestia-installer/docs/BACKLOG.md#ux-003--assistant-de-mise-en-service)
