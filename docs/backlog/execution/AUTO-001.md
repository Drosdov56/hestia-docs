# AUTO-001 — Reconnexion autonome nœud ↔ serveur

| Attribut | Valeur |
|----------|--------|
| **Lot** | AUTO-001 |
| **Statut** | **TERMINÉ / VALIDÉ** (matériel réel) |
| **Validation terrain** | **2026-07-27** — mini-PC BMAX (`hestia-bmax`, Ubuntu 26.04) |
| **Spécification d’architecture (normative)** | [`docs/architecture/AUTO-001-reconnection-autonome-noeud.md`](../../architecture/AUTO-001-reconnection-autonome-noeud.md) |
| **Revue d’architecture (pré-lots)** | [`docs/architecture/AUTO-001-revue-architecture.md`](../../architecture/AUTO-001-revue-architecture.md) |
| **Livré** | **AUTO-001A…F (1ʳᵉ étape)** — présence qualifiée + cycle commandes sûres |
| **Phase** | Phase 1 clôturée (A–E) ; Phase 2 amorcée (F — commandes sûres, sans UI) ; **lot AUTO-001 clos** |
| **Phase suivante** | **AUTO-002** — supervision et administration des nœuds ([spec](../../architecture/AUTO-002-supervision-administration-noeuds.md)) |
| **Dépôts** | `hestia-agent`, `hestia`, `hestia-installer`, `hestia-docs` |
| **Alignement** | EPIC-011 (admin distante) — amorcé sans attendre EPIC-002 |
| **Jalons roadmap** | Inchangés (J1, J2, …) — AUTO-001 n’est **pas** un jalon J* |

> Ce fichier reste le **suivi d’exécution**. Le comportement attendu (états, événements, transitions, contrat ONLINE, heartbeat, dégradé, observabilité, lots A–F) est défini dans la spécification d’architecture ci-dessus.

## 1. Objectif

Après toute coupure (secteur, réseau, serveur), le mini-PC doit **retrouver de lui-même** un état opérationnel et redevenir **pilotable via le serveur Hestia**, sans SSH ni intervention locale.

```text
Administrateur → Serveur Hestia ← HTTPS sortant — Hestia Agent ← mini-PC
```

## 2. Analyse — état avant AUTO-001

| Besoin | Avant |
|--------|-------|
| Register / annonce au boot | Absent |
| ONLINE / OFFLINE nœud | Absent |
| Heartbeat | Absent |
| Snapshot santé publié | `--health` local uniquement |
| Reprise Backend | Retry ingest L6 si file non vide |
| Canal Serveur → Agent | Absent |

Conséquence Cold Boot : même avec Ubuntu/réseau OK, le serveur **ne savait pas** que le nœud était revenu.

## 3. Phase 1 — contrat livré

### API (`hestia`)

| Méthode | Chemin | Auth | Effet |
|---------|--------|------|-------|
| `POST` | `/api/v1/nodes/heartbeat` | Bearer nœud (`ingest.node_token`) | Upsert nœud, `ONLINE`, snapshot |
| `GET` | `/api/v1/nodes/{node_id}` | Bearer nœud **ou** session admin | État ; `OFFLINE` si `last_seen` > TTL (120 s) |

Réponse heartbeat : `{ ack, status: "ONLINE", node_id, commands }` — `commands` peuplé en AUTO-001F (0 ou 1 élément).

Persistance : `data/nodes/{node_id}.json` (un fichier par nœud) ; commandes : `data/node-commands/{node_id}.json`.

### Agent (`hestia-agent`)

Module `lib/presence.sh` :

1. Gate **Ready** (MQTT + HA + Backend configurés joignables) avant première annonce.
2. Heartbeat périodique (`NODE_HEARTBEAT_SEC`, défaut 30) + immédiat au Ready / reprise Backend.
3. Retry **infini** avec délai `RECONNECT_RETRY_SEC` (défaut 5) + jitter optionnel `NODE_RECONNECT_JITTER_PCT` (défaut 20, AUTO-001D) — pas de `PRESENCE_BACKOFF_*` distinct.
4. Logs cycle de vie (préfixe `presence:`) : démarrage → attente Ready → Authentication / Node registered / ONLINE → heartbeats ; perte → `RECONNECTING` / `DEGRADED` avec **cause** ; retry #N.
5. Unit systemd : **source de vérité OPS** = `hestia-installer/lib/hestia-agent.sh` → `agent_render_unit()` (module 70 génère, module 80 applique). Le fichier `hestia-agent/systemd/hestia-agent.service` est la référence dépôt Agent — à garder aligné, ne pas l’éditer seul sur le nœud.

