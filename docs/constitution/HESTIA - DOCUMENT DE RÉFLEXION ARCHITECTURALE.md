# DOCUMENT DE RÉFLEXION ARCHITECTURALE
## Hestia — Fondements conceptuels, invariants et décisions structurantes

**Version :** 1.0  
**Statut :** Validé (fond) — Document fondateur (pré-ADR) — **Constitution de l’écosystème**  
**Emplacement :** `hestia-docs/docs/constitution/`  
**Objectif :** Préserver le raisonnement ayant conduit aux choix architecturaux d'Hestia.

> Ce document est la **seule source de vérité conceptuelle** de l’écosystème.  
> Glossaire : [`../gouvernance/GLOSSAIRE.md`](../gouvernance/GLOSSAIRE.md) · Index : [`../INDEX.md`](../INDEX.md).

---

## Préambule

Ce document n'est pas une spécification technique.

Il n'est pas non plus un ADR.

Il documente la réflexion ayant conduit à la définition des fondements conceptuels d'Hestia.

Il constitue le niveau le plus élevé de l'architecture.

Tous les ADR, modèles de données, contrats logiciels et décisions techniques devront rester cohérents avec les principes définis ici.

L'objectif n'est pas de décrire comment Hestia fonctionne aujourd'hui.

L'objectif est d'expliquer pourquoi Hestia est conçu ainsi.

Cette nuance est fondamentale.

Les technologies évolueront.

Les protocoles évolueront.

Les frameworks évolueront.

Les composants logiciels pourront être remplacés.

Les principes fondateurs, eux, doivent rester stables pendant de nombreuses années.

---

## Pourquoi ce document existe

Au début du projet, l'architecture était naturellement pensée sous un angle technique.

Les premières questions étaient typiques d'un projet informatique :

- Comment communiquer avec Home Assistant ?
- Où stocker les données ?
- Faut-il utiliser MQTT ?
- Faut-il utiliser WebSocket ?
- Que doit contenir le mini-PC ?
- Que doit contenir le serveur ?

Ces questions sont légitimes.

Mais elles sont apparues rapidement comme étant des conséquences d'un problème plus profond.

En poursuivant la réflexion, il est devenu évident que la véritable question n'était pas :

> Comment transporter des données ?

mais :

> Que cherche réellement à faire Hestia ?

Cette prise de conscience a profondément modifié la manière de concevoir le projet.

---

## Une évolution de perspective

Au départ, Hestia pouvait être vu comme un système domotique évolué.

Cette vision était incomplète.

La domotique s'intéresse principalement :

- aux équipements,
- aux capteurs,
- aux automatismes,
- aux protocoles.

Hestia poursuit un objectif différent.

Hestia ne cherche pas à piloter une maison.

Hestia cherche à comprendre ce qui se passe dans un lieu de vie afin d'aider les personnes qui y vivent.

Cette différence paraît subtile.

Elle change pourtant entièrement l'architecture.

Une maison intelligente réagit à des événements.

Hestia cherche à construire une compréhension.

Cette compréhension constitue le véritable produit du système.

Les équipements ne sont que des moyens.

---

## Une architecture centrée sur la connaissance

Cette réflexion conduit à une première affirmation structurante.

> Hestia n'est pas un système de transport de données.

Hestia est un système de transformation de connaissances.

Autrement dit :

les capteurs ne produisent pas de la valeur.

Ils produisent des observations.

La valeur apparaît uniquement lorsque ces observations deviennent une compréhension exploitable.

Ce changement de point de vue entraîne plusieurs conséquences majeures.

Les composants logiciels cessent d'être le centre de l'architecture.

Ils deviennent les supports d'un processus cognitif.

L'architecture d'Hestia est donc d'abord une architecture de la connaissance.

Les logiciels viennent ensuite.

---

## Méthodologie de réflexion

Les principes décrits dans ce document ne résultent pas d'une intuition unique.

Ils sont le résultat d'un processus de confrontation volontaire entre plusieurs raisonnements indépendants.

Chaque sujet majeur a suivi la même méthode.

1. Formulation d'une hypothèse.

2. Recherche volontaire de contre-arguments.

3. Remise en question de l'hypothèse.

4. Recherche d'un modèle plus robuste.

5. Validation par confrontation sur des cas concrets.

6. Extraction des invariants.

Cette méthode a permis d'éviter un piège classique des projets techniques :

confondre une bonne idée avec une bonne architecture.

Une bonne architecture résiste à la contradiction.

Lorsqu'une idée ne résistait pas à l'analyse, elle était abandonnée ou reformulée.

Ce document conserve uniquement les conclusions ayant résisté à cette démarche.

---

## Les erreurs des premières approches

Plusieurs hypothèses initiales ont été volontairement abandonnées.

Leur conservation dans ce document est importante.

Comprendre pourquoi elles ont été rejetées évitera de les redécouvrir plusieurs années plus tard.

### Erreur n°1 : raisonner par composants

La première architecture pouvait se résumer ainsi :

Capteurs

↓

Home Assistant

↓

Agent

↓

Serveur

↓

Applications

Cette représentation décrit un flux technique.

Elle ne décrit absolument pas la finalité du système.

Elle explique comment circulent les informations.

Elle n'explique pas ce que devient leur sens.

Elle est donc insuffisante pour concevoir Hestia.

---

### Erreur n°2 : raisonner par protocoles

Une partie importante de la réflexion portait initialement sur :

- MQTT
- WebSocket
- HTTPS
- Broker
- API

Progressivement, il est apparu que ces discussions arrivaient beaucoup trop tôt.

Le protocole n'est jamais un objectif.

Il est la conséquence du modèle conceptuel.

Le bon protocole découlera naturellement des responsabilités définies.

L'inverse conduit presque toujours à une architecture fragile.

---

### Erreur n°3 : considérer les événements comme la valeur

Un capteur peut produire des milliers d'événements par jour.

Ces événements ne représentent pas nécessairement une connaissance utile.

Exemple :

Présence détectée.

Présence détectée.

Présence détectée.

Présence détectée.

Ces informations peuvent être indispensables pendant quelques secondes.

Elles deviennent rapidement du bruit.

À l'inverse :

> Le kinésithérapeute est venu aujourd'hui pendant 42 minutes.

constitue une information durable.

Cette distinction conduira plus tard à la séparation entre mémoire technique et mémoire utile.

---

### Erreur n°4 : confondre donnée et connaissance

Un détecteur de présence produit une donnée.

Cette donnée n'est pas encore une connaissance.

La connaissance apparaît seulement lorsque cette observation est replacée dans son contexte.

Exemple :

Observation :

Présence détectée.

Connaissance :

Une personne est probablement entrée.

Interprétation :

Il s'agit probablement du kinésithérapeute.

Décision :

Aucune action nécessaire.

Cette progression est le cœur du fonctionnement d'Hestia.

---

## La véritable question

Au terme de cette première phase de réflexion, la question fondatrice du projet a changé.

Elle n'est plus :

> Comment les composants communiquent-ils ?

Elle devient :

> Comment une observation du monde réel devient-elle une connaissance utile pour les habitants du foyer ?

Cette question servira désormais de fil conducteur à toute l'architecture.


