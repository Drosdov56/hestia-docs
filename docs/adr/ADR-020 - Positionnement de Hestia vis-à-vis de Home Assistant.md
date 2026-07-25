# ADR-020 — Positionnement de Hestia vis-à-vis de Home Assistant

**Statut :** Accepté  
**Date :** 2026-07-17  
**Emplacement :** `hestia-docs/docs/adr/`  

> Cadre conceptuel : [Constitution](../constitution/HESTIA%20-%20DOCUMENT%20DE%20RÉFLEXION%20ARCHITECTURALE.md) · [Glossaire](../gouvernance/GLOSSAIRE.md).  
> Cet ADR **formalise** une décision déjà portée par la Constitution ; il ne la remplace pas.

---

# Contexte

Le projet Hestia vise à accompagner les personnes et leur entourage grâce à un système capable d'interpréter des informations provenant de plusieurs sources.

Les premières versions de l'architecture prévoyaient que Hestia assure directement une grande partie des fonctions habituellement couvertes par une plateforme domotique : intégration des équipements, supervision des capteurs, gestion des protocoles et automatisations techniques.

Cette approche présentait plusieurs inconvénients :

* duplication d'un travail déjà réalisé par des projets matures ;
* augmentation importante de la complexité de maintenance ;
* ralentissement du développement des fonctionnalités différenciantes d'Hestia.

Après analyse, il a été décidé d'intégrer Home Assistant comme backend domotique local.

Cette décision ne modifie pas la finalité du projet mais clarifie la répartition des responsabilités entre les différents composants.

---

# Décision

Hestia n'est pas une plateforme domotique.

Hestia est un **assistant familial contextuel**.

Sa mission consiste à interpréter les informations provenant de différentes sources afin d'aider les proches à comprendre la situation d'une personne et à l'accompagner dans son quotidien.

La domotique constitue l'une de ces sources d'information mais n'est pas le cœur fonctionnel du produit.

Home Assistant devient le backend domotique de référence utilisé par Hestia.

---

# Répartition des responsabilités

## Home Assistant

Home Assistant est responsable de toute la couche technique de la maison connectée.

Cela comprend notamment :

* découverte des équipements ;
* intégration des protocoles (Zigbee, Matter, Z-Wave, Wi-Fi, Bluetooth, etc.) ;
* supervision des périphériques ;
* automatisations purement techniques ;
* diagnostics ;
* maintenance ;
* mises à jour des intégrations.

Home Assistant répond à la question :

> **Que se passe-t-il dans la maison ?**

---

## Agent Hestia

L'Agent Hestia constitue la couche d'intégration entre Home Assistant et l'écosystème Hestia.

Ses responsabilités sont notamment :

* collecte des événements provenant du backend domotique ;
* normalisation des données ;
* filtrage des informations pertinentes ;
* synchronisation locale avec le serveur Hestia ;
* fonctionnement autonome en cas de perte de connexion Internet.

L'Agent Hestia ne contient aucune logique métier liée à l'accompagnement des personnes.

---

## Serveur Hestia

Le serveur Hestia centralise la connaissance métier.

Il gère notamment :

* les utilisateurs ;
* les profils familiaux ;
* les habitudes de vie ;
* les rappels ;
* les calendriers ;
* les historiques ;
* les règles métier ;
* les notifications ;
* les futures capacités d'analyse par intelligence artificielle.

Le serveur Hestia répond à la question :

> **Que signifie ce qui se passe pour cette personne et sa famille ?**

---

## Applications Hestia

Les applications Hestia constituent l'unique interface destinée aux utilisateurs.

Elles présentent une vision contextualisée de la situation familiale.

Les utilisateurs ne manipulent jamais directement les concepts propres à Home Assistant.

---

# Interface utilisateur

L'interface utilisateur de Home Assistant n'est pas utilisée comme interface fonctionnelle du produit Hestia.

Elle reste réservée aux administrateurs afin de :

* ajouter ou supprimer des équipements ;
* configurer les intégrations ;
* effectuer les diagnostics ;
* superviser l'infrastructure domotique ;
* assurer la maintenance.

Toutes les interactions quotidiennes des utilisateurs sont réalisées exclusivement via les interfaces Hestia.

---

# Couplage

Home Assistant est considéré comme un backend domotique interchangeable.

Le reste de l'application ne doit jamais dépendre directement de son API.

Toutes les communications passent par une couche d'abstraction assurée par l'Agent Hestia.

Cette architecture permet, si nécessaire, de remplacer Home Assistant par une autre solution sans remettre en cause les composants métier de Hestia.

---

# Sources d'information

La maison connectée n'est qu'une source d'information parmi d'autres.

À terme, Hestia pourra également exploiter des données provenant notamment de :

* calendriers ;
* services météo ;
* objets connectés de santé ;
* dispositifs de géolocalisation ;
* services tiers ;
* informations fournies par les proches ;
* futurs connecteurs métiers.

Toutes ces informations alimentent un même modèle de contexte familial.

---

# Conséquences

Cette décision permet :

* de capitaliser sur un écosystème domotique mature ;
* d'éviter la réécriture de fonctionnalités techniques à faible valeur ajoutée ;
* de concentrer les développements sur les fonctionnalités différenciantes d'Hestia ;
* de préserver une interface utilisateur unique ;
* de limiter le couplage avec les technologies domotiques.

---

# Conséquences négatives

Cette décision introduit une dépendance externe supplémentaire.

Cette dépendance est toutefois :

* encapsulée ;
* documentée ;
* assumée ;
* remplaçable.

Elle ne remet pas en cause l'indépendance fonctionnelle du projet Hestia.

---

# Vision

Home Assistant automatise la maison.

Hestia accompagne les personnes.

Cette distinction constitue désormais un principe fondateur de l'architecture du projet.
