# AUTO-002F — Identité et cycle de vie des nœuds

| Attribut | Valeur |
|----------|--------|
| **Lot** | AUTO-002F |
| **Phase** | **F1→F6 livrés** — chantier AUTO-002F / AUTO-002 **clôturés** (2026-07-29) |
| **Nature** | Spécification fonctionnelle et technique (normative) |
| **Statut** | **Implémenté** — modèle d’identité définitif (plus de token global) |
| **Prérequis** | AUTO-002A→E validés ; stub §002F dans [AUTO-002](AUTO-002-supervision-administration-noeuds.md) |
| **Références amont** | [AUTO-001 revue](AUTO-001-revue-architecture.md) Sujets 2–3 · [ADR-023](../adr/ADR-023-terminal-distant-websocket-sortant.md) · Glossaire |
| **ADR associé** | **[ADR-021](../adr/ADR-021-identite-credentials-noeud.md)** — **Accepté** (2026-07-29) |
| **Dépôts concernés** | `hestia`, `hestia-agent`, `hestia-installer`, `hestia-docs` |
| **Hors périmètre F1** | Code, migrations données, UI livrable, bootstrap Installer implémenté |

> Ce document **remplace et détaille** le stub § AUTO-002F de la spécification AUTO-002.  
> Le relay WebSocket (ADR-023) reste un **simple transport** : aucune logique d’identité, de token ou de cycle de vie n’y est ajoutée.

---

## 1. Objectif

Définir le modèle d’**identité permanente** d’un nœud Hestia, son **authentification par token**, son **cycle de vie autoritaire**, les **états** et les **opérations d’administration**, de façon que le serveur reste la seule autorité de confiance et que le parc multi-nœuds ne dépende plus d’un token ingest global.

### 1.1 Problème actuel (post-002E)

| Élément | État aujourd’hui | Problème |
|---------|------------------|----------|
| Auth Agent | Token **global** `ingest.node_token` / `BACKEND_TOKEN` | Compromission ou rotation = impact **tout le parc** |
| Binding | Bearer **non** lié au `node_id` | Un Agent peut usurper n’importe quel `node_id` s’il a le token global |
| Lifecycle | Champ registre déclaratif | `revoked` / `suspended` **n’empêchent pas** heartbeat / commandes |
| Hostname | Absent du modèle | Confusion possible avec `node_id` / `display_name` |
| Credentials | `token_id` = métadonnée texte | Pas de hash secret, pas de rotation, pas d’affichage one-shot |
| Upsert | Heartbeat crée le snapshot librement | Nœud non enregistré encore accepté |

### 1.2 Objectifs F (cible)

1. Chaque nœud a une identité logique **permanente** (`node_id`).
2. Chaque nœud authentifié possède **au plus un token actif**, qui lui appartient exclusivement.
3. Le serveur valide **Bearer ↔ `node_id`** et refuse les nœuds non autorisés selon leur état.
4. L’admin peut créer, générer / révoquer / régénérer un token, désactiver et supprimer un nœud.
5. Hostname et nom affiché restent **mutables** et distincts de `node_id`.
6. Remplacement matériel = **même `node_id`**, nouveau secret (continuité logique).

---

## 2. Principes non négociables

| # | Principe | Conséquence |
|---|----------|-------------|
| I1 | **Serveur = autorité** | Création, révocation, états, secrets : décidés et stockés côté serveur. |
| I2 | **Agent = porteur** | L’Agent transporte `NODE_ID` + secret ; il ne régénère jamais l’identité ni le token seul. |
| I3 | **`node_id` permanent** | Immuable pour la durée de vie de l’identité logique. Changer d’ID = nouvel enregistrement. |
| I4 | **Hostname mutable** | Valeur observationnelle (OS / Agent) ; jamais clé d’auth ni d’identité. |
| I5 | **Display name mutable** | Label admin UX ; jamais clé d’auth ni d’identité. |
| I6 | **Un token ↔ un nœud** | Un secret actif n’appartient qu’à un seul `node_id`. Pas de token partagé parc. |
| I7 | **Relay = transport** | ADR-023 inchangé : pas d’émission/révocation token, pas d’états lifecycle dans `hestia-ws-relay`. |
| I8 | **Présence ≠ lifecycle** | `ONLINE` / `OFFLINE` (TTL heartbeat) reste orthogonal aux états `provisioned` / `active` / … |
| I9 | **Pas d’auto-identité Agent** | Pas de dérivation MAC/série, pas d’auto-création d’ID, pas d’auto-révocation. |
| I10 | **Secret jamais re-lisible** | Le clair n’est stocké nulle part côté serveur ; affichage **une seule fois** à l’émission. |

