# Hestia – Architecture de référence V1

> **ARCHIVÉ — non normatif.** Conservé pour l’historique.  
> Ne pas traiter comme source normative.  
> Constitution : [`../constitution/`](../constitution/) · Écosystème : [`../ecosysteme/ecosysteme.md`](../ecosysteme/ecosysteme.md).

Statut : Document fondateur

1. Présentation

Hestia est un assistant numérique familial destiné à devenir le point d'accès unique aux informations utiles du quotidien.

Il est conçu autour d'un principe simple :

Une seule interface pour retrouver tout ce qui compte.

Le projet est développé initialement pour un usage personnel et familial, mais son architecture doit permettre son utilisation par plusieurs utilisateurs sans remise en cause de sa conception.

Le domaine principal du projet est :

https://hestia.serpette.fr

2. Objectifs

Hestia n'est pas un logiciel de domotique.

La domotique constitue seulement l'un de ses modules.

Sa vocation est d'offrir une plateforme personnelle regroupant notamment :

rappels

calendrier

communication

caméras

domotique

informations familiales

santé

projets

tableaux de bord

notifications

services connectés

3. Principes fondateurs

Le projet repose sur les principes suivants.

Simplicité

Toute solution complexe doit être justifiée.

La solution la plus simple est privilégiée lorsqu'elle répond au besoin.

Pérennité

Les choix techniques privilégient les standards ouverts.

Le projet doit pouvoir évoluer progressivement pendant de nombreuses années.

Modularité

Chaque fonctionnalité est développée comme un module indépendant.

L'ajout ou la suppression d'un module ne doit pas impacter les autres.

Résilience

Le système doit continuer à fonctionner même en cas de perte temporaire de connexion Internet.

Documentation

Toute décision d'architecture importante doit être documentée.

4. Architecture générale

Le système est composé de quatre couches.

+----------------------------------------------+

|                Utilisateur                   |

+----------------------------------------------+

│

▼

+----------------------------------------------+

|     Application Android (conteneur natif)    |

+----------------------------------------------+

|                                              |

|                PWA Hestia                    |

|                                              |

+----------------------------------------------+

|            SQLite (données locales)          |

+----------------------------------------------+

│

Synchronisation HTTPS

│

▼

+----------------------------------------------+

|           API REST PHP (serveur)             |

+----------------------------------------------+

|                 MariaDB                      |

+----------------------------------------------+

5. Répartition des responsabilités

Application Android

Responsabilités :

démarrage automatique

notifications Android

mode kiosque

maintien de l'écran actif

accès aux fonctionnalités natives

hébergement de la PWA

Aucune logique métier ne doit être développée dans cette couche.

PWA

Responsabilités :

interface utilisateur

navigation

affichage

interaction utilisateur

fonctionnement hors ligne

La PWA constitue le cœur fonctionnel de l'application.

Le Dashboard constitue désormais une couche de présentation stable. Les modules sont indépendants et responsables de leur propre contenu. Le Dashboard ne fait qu'afficher leur synthèse.

API

Responsabilités :

logique métier

authentification

synchronisation

validation

services

Toute règle métier doit être centralisée dans cette couche.

Base serveur

Responsabilités :

stockage principal

sauvegarde

historique

gestion multi-utilisateurs

Base locale

Responsabilités :

fonctionnement hors connexion

cache

synchronisation

performances

6. Technologies retenues

Backend

PHP

Architecture MVC légère

API REST

Base de données

Serveur :

MariaDB

Client :

SQLite

Communication

HTTPS

JSON

REST

Frontend

HTML

CSS

JavaScript / TypeScript

Les standards du Web sont privilégiés.

L'utilisation de frameworks reste possible mais ne doit jamais conditionner l'architecture du projet.

> **Décision actée (ADR-0005, juillet 2026)** — Cette piste n'a pas été retenue : le projet reste en **JavaScript vanilla** (modules ES), sans framework.

7. Organisation fonctionnelle

Chaque domaine fonctionnel est développé sous forme de module.

Modules prévus :

Dashboard

Agenda

Rappels

Communication

Caméras

Domotique

Santé

Maison

Médias

Projets

Paramètres

Administration

Les modules communiquent uniquement via les services communs.

Ils ne doivent jamais dépendre directement les uns des autres.

8. Synchronisation

Le serveur constitue la référence principale.

La tablette conserve une copie locale.

Les synchronisations sont automatiques.

En cas de perte réseau :

la tablette continue de fonctionner ;

les modifications sont mémorisées ;

la synchronisation reprend automatiquement dès le retour de la connexion.

9. Sécurité

Authentification centralisée.

Communication exclusivement chiffrée.

Sauvegardes régulières.

Gestion des droits par utilisateur.

Aucune information sensible ne doit être stockée sans justification.

10. Dépendances

Principe fondamental :

Limiter au maximum les dépendances externes.

Chaque dépendance doit répondre à trois critères :

maturité

documentation

pérennité

Les standards ouverts sont systématiquement privilégiés.

11. Documentation

Le projet comprend notamment :

architecture

modèle de données

documentation API

conventions de développement

guide de déploiement

journal des décisions d'architecture (ADR)

Toute évolution importante devra être documentée avant sa mise en œuvre.

12. Évolutivité

L'application doit pouvoir évoluer sans remise en cause de son architecture.

Une évolution technologique doit toujours rester un choix.

Aucune technologie ne doit empêcher une migration future.

Les données devront toujours survivre au code.

13. Vision long terme

Hestia est conçu comme un patrimoine logiciel familial.

Son objectif est d'accompagner durablement ses utilisateurs en conservant leurs données, leurs habitudes et leurs services, indépendamment des évolutions technologiques.

Le projet privilégie la stabilité, la lisibilité et la transmission des connaissances plutôt que la sophistication technique.

La réussite du projet ne sera pas mesurée au nombre de fonctionnalités développées, mais à sa capacité à rester utile, compréhensible et maintenable pendant de nombreuses années.
