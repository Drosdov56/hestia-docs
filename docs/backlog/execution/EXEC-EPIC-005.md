# EXEC-EPIC-005

Statut :
**TERMINÉ**

Date d'ouverture :
2026-07-31

Date de clôture :
2026-07-31

---

## Références

- EPIC-005.md
- FUNCTIONAL-VISION.md (§9, §11, §12)
- ADR-020 — Apps = présentation (pas d’UI HA pour l’utilisateur final)
- ADR-018 — modules `home` / `cameras`
- architecture-domotique.md §2, §8
- ecosysteme.md
- GLOSSAIRE.md — Applications Hestia
- MODELE-INFORMATION.md — information utile consommée, non reformulée côté Apps
- EPIC-004 — PoC événement → information utile (prérequis livré)

---

## Objectif

Suivre l'exécution technique de l'EPIC-005 (Hub familial & notifications).

Ce document complète EPIC-005.md.

Il ne remplace ni la vision fonctionnelle, ni les ADR, ni MODELE-INFORMATION.

Faire des applications Hestia la **seule** expérience utilisateurs finaux pour consulter l’information utile et recevoir des notifications — **sans logique métier dans le client**.

---

## Prérequis

- **EPIC-004 CLÔTURÉ** — flux PoC : événement → information utile → Hub minimal → notification optionnelle (enregistrée).
- Tip code PoC : `hestia` `2fd0cce`.
- Ne pas rouvrir EPIC-001 / EPIC-002 / EPIC-003 / EPIC-004 sauf anomalie critique.

---

## Découpage d'exécution

| Lot | Objet | Features | Statut | Commit (`hestia`) |
|-----|-------|----------|--------|-------------------|
| **A** | Contrat API Hub utilisateur (consultation durable) | F-020 (fondation) | **TERMINÉ** | `1bc2fb2c7c6ea2a596af3e1177bd10bf4ca5912b` |
| **B** | Affichage Hub / module home (PWA) | F-020 | **TERMINÉ** | `ae5717c80f994a685ede404352d7c6355462d3ce` |
| **C** | Canal notifications Serveur → Apps (in-app) | F-021 | **TERMINÉ** | `b2fdc016c46b50075b28772c4a9fb3a6b379b50b` |
| **D** | Android WebView / bridge notification | F-020, F-021 | **TERMINÉ** | `0a564b025b5633a9cee2cf77f83d2590e8245735` |

Dépôt cible : `hestia` (`core/`, `client/`, `android/`).  
S’appuyer sur les livrables EPIC-004 (Hub minimal, `NotificationStore`) sans les re-formuler.  
Ne pas livrer de moteur décisionnel (EPIC-007) ni d’IA (EPIC-013).  
Ne pas exposer le jargon HA / Z2M / MQTT à l’utilisateur final.  
Aucune logique métier / formulation / décision dans le client.

---

### Lot A — Contrat API Hub utilisateur

**Objectif métier**  
Consolider la consultation utilisateur des informations utiles (hors Admin) : contrat stable, permissions, rafraîchissement / polling, indépendant de l’UI Admin.

**Fonctionnalités concernées**
- **F-020** — fondation API pour le module home
- endpoints Hub utilisateur (lecture seule) ;
- permissions catalogue (`home.view`) ;
- pas de recalcul métier côté API Hub.

**Dépendances** : EPIC-004 (Lot C API Hub minimale). Aucun lot EPIC-005 préalable.

**Critères de validation**
- un utilisateur authentifié avec `home.view` consulte les informations utiles ;
- le contenu reste celui produit en Lot B EPIC-004 (pas de reformulation) ;
- un mécanisme de rafraîchissement (polling ou équivalent) est disponible ;
- aucun terme HA / MQTT / Z2M dans les payloads Hub.

**État** : **TERMINÉ** — commit `1bc2fb2c7c6ea2a596af3e1177bd10bf4ca5912b`

---

### Lot B — Affichage Hub / module home (PWA)

**Objectif métier**  
Rendre le Hub / module home l’expérience durable de consultation des informations utiles dans la PWA.

