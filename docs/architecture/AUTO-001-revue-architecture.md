# Revue d’architecture AUTO-001 — Gate Ready, identité nœud, remplacement, sauvegarde

| Attribut | Valeur |
|----------|--------|
| **Objet** | Revue avant lancement des lots AUTO-001A … AUTO-001F |
| **Référence** | [`AUTO-001-reconnection-autonome-noeud.md`](AUTO-001-reconnection-autonome-noeud.md) |
| **Statut** | Décisions de revue (normatives pour la suite) |
| **Code / implémentation** | Aucun — document uniquement |
| **Date** | 2026-07-26 |

---

## 0. Cadre et principes (filtre obligatoire)

Toute option ci-dessous a été filtrée par :

| Principe | Conséquence pour cette revue |
|----------|------------------------------|
| Nœud autonome | Le mini-PC doit pouvoir redémarrer et se rattacher **seul**. |
| Serveur = autorité | Identité logique, révocation, remplacement **décidés / enregistrés** côté serveur (à terme) ; l’Agent n’invente pas de politique métier. |
| Admin via le serveur | Pas de procédure nominale « se connecter en SSH pour régénérer l’ID ». |
| Zéro intervention locale en régime | Provisionnement initial et remplacement matériel = **exceptions de mise en service**, pas le quotidien. |
| Simplicité > sophistication | Rejeter fingerprint matériel complexe, consensus multi-nœuds, PKI complète **tant qu’ils n’apportent pas de valeur utilisateur immédiate**. |

**Rejets explicites (contraires aux principes ou surdimensionnés pour Phase 1) :**

| Proposition | Motif de rejet |
|-------------|----------------|
| Gate Ready **codé en dur** « toujours HA + MQTT + Z2M + Docker » | Cassera les profils sans Zigbee / sans HA ; viole l’extensibilité simple. |
| Gate Ready **100 % libre** (opérateur liste des sondes ad hoc sans profil) | Complexité ops, configs divergentes, support difficile. |
| `node_id` dérivé automatiquement du numéro de série / MAC à chaque boot | Fragile (changement NIC), opaque, difficile à remplacer « en conservant l’identité ». |
| Auto-génération d’un nouvel ID si collision détectée | L’Agent ne doit pas décider d’une nouvelle identité métier ; le serveur / admin oui. |
| Exiger une intervention SSH pour rattacher un nœud de remplacement | Contredit l’admin via serveur (même si la **première** install physique reste inévitable). |
| Concevoir le système de sauvegarde dans AUTO-001 | Hors sujet ; cette revue **inventorie** seulement. |

---

## Sujet 1 — Gate Ready

### 1.1 Question

Le gate doit-il être **fixe** ou **entièrement piloté par la configuration** ?

### 1.2 Approches comparées

| Approche | Description | Avantages | Inconvénients |
|----------|-------------|-----------|---------------|
| **A. Fixe** | Toujours : réseau + MQTT + HA (+ Z2M). | Prévisible ; une seule sémantique de « Ready ». | Inadapté aux profils sans HA / sans Zigbee ; faux négatifs bloquants. |
| **B. 100 % conf libre** | Liste arbitraire de checks dans la conf. | Maximale flexibilité. | Explosion de modes ; docs/support coûteux ; risque de configs « Ready » vides ou incohérentes. |
| **C. Piloté par la conf *effective*** (recommandé) | Le gate = **union des composants déjà activés** par la conf Agent/Installer. Pas de check pour un composant non configuré. | Simple, aligné sur l’existant EPIC-001 (`HA_URL`, `MQTT_TOPICS`, …), extensible. | Il faut documenter clairement « absente = non exigée ». |
| **D. Profils nommés** (`gateway`, `ha-only`, `agent-only`) | Un enum de profils + overrides. | Lisibilité produit. | Couche supplémentaire ; risque de double source de vérité avec les clés déjà présentes. |

### 1.3 Analyse par profil

