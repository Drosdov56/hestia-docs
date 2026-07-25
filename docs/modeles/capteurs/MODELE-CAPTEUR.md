# Modèle de référentiel capteur — Hestia

**Statut :** Référence officielle (contrat documentaire capteur)  
**Emplacement :** `hestia-docs/docs/modeles/capteurs/MODELE-CAPTEUR.md`  
**Constitution :** [Document de réflexion architecturale](../../constitution/HESTIA%20-%20DOCUMENT%20DE%20RÉFLEXION%20ARCHITECTURALE.md)  
**Équipements (SoT) :** [Module 70](../../equipements/70-cycle-vie-equipements.md)

Ce document ne décrit **aucun** capteur particulier.  
Il définit le **contrat documentaire** que doit respecter tout référentiel de capteur Hestia (`SNZB-06P24.md`, futurs `SNZB-02.md`, détecteurs d’ouverture, fumée, fuite d’eau, etc.).

---

## 1. Objectif du référentiel

Un référentiel de capteur est la **fiche de référence** d’un modèle matériel dans l’écosystème Hestia.

Il sert à :

* **identifier** le matériel (fabricant, modèle, technologie, alimentation) ;
* **décrire** l’ensemble de ses **capacités natives**, indépendamment de l’usage Hestia ;
* **documenter** ce que Home Assistant (et le backend protocole, ex. Zigbee2MQTT) **expose réellement** sur l’installation ;
* **définir** quelles informations deviennent des **données Hestia** (conservées, historisées, destinées au moteur, affichées ou non) ;
* servir de **référence technique et fonctionnelle** unique pour l’équipe, avant tout travail sur le moteur décisionnel ou les applications.

Un référentiel **n’est pas** :

* un scénario ;
* une automatisation ;
* une spécification du moteur de décision ;
* une fiche marketing constructeur recopiée sans observation.

---

## 2. Structure normalisée

Tout référentiel de capteur **doit** contenir les sections suivantes, dans cet ordre.  
Des sous-sections sont autorisées ; le renommage ou la suppression d’une section obligatoire ne l’est pas.

### 1. Présentation

Objectif : situer le document et le matériel.

Contenu minimal :

* statut du document (brouillon, validé, obsolète) ;
* identification du modèle ;
* rôle du capteur dans le foyer (une phrase) ;
* liens vers ce modèle (`MODELE-CAPTEUR.md`), le Module 70 / ADR d’identité, et la vision fonctionnelle si utile.

### 2. Caractéristiques matérielles

Objectif : figer l’identité physique et l’identité Hestia du type d’équipement.

Contenu minimal :

* tableau d’identification (fabricant, modèle, type, technologie, protocole, alimentation) ;
* identité Hestia attendue (`hestia_device_id`, nom logique, `room_id`, état, notes, etc. — **référence** au Module 70, sans le redéfinir) ;
* identifiants techniques typiques (ancre physique, bindings HA / protocole) — rôle administratif / mapping uniquement.

### 3. Capacités natives

Objectif : inventaire **constructeur / matériel** de ce que l’appareil peut faire, **sans** filtrer par Home Assistant ni par Hestia.

Contenu minimal :

* listes par famille de capacité (mesure, détection, configuration embarquée, santé appareil, etc.) ;
* aucune affirmation « Hestia utilise X » dans cette section.

### 4. Exposition Home Assistant

Objectif : décrire **uniquement** ce qui est observé sur l’installation Hestia (HA + backend protocole).

Contenu minimal :

* date, nœud, méthode de relevé (lecture seule) ;
* plateforme d’origine (ex. Zigbee2MQTT via MQTT, ZHA, autre) ;
* inventaire des entités (identifiants, domaines, états, plages, attributs) ;
* paramètres configurables réellement exposés ;
* tableau de correspondance capacités natives ↔ exposition HA ;
* capacités natives absentes ou inexploitables ;
* incohérences observées.

**Interdit :** compléter cette section avec de la documentation Internet ou constructeur non vérifiée sur le terrain.

### 5. Exploitation par Hestia

Objectif : répondre à la question — *Parmi les informations disponibles, lesquelles deviennent des données métier Hestia ?*

Contenu minimal :