**Fonctionnalités concernées**
- **F-020** — Affichage Hub (module home)
- brancher le module `home` (catalogue) sur l’API réelle (Lot A) ;
- affichage / rafraîchissement côté PWA ;
- widget dashboard cohérent avec le feed.

**Dépendances** : Lot A.

**Critères de validation**
- l’utilisateur consulte les informations utiles sur le Hub / module home ;
- l’utilisateur n’accède jamais à l’UI Home Assistant pour ce parcours ;
- le client n’interprète / ne reformule pas les informations ;
- le scénario PoC reste visible durablement dans le Hub.

**État** : **TERMINÉ** — commit `ae5717c80f994a685ede404352d7c6355462d3ce`

---

### Lot C — Canal notifications Serveur → Apps (in-app)

**Objectif métier**  
Faire parvenir aux Apps les notifications liées aux informations utiles (canal Serveur → Apps), avec règles d’émission minimales post-PoC.

**Fonctionnalités concernées**
- **F-021** — Notifications utilisateurs
- consultation / consommation des notifications enregistrées ;
- couche in-app (PWA) branchée sur l’API ;
- règles d’émission minimales (optionnelles, désactivables) ;
- pas d’interprétation métier dans l’App.

**Dépendances** : EPIC-004 Lot D (store / service notification) ; Lot A recommandé pour cohérence auth.

**Critères de validation**
- une notification liée à une information utile est consultable / affichable in-app ;
- l’absence de notification n’empêche pas la consultation Hub (Lots A–B) ;
- pas d’envoi réel multi-canal exigé dans ce lot (email / SMS hors périmètre si non déjà prévu) ;
- déduplication / optionnalité conservées.

**État** : **TERMINÉ** — commit `b2fdc016c46b50075b28772c4a9fb3a6b379b50b`

---

### Lot D — Android WebView / bridge notification

**Objectif métier**  
Étendre le parcours Hub / notifications à l’enveloppe Android (WebView), sans logique métier native.

**Fonctionnalités concernées**
- **F-020** / **F-021** — surface Android
- WebView chargeant la PWA Hub ;
- bridge notification native minimal (si canal déjà préparé) ;
- aucune interprétation métier côté Android.

**Dépendances** : Lots B et C (PWA Hub + notifications in-app).

**Critères de validation**
- le parcours Hub est utilisable dans l’App Android WebView ;
- une notification optionnelle est démontrable (in-app et/ou bridge) ;
- pas de logique métier / formulation dans `android/` ;
- critères de done Epic : information PoC visible durablement ; notification optionnelle démontrable.

**État** : **TERMINÉ** — commit `0a564b025b5633a9cee2cf77f83d2590e8245735`

---

## Règles

- un lot = un objectif ;
- un lot = un commit dédié (petit nombre de commits si nécessaire, jamais de mélange entre lots) ;
- validation indépendante de chaque lot ;
- aucun mélange de développements ;
- ne pas rouvrir EPIC-001 / EPIC-002 / EPIC-003 / EPIC-004 sauf anomalie critique ;
- ne pas anticiper EPIC-006 (pipeline information), EPIC-007 (décision), EPIC-013 (IA) ;
- Apps = présentation uniquement (ADR-020).

---

## Journal

### 2026-07-31

Ouverture du suivi d'exécution de l'EPIC-005 (préparation post-clôture EPIC-004).

Découpage A→D proposé puis exécuté :
- A contrat API Hub utilisateur ;
- B affichage Hub / module home (PWA) ;
- C canal notifications Serveur → Apps (in-app) ;
- D Android WebView / bridge notification.

Lots A→D implémentés et poussés sur `hestia` (`1bc2fb2` … `0a564b0`).

Clôture documentaire officielle — verdict **TERMINÉ**.

---

## Clôture

| Attribut | Valeur |
|----------|--------|
| Verdict | **TERMINÉ** |
| Date | 2026-07-31 |
| Dépôt code | `hestia` tip `0a564b0` |
| Features | F-020, F-021 validées |
| Critères de done | Hub durable + notification optionnelle démontrable |
| Surfaces | contrat `hub.v1` · Hub PWA · notifications in-app `notifications.v1` · bridge Android |
| Suite produit | **EPIC-006** (pipeline information) — **ne pas démarrer sans demande explicite** |
