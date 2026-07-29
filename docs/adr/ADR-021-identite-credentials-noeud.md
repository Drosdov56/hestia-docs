# ADR-021 — Identité et credentials des nœuds Hestia

**Statut :** Accepté  
**Date :** 2026-07-29  
**Emplacement :** `hestia-docs/docs/adr/`  
**Implémentation :** AUTO-002F (phases F2→F6) — **livrée** ; token global retiré (F6)  

> Cadre : [Constitution](../constitution/HESTIA%20-%20DOCUMENT%20DE%20RÉFLEXION%20ARCHITECTURALE.md) · [AUTO-002](../architecture/AUTO-002-supervision-administration-noeuds.md) · [AUTO-002F](../architecture/AUTO-002F-identite-cycle-vie-noeud.md) · [AUTO-001 revue](../architecture/AUTO-001-revue-architecture.md) Sujets 2–3 · [ADR-018](ADR-018-architecture-domotique-agent-passerelle.md) · [ADR-023](ADR-023-terminal-distant-websocket-sortant.md).

---

## Contexte

### Problème

Après AUTO-002A→E, le parc nœuds est administrable (registre, commandes, terminal WS). L’authentification Agent reste celle de la Phase 1 :

- un **token ingest global** (`ingest.node_token` / `BACKEND_TOKEN`) partagé par tous les nœuds ;
- le Bearer **n’est pas lié** au `node_id` de la requête ;
- le champ `lifecycle_state` du registre est **déclaratif** : `revoked` / `suspended` n’empêchent pas heartbeat ni commandes ;
- le hostname OS n’est pas modélisé, ce qui favorise la confusion avec `node_id` / `display_name`.

Conséquences :

1. Compromission ou rotation du secret = impact **tout le parc**.
2. Un Agent légitime (ou un clone) peut usurper n’importe quel `node_id`.
3. Impossible de révoquer / remplacer un mini-PC sans casser les autres.
4. La présence ONLINE/OFFLINE et le cycle de vie administratif se mélangent dans les décisions ops.

La spécification normative [AUTO-002F](../architecture/AUTO-002F-identite-cycle-vie-noeud.md) (Phase F1) décrit le modèle cible. **Cet ADR formalise la décision d’architecture** avant toute implémentation (F2+).

### Contraintes structurantes

- Serveur Hestia = **seule autorité** de confiance (création, révocation, états).
- Agent = initiateur HTTPS sortant uniquement (ADR-018 / AUTO-001).
- Relay WebSocket = **transport** (ADR-023) : aucune logique d’identité, de token ou de lifecycle.
- Simplicité > PKI / TPM / fingerprint hardware (revue AUTO-001).
- `node_id` ≠ `hestia_device_id` (équipement métier).

---

## Options étudiées

### Option A — Conserver le token global (statu quo)

**Pour :** zéro changement Agent/Installer ; simple.  
**Contre :** incompatible parc multi-nœuds sûr ; révocation impossible par nœud ; usurpation d’identité triviale.  
**Verdict :** rejeté pour la cible ; acceptable uniquement en **fenêtre de migration** F2.

### Option B — Identité dérivée du matériel (MAC / série / TPM)

**Pour :** anti-clonage fort.  
**Contre :** fragile (changement NIC), opaque ops, incompatible « même identité logique sur nouveau matériel », sophistication sans valeur utilisateur immédiate (revue AUTO-001).  
**Verdict :** rejeté.

### Option C — Certificats mTLS / PKI nœud

**Pour :** standard industrie.  
**Contre :** charge ops (émission, renouvellement, horloge), hors maturité V1, ADR-0005 (complexité).  
**Verdict :** rejeté pour AUTO-002F ; réévaluable plus tard.

### Option D — Token par nœud + `node_id` permanent + hash serveur (retenue)

**Pour :**

- révocation / rotation ciblée ;
- binding Bearer ↔ `node_id` ;
- continuité de remplacement (même ID, nouveau secret) ;
- aligné registre 002A et hook `authorizeNodeForId` déjà prévu ;
- compatible ADR-023 (décisions restent dans l’API).