## 4. Phase 2

### Livré (AUTO-001F — première étape)

- Une commande active / nœud ; poll `commands[]` ; rapport `command_report` ; types `noop` / `ping` / `reload_configuration` / `health_snapshot`.
- Idempotence Agent + timeout serveur ; pas d’UI ; token global inchangé.

### Suite (F+)

- File multi-commandes ; types admin (`reboot`, `update`, …) ; token par nœud ; UI Admin « nœuds ONLINE ».

## 5. Critères d’acceptation Phase 1

1. Cold boot sans SSH / sans login local → heartbeat → `GET nodes/{id}` = `ONLINE` + snapshot.
2. Coupure Internet / serveur / reboot nœud → retour ONLINE automatique.
3. Multi-nœuds : deux `node_id` → deux fichiers, pas d’écrasement.
4. Pas d’annonce Ready si gate services essentiels échoue.
5. Journaux Agent lisibles (cycle de vie).

## 6. Scénarios de test

| ID | Scénario | Attendu |
|----|----------|---------|
| T1 | Cold boot (AP dispo) | ONLINE sans intervention |
| T2 | Coupure Internet temporaire | Retry puis ONLINE |
| T3 | Restart Agent | Nouveau heartbeat, ONLINE |
| T4 | Restart API serveur | Retry puis ONLINE |
| T5 | Serveur down plusieurs minutes | Backoff max, puis reprise |
| T6 | Pertes réseau successives | Pas de crash ; ONLINE à chaque retour |
| T7 | Deux node_id | États indépendants |

## 7. Validation terrain

| Champ | Valeur |
|-------|--------|
| Date | 2026-07-26 |
| Lot | **AUTO-001B-G8** |
| Nœud | `hestia-bmax` (mini-PC BMAX, `10.80.157.203`) |
| API | `https://hestia.serpette.fr` |
| Critère métier | **ONLINE côté serveur** (poll `GET /api/v1/nodes/hestia-bmax`), pas SSH |
| Verdict G8 | **AUTO-001B validé terrain** — conformité comportementale ; aucune correction code |
| SKIP opératoires | Restart Mosquitto/HA (sudo docker hors NOPASSWD) ; coupure Internet réelle ; cold boot secteur — à rejouer opérateur |

### Preuves Phase 1 (session antérieure)

| Scénario | Résultat |
|----------|----------|
| Déploiement API (NodeStore + routes) | PASS |
| Déploiement Agent (`presence.sh` + `update-agent`) | PASS |
| Premier heartbeat → ONLINE + snapshot | PASS (`agent_version=0.1.0`, `services.ready=true`) |
| Cycle logs Agent | PASS (`Ready` → `Authentication successful` → `Node registered` → `Node ONLINE`) |
| Restart Agent → nouveau heartbeat ONLINE | PASS (T3) |
| Multi-nœuds (`hestia-bmax` + `auto001-probe-2`) | PASS — 2 fichiers sous `core/data/nodes/` |
| Cold boot secteur complet (coupure alimentation) | SKIP opérateur (AP hotspot) — proxy T3 / restart Agent |

### Preuves AUTO-001B-G8 (2026-07-26)

| Vérification | Résultat | Preuve |
|--------------|----------|--------|
| Démarrage auto Agent (systemd) | PASS | `hestia-agent.service=active` ; présence au boot service |
| FSM `BOOTING → WAITING_READY → READY → ONLINE` | PASS | journal `12:28:29`→`12:29:12` (et rejoué `12:33:47`→`12:34:42`) |
| Timers configurables | PASS | log `heartbeat=30s ready_retry=5s reconnect_retry=5s` |
| Heartbeats périodiques | PASS | ~30–35 s entre envois acceptés en régime |
| Restart Agent | PASS | nouveau `boot_id`, même `node_id=hestia-bmax`, retour ONLINE (~52 s gate) |
| Restart backend Hestia (Apache stop 45 s) | PASS | `ONLINE → RECONNECTING` → retries → `READY → ONLINE` (`12:31:49`→`12:32:35`) |
| Même `node_id` / pas de doublon | PASS | seul fichier `core/data/nodes/hestia-bmax.json` |
| Journaux FSM | PASS | transitions + `E_HEARTBEAT_FAIL` / reconnect gate / ack |
| Restart Mosquitto | SKIP | `sudo docker` + pas de commande `hestia-ops` dédiée (NOPASSWD OPS seulement) |
| Restart Home Assistant | SKIP | idem |
| Coupure Internet puis retour | SKIP | risque coupure SSH ; pas d’iptables NOPASSWD |
| Cold boot secteur complet | SKIP | à rejouer opérateur ; critère = `GET …/hestia-bmax` = ONLINE |

