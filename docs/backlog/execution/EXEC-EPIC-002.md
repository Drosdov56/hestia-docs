# EXEC-EPIC-002

Statut :
OUVERT

Date d'ouverture :
2026-07-31

---

## Références

- EPIC-002.md
- ADR-004
- ADR-005
- 70-cycle-vie-equipements.md

---

## Objectif

Suivre l'exécution technique de l'EPIC-002.

Ce document complète EPIC-002.md.

Il ne remplace ni la spécification fonctionnelle, ni les ADR.

---

## Découpage d'exécution

| Lot | Objet | Features | Statut |
|-----|-------|----------|--------|
| **A** | Fondation `Equipment` + identité + ancre / bindings | F-007, F-009 | À faire |
| **B** | Machine d'états Module 70 | F-008 | À faire |
| **C** | Nom logique SoT + `pending_ops` | F-010 | À faire |
| **D** | Remplacement deux fiches + reprises | F-011, F-012 | À faire |

Dépôt cible : `hestia` (API / core). Hors périmètre Epic : UI Admin complète (EPIC-003), Installer, logique métier Agent → SoT.

---

### Lot A — Fondation `Equipment`

**Objectif**  
Persister l’entité `Equipment` : attribution de `hestia_device_id` à l’admission, ancre physique et bindings protocole — sans faire de l’ancre la clé métier.

**Périmètre**
- modèle de données `Equipment` (schéma Module 70 §3) ;
- création à `detected` → `pending_provisioning` + `hestia_device_id` (moment unique) ;
- API CRUD contrainte (écritures métier Backend uniquement) ;
- champs `physical_anchor`, `protocol`, `protocol_bindings`, `ha_bindings` ;
- gestion des doublons d’ancre (Module 70 §6.6).

**Hors lot A** : transitions d’états hors admission ; rename / `pending_ops` ; remplacement ; scénarios de reprise §6.8.

**Critères de validation**
- aucun `hestia_device_id` à l’état `detected` seul ;
- ID créé uniquement à l’admission (`pending_provisioning`) ;
- ancre = corrélation technique, pas PK ;
- pas de champ Z2M obligatoire au modèle cœur ;
- doublons d’ancre refusés / traités selon §6.6 ;
- aucune écriture métier Agent → SoT sans Backend.

**Dépendances** : EPIC-001 (contrat sync minimal). Aucun lot EPIC-002 préalable.

---

### Lot B — Machine d’états

**Objectif**  
N’autoriser que les transitions documentées Module 70 / ADR-005 ; exposer les attributs transverses d’erreur et de sync.

**Périmètre**
- états normatifs (neuf états Module 70 §2.1) ;
- table des transitions autorisées et interdites (§2.5, §2.6) ;
- attributs `validation_status`, `sync_status`, `unreachable`, `last_error` ;
- transitions techniques `active`↔`offline` / `synced`↔`offline` (persistance Backend).

**Hors lot B** : SoT nom logique / rename ; wizard remplacement ; tests de reprise panne complets.

**Critères de validation**
- transitions autorisées acceptées ;
- transitions interdites refusées ;
- sous-statuts d’erreur = attributs, pas d’états supplémentaires ;
- Admin/Agent peuvent lire états et attributs transverses.

**Dépendances** : Lot A.

---

### Lot C — Nom logique SoT + `pending_ops`

**Objectif**  
Le Backend est SoT du nom logique ; propagation contrôlée ; file `pending_ops` pour ops différées (Module 70 §5).

**Périmètre**
- écriture / unicité du nom logique (par nœud, v1) ;
- file `pending_ops[]` (rename / sync différés) ;
- option admin rename `entity_id` (v1) — politique HA figée ADR-005 ;
- refus des écritures nom depuis HA/Z2M vers le métier.

**Hors lot C** : UX Admin complète (EPIC-003) ; exécution terrain Agent des ops (consommation de la file).

**Critères de validation**
- nom logique écrit uniquement côté Backend ;
- HA / Z2M ne sont jamais SoT du nom métier ;
- op différée enregistrée en `pending_ops` si offline / panne ;
- collisions / unicité conformes §5 et §6.6.

**Dépendances** : Lot A ; Lot B (états `offline` / sync pour file différée).

---

### Lot D — Remplacement + reprises

**Objectif**  
Modèle deux fiches au remplacement ; comportements normatifs de reprise Module 70 §6.8.

**Périmètre**
- `predecessor_id` / `successor_id` ; prédécesseur → `replaced` ;
- interdiction de réutiliser le `hestia_device_id` du prédécesseur ;
- scénarios §6.8 : panne courant, MQTT, réinstall HA/Z2M, sauvegardes, coordinateur ;
- tests / scénarios de non-régression documentaires.

**Hors lot D** : multi-nœud / multi-logement (hors v1) ; UI wizard Admin complète (EPIC-003 peut consommer l’API).

**Critères de validation**
- remplacement = deux fiches + nouvel ID successeur ;
- `hestia_device_id` prédécesseur jamais réutilisé ;
- comportements §6.8 couverts par tests ou scénarios acceptés ;
- API conforme ADR-005 / Module 70 (critère de done Epic).

**Dépendances** : Lots A, B, C.

---

## Règles

- un lot = un objectif ;
- un lot = un commit dédié (petit nombre de commits si nécessaire, jamais de mélange entre lots) ;
- validation indépendante de chaque lot ;
- aucun mélange de développements ;
- ne pas rouvrir ADR-004 / ADR-005 / Module 70.

---

## Journal

### 2026-07-31

Ouverture officielle de l'exécution de l'EPIC-002.

Aucun développement réalisé.

Découpage A→D fixé (F-007→F-012) — lots à démarrer uniquement sur demande explicite.

---

## Clôture

À compléter lors de la fin de l'EPIC.