---

## 3. Modèle d’identifiants

### 3.1 Tableau des identifiants

| Identifiant | Portée | Autorité | Mutabilité | Rôle |
|-------------|--------|----------|------------|------|
| **`node_id`** | Instance logique Agent / mini-PC | Serveur (création registre) ; copie conf Agent | **Immuable** après création | Clé primaire d’identité, auth, commandes, sessions, artefacts |
| **`display_name`** | UI admin | Admin (PATCH registre) | Mutable | Libellé humain (`Salon`, `Cave`) |
| **`hostname`** | Machine OS | Agent (télémétrie heartbeat) | Mutable | Observation ops ; **pas** d’auth |
| **`token_id`** | Credential | Serveur à l’émission | Immuable par credential ; remplacé à rotation | Référence publique du secret (logs, UI) |
| **`secret` (Bearer)** | Auth transport Agent→API | Serveur émet ; Agent stocke | Remplacé à rotation / révocation | Auth seule ; jamais loggé |
| **`bootstrap_token`** | Enrôlement one-shot | Serveur | TTL court, usage unique | Échange Installer → credential long terme |
| **`hestia_device_id`** | Équipement métier | Hors AUTO-002F | — | **≠** `node_id` (interdit de confondre) |

### 3.2 Format et contraintes

| Champ | Règle |
|-------|-------|
| `node_id` | `^[A-Za-z0-9._:-]{1,128}$` (inchangé vs Phase 1) ; choisi à la création admin / Installer, **pas** alloué à l’heartbeat |
| `display_name` | Chaîne optionnelle ≤ 256 ; vide → UI affiche `node_id` |
| `hostname` | Chaîne optionnelle ≤ 253 (DNS-like) ; fournie par Agent dans le snapshot ; serveur **ne l’utilise pas** pour l’auth |
| `token_id` | Identifiant opaque serveur (ex. `tok_` + 22 chars URL-safe) |
| `secret` | Entropie ≥ 256 bits ; préfixe optionnel `hnt_` pour détection logs ; transmis uniquement en `Authorization: Bearer` |

### 3.3 Séparation des couches

```text
Identité logique     node_id          (permanent)
Métadonnée admin     display_name     (mutable)
Observation runtime  hostname         (mutable, Agent)
Présence             ONLINE/OFFLINE   (dérivé last_seen)
Cycle de vie         lifecycle_state  (autoritaire serveur)
Credential           token_id+hash    (1 actif / nœud)
```

---

## 4. Entités de données (modèle logique)

### 4.1 NodeRegistry (existant, étendu)

Fiche autoritaire du nœud. Stockage cible : `data/node-registry/{node_id}.json` (ou équivalent).

| Champ | Type | Notes |
|-------|------|-------|
| `node_id` | string | PK, immuable |
| `display_name` | string? | Mutable |
| `hostname` | string? | Dernière valeur connue (miroir snapshot) — **optionnel en F2** si déjà dans NodeStore |
| `site` | string? | Existant |
| `tags` | string[] | Existant |
| `notes` | string? | Existant |
| `hardware_profile` | string? | Existant |
| `lifecycle_state` | enum | Voir §5 |
| `active_token_id` | string? | FK logique vers credential actif ; `null` si aucun |
| `agent_version_expected` | string? | Existant |
| `created_at` / `updated_at` | ISO-8601 | |
| `disabled_at` | ISO-8601? | Rempli si `suspended` |
| `revoked_at` | ISO-8601? | Rempli si `revoked` |
| `replaced_at` | ISO-8601? | Rempli si `replaced` |
| `deleted_at` | ISO-8601? | Soft-delete si retenu ; sinon absence de fichier = deleted |

> Le champ legacy `token_id` (métadonnée libre) est **remplacé** sémantiquement par `active_token_id` pointant vers `NodeCredential`. Migration documentée en F2.

### 4.2 NodeCredential

Nouveau store : `data/node-credentials/{token_id}.json` (ou index par `node_id`).

| Champ | Type | Notes |
|-------|------|-------|
| `token_id` | string | PK |
| `node_id` | string | Propriétaire exclusif |
| `secret_hash` | string | Hash one-way (argon2id ou bcrypt cost élevé ; algo versionné) |
| `label` | string? | Ex. `initial`, `rotation-2026-07`, `replace` |
| `issued_at` | ISO-8601 | |
| `revoked_at` | ISO-8601? | `null` = potentiellement actif |
| `revoked_reason` | enum? | `admin_revoke` \| `rotation` \| `replace` \| `node_delete` \| `node_disable` |
| `created_by` | string? | Identifiant session admin (audit) |