## Les fondements conceptuels d'Hestia

---

### Une architecture centrée sur la transformation de l'information

La réflexion menée sur Hestia a progressivement conduit à une remise en question profonde de la manière de représenter le système.

Une architecture informatique classique décrit généralement des composants :

- capteurs ;
- services ;
- bases de données ;
- API ;
- interfaces.

Cette représentation est utile pour développer un logiciel.

Elle est insuffisante pour expliquer la finalité d'Hestia.

En effet, les composants ne constituent pas la raison d'être du système.

Ils ne sont que les instruments d'un processus beaucoup plus fondamental :

la transformation progressive d'une observation du monde réel en une connaissance utile, puis en une décision.

Autrement dit, Hestia n'est pas construit autour de composants.

Il est construit autour d'un cycle de transformation de l'information.

Les composants existent uniquement pour permettre ce cycle.

Cette inversion de perspective constitue probablement la décision conceptuelle la plus importante du projet.

---

### Le monde réel est la véritable source

Une erreur fréquente consiste à considérer le premier composant logiciel comme le début du système.

Dans Hestia, ce n'est pas le cas.

Le système commence avant tout logiciel.

Il commence dans le monde réel.

Exemples :

- une porte s'ouvre ;
- une personne entre ;
- une ampoule s'allume ;
- une chute survient ;
- un détecteur de fumée se déclenche ;
- un tensiomètre effectue une mesure.

Ces phénomènes existent indépendamment d'Hestia.

Le système ne crée pas ces événements.

Il tente simplement de les observer puis de les comprendre.

Cette distinction est importante.

Le monde reste toujours la référence ultime.

Hestia ne manipule jamais directement la réalité.

Il manipule uniquement des représentations de cette réalité.

---

### Le cycle de transformation de l'information

L'ensemble du fonctionnement d'Hestia peut être représenté sous la forme d'un pipeline cognitif.

```text
Monde réel
        │
        ▼
Observation
        │
        ▼
Connaissance
        │
        ▼
Interprétation
        │
        ▼
Décision
        │
        ▼
Action
        │
        ▼
Mémoire
```

Cette représentation n'est pas un schéma logiciel.

C'est un schéma conceptuel.

Chaque étape transforme la précédente.

À aucun moment une étape ne remplace la précédente.

Elle l'enrichit.

---

### Étape 1 — L'observation

L'observation représente la perception brute du monde.

Elle est volontairement pauvre.

Elle ne cherche pas à expliquer.

Elle constate.

Exemples :

- mouvement détecté ;
- température 22,4 °C ;
- porte ouverte ;
- badge détecté ;
- image capturée ;
- fréquence cardiaque mesurée.

Une observation ne possède pratiquement aucun contexte.

Elle répond uniquement à une question :

> Que s'est-il passé ?

Pas :

> Pourquoi ?

Ni :

> Qui ?

Ni :

> Est-ce important ?

Une observation est donc un fait brut.

---

### Étape 2 — La connaissance

La connaissance apparaît lorsqu'une observation est enrichie par le contexte disponible.

Exemple :

Observation :

> Mouvement détecté.

Connaissance :

> Une personne est entrée dans l'entrée.

Autre exemple :

Observation :

> Badge RFID reconnu.

Connaissance :

> Christophe est rentré au domicile.

Autre exemple :

Observation :

> Valeur reçue : 15,8 / 9,6.

Connaissance :

> Une mesure de tension vient d'être effectuée.

La connaissance répond désormais à une nouvelle question :

> Que savons-nous avec un niveau raisonnable de confiance ?

Ce n'est toujours pas une interprétation.

C'est une représentation enrichie du réel.

---

### Étape 3 — L'interprétation

L'interprétation constitue le cœur d'Hestia.

C'est ici que le système commence véritablement à raisonner.

Une interprétation consiste à relier plusieurs connaissances entre elles afin de produire une compréhension.

Exemples :

Le kinésithérapeute est arrivé.

Le rendez-vous était prévu.

La durée habituelle est de quarante minutes.

↓

Une séance de kinésithérapie est probablement en cours.

Autre exemple :

Aucun mouvement depuis six heures.

La personne est habituellement levée à cette heure.

Le planning indique un rendez-vous aujourd'hui.

↓

Situation inhabituelle nécessitant une attention.

Une interprétation ne décrit plus seulement le monde.

Elle tente de lui donner un sens.

---

### Étape 4 — La décision

Une fois la situation comprise, Hestia peut décider.

La décision ne consiste pas nécessairement à agir.

Ne rien faire est une décision.

La décision répond à une seule question :

> Quelle est la meilleure réponse compte tenu de ce que nous savons ?

Exemples :

- ne rien faire ;
- allumer une lumière ;
- prévenir un proche ;
- demander une confirmation ;
- enregistrer simplement l'information.

La décision appartient au domaine métier.

Elle constitue la véritable valeur produite par Hestia.

---

### Étape 5 — L'action

Une décision peut conduire à une action.

Une action peut être :

physique :

- ouvrir un volet ;
- allumer une lumière ;
- couper une alimentation.

ou informationnelle :

- envoyer une notification ;
- créer un rappel ;
- mettre à jour un agenda.

ou inexistante.

Toutes les décisions ne nécessitent pas une action.

---

### Étape 6 — La mémoire

La dernière étape est souvent négligée.

Elle est pourtant essentielle.

Tout ne mérite pas d'être mémorisé.

Cette affirmation constitue un principe fondateur d'Hestia.

Une architecture classique conserve généralement tout ce qu'elle reçoit.

Hestia adopte une approche différente.

La mémoire est une décision.

Elle n'est jamais automatique.

Le système choisit explicitement ce qui mérite d'être conservé.

Cette distinction conduit naturellement à deux formes de mémoire.

---

### Mémoire technique

La mémoire technique contient les informations nécessaires au fonctionnement du système.

Elle sert notamment :

- au rejeu après une coupure ;
- au diagnostic ;
- au débogage ;
- à la résilience.

Elle est temporaire.

Sa durée de conservation est volontairement limitée.

Elle ne constitue jamais une mémoire familiale.

---

### Mémoire utile

La mémoire utile représente ce que le foyer souhaite réellement conserver.

Elle contient des informations ayant une valeur durable.

Par exemple :

- visite du kinésithérapeute ;
- anniversaire ;
- changement d'habitude ;
- épisode important ;
- événement médical significatif ;
- décision prise par le système.

Contrairement à la mémoire technique, cette mémoire est intentionnelle.

Une information n'y entre qu'après une décision explicite.

Cette distinction permet d'éviter qu'Hestia ne devienne un simple accumulateur de données techniques.

---

### Trois natures fondamentales de données

Cette réflexion conduit à distinguer trois familles de données.

#### Les connaissances de référence

Ce sont les éléments relativement stables du système.

Exemples :

- membres du foyer ;
- pièces ;
- habitat ;
- équipements ;
- règles ;
- préférences.

Ces informations changent peu.

Elles structurent le système.

---

#### Les événements

Les événements décrivent ce qui arrive.

Ils sont généralement nombreux.

Très volatils.

Souvent peu intéressants individuellement.

Ils alimentent le raisonnement.