### Anomalies / corrections G8

| Item | Statut |
|------|--------|
| Anomalies bloquantes conformité AUTO-001 | **Aucune** |
| Corrections code | **Aucune** |
| Faux négatif script S1 (grep Unicode `→`) | Non bloquant — preuves journal = PASS |

1. Critère métier : **nœud ONLINE côté serveur**, pas « SSH disponible ».
2. Cold boot physique : procédure INSTALL.md / REPORT-J1 §2 + poll API uniquement.
3. SKIP HA/MQTT/WAN : procédure manuelle opérateur (`sudo docker restart …` / iptables) si preuve terrain exhaustive souhaitée.

## 8. AUTO-001C — Durcissement opérationnel (2026-07-26)

| Champ | Valeur |
|-------|--------|
| Objectif | Robustesse exploitation (systemd, conf OPS, journaux, sondes) — **sans** changement API / identité / FSM métier |
| Verdict | **Livré** — tests unitaires PASS ; **terrain BMAX validé** (unit + présence ONLINE) |

### Changements

| Zone | Fichiers | Effet |
|------|----------|--------|
| systemd (référence Agent) | `hestia-agent/systemd/hestia-agent.service` (+ vendor) | `network-online`, `docker.service` (ordre), `StartLimitBurst=5` |
| systemd (générateur OPS) | `hestia-installer/lib/hestia-agent.sh` (`agent_render_unit`) | **Source de vérité** déployée par module 70/80 |
| Conf OPS | `lib/config.sh`, `agent.conf.example` | Timers `5..3600` / `1..600` ; erreurs explicites ; défauts documentés |
| Observabilité | `lib/presence.sh`, `lib/logger.sh` (`log_debug`) | Messages `ONLINE` / `DEGRADED` / `RECONNECTING` ; anti-spam gate ; HB périodique DEBUG |
| Robustesse | `lib/presence.sh` | Pas de sondes lourdes hors échéance en ONLINE/DEGRADED/RECONNECTING |

### Tests

| Suite | Résultat |
|-------|----------|
| AUTO-001B `test_presence_auto001.sh` | **35/35 PASS** (non-régression) |
| AUTO-001C `test_presence_auto001c.sh` | **19/19 PASS** |
| L0 + timers C `test_config_l0.sh` | **20/20 PASS** |
| Health J1 | **35/35 PASS** |
| Installer module 70 | **15/15 PASS** |

### Terrain (BMAX, 2026-07-26)

| Contrôle | Résultat |
|----------|----------|
| Unit `/etc/systemd/system/hestia-agent.service` | `After=…network-online…docker`, `Wants=network-online`, `StartLimitBurst=5`, `Restart=on-failure` |
| `systemctl show` | `Wants=network-online.target` ; After inclut `network-online.target` + `docker.service` |
| Présence après `update-agent` | `BOOTING → WAITING_READY → READY → ONLINE` + log `ONLINE — heartbeat accepté… (node_id=hestia-bmax)` |

### Reste pour AUTO-001D

- Raffinement DEGRADED / causes locales (hors durcissement C).
- Éléments Phase 1 non C : cold boot secteur opérateur ; SKIP HA/MQTT/WAN G8.
- **Ne pas** ouvrir D/E/F (commandes, token/nœud, UI) dans C.

## 9. AUTO-001D — Enrichissement diagnostic (2026-07-26)

| Champ | Valeur |
|-------|--------|
| Objectif | Causes explicites, snapshot enrichi, logs, jitter — **sans** changement contrat API / identité |
| Verdict | **Livré** |

### Changements