**Invariant :** pour un `node_id` donné, **au plus une** credential avec `revoked_at = null`.

### 4.3 BootstrapToken (F4)

| Champ | Type | Notes |
|-------|------|-------|
| `bootstrap_id` | string | |
| `node_id` | string | Nœud pré-créé `provisioned` |
| `secret_hash` | string | |
| `expires_at` | ISO-8601 | TTL court (ex. 15–60 min) |
| `used_at` | ISO-8601? | One-shot |
| `created_by` | string? | |

### 4.4 NodeStore (snapshot présence — inchangé dans son rôle)

Continue de porter télémétrie (`last_seen`, sondes, éventuellement `hostname` runtime).  
**Ne décide pas** du droit d’auth. Un snapshot orphelin sans registre valide n’est plus accepté en régime post-F2 (sauf fenêtre de migration explicite).

---

## 5. États du cycle de vie

### 5.1 États retenus

| État | Signification | Auth Agent (Bearer valide) | Poll commandes / WS session | Visible parc admin |
|------|---------------|----------------------------|-----------------------------|--------------------|
| **`provisioned`** | Créé côté serveur ; en attente premier rattachement réussi | **Oui** (si token actif) | Oui | Oui |
| **`active`** | En service nominal | **Oui** | Oui | Oui |
| **`suspended`** | Désactivé admin (temporaire) | **Non** (401/403) | Non | Oui (badge désactivé) |
| **`revoked`** | Identité définitivement retirée du service | **Non** | Non | Oui (archivage / filtre) |
| **`replaced`** | Continuité matérielle terminée côté ancien secret ; fiche conservée pour historique **ou** état terminal après replace — voir §5.3 | **Non** (sauf nouveau token post-replace → repasse `active`) | Selon token | Oui |
| **`discovered`** | (Legacy / transition) Snapshot vu sans fiche — **déprécié** post-F2 | Non (cible) | Non | Optionnel migration |

**Présence** `ONLINE` / `OFFLINE` : calculée uniquement si auth + lifecycle permettent le heartbeat ; un nœud `suspended` apparaît OFFLINE (ou « désactivé ») sans accepter de nouvelles écritures Agent.

### 5.2 Transitions autorisées

```text
                    create()
                       │
                       ▼
               ┌──────────────┐
               │ provisioned  │
               └──────┬───────┘
                      │ first_successful_auth()  (ou activate() admin)
                      ▼
               ┌──────────────┐
          ┌───►│    active    │◄──┐
          │    └──────┬───────┘   │
          │           │           │
          │    disable()    enable()
          │           │           │
          │           ▼           │
          │    ┌──────────────┐   │
          │    │  suspended   │───┘
          │    └──────┬───────┘
          │           │
          │    revoke_node() / delete path
          │           │
 regenerate_token()   │
 (reste active ou     │
  provisioned)        ▼
               ┌──────────────┐
               │   revoked    │  ──►  delete()  ──►  (absent / soft-deleted)
               └──────────────┘

 replace_prepare() : active|provisioned → (token révoqué + nouveau token)
                     lifecycle reste active|provisioned (continuité)
                     ancien matériel hors service par révocation secret

 replace_retire()  : optionnel — marque replaced si on archive l’ancienne
                     identité sans créer de nouvelle (rare ; préférer revoke)
```

### 5.3 Table de transitions (normative)

| Depuis | Opération admin / événement | Vers | Effet credentials |
|--------|-----------------------------|------|-------------------|
| — | `create_node` | `provisioned` | Émet **ou** diffère l’émission du premier token (voir §6) |
| `provisioned` | Premier heartbeat auth OK | `active` | Inchangé |
| `provisioned` \| `active` | `disable_node` | `suspended` | Option A (retenue) : **révoque** le token actif ; Option B : conserve mais refuse auth — **retenu A** (plus sûr) |
| `suspended` | `enable_node` | `active` ou `provisioned` | **Exige** `regenerate_token` (ou token déjà réémis) avant auth |
| `*` authentifiable | `revoke_token` | état inchangé **ou** `suspended` si plus aucun token | Credential → `revoked_at` |
| `*` authentifiable | `regenerate_token` | inchangé (`provisioned`/`active`) | Ancien révoqué (`rotation`) ; nouveau actif |
| `provisioned` \| `active` \| `suspended` | `revoke_node` | `revoked` | Tous tokens révoqués |
| `revoked` \| `replaced` \| `suspended` | `delete_node` | supprimé | Credentials purgés |
| `active` \| `provisioned` | `replace` (continuité) | reste `active`/`provisioned` | Rotation forcée (= regenerate + label `replace`) |
| `active` | — | `replaced` | **Non** utilisé comme transition nominale de continuité ; réservé si on retire l’identité sans delete (archivage). **Recommandation F1 :** continuum = même `node_id` + regenerate ; `replaced` = archivage rare / migration legacy |