Ils ne constituent pas nécessairement la mémoire.

---

#### Les états dérivés

Les états dérivés représentent la meilleure compréhension actuelle du système.

Exemples :

- Christophe est actuellement à la maison.
- La porte est probablement restée ouverte.
- Une séance de soins est en cours.

Ces états ne sont pas observés directement.

Ils sont calculés.

Ils évoluent continuellement.

Ils constituent une représentation du monde à un instant donné.

Cette distinction entre connaissances de référence, événements et états dérivés est l'un des piliers conceptuels d'Hestia.

Elle évite de confondre le monde réel, son historique et sa compréhension actuelle.

---

### La fiche d'identité de l'information

Au cours des réflexions, une idée importante a émergé.

Une information ne devrait pas être caractérisée uniquement par son contenu.

Elle devrait également porter une identité.

Chaque élément manipulé par Hestia est ainsi susceptible de posséder plusieurs métadonnées indépendantes.

Les quatre premières sont considérées comme fondatrices.

**Nature**

Observation.

Connaissance.

Interprétation.

Décision.

Action.

Mémoire.

**Origine**

Capteur.

Home Assistant.

IA locale.

Utilisateur.

Application.

Serveur.

**Sensibilité**

Neutre.

Sensible.

Donnée de santé.

Information biométrique.

Autre classification pertinente.

**Finalité**

Confort.

Sécurité.

Suivi de fragilité.

Maintenance.

Autre usage déclaré.

Ces attributs ne sont pas figés.

Ils peuvent évoluer au cours du cycle de transformation.

La sensibilité d'une information n'est pas uniquement déterminée par sa valeur brute.

Elle dépend également de son contexte, de sa finalité et des traitements appliqués.

Cette approche permet d'éviter une classification simpliste des données et prépare Hestia à des usages très différents sans modifier son architecture fondamentale.

---

## Les composants d'Hestia et leurs responsabilités

---

### Les composants ne définissent pas l'architecture

Une conséquence directe de ces principes est qu'il devient possible de séparer deux notions souvent confondues :

- l'architecture conceptuelle ;
- l'architecture logicielle.

L'architecture conceptuelle décrit comment une observation devient une connaissance puis une décision.

L'architecture logicielle décrit quels composants permettent cette transformation.

Cette distinction est volontaire.

Elle permet de faire évoluer l'implémentation sans remettre en cause le modèle conceptuel.

Autrement dit :

les composants servent l'architecture.

Ils ne la définissent pas.

---

### Les quatre grands acteurs

L'architecture actuelle repose sur quatre acteurs principaux.

```text
Monde réel
      │
      ▼
Home Assistant
      │
      ▼
Agent Hestia
      │
      ▼
Serveur Hestia
      │
      ▼
Applications
```

Il est essentiel de comprendre que cette représentation décrit une responsabilité.

Elle ne décrit pas nécessairement un découpage physique.

Demain :

- plusieurs Agents pourront exister ;
- plusieurs Applications également ;
- certaines fonctions pourront être déplacées.

Les responsabilités, elles, doivent rester identiques.

---

### Home Assistant

#### Son rôle

Home Assistant constitue exclusivement la couche d'intégration du monde physique.

Il assure notamment :

- découverte des équipements ;
- communication avec les protocoles domotiques ;
- intégration Zigbee ;
- intégration Z-Wave ;
- intégration Bluetooth ;
- intégration Wi-Fi ;
- intégration Matter ;
- gestion des actionneurs ;
- abstraction matérielle.

Autrement dit :

Home Assistant sait dialoguer avec les équipements.

Il ne connaît rien du métier Hestia.

---

### Ce que Home Assistant ne doit jamais faire

Cette réflexion a conduit à l'un des invariants les plus importants du projet.

Home Assistant ne doit jamais devenir un moteur métier.

En conséquence :

- aucune logique métier ;
- aucune décision métier ;
- aucune règle familiale ;
- aucune interprétation ;
- aucune mémoire métier.

Les automatisations éventuellement présentes dans Home Assistant doivent rester exclusivement techniques.

Exemples acceptables :

- heartbeat ;
- watchdog ;
- redémarrage d'intégration ;
- supervision technique.

Exemples interdits :

- prévenir le fils si la mère ne s'est pas levée ;
- déterminer qu'une visite est inhabituelle ;
- décider qu'une alerte médicale doit être envoyée.

Cette gouvernance devra être explicitement documentée afin d'éviter une dérive naturelle du projet.

Il est toujours plus simple d'ajouter rapidement une automatisation dans Home Assistant.

À long terme, cette facilité conduit à une dispersion de la logique métier.

Cette dérive doit être empêchée.

---

### Pourquoi Home Assistant est volontairement remplaçable

Un autre principe est apparu progressivement.

Hestia ne doit jamais dépendre d'un logiciel particulier.

Aujourd'hui, Home Assistant est la meilleure solution connue.

Demain, une autre pourra apparaître.

L'architecture doit permettre ce remplacement sans remettre en cause le reste du système.

Autrement dit :

Hestia dépend d'un rôle.

Pas d'un produit.

Le rôle est :

> fournir des observations du monde réel.

Peu importe l'outil.

---

### L'Agent Hestia

L'Agent constitue le premier composant réellement spécifique à Hestia.

Il est installé au plus près du monde réel.

Son rôle est souvent mal compris.

L'Agent n'est pas un mini-serveur.

Il n'est pas non plus un simple proxy.

Il constitue le runtime local d'Hestia.

---

### Les responsabilités de l'Agent

L'Agent possède plusieurs responsabilités clairement identifiées.

#### Acquisition

Recevoir les observations produites par Home Assistant ou d'autres sources.

---

#### Normalisation

Transformer les observations techniques en événements compréhensibles par Hestia.

Exemple :

```text
binary_sensor.motion_entry = on
```

devient

```text
Présence détectée dans l'entrée.
```

Le vocabulaire domotique disparaît.

Le vocabulaire métier apparaît.

---

#### Validation

Contrôler :

- cohérence ;
- format ;
- horodatage ;
- qualité minimale.

L'Agent garantit que le serveur ne reçoit jamais des informations incohérentes.

---

#### Résilience

L'Agent doit continuer à fonctionner même en cas :

- de coupure Internet ;
- d'indisponibilité temporaire du serveur ;
- de redémarrage.

Les observations sont conservées temporairement afin d'être rejouées ultérieurement.

Cette mémoire reste technique.

Elle n'est jamais une mémoire métier.

---

#### Exécution locale

Certaines transformations doivent rester locales.

Exemples :

- traitement vidéo ;
- reconnaissance faciale ;
- analyse audio ;
- autres traitements nécessitant un accès au flux brut.

La raison n'est pas uniquement la performance.

C'est également une question de souveraineté des données.

---

### Ce que l'Agent ne doit pas devenir

Au cours des réflexions, une tentation est apparue.

Faire de l'Agent un deuxième cerveau.

Cette idée a été rejetée.

Pourquoi ?

Parce qu'elle conduirait progressivement à deux systèmes de décision :

- un local ;
- un central.

La cohérence deviendrait extrêmement difficile.

L'Agent peut réagir.

Il peut préparer.

