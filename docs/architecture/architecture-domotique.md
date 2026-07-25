# Architecture domotique Hestia — Agent, passerelle et capteurs

> Organisation des dépôts : **[`ecosysteme.md`](../ecosysteme/ecosysteme.md)**.  
> Fondements : **[Constitution](../constitution/HESTIA%20-%20DOCUMENT%20DE%20RÉFLEXION%20ARCHITECTURALE.md)** · **[Glossaire](../gouvernance/GLOSSAIRE.md)** · **[ADR-020](../adr/ADR-020%20-%20Positionnement%20de%20Hestia%20vis-%C3%A0-vis%20de%20Home%20Assistant.md)**.  
> Ce document détaille le **comportement** de la passerelle et du flux domotique — pas l'installeur ni le code Agent.

## 1. Objectif

Surveiller un domicile, collecter des événements de capteurs, centraliser sur le VPS Hestia et afficher sur les applications Hestia — indépendamment de tout constructeur propriétaire.

Les définitions (assistant familial, HA = backend technique, Apps = présentation) sont dans la Constitution / le glossaire ; ce document ne les redéfinit pas.

## 2. Vue d'ensemble

```text
Capteurs
    ↓
Home Assistant          ← nœud (installé via hestia-installer)
    ↓
hestia-agent            ← dépôt dédié (déployé par l'Installer, code séparé)
    ↓
Serveur Hestia          ← dépôt hestia (VPS)
    ↓
Applications Hestia     ← PWA / Android (dépôt hestia)
```