| Profil mini-PC | Essentiels pour annoncer Ready | Non essentiels (dégradé / info) |
|----------------|--------------------------------|----------------------------------|
| Passerelle domestique V1 (HA + MQTT + Z2M) | Réseau sortant ; MQTT si `MQTT_TOPICS` ; HA si `HA_URL` | Conteneurs : signalés si OPS dispo, sinon `unknown` **sans mentir** |
| Sans Zigbee | Idem sans exiger Z2M | Z2M absent ≠ blocage si non déployé |
| Sans HA (Agent + MQTT seul, futur) | Réseau ; MQTT si configuré | HA non configuré → **non exigé** |
| Rôle futur « edge léger » (ingest seulement) | Réseau + backend | Locaux optionnels selon conf |

### 1.4 Recommandation finale (Sujet 1)

**Retenir l’approche C — gate dérivé de la configuration effective**, avec règles stables :

1. **Toujours** : chemin réseau sortant (sinon pas de lien serveur).
2. **Si** `MQTT_TOPICS` (ou équivalent MQTT activé) → MQTT exigé pour Ready.
3. **Si** `HA_URL` → HA exigé pour Ready.
4. **Z2M / Docker** : jamais bloquants *par défaut* ; contribution au snapshot (`services` / `health`) ; bloquants seulement si une clé conf explicite future l’exige (ex. `READY_REQUIRE_Z2M=1`) — **non requis en Phase 1**.
5. Composant **non configuré** → non testé pour le gate (pas de faux `OK`).
6. Composant configuré mais sonde impossible (pas root) → `unknown` documenté ; **ne pas** afficher un vert mensonger ; politique Phase 1 : `unknown` sur un essentiel **configuré** **bloque** Ready (prudence) *ou* se limite aux sondes déjà possibles sans privilège — **retenir : Ready ne passe que sur essentiels *prouvés* OK ; `unknown` sur un essentiel configuré = pas Ready**.

**Profils nommés (D)** : reportés ; on peut les ajouter plus tard comme *sucre* au-dessus de C, sans changer la sémantique.

### 1.5 Impact AUTO-001

- La spécification actuelle (gate MQTT/HA « selon conf ») est **déjà alignée** sur C.
- **Aucun changement bloquant** de la FSM.
- Clarification rédactionnelle utile (addendum une phrase) : « essentiels = composants activés par conf ; Z2M non bloquant Phase 1 ».

---

## Sujet 2 — Cycle de vie de l’identité (`node_id`)

### 2.1 Distinction d’identifiants (rappel)

| Identifiant | Portée | Autorité |
|-------------|--------|----------|
| `node_id` | Mini-PC / instance Agent (passerelle) | Provisionnement nœud |
| `hestia_device_id` | Équipement métier (capteur, etc.) | Serveur (EPIC-002) — **≠** `node_id` |
| `event_id` | Événement | Agent (UUID) |
| Token Bearer | Auth transport Agent→API | Serveur (secret) |

Confusion `node_id` / `hestia_device_id` : **interdite**.

### 2.2 Questions / réponses (modèle simple retenu)

#### Comment est-il créé ? À quel moment ?

| Étape | Qui | Quoi |
|-------|-----|------|
| Mise en service | **Installer** (ou admin serveur à terme) | Attribue un `node_id` **lisible et stable** (ex. `hestia-bmax`, `maison-a-salon`) + écrit la conf Agent |
| Premier heartbeat | Agent | **Annonce** l’ID déjà choisi ; le serveur **upsert** le registre |

Le serveur Phase 1 **n’alloue pas** encore l’ID (upsert libre si token global). L’allocation serveur stricte (création préalable + token dédié) est le modèle cible (lot F / ADR identité).

#### Est-il immuable ?

**Oui**, pour la durée de vie de l’*identité logique* du nœud dans le foyer.

Changer de `node_id` = **nouvel** enregistrement logique (sauf procédure de remplacement / fusion admin — Sujet 3).

#### Peut-il être régénéré ?

| Cas | Autorisé ? |
|-----|------------|
| Auto par l’Agent | **Non** |
| Opérateur local « pour dépanner » | **Non** (nominal) |
| Admin serveur (révocation + nouvelle identité) | **Oui** (cible) |
| Remplacement matériel **en conservant** l’ID | **Oui** — même `node_id`, nouveau secret (cible) |