* tableau de décision (conservée, historisée, moteur, admin, utilisateur, durée, justification) ;
* classification en données métier / administration / techniques / ignorées ;
* mention explicite des données « pour plus tard » vs modèle minimal actuel ;
* criticité en cas d’absence des données essentielles.

**Interdit :** scénarios, automatisations, règles du moteur.

### 6. Présentation dans Hestia

Objectif : ce que voient (ou ne voient pas) les profils humains.

Contenu minimal :

* vue **administrateur** (identité, localisation, santé, diagnostics, données techniques) ;
* vue **utilisateur** : rappel que l’utilisateur ne voit **pas** le capteur, seulement des **informations utiles** produites par Hestia.

Cette section reste descriptive ; elle ne conçoit pas d’écrans détaillés.

### 7. Limites connues

Objectif : border le périmètre de confiance du document.

Exemples de contenu :

* capacités matérielles non exposées ;
* entités HA désactivées ou incohérentes ;
* écarts entre backends (ex. état protocole vs registre HA) ;
* données volontairement hors modèle Hestia ;
* observations incomplètes (à compléter après nouveau relevé).

### 8. Évolutions possibles

Objectif : noter les pistes **sans** les engager.

Exemples :

* réactivation d’un diagnostic (LQI, etc.) ;
* nouveaux protocoles pour le même type de mesure ;
* enrichissement du modèle métier après validation PoC.

Chaque évolution reste une **hypothèse ou intention** jusqu’à observation et décision explicites.

---

## 3. Principes

1. **Capacités matérielles d’abord** — la section « Capacités natives » documente le matériel indépendamment de Home Assistant et d’Hestia.
2. **Home Assistant tel qu’observé** — l’exposition HA décrit l’installation réelle, pas le catalogue théorique du constructeur.
3. **Choix explicite d’Hestia** — toute conservation, historisation ou exclusion est une **décision** documentée, pas un import silencieux de tout le registre HA.
4. **Non-usage documenté** — une information disponible mais non utilisée (ou ignorée) est listée comme telle, avec raison.
5. **Pas d’hypothèse à la place d’une observation** — si une valeur, une entité ou une fréquence n’a pas été constatée, le document l’indique (inconnu / non observé / non applicable). Les hypothèses, si nécessaires, sont marquées clairement et cantonnées (ex. section « Évolutions possibles »).
6. **Indépendance des scénarios** — le référentiel capteur ne décrit ni scénarios ni moteur ; il prépare seulement le socle de données.
7. **Alignement identité** — l’identité équipement suit le Module 70 / ADR associés ; le référentiel capteur ne crée pas un second modèle d’identité.

---

## 4. Séparation des responsabilités

Chaîne de transformation (responsabilités distinctes) :

```
Capteur physique
        ↓
Home Assistant / backend protocole (ex. Zigbee2MQTT)
        ↓
Hestia — modèle métier (fiche, événements retenus, mapping)
        ↓
Moteur décisionnel
        ↓
Applications Hestia (Hub, notifications, Admin)
```

| Niveau | Responsabilité |
| ------ | -------------- |
| Capteur physique | Produire des signaux / mesures selon ses capacités natives |
| HA / backend protocole | Découvrir, normaliser techniquement, exposer des entités et attributs |
| Hestia — modèle métier | Choisir quoi conserver, sous quels identifiants (`hestia_device_id`, pièce, etc.) |
| Moteur décisionnel | Interpréter les données métier (hors périmètre du référentiel capteur) |
| Applications Hestia | Présenter informations utiles (utilisateur) ou outils (admin) |

Le référentiel documente surtout les **trois premiers** niveaux et la **présentation** ; il ne spécifie pas le moteur ni les écrans applicatifs.

---

## 5. Classification des données

Tout référentiel utilise les **mêmes** quatre catégories.

### Données métier

Critères :

* participent directement à la **compréhension de la vie du foyer** (présence, ouverture, température ambiante utile, alerte fumée/fuite en tant que signal de vie/sécurité du lieu, etc.) ;
* sont destinées, à terme, au **moteur** et/ou à la production d’**informations utiles** ;
* restent en général **invisibles** telles quelles à l’utilisateur final.

### Données d’administration

Critères :

