# EXEC-EPIC-001 — Plan d’exécution Agent métier

**Epic :** EPIC-001  
**Statut :** Clôturée (2026-07-25) — [REPORT-CLOTURE-EPIC-001.md](REPORT-CLOTURE-EPIC-001.md)  
**Phase backlog :** P0  
**Bloque :** EPIC-002, EPIC-004, EPIC-011  

---

## 1. Objectif

Faire évoluer `hestia-agent` **au-delà de la V1 infrastructure** pour :

1. **Collecter** des événements depuis le backend domotique local (HA / MQTT).  
2. **Normaliser** ces événements dans un format Hestia stable.  
3. **Filtrer** (sélection technique) ce qui est remonté.  
4. **Synchroniser** vers le Serveur Hestia de façon sécurisée, avec file locale si hors-ligne.  
5. **Détecter** le silence / reprise de communication d’une ancre (préparation transitions `active`/`synced` ↔ `offline`).

**Sans** aucune logique d’accompagnement familial, d’interprétation foyer, ni d’UI Admin.

**Critère de done Epic (rappel) :** un événement SNZB (ou équivalent) traverse `HA → Agent → API Serveur` ; le contrat `--health` V1 reste respecté ; aucune règle « signification familiale » dans l’Agent.

---

## 2. Périmètre

### 2.1 Responsabilités

| Acteur | Dans EPIC-001 | Hors EPIC-001 |
|--------|---------------|---------------|
| **hestia-agent** | Collecte, normalisation, sélection technique, queue locale, sync montante, détection silence/heartbeat, logs/health étendus | UI ; SoT métier ; décisions familiales ; MS équipements |
| **hestia (API)** | Endpoint(s) d’**ingest** télémétrie / événements normalisés + ack / dédup | Modèle `Equipment` complet (EPIC-002) ; Hub ; Admin ; information utile |
| **Home Assistant** | Source technique d’états / événements (déjà déployé) | Interface utilisateur produit |
| **Mosquitto** | Transport MQTT local (déjà déployé, INT-001) | — |
| **hestia-installer** | Inchangé pour cette Epic (déploie toujours l’Agent) | Nouveaux modules install non requis pour démarrer le dev |

### 2.2 Entrées

| Entrée | Description | Origine |
|--------|-------------|---------|
| Événements / états HA | Entités exposées par Home Assistant | Nœud (HA) |
| Messages MQTT locaux | Topics pertinents (dont pont HA/Z2M selon config nœud) | Mosquitto localhost |
| Configuration Agent | Extension de `agent.conf` (URLs, credentials, topics, filtres, `OFFLINE_GRACE`) | Fichier conf + reload SIGHUP |
| (Optionnel) Config descendante minimale | Liste d’ancres / filtres connus — **lecture seule** ; SoT reste le VPS | Stub acceptable si absente en début d’Epic |

### 2.3 Sorties

| Sortie | Description | Destination |
|--------|-------------|-------------|
| Événement normalisé Hestia | Payload stable, indépendant de HA | API ingest `hestia` |
| Identifiant d’événement | UUID d’événement + horodatage (dédup) — **≠** `hestia_device_id` | Dans le payload |
| File locale | Événements non encore ack | `${RUNTIME_DIR}` (ou sous-répertoire dédié) |
| Signaux reachability | Silence / reprise pour une ancre physique | Même canal ingest (type dédié) |
| Logs / health | État des flux (MQTT, HA, sync) | Contrats V1 étendus sans rupture |

### 2.4 Limites (non négociables)

1. **Pas de logique métier familiale** (ADR-020, Glossaire).  
2. **Pas de dépendance directe** du code `hestia` (`client/`, `core/` métier) à l’API Home Assistant — uniquement via Agent (ADR-020).  
3. **Backend = SoT** des écritures métier équipements ; l’Agent **propose / signale**, il ne devient pas SoT (Module 70 §1.1).  
4. **V1 infra préservée** : CLI, lock, state, `--health` codes 0/6/7, format logs, mono-instance.  
5. **Sélection = technique** (MODELE-INFORMATION §4) : retenir / ignorer des signaux ; **pas** produire d’« information utile » (EPIC-004).  
6. **Transitions d’état Module 70** : l’Agent émet les **faits techniques** (silence, heartbeat) ; la **persistance d’état** `Equipment` est finalisée dans EPIC-002. EPIC-001 doit toutefois rendre ces faits visibles côté Serveur via l’ingest.

### 2.5 Hors périmètre