#### Doublon / deux nœuds même identité ?

**Phase 1 (token global)** : deux machines avec le même `NODE_ID` + même token → **dernier heartbeat gagne** (écrasement snapshot). C’est un **risque accepté documenté**, pas une détection fine.

**Cible (token par nœud)** :

- Enregistrement serveur : `node_id` → `token_hash`, état `active|revoked`.
- Collision : second matériel avec même ID mais mauvais token → `401`.
- Deux actifs impossible : un seul token actif par `node_id`.

Détection « soft » Phase 1 (optionnelle, non bloquante) : publier `boot_id` / empreinte faible dans le snapshot ; l’admin voit un changement brutal de `boot_id` sans reboot attendu — **pas** de décision automatique.

#### Clonage de SSD ?

| Risque | Effet |
|--------|-------|
| Clone bit-à-bit | Même `node_id` + même token → deux Agents « légitimes » → course au heartbeat |

**Mitigations simples (par maturité)** :

1. **Phase 1** : procédure — ne pas cloner un nœud déjà enrôlé ; documenter le risque.
2. **Cible** : token par nœud + rotation à la remise en service ; clone sans nouveau token = un seul « gagne », l’autre révoqué dès rotation.
3. **Rejeté pour l’instant** : binding TPM / IDevID — sophistication sans valeur utilisateur immédiate.

#### Révocation ?

| Qui | Effet |
|-----|-------|
| Admin serveur | Marque nœud `revoked` **ou** rotate le token ; heartbeats suivants → `401` |
| Agent | Ne peut pas se « auto-révoquer » pour échapper à l’autorité serveur |

Phase 1 : révocation = **changer le token global** (impacte tous les nœuds) — insuffisant multi-nœuds → motive le token par nœud (AUTO-001F).

#### Remplacer le mini-PC en conservant l’identité ?

Oui (modèle cible) : **même `node_id`**, nouveau matériel, **nouveau token** poussé à l’install / commande distante, historique registre conservé côté serveur. Détail Sujet 3.

#### Que reste côté serveur vs mini-PC ?

| Élément | Serveur (SoT) | Mini-PC |
|---------|---------------|---------|
| `node_id` (identité logique) | Registre + métadonnées admin (label, foyer, revoked) | Copie conf (`NODE_ID`) |
| Token | Hash / secret (autorité) | Secret runtime conf |
| Snapshot présence / last_seen | Oui | Non (éphémère) |
| Conf MQTT/HA locale | Non (détail local) | Oui |
| File L6, logs locaux | Non | Oui |
| Parc `hestia_device_id` | Oui (EPIC-002) | Non |

### 2.3 Recommandation finale (Sujet 2)

1. `node_id` = **identifiant logique provisionné**, immuable, **non** dérivé du hardware à chaque boot.
2. Création à la **mise en service** (Installer aujourd’hui ; serveur demain).
3. Phase 1 : conserver upsert + token ingest **global** (pragmatisme), en documentant la limite clonage/doublon.
4. Cible multi-nœuds sûr : **token par `node_id` + révocation admin** (AUTO-001F / ADR).
5. Aucune auto-régénération Agent.

### 2.4 Impact AUTO-001

- **Pas de frein** aux lots A–E (présence).
- Prévoir dans 001A : stocker `node_id` tel quel ; ne pas inventer d’allocation serveur obligatoire Phase 1.
- AUTO-001F (+ ADR) : auth par nœud, révocation, anti-doublon fort.

---

## Sujet 3 — Remplacement d’un mini-PC

### 3.1 Scénario

```text
mini-PC détruit / HS
    → nouveau matériel
    → réinstallation (Installer)
    → retour dans Hestia (ONLINE)
```

### 3.2 Deux intentions admin (à ne pas confondre)

| Intention | `node_id` | Effet |
|-----------|-----------|-------|
| **Continuité** (même foyer / même « boîte logique ») | **Conservé** | Même fiche nœud ; historique last_seen / label ; équipements du site restent rattachés au même nœud logique |
| **Nouveau nœud** | **Nouveau** | Ancien ID → `revoked` ou archivé ; nouvelle fiche |

