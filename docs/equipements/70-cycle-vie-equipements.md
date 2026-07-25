# Conception — Module 70 · Cycle de vie des équipements

| Attribut | Valeur |
|----------|--------|
| **Statut** | **Gelée — clôturée** (contrôle documentaire final 2026-07-21) |
| **Date** | 2026-07-21 |
| **Références** | [ADR-004](../adr/ADR-004-mise-en-service-equipements.md), [ADR-005](../adr/ADR-005-cycle-vie-equipements.md), UX-003 / INT-001 (BACKLOG `hestia-installer`) |

Document **normatif** du Module 70 **métier** (cycle de vie équipements). Il spécifie le cycle de vie, l'identité, les métadonnées, le renommage, les cas particuliers, les reprises et les responsabilités.

> **Périmètre installeur :** le script `install/70-hestia-agent.sh` déploie l'Agent (processus natif, conf, `.service` ; pas de `systemctl`). Il **n'implémente pas** ce modèle. Voir [ADR-004](../adr/ADR-004-mise-en-service-equipements.md) et [ADR-005](../adr/ADR-005-cycle-vie-equipements.md).
> **Note historique (2026-07-22) — §8 script install :** le paragraphe §8 ci-dessous conserve une description **visée puis remplacée** pour l'Agent V0.1.0 / **INT-002B** (pas de conf Backend/MQTT ni enregistrement nœud dans le module 70). La vérité opérationnelle = `install/70-hestia-agent.sh` + `lib/hestia-agent.sh` + contrat Agent V1. Ne pas supprimer §8 (historique) ; ne pas l'utiliser comme spec d'implémentation courante.

---

## 1. Positionnement

```
Phase 1 — Hestia Installer                Phase 2 — Hestia Admin              Phase 3 — Exploitation
(modules 00–70 script, 90, INT-001)       (UX-003 + runtime Module 70)        (Hestia)
────────────────────────────────────      ───────────────────────────         ────────────────────
Plateforme opérationnelle + Agent   →     Cycle de vie équipement       →     Consommation via HA
FIN DE RESPONSABILITÉ INSTALLEUR          Machine d'états + métier            Z2M transparent
```

### 1.1 Frontières des composants Hestia