Il peut transformer.

Il ne doit pas interpréter le contexte global du foyer.

---

### Une nuance importante

Cette affirmation ne signifie pas que l'Agent est dépourvu d'intelligence.

Il peut parfaitement exécuter des règles locales immédiates.

Exemples :

- allumer une lumière ;
- déclencher une sirène incendie ;
- couper une alimentation électrique.

Ces réactions ne nécessitent aucun contexte historique.

En revanche :

déterminer qu'une situation est inhabituelle.

croiser les habitudes.

prendre une décision familiale.

reste du domaine du serveur.

---

### Le Serveur Hestia

Le Serveur constitue le cœur métier du système.

Il ne dialogue pas directement avec les équipements.

Il ne voit jamais le monde physique.

Il reçoit uniquement une représentation normalisée de ce monde.

Cette séparation est fondamentale.

---

### Les responsabilités du Serveur

Le Serveur possède quatre missions principales.

#### Construire la connaissance

Fusionner les observations.

Créer une compréhension cohérente.

---

#### Décider

Appliquer les règles métier.

Prendre les décisions.

Prioriser.

Déterminer les actions.

---

#### Mémoriser

Décider explicitement ce qui mérite d'être conservé.

Constituer la mémoire familiale.

Construire progressivement l'histoire du foyer.

---

#### Publier

Distribuer cette connaissance :

- applications ;
- tableaux de bord ;
- notifications ;
- API.

Le serveur ne diffuse jamais les observations brutes.

Il diffuse une compréhension.

---

### Le Serveur comme Source of Truth

L'expression Source of Truth est fréquemment utilisée en architecture.

Elle mérite ici une définition précise.

Le serveur n'est pas la vérité du monde.

Le monde reste toujours la référence.

Le serveur constitue la **Source of Truth métier**.

Autrement dit :

lorsqu'un composant souhaite connaître :

- les membres du foyer ;
- les habitudes ;
- les décisions ;
- les états actuels ;
- l'historique utile ;

une seule réponse existe.

Le serveur.

Cette unicité est indispensable.

Elle évite les divergences entre plusieurs représentations concurrentes.

---

### Les Applications

Les applications constituent le dernier maillon.

Elles ne produisent pratiquement aucune logique métier.

Leur mission est de rendre la connaissance accessible.

Une application Hestia n'est pas censée reconstruire une interprétation.

Elle présente celle produite par le serveur.

Cette règle simplifie fortement les interfaces.

Toutes les applications partagent la même compréhension du foyer.

---

### Une conséquence importante

Cette répartition des responsabilités permet de remplacer un composant sans remettre en cause les autres.

Exemples :

Remplacer Home Assistant.

↓

Aucun impact sur le modèle métier.

---

Créer une nouvelle application mobile.

↓

Aucun impact sur les décisions.

---

Changer le protocole de communication.

↓

Aucun impact sur la compréhension.

---

Cette indépendance constitue un objectif majeur de l'architecture.

Elle garantit sa pérennité.

---

### Synthèse

À l'issue de cette réflexion, chaque composant possède une responsabilité claire.

**Home Assistant**

Observe le monde matériel.

Jamais le métier.

---

**Agent**

Acquiert.

Normalise.

Valide.

Résiste.

Exécute les traitements locaux.

---

**Serveur**

Comprend.

Décide.

Mémorise.

Diffuse.

---

**Applications**

Présentent.

Interagissent.

N'interprètent pas.

---

Cette séparation constitue l'un des fondements les plus solides de l'architecture d'Hestia.

Elle permet de maintenir une responsabilité unique par composant tout en conservant une vision globale cohérente du système.

## Les grandes décisions architecturales

---

### Les décisions présentées dans ce chapitre

Le modèle conceptuel d'Hestia ainsi que les responsabilités des principaux composants étant établis, il reste cependant une série de décisions qui ne relèvent ni du modèle métier, ni directement de l'implémentation.

Ces décisions structurent durablement l'architecture.

Elles résultent d'une longue phase de réflexion contradictoire au cours de laquelle plusieurs hypothèses ont été envisagées, challengées puis parfois abandonnées.

Le présent chapitre ne décrit pas uniquement les décisions retenues.

Il explique également pourquoi elles ont été retenues.

---

### Décision n°1 — L'architecture est cognitive avant d'être logicielle

Cette décision est probablement la plus importante de tout le projet.

Au début des réflexions, Hestia était spontanément représenté comme un assemblage de composants :

- Home Assistant
- Agent
- Serveur
- Applications

Cette représentation était correcte techniquement.

Elle s'est révélée insuffisante conceptuellement.

En effet, les composants n'expliquent pas la raison d'être du système.

Ils n'expliquent que sa construction.

Progressivement, une autre représentation est apparue beaucoup plus robuste.

```text
Monde réel

↓

Observation

↓

Connaissance

↓

Interprétation

↓

Décision

↓

Action

↓

Mémoire
```

Cette représentation possède une propriété essentielle.

Elle reste valable même si tous les composants logiciels changent.

L'architecture devient indépendante de son implémentation.

Cette décision constitue le socle de toutes les autres.

---

### Décision n°2 — Les composants ne manipulent pas des données. Ils manipulent des connaissances.

Cette nuance peut sembler purement sémantique.

Elle ne l'est pas.

Une donnée est une valeur.

Une connaissance est une donnée replacée dans un contexte.

Exemple.

Le capteur indique :

```
binary_sensor.motion = on
```

Il s'agit d'une donnée.

Après normalisation :

> Présence détectée dans l'entrée.

Il s'agit déjà d'une connaissance.

Après enrichissement :

> Christophe est probablement rentré.

La connaissance devient plus riche.

Enfin :

> Rien d'anormal.

Le système possède désormais une compréhension.

Autrement dit, Hestia manipule essentiellement des connaissances.

Les données brutes ne représentent qu'une matière première.

---

### Décision n°3 — La mémoire est une décision

Une base de données traditionnelle conserve généralement tout.

Cette stratégie a été rejetée.

Pourquoi ?

Parce qu'Hestia ne poursuit pas un objectif statistique.

Il poursuit un objectif d'assistance familiale.

Prenons un détecteur de présence.

Une journée peut produire plusieurs milliers de changements d'état.

Cette accumulation ne crée aucune valeur.

À l'inverse :

> Le kinésithérapeute est venu aujourd'hui.

constitue une véritable connaissance.

Cette réflexion conduit à une idée simple.

Le stockage ne doit jamais être automatique.

La mémorisation est un acte volontaire du système.

Le système décide explicitement :

- ce qui mérite d'être conservé ;
- pendant combien de temps ;
- pour quelle finalité.

Cette approche simplifie :

- le stockage ;
- les performances ;
- la protection des données ;
- la compréhension des utilisateurs.

---

### Décision n°4 — Deux mémoires indépendantes

Cette décision découle directement de la précédente.

Hestia distingue deux mémoires.

#### La mémoire technique

Elle existe uniquement pour permettre le fonctionnement du système.

Elle contient par exemple :

- événements bruts ;
- files d'attente ;
- rejeux ;
- journaux techniques.

Sa durée de vie est courte.

Elle est automatiquement purgée.

