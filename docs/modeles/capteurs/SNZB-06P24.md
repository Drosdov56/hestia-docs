# SNZB-06P24 — Capteur de présence humaine mmWave

**Statut :** Brouillon v1  
**Emplacement :** `hestia-docs/docs/modeles/capteurs/SNZB-06P24.md`

**Références croisées :**
- Identité / cycle de vie : [Module 70](../../equipements/70-cycle-vie-equipements.md) · [ADR-005](../../adr/ADR-005-cycle-vie-equipements.md)
- Vision fonctionnelle : [FUNCTIONAL-VISION](../../vision/FUNCTIONAL-VISION.md)
- Positionnement HA : [ADR-020](../../adr/)

---

# Objectif

Ce document constitue la référence fonctionnelle du **SONOFF SNZB-06P24** dans l'écosystème Hestia.

Son objectif n'est pas de décrire uniquement ce qui est exploité aujourd'hui, mais **l'ensemble des capacités offertes par le matériel**.

Chaque fonctionnalité sera ensuite analysée afin de déterminer :

* ce que Home Assistant expose réellement ;
* ce que Hestia conserve ;
* ce que le moteur décisionnel exploite ;
* ce qui est affiché aux différents profils utilisateurs.

Ce document servira de modèle pour tous les futurs capteurs.

---

# 1. Identification

| Champ        | Valeur                      |
| ------------ | --------------------------- |
| Fabricant    | SONOFF                      |
| Modèle       | SNZB-06P24                  |
| Type         | Capteur de présence humaine |
| Technologie  | Radar mmWave 24 GHz         |
| Protocole    | Zigbee 3.0                  |
| Alimentation | USB-C 5V                    |

---

# 2. Identité Hestia

Chaque capteur possède une identité indépendante de Home Assistant.

Informations conservées :

* `hestia_device_id`
* nom du capteur
* pièce (`room_id`)
* fabricant
* modèle
* version matérielle
* version firmware
* date d'installation
* état (actif / inactif)
* notes administrateur

Le `hestia_device_id` est l'identifiant de référence de tout l'écosystème Hestia.

> Alignement : champs et machine d'états normatifs dans le [Module 70](../../equipements/70-cycle-vie-equipements.md). Ce document ne redéfinit pas le cycle de vie ; il le référence.

---

# 3. Identifiants techniques

Informations techniques permettant le dialogue avec l'infrastructure :

* IEEE Address
* Zigbee Device ID
* Home Assistant `device_id`
* Home Assistant `entity_id`
* Home Assistant `area_id` (si utilisé)
* Friendly Name
* MQTT Topic (si utilisé)

Ces informations sont destinées à l'administration.

> Alignement : `physical_anchor`, `protocol_bindings`, `ha_bindings` — [Module 70 §3](../../equipements/70-cycle-vie-equipements.md).

---

# 4. Capacités natives du matériel

Cette section décrit **tout ce que le constructeur annonce**, indépendamment de l'utilisation actuelle par Hestia.

## Présence

Le capteur est capable de détecter :

* présence humaine
* absence
* début de présence
* fin de présence
* présence prolongée
* micro-mouvements
* personne immobile

---

## Détection spatiale

Le capteur permet :

* détection jusqu'à 4 mètres
* découpage en 7 zones indépendantes
* activation/désactivation des zones
* orientation à 360°

---

## Luminosité

Le capteur mesure :

* luminosité ambiante

---

## Intelligence embarquée

Le firmware intègre notamment :

* auto-apprentissage des interférences
* filtrage des mouvements parasites
* réduction des faux déclenchements
* réduction des absences de détection

---

## Santé du capteur

Le capteur peut fournir :

* disponibilité (online/offline)
* qualité radio (LQI)
* puissance du signal (RSSI)
* dernière communication
* firmware
* version matérielle

---

# 5. Exposition Home Assistant

Cette section sera complétée après intégration réelle.

Pour chaque capacité native, préciser :

* l'entité Home Assistant créée ;
* le type de donnée exposée ;
* la fréquence de mise à jour ;
* les attributs disponibles.

---

# 6. Exploitation par Hestia

Pour chaque donnée issue de Home Assistant, préciser :

| Information | Conservée | Historisée | Utilisée | Commentaire |
| ----------- | --------- | ---------- | -------- | ----------- |

Exemples :

* présence
* absence
* dernier changement
* luminosité
* LQI
* RSSI
* disponibilité
* firmware

---

# 7. Exploitation par le moteur décisionnel

Cette section décrira uniquement les usages métier.

Exemples :

* déterminer qu'une pièce est occupée ;
* confirmer une présence durable ;
* détecter une anomalie ;
* corréler avec d'autres capteurs.

Aucun scénario ne sera décrit ici.

---

# 8. Affichage

## Administration

L'administrateur peut consulter :

* identité complète du capteur ;
* localisation ;
* état de fonctionnement ;
* informations radio ;
* firmware ;
* diagnostics ;
* historique ;
* données techniques Home Assistant.

---

## Utilisateur

Les utilisateurs ne voient jamais le capteur.

Ils voient uniquement des informations utiles produites par Hestia.

---

# 9. Questions ouvertes

Cette section recense les décisions à prendre.

Exemples :

* faut-il conserver le RSSI ?
* faut-il historiser la luminosité ?
* les 7 zones doivent-elles être visibles dans Hestia ?
* la présence immobile apporte-t-elle une valeur métier ?
* quelle durée de conservation pour chaque information ?

---

# Principe de conception

Chaque nouveau capteur suivra exactement cette structure.

L'objectif est de constituer un **référentiel fonctionnel des capacités des capteurs**, indépendant des scénarios.

Les scénarios, le moteur décisionnel et les informations utiles seront construits **uniquement après** la définition complète des capacités de chaque capteur.