| Composant | Rôle | Ce qu'il n'est pas |
|-----------|------|--------------------|
| **Hestia Installer** | Déploie la plateforme et l'Agent ; INT-001 | Pas de logique métier équipement |
| **Hestia Admin** | UI + décisions métier (transitions manuelles, UX-003) | Pas d'exécution technique directe sur Z2M/HA |
| **Agent Hestia** | Observation, réplique locale, exécution sync, transitions **techniques automatiques** | Pas de SoT métier ; pas d'UI Admin |
| **Backend Hestia** | **Source de vérité** du modèle fonctionnel (états métier, noms, pièces, liens) | Pas de contact direct Z2M/HA (passe par l'Agent) |

**Règle d'autorité :** toute écriture métier aboutit dans le **Backend**. L'Agent détient une **réplique locale** pour fonctionner si le cloud est momentanément injoignable, puis se réconcilie avec le Backend (Backend gagne en cas de conflit d'écriture métier).

### 1.2 Appairage technique — clarification ADR-004

| Moment | Qui | Périmètre |
|--------|-----|-----------|
| Pendant / juste après install | Installer **peut** faciliter l'accès technique (ex. Z2M joignable) | **Aucun** nommage métier, **aucune** fiche Hestia |
| Exploitation | Admin déclenche permit-join / appairage **via l'Agent** | Z2M exécute la radio ; Admin ne manipule pas l'UI Z2M |

---

## 2. Cycle de vie — machine d'états

### 2.1 États normatifs

| État | Code | Description |
|------|------|-------------|
| **Détecté** | `detected` | Observation technique en file Agent (Zigbee/MQTT et/ou HA). **Pas encore** d'enregistrement `Equipment` Backend. |
| **En attente de provisioning** | `pending_provisioning` | Fiche `Equipment` créée ; file UX-003 ; checks en cours ou en échec. |
| **Provisionné** | `provisioned` | Infos métier saisies et persistées Backend. Backends techniques non confirmés alignés. |
| **Synchronisé avec Home Assistant** | `synced` | Liaisons Z2M/HA confirmées. Prêt exploitation ; pas encore (ou plus) de preuve de joignabilité courante. |
| **Actif** | `active` | En service, joignable, exploité. |
| **Hors ligne** | `offline` | Était `active` **ou** `synced` ; plus de communication après `OFFLINE_GRACE`. Fiche conservée. |
| **Désactivé** | `disabled` | Hors service volontaire admin. Exclu de l'exploitation courante. |
| **Supprimé** | `deleted` | Soft-delete métier. Historique conservé. |
| **Remplacé** | `replaced` | Prédécesseur après wizard remplacement (terminal). Continuité via successeur. |

Les sous-statuts d'erreur sont des **attributs**, pas des états : `validation_status`, `sync_status`, `last_error`, `unreachable`.

### 2.2 Attributs transverses

| Attribut | Valeurs | Usage |
|----------|---------|-------|
| `validation_status` | `unknown` \| `ok` \| `failed` | Checks UX-003 |
| `sync_status` | `idle` \| `pending` \| `ok` \| `failed` | Alignement backends |
| `unreachable` | bool | Introuvable alors que hors états `offline`/`deleted`/`replaced` |
| `last_error` | texte / code | Dernière erreur |
| `successor_id` / `predecessor_id` | `hestia_device_id` \| null | Chaîne de remplacement |
| `pending_ops[]` | file d'ops | Rename/sync différés (panne, offline) |

### 2.3 Création de la fiche et de `hestia_device_id`

1. **`detected`** : buffer d'observation Agent uniquement (clé technique = ancre physique). Pas de `hestia_device_id`.
2. Transition **`detected` → `pending_provisioning`** : création `Equipment` dans le Backend + attribution **`hestia_device_id`**. Moment unique et obligatoire.
3. Jamais de `hestia_device_id` « provisoire » ni de création retardée à `provisioned`.

### 2.4 Diagramme des transitions

```
  observation technique
         │
         ▼
    ┌─────────┐
    │ Détecté │  (buffer — pas d'Equipment)
    └────┬────┘
         │ admission UX-003 → crée Equipment + hestia_device_id
         ▼
┌────────────────────────────┐
│ En attente de provisioning │◄── rejeu checks
└────────────┬───────────────┘
             │ saisie métier OK
             ▼
      ┌─────────────┐
      │ Provisionné │◄── échec / reprise sync
      └──────┬──────┘
             │ sync Z2M+HA OK (atomique métier)
             ▼
┌────────────────────────────┐
│ Synchronisé avec HA        │────── timeout comm. ──► Hors ligne
└────────────┬───────────────┘◄──── retour online ────┘
             │ preuve joignabilité
             ▼
        ┌─────────┐
        │  Actif  │────── timeout comm. ──► Hors ligne
        └────┬────┘◄──── retour online ────┘
             │
             ├── disable ──► Désactivé ── enable ──► Actif ou Hors ligne
             ├── replace ──► Remplacé (+ successeur)
             └── delete  ──► Supprimé
```

### 2.5 Table des transitions autorisées

| Depuis | Vers | Déclencheur | Conditions | Échec / refus |
|--------|------|-------------|------------|---------------|
| — | `detected` | Event Z2M / MQTT / device HA | Ancre inconnue du parc non terminal | Doublon ancre → §6.6 |
| `detected` | `pending_provisioning` | Admission UX-003 | Agent + Backend joignables pour créer la fiche | Reste `detected` + erreur |
| `pending_provisioning` | `pending_provisioning` | Relance checks | — | `validation_status=failed` bloque saisie sauf dérogation admin |
| `pending_provisioning` | `provisioned` | Validation admin métier | `validation_status=ok` ou dérogation ; champs obligatoires | Refus si nom vide / pièce invalide |
| `provisioned` | `synced` | Sync Z2M **et** HA confirmées | Cibles nom + liens cohérents | Reste `provisioned`, `sync_status=failed` ; **sync partielle ≠ synced** |
| `synced` | `active` | Preuve de disponibilité | Entité HA disponible **ou** heartbeat protocole récent | Peut rester `synced` + alerte |
| `active` | `offline` | Silence > `OFFLINE_GRACE` | — | Heartbeat tardif → `active` |
| `synced` | `offline` | Silence > `OFFLINE_GRACE` | — | Idem |
| `offline` | `active` | Reprise communication | Même ancre physique | Ancre différente → doublon / remplacement |
| `offline` | `synced` | Reprise sans preuve « active » suffisante | Liens HA toujours valides | — |
| `active` \| `offline` \| `synced` \| `provisioned` \| `pending_provisioning` | `disabled` | Action admin | — | Backends non purgés (§6.9) |
| `disabled` | `active` | Réactivation + joignable | — | Si absent → `offline` |
| `disabled` | `offline` | Réactivation + non joignable | — | — |
| `*` sauf `deleted`/`replaced` | `deleted` | Action admin confirmée | — | Purge backends = option explicite (§6.9) |
| `active` \| `offline` \| `disabled` \| `synced` \| `provisioned` | `replaced` | Wizard remplacement OK | Successeur créé (nouvel ID) + métadonnées copiées | Sinon pas de `replaced` |
| `deleted` / `replaced` | — | — | Terminaux | Réapparition ancre → nouvel `detected` (pas de résurrection) |

### 2.6 Transitions interdites (explicites)

| Transition | Raison |
|------------|--------|
| `deleted` → tout | Terminal ; nouvel équipement = nouveau cycle |
| `replaced` → tout | Terminal |
| `detected` → `provisioned` / `synced` / `active` | Interdit de sauter UX-003 |
| `active` → `provisioned` | Pas de retour arrière métier sans delete/replace |
| `synced` → `provisioned` | Uniquement via échec de **re**-sync détecté : alors `sync_status=failed` **sans** quitter `synced` si liens encore valides ; si liens cassés → retour `provisioned` autorisé (**seule** exception de retour) |
| Tout → `detected` | `detected` n'existe que pour observations sans fiche |

### 2.7 Paramètres temporels

| Paramètre | Valeur cible initiale | Description |
|-----------|----------------------|-------------|
| `OFFLINE_GRACE` | 15 min (configurable) | Avant `offline` |
| `LONG_ABSENCE` | 7 jours | Alerte longue absence (§6.7) |
| `SYNC_RETRY` | 3, backoff | Relances `provisioned` → `synced` |
| `RECONCILE_INTERVAL` | 5 min | Réconciliation Agent ↔ Backend |

### 2.8 Cas d'erreur et reprise

| Erreur | État | Reprise |
|--------|------|---------|
| Checks UX-003 KO | `pending_provisioning` | Relance / dérogation |
| Sync Z2M ou HA KO | `provisioned` | Retry auto puis manuel ; ops en `pending_ops` |
| Panne milieu de sync | `provisioned` + `sync_status=pending\|failed` | Reprise idempotente depuis Backend (pas d'état « demi-synced ») |
| Conflit de nom | état courant | Blocage ; résolution admin §5.4 |
| Disparition pendant provisioning | `pending_provisioning` + `unreachable` | Attendre, désactiver, supprimer |
| Backend injoignable | réplique Agent | Transitions **automatiques** locales (`active`↔`offline`) OK ; transitions **métier** mises en file jusqu'à réconciliation |

---

## 3. Identité d'un équipement

### 3.1 Identifiants

| Identifiant | Nature | Immuable | Modifiable | Dérivé | Rôle |
|-------------|--------|:--------:|:----------:|:------:|------|
| **`hestia_device_id`** | Métier | **Oui** | Non | Non | **Clé primaire** de toute référence métier |
| **Ancre physique** (IEEE Zigbee, etc.) | Technique | Oui\* | Non\* | Non | Corrélation technique ; unique par `(node_id, protocol, anchor)` hors terminaux |
| **Friendly Name Z2M** | Technique passerelle | Non | Oui via Hestia | Non | Miroir cible de sync (Zigbee/Z2M seulement) |
| **MQTT Topic** | Technique | Non | Indirect | **Oui** | `{base}/{friendly_name}` — jamais saisi |
| **HA Device ID** | Technique HA | Quasi | Non (HA) | Non | Lien stocké, jamais clé métier |
| **HA Entity ID(s)** | Technique HA | Non | Oui† | Souvent | Liens stockés ; †voir politique §5.2 |

\* Change uniquement si le **matériel** change → procédure `replaced` (nouvel `hestia_device_id` successeur).

### 3.2 Règles d'identité

1. `hestia_device_id` créé **uniquement** à `detected` → `pending_provisioning`.
2. Remplacement = **deux fiches** : prédécesseur `replaced` + successeur **nouveau** `hestia_device_id` (§6.3). **Jamais** réutilisation du `hestia_device_id` du prédécesseur.
3. MQTT topic toujours dérivé.
4. Protocoles non-Zigbee : même modèle ; champ `physical_anchor` + `protocol` ; pas de dépendance au concept IEEE hors Zigbee.
5. Unicité du **nom logique** : par nœud Hestia (pas global multi-logements en v1).

### 3.3 Schéma logique (agnostique protocole)

```
Equipment {
  hestia_device_id          // PK immuable
  node_id                   // nœud Hestia
  protocol                  // zigbee | bluetooth | wifi | matter | zwave | …
  physical_anchor           // IEEE, bt_addr, mac, matter_node_id, …
  state                     // enum §2.1
  validation_status, sync_status, unreachable, last_error
  logical_name              // SoT Backend Hestia
  room_id, zone_id, group_ids[], owner, comments, category, options
  protocol_bindings {       // spécifique protocole — pas de champ z2m_* obligatoire au sommet
    // zigbee/z2m: friendly_name, mqtt_topic
    // autres: clés propres à l'adaptateur
  }
  ha_bindings {
    ha_device_id
    ha_entity_ids[]         // tels que découverts + optionnellement alignés
    ha_display_names[]      // cibles de nom d'affichage
  }
  successor_id, predecessor_id
  pending_ops[]
  timestamps, telemetry_cache
}
```

**Anti-couplage Z2M :** le modèle ne doit pas exiger `z2m_friendly_name` pour exister. L'adaptateur Zigbee/Z2M remplit `protocol_bindings` ; Matter/Z-Wave/Bluetooth fournissent le leur.

---

## 4. Métadonnées conservées

### 4.1 Inventaire et propriétaire

| Métadonnée | Passerelle protocole (ex. Z2M) | Home Assistant | Hestia (Backend) |
|------------|:------------------------------:|:--------------:|:----------------:|
| Fabricant / modèle / firmware | **Source** | Miroir | Cache |
| Catégorie métier | — | — | **SoT** |
| Type technique | Source | Classe entité | Cache distinct de la catégorie métier |
| `paired_at` / dernière comm. | Source partielle | Source partielle | **SoT consolidée** |
| LQI / batterie | **Source** | Entités | Cache + alertes |
| Pièce / zone / groupe / propriétaire / commentaires | — | Non SoT | **SoT** |
| Nom logique | Cible sync (friendly) | Cible sync (display name) | **SoT** |

### 4.2 Propagation des métadonnées

| Donnée | Propagation Hestia → backends | Interdit |
|--------|-------------------------------|----------|
| Nom logique | Oui — selon §5 | Backends → nom logique (écriture) |
| Pièce / zone / groupe / commentaires | **Non** en v1 (HA Areas non écrites) | — |
| Fabricant / modèle / firmware | **Non** | Hestia ne réécrit jamais ces champs |

---

## 5. Règles de renommage et synchronisation
<!-- ancre stable : #module-70-renommage -->
<a id="module-70-renommage"></a>

### 5.1 Source de vérité

| Élément | SoT | Propagation |
|---------|-----|-------------|
| Nom logique | **Backend Hestia** (via Admin) | → adaptateur protocole (ex. friendly Z2M) ; → noms d'affichage HA |
| Friendly Z2M / équivalent | Miroir après sync | Écriture **uniquement** via Agent |
| Entity ID HA | Lien ; alignement **optionnel** | Voir §5.2 |

### 5.2 Politique HA figée (v1)

1. **Par défaut :** Hestia met à jour le **nom d'affichage** HA (device / entities) ; les **`entity_id` restent ceux découverts** et sont stockés comme liens.
2. **Option admin explicite** « Aligner les entity_id » : tentative de rename `entity_id` vers un slug dérivé du nom logique ; collisions = refus + proposition.
3. **Rationale :** éviter de casser automatisations HA / paquets externes par un rename systématique.

### 5.3 Dérivation du slug technique

```
logical_name (affichage, Unicode)
        ↓ normalise (minuscules, ASCII, underscores, longueur max)
technical_slug  →  friendly Z2M / proposition entity_id
```

Unicité du slug contrôlée avant sync. Collision = refus.

### 5.4 Flux autorisé

```
Admin modifie logical_name (Backend)
  → validation unicité + slug
  → Agent exécute adaptateur protocole (ex. rename Z2M)
  → Agent aligne noms d'affichage HA (+ entity_id si option)
  → Backend : ha_bindings / protocol_bindings + sync_status=ok
```

Si offline / panne : op en `pending_ops` ; réapplication à la reprise.

### 5.5 Directions

| Direction | Autorisée |
|-----------|:---------:|
| Hestia → passerelle (friendly / équivalent) | Oui |
| Hestia → HA display name | Oui |
| Hestia → HA entity_id | Oui **si** option admin |
| Passerelle / HA → logical_name (écriture) | **Non** |
| Lecture passerelle / HA (dérive) | Oui |

### 5.6 Conflits / dérives

| Situation | Résolution |
|-----------|------------|
| Rename manuel Z2M ou HA | Signalement dérive ; défaut « réappliquer Hestia » ; import vers Hestia = exception journalisée |
| Collision slug / entity_id | Refus + alternative |
| Rename pendant offline | `pending_ops` |

---

## 6. Cas particuliers et limites

### 6.1 Disparition

`OFFLINE_GRACE` → `offline` si `active`/`synced`. Sinon `unreachable`. Pas de suppression auto.

### 6.2 Factory reset

Même ancre → rattacher la fiche ; dérive friendly → réappliquer Hestia. NWK seul change → sans impact identité.

### 6.3 Remplacement physique

Modèle **deux fiches** :

- Prédécesseur → `replaced` + `successor_id`
- Successeur → **nouveau** `hestia_device_id`, métadonnées métier copiées, `predecessor_id`, cycle UX-003 jusqu'à `active`

`hestia_device_id` du prédécesseur **jamais** réutilisé.

### 6.4 Renommage hors procédure

§5.6 — pas d'import silencieux.

### 6.5 Changement de pièce

SoT Backend Hestia uniquement. Pas d'écriture HA Areas en v1. Convention de nom incluant la pièce → déclenche §5.4.

### 6.6 Doublons

| Cas | Traitement |
|-----|------------|
| Même ancre, deux fiches non terminales | Fusion : garder la plus avancée ; journaliser |
| Deux ancres, même nom logique | Refus second nom |
| Ré-appairage Z2M « nouveau » mais même IEEE | Rattacher |

### 6.7 Longue absence

Retour → `active` (ou `synced`) ; si ≥ `LONG_ABSENCE` : + alerte. `deleted` : pas de résurrection. `disabled` : reste disabled.

### 6.8 Scénarios de panne / reprise (normatifs)
<!-- ancre stable : #module-70-reprises -->
<a id="module-70-reprises"></a>

| Scénario | Comportement attendu |
|----------|----------------------|
| **Perte de courant** milieu de sync | Au redémarrage : lire Backend ; si `sync_status≠ok` → reprendre sync idempotente depuis `provisioned` ; pas d'état demi-sync |
| **Broker MQTT corrompu / recréé** | INT-001 / module 50 remettent credentials ; Agent se reconnecte ; dérives noms détectées ; réappliquer Hestia ; états métier **inchangés** |
| **Suppression / réinstall HA** | Liens `ha_bindings` invalidés → `sync_status=failed` ; état ramené à `provisioned` si device/entities absents ; Admin lance resync (redécouverte MQTT) ; **pas** de perte des fiches métier Backend |
| **Suppression / réinstall Z2M** | `protocol_bindings` invalidés ; même traitement ; ré-appairage radio peut être requis (ancres IEEE peuvent réapparaître) ; rattachement par ancre |
| **Restauration sauvegarde Hestia (Backend)** | Backend restaure SoT ; Agent réconcilie ; si backends plus récents/différents → dérives signalées, Hestia gagne après confirmation ou auto-réapply selon politique nœud |
| **Restauration sauvegarde Z2M ou HA seule** | Risque de dérive / orphelins ; Agent inventorie ; Admin : réapply Hestia ou rattacher |
| **Changement de coordinateur Zigbee** | Si réseau Zigbee migré (backup Z2M/coordinateur) : IEEE stables → rattachement ; si **nouveau** réseau : devices à réappairer → apparaissent `detected`, rattacher manuellement aux fiches offline ou wizard remplacement |
| **Plusieurs semaines d'absence** | §6.7 |
| **Capteur remplacé physiquement** | §6.3 |

### 6.9 Désactivation et suppression — effet backends

| Action | Backend Hestia | Z2M / HA (v1) |
|--------|----------------|---------------|
| **Désactiver** | `disabled` ; exclu exploitation | **Aucun** unpair / delete automatique |
| **Supprimer** (défaut) | `deleted` soft | **Aucun** unpair automatique |
| **Supprimer + retirer technique** | `deleted` | Option admin explicite : unpair Z2M et/ou archive device HA |

---

## 7. Responsabilités (sans chevauchement)

| Responsabilité | Z2M | MQTT | HA | Installer | Admin | Agent | Backend |
|----------------|:---:|:----:|:--:|:---------:|:-----:|:-----:|:-------:|
| Appairage radio | **R** | — | — | — | Déclenche | Exécute commande | — |
| Transport messages | — | **R** | — | — | — | Client | — |
| Discovery MQTT / entités | Publie | Transporte | **R** | — | — | Observe | — |
| INT-001 HA↔broker | — | Broker | Intégration | **R** | — | — | — |
| Déploiement plateforme | — | — | — | **R** | — | — | — |
| Décisions métier (MS, rename, delete, replace) | — | — | — | — | **R** | — | Persiste |
| Transitions techniques auto (`active`↔`offline`, détection) | — | — | — | — | — | **R** | Persiste |
| SoT modèle fonctionnel | — | — | — | — | UI | Réplique | **R** |
| Exécution sync backends | — | — | — | — | Demande | **R** | Enregistre résultat |
| Mise en service guidée UX-003 | Interdit UI | — | Interdit UI | Interdit | **R** | Support | Persiste |
| Télémétrie UI user | Source | Transporte | États | — | — | Relais | Agrège |

**R** = responsable unique. Admin décide ; Agent exécute le technique ; Backend conserve la vérité.

---

## 8. Script installateur `70-hestia-agent`

> **Historique / remplacé pour V0.1.0 (INT-002B) :** les points 3 et 5 (Backend/MQTT, enregistrement nœud) ne sont **pas** implémentés par le module 70 actuel. Conservés ici comme trace de conception antérieure.

Runtime V1 : **processus Linux natif** ; supervision **systemd** (pas de conteneur Docker).

**Module 70 — déploiement technique (pas de `systemctl`) — réalité INT-002B :**

1. Récupérer une **version explicitement figée** (`vendor/hestia-agent/<VERSION>/` — jamais `latest`) ;
2. Déployer les fichiers (idempotent) ;
3. Générer `agent.conf` (AGENT_VERSION, LOG_DIR, RUNTIME_DIR, rotation) — **sans** Backend/MQTT/HA ;
4. Produire le fichier unit `.service` (pour le module 80) ;
5. ~~Tenter l'enregistrement du nœud (best-effort)~~ — **hors périmètre V0.1.0 / INT-002B**.

**Succès module 70 :** version installée, fichiers présents, configuration présente, `.service` produit.  
**Démarrage / enable / health runtime du service :** exclusifs au module **80** (unique propriétaire systemd).

Contrat Installer ↔ Agent (minimal) : version, emplacements install/config, conventions de fichiers, health contract.  
L'Agent ignore GitHub ; pas d'auto-mise à jour (mises à jour = Installer uniquement).

Aucune API de cycle de vie dans `install/70-hestia-agent.sh`.

---

## 9. Conformité modules 40 / 50 / 60

| Module | Attente Module 70 | Non remis en cause |
|--------|-------------------|--------------------|
| 40 HA | API états / devices pour sync | `network=host`, backend |
| 50 Mosquitto | Topics + compte Agent | `127.0.0.1:1883` |
| 60 Z2M | Bridge, rename, permit-join | by-id, frontend ≠ MS |

INT-001 = prérequis découverte HA pour `detected`→`synced`.

---

## 10. Évolutivité

| Cible | Validité du modèle v1 | Adaptation requise |
|-------|----------------------|--------------------|
| **Bluetooth / Wi-Fi natif** | Oui | Nouvel adaptateur `protocol` + ancre ; pas de Z2M |
| **Matter / Z-Wave** | Oui (principe) | Adaptateur + éventuellement autre bus que MQTT ; schéma `protocol_bindings` |
| **Plusieurs coordinateurs Zigbee** | **Hors v1** | v1 = **une** instance Z2M / nœud ; multi-coord = multi-adaptateurs + clé `(protocol, adapter_id, anchor)` |
| **Plusieurs logements** | **Hors v1** | Introduire `home_id` au-dessus de `node_id` ; unicité noms par logement |
| **Plusieurs HA** | **Hors v1** | `ha_bindings` deviendrait multi-cibles ; aujourd'hui **un** HA / nœud |

**Dépendance Z2M :** limitée à l'adaptateur Zigbee. Le cœur Module 70 (`hestia_device_id`, états, SoT noms, Admin) **ne dépend pas** de Z2M.

---

## 11. Hors périmètre v1

- Sync Areas HA ;
- Résurrection auto des `deleted` ;
- Multi-nœuds / multi-HA / multi-logements / multi-coordinateurs ;
- Alignement systématique des `entity_id` (option avancée seulement) ;
- Code d'implémentation.

---

## 12. Index des références

| Document | Usage |
|----------|-------|
| [ADR-005](../adr/ADR-005-cycle-vie-equipements.md) | Décisions structurantes |
| [ADR-004](../adr/ADR-004-mise-en-service-equipements.md) | Frontière Installer / Admin |
| `hestia-installer` → ARCHITECTURE.md | Vue d'ensemble modules install |
| `hestia-installer` → BACKLOG (UX-003) | Parcours UI Phase 2 |
| `hestia-installer` → ROADMAP.md | Planification |
| `hestia-installer` → ETAT-PROJET.md | Suivi |