Elle ne possède aucune valeur familiale.

---

#### La mémoire utile

La mémoire utile constitue le patrimoine informationnel du foyer.

Elle contient uniquement des informations ayant une valeur durable.

Exemples.

- visite du kinésithérapeute ;
- changement d'habitude ;
- décision importante ;
- événement notable ;
- historique pertinent.

Cette mémoire n'est jamais alimentée automatiquement.

Elle résulte toujours d'une décision.

Cette séparation constitue l'un des principes les plus originaux d'Hestia.

---

### Décision n°5 — Une information possède une identité

L'une des avancées majeures de cette réflexion est l'abandon d'une vision purement "contenu".

Une information ne se résume pas à sa valeur.

Elle possède une identité.

Cette identité accompagne l'information tout au long de son cycle de vie.

Les quatre premiers attributs sont considérés comme fondateurs.

#### Nature

Que représente cette information ?

Observation.

Connaissance.

Interprétation.

Décision.

Action.

Mémoire.

---

#### Origine

D'où provient-elle ?

Capteur.

Home Assistant.

Agent.

IA locale.

Serveur.

Utilisateur.

Application.

---

#### Sensibilité

Quel niveau de protection nécessite-t-elle ?

Neutre.

Sensible.

Biométrique.

Donnée de santé.

Autre.

---

#### Finalité

Pourquoi cette information existe-t-elle ?

Confort.

Sécurité.

Suivi de fragilité.

Maintenance.

Administration.

Autre.

---

Cette fiche d'identité constitue désormais un invariant architectural.

Elle n'est volontairement pas limitée à ces quatre attributs.

D'autres métadonnées pourront être ajoutées dans le futur :

- niveau de confiance ;
- traçabilité ;
- temporalité ;
- durée de conservation ;
- version ;
- justification.

Le modèle est pensé pour être extensible.

---

### Décision n°6 — La sensibilité n'est pas une propriété intrinsèque

Cette décision mérite une attention particulière.

Elle est apparue tardivement pendant les réflexions.

Une même observation peut avoir une signification totalement différente selon son contexte.

Exemple.

Présence détectée.

Chez un adolescent.

Cette information est essentiellement une donnée de confort.

Chez une personne suivie pour perte d'autonomie.

Cette même observation peut participer à une évaluation de santé.

Le capteur n'a pas changé.

La donnée brute n'a pas changé.

La finalité du système, elle, a changé.

Il est donc impossible de définir une classification universelle de la sensibilité uniquement à partir de la donnée.

La sensibilité dépend également :

- de la finalité déclarée du système ;
- du contexte ;
- des traitements appliqués ;
- des inférences produites.

Cette décision évite une sur-classification inutile tout en permettant un haut niveau de protection lorsque cela devient nécessaire.

---

### Décision n°7 — La souveraineté de la donnée

La réflexion a progressivement dépassé les seules considérations de stockage.

Une nouvelle notion est apparue :

la souveraineté de la donnée.

Cette notion répond à une question simple.

Quelle est la forme la plus sensible d'une information ?

Exemple.

Une caméra produit une image.

Cette image peut être :

conservée ;

supprimée ;

transformée en embedding ;

transformée en identité ;

transformée en événement.

Chaque transformation modifie profondément la nature de l'information.

L'architecture d'Hestia adopte donc un principe fort.

Les formes les plus brutes et les plus sensibles des données doivent rester aussi proches que possible de leur lieu de production.

Autrement dit.

Le mini-PC constitue le premier espace de souveraineté.

Le serveur reçoit prioritairement des transformations.

Pas des flux bruts.

Ce principe protège :

- la vie privée ;
- les performances ;
- la bande passante ;
- la résilience.

Il prépare également Hestia à des évolutions réglementaires futures.

---

### Décision n°8 — Deux niveaux d'intelligence artificielle

La réflexion a montré que parler de "l'IA" est insuffisant.

Deux usages profondément différents existent.

#### IA de perception

Elle intervient très tôt.

Son rôle consiste à transformer des observations.

Exemples.

- reconnaissance faciale ;
- détection de chute ;
- transcription audio ;
- reconnaissance d'objets.

Elle répond essentiellement à la question :

> Que suis-je en train d'observer ?

Cette IA est naturellement exécutée au plus près des capteurs.

---

#### IA de compréhension

Elle intervient beaucoup plus tard.

Elle manipule déjà des connaissances.

Elle répond à une autre question.

> Que signifie cette situation ?

Elle croise :

- historique ;
- habitudes ;
- agenda ;
- contexte ;
- préférences.

Elle produit une interprétation.

Cette séparation évite de confondre perception et raisonnement.

---

### Décision n°9 — Trois natures de décisions

Toutes les décisions ne possèdent pas les mêmes contraintes.

Une classification s'est progressivement imposée.

#### Réaction

Décision immédiate.

Très faible latence.

Peu ou pas de contexte.

Exemples.

Allumer une lumière.

Déclencher une sirène.

Couper une alimentation.

---

#### Interprétation

Décision nécessitant du contexte.

Historique.

Connaissances.

Habitudes.

Présence.

Exemple.

Déterminer qu'une visite est inhabituelle.

---

#### Planification

Décision tournée vers le futur.

Conseil.

Prévision.

Organisation.

Accompagnement.

Cette classification décrit la nature des décisions.

Elle ne décrit pas leur criticité.

Une réaction peut être anodine.

Ou vitale.

Le mécanisme reste identique.

---

### Décision n°10 — La pérennité avant l'élégance technique

Tout au long des réflexions, un principe est revenu de manière constante.

Une architecture est conçue pour durer.

Pas pour être à la mode.

En conséquence.

Chaque dépendance supplémentaire doit être justifiée.

Chaque protocole doit démontrer sa valeur.

Chaque technologie doit pouvoir être remplacée.

Le choix final d'une implémentation devra toujours privilégier :

- la simplicité d'exploitation ;
- la robustesse ;
- la compréhension ;
- la maintenabilité ;
- la pérennité.

Ce principe dépasse largement le choix d'un protocole ou d'une base de données.

Il constitue une philosophie générale du projet.

---

### Ce qui a été explicitement rejeté

Les réflexions ont également permis d'écarter plusieurs orientations.

Leur conservation est volontaire.

Elles ne doivent pas être redécouvertes dans quelques années.

Ont notamment été rejetés :

- Home Assistant comme moteur métier ;
- plusieurs Sources of Truth concurrentes ;
- un serveur recevant des flux vidéo bruts ;
- une mémoire conservant systématiquement tous les événements ;
- une architecture définie par ses composants plutôt que par son cycle cognitif ;
- une classification figée de la sensibilité indépendante du contexte ;
- une dépendance forte à une technologie particulière.

Ces rejets sont aussi importants que les décisions retenues.

Ils définissent les frontières de l'architecture.

## Les invariants fondateurs

---

### Qu'est-ce qu'un invariant ?

Au cours de la conception d'un système complexe, toutes les décisions n'ont pas la même importance.

Certaines concernent l'implémentation.

D'autres concernent l'organisation du code.

D'autres encore découlent des contraintes technologiques du moment.

Ces décisions pourront évoluer au fil du temps.

