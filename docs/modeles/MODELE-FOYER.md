# Modèle du foyer — Hestia

**Statut :** Spécialisation conceptuelle (sous Constitution)  
**Emplacement :** `hestia-docs/docs/modeles/MODELE-FOYER.md`  
**Constitution :** [Document de réflexion architecturale](../constitution/HESTIA%20-%20DOCUMENT%20DE%20RÉFLEXION%20ARCHITECTURALE.md)  
**Glossaire :** [GLOSSAIRE.md](../gouvernance/GLOSSAIRE.md)

---

## 1. Rôle du document

Ce document définit le **modèle du foyer d’Hestia** : la représentation métier des **personnes**, des **animaux**, de leurs **rôles** et de leurs **relations**.

Il répond à la question :

> Comment Hestia représente-t-il les personnes et les êtres vivants composant le foyer, indépendamment des moyens utilisés pour les identifier ou les observer ?

Il constitue la **référence** de ces concepts. Il est volontairement indépendant :

* des **technologies d’identification** ;
* des **protocoles** ;
* des **capteurs** ;
* des **applications** Hestia.

Ce document est **conceptuel**. Il décrit les **concepts métier**, pas leur implémentation.

Il est le **pendant** du [modèle d’habitat](MODELE-HABITAT.md) (environnement physique).  
L’**identification** (preuves, confiance, fusion) est traitée dans le [modèle d’identité](MODELE-IDENTITE.md), sans être redéfinie ici.

---

## 2. Principes

1. **Le foyer existe indépendamment de l’habitat.**  
   Les êtres vivants et leurs liens métier ne sont pas créés par la structure des pièces ; un déménagement peut changer d’habitation sans effacer le foyer.

2. **Une personne existe indépendamment des preuves permettant de l’identifier.**  
   La fiche personne précède et survit aux moyens techniques de reconnaissance.

3. **Une personne peut être connue sans être actuellement détectée.**  
   Être dans le modèle du foyer n’implique pas d’être « vu » à l’instant T.

4. **Une détection n’est jamais une personne.**  
   Un signal (présence, appareil, visage, etc.) peut contribuer à une identification ; il ne se substitue pas à l’entité métier Personne.

5. **L’identité opérationnelle (preuves, décision d’identification) est traitée dans `MODELE-IDENTITE.md`.**  
   Le présent document porte sur **qui compose le foyer**, pas sur **comment on le reconnaît**.

---

## 3. Les objets métier

### Foyer

**Entité** représentant l’ensemble des **êtres vivants** rattachés à une (ou plusieurs) habitation(s) dans le sens métier Hestia.

Le foyer est le point d’ancrage des personnes, animaux, rôles et relations — complémentaire de l’habitation, qui ancre l’espace.

### Personne

**Entité** représentant un **individu** connu d’Hestia.

Une personne possède une **identité métier** (qui elle est pour le foyer) **indépendante** de toute technologie d’observation ou d’identification.

### Animal

**Entité** représentant un **animal** vivant dans le foyer.

Le modèle permet d’en gérer **plusieurs**, sans imposer ici de typologie détaillée (chien, chat, etc.).

### Rôle

Fonction ou statut d’une personne **vis-à-vis du foyer**, dans un contexte donné.

Rôles principaux (liste ouverte, alignée sur le modèle d’identité) :

* résident ;
* aidant familial ;
* professionnel de santé ;
* intervenant régulier ;
* visiteur attendu ;
* visiteur occasionnel ;
* personne inconnue.

Une **même personne** peut **cumuler plusieurs rôles** selon le contexte (moment, situation, droits).

### Relation

Lien métier **entre personnes** (et, le cas échéant, entre une personne et un animal au sens « référent / animal du foyer » si le produit l’exige plus tard).

Exemples de nature (liste générique, non exhaustive) :

* conjoint ;
* parent ;
* enfant ;
* proche ;
* aidant principal ;
* référent médical.

Le modèle reste **générique** : il n’impose pas une taxonomie familiale complète.

---

## 4. Relations

Relations typiques (description textuelle, sans schéma UML) :

* un **foyer** contient une ou plusieurs **personnes** ;
* un **foyer** peut contenir zéro, un ou plusieurs **animaux** ;
* une **personne** peut avoir un ou plusieurs **rôles** (simultanés ou selon le contexte) ;
* plusieurs **personnes** peuvent être liées entre elles par des **relations** ;
* un **foyer** peut être rattaché à une ou plusieurs **habitations** (lien conceptuel avec le modèle d’habitat ; cardinalités précisées à l’implémentation).

Les cardinalités exactes pourront être précisées à l’implémentation ; le modèle conceptuel admet un foyer minimal (une personne) comme un foyer élargi (résidents, aidants, intervenants, visiteurs).

---

## 5. Présence dans le foyer

Une personne possède des **statuts métier** relatifs au foyer, **indépendants** de sa détection technique à un instant donné.

Exemples de nature :

* réside dans le foyer ;
* est attendue ;
* est actuellement présente ;
* est absente.

La **détermination** concrète de ces états (à partir de signaux, preuves, contexte) relève du [modèle d’information](MODELE-INFORMATION.md) et du [modèle de décision](MODELE-DECISION.md), éventuellement aidée par le [modèle d’identité](MODELE-IDENTITE.md).  
Le présent document fixe seulement que ces notions **existent** comme attributs / situations métier du foyer.

---

## 6. Évolution

Le modèle doit permettre, **sans remettre en cause les concepts fondamentaux** :

* plusieurs foyers ;
* le rattachement à plusieurs habitations ;
* l’évolution des rôles ;
* de nouvelles catégories de personnes ;
* de nouveaux types de relations ;
* l’enrichissement du traitement des animaux.

Ce qui reste stable : foyer comme ensemble des êtres vivants rattachés, personne distincte de toute détection, rôles contextuels, relations interpersonnelles, indépendance vis-à-vis des technologies d’identification.

---

## 7. Limites

Ce document **ne décrit pas** :

* les **équipements** ni la structure spatiale (voir [modèle d’habitat](MODELE-HABITAT.md)) ;
* les **preuves d’identité** ni la fusion d’indices (voir [modèle d’identité](MODELE-IDENTITE.md)) ;
* les **informations** utiles ou la typologie des données (voir [modèle d’information](MODELE-INFORMATION.md)) ;
* les **décisions** (voir [modèle de décision](MODELE-DECISION.md)) ;
* les **scénarios**.

Il décrit **uniquement** les **êtres vivants**, leurs **rôles** et leurs **relations** au sein du foyer.

---

## Références (positionnement)

Documents complémentaires — ils ne remplacent pas le présent modèle :

* [Modèle d’habitat](MODELE-HABITAT.md) — environnement physique (pendant)
* [Modèle d’identité](MODELE-IDENTITE.md) — preuves et identification
* [Modèle d’information](MODELE-INFORMATION.md)
* [Modèle de décision](MODELE-DECISION.md)
* Vision produit : `docs/FUNCTIONAL-VISION.md`
