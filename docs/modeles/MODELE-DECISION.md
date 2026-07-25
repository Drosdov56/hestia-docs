# Modèle de décision — Hestia

**Statut :** Spécialisation conceptuelle (sous Constitution)  
**Emplacement :** `hestia-docs/docs/modeles/MODELE-DECISION.md`  
**Constitution :** [Document de réflexion architecturale](../constitution/HESTIA%20-%20DOCUMENT%20DE%20RÉFLEXION%20ARCHITECTURALE.md)  
**Glossaire :** [GLOSSAIRE.md](../gouvernance/GLOSSAIRE.md)  
**Amont :** [MODELE-INFORMATION.md](MODELE-INFORMATION.md)

---

## 1. Rôle du document

Ce document définit le **modèle de décision d’Hestia** : comment des informations fiables peuvent conduire à une **conclusion**, une **action** ou une **recommandation**, de façon **compréhensible** et **explicable**.

Il répond à la question :

> Comment Hestia passe d’informations fiables à une décision compréhensible et explicable ?

Il **ne redéfinit pas** l’étape Décision de la Constitution ; il la spécialise.  
Il s’appuie sur le [modèle d’information](MODELE-INFORMATION.md) : les décisions portent sur des **informations** déjà sélectionnées et typées par Hestia, non sur des signaux bruts d’équipement.

Il constitue la **référence** de ce processus. Il est volontairement indépendant :

* des **capteurs** ;
* des **protocoles** ;
* de **Home Assistant** ;
* des **applications** Hestia ;
* du **moteur technique** utilisé (règles, intelligence artificielle, approche hybride, etc.).

Ce document est **conceptuel**. Il décrit la **logique attendue**, pas son implémentation.  
Il ne définit ni algorithme, ni scénario métier, ni règle de décision particulière, ni technologie d’IA.
---

## 2. Chaîne de décision

Chaîne générale :

```
Informations utiles
        ↓
Mise en contexte
        ↓
Corrélation
        ↓
Évaluation
        ↓
Décision
        ↓
Action ou recommandation
        ↓
Présentation aux applications
```

| Étape | Rôle |
| ----- | ---- |
| Informations utiles | Entrées du processus : informations déjà produites ou retenues selon le modèle d’information (événements, états, mesures, contexte, informations utiles déjà formulées). |
| Mise en contexte | Relier ces informations à un cadre (lieu, moment, acteurs, situation courante du foyer) sans encore conclure. |
| Corrélation | Mettre en regard plusieurs informations ou plusieurs observations dans le temps / l’espace. |
| Évaluation | Apprécier la cohérence, la fraîcheur et la pertinence de l’ensemble au regard de ce que l’on cherche à trancher. |
| Décision | Formuler une conclusion de nature déterministe, probabiliste, une recommandation ou une alerte (voir typologie). |
| Action ou recommandation | Traduire la décision en geste système (lorsqu’il est prévu) ou en suggestion / signal à destination humaine. |
| Présentation aux applications | Rendre la décision (et son explication) disponibles aux interfaces et services Hestia selon les droits et la visibilité. |

Les étapes peuvent être plus ou moins riches selon le cas ; aucune n’impose une implémentation particulière.

---

## 3. Types de décisions

Ces catégories décrivent la **nature** de la sortie. Elles sont **indépendantes** de leur implémentation (règles fixes, modèle statistique, IA, etc.).

### Décision déterministe

Pour une **même** situation d’entrée retenue, la conclusion est **toujours identique**.

### Décision probabiliste

Conclusion fondée sur **plusieurs indices convergents** ; elle exprime une lecture de situation plutôt qu’une certitude absolue.

### Recommandation

**Suggestion** formulée par Hestia, destinée à éclairer un humain ou un processus, sans imposer à elle seule une exécution.

### Alerte

**Signal** indiquant qu’une situation mérite une **attention particulière** (urgence, anomalie, risque perçu, etc.), selon les critères que l’implémentation fixera plus tard.

Une même chaîne peut aboutir à plusieurs types (par exemple une décision probabiliste présentée aussi comme recommandation).

---

## 4. Sources d’information

Une décision peut s’appuyer sur :

* plusieurs **équipements** / capteurs ;
* plusieurs **pièces** ou lieux ;
* plusieurs **instants** (fenêtre temporelle, historique) ;
* des **informations métier** déjà structurées ;
* du **contexte** (profils, horaires, état du foyer, etc.).

Une décision **ne repose pas nécessairement** sur une seule observation.  
Le modèle de décision admet la **composition** ; il n’impose pas « une information = une décision ».

---

## 5. Confiance

Toute décision peut être associée à un **niveau de confiance** conceptuel, par exemple :

* **certaine** — la situation retenue ne laisse pas de doute utile pour le cas traité ;
* **probable** — plusieurs indices convergent, sans certitude absolue ;
* **incertaine** — indices insuffisants, contradictoires ou trop partiels pour trancher net.

Ce document **ne définit aucun calcul de score**, aucun seuil numérique, aucune formule.  
Il fixe uniquement le **principe** : la confiance fait partie du modèle de décision et peut être communiquée avec la conclusion.

---

## 6. Explicabilité

**Principe fondamental :** toute décision **importante** doit pouvoir être **expliquée**.

Une explication vise la **compréhension humaine**, pas la justification technique détaillée de l’implémentation.

Elle doit permettre d’indiquer, par exemple :

* **quelles informations** ont été utilisées ;
* **quels éléments de contexte** ont été pris en compte ;
* **pourquoi** cette conclusion a été retenue (et, le cas échéant, pourquoi d’autres lectures ne l’ont pas emporté).

L’explicabilité s’applique quel que soit le moteur (règles, IA, hybride).  
Une décision non expliquable pour un cas important est hors de l’esprit de ce modèle.

---

## 7. Place de l’intelligence artificielle

* L’IA est un **composant possible** du moteur décisionnel, pas une obligation.
* Elle **exploite** le [modèle d’information](MODELE-INFORMATION.md) défini par Hestia.
* Elle **ne définit pas** les concepts métier (typologie des informations, identité des équipements, sens d’une « information utile », etc.).
* Elle peut **enrichir** l’analyse, **proposer** des interprétations ou des **recommandations**.
* Elle **n’est pas obligatoire** pour appliquer le présent modèle de décision.

Le modèle de décision reste **valable avec ou sans IA**.  
Changer de technologie d’IA, ou s’en passer, ne doit pas remettre en cause les principes de cette page.

---

## 8. Évolution

Le modèle doit permettre, **sans remise en cause de ses principes** :

* l’ajout de nouveaux capteurs / types d’équipements ;
* l’ajout de nouvelles sources d’information ;
* l’évolution des moteurs de décision (règles, hybrides, etc.) ;
* l’introduction de nouveaux modèles d’IA.

Ce qui peut évoluer librement : contenus métier, règles concrètes, scores éventuels, briques techniques.

Ce qui reste stable : la **chaîne de décision**, les **types de décisions**, le recours possible à **plusieurs sources**, la notion de **confiance**, l’exigence d’**explicabilité**, et la place de l’IA comme **composant optionnel** consommateur du modèle d’information — non comme auteur des concepts métier.

---

## Références (positionnement)

Documents complémentaires — ils ne remplacent pas le présent modèle :

* [Modèle d’information](MODELE-INFORMATION.md) — de la donnée à l’information
* Vision produit : `docs/FUNCTIONAL-VISION.md`
* Contrat documentaire capteurs : `docs/conception/capteurs/MODELE-CAPTEUR.md`
* Identité / cycle de vie équipements : `docs/conception/70-cycle-vie-equipements.md`