| Zone | Fichiers | Effet |
|------|----------|--------|
| Spec | `AUTO-001-reconnection-autonome-noeud.md` §9.2 | Vocabulaire fermé des causes ; champs snapshot normatifs D |
| Agent | `lib/presence.sh`, `lib/config.sh`, `agent.conf.example` | `health.cause` / `link`, `agent_fsm_state`, `degraded_reasons`, distinction auth/network/backend, jitter |
| Serveur | `NodeStore.php` | Persistance snapshot `agent_fsm_state`, `presence_attempts`, `degraded_reasons` (réponse POST inchangée) |

### Tests

| Suite | Résultat |
|-------|----------|
| AUTO-001A (+ UT-N-11) | **11/11 PASS** |
| AUTO-001B | **35/35 PASS** |
| AUTO-001C | **19/19 PASS** |
| AUTO-001D `test_presence_auto001d.sh` | **26/26 PASS** |
| L0 | **20/20 PASS** |

### Corrections documentaires (revue de clôture)

1. §3 Agent : suppression libellés obsolètes `PRESENCE_BACKOFF_*` / « Health published → Ready ».
2. §3 Agent : référence unique OPS unit = `agent_render_unit()` (évite divergence fichier Agent vs générateur).

### Reste pour AUTO-001E

- Cold boot secteur réel ; coupure WAN ; restart HA/Mosquitto (SKIP G8).
- Preuves terrain exhaustives sans SSH.
- **Hors E** : commandes / token par nœud / UI → AUTO-001F.

## 10. AUTO-001E — Qualification opérationnelle G9 (2026-07-26)

| Champ | Valeur |
|-------|--------|
| Nœud | `hestia-bmax` (BMAX) |
| API | `https://hestia.serpette.fr` |
| Agent | AUTO-001D déployé (`jitter=20%`, causes / snapshot) |
| Verdict | **AUTO-001E validé** — présence autonome qualifiée pour exploitation opérationnelle |

### Étape 0 — Audit (réemploi, pas de doublon)

| Source | Réutilisé pour G9 |
|--------|-------------------|
| G8 | Restart Agent (T3) ; backend Apache ; FSM boot service ; identité / pas de doublon |
| C terrain | Unit `network-online` + `StartLimit` |
| D unitaire | Causes `mqtt` / `ha` / `auth` / `backend` / `network` (UT-D-02…06) |
| Spec T1–T7 | Couverture ci-dessous (PASS ou SKIP justifié) |

### Scénarios exécutés

| # | Scénario | Résultat | Preuve utile |
|---|----------|----------|--------------|
| 1 | Cold boot secteur (coupure alimentation) | **SKIP justifié** | `sudo -n reboot` indisponible (NOPASSWD = `hestia-ops` seul) ; AP hotspot — procédure INSTALL. **Proxy PASS** : restart Agent + unit C (`network-online`) + ONLINE sans SSH |
| 2 | Coupure Internet WAN | **SKIP justifié** | Risque coupure SSH / pas d’iptables NOPASSWD. **Proxy PASS** : scénario 3 (même FSM `RECONNECTING`, cause lien) ; UT-P-10 réseau |
| 3 | Backend indisponible (Apache stop ~55 s) | **PASS** | `ONLINE → RECONNECTING (cause=backend)` → retries ±20 % → `READY → ONLINE` (`18:02:24`→`18:03:24`) ; `node_id` inchangé ; 1 fichier |
| 4 | Home Assistant stop/start | **SKIP justifié** | `sudo docker` interactif requis. **Couvert** UT-D-03 (`cause=ha`) + health gate terrain |
| 5 | Mosquitto stop/start | **SKIP justifié** | Idem docker. **Couvert** UT-D-02 (`cause=mqtt`) |
| 6 | Longue durée (~12 min post-restart) | **PASS** | Heartbeats ~30–35 s en DEBUG ; RSS Agent stable (~6,5 Mo) ; 1 process Agent ; ~36 lignes `presence:` / 10 min (pas de spam régime) ; `uptime_seconds=731`, `status=ONLINE` |
| 7 | Résilience identité | **PASS** | `node_id=hestia-bmax` constant ; `boot_id` `17:59:08`→`18:04:00` après restart Agent seulement ; seul `hestia-bmax.json` ; snapshot D (`agent_fsm_state`, `health.cause=none`) |

### Anomalies / corrections

| Item | Statut |
|------|--------|
| Anomalies bloquantes | **Aucune** |
| Corrections code G9 | **Aucune** |
| Cosmétique | log `http=000000` en outage (affichage code) — non bloquant |
| Accès OPS docker / reboot | Limite sudoers — hors défaut présence ; procédure opérateur INSTALL |

