# Architecture conceptuelle — Hestia

**Statut :** Carte des modèles (niveau 2) — **pas** une constitution  
**Emplacement :** `hestia-docs/docs/modeles/ARCHITECTURE-CONCEPTUELLE.md`  
**Constitution :** [Document de réflexion architecturale](../constitution/HESTIA%20-%20DOCUMENT%20DE%20RÉFLEXION%20ARCHITECTURALE.md)  
**Glossaire :** [GLOSSAIRE.md](../gouvernance/GLOSSAIRE.md)

---

## 1. Rôle du document

Ce document est la **carte d’ensemble** des modèles conceptuels d’Hestia.

Il répond à la question :

> Comment les différents modèles conceptuels collaborent-ils pour permettre à Hestia de comprendre son environnement et d’assister le foyer ?

Il **ne remplace pas** la Constitution. Il **explique les relations** entre MODELE-* et sert de point d’entrée vers ces spécialisations.

Il ne définit **aucun** nouveau concept fondateur, **aucune** règle métier, **aucune** implémentation technique.

---

## 2. Ancrage constitutionnel

Les principes fondateurs (assistant de vie, domotique = moyen, décisions à partir du modèle métier, indépendance technologique des modèles) sont portés par la **Constitution** et le **glossaire**.

Ce document ne les reformule pas.  
Vision opérationnelle : [FUNCTIONAL-VISION.md](../vision/FUNCTIONAL-VISION.md).
---

## 3. Les grands modèles

Chacun des modèles ci-dessous a un rôle distinct. Aucun ne se substitue à un autre.

### MODELE-HABITAT

Décrit l’**environnement physique** (habitation, bâtiment, niveau, pièce, zone, ouverture).  
L’**équipement** y apparaît uniquement comme objet **localisé** dans l’espace — sa **définition normative** (identité, cycle de vie, bindings, états, métadonnées, remplacement) est portée par le **Module 70** et **ADR-005**, non par ce modèle.

| | |
| - | - |
| **Consomme** | Aucun (structure spatiale autonome) |
| **Produit** | Structure de l’habitat |

### MODELE-FOYER

Décrit les **êtres vivants**, leurs **rôles** et leurs **relations**.

| | |
| - | - |
| **Consomme** | Aucun (structure sociale autonome) |
| **Produit** | Structure du foyer |

### MODELE-IDENTITE

Relie des **preuves** techniques ou contextuelles à des **identités métier** (personne ≠ preuve ≠ décision d’identification).  
*(Identité des **personnes**, distincte de l’identité des **équipements** — Module 70 / ADR-005.)*

| | |
| - | - |
| **Consomme** | Preuves d’identité |
| **Produit** | Hypothèses / conclusions d’identité (avec confiance) |

### MODELE-INFORMATION

Transforme des **données observées** (après sélection) en **informations métier**, jusqu’à l’**information utile**.

| | |
| - | - |
| **Consomme** | Observations retenues et contexte |
| **Produit** | Informations utiles (et typologie associée) |

### MODELE-DECISION

Transforme des **informations** en **conclusions**, **alertes** ou **recommandations** explicables.

| | |
| - | - |
| **Consomme** | Informations métier / utiles |
| **Produit** | Décisions explicables |

### MODELE-CAPTEUR

**Contrat documentaire** pour les référentiels par modèle de capteur (capacités natives, exposition HA observée, exploitation retenue).  
Il **ne définit pas** l’entité équipement Hestia : pour cela → **Module 70** / **ADR-005**.

| | |
| - | - |
| **Consomme** | Caractéristiques matérielles et exposition Home Assistant (observées) |
| **Produit** | Référentiel d’intégration documentaire par modèle de capteur |

### Équipements Hestia (hors « grand modèle » dédié)

Il n’existe **pas** de `MODELE-EQUIPEMENT.md`.  
La **Source of Truth** unique pour l’équipement Hestia est :

* [`70-cycle-vie-equipements.md`](../equipements/70-cycle-vie-equipements.md) (Module 70) — spécification normative ;
* [`ADR-005`](../adr/ADR-005-cycle-vie-equipements.md) — décisions structurantes.
Ce document d’architecture **ne recopie pas** leur contenu.

---

## 4. Relations entre les modèles

Dépendances **conceptuelles** (pas un graphe d’appel logiciel) :