| Exclu | Reporté à |
|-------|-----------|
| Modèle `Equipment` + `hestia_device_id` + machine d’états complète | EPIC-002 |
| Hestia Admin / UX-003 | EPIC-003 |
| Information utile / Hub / notifications | EPIC-004, EPIC-005 |
| Corrélation multi-signaux, caméras, décision métier | EPIC-007, EPIC-010 |
| Config maison descendante complète, réinstall nœud | EPIC-011 |
| Permit-join / appairage déclenché Admin | EPIC-003 |
| IA | EPIC-013 |
| Remplacement HA par un autre backend | Hors scope (architecture le permet, non implémenté) |

---

## 3. Découpage en lots techniques

Chaque lot est **indépendamment testable** et **intégrable** (merge possible sans attendre la fin de l’Epic).  
Ordre recommandé : L0 → L1 → L2 → L3 → L4 → L5 → L6 → L7.

### L0 — Socle configuration & non-régression V1

| | |
|--|--|
| **US** | (fondation — toutes) |
| **Contenu** | Étendre `agent.conf` avec clés **optionnelles** (MQTT, HA, Backend URL, secrets, filtres, `OFFLINE_GRACE`). Si absentes → comportement V1 inchangé (idle infra). Reload SIGHUP. |
| **Dépôt** | `hestia-agent` |
| **Testable** | Conf invalide → codes existants ; conf V1 seule → `--health` OK/WARNING comme aujourd’hui |
| **Done lot** | Aucune régression suite tests V1 ; nouvelles clés documentées dans `agent.conf.example` |

### L1 — Collecte MQTT locale (US-001.2)

| | |
|--|--|
| **US** | US-001.2 |
| **Contenu** | Client MQTT localhost ; abonnements configurables ; journalisation connecté/déconnecté ; messages bruts en mémoire ou buffer temporaire. |
| **Dépôt** | `hestia-agent` |
| **Dépend** | L0 ; Mosquitto nœud (prérequis plateforme) |
| **Done lot** | Message publié sur un topic de test → log Agent + compteur / trace interne |

### L2 — Collecte événements HA (US-001.1)

| | |
|--|--|
| **US** | US-001.1 |
| **Contenu** | Brancher la source HA retenue (MQTT discovery/state HA **ou** API/WS HA — **un** mécanisme choisi et documenté dans le lot, aligné pile INT-001). Produire un flux interne « événement technique brut ». |
| **Dépôt** | `hestia-agent` |
| **Dépend** | L0 ; HA opérationnel ; idéalement L1 si MQTT |
| **Done lot** | Changement d’état d’une entité HA de test visible dans le flux Agent |

### L3 — Schéma normalisé Hestia (US-001.3)

| | |
|--|--|
| **US** | US-001.3 |
| **Contenu** | Définir et implémenter le **contrat d’événement normalisé** (champs minimaux ci-dessous). Mapping ancre physique / entity HA → champs stables. **Pas** d’interprétation foyer. |
| **Dépôt** | `hestia-agent` (+ doc contrat partagée ou miroir côté `hestia`) |
| **Dépend** | L2 (ou L1) ; **Python 3** (`json`, `uuid`) garanti par Installer module 20 |
| **Done lot** | Pour un message d’entrée, sortie JSON (ou équivalent) conforme au schéma, déterministe |

#### Schéma minimal d’événement normalisé (contrat EPIC-001)

| Champ | Obligatoire | Rôle |
|-------|-------------|------|
| `event_id` | oui | UUID d’événement (dédup) |
| `occurred_at` | oui | Horodatage UTC ISO8601 de l’observation |
| `received_at` | oui | Horodatage UTC réception Agent |
| `node_id` | oui | Identifiant du nœud (config) |
| `source` | oui | `ha` \| `mqtt` \| `agent` |
| `physical_anchor` | oui si connu | Ancre technique (ex. IEEE Zigbee) — **pas** `hestia_device_id` |
| `ha_entity_id` | si source HA | Entity id technique |
| `signal_type` | oui | Type technique stable (ex. `presence`, `state_change`, `reachability`) |
| `payload` | oui | Données techniques retenues (clé/valeur) |
| `schema_version` | oui | Version du contrat |

Interdit dans ce payload : nom logique métier, pièce, identité personne, « information utile », décision familiale.

### L4 — Sélection technique (US-001.4)

| | |
|--|--|
| **US** | US-001.4 |
| **Contenu** | Appliquer filtres configurables (allowlist entity/topic/signal_type ; denylist bruit). Journaliser les écarts (retenu / ignoré) en DEBUG ou compteur. Aligné MODELE-INFORMATION §4 (choix explicite). |
| **Dépôt** | `hestia-agent` |
| **Dépend** | L3 |
| **Done lot** | Message hors allowlist **non** envoyé ; message dans allowlist **envoyé** (vers queue ou sync) |

### L5 — API ingest Serveur + transport sécurisé (US-001.5)