À l'inverse, certaines propositions sont suffisamment fondamentales pour que toute évolution future doive rester compatible avec elles.

Ces propositions sont appelées **invariants**.

Un invariant n'est pas une règle technique.

C'est une propriété fondamentale de l'architecture.

Modifier un invariant revient à redéfinir l'identité même d'Hestia.

Les invariants présentés ci-dessous constituent donc le socle sur lequel devront s'appuyer les futurs ADR.

---

### IF-001 — Hestia est un assistant de vie, pas une application domotique.

La finalité d'Hestia est l'accompagnement des personnes.

La domotique constitue un moyen.

Jamais un objectif.

En conséquence :

- les équipements n'ont pas de valeur en eux-mêmes ;
- les protocoles ne sont jamais au centre de l'architecture ;
- la qualité d'Hestia se mesure à la qualité de l'assistance rendue, pas au nombre d'équipements compatibles.

Cet invariant doit guider chaque décision future.

Lorsqu'un arbitrage oppose une logique domotique à une logique d'assistance, la seconde prévaut systématiquement.

---

### IF-002 — Le monde réel demeure la référence ultime.

Aucun composant logiciel ne détient la vérité.

Le seul référentiel absolu est le monde réel.

Les composants ne manipulent que des représentations plus ou moins fidèles de cette réalité.

Cette distinction impose plusieurs conséquences.

Une connaissance peut être :

- incomplète ;
- erronée ;
- périmée ;
- contradictoire.

Le système doit donc être conçu pour gérer l'incertitude plutôt que pour supposer une connaissance parfaite.

---

### IF-003 — L'architecture est gouvernée par un cycle de transformation de l'information.

Le cycle :

```text
Monde réel
    ↓
Observation
    ↓
Connaissance
    ↓
Interprétation
    ↓
Décision
    ↓
Action
    ↓
Mémoire
```

constitue le modèle conceptuel de référence.

Toute évolution future devra pouvoir s'y rattacher.

Ajouter un composant est possible.

Supprimer une étape du cycle reviendrait à modifier la nature même d'Hestia.

---

### IF-004 — Chaque composant possède une responsabilité unique.

Les responsabilités sont réparties de manière explicite.

**Home Assistant**

Observer le monde physique.

**Agent**

Acquérir.

Normaliser.

Valider.

Résister.

Prétraiter localement.

**Serveur**

Comprendre.

Décider.

Mémoriser.

Publier.

**Applications**

Présenter.

Interagir.

Jamais interpréter.

Toute évolution future devra préserver cette séparation.

Une responsabilité ne doit jamais être dupliquée entre plusieurs composants.

---

### IF-005 — Le serveur constitue l'unique Source of Truth métier.

Les représentations métier ne doivent jamais exister simultanément dans plusieurs composants.

Une seule version de référence est autorisée.

Cette unicité simplifie :

- la cohérence ;
- les synchronisations ;
- les évolutions ;
- les audits ;
- la compréhension du système.

Le serveur ne constitue pas la vérité du monde.

Il constitue la meilleure représentation métier connue du monde.

---

### IF-006 — Une information possède une identité.

Toute information manipulée par Hestia peut être caractérisée indépendamment de son contenu.

Les attributs minimaux sont :

- nature ;
- origine ;
- sensibilité ;
- finalité.

Cette identité accompagne l'information durant tout son cycle de vie.

Elle permet :

- une gouvernance cohérente ;
- une protection adaptée ;
- une traçabilité ;
- une meilleure compréhension du système.

Cette identité est extensible.

Elle n'est volontairement pas figée.

---

### IF-007 — La mémoire est un choix.

La conservation d'une information n'est jamais automatique.

Une information est mémorisée uniquement lorsqu'elle possède une valeur durable pour le foyer ou pour le fonctionnement du système.

Ce principe conduit naturellement à distinguer :

- mémoire technique ;
- mémoire utile.

Confondre ces deux mémoires reviendrait à transformer Hestia en simple collecteur de données.

---

### IF-008 — Les données les plus sensibles restent au plus près de leur origine.

La souveraineté de la donnée constitue un principe fondateur.

Lorsque cela est techniquement possible :

- les flux bruts restent locaux ;
- les traitements les plus sensibles sont réalisés localement ;
- le serveur reçoit prioritairement des informations déjà transformées.

Ce principe protège simultanément :

- la vie privée ;
- les performances ;
- la bande passante ;
- la résilience.

---

### IF-009 — L'intelligence artificielle intervient à plusieurs niveaux.

Hestia distingue explicitement :

**l'IA de perception**

qui transforme une observation en connaissance ;

et

**l'IA de compréhension**

qui transforme plusieurs connaissances en interprétation.

Cette distinction évite de considérer l'IA comme un bloc unique.

Elle facilite également le remplacement ou l'amélioration indépendante des différents modèles.

---

### IF-010 — Les décisions appartiennent au métier.

Une décision n'est jamais dictée par un protocole.

Ni par un capteur.

Ni par une interface.

Une décision résulte exclusivement de la compréhension métier de la situation.

Cette séparation garantit que les interfaces, les protocoles ou les équipements peuvent évoluer sans modifier les règles de décision.

---

### IF-011 — Les choix technologiques sont remplaçables.

Aucune technologie ne doit être indispensable.

Les logiciels utilisés aujourd'hui répondent à un besoin actuel.

Ils ne définissent pas l'identité du projet.

Le remplacement d'un composant ne doit jamais remettre en cause les invariants précédents.

Cet invariant protège Hestia contre l'obsolescence technologique.

---

### IF-012 — La simplicité est une exigence architecturale.

La simplicité prime sur la sophistication.

La complexité ne doit jamais être recherchée pour elle-même.

Chaque nouveau composant.

Chaque nouveau protocole.

Chaque nouvelle dépendance.

Chaque nouvelle abstraction.

doit démontrer une valeur supérieure au coût qu'il introduit.

Une architecture simple est :

- plus robuste ;
- plus maintenable ;
- plus compréhensible ;
- plus durable.

La simplicité constitue donc un objectif permanent.

---

### Les invariants comme critères d'évaluation

Ces invariants ne sont pas destinés à être lus uniquement lors de la conception initiale.

Ils doivent devenir des outils de décision.

Avant toute évolution importante, une question simple devra être posée.

> Cette évolution respecte-t-elle les invariants fondateurs d'Hestia ?

Si la réponse est positive, l'évolution est probablement cohérente.

Si plusieurs invariants sont remis en cause, deux possibilités existent.

La première est que l'évolution soit inadaptée.

La seconde est qu'Hestia soit en train de changer de nature.

Dans ce second cas, la modification ne relève plus d'un simple ADR.

Elle nécessite une révision explicite du présent document.

---

### Hiérarchie documentaire

Ce document définit les principes.

Les ADR traduisent ces principes en décisions architecturales.

Les spécifications traduisent les ADR en solutions techniques.

Les implémentations traduisent les spécifications en code.

La hiérarchie est donc la suivante :

```text
Réflexion architecturale
        ↓
Invariants fondateurs
        ↓
ADR
        ↓
Architecture logicielle
        ↓
Spécifications
        ↓
Implémentation
```