**Contre :** migration depuis le token global ; procédure ops pour écrire le secret sur le nœud.  
**Verdict :** **retenu**.

### Option E — Changer le `node_id` à chaque remplacement matériel

**Pour :** rupture nette ancien / nouveau.  
**Contre :** casse la continuité admin (historique, rattachements logiques, UX parc) ; contredit la revue AUTO-001 Sujet 3.  
**Verdict :** rejeté comme nominal ; un **nouveau** `node_id` reste possible si l’admin veut volontairement une nouvelle identité logique.

---

## Décision

1. **`node_id` est l’identité logique permanente** d’un nœud (instance Agent / mini-PC). Il est choisi à la création (admin / Installer), immuable ensuite, et sert de clé primaire pour registre, auth, commandes, sessions WS et artefacts.

2. **`display_name` est un libellé admin mutable.** Il n’entre jamais dans l’authentification.

3. **`hostname` est une observation runtime mutable** (OS / Agent). Il n’entre jamais dans l’authentification ni ne remplace `node_id`.

4. **Authentification Agent = Bearer token par nœud.** Un nœud a **au plus un token actif**. Le secret appartient exclusivement à ce `node_id`.

5. **Le serveur stocke uniquement un hash** du secret (`NodeCredential.secret_hash`). Le clair est affiché **une seule fois** à l’émission (create / generate / regenerate / replace).

6. **Le serveur valide `Bearer ↔ node_id`** sur chaque requête Agent concernée (`authorizeNodeForId`). Mismatch, secret inconnu, credential révoquée, registre absent ou lifecycle non authentifiable → refus (401/403).

7. **Le cycle de vie (`lifecycle_state`) est autoritaire** et orthogonal à la **présence** (`ONLINE` / `OFFLINE` dérivés du heartbeat). Les états authentifiables sont `provisioned` et `active`. `suspended`, `revoked`, `replaced` refusent l’API Agent.

8. **Remplacement matériel (continuité) = même `node_id` + rotation du token** (pas de nouvel ID). L’état `replaced` est réservé à l’archivage rare, pas au flux nominal de replace.

9. **Le token ingest global est une dette de migration.** F2 introduit un dual-mode ; F6 le retire. Après clôture 002F, aucun Agent de production ne s’authentifie via le secret parc.

10. **`hestia-ws-relay` (ADR-023) ne gère ni identité ni credentials.** Toute décision (émission, révocation, validation métier des tickets liés au nœud) reste dans l’API REST.

11. **L’Agent ne régénère jamais** `node_id` ni token de sa propre initiative. L’Installer / l’admin écrivent la conf.

---

## Motivations détaillées (pourquoi)

### Pourquoi `node_id` est permanent

L’identité logique représente « la boîte Hestia du salon » dans le foyer, pas un disque ou une carte réseau. La permanence permet :

- continuité d’historique et de fiche admin ;
- rattachement stable des opérations (commandes, sessions, artefacts) ;
- remplacement matériel sans recréer toute la fiche.

Changer de `node_id` = **nouvelle** identité logique (intention admin explicite), pas une opération de maintenance courante.

### Pourquoi `hostname` ne participe jamais à l’authentification

Le hostname OS change (réinstall, DHCP, typo, clone). En faire une clé d’auth rendrait l’accès fragile et confondrait observation et identité. Le hostname reste de la **télémétrie** utile à l’opérateur.

### Pourquoi le token est unique par nœud

Un secret partagé (Option A) empêche la révocation ciblée et permet l’usurpation d’ID. Un token actif unique par `node_id` garantit : un secret compromis = un nœud à rotator ; deux machines avec le même ID ne peuvent pas être toutes deux légitimes après rotation.

### Pourquoi seul le hash est conservé côté serveur

Même principe que les mots de passe : une fuite du store credentials ne doit pas révéler des Bearer réutilisables. L’admin qui perd le clair doit **régénérer**, pas « relire » le secret.

