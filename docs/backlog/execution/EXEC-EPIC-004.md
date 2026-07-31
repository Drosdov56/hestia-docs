# EXEC-EPIC-004

Statut :
OUVERT

Date d'ouverture :
2026-07-31

---

## Références

- EPIC-004.md
- FUNCTIONAL-VISION.md (§3, §12, §15) — cible PoC
- MODELE-INFORMATION.md
- Constitution — cycle Observation → … ; information utile
- GLOSSAIRE.md — mapping cycle / information utile
- architecture-domotique.md §11
- SNZB-06P24.md — scénario matériel de référence
- ADR-020 — positionnement HA (backend technique, pas interface utilisateur)
- ADR-005 / Module 70 — équipement SoT connu (prérequis identité)
- EPIC-001 — contrat ingest / observation Agent
- EPIC-002 — SoT équipements

---

## Objectif

Suivre l'exécution technique de l'EPIC-004 (PoC : événement → information utile).

Ce document complète EPIC-004.md.

Il ne remplace ni la vision fonctionnelle, ni MODELE-INFORMATION, ni les ADR.

Le PoC démontre le **bénéfice utilisateur**, pas une démo d’administration ni un moteur décisionnel / IA.

---

## Prérequis

- **EPIC-001 livré** — Agent → Backend (observation / ingest minimal).
- **EPIC-002 CLÔTURÉ** — équipement connu en SoT (`hestia_device_id`, états, métadonnées).
- **EPIC-003 CLÔTURÉ** — non bloquant strict pour le PoC (ROADMAP : équipement déjà provisionné possible), mais recommandé pour un parcours parc cohérent.
- Capteur de référence : **SNZB-06P24** (présence) sur nœud qualifié.

---

## Découpage d'exécution

| Lot | Objet | Features | Statut |
|-----|-------|----------|--------|
| **A** | Ingest métier présence | F-018 (chaîne Agent → Backend) | À faire |
| **B** | Formulation information utile | F-019 | À faire |
| **C** | Surface Hub minimale | F-018 (affichage utilisateur) | À faire |
| **D** | Notification optionnelle | F-018 (notification) | À faire |

Dépôts cibles : `hestia-agent` (émission / sélection événement) · `hestia` (Backend + surface Hub).  
Ne pas livrer de moteur décisionnel (EPIC-007) ni d’IA (FUNCTIONAL-VISION §6).  
Ne pas exposer le jargon HA / Z2M / MQTT à l’utilisateur final.

---

### Lot A — Ingest métier présence

**Objectif métier**  
Brancher **un** type d’événement (présence SNZB) de bout en bout jusqu’au Backend Hestia, en le rattachant à un équipement SoT connu.

**Fonctionnalités concernées**
- **F-018** — partie chaîne Capteur → Agent → Backend
- contrat d’événement métier (hors signaux bruts HA) ;
- persistance / consultation Backend minimale ;
- corrélation à `hestia_device_id` (équipement actif connu).

**Dépendances** : EPIC-001, EPIC-002. Aucun lot EPIC-004 préalable.

**Critères de validation**
- un événement de présence émis par l’Agent est accepté par le Backend ;
- l’événement est lié à un équipement SoT (pas uniquement à une entité HA) ;
- le scénario est reproductible pour le type « présence » uniquement ;
- aucune UI Admin n’est requise pour valider ce lot (hors éventuellement outillage de test).

**État** : À faire

---

### Lot B — Formulation information utile

**Objectif métier**  
Transformer l’événement retenu en **une** information claire, compréhensible par un humain, sans jargon technique.

**Fonctionnalités concernées**
- **F-019** — Formulation d’une information utile
- gabarit / structure minimale d’information utile ;
- texte ou structure destinée aux applications (Hub, notification) ;
- pas de moteur décisionnel complexe ;
- pas d’IA.

**Dépendances** : Lot A ; MODELE-INFORMATION (structure conceptuelle).

**Critères de validation**
- à partir d’un événement de présence ingéré, une information utile est produite ;
- le contenu est compréhensible sans connaissance HA / Z2M / MQTT ;
- une seule information claire par événement de référence (PoC) ;
- aucune règle décisionnelle multi-signaux (reporté EPIC-007 / EPIC-010).

**État** : À faire

---

### Lot C — Surface Hub minimale

**Objectif métier**  
Rendre l’information utile visible à l’utilisateur sur une surface Hub minimale (consultation).

**Fonctionnalités concernées**
- **F-018** — partie surface Hub
- affichage de l’information utile produite au Lot B ;
- parcours utilisateur (pas Admin) ;
- pas de généralisation multi-équipements / multi-acteurs.

**Dépendances** : Lot B.

**Critères de validation**
- l’utilisateur voit le résultat d’un événement de présence dans Hestia ;
- l’affichage s’appuie sur l’information utile (pas sur des identifiants techniques HA) ;
- le scénario PoC §15 est reproductible jusqu’à la surface Hub ;
- aucune dépendance à l’UI Home Assistant pour consulter le résultat.

**État** : À faire

---

### Lot D — Notification optionnelle

**Objectif métier**  
Notifier optionnellement l’utilisateur lorsqu’une information utile de présence est produite.

**Fonctionnalités concernées**
- **F-018** — branche notification (optionnelle)
- déclenchement à partir de l’information utile ;
- canal minimal (à définir à l’implémentation : in-app et/ou mécanisme déjà présent) ;
- désactivable / non bloquant pour le critère de done du PoC.

**Dépendances** : Lot B (Lot C recommandé pour cohérence UX, non strict si canal hors Hub).

**Critères de validation**
- une notification optionnelle peut être émise pour l’information utile de présence ;
- le contenu notifié reste compréhensible (même exigence F-019) ;
- l’absence de notification n’empêche pas la validation des lots A–C ;
- pas d’exigence de multi-canal / multi-destinataires (hors PoC).

**État** : À faire

---

## Règles

- un lot = un objectif ;
- un lot = un commit dédié (petit nombre de commits si nécessaire, jamais de mélange entre lots) ;
- validation indépendante de chaque lot ;
- aucun mélange de développements ;
- ne pas rouvrir EPIC-001 / EPIC-002 / EPIC-003 sauf anomalie critique ;
- ne pas anticiper EPIC-005 (Hub complet), EPIC-006 (pipeline information complet), EPIC-007 (décision), EPIC-013 (IA) ;
- le PoC reste centré **valeur utilisateur**, pas démo technique Admin.

---

## Journal

### 2026-07-31

Ouverture du suivi d'exécution de l'EPIC-004 (préparation post-clôture EPIC-003).

Découpage A→D proposé :
- A ingest métier présence ;
- B formulation information utile ;
- C surface Hub minimale ;
- D notification optionnelle.

Aucun développement réalisé.

---

## Clôture

À compléter lors de la fin de l'EPIC.