### 5.4 Décision F1 sur `replaced`

Pour éviter l’ambiguïté stub vs revue AUTO-001 :

- **Remplacement matériel (continuité)** = **même `node_id`** + **`regenerate_token`** (raison `replace`) + Installer écrit le nouveau secret. Lifecycle reste `provisioned`/`active`.
- L’état **`replaced`** est conservé pour compatibilité registre existant et cas « identité logique abandonnée mais fiche conservée » ; il n’est **pas** l’état intermédiaire obligatoire du workflow replace.
- ADR-021 doit figer cette sémantique.

### 5.5 Décision F1 sur désactivation vs révocation

| Opération | Intention | Réversible ? |
|-----------|-----------|--------------|
| **Désactiver** (`suspended`) | Couper le nœud temporairement (maintenance, doute) | Oui via `enable` + nouveau token |
| **Révoquer le token** | Invalider le secret sans supprimer la fiche | Oui via regenerate |
| **Révoquer le nœud** (`revoked`) | Fin de service de l’identité | Non (sauf recréation **nouveau** `node_id`) |
| **Supprimer** | Purge registre + credentials (+ optionnellement snapshots/commandes) | Non |

---

## 6. Opérations d’administration

Toutes les opérations ci-dessous sont **autoritaires serveur**, session admin (sauf bootstrap exchange).

### 6.1 Créer un nœud — `create_node`

**Entrée :** `node_id` (obligatoire), `display_name?`, `site?`, `tags?`, …  
**Effet :**

1. Refuse si `node_id` déjà existant (y compris soft-deleted non purgé — politique F2).
2. Crée `NodeRegistry` en `provisioned`.
3. Émet un `NodeCredential` actif (sauf flag `defer_token: true` pour flux bootstrap F4).
4. Retourne le **secret en clair une seule fois** + `token_id`.

**Invariant post-création :** `active_token_id` renseigné **ou** bootstrap en attente (F4).

### 6.2 Générer un token — `generate_token`

Cas : nœud sans token actif (`provisioned` avec `defer_token`, ou après revoke token).  
**Effet :** crée credential ; refuse s’il existe déjà un token non révoqué (utiliser regenerate).

### 6.3 Révoquer un token — `revoke_token`

**Effet :** `revoked_at` immédiat sur credential active ; `active_token_id = null`.  
Lifecycle : reste `provisioned`/`active`/`suspended` selon politique ; **recommandation** : passer en `suspended` si plus aucun token (évite nœud « active » sans secret).

Auth Agent : rejet immédiat (pas de grâce > 60 s — aligné stub).

### 6.4 Régénérer un token — `regenerate_token`

**Effet atomique :**

1. Révoque l’ancien (`revoked_reason = rotation` ou `replace`).
2. Émet le nouveau.
3. Retourne le clair one-shot.

Utilisé pour : rotation routine, suspicion compromission, **remplacement matériel**.

### 6.5 Désactiver un nœud — `disable_node`

**Effet :** `lifecycle_state = suspended` ; révocation du token actif (Option A §5.3) ; refuse toute API Agent pour ce `node_id`.  
Sessions WS en cours : l’API invalide / refuse le renouvellement ; le relay **ne décide pas** — il s’appuie sur `POST /internal/ws/validate` déjà existant (API reste SoT).

### 6.6 Réactiver un nœud — `enable_node`

**Effet :** `suspended` → `active` (ou `provisioned` si jamais heartbeate) ; **n’émet pas** automatiquement de token (admin doit `generate` / `regenerate`) — évite secret non récupéré.

### 6.7 Révoquer un nœud — `revoke_node`

**Effet :** `lifecycle_state = revoked` ; tous credentials révoqués ; auth définitivement refusée pour cet ID.  
La fiche reste consultable (historique). Pour réintroduire le même ID physique logique : **interdit** — créer un **nouveau** `node_id` ou procédure exception documentée (hors V1).