### Pourquoi la rotation est privilégiée à la modification du `node_id`

Rotation (regenerate / replace) invalide l’ancien matériel tout en conservant l’identité logique. Modifier le `node_id` casse la continuité et crée un second enregistrement. Le chemin nominal de compromission, remplacement ou doute = **nouveau secret, même ID**.

### Pourquoi présence et lifecycle sont indépendants

| Plan | Question répondue | Source |
|------|-------------------|--------|
| Présence | Le nœud a-t-il heartbeate récemment ? | TTL / `last_seen` |
| Lifecycle | L’admin autorise-t-il encore ce nœud ? | Registre serveur |

Un nœud `suspended` est OFFLINE (ou « désactivé ») **parce que** l’auth est refusée, non parce que le TTL a expiré. Un nœud `active` OFFLINE est simplement injoignable — l’identité reste valide. Fusionner les deux plans produit des erreurs ops (réactiver en « forçant ONLINE », ou croire qu’un nœud révoqué « reviendra » au prochain heartbeat).

---

## Conséquences techniques

### Serveur (`hestia`) — préparation F2

| Élément | Conséquence |
|---------|-------------|
| Store | Nouveau `NodeCredential` (hash, `token_id`, `node_id`, `revoked_at`, …) |
| Registre | `active_token_id` remplace sémantiquement le `token_id` métadonnée libre |
| Auth | Implémenter réellement `authorizeNodeForId($nodeId)` |
| Lifecycle | Enforcement sur toutes les routes Agent concernées |
| Upsert | Refuser heartbeat créant un nœud sans registre valide (hors dual-mode migration) |
| Migration | Fenêtre dual-mode : accepter temporairement le token global **ou** le token par nœud |
| Relay | **Aucun** changement métier ADR-023 |

Détail des entités, transitions et endpoints : [AUTO-002F](../architecture/AUTO-002F-identite-cycle-vie-noeud.md) §§4–8.

### API

- Endpoints admin de credentials / lifecycle (create avec one-shot, tokens, disable/enable, revoke, delete, replace) — livrés surtout en **F3** ; le cœur auth en **F2**.
- Réponses Agent : 401 (auth) / 403 (lifecycle) ; pas d’énumération utile via 404 différencié.
- Secret jamais dans GET liste / fiche après émission.
- Validation interne WS (`/internal/ws/validate`) : l’API refuse les tickets pour nœuds non authentifiables ; le relay ne réinterprète pas.

### Agent (`hestia-agent`)

- Conf inchangée en forme : `NODE_ID` + `BACKEND_TOKEN` + `BACKEND_URL`.
- Sémantique : `BACKEND_TOKEN` = secret **du** nœud, plus le secret parc.
- Pas d’auto-rotation ; après regenerate, réécriture conf (ops / Installer / future commande dédiée F5).
- Publication éventuelle de `hostname` dans le snapshot (F5) — purement informatif.
- Sur 401/403 : comportement de reconnexion AUTO-001 (retry) ; **jamais** régénérer l’identité localement.

### Installer (`hestia-installer`)

- Pose / replace : écrire `NODE_ID` stable + secret fourni par le serveur (clair one-shot ou bootstrap F4).
- Ne pas inventer de `node_id` côté Agent au runtime.
- Bootstrap `/api/v1/bootstrap/exchange` : phase **F4** (contrat déjà dans AUTO-002F) ; pas bloquant pour F2 cœur auth.

### Administration (Web)

- Wizard / actions : créer, afficher secret one-shot, rotator, désactiver, révoquer, supprimer, remplacer.
- `display_name` éditable ; `node_id` non éditable après création.
- Lifecycle via **verbes métier** plutôt que PATCH libre d’état (F3).
- Présence et badges lifecycle affichés comme **deux dimensions** distinctes.

### Sécurité