| | |
|--|--|
| **US** | US-001.5 |
| **Contenu** | Côté `hestia` : endpoint d’ingest authentifié acceptant le schéma L3, ack + dédup par `event_id`. Côté Agent : client HTTPS (ou canal sécurisé retenu) vers le VPS. |
| **Dépôts** | `hestia` + `hestia-agent` |
| **Dépend** | L3 (schéma) ; L4 recommandé |
| **Done lot** | POST d’un événement → stocké ou journalisé côté Serveur ; doublon `event_id` → ack sans double traitement |

### L6 — Retry / backoff + file locale (US-001.6, US-001.7)

| | |
|--|--|
| **US** | US-001.6, US-001.7 |
| **Contenu** | Queue durable locale ; enqueue si VPS down ; retry avec backoff ; reprise auto ; pas de perte ; dédup à l’envoi. |
| **Dépôt** | `hestia-agent` (principal) |
| **Dépend** | L5 |
| **Done lot** | Coupure VPS simulée → événements conservés → reprise → tous ack sans perte ni doublon côté Serveur |

### L7 — Reachability / OFFLINE_GRACE (US-001.8)

| | |
|--|--|
| **US** | US-001.8 |
| **Contenu** | Surveiller le silence par ancre / entité suivie ; après `OFFLINE_GRACE` (défaut **15 min**, configurable — Module 70) émettre événement `signal_type=reachability` (`offline` / `online`). Heartbeat / dernier vu. **Ne pas** implémenter toute la machine Module 70 (EPIC-002). |
| **Dépôt** | `hestia-agent` (+ ingest L5) |
| **Dépend** | L5 ; idéalement L6 |
| **Done lot** | Silence prolongé → événement offline ; reprise signal → événement online ; valeur grace configurable |

---

## 4. Dépendances

### 4.1 Prérequis (déjà livrés)

| Prérequis | Preuve doc |
|-----------|------------|
| Nœud Ubuntu + Docker + HA + Mosquitto + Z2M | FUNCTIONAL-VISION §9 |
| **Python 3** (`json`, `uuid`) — paquet `python3` | Installer `packages.conf` module 20 ; precheck + final-check |
| **OpenSSL** — paquet `openssl` | Installer `packages.conf` module 20 ; precheck + final-check (secrets MQTT mod. 50) |
| INT-001 HA ↔ MQTT | FUNCTIONAL-VISION §3 |
| Agent infra V1 (daemon, health, conf, systemd) | `hestia-agent` ARCHITECTURE.md |
| Installer déploie l’Agent | Module 70 / ecosysteme |

### 4.2 Interfaces à créer / figer

| Interface | Producteur | Consommateur | Lot |
|-----------|------------|--------------|-----|
| Schéma événement normalisé | Agent | API `hestia` | L3 / L5 |
| API ingest + ack/dédup | `hestia` | Agent | L5 |
| Config MQTT/HA/Backend Agent | conf fichier | Agent | L0 |
| Événements reachability | Agent | Serveur (puis EPIC-002) | L7 |

### 4.3 Modules impactés

| Dépôt | Zones |
|-------|-------|
| `hestia-agent` | `bin/`, `lib/` (nouveaux modules), `config/agent.conf.example`, doc ARCHITECTURE (extension post-impl — hors cette mission) |
| `hestia` | `core/` API ingest minimale (nouveau endpoint), auth nœud |

### 4.4 Dépendances externes

| Externe | Usage EPIC-001 |
|---------|----------------|
| Home Assistant | Source d’événements / états |
| Mosquitto | MQTT local |
| **Python 3** | Normalisation L3 Agent (+ INT-001 JSON) — via Installer |
| Réseau vers VPS | Sync montante HTTPS |
| Capteur SNZB-06P24 (ou mock) | Validation terrain / PoC chaîne |

### 4.5 Risques / points d’attention

| Risque | Mitigation |
|--------|------------|
| Choisir trop tôt WS HA vs MQTT | Trancher dans L2 ; un seul chemin documenté ; MQTT privilégié si déjà INT-001 |
| Fuite de logique métier dans filtres | Revue : filtres = allow/deny techniques uniquement |
| Casser `--health` | L0 + tests non-régression obligatoires |
| File locale corrompue | Format simple, fsync, tests crash |
| Confusion UUID événement / `hestia_device_id` | Schéma L3 ; glossaire |

---

## 5. Critères d’acceptation par User Story

### US-001.1 — Réception événements HA

**Accepté si et seulement si :**

1. Un changement d’état d’une entité HA de test apparaît dans le flux interne Agent en ≤ 5 s (hors charge anormale).  
2. L’Agent fonctionne sans ouvrir l’UI HA.  
3. Si HA est stoppé, l’Agent reste up, loggue l’erreur de source, et `--health` ne passe pas à FAILED **uniquement** pour cause HA (WARNING acceptable si défini ; conf/runtime restent valides).  
4. Aucun appel depuis `hestia` PWA/API métier directement vers HA.

