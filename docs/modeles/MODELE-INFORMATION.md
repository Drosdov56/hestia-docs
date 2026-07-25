# Modèle d’information — Hestia

**Statut :** Spécialisation conceptuelle (sous Constitution)  
**Emplacement :** `hestia-docs/docs/modeles/MODELE-INFORMATION.md`  
**Constitution :** [Document de réflexion architecturale](../constitution/HESTIA%20-%20DOCUMENT%20DE%20RÉFLEXION%20ARCHITECTURALE.md)  
**Glossaire :** [GLOSSAIRE.md](../gouvernance/GLOSSAIRE.md) — mapping cycle cognitif ↔ chaîne ci-dessous

---

## 1. Rôle du document

Ce document définit le **modèle d’information d’Hestia** : comment un signal issu d’un équipement devient une **information exploitable** par les applications Hestia.

Il répond à la question :

> À partir d’un signal provenant d’un équipement, comment Hestia construit une information exploitable par ses applications ?

Il **ne redéfinit pas** les fondements (Observation → … → Mémoire) : voir Constitution et glossaire. Il spécialise la **chaîne technique → information utile**.

Il constitue la **référence** de ce cycle opérationnel. Il est volontairement indépendant :

* des **fabricants** ;
* des **protocoles** (Zigbee, Matter, Z-Wave, BLE, Wi-Fi, etc.) ;
* des **applications** Hestia (Hub, Admin, notifications, etc.).

Le modèle d’information est également **indépendant de tout moteur décisionnel ou composant d’intelligence artificielle**.

Il forme le **socle commun** sur lequel peuvent s’appuyer :

* le moteur décisionnel Hestia ;
* les composants d’intelligence artificielle présents ou futurs ;
* les applications Hestia.

Ce modèle doit rester **valide** même si les mécanismes de décision évoluent, sont remplacés ou complétés.

Ce document **ne définit pas** :

* de scénarios ;
* de règles métier particulières ;
* de capteurs ou d’entités Home Assistant nommés ;
* d’algorithmes d’IA.

Il se limite à la **structure conceptuelle** de la transformation des données en informations.
---

## 2. Chaîne de transformation

Chaîne générale :

```
Capteur / équipement
        ↓
Signal brut
        ↓
Home Assistant
        ↓
Entités
        ↓
Sélection Hestia
        ↓
Modèle métier
        ↓
Moteur décisionnel
        ↓
Informations utiles
        ↓
Applications Hestia
```

| Étape | Rôle |
| ----- | ---- |
| Capteur / équipement | Produit un phénomène physique ou un geste (présence, ouverture, mesure, appui, etc.). |
| Signal brut | Manifestation technique côté protocole / passerelle, encore hors sémantique Hestia. |
| Home Assistant | Backend technique : découvre, agrège et expose le matériel sous forme exploitable par logiciel. |
| Entités | Unités d’exposition HA (états, mesures, commandes) — toujours techniques, pas encore « métier foyer ». |
| Sélection Hestia | Choix explicite de ce qui est retenu, ignoré ou réservé à l’administration (voir principes de sélection). |
| Modèle métier | Représentation Hestia des données retenues (identité équipement, pièce, typologie ci-dessous, durées, etc.). |
| Moteur décisionnel | Interprète une ou plusieurs données métier pour produire une lecture de situation. |
| Informations utiles | Résultat formulé, compréhensible, contextualisé — destination humaine ou applicative. |
| Applications Hestia | Affichent, notifient ou permettent d’agir à partir des informations utiles (et, pour Admin, de données d’administration). |

Chaque étape a une responsabilité distincte. Franchir une étape n’implique pas de conserver automatiquement tout ce qui précède.

---

## 3. Typologie des informations

### Événement

Information **ponctuelle** : quelque chose **vient de se produire**.

Exemples de nature (non liés à un matériel nommé) :

* présence détectée ;
* bouton pressé.

### État

Information **persistante** : quelque chose **est** dans une situation donnée, jusqu’à changement.

Exemples de nature :

* porte ouverte ;
* lumière allumée.

### Mesure

**Valeur numérique** (éventuellement avec unité), typiquement mise à jour dans le temps.

Exemples de nature :

* température ;
* humidité ;
* luminosité.

### Contexte

Information qui **enrichit** une décision ou une formulation, sans être nécessairement le signal principal.

Exemples de nature :

* pièce ;
* heure ;
* profil utilisateur ;
* niveau de batterie.

### Information utile

**Résultat de l’interprétation** réalisée par Hestia (moteur décisionnel et, le cas échéant, enrichissements ultérieurs).

Exemple de nature :

« Une personne est probablement présente dans le salon. »

L’information utile **n’existe ni** dans le capteur **ni** dans Home Assistant.  
Elle n’apparaît qu’après sélection, ancrage métier et interprétation dans Hestia.

Une même situation peut combiner plusieurs types (par exemple un **événement** de présence + un **contexte** de pièce + une **mesure** de luminosité → une **information utile**).

