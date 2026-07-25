# Modèle d’identité — Hestia

**Statut :** Spécialisation conceptuelle (sous Constitution)  
**Emplacement :** `hestia-docs/docs/modeles/MODELE-IDENTITE.md`  
**Constitution :** [Document de réflexion architecturale](../constitution/HESTIA%20-%20DOCUMENT%20DE%20RÉFLEXION%20ARCHITECTURALE.md)  
**Glossaire :** [GLOSSAIRE.md](../gouvernance/GLOSSAIRE.md)  
**Note :** identité des **personnes** — distincte de l’identité des **équipements** ([Module 70](../equipements/70-cycle-vie-equipements.md) / [ADR-005](../adr/ADR-005-cycle-vie-equipements.md)).

---

## 1. Rôle du document

Ce document définit le **modèle d’identité d’Hestia** : comment Hestia **représente une personne** et comment il peut **établir une identification** à partir d’indices issus de sources variées.

Il répond à la question :

> Comment Hestia représente-t-il une personne et comment établit-il son identité à partir d’indices provenant de différentes sources ?

Il constitue la **référence** de ces concepts. Il est volontairement indépendant :

* des **fabricants** ;
* des **protocoles** ;
* des **caméras** et autres capteurs ;
* des **modèles d’IA** ;
* des **applications** Hestia.

Ce document est **conceptuel**. Il décrit les **concepts métier**, pas leur implémentation.  
Il ne définit ni algorithme, ni scénario métier, ni technologie d’identification privilégiée.

Il s’articule avec le [modèle d’information](MODELE-INFORMATION.md) (preuves et conclusions comme informations) et le [modèle de décision](MODELE-DECISION.md) (corrélation, confiance, explicabilité).

---

## 2. Principes

1. **Une identité Hestia n’est pas une détection.**  
   Une détection (présence, mouvement, apparition d’un appareil, etc.) est un **signal** ou une **preuve éventuelle**. L’identité est une **représentation métier** d’une personne connue — ou non — du foyer.

2. **Une identité est une représentation métier d’une personne.**  
   Elle porte un sens pour le foyer (qui est cette personne, quel est son rôle), indépendamment du moyen technique qui a permis de la rattacher à une situation.

3. **Hestia ne confond jamais :**
   * une **personne** (entité métier) ;
   * une **preuve d’identité** (indice technique ou contextuel) ;
   * une **décision d’identification** (conclusion, avec niveau de confiance et explication).

Ces trois niveaux restent distincts dans tout le modèle.

---

## 3. Les personnes

Catégories métier principales (liste ouverte, non exhaustive) :

| Catégorie | Intention |
| --------- | --------- |
| Résident | Personne vivant au foyer / au centre du suivi |
| Aidant familial | Proche qui accompagne |
| Professionnel de santé | Intervenant soignant |
| Intervenant régulier | Prestataire ou aide récurrente |
| Visiteur attendu | Personne dont la venue est anticipée |
| Visiteur occasionnel | Personne de passage, non structurée comme « attendue » |
| Personne inconnue | Aucune identité métier retenue avec une confiance suffisante |

Ces catégories sont **métier**. Elles sont **indépendantes** de la manière dont une personne est reconnue (visage, téléphone, badge, calendrier, etc.).

Une même personne peut, selon le contexte, être présentée sous des angles différents (droits, visibilité) sans changer son identité métier de fond.

---

## 4. Les preuves d’identité

Une **preuve** est un **indice** pouvant **contribuer** à identifier une personne.  
Ce n’est ni la personne, ni la décision d’identification.

Exemples de natures de preuves (aucune n’est obligatoire, aucune n’est exclusive) :

* reconnaissance faciale ;
* smartphone connu ;
* montre connectée ;
* badge NFC ;
* empreinte vocale ;
* calendrier (rendez-vous attendu) ;
* géolocalisation autorisée ;
* présence simultanée d’autres personnes ;
* historique récent.

Principes :

* **Aucune preuve n’est obligatoire** pour que le modèle d’identité existe.
* Une preuve peut être **forte**, **faible**, **absente** ou **contradictoire** selon le cas.
* Le modèle doit **accepter de nouvelles preuves** demain sans changer la notion de « personne » ni de « décision d’identification ».

Les preuves techniques restent séparables de l’identité métier (voir vie privée).

---

## 5. Fusion des preuves

Principe général :

* **Plusieurs preuves** peuvent être **combinées**.
* Une preuve peut **renforcer** ou **affaiblir** une hypothèse d’identité.
* L’identification est une **corrélation d’informations**, pas la simple lecture d’un capteur unique.

Ce document **ne définit aucun algorithme** de fusion, aucun poids, aucune règle numérique.  
Il fixe uniquement que la conclusion d’identité est le résultat d’une **mise en regard** de preuves et de contexte, dans l’esprit du modèle de décision.

---

## 6. Niveau de confiance

Une conclusion d’identification peut s’exprimer, par exemple, comme :

* **identité confirmée** ;
* **identité probable** ;
* **identité possible** ;
* **identité inconnue**.

Ce document **ne définit aucun calcul de score**.  
Il fixe le principe : toute identification retenue pour un usage important s’accompagne d’un **niveau de confiance** conceptuel, communicable et distinct de la catégorie de personne.

---

## 7. Explicabilité

**Toute identification importante doit pouvoir être expliquée.**

Une explication vise la **compréhension**, pas le détail interne des modèles ou des capteurs.

Elle doit pouvoir indiquer **quelles preuves** (et quels éléments de contexte) ont contribué à la conclusion.

Exemple **conceptuel** d’explication (non lié à une implémentation) :

* visage reconnu ;
* rendez-vous prévu à cette heure ;
* arrivée par l’entrée principale.

L’explicabilité s’applique avec ou sans IA, et quel que soit l’ensemble de preuves utilisé.

---

## 8. Respect de la vie privée

Principes de conception du modèle :

1. **Minimisation** des données conservées — ne retenir que ce qui est nécessaire aux finalités déclarées.
2. **Séparation** entre **preuves techniques** et **identité métier** — les traces brutes ne se confondent pas avec la fiche personne.
3. **Possibilité d’utiliser Hestia avec ou sans reconnaissance faciale** — le modèle d’identité ne dépend pas d’une preuve particulière.
4. **Aucune dépendance à une technologie unique** — l’absence d’un canal (caméra, Bluetooth, NFC, etc.) ne rend pas le concept d’identité invalide.

Le modèle doit rester compatible avec **différents niveaux d’exigence** en matière de confidentialité (foyer, aidants, cadres réglementaires éventuels), sans imposer ici de politique concrète.

---

## 9. Évolutivité

Le modèle doit permettre d’**ajouter de nouveaux moyens d’identification** sans modifier les concepts métier :

* personne ;
* preuve ;
* décision d’identification ;
* confiance ;
* explicabilité ;
* catégories de personnes.

Le document reste valable si, demain, Hestia utilise d’autres capteurs, d’autres sources contextuelles ou d’autres modèles d’IA : ceux-ci **fournissent ou interprètent des preuves** ; ils **ne redéfinissent pas** ce qu’est une identité Hestia.

---

## Références (positionnement)

Documents complémentaires — ils ne remplacent pas le présent modèle :

* [Modèle d’information](MODELE-INFORMATION.md)
* [Modèle de décision](MODELE-DECISION.md)
* Vision produit : `docs/FUNCTIONAL-VISION.md`
* Acteurs / catégories ouvertes du foyer : voir FUNCTIONAL-VISION (liste d’acteurs)