| Étape | Dépôt / lieu | Rôle |
|-------|--------------|------|
| **Capteurs** | Physique | Événements physiques (présence, ouverture, etc.) — protocoles gérés en amont par Home Assistant |
| **Home Assistant** | Nœud Ubuntu (installé par `hestia-installer`) | Backend domotique local : protocoles, découverte, supervision, automatisations techniques, maintenance. Répond à « que se passe-t-il dans la maison ? ». UI réservée aux administrateurs |
| **hestia-agent** | [hestia-agent](https://github.com/Drosdov56/hestia-agent) | Couche d'abstraction sur le nœud : traduit les événements Home Assistant vers le modèle Hestia. **Pas** un sous-projet de l'Installer — l'Installer le déploie seulement |
| **Serveur Hestia** (VPS) | [hestia](https://github.com/Drosdov56/hestia) | Logique métier, comptes, historique, notifications, règles, API |
| **Applications Hestia** | `hestia` (`client/`, `android/`) | Seule expérience utilisateur finale ; aucune logique métier (ADR-0003) |

Le mini PC est un **nœud local autonome** : Home Assistant et `hestia-agent` y tournent. Le VPS héberge le serveur `hestia`. L'interface Home Assistant n'est **jamais** utilisée par les utilisateurs finaux.

## 3. Matériel de référence

| Composant | Référence |
|-----------|-----------|
| **Mini PC** | Intel N150, 16 Go RAM, SSD 512 Go min., Ethernet Gigabit + Wi-Fi, Ubuntu Server — provisionné par **hestia-installer** (HA + Agent + services) |
| **Passerelle Zigbee** | SONOFF ZBDongle-P (référence figée) — intégrée via Home Assistant |
| **Premier capteur** | SONOFF SNZB-06P24 (présence) — objectifs : découvrir Zigbee via Home Assistant, valider la chaîne jusqu'à `hestia-agent` et les événements temps réel |
| **Deuxième capteur** | Ouverture porte/fenêtre Zigbee — valide les scénarios ouverture/fermeture et notifications |

## 4. Philosophie de corrélation

Hestia ne décide jamais sur un seul capteur : il corrèle plusieurs signaux.

**Exemple** : porte ouverte + présence détectée + caméra confirmant une personne + aucun membre de la famille identifié ⇒ intrusion probable.

## 5. Caméras

Rôle de **confirmation**, jamais de détection unique :

```text
Radar → Hestia → Caméra → Analyse → Notification
```

## 6. Réseau et mobilité

Ethernet prioritaire, Wi-Fi possible. Détail du principe de connectivité, de la mobilité, du mode autonome et de l'autorité des données : voir [§12 Connectivité réseau](#12-connectivité-réseau).

## 7. Principes d'architecture

### 7.1 Identité des équipements

Chaque équipement (passerelle, capteur, caméra, relais, actionneur) possède un **`hestia_device_id`** (identifiant métier immuable) qui ne change jamais. Le nom affiché (« Porte d'entrée », « Salon ») est un libellé modifiable, indépendant de cet identifiant — ce qui permet de remplacer un équipement sans casser la configuration.

### 7.2 Mises à jour à distance

Le mini PC doit être intégralement administrable à distance : Home Assistant, Agent (`hestia-agent`), configuration, règles, certificats, éventuellement l'OS — via les outils de **hestia-installer** et la supervision systemd. Aucune intervention physique ne doit être nécessaire.

### 7.3 Fonctionnement hors connexion

Principe d'architecture : le nœud reste autonome sans accès Internet (capteurs, scénarios locaux, persistance et synchronisation différée). Comportement détaillé, autorité des données et mécanique de sync : voir [§12 Connectivité réseau](#12-connectivité-réseau).

## 8. Composants et dépôts

| Composant | Dépôt | Rôle |
|-----------|-------|------|
| **Nœud** (mini PC) | provisionné par [hestia-installer](https://github.com/Drosdov56/hestia-installer) ; runtime [hestia-agent](https://github.com/Drosdov56/hestia-agent) | HA + Agent ; communication locale, autonomie hors connexion, synchronisation VPS |
| **Serveur Hestia** (VPS) | [hestia](https://github.com/Drosdov56/hestia) | Comptes, historique, notifications, règles globales, sauvegardes, API |
| **Applications Hestia** | `hestia` | Affichage, consultation, paramétrage, notifications — seule interface utilisateurs finaux |

Cartographie complète : [`ecosysteme.md`](../ecosysteme/ecosysteme.md).
## 9. Évolution future

Plusieurs logements, plusieurs mini PC, plusieurs familles : chaque mini PC avec un identifiant unique, configuration téléchargée automatiquement depuis le VPS. Non prioritaire, mais l'architecture doit le permettre sans refonte.

## 10. Périmètre ADR-0005 (dépendances externes)

ADR-0005 (limiter les dépendances externes) s'applique à `client/` (PWA) et `core/` (API PHP) du dépôt **`hestia`** — le stack applicatif vanilla existant.

Le nœud (mini PC) est un **périmètre distinct**, avec ses propres dépendances justifiées (Home Assistant, Docker, Mosquitto, etc. via `hestia-installer` ; runtime via `hestia-agent`). ADR-0005 ne s'y applique pas : pas de contradiction avec le principe existant. Home Assistant reste encapsulé derrière l'Agent : le code métier Hestia (`client/`, `core/`) ne dépend jamais directement de Home Assistant.

## 11. Prochaines étapes techniques

1. Installation / maintenance du nœud via **hestia-installer** (Ubuntu, Docker, Home Assistant, Mosquitto)
2. Appairage SONOFF ZBDongle-P via Home Assistant
3. Appairage premier capteur (présence)
4. Évolution de **hestia-agent** au-delà de la V1 infrastructure (contrat sync, MQTT, intégration HA) — dépôt dédié, déjà créé
5. Transmission des événements vers le serveur Hestia (VPS, dépôt `hestia`)
6. Affichage temps réel sur les applications Hestia

## 12. Connectivité réseau

### Principe

L'architecture ne dépend d'aucun type particulier d'accès Internet. Le mini PC
communique exclusivement en IP avec le VPS et n'a aucune connaissance du type
de connexion utilisée (box fibre/ADSL, routeur 4G/5G, hotspot smartphone). Ces
trois cas sont équivalents du point de vue logiciel ; aucune modification n'est
nécessaire pour passer de l'un à l'autre.

Le mini PC doit uniquement :

- obtenir une adresse IP (DHCP ou configuration statique) ;
- résoudre le nom du VPS (DNS) ;
- établir une connexion sécurisée avec celui-ci.

### Mobilité

Le mini PC est un équipement transportable. En cas de changement de logement,
le matériel et les capteurs associés restent inchangés ; seule la connexion au
nouveau réseau local doit être reconfigurée.

### Mode autonome et synchronisation

Le fonctionnement local ne dépend pas du VPS. En cas de perte d'accès Internet :

- les capteurs continuent à fonctionner via Home Assistant ;
- les automatisations techniques locales (Home Assistant) et `hestia-agent` continuent à s'exécuter ;
- la tablette reste utilisable sur le réseau local ;
- les événements sont enregistrés localement.

À la reprise de la connexion, les événements sont synchronisés automatiquement
avec le VPS, sans intervention utilisateur et sans perte de données.

**Autorité par type de donnée** (et non un modèle symétrique de résolution de
conflit) :

- **Configuration de la maison** (équipements déclarés, règles, seuils) : le
  VPS est la source de vérité, la donnée descend vers le nœud. Le mini PC
  ne modifie jamais cette donnée localement — pas de conflit possible, un état
  est simplement à jour ou en attente de réception.
- **Événements et télémétrie** (relevés capteurs, présence, historique) : le
  nœud est la source de vérité, la donnée remonte vers le VPS. Le VPS ne
  génère jamais cet événement — pas de conflit non plus, seulement un enjeu de
  complétude (rien ne doit manquer) et d'ordre (horodatage + UUID pour
  dédupliquer en cas de renvoi).

Ce modèle évite un algorithme de fusion/arbitrage : il s'agit d'une file
d'attente locale résiliente avec horodatage et déduplication par UUID, pas
d'une résolution de conflit au sens strict. Le détail mécanique (format de la
queue, retry/backoff, gestion d'une coupure longue) est calibré dans le dépôt
**hestia-agent** (et le contrat côté API `hestia`), plutôt que spéculé ici.

### Remplacement / réinstallation du nœud

Hestia reste pensé pour **un seul foyer, un seul nœud, un seul VPS** —
pas de modèle multi-instance au sein d'un même foyer. Le projet peut être
redéployé de façon indépendante pour un autre foyer : installation Hestia
distincte, avec son propre VPS et son propre nœud.

La réinstallation du nœud (panne matérielle, déménagement) s'appuie sur
**hestia-installer** (déjà en production), pas sur une « image logicielle » à inventer :

1. connexion au réseau local ;
2. exécution de l'installeur / authentification auprès du VPS ;
3. récupération de la configuration de la maison ;
4. fonctionnement normal (Agent via module de déploiement de l'Installer).

L'objectif est de minimiser l'intervention technique nécessaire à cette
réinstallation, pas de gérer plusieurs passerelles simultanées pour un même
foyer.