### 6.8 Supprimer un nœud — `delete_node`

**Préconditions recommandées :** état `revoked` \| `suspended` \| `replaced` (pas `active` sans confirmation forte).  
**Effet :**

1. Révoque credentials restantes.
2. Supprime (ou soft-delete) registre.
3. Politique artefacts / commandes / snapshots : **purge soft** en V1 (supprimer fichiers `node-registry`, credentials ; conserver events historiques anonymisés si besoin — détail F3).

### 6.9 Remplacer le matériel — `replace_node` (continuité)

Raccourci ops = `regenerate_token` + métadonnées (`label=replace`, audit) + éventuel wizard UI.  
**Même `node_id`.** Pas de nouvel enregistrement. Ancien secret mort → ancien disque inutilisable pour l’API.

---

## 7. Authentification Agent

### 7.1 Flux nominal (post-F2)

```text
Agent                      Serveur
  │  Authorization: Bearer <secret>
  │  body/path node_id = N
  │───────────────────────────────►│
  │                                │ 1. Résoudre credential par hash(secret)
  │                                │    ou vérifier hash pour node_id N
  │                                │ 2. credential.node_id == N ?
  │                                │ 3. credential.revoked_at == null ?
  │                                │ 4. registry.lifecycle ∈ {provisioned, active} ?
  │                                │ 5. registry existe ?
  │◄──────── 200 / 401 / 403 ─────│
```

### 7.2 Règles de validation (`authorizeNodeForId`)

Hook déjà réservé dans `NodesController` — comportement cible :

| Check | Échec |
|-------|-------|
| Bearer absent / malformé | 401 |
| Secret inconnu (hash miss) | 401 |
| `credential.node_id` ≠ `node_id` demandé | 401 (pas 404 — anti-énumération) |
| Credential révoquée | 401 |
| Registre absent | 401/403 |
| `lifecycle_state` ∈ {`suspended`,`revoked`,`replaced`} | 403 |
| Token global legacy | Uniquement pendant **fenêtre migration** F2 (§11) |

### 7.3 Endpoints Agent concernés

Tous les endpoints aujourd’hui protégés par `authorizeNode` / `authorizeNodeForId` :

- `POST /api/v1/nodes/heartbeat`
- `GET /api/v1/nodes/{id}`
- `POST|GET …/commands*`
- `POST …/artifacts`
- `POST /api/v1/ingest/events`
- Validation tickets WS (chaîne API → relay) : le ticket reste émis par l’API **après** auth admin/Agent selon flux 002E ; le relay ne vérifie **pas** le Bearer nœud lui-même au-delà des tickets API.

### 7.4 Hostname dans le heartbeat

L’Agent **peut** (F2/F3) publier `hostname` dans le snapshot.  
Le serveur met à jour le miroir registre/snapshot.  
**Aucun** contrôle d’égalité hostname ↔ node_id.

---

## 8. Impacts API

### 8.1 Admin — endpoints cibles

| Méthode | Chemin | Opération | Réponse notable |
|---------|--------|-----------|-----------------|
| `POST` | `/api/v1/admin/nodes` | `create_node` | Body + **`token` clair one-shot** + `token_id` |
| `GET` | `/api/v1/admin/nodes` | liste | Pas de secrets ; `active_token_id`, `lifecycle_state`, `hostname?` |
| `GET` | `/api/v1/admin/nodes/{id}` | fiche | Idem |
| `PATCH` | `/api/v1/admin/nodes/{id}` | métadonnées | `display_name`, site, tags… ; **pas** `node_id` ; **pas** secret |
| `POST` | `/api/v1/admin/nodes/{id}/tokens` | `generate_token` ou `regenerate_token` (query/body `rotate=true`) | Clair one-shot |
| `DELETE` | `/api/v1/admin/nodes/{id}/tokens/current` | `revoke_token` | 204 |
| `POST` | `/api/v1/admin/nodes/{id}/disable` | `disable_node` | |
| `POST` | `/api/v1/admin/nodes/{id}/enable` | `enable_node` | |
| `POST` | `/api/v1/admin/nodes/{id}/revoke` | `revoke_node` | |
| `DELETE` | `/api/v1/admin/nodes/{id}` | `delete_node` | |
| `POST` | `/api/v1/admin/nodes/{id}/replace` | `replace_node` | Clair one-shot (= regenerate) |
| `POST` | `/api/v1/bootstrap/exchange` | F4 | Credential long terme |

