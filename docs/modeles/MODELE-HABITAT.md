# Modèle d’habitat — Hestia

**Statut :** Spécialisation conceptuelle (sous Constitution)  
**Emplacement :** `hestia-docs/docs/modeles/MODELE-HABITAT.md`  
**Constitution :** [Document de réflexion architecturale](../constitution/HESTIA%20-%20DOCUMENT%20DE%20RÉFLEXION%20ARCHITECTURALE.md)  
**Glossaire :** [GLOSSAIRE.md](../gouvernance/GLOSSAIRE.md)

---

## 1. Rôle du document

Ce document définit le **modèle d’habitat d’Hestia** : la représentation métier de l’**environnement physique** dans lequel évolue le foyer.

Il répond à la question :

> Comment Hestia représente-t-il un habitat, indépendamment des technologies utilisées pour l’observer ?

Il constitue la **référence** de ces concepts. Il est volontairement indépendant :

* des **fabricants** ;
* des **protocoles** (Zigbee, Z-Wave, Matter, BLE, Wi-Fi, etc.) ;
* de **Home Assistant** ;
* des **applications** Hestia.

Ce document est **conceptuel**. Il décrit les **concepts métier**, pas leur implémentation.

Il est le **pendant** du [modèle du foyer](MODELE-FOYER.md) (personnes, animaux, rôles, relations).  
L’**équipement** n’y apparaît que comme **objet placé dans l’espace**. Sa définition normative (identité, cycle de vie, bindings, états, métadonnées, remplacement) est portée exclusivement par le [Module 70](../equipements/70-cycle-vie-equipements.md) et [ADR-005](../adr/ADR-005-cycle-vie-equipements.md) — sans redéfinition ici.

---

## 2. Principes

1. **Un habitat existe indépendamment des capteurs.**  
   La structure du lieu de vie précède et survit à l’instrumentation.

2. **Les équipements décrivent l’habitat mais ne le définissent pas.**  
   Observer une pièce n’est pas créer la pièce ; retirer tous les capteurs ne fait pas disparaître le salon dans le modèle métier.

3. **Un équipement peut être remplacé sans modifier le modèle de l’habitat.**  
   La cuisine, le niveau, la zone restent ; seul le lien vers une nouvelle fiche équipement change.

4. **Les informations métier ne dépendent pas d’un fabricant.**  
   Les noms et découpages (pièce, zone, ouverture) sont des choix Hestia / foyer, pas des libellés constructeur.

---

## 3. Les objets métier

### Habitation

**Entité racine** représentant le **lieu de vie** pris en charge par Hestia pour un foyer (au sens métier du déploiement).

C’est le point d’ancrage de la structure spatiale.

### Bâtiment

Permet de gérer **plusieurs bâtiments** rattachés à une même habitation lorsque c’est nécessaire (maison + dépendance, pavillon + garage séparé, etc.).

Une habitation simple peut n’avoir qu’un seul bâtiment.

### Niveau

Découpage vertical d’un bâtiment : étage, rez-de-chaussée, sous-sol, etc.

### Pièce

**Espace fonctionnel** du quotidien : cuisine, salon, chambre, salle de bain, etc.

La pièce est l’unité spatiale principale pour localiser la vie du foyer et y rattacher des équipements.

### Zone

**Subdivision facultative** d’une pièce.

Exemples de nature :

* coin repas ;
* entrée ;
* canapé ;
* lit.

Une zone est définie par le **modèle métier** (besoin du foyer), **pas** par un constructeur ni par une « area » technique tierce.

### Ouverture

Éléments de passage ou de baie : portes, fenêtres, baies vitrées, etc.

Les ouvertures appartiennent à la structure de l’habitat ; elles ne se confondent pas avec les équipements qui les observent ou les actionnent.

### Équipement

Tout **objet physique installé** dans l’habitat et connu d’Hestia en tant que fiche d’équipement.

Sous-catégories possibles (liste ouverte, évolutive) :

* capteur ;
* actionneur ;
* caméra ;
* passerelle ;
* appareil connecté.

Ces sous-catégories pourront être enrichies sans changer la place de l’équipement dans la hiérarchie spatiale.

---

## 4. Relations

Relations typiques (description textuelle, sans schéma UML) :

* une **habitation** contient un ou plusieurs **bâtiments** ;
* un **bâtiment** contient un ou plusieurs **niveaux** ;
* un **niveau** contient une ou plusieurs **pièces** ;
* une **pièce** peut contenir zéro, une ou plusieurs **zones** ;
* une **pièce** (et éventuellement une **zone**) peut contenir plusieurs **équipements** ;
* une **ouverture** est rattachée à la structure (pièce, limite entre pièces, façade, etc.) selon le besoin métier ;
* un **équipement** est affecté à **une seule localisation métier** à un instant donné.

Les cardinalités exactes (obligatoire / optionnel) pourront être précisées à l’implémentation ; le modèle conceptuel admet les habitats simples (une habitation, un bâtiment, un niveau, N pièces) comme les plus complexes.

---

## 5. Localisation

**Principe :** un équipement est associé à une **localisation métier** (en pratique : pièce, et zone si elle existe).

Cette localisation :

* **ne dépend pas** de Home Assistant ni d’un backend protocole ;
* est une **décision Hestia** (SoT métier) ;
* peut **changer** (déménagement d’un équipement d’une pièce à une autre) **sans** modifier l’**identité** de l’équipement.

Déplacer un équipement met à jour son rattachement spatial ; cela ne crée pas un nouvel habitat ni une nouvelle pièce par magie technique.

---

## 6. Évolution

Le modèle doit permettre, **sans remettre en cause les concepts existants** :

* l’ajout de nouvelles catégories d’équipements ;
* plusieurs habitations (si le produit l’exige plus tard) ;
* plusieurs bâtiments ;
* plusieurs niveaux ;
* de nouveaux types de localisation (au-delà de pièce / zone), introduits comme extensions cohérentes.

Ce qui reste stable : habitation comme racine du lieu de vie, découpage spatial métier, équipement localisé mais non définissant de l’espace, indépendance vis-à-vis des technologies d’observation.

---

## 7. Limites

Ce document **ne décrit pas** :

* les **personnes**, **animaux**, **rôles** et **relations** (voir [modèle du foyer](MODELE-FOYER.md)) ;
* les **preuves d’identité** (voir [modèle d’identité](MODELE-IDENTITE.md)) ;
* les **décisions** (voir [modèle de décision](MODELE-DECISION.md)) ;
* les **scénarios** ;
* les **informations** produites par les équipements (voir [modèle d’information](MODELE-INFORMATION.md) et référentiels capteurs).

Il décrit **uniquement** la **structure physique** de l’environnement manipulé par Hestia.

---

## Références (positionnement)

Documents complémentaires — ils ne remplacent pas le présent modèle :

* [Modèle du foyer](MODELE-FOYER.md) — êtres vivants et relations (pendant)
* [Modèle d’information](MODELE-INFORMATION.md)
* [Modèle d’identité](MODELE-IDENTITE.md)
* [Modèle de décision](MODELE-DECISION.md)
* Cycle de vie / identité des équipements (**SoT**) : [Module 70](../equipements/70-cycle-vie-equipements.md) · [ADR-005](../adr/ADR-005-cycle-vie-equipements.md)
* Vision produit : `docs/FUNCTIONAL-VISION.md`