Cette hiérarchie garantit que le code reste toujours rattaché à une intention clairement exprimée.

Elle évite que les choix techniques ne deviennent progressivement les véritables décideurs de l'architecture.

---

### Conclusion des invariants

Les invariants présentés dans ce chapitre ne décrivent pas une implémentation.

Ils décrivent l'identité d'Hestia.

Ils constituent le contrat que le projet passe avec lui-même.

Tout pourra évoluer :

- les composants ;
- les protocoles ;
- les bases de données ;
- les modèles d'intelligence artificielle ;
- les interfaces.

Mais tant que ces invariants demeureront vrais, Hestia conservera sa cohérence architecturale et sa philosophie fondatrice.

## Éprouver l'architecture

---

### Pourquoi éprouver l'architecture ?

Une architecture ne peut pas être considérée comme robuste uniquement parce qu'elle paraît élégante.

De nombreuses architectures sont cohérentes sur le papier et deviennent rapidement incohérentes lorsqu'elles sont confrontées au monde réel.

L'objectif de ce chapitre est donc simple.

Soumettre les principes fondateurs d'Hestia à des situations concrètes.

Chaque scénario répond à trois questions :

- Les responsabilités restent-elles clairement réparties ?
- Les invariants sont-ils respectés ?
- La solution obtenue est-elle cohérente avec la finalité d'Hestia ?

Cette démarche ne cherche pas à valider une implémentation.

Elle cherche à valider le modèle conceptuel.

---

### Scénario 1 — Une personne rentre au domicile

#### Situation

Une personne franchit la porte d'entrée.

Le détecteur d'ouverture signale l'ouverture.

Le détecteur de présence confirme un mouvement.

Le badge RFID est reconnu.

#### Analyse

Le monde réel produit plusieurs observations indépendantes.

Home Assistant collecte ces événements.

L'Agent les normalise.

Le serveur les rapproche.

Il conclut :

> Christophe est probablement rentré au domicile.

La décision est :

> Aucune action particulière.

La mémoire technique conserve les événements pendant la durée nécessaire.

La mémoire utile ne conserve éventuellement que l'heure d'arrivée si cette information possède une valeur métier.

#### Validation

Les responsabilités sont respectées.

Aucune logique métier n'est exécutée localement.

Le serveur demeure la Source of Truth.

---

### Scénario 2 — Coupure Internet

#### Situation

La connexion Internet disparaît.

Les capteurs continuent à fonctionner.

#### Analyse

Home Assistant continue d'observer.

L'Agent continue de recevoir les observations.

Il les place dans sa mémoire technique.

Aucune donnée n'est perdue.

Le serveur devient temporairement indisponible.

Dès le retour de la connexion, les observations sont retransmises.

Le serveur reconstitue la chronologie.

#### Validation

La mémoire technique joue pleinement son rôle.

La mémoire métier reste unique.

La coupure réseau ne remet pas en cause la cohérence globale.

---

### Scénario 3 — Défaillance complète du serveur

#### Situation

Le serveur Hestia est indisponible pendant plusieurs heures.

#### Analyse

Le mini-PC continue à fonctionner.

Les traitements locaux continuent.

Les réactions immédiates restent possibles.

Exemples :

- détection incendie ;
- éclairage automatique ;
- sirène locale.

En revanche :

- aucune interprétation globale ;
- aucune synchronisation familiale ;
- aucune mise à jour de la mémoire utile.

À son retour, le serveur retrouve progressivement une vision cohérente grâce aux événements conservés temporairement.

#### Validation

Les responsabilités restent inchangées.

Le système se dégrade de manière contrôlée.

---

### Scénario 4 — Défaillance du mini-PC

#### Situation

Le mini-PC cesse brutalement de fonctionner.

#### Analyse

Home Assistant devient indisponible.

L'Agent disparaît.

Le serveur ne reçoit plus aucune observation.

La représentation du monde cesse progressivement d'être actualisée.

Le système ne produit pas de fausses connaissances.

Il reconnaît explicitement la perte d'information.

Cette différence est essentielle.

Une absence d'observation n'est jamais interprétée comme une observation d'absence.

#### Validation

L'incertitude est assumée.

Le système ne fabrique pas de conclusions injustifiées.

---

### Scénario 5 — Détection d'une chute

#### Situation

Une caméra détecte une chute.

#### Analyse

La vidéo reste locale.

Le modèle de perception analyse les images.

Le serveur ne reçoit jamais le flux vidéo.

Il reçoit uniquement une connaissance.

Par exemple :

> Chute détectée avec une confiance de 97 %.

Le serveur croise :

- identité probable ;
- localisation ;
- heure ;
- historique ;
- contexte.

Il décide ensuite des actions appropriées.

#### Validation

La souveraineté des données est respectée.

La séparation IA de perception / IA de compréhension est respectée.

---

### Scénario 6 — Visite du kinésithérapeute

#### Situation

Une personne identifiée comme le kinésithérapeute entre dans le logement.

#### Analyse

L'information brute est :

> Une personne est entrée.

Après enrichissement :

> Le kinésithérapeute est arrivé.

Après interprétation :

> La séance prévue est probablement en cours.

Après décision :

Aucune alerte.

Après mémoire :

La visite est enregistrée comme événement utile.

#### Validation

La mémoire conserve la connaissance.

Pas les milliers d'événements techniques ayant permis cette conclusion.

---

### Scénario 7 — Évolution progressive des habitudes

#### Situation

Depuis plusieurs semaines, les heures de lever deviennent de plus en plus tardives.

Aucune journée n'est inquiétante.

La tendance globale, en revanche, est significative.

#### Analyse

Aucun capteur ne peut produire directement cette connaissance.

Elle résulte de l'analyse historique.

Le serveur construit progressivement cette compréhension.

Il peut conclure :

> Modification durable des habitudes matinales.

Cette conclusion pourra être :

simplement mémorisée ;

ou signalée.

#### Validation

La compréhension dépasse largement les observations individuelles.

Le serveur remplit pleinement son rôle.

---

### Scénario 8 — Capteur défectueux

#### Situation

Un détecteur reste bloqué sur "présence".

#### Analyse

Les autres observations deviennent contradictoires.

Le système détecte une incohérence.

Il ne conclut pas :

> Présence permanente.

Il conclut :

> Le niveau de confiance diminue.

Le modèle de connaissance intègre donc l'incertitude.

#### Validation

Les observations ne sont jamais considérées comme infaillibles.

---

### Scénario 9 — Nouvelle technologie

#### Situation

Home Assistant est remplacé.

#### Analyse

L'architecture ne change pas.

Le rôle reste identique.

Observer.

Normaliser.

Transmettre.

Les décisions métier restent inchangées.

#### Validation

L'architecture dépend des responsabilités.

Pas des logiciels.

---

### Scénario 10 — Nouvel usage du foyer

#### Situation

Le même système est installé dans une résidence seniors.

#### Analyse

Les observations restent identiques.

La finalité change.

Les niveaux de sensibilité évoluent.

Les règles métier évoluent.

Les décisions évoluent.

L'architecture, elle, demeure identique.

#### Validation

La séparation entre information et finalité démontre toute sa valeur.

---

### Scénario 11 — Deux informations contradictoires

