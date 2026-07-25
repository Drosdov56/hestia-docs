# CHECKLIST-EPIC-001 — Progression & done

**Epic :** EPIC-001  
**Statut :** Clôturée (2026-07-25)  
**Référence :** [EXEC-EPIC-001.md](EXEC-EPIC-001.md) · [TESTPLAN-EPIC-001.md](TESTPLAN-EPIC-001.md) · [REPORT-CLOTURE-EPIC-001.md](REPORT-CLOTURE-EPIC-001.md)

Cocher uniquement lorsque le critère est **vérifié** (test ou revue), pas « prévu ».

---

## A. Prérequis environnement

- [x] Nœud avec HA + Mosquitto + Agent infra V1 opérationnels (prérequis hors backlog / FUNCTIONAL-VISION §9 ; IT Mosquitto/HA réels = E-L1-01 / E-L2-01)
- [x] Accès dépôt `hestia-agent` et `hestia`
- [x] Schéma L3 lu et accepté par les deux dépôts (revue courte — DEV-001-L3 / L5)
- [x] Secret / auth nœud pour ingest défini (même brouillon — conf + `ingest.node_token` ; provisionnement prod = E-L5-02)

---

## B. Lots techniques

### L0 — Socle conf & V1

- [x] Clés optionnelles documentées dans `agent.conf.example`
- [x] Sans nouvelles clés → comportement V1 inchangé
- [x] SIGHUP reload OK
- [x] Suite tests V1 (health, lock, state) verte

### L1 — MQTT (US-001.2)

- [x] Connexion localhost OK
- [x] Abonnements conf OK
- [x] Message test reçu / tracé
- [x] Broker down → pas de crash daemon

### L2 — HA (US-001.1)

- [x] Source HA branchée (mécanisme documenté)
- [x] Changement entité test visible dans flux Agent
- [x] HA down → Agent up + log

### L3 — Normalisation (US-001.3)

- [x] Schéma L3 implémenté
- [x] `event_id` + horodatages présents
- [x] Pas de champs métier interdits dans le payload
- [x] Mapping ancre / entity documenté

### L4 — Sélection (US-001.4)

- [x] Allowlist / denylist configurables
- [x] Signal hors policy non envoyé
- [x] Signal in policy envoyé / enfilé
- [x] Revue : aucune règle familiale

### L5 — Ingest (US-001.5)

- [x] Endpoint `hestia` authentifié
- [x] Agent envoie en sécurisé
- [x] Ack 2xx sur succès
- [x] Dédup `event_id` vérifiée

### L6 — Retry + file (US-001.6 / US-001.7)

- [x] Backoff documenté et observé
- [x] File durable sur disque
- [x] Survive redémarrage Agent
- [x] Reprise VPS → complétude sans doublon

### L7 — Reachability (US-001.8)

- [x] `OFFLINE_GRACE` configurable (défaut 15 min)
- [x] Test grace courte OK
- [x] Événement offline émis
- [x] Événement online émis
- [x] Pas de SoT locale concurrente

---

## C. Critères done Epic

- [x] Chaîne **SNZB (ou équivalent) → HA → Agent → API Serveur** démontrée (banc mock L2→L5 ; SNZB terrain réel = E-L7-01)
- [x] Aucune règle « signification familiale » dans le code Agent (revue L4)
- [x] `--health` V1 non régressé (OK / WARNING / FAILED + codes — NR L0)
- [x] TESTPLAN sections unitaires / intégration / fonctionnels exécutées (ou N/A justifié — écarts ci-dessous)
- [x] Validation terrain (ou banc équivalent) cochée dans TESTPLAN (§6.3 — banc ; SNZB réel = E-L7-01)

---

## D. Clôture

- [x] Checklist A–C complète
- [x] Écarts connus listés ci-dessous (ou « aucun »)
- [x] EPIC-001 statut backlog → prêt à passer EPIC-002 / amorcer EPIC-004

### Écarts / dettes — ouvertes à la clôture

| ID | Description | Report |
|----|-------------|--------|
| E-L1-01 | IT Mosquitto réel non exécuté sur poste Windows de dev (pas de `mosquitto_pub/sub`) — critères L1 couverts par mock `MQTT_SUB_CMD` | DEV-001-L1 |
| E-L2-01 | IT HA réel non exécuté sur poste de dev — critères L2 couverts par mock MQTT + `HA_PROBE_CMD` | DEV-001-L2 |
| E-L3-02 | Preuve Python 3 = nœud Ubuntu (module 20) ; stub WindowsApps ≠ validation plateforme | DEV-001-L3 |
| E-L5-02 | Token nœud à provisionner en prod (`ingest.node_token` / `HESTIA_INGEST_NODE_TOKEN` + `BACKEND_TOKEN`) | DEV-001-L5 |
| E-L6-01 | IT-SYNC durée réelle « 1 min » API down non chronométrée — critères couverts par file + backoff + reprise mock | DEV-001-L6 |
| E-L7-01 | Validation terrain SNZB / grace 15 min réelle reportée (TESTPLAN §6) — critères L7 couverts par grace courte mock | DEV-001-L7 |

### Écarts résolus par lots ultérieurs (historique)

| ID | Description | Résolu par |
|----|-------------|------------|
| E-L3-01 | FT-003 / IT-SYNC côté Serveur reportés à L5 | DEV-001-L5 |
| E-L4-01 | FT-004/005 côté Serveur reportés à L5 | DEV-001-L5 |
| E-L5-01 | IT-SYNC-02..04 (file / retry / restart) reportés à L6 | DEV-001-L6 |

---

## Sign-off

| Rôle | Nom | Date |
|------|-----|------|
| Dev | Clôture docs EPIC-001 | 2026-07-25 |
| Revue | | |