Alignement avec le stub AUTO-002 : les chemins `tokens` / `revoke` / `replace` / `bootstrap` sont confirmés ; **disable / enable / DELETE** sont **ajoutés** explicitement par F1.

### 8.2 Changements de contrat

| Avant (002A) | Après (002F) |
|--------------|--------------|
| `POST /admin/nodes` crée fiche sans secret réel | Crée fiche + credential (ou defer) |
| `PATCH lifecycle_state` libre | Transitions contraintes (§5.3) ; préférer verbes dédiés |
| `token_id` éditable à la main | Lecture seule (`active_token_id`) |
| Heartbeat sans registre | Refusé hors migration |

### 8.3 Codes d’erreur (indicatif)

| Code | Cas |
|------|-----|
| 401 | Auth Agent échouée |
| 403 | Lifecycle refuse l’opération Agent |
| 409 | Token actif existe déjà (generate sans rotate) ; transition lifecycle invalide |
| 422 | `node_id` invalide ; payload |

### 8.4 Non-impact relay

**Aucun** nouvel endpoint métier sur `hestia-ws-relay`.  
`POST /api/v1/internal/ws/validate` continue de consulter l’API (session/ticket) ; l’API refuse les tickets pour nœuds `suspended`/`revoked`.

---

## 9. Impacts Agent

| Zone | Impact |
|------|--------|
| `agent.conf` | `NODE_ID` + `BACKEND_TOKEN` restent ; sémantique = token **par nœud** (plus le global) |
| Rotation | Pas d’auto-rotation ; ops / Installer réécrit `BACKEND_TOKEN` après regenerate/replace |
| Hostname | Lecture OS → champ snapshot heartbeat (si absent aujourd’hui : ajout F2/F3) |
| Erreurs 401/403 | Comportement reconnexion AUTO-001 inchangé (retry) ; pas de régénération ID |
| Multi-process | Un seul secret en conf ; pas de cache long d’un ancien Bearer après rotation manuelle (redémarrage Agent recommandé après écriture conf) |

**Hors Agent V1 F :** UI locale de rotation, TPM, enrollment QR natif.

---

## 10. Impacts Installer (F4)

| Étape | Rôle |
|-------|------|
| Pré-création admin | `node_id` + bootstrap ou token clair |
| Pose | Écrit `NODE_ID`, `BACKEND_URL`, `BACKEND_TOKEN` |
| Exchange | `POST /bootstrap/exchange` → credential ; invalide bootstrap |
| Replace | Même `NODE_ID`, nouveau token fourni par admin/wizard |

F1 ne spécifie pas l’UX Installer au-delà de ces contrats.

---

## 11. Impacts sécurité

### 11.1 Propriétés visées

| Propriété | Mécanisme |
|-----------|-----------|
| Confidentialité secret | Hash serveur ; clair one-shot ; jamais dans logs/events/UI liste |
| Binding identité | Bearer ⇒ un seul `node_id` |
| Révocation rapide | `revoked_at` consulté à chaque requête (cache ≤ 60 s max si introduit) |
| Anti-clonage | Rotation à replace ; ancien secret mort |
| Anti-usurpation parc | Fin du token global (après migration) |
| Moindre privilège relay | ADR-023 : pas de secrets nœud dans le relay |
| Audit | Qui a créé / rotaté / révoqué / désactivé / supprimé |

### 11.2 Menaces et mitigations

| Menace | Mitigation |
|--------|------------|
| Vol `agent.conf` | Rotate token ; disable nœud |
| Clone disque | Replace/regenerate avant rebranchement ancien |
| Token global résiduel | Migration F2 + feature flag ; puis suppression config `ingest.node_token` |
| Énumération `node_id` | 401 uniforme |
| Fuite secret dans artifact/diag | Allowlist + scrub (hors F1 détail) |
| Admin malveillant | Session admin déjà requise ; audit trail |

### 11.3 Rejets explicites (non-objectifs F)

- PKI / mTLS nœud
- Binding TPM / IDevID
- Détection active multi-agent temps réel au-delà de « un token actif »
- Fingerprint hardware comme identité

---

## 12. Invariants (checklist normative)

