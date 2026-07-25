# Brief de démarrage – Hub Familial

> **ARCHIVÉ — non normatif.** Conservé pour l’historique.  
> Constitution actuelle : [`../constitution/`](../constitution/) · Écosystème : [`../ecosysteme/ecosysteme.md`](../ecosysteme/ecosysteme.md).

Brief de démarrage – Hub Familial (nom provisoire)

Vision

Développer un assistant numérique familial, fonctionnant principalement sur une tablette Android, destiné à centraliser les informations utiles du quotidien.

L'objectif n'est pas de créer une application de domotique, mais un véritable hub de vie regroupant les rappels, la communication, les informations personnelles, la surveillance, la domotique et, à terme, tout service utile à la famille.

Le projet est avant tout destiné à un usage personnel et familial, avec une première utilisation envisagée pour ma mère, tout en restant suffisamment générique pour être utilisé ensuite par d'autres membres de la famille.

Philosophie du projet

Le projet privilégie la simplicité, la robustesse et la pérennité plutôt que la recherche des dernières technologies.

L'objectif n'est pas de construire un logiciel "éternel", mais un logiciel :

compréhensible ;

facilement maintenable ;

facilement transmissible ;

capable d'évoluer progressivement.

Les décisions techniques devront toujours privilégier les standards ouverts et limiter les dépendances.

Objectifs principaux

Créer une interface unique permettant notamment de :

gérer les rappels quotidiens ;

consulter un calendrier ;

centraliser les appels et la messagerie ;

afficher une ou plusieurs caméras ;

accéder à des informations pratiques ;

gérer progressivement différents équipements de la maison ;

afficher des tableaux de bord personnalisés.

Le système devra être entièrement modulaire afin de permettre l'ajout de nouvelles fonctionnalités sans remettre en cause l'architecture existante.

Principes d'architecture

Le projet reposera sur trois couches clairement séparées.

1. Le client

La tablette constitue uniquement l'interface utilisateur.

Elle doit pouvoir être remplacée facilement sans perte de données.

Une nouvelle tablette devra pouvoir être opérationnelle en quelques minutes.

2. Le serveur

Le serveur centralise :

les données ;

les paramètres ;

les sauvegardes ;

les synchronisations ;

les services communs.

La tablette doit cependant continuer à fonctionner en mode dégradé si le serveur est momentanément indisponible.

3. Les données

Les données représentent l'élément le plus précieux du projet.

Le code pourra évoluer.

Les technologies pourront changer.

Les données devront toujours pouvoir être conservées et migrées.

Choix technologiques retenus

Backend

PHP

API REST

Architecture claire et documentée

Faible dépendance à des bibliothèques externes

Base de données serveur

MariaDB

Base locale

SQLite

La tablette disposera d'une base locale synchronisée avec le serveur afin d'assurer un fonctionnement même hors connexion.

Communication

HTTPS

JSON

API REST

Ces standards ouverts sont privilégiés pour leur stabilité et leur interopérabilité.

Interface utilisateur

Le projet prendra la forme d'une PWA.

Une application Android privée pourra ensuite encapsuler cette PWA afin d'accéder à certaines fonctionnalités natives (notifications, démarrage automatique, maintien de l'écran actif, etc.).

L'application ne sera pas distribuée via un store public.

Les APK seront générés et installés directement.

Dépendances

Principe fondamental :

Limiter autant que possible les dépendances externes.

Les frameworks pourront être utilisés lorsqu'ils apportent une réelle valeur, mais ils ne devront jamais devenir indispensables à l'architecture.

> **Décision actée (ADR-0005, juillet 2026)** — Cette piste n'a pas été retenue : le projet reste en **JavaScript vanilla** (modules ES), sans framework.

La logique métier devra rester indépendante de l'interface graphique.

Évolutivité

Chaque fonctionnalité devra être développée sous forme de module indépendant.

Exemples :

rappels

calendrier

caméras

météo

domotique

appels

WhatsApp

surveillance

santé

tableaux de bord

projets personnels

L'ajout d'un module ne devra pas nécessiter de modifier les modules existants.

Pérennité

Le projet est conçu pour pouvoir vivre de nombreuses années.

Cependant, aucune technologie n'est considérée comme immuable.

Le principe retenu est le suivant :

conserver les données ;

conserver les règles métier ;

documenter l'architecture ;

permettre la réécriture d'une couche technique si cela devient pertinent.

Une évolution devra toujours être un choix, jamais une obligation.

Documentation

La documentation fait partie intégrante du projet.

Elle devra permettre à un autre développeur de comprendre rapidement :

l'architecture ;

les modules ;

les API ;

le modèle de données ;

le processus de déploiement.

Principe directeur

Les données doivent survivre au code.

Les informations importantes (utilisateurs, rappels, paramètres, historiques, scénarios, etc.) devront toujours pouvoir être exportées dans des formats ouverts (SQL, JSON, CSV...) afin de garantir leur conservation, quelle que soit l'évolution des technologies.

Critère de réussite

Le projet sera considéré comme réussi si, dans dix ans, après plusieurs années sans y avoir travaillé, il est possible de reprendre son développement rapidement grâce à une architecture claire, une documentation complète et des choix techniques simples.

L'objectif n'est pas de créer le logiciel le plus sophistiqué, mais le plus durable et le plus utile pour ses utilisateurs.