### US-001.2 — Abonnements MQTT locaux

**Accepté si et seulement si :**

1. Les topics listés en conf sont abonnés au démarrage (ou au reload).  
2. Un publish de test sur un topic abonné est reçu et tracé.  
3. Broker injoignable → log ERROR/WARNING, pas de crash du daemon ; reprise auto ou au reload documentée.  
4. Broker = localhost (pas d’exigence de MQTT distant pour EPIC-001).

### US-001.3 — Format Hestia stable

**Accepté si et seulement si :**

1. Tout événement remonté au Serveur respecte le schéma L3 (`schema_version`, `event_id`, horodatages, `source`, `signal_type`, `payload`).  
2. Deux observations distinctes → deux `event_id` distincts.  
3. Le payload **ne contient pas** : nom logique métier, pièce, identité personne, texte d’information utile.  
4. Le même événement HA rejoué avec le même `event_id` (renvoi) est traité comme doublon côté Serveur (voir US-001.5).

### US-001.4 — Filtrage / sélection

**Accepté si et seulement si :**

1. Un signal hors politique de sélection **n’est pas** envoyé au Serveur.  
2. Un signal dans la politique **est** envoyé (ou mis en file).  
3. La politique est **configurable** (fichier conf), pas hardcodée « magique ».  
4. Aucune règle du type « si présence alors alerter la famille ».

### US-001.5 — Sync sécurisée vers Serveur

**Accepté si et seulement si :**

1. L’endpoint ingest n’accepte que des requêtes authentifiées (mécanisme documenté : token nœud / mTLS / équivalent).  
2. Un événement valide → HTTP 2xx + ack ; visible côté Serveur (store ou journal d’ingest dédié).  
3. Rejeu même `event_id` → 2xx idempotent, **une seule** occurrence logique.  
4. Trafic hors canal sécurisé retenu → refusé.

### US-001.6 — Retry

**Accepté si et seulement si :**

1. Sur 5xx / timeout VPS, l’Agent réessaie avec backoff (paramètres documentés).  
2. Pas de boucle CPU serrée (intervalle minimal respecté).  
3. Succès après N échecs → événement livré une fois (avec dédup).

### US-001.7 — File locale hors-ligne

**Accepté si et seulement si :**

1. VPS down → événements restent sur disque local Agent.  
2. Redémarrage Agent avec VPS down → file **conservée**.  
3. VPS up → vidage ordonné ; complétude (aucun événement perdu de la période de test).  
4. Dédup empêche les doubles après renvoi.

### US-001.8 — Silence / reprise (OFFLINE_GRACE)

**Accepté si et seulement si :**

1. `OFFLINE_GRACE` configurable ; défaut 15 minutes (Module 70).  
2. Pour les tests : grace raccourcie (ex. 30–60 s) via conf.  
3. Après grace sans signal de l’ancre suivie → événement reachability `offline` émis / enfilé.  
4. Reprise de signal → événement reachability `online`.  
5. Aucune suppression d’équipement ; pas d’écriture SoT locale concurrente au Backend.

---

## 6. Ordre d’implémentation développeur

```text
1. L0  non-régression + conf
2. L1  MQTT
3. L2  source HA
4. L3  schéma normalisé (+ revue contrat avec hestia)
5. L5  ingest API hestia + client Agent   ← peut démarrer en parallèle de L4
6. L4  filtres
7. L6  queue + retry
8. L7  reachability
9. Validation terrain SNZB (TESTPLAN)
10. Checklist done Epic
```

Parallélisation possible : **L5 (côté hestia)** dès que le schéma L3 est figé sur papier ; **L4** en parallèle du client Agent L5.

---

## 7. Références (approfondissement)

| Sujet | Document |
|-------|----------|
| Rôle Agent | ADR-020 ; Glossaire |
| Chaîne nœud | architecture-domotique §2, §11, §12 |
| Frontière Agent / Backend | Module 70 §1.1 |
| Sélection | MODELE-INFORMATION §4 |
| OFFLINE_GRACE | Module 70 (15 min) |
| Contrat V1 actuel | hestia-agent `docs/ARCHITECTURE.md` |
| Epic | [EPIC-001.md](../EPIC-001.md) |
| Tests | [TESTPLAN-EPIC-001.md](TESTPLAN-EPIC-001.md) |
| Checklist | [CHECKLIST-EPIC-001.md](CHECKLIST-EPIC-001.md) |
| Clôture | [REPORT-CLOTURE-EPIC-001.md](REPORT-CLOTURE-EPIC-001.md) |