1. `node_id` unique et immuable après `create_node`.
2. `display_name` et `hostname` ne participent jamais à l’auth.
3. Au plus **un** `NodeCredential` non révoqué par `node_id`.
4. Un `secret` valide n’authentifie **que** son `node_id`.
5. `lifecycle_state ∈ {suspended, revoked, replaced}` ⇒ aucune API Agent acceptée.
6. Le serveur ne stocke jamais le secret en clair.
7. Le relay ne stocke / n’émet / ne révoque aucun token nœud.
8. L’Agent ne modifie jamais `node_id` ni ne s’auto-émet de credential.
9. Remplacement continuité ⇒ même `node_id`, nouveau secret.
10. Présence ONLINE/OFFLINE ne peut pas « ressusciter » un nœud révoqué.

---

## 13. Scénarios fonctionnels

### 13.1 Mise en service

1. Admin `create_node(hestia-salon)`.
2. Reçoit secret one-shot.
3. Installer écrit conf.
4. Agent heartbeat → `provisioned`→`active`, `hostname` observé.
5. UI : ONLINE + display_name.

### 13.2 Rotation de sécurité

1. Admin `regenerate_token`.
2. Ancien Agent → 401.
3. Ops met à jour `BACKEND_TOKEN` (SSH exceptionnel ou future commande distante dédiée — hors F1).
4. Retour ONLINE.

### 13.3 Remplacement matériel

1. Admin `replace` / `regenerate_token` (raison replace).
2. Ancien mini-PC mort pour l’API.
3. Nouveau matériel + même `NODE_ID` + nouveau secret.
4. Continuité fiche / historique / rattachements logiques.

### 13.4 Suspension temporaire

1. `disable_node` → `suspended` + token révoqué.
2. Réactivation : `enable_node` + `generate_token` + mise à jour conf.

### 13.5 Fin de vie

1. `revoke_node` → `revoked`.
2. Puis `delete_node` si purge souhaitée.

---

## 14. Structure documentaire proposée

| Document | Rôle | Statut F1 |
|----------|------|-----------|
| **Ce fichier** `docs/architecture/AUTO-002F-identite-cycle-vie-noeud.md` | Spec normative identité / lifecycle | **Créé (F1)** |
| `docs/architecture/AUTO-002-supervision-administration-noeuds.md` §002F | Stub → pointeur vers ce doc | À mettre à jour |
| `docs/backlog/execution/AUTO-002.md` | Suivi lots F1/F2/… | À mettre à jour |
| **`docs/adr/ADR-021-identite-credentials-noeud.md`** | Décision formelle identité + token par nœud | **Accepté** |
| `docs/gouvernance/GLOSSAIRE.md` | Entrées `hostname`, `display_name`, token nœud, présence ≠ lifecycle | **Enrichi** (avec ADR-021) |
| `docs/gouvernance/PROJECT-STATE.md` | Chantier actif 002F | Mise à jour session |
| Stubs `hestia/docs/*` | Renvoient vers hestia-docs | Inchangés sauf besoin |

**Pas de** nouveau chantier documentaire hors AUTO-002F / ADR-021.

---

## 15. ADR éventuellement nécessaires

| ADR | Besoin | Priorité |
|-----|--------|----------|
| **ADR-021 — Identité et credentials nœud** | Fige : `node_id` permanent ; token par nœud ; hash ; binding Bearer↔id ; sémantique `replaced` vs regenerate ; fin du token global | **Accepté** — prérequis F2 satisfait |
| ADR-023 | **Inchangé** — rappel explicite « no business logic » dans ADR-021 | Référence |
| ADR-022 / 024 / 025 | Commandes / artifacts / frontière Agent — **hors F1** ; pas bloquants identité | Ne pas ouvrir |
| ADR éventuel « bootstrap enrollment » | Seulement si le détail Installer/QR dépasse ADR-021 | **F4** si besoin (sinon section ADR-021 suffit) |

Contenu minimal attendu d’**ADR-021** : **couvert** par [ADR-021](../adr/ADR-021-identite-credentials-noeud.md) (Accepté).

---

## 15bis. Préparation F2 (dépôt `hestia`)

Prérequis documentaires **satisfaits** : F1 + ADR-021.

Périmètre F2 (rappel) — **implémentation serveur uniquement**, pas de logique métier relay :

1. Store `NodeCredential` (hash-only).
2. `authorizeNodeForId` réel + binding Bearer ↔ `node_id`.
3. Enforcement `lifecycle_state` sur routes Agent.
4. Dual-mode migration token global → par nœud.
5. Refus upsert libre hors migration.
6. Tests anti-usurpation / révocation.

Hors F2 : UI admin complète (F3), bootstrap Installer (F4), hostname Agent (F5), retrait définitif du token global (F6).

Détail plan : ADR-021 § « Plan d’implémentation F2 ».