### 3.3 Flux recommandé — continuité (cas nominal de remplacement)

1. **Admin (serveur)** — prépare le remplacement (cible) : conserve `node_id`, **émet un nouveau token** (invalide l’ancien), marque éventuel `replace_pending`.
2. **Mise en service physique** — Installer sur le nouveau matériel avec **le même `NODE_ID`** + nouveau token + même `BACKEND_URL` (bootstrap : clé one-shot, QR, ou conf préparée — **chantier provisioning**, pas AUTO-001 présence).
3. **Agent** — gate Ready → heartbeat → serveur reconnaît le `node_id` connu → `ONLINE`.
4. **Continuité admin** — l’UI nœud / foyer ne change pas d’identité ; l’opérateur voit un retour ONLINE après coupure.

**Phase 1 (sans token par nœud)** : continuité = réinstaller avec le **même** `NODE_ID` et le **même** token global ; le serveur upsert. Limite : l’ancien disque encore vivant crée un doublon (Sujet 2) — d’où l’intérêt de détruire / ne pas rebrancher l’ancien.

### 3.4 Éviter les incohérences

| Risque | Mitigation |
|--------|------------|
| Deux machines actives même ID | Token unique actif ; ne pas rebrancher l’ancien ; Phase 1 = discipline ops |
| Changer d’ID « pour faire propre » sans le vouloir | Perte de continuité admin ; documenter l’intention Continuité vs Nouveau |
| Conf locale HA/Z2M différente | Attendu ; snapshot reflète le nouvel état ; SoT équipements reste serveur (EPIC-002) |
| File L6 de l’ancien nœud | Perdue avec le disque ; **régénérable** (nouveaux events) — pas bloquant présence |

### 3.5 Recommandation finale (Sujet 3)

- Remplacement **avec continuité** = même `node_id` + (cible) rotation token **côté serveur**.
- Retrouver le nœud = **même clé de registre** `node_id`, pas une heuristique hardware.
- Bootstrap initial du nouveau matériel reste une **mise en service** (Installer) ; le quotidien ensuite = autonomie + admin serveur.
- Ne **pas** charger AUTO-001A–E d’un workflow UI de remplacement complet.

### 3.6 Impact AUTO-001

- Spécification présence : **inchangée**.
- Documenter le scénario en addendum court (lien vers cette revue).
- Workflow admin remplacement + token : **AUTO-001F** ou epic provisioning / EPIC-011.

---

## Sujet 4 — Sauvegarde (analyse de besoins uniquement)

Aucune conception de système de backup. Inventaire pour un **futur chantier dédié**.

### 4.1 Appartenance

| Donnée | Serveur | Mini-PC | Indispensable pour « restaurer un nœud » ? | Régénérable ? |
|--------|---------|---------|--------------------------------------------|---------------|
| Registre nœud (`node_id`, last_seen, snapshot) | Oui | Non | Utile (continuité admin) | Partiellement (re-heartbeat) |
| Token / secrets nœud | Oui (autorité) | Copie runtime | **Oui** (sinon pas d’auth) | Non (réémission admin) |
| Métadonnées admin nœud (label, foyer) | Oui | Non | Oui pour UX | Non |
| Parc `Equipment` / `hestia_device_id` | Oui | Non | Oui métier | Non |
| Users / sessions PWA | Oui | Non | Hors nœud | — |
| Events ingest historiques | Oui | Non | Non pour présence | Souvent non critiques |
| `agent.conf` (NODE_ID, URLs, topics) | Idéalement copie/provision | Oui | **Oui** pour rattachement | Re-provision Installer |
| Secrets locaux MQTT/HA | Non | Oui | Oui pour Ready local | Re-saisie / vault futur |
| Runtime Agent (files L6, locks) | Non | Oui | Non | **Oui** (vide au boot) |
| Volumes Docker HA / Z2M / Mosquitto | Non | Oui | Selon RPO foyer | Partiel (re-pair Zigbee = douleur) |
| OS / images | Non | Oui | Installer | Oui via réinstall |

### 4.2 Lectures pour le futur chantier backup