* servent au **réglage**, à la **maintenance** ou au **diagnostic** ;
* consultables / actionnables par l’administrateur ;
* ne constituent pas, seules, la « vie du foyer » (timeouts, sensibilités, zones de détection, actions d’apprentissage, etc.).

### Données techniques

Critères :

* permettent le **mapping**, l’**inventaire** et le **cycle de vie** (IEEE / ancre, `entity_id`, `unique_id`, fabricant/modèle, firmware en cache, `hestia_device_id`) ;
* alignées sur le Module 70 et les bindings protocole / HA ;
* non destinées à l’utilisateur final.

### Données ignorées

Critères :

* disponibles (ou partiellement disponibles) mais **volontairement non conservées** ;  
  **ou** non exposées / inexploitables sur l’installation ;  
  **ou** non applicables à ce modèle ;
* la raison d’ignorance est **écrite** (bruit, incohérence, volume, hors HA, « pour plus tard » hors modèle minimal, etc.).

Une donnée « utile plus tard » n’entre dans le modèle minimal **que** si une décision explicite le dit ; sinon elle reste **ignorée** ou reléguée aux évolutions possibles.

---

## 6. Règles de rédaction

Chaque référentiel doit :

1. **Distinguer observation et hypothèse** — formules du type « observé le … sur le nœud … » vs « envisageable / non vérifié ».
2. **Indiquer les limites connues** — section dédiée à jour.
3. **Référencer les ADR / specs concernés** — au minimum identité / cycle de vie (Module 70, ADR associés) ; vision fonctionnelle si le positionnement produit est rappelé.
4. **Utiliser un vocabulaire homogène** — `hestia_device_id`, `room_id`, occupancy / présence selon le terme retenu dans le document, Admin vs Utilisateur, etc. Éviter les synonymes non définis.
5. **Rester aussi indépendant du fabricant que possible** — le constructeur apparaît en identification ; le modèle métier Hestia parle de **signaux** (présence, température, ouverture…), pas de jargon marketing.
6. **Ne pas dupliquer** les specs gelées (Module 70, ADR) — les **référencer**.
7. **Dater les relevés terrain** de la section exposition HA.
8. **Ne pas inventer** d’entités, d’unités ou de capacités « parce que le constructeur les annonce » sans constat sur l’installation.

---

## 7. Évolutivité

La structure des huit sections est **agnostique au protocole**.

Peuvent changer d’un référentiel à l’autre, **sans** changer le plan du document :

* protocole (Zigbee, Z-Wave, Matter, BLE, Wi-Fi, etc.) ;
* backend (Zigbee2MQTT, ZHA, intégration constructeur, etc.) ;
* forme de l’ancre physique (`physical_anchor`) ;
* détail des entités HA.

Ce qui ne change pas :

* l’ordre et le sens des sections ;
* les quatre catégories de données ;
* la séparation des responsabilités ;
* l’exigence d’observation réelle pour l’exposition HA ;
* le choix explicite de ce qu’Hestia conserve.

Un nouveau protocole s’ajoute comme **contenu** dans les sections 2–4 (caractéristiques, capacités, exposition), pas comme une nouvelle architecture documentaire.

---

## 8. Conformité

| Élément | Rôle |
| ------- | ---- |
| **Ce document** (`MODELE-CAPTEUR.md`) | Contrat documentaire — référence officielle |
| Référentiels par modèle (`SNZB-06P24.md`, …) | Implémentations conformes à ce contrat |
| Module 70 / ADR identité | SoT du cycle de vie et des identifiants équipement |
| FUNCTIONAL-VISION | Positionnement produit (assistant de vie, info utile) |

En cas de divergence de structure entre un référentiel existant et ce modèle, **ce modèle prime** ; le référentiel est aligné lors de la prochaine révision documentaire dédiée.

---

## Références

* [Module 70 — Cycle de vie des équipements](../../equipements/70-cycle-vie-equipements.md)
* [ADR-005 — Cycle de vie des équipements](../../adr/ADR-005-cycle-vie-equipements.md)
* [ADR-004 — Mise en service des équipements](../../adr/ADR-004-mise-en-service-equipements.md)
* [FUNCTIONAL-VISION](../../vision/FUNCTIONAL-VISION.md)
* Première implémentation : [SNZB-06P24.md](SNZB-06P24.md)