## 16. Découpage d’implémentation — F2, F3, F4…

| Phase | Intitulé | Livrable | Dépôts | Dépendance | Statut |
|-------|----------|----------|--------|------------|--------|
| **F1** | Conception identité | Ce document (+ maj pointeurs) | `hestia-docs` | — | **Fait** |
| **F2** | Auth par nœud (cœur serveur) | `NodeCredential` store ; auth Binding Bearer↔`node_id` ; enforcement lifecycle ; (dual-mode retiré en F6) | `hestia`, `hestia-docs` | F1 + **ADR-021 Accepté** | **Fait** |
| **F3** | API & UI administration lifecycle | Endpoints §8.1 ; transitions ; one-shot UI ; disable/enable/revoke/delete | `hestia`, `hestia-docs` | F2 | **Fait** |
| **F4** | Bootstrap & Installer | `BootstrapToken` + `/bootstrap/exchange` ; conf Installer | `hestia`, `hestia-installer`, `hestia-docs` | F3 | **Fait** (F4A+F4B) |
| **F5** | Agent hostname + DX rotation | Champ `hostname` heartbeat ; contrat credential | `hestia-agent`, `hestia`, docs | F2+ | **Fait** |
| **F6** | Durcissement & clôture 002F | **Suppression token global** ; tests identité ; audit credentials | tous | F3–F5 | **Fait** |

### 16.1 Ordre recommandé

```text
F1 (doc) → ADR-021 → F2 (auth binding) → F3 (admin API/UI)
         → F4 (bootstrap/Installer) → F5 (Agent polish) → F6 (retrait global + clôture)
```

### 16.2 Critères de ready par phase (résumé)

| Phase | Ready quand |
|-------|-------------|
| F2 | Un Agent avec token nœud A ne peut pas heartbeat sous `node_id` B ; nœud `revoked` → 401/403 |
| F3 | Admin crée / rotate / disable / delete sans SSH serveur ; secret jamais re-affiché |
| F4 | Pose Installer sans coller manuellement un token global |
| F5 | Hostname visible fiche ; procédure rotation documentée |
| F6 | `ingest.node_token` retiré ; critère clôture AUTO-002 « token par nœud » vert |

### 16.3 Hors découpage F (ne pas mélanger)

- UI-001 refonte ergonomique parc
- EPIC-002 équipements / `hestia_device_id`
- PKI, backup nœud, sync conf maison (F-038)

---

## 17. Mapping avec l’existant code (informative, non implémentation)

| Existant | Devenir |
|----------|---------|
| `authorizeNode()` token global | Migration puis retrait (F2/F6) |
| `authorizeNodeForId($nodeId)` | Implémentation réelle (F2) |
| `NodeRegistryStore.token_id` | `active_token_id` + store credentials |
| `lifecycle_state` déclaratif | Enforcement auth (F2) + verbes API (F3) |
| UI select lifecycle | Remplacé progressivement par actions disable/revoke/replace |
| `BACKEND_TOKEN` Agent | Reste le nom de conf ; sémantique par nœud |
| ws-relay | **Aucun changement métier** |

---

## 18. Décisions ouvertes (à trancher dans ADR-021 ou F2)

| # | Question | Proposition F1 (défaut) |
|---|----------|-------------------------|
| O1 | Soft-delete vs hard-delete | Soft-delete court puis purge ; `node_id` non réutilisable immédiatement |
| O2 | Disable révoque-t-il le token ? | **Oui** (Option A) |
| O3 | Algo de hash | argon2id si dispo PHP ; sinon password_hash bcrypt |
| O4 | Cache auth TTL | ≤ 60 s ou aucun cache V1 |
| O5 | Qui passe `provisioned`→`active` ? | Automatique au premier auth OK |
| O6 | État `replaced` | Archivage rare ; replace nominal = regenerate |
| O7 | Purge CommandStore à delete | Oui fichiers `node-commands/{id}` |

---

## 19. Clôture phase F1

**F1 est une phase documentaire.** Livrables :

1. Le présent document.
2. Pointeur depuis AUTO-002 §002F.
3. Entrée d’exécution F1 dans `execution/AUTO-002.md`.
4. Liste ADR-021 + découpage F2+ (ci-dessus).

**Explicitement non livré en F1 :** code PHP/JS/Agent/Installer, ADR-021 texte final accepté (rédaction ADR = prérequis F2, peut être enchaînée juste après F1).

---

*Fin AUTO-002F Phase F1 — Conception.*