1. **Priorité serveur** : identité nœud, tokens, métadonnées, SoT équipements — c’est ce qui préserve l’administration.
2. **Priorité mini-PC** : conf Agent + (si besoin métier) volumes HA/Z2M — distinct du chantier présence.
3. **Ne pas** sauvegarder comme SoT : files L6, pidfiles, snapshots présence (reconstruits).
4. Clonage SSD ≠ backup maîtrisé (voir Sujet 2).

### 4.3 Impact AUTO-001

**Aucun** sur les lots A–F présence/commandes. Reporter : epic **BACKUP-*** / OPS backup (à créer plus tard).

---

## Synthèse des recommandations

| Sujet | Décision |
|-------|----------|
| Gate Ready | **Dérivé de la conf effective** (approche C) ; Z2M non bloquant Phase 1 ; pas de profils nommés pour l’instant |
| `node_id` | Logique, **provisionné**, **immuable** ; pas d’auto-regen Agent ; token par nœud + révocation = cible |
| Remplacement | Continuité = **même `node_id`** (+ rotation token cible) |
| Sauvegarde | Inventaire seulement ; chantier séparé |

---

## ADR à créer (proposés)

| ADR | Contenu | Priorité |
|-----|---------|----------|
| **ADR-0xx — Identité de nœud (`node_id`)** | Immutabilité, provisionnement, ≠ `hestia_device_id`, révocation, remplacement, limites Phase 1 token global | Avant ou avec AUTO-001F |
| **ADR-0xy — Gate Ready dérivé de conf** | Règles C ; `unknown` ; non-blocage Z2M | Optionnel si addendum AUTO-001 suffit (préférer addendum court + ADR seulement si débat récurrent) |
| **ADR backup** | Hors scope — ne pas créer maintenant | Plus tard |

---

## Impacts sur AUTO-001 et lots

### Modification de la spécification AUTO-001 ?

| Zone spec | Action |
|-----------|--------|
| FSM, heartbeat, TTL, RECONNECTING, DEGRADED | **Aucun changement** |
| Gate Ready | **Clarification** (conf effective, Z2M) — non bloquante |
| Identité / remplacement / backup | **Hors cœur Phase 1** ; références à cette revue + ADR futurs |

**Conclusion :** aucune modification **bloquante** de la spécification AUTO-001 n’est nécessaire pour démarrer.

### Lots

| Lot | Peut être engagé ? | Notes issues de la revue |
|-----|--------------------|--------------------------|
| **AUTO-001A** | **Oui** | Upsert par `node_id` ; pas d’allocation serveur obligatoire |
| **AUTO-001B** | **Oui** | Gate = conf effective (déjà prévu) |
| **AUTO-001C** | **Oui** | Docs : mentionner risque clonage SSD / même ID |
| **AUTO-001D** | **Oui** | — |
| **AUTO-001E** | **Oui** | — |
| **AUTO-001F** | Oui **après** A–E | Token par nœud, révocation, amorces remplacement admin |

Les lots **AUTO-001A à AUTO-001F** peuvent être engagés **sans changer** le cœur de la spécification Phase 1 ; F porte les sujets identité forte / admin remplacement.

---

## Sujets reportés (hors AUTO-001 présence)

| Sujet | Destination |
|-------|-------------|
| Token par nœud + révocation UI | AUTO-001F / ADR identité nœud |
| Workflow remplacement guidé (serveur) | AUTO-001F ou EPIC-011 / provisioning |
| Profils nommés de gate | Plus tard (sucre sur conf) |
| Empreinte anti-clonage avancée (TPM, etc.) | Rejeté / très distant |
| Système de sauvegarde | Chantier BACKUP dédié |
| Binding nœud ↔ foyer / multi-logement | EPIC habitat / plus tard |

---

## Décision de go

**GO** pour lancer **AUTO-001A → AUTO-001E** sur la base de la spécification actuelle, enrichie mentalement par cette revue (gate conf-effective, `node_id` provisionné immuable, limites token global assumées).

**AUTO-001F** reste Phase 2 et absorbe l’essentiel des réponses « autorité serveur » sur identité et remplacement.