#### Situation

Le badge RFID indique que Marie est présente.

Le téléphone de Marie est géolocalisé à plusieurs kilomètres.

#### Analyse

Le système ne cherche pas immédiatement à désigner une information comme fausse.

Il conserve les deux connaissances.

Il leur attribue un niveau de confiance.

L'interprétation devient :

> Informations contradictoires nécessitant une confirmation.

#### Validation

Hestia gère des connaissances imparfaites.

Pas des certitudes absolues.

---

### Scénario 12 — L'IA progresse

#### Situation

Un nouveau modèle d'analyse vidéo devient disponible.

#### Analyse

Seule l'IA de perception est remplacée.

Les connaissances produites restent compatibles.

Aucune règle métier ne change.

Aucune application ne change.

#### Validation

La séparation entre perception et compréhension garantit la pérennité de l'architecture.

---

### Enseignements

Ces scénarios montrent que les invariants définis précédemment ne sont pas des principes théoriques.

Ils permettent de résoudre des situations très différentes tout en conservant une architecture cohérente.

Chaque scénario valide plusieurs propriétés essentielles :

- les responsabilités demeurent clairement réparties ;
- la Source of Truth reste unique ;
- les composants restent remplaçables ;
- les traitements sensibles demeurent locaux lorsque cela est possible ;
- la mémoire conserve uniquement ce qui possède une valeur durable ;
- l'incertitude est explicitement prise en compte ;
- la compréhension du monde prime toujours sur l'accumulation d'événements techniques.

L'architecture est ainsi capable d'évoluer sans perdre son identité.

Cette capacité constitue probablement son principal critère de qualité.

## Gouvernance architecturale

---

### Pourquoi une gouvernance ?

Une architecture ne devient pas incohérente du jour au lendemain.

Elle dérive progressivement.

Une exception devient une habitude.

Une simplification temporaire devient une dépendance permanente.

Une logique métier apparaît dans un composant qui ne devrait pas en contenir.

Quelques années plus tard, personne ne sait plus réellement où se trouve la responsabilité de chaque décision.

La gouvernance architecturale a pour objectif d'éviter cette dérive.

Elle ne cherche pas à ralentir le développement.

Elle cherche à préserver la cohérence du projet.

---

### Une architecture est une succession de décisions

Chaque évolution importante doit être considérée comme une décision architecturale.

Une décision n'est jamais neutre.

Elle modifie :

- les responsabilités ;
- les dépendances ;
- les flux d'information ;
- la compréhension du système.

Une décision mérite donc d'être explicitée lorsqu'elle modifie durablement l'architecture.

C'est précisément le rôle des ADR.

---

### Quand écrire un ADR ?

Un ADR devient nécessaire lorsqu'une évolution répond à au moins un des critères suivants :

- création d'une nouvelle responsabilité ;
- modification des responsabilités existantes ;
- introduction d'une nouvelle dépendance structurante ;
- modification du cycle de transformation de l'information ;
- impact sur les invariants fondateurs ;
- changement durable des règles de gouvernance.

À l'inverse, une simple optimisation de code, un changement de bibliothèque ou une amélioration de performances ne nécessitent généralement pas d'ADR.

---

### Les invariants avant la technique

Toute réflexion architecturale doit suivre le même ordre.

1. Quel besoin métier cherche-t-on à satisfaire ?

2. Quels invariants sont concernés ?

3. Quel impact sur les responsabilités ?

4. Quelle solution conceptuelle répond au besoin ?

5. Quelle implémentation est la plus adaptée ?

L'ordre inverse conduit presque toujours à une architecture pilotée par la technologie plutôt que par les besoins.

---

### Les décisions rejetées font partie de l'architecture

Une architecture ne se résume pas aux décisions retenues.

Les décisions rejetées possèdent elles aussi une valeur.

Elles évitent de rouvrir régulièrement les mêmes débats.

Chaque ADR important devrait donc conserver :

- les options envisagées ;
- les avantages de chacune ;
- les limites identifiées ;
- les raisons ayant conduit au choix final.

Cette pratique permet de préserver le raisonnement et pas uniquement la conclusion.

---

### Les exceptions

Toute architecture connaît des exceptions.

Une exception ne constitue pas nécessairement une erreur.

En revanche, une exception doit toujours être :

- identifiée ;
- documentée ;
- justifiée ;
- limitée dans son périmètre.

Une exception silencieuse finit presque toujours par devenir une règle implicite.

---

### L'évolution des invariants

Les invariants ne sont pas immuables.

Ils peuvent évoluer.

En revanche, leur modification doit rester exceptionnelle.

Modifier un invariant revient à modifier l'identité du projet.

Une telle décision nécessite :

- une réflexion spécifique ;
- une justification complète ;
- une validation explicite.

Un ADR ne peut pas modifier seul un invariant.

Le présent document doit alors être révisé.

---

### La dette architecturale

Comme la dette technique, la dette architecturale existe.

Elle apparaît lorsqu'une décision est volontairement prise en contradiction avec les principes établis.

Cette dette peut être acceptable.

À condition :

- d'être identifiée ;
- d'être temporaire ;
- d'être suivie.

La pire dette architecturale est celle dont personne n'a conscience.

---

### Une architecture vivante

Ce document n'a pas vocation à figer Hestia.

Au contraire.

Il fournit un cadre permettant au projet d'évoluer sans perdre sa cohérence.

Une architecture vivante n'est pas une architecture qui change sans cesse.

C'est une architecture capable d'intégrer le changement tout en conservant son identité.

---

### Conclusion générale

La réflexion menée autour d'Hestia a progressivement déplacé le centre de gravité du projet.

Au départ, la question semblait être :

> Comment construire un système domotique moderne ?

Cette formulation s'est révélée insuffisante.

Le véritable objectif d'Hestia est d'accompagner les personnes dans leur vie quotidienne.

La technologie n'est qu'un moyen au service de cette finalité.

Cette prise de conscience a conduit à plusieurs choix structurants.

Hestia ne s'organise pas autour de composants mais autour d'un cycle de transformation de l'information.

Les observations deviennent des connaissances.

Les connaissances deviennent des interprétations.

Les interprétations conduisent à des décisions.

Les décisions produisent des actions et, lorsque cela est pertinent, alimentent une mémoire utile.

Chaque composant possède une responsabilité clairement définie.

Le serveur constitue l'unique Source of Truth métier.

Les traitements les plus sensibles restent au plus près de leur origine.

La mémoire n'est plus considérée comme un stockage systématique mais comme un choix délibéré.

Les données sont caractérisées non seulement par leur contenu, mais également par leur nature, leur origine, leur sensibilité et leur finalité.

Enfin, les choix technologiques sont explicitement séparés des principes architecturaux.

Cette séparation garantit que l'évolution des outils ne remettra pas en cause l'identité du projet.

Ce document ne décrit donc pas une implémentation.

Il décrit une manière de penser Hestia.

Il constitue le niveau de référence à partir duquel les ADR, les spécifications techniques et les développements futurs devront être élaborés.

Son objectif n'est pas d'empêcher l'évolution.

Son objectif est de garantir que cette évolution reste fidèle à la vision fondatrice du projet.