### Suites unitaires (non-régression)

A 11/11 · B 35/35 · D 26/26 (session G9) — inchangées.

### Reste pour AUTO-001F (avant livraison F)

File `commands[]`, token par nœud, UI admin nœuds — Phase 2.  
Rejouages opérateur optionnels : cold boot secteur, WAN, `sudo docker restart homeassistant|mosquitto`.

## 11. AUTO-001F — Commandes serveur → Agent (2026-07-26)

| Champ | Valeur |
|-------|--------|
| Objectif | Cycle complet commande sûre : enqueue → heartbeat → exécution 1× → résultat |
| Verdict | **Livré** (première étape Phase 2) — tests unitaires PASS ; terrain non bloquant pour F |

### Étape 0 — Spec

Spécification complétée : §2.2 / §2.3, §8.4, §15 (modèle, FSM, invariants, types, robustesse) dans `AUTO-001-reconnection-autonome-noeud.md`. Aucun nouveau document.

### Changements

| Zone | Fichiers | Effet |
|------|----------|--------|
| Serveur | `CommandStore.php`, `NodesController.php`, `index.php` | Enqueue, livraison `commands[]`, `command_report`, timeout, GET current |
| Agent | `lib/commands.sh`, `lib/presence.sh`, `bin/hestia-agent` | Parse réponse HB, exécution allowlist, file rapports, état durable anti-doublon |
| Tests | `tests/api/test_nodes_commands.sh`, `tests/presence/test_commands_auto001f.sh` | Cycle API + Agent |

### Contrat (résumé)

```text
POST /api/v1/nodes/{id}/commands   → pending
POST /api/v1/nodes/heartbeat       → commands:[cmd]  (pending→running)
Agent exécute 1× + command_report started|finished
GET  /api/v1/nodes/{id}/commands/current → statut traçable
```

Types autorisés : `noop`, `ping`, `reload_configuration`, `health_snapshot`.  
Interdits : `reboot`, `update`, `shutdown`, `shell`, `docker`, `systemctl`, scripts.

### Tests

| Suite | Résultat |
|-------|----------|
| AUTO-001F API `test_nodes_commands.sh` | **11/11 PASS** |
| AUTO-001F Agent `test_commands_auto001f.sh` | **16/16 PASS** |
| AUTO-001A | **11/11 PASS** (non-régression) |
| AUTO-001B | **35/35 PASS** |
| AUTO-001C | **19/19 PASS** |
| AUTO-001D | **26/26 PASS** |

### Terrain

Non exigé pour le critère F unitaire (cycle local). Validation ops optionnelle : enqueue `ping` sur `hestia-bmax` + lecture `commands/current` (voir INSTALL).

## 12. Clôture officielle — validation terrain BMAX (2026-07-27)

| Champ | Valeur |
|-------|--------|
| Nœud | `hestia-bmax` (BMAX, Ubuntu Server 26.04) |
| Verdict | **AUTO-001 TERMINÉ / VALIDÉ** sur matériel réel |
| Pipeline | pin Agent `0.1.1` · OPS-001/002 · AGENT-001 (flock) · AGENT-002 (vendor) |
| Prochaine étape | **AUTO-002** |

### Preuves finales (critères d’acceptation Phase 1)

| Preuve | Résultat |
|--------|----------|
| Reboot logiciel | **PASS** — service `hestia-agent` redémarré automatiquement |
| Coupure secteur (cold boot) | **PASS** — reprise sans intervention manuelle |
| Verrou flock | **PASS** — `/run/hestia/agent/hestia-agent.lock` recréé (`RuntimeDirectory`) |
| systemd | **PASS** — redémarrage auto ; pas de StartLimit bloquant |
| Node ONLINE | **PASS** — heartbeat accepté (`GET /api/v1/nodes/{id}`) |
| Queue L6 | **PASS** — `pending` transitoire puis retour à `0` |
| Journal | **PASS** — `sudo journalctl -u hestia-agent -b -p warning` : aucune entrée |

### Reste hors AUTO-001 (F+ / AUTO-002)

- File multi-commandes / priorités.
- Commandes d’administration (reboot, update OPS, …).
- Token par nœud + révocation.
- UI admin nœuds.
- Signature des commandes (extension point réservé).
