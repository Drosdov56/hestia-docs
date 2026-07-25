# CHECKLIST-EPIC-001 — Progression & done

**Epic :** EPIC-001  
**Référence :** [EXEC-EPIC-001.md](EXEC-EPIC-001.md) · [TESTPLAN-EPIC-001.md](TESTPLAN-EPIC-001.md)

Cocher uniquement lorsque le critère est **vérifié** (test ou revue), pas « prévu ».

---

## A. Prérequis environnement

- [ ] Nœud avec HA + Mosquitto + Agent infra V1 opérationnels
- [ ] Accès dépôt `hestia-agent` et `hestia`
- [ ] Schéma L3 lu et accepté par les deux dépôts (revue courte)
- [ ] Secret / auth nœud pour ingest défini (même brouillon)

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

- [ ] Endpoint `hestia` authentifié
- [ ] Agent envoie en sécurisé
- [ ] Ack 2xx sur succès
- [ ] Dédup `event_id` vérifiée

### L6 — Retry + file (US-001.6 / US-001.7)

- [ ] Backoff documenté et observé
- [ ] File durable sur disque
- [ ] Survive redémarrage Agent
- [ ] Reprise VPS → complétude sans doublon

### L7 — Reachability (US-001.8)

- [ ] `OFFLINE_GRACE` configurable (défaut 15 min)
- [ ] Test grace courte OK
- [ ] Événement offline émis
- [ ] Événement online émis
- [ ] Pas de SoT locale concurrente

---

## C. Critères done Epic

- [ ] Chaîne **SNZB (ou équivalent) → HA → Agent → API Serveur** démontrée
- [ ] Aucune règle « signification familiale » dans le code Agent (revue)
- [ ] `--health` V1 non régressé (OK / WARNING / FAILED + codes)
- [ ] TESTPLAN sections unitaires / intégration / fonctionnels exécutées (ou N/A justifié)
- [ ] Validation terrain (ou banc équivalent) cochée dans TESTPLAN

---

## D. Clôture

- [ ] Checklist A–C complète
- [ ] Écarts connus listés ci-dessous (ou « aucun »)
- [ ] EPIC-001 statut backlog → prêt à passer EPIC-002 / amorcer EPIC-004

### Écarts / dettes

| ID | Description | Report |
|----|-------------|--------|
| E-L1-01 | IT Mosquitto réel non exécuté sur poste Windows de dev (pas de `mosquitto_pub/sub`) — critères L1 couverts par mock `MQTT_SUB_CMD` | DEV-001-L1 |
| E-L2-01 | IT HA réel non exécuté sur poste de dev — critères L2 couverts par mock MQTT + `HA_PROBE_CMD` | DEV-001-L2 |
| E-L3-01 | FT-003 / IT-SYNC côté Serveur reportés à L5 — inspection schéma L3 faite en local (`events-normalized.jsonl`) | DEV-001-L3 |
| E-L3-02 | Preuve Python 3 = nœud Ubuntu (module 20) ; stub WindowsApps ≠ validation plateforme | DEV-001-L3 |
| E-L4-01 | FT-004/005 côté Serveur reportés à L5 — preuve locale via `events-outbound.jsonl` | DEV-001-L4 |

---

## Sign-off

| Rôle | Nom | Date |
|------|-----|------|
| Dev | | |
| Revue | | |