---

## 4. Principes de sélection

1. **Hestia ne conserve pas tout.** L’exposition technique (HA, protocole) est plus large que le modèle métier.
2. **Chaque donnée conservée doit avoir une justification** (métier, administration, technique de mapping / cycle de vie).
3. **Une donnée technique n’est pas automatiquement une donnée métier.** Identifiants, qualité radio, firmware, etc. restent techniques ou admin tant qu’une décision contraire n’est pas prise.
4. **Une information métier peut résulter de plusieurs sources.** Le modèle d’information admet la composition ; il n’impose pas « une entité HA = une information Hestia ».
5. **Ce qui n’est pas conservé est un choix**, pas un oubli : le non-usage doit pouvoir être documenté au niveau des référentiels concernés.

---

## 5. Durée de vie

Sans imposer de durées chiffrées, le modèle distingue :

| Notion | Sens |
| ------ | ---- |
| **Instantanée** | N’a de sens qu’au moment de la production (ex. signal de pressage consommé aussitôt). |
| **Temporaire** | Conservée le temps d’un raisonnement, d’une session ou d’une fenêtre courte, puis écartée. |
| **Persistante** | Représente l’état ou la valeur **courante** tant qu’elle n’est pas remplacée. |
| **Historisée** | Conservée dans le temps sous forme de série ou de journal, pour analyse, traçabilité ou décisions différées. |

Une même information peut changer de régime (par exemple un événement instantané aussi **historisé** pour traçabilité).

---

## 6. Visibilité

Destinataires possibles d’une information (non exclusifs) :

| Destinataire | Intention |
| ------------ | --------- |
| Moteur décisionnel | Entrée d’interprétation |
| Administration | Diagnostic, configuration, maintenance |
| Aidants | Suivi / compréhension selon droits |
| Utilisateur final | Informations utiles, jamais le détail technique du capteur comme finalité |
| Applications internes | Traitements Hestia (stockage, corrélation, notification, etc.) |

Une information peut être visible par **plusieurs** catégories, par **une seule**, ou par **aucune** en tant qu’affichage (tout en restant utilisée en interne).

La visibilité est une propriété du **modèle d’information** et des droits ; elle n’est pas déduite automatiquement du fait qu’une entité existe dans Home Assistant.

---

## 7. Traçabilité

Une **information métier** (et a fortiori une **information utile**) doit pouvoir être reliée à son origine, dans la mesure du possible :

* **équipement** (identité Hestia de l’équipement source, lorsque applicable) ;
* **entité Home Assistant** (ou équivalent technique) ayant fourni le signal retenu ;
* **horodatage** de production ou de dernière mise à jour ;
* **contexte de production** (pièce, nœud, autres éléments de contexte utilisés).

La traçabilité sert la confiance, le diagnostic et l’évolution des règles — sans exposer nécessairement toute la chaîne à l’utilisateur final.

---

## 8. Exploitation du modèle

Le modèle d’information est un **contrat de données et de sens** entre les couches Hestia. Il est **consommé**, non redéfini, par les composants qui raisonnent ou affichent.

### Principe d’architecture

> Les composants d’intelligence artificielle exploitent le modèle d’information Hestia mais n’en définissent ni la structure ni le contenu.

Précisions :

* l’IA **consomme** les informations produites selon ce modèle ;
* elle peut **enrichir** leur interprétation ou **proposer** des recommandations ;
* elle **ne modifie pas** le modèle d’information lui-même (typologie, chaîne, principes de sélection, traçabilité).

De même, le moteur décisionnel **s’appuie** sur le modèle métier et la typologie définis ici ; l’évolution de ses règles internes ne doit pas exiger de changer la nature de ce qu’est une information Hestia.

Les applications Hestia **présentaient** et **acheminent** les informations selon la visibilité et les droits ; elles ne substituent pas leur propre modèle de données au présent document.

---

## 9. Principes d’évolution

Le modèle doit permettre, **sans remise en cause de sa structure** :

* l’ajout de nouveaux types de capteurs / équipements ;
* l’ajout de nouvelles sources de données (protocoles, services, saisies humaines) ;
* l’amélioration des règles de décision ;
* l’introduction ou le remplacement de composants d’intelligence artificielle.

Ce qui peut évoluer librement : contenus métier, référentiels par équipement, règles du moteur, modèles d’IA.

Ce qui reste stable : la **chaîne de transformation**, la **typologie**, les **principes de sélection**, les notions de **durée de vie**, de **visibilité** et de **traçabilité**, et le principe selon lequel l’IA et le moteur **exploitent** le modèle sans en dicter la structure.

---

## Références (positionnement)

Ces documents sont complémentaires ; ils ne remplacent pas le présent modèle :

* Vision produit : `docs/FUNCTIONAL-VISION.md`
* Contrat documentaire capteurs : `docs/conception/capteurs/MODELE-CAPTEUR.md`
* Identité / cycle de vie équipements : `docs/conception/70-cycle-vie-equipements.md`