| Propriété | Mécanisme |
|----------|-----------|
| Binding | Bearer ⇒ un seul `node_id` |
| Révocation ciblée | `revoked_at` + enforcement immédiat (cache ≤ 60 s si introduit) |
| Anti-clonage ops | Rotation à replace ; ancien secret mort |
| Fuite store | Hash only |
| Surface relay | Pas de secrets nœud dans `hestia-ws-relay` |
| Audit | Qui a émis / rotaté / révoqué / désactivé / supprimé |

**Rejets explicites (hors ADR) :** mTLS, TPM, fingerprint comme identité, détection multi-agent temps réel au-delà de « un token actif ».

---

## Lien avec ADR-023

ADR-023 reste **inchangé** et **primant** pour le relay :

- le relay n’émet, ne stocke, ne révoque aucun token nœud ;
- le relay ne connaît pas `lifecycle_state` ;
- l’API décide ; le relay transporte et valide les tickets selon l’API.

ADR-021 **complète** ADR-023 : le durcissement auth nœud se fait dans `core/` (API), pas dans `services/ws-relay/`. Toute PR qui déplacerait la logique d’identité vers le relay est refusée.

---

## Lien avec AUTO-002F

| Document | Rôle |
|----------|------|
| [AUTO-002F](../architecture/AUTO-002F-identite-cycle-vie-noeud.md) | Spec normative détaillée (états, ops, API, découpage F2–F6) |
| **ADR-021** (ce document) | Décision d’architecture et motivations ; contrat stable pour l’implémentation |

En cas de détail opérationnel (schémas de champs, chemins exacts), **AUTO-002F prime** tant qu’il ne contredit pas les décisions numérotées de cet ADR. Toute contradiction future exige une **amendement d’ADR** ou une nouvelle ADR.

---

## Hors périmètre

- Implémentation code (F2+).
- Bootstrap Installer complet (F4).
- UI-001 (ergonomie).
- Modèle `Equipment` / `hestia_device_id` (EPIC-002).
- ADR-022 / 024 / 025 (autres sujets AUTO-002).

---

## Critères d’acceptation (architecture → F2 ready)

F2 pourra démarrer dans le dépôt `hestia` lorsque :

1. Cet ADR est **Accepté** (état actuel).
2. AUTO-002F F1 reste la spec d’implémentation.
3. Le plan F2 ci-dessous est suivi **sans** toucher au relay métier.

### Plan d’implémentation F2 (dépôt `hestia` uniquement pour le cœur)

Ordre indicatif — détail d’exécution hors ADR :

1. Introduire le store `NodeCredential` (hash versionné).
2. Étendre le registre (`active_token_id`) + migration douce du champ legacy `token_id`.
3. Implémenter `authorizeNodeForId` : resolve secret → vérifie `node_id` + lifecycle.
4. Brancher toutes les routes Agent déjà protégées par le hook.
5. Dual-mode migration : token par nœud **ou** token global (feature flag / conf).
6. Refuser l’upsert libre d’un `node_id` inconnu hors mode migration documenté.
7. Tests : token A ne heartbeat pas sous id B ; nœud `revoked`/`suspended` → 401/403.
8. **Ne pas** modifier la logique métier de `hestia-ws-relay` ; seulement s’assurer que l’API de validation de tickets respecte le lifecycle.

Les endpoints admin riches (UI one-shot, disable/delete/replace) relèvent de **F3** ; F2 peut exposer un minimum serveur nécessaire aux tests (création credential / seed) sans livrer l’UI complète.

---

## Références

- [AUTO-002F — Identité et cycle de vie des nœuds](../architecture/AUTO-002F-identite-cycle-vie-noeud.md)
- [AUTO-002 — Supervision et administration des nœuds](../architecture/AUTO-002-supervision-administration-noeuds.md) §002F
- [AUTO-001 — Revue d’architecture](../architecture/AUTO-001-revue-architecture.md) Sujets 2–3
- [ADR-023 — Terminal distant WS sortant](ADR-023-terminal-distant-websocket-sortant.md)
- [ADR-018 — Architecture agent / passerelle](ADR-018-architecture-domotique-agent-passerelle.md)
- [GLOSSAIRE](../gouvernance/GLOSSAIRE.md) — `node_id`