* le **modèle d’information** s’appuie sur le **modèle d’habitat** (où cela se passe : pièce, zone, etc.) ;
* le **modèle d’information** s’appuie sur le **modèle du foyer** (qui est concerné : personnes, rôles, présence métier) ;
* le **modèle d’identité** **enrichit** le modèle d’information (rattacher une observation à une personne connue, avec confiance) ;
* le **modèle de décision** **exploite** les informations produites (y compris contexte habitat / foyer / identité) ;
* les **applications Hestia** consomment en priorité les **décisions** et **informations utiles**, **pas** les données brutes des capteurs ;
* le **modèle capteur** documente ce qui peut être **observé** et **retenu** pour un type de matériel, sans définir l’entité équipement (Module 70 / ADR-005), ni l’habitat, le foyer ou les décisions ;
* les **fiches équipement** runtime (identité, états, bindings, localisation métier) relèvent exclusivement du **Module 70** / **ADR-005**.

Habitat et foyer sont des **socles parallèles** : l’un structure l’espace, l’autre les êtres vivants. L’information et la décision les **composent** ; l’identité **personnes** relie des preuves aux membres du foyer ; l’identité **équipements** est hors de ce couple — Module 70.

---

## 5. Vue d’ensemble

Schéma **indicatif** des relations :

```text
                         Monde réel
                              │
              ┌───────────────┴───────────────┐
              │                               │
       MODELE-HABITAT                   MODELE-FOYER
       (espace physique)              (êtres vivants)
              │                               │
              │     Module 70 / ADR-005       │
              │     (équipement Hestia :      │
              │      identité, cycle de vie,  │
              │      bindings, localisation)  │
              │               │               │
              │         MODELE-CAPTEUR        │
              │    (référentiels documentaires│
              │     par modèle de capteur)    │
              │               │               │
              └───────────────┼───────────────┘
                              │
                      MODELE-IDENTITE
                   (preuves → personne)
                              │
                              ▼
                   MODELE-INFORMATION
              (observations + contexte
                 → informations utiles)
                              │
                              ▼
                     MODELE-DECISION
               (conclusions, alertes,
                  recommandations)
                              │
                              ▼
                   Applications Hestia
                    (Hub, Admin, notif…)
```

Lecture rapide : le monde réel est représenté par l’**habitat** et le **foyer** ; le **Module 70** définit l’**équipement** métier ; les **référentiels capteurs** documentent un type de matériel ; l’**identité** (personnes) rattache des preuves aux membres du foyer ; l’**information** puis la **décision** produisent ce que les **applications** présentent.

---

## 6. Évolutivité

Cette architecture permet d’ajouter, **sans remettre en cause les modèles conceptuels** :

* de nouveaux capteurs / types d’équipements ;
* de nouveaux protocoles ;
* de nouvelles sources d’information ;
* de nouvelles IA (consommatrices des modèles, non auteures de leur structure) ;
* de nouvelles applications.

Ce qui évolue : contenus, référentiels, règles, briques techniques.  
Ce qui reste stable : la **séparation des responsabilités** entre habitat, foyer, identité (personnes), information, décision, référentiels capteurs documentaires, et **Module 70** pour l’équipement.

---

## 7. Documentation associée

| Document | Rôle |
| -------- | ---- |
| [`FUNCTIONAL-VISION.md`](../vision/FUNCTIONAL-VISION.md) | Vision opérationnelle (sous Constitution) |
| [`MODELE-HABITAT.md`](MODELE-HABITAT.md) | Environnement physique du foyer |
| [`MODELE-FOYER.md`](MODELE-FOYER.md) | Personnes, animaux, rôles et relations |
| [`MODELE-IDENTITE.md`](MODELE-IDENTITE.md) | Preuves, fusion et conclusions d’identité **personne** |
| [`MODELE-INFORMATION.md`](MODELE-INFORMATION.md) | De la donnée sélectionnée à l’information utile |
| [`MODELE-DECISION.md`](MODELE-DECISION.md) | De l’information à une décision explicable |
| [`capteurs/MODELE-CAPTEUR.md`](capteurs/MODELE-CAPTEUR.md) | Contrat documentaire des référentiels de capteurs |
| [`70-cycle-vie-equipements.md`](../equipements/70-cycle-vie-equipements.md) | **SoT** normative de l’équipement Hestia (Module 70) |
| [`ADR-005`](../adr/ADR-005-cycle-vie-equipements.md) | Décisions structurantes du cycle de vie équipements |

**Parcours suggéré pour un nouveau développeur (ou une IA) :** Constitution → ce document → FUNCTIONAL-VISION → HABITAT + FOYER → Module 70 / ADR-005 (équipements) → INFORMATION → DECISION → IDENTITE (personnes, selon besoin) → MODELE-CAPTEUR puis référentiels concrets (`capteurs/…`).

Compléments (ADR, pilotage) : ADR-004, ROADMAP / BACKLOG `hestia-installer` — hors détail de cette carte.