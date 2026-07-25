# TESTPLAN-EPIC-001 — Stratégie de test

**Epic :** EPIC-001  
**Référence :** [EXEC-EPIC-001.md](EXEC-EPIC-001.md)

Objectif : valider l’implémentation **sans interprétation** — chaque cas a un résultat attendu observable.

---

## 1. Stratégie globale

| Niveau | Périmètre | Quand |
|--------|-----------|-------|
| **Unitaire** | Modules Agent (parse conf, normalize, filter, queue, backoff, grace) ; handler ingest `hestia` (auth, dédup) | Chaque lot |
| **Intégration** | Agent ↔ Mosquitto ; Agent ↔ HA ; Agent ↔ API `hestia` | L1, L2, L5, L6 |
| **Fonctionnel** | Chaîne bout-en-bout selon US | Fin de lot + fin Epic |
| **Terrain** | Nœud réel + SNZB-06P24 (ou banc) | Fin Epic |

**Règle :** un lot n’est mergeable que si ses tests unitaires + le smoke d’intégration du lot passent.

---

## 2. Tests unitaires

### 2.1 Configuration (L0)

| ID | Cas | Attendu |
|----|-----|---------|
| UT-L0-01 | Conf V1 seule (clés historiques) | Validation OK ; modules métier inactifs |
| UT-L0-02 | URL Backend invalide | Erreur conf claire (code/config) |
| UT-L0-03 | `OFFLINE_GRACE` non numérique | Rejet conf |
| UT-L0-04 | Reload SIGHUP avec nouvelle allowlist | Nouvelle policy active sans redémarrage process (si contrat reload) |

### 2.2 Normalisation (L3)

| ID | Cas | Attendu |
|----|-----|---------|
| UT-L3-01 | Entrée HA minimale valide | Payload conforme schéma ; `schema_version` posé |
| UT-L3-02 | Deux appels normalize | Deux `event_id` distincts |
| UT-L3-03 | Champ métier interdit injecté en entrée | Absent de la sortie (strip / refuse) |
| UT-L3-04 | Horodatage manquant côté source | `occurred_at` ou `received_at` renseigné selon règle documentée |

### 2.3 Filtrage (L4)

| ID | Cas | Attendu |
|----|-----|---------|
| UT-L4-01 | Entity dans allowlist | `retain = true` |
| UT-L4-02 | Entity hors allowlist | `retain = false` |
| UT-L4-03 | Denylist prime sur allowlist | `retain = false` |
| UT-L4-04 | Allowlist vide + politique « deny all » | Aucun retain (si c’est la politique documentée) |

### 2.4 Queue & retry (L6)

| ID | Cas | Attendu |
|----|-----|---------|
| UT-L6-01 | Enqueue / dequeue FIFO | Ordre préservé |
| UT-L6-02 | Crash après enqueue (fichier présent) | Relecture récupère l’événement |
| UT-L6-03 | Backoff après échecs | Délais croissants bornés |
| UT-L6-04 | Ack succès | Entrée retirée de la file |

### 2.5 Reachability (L7)

| ID | Cas | Attendu |
|----|-----|---------|
| UT-L7-01 | Dernier vu récent | Pas d’événement offline |
| UT-L7-02 | Silence > grace | Événement offline généré **une** fois |
| UT-L7-03 | Signal après offline | Événement online généré |
| UT-L7-04 | Grace = 0 ou négatif | Rejet conf ou borne minimale documentée |

### 2.6 API ingest (L5, `hestia`)

| ID | Cas | Attendu |
|----|-----|---------|
| UT-API-01 | Sans auth | 401/403 |
| UT-API-02 | Payload valide | 2xx + persist/journal |
| UT-API-03 | Même `event_id` deux fois | 2xx ; une seule occurrence logique |
| UT-API-04 | Schéma invalide | 4xx |

---

## 3. Tests d’intégration

### 3.1 MQTT (L1)

| ID | Montage | Attendu |
|----|---------|---------|
| IT-MQTT-01 | Mosquitto local + Agent | Subscribe OK |
| IT-MQTT-02 | `mosquitto_pub` topic abonné | Trace réception Agent |
| IT-MQTT-03 | Stop broker | Agent up ; log ; pas de sortie process |

### 3.2 Home Assistant (L2)

| ID | Montage | Attendu |
|----|---------|---------|
| IT-HA-01 | HA up ; toggle entité test | Événement dans flux Agent |
| IT-HA-02 | Stop HA | Agent up ; erreur source loguée |
| IT-HA-03 | Restart HA | Reprise flux sans redémarrage forcé Agent (ou doc si reload requis) |

### 3.3 Agent ↔ Serveur (L5–L6)

| ID | Montage | Attendu |
|----|---------|---------|
| IT-SYNC-01 | VPS/API up | Événement normalisé reçu côté Serveur |
| IT-SYNC-02 | API down pendant 1 min | File locale grossit |
| IT-SYNC-03 | API up après down | File vidée ; N événements = N uniques `event_id` |
| IT-SYNC-04 | Restart Agent pendant down | File intacte après restart |
| IT-SYNC-05 | Rejeu volontaire même `event_id` | Pas de double côté Serveur |

---

## 4. Tests fonctionnels (User Stories)

| ID | US | Scénario | Attendu |
|----|----|----------|---------|
| FT-001 | US-001.1 | Changer état entité HA | Visible Agent ≤ 5 s |
| FT-002 | US-001.2 | Pub MQTT test | Reçu Agent |
| FT-003 | US-001.3 | Inspecter payload Serveur | Schéma L3 ; pas de champs interdits |
| FT-004 | US-001.4 | Entity hors policy | Absent Serveur |
| FT-005 | US-001.4 | Entity in policy | Présent Serveur |
| FT-006 | US-001.5 | Appel sans token | Refusé |
| FT-007 | US-001.5 | Appel avec token | Accepté |
| FT-008 | US-001.6 | 503 simulé puis 200 | Livraison unique après retry |
| FT-009 | US-001.7 | Coupure réseau VPS | Aucune perte sur reprise |
| FT-010 | US-001.8 | Grace courte + silence | Offline puis online |

---

## 5. Non-régression V1 infrastructure

| ID | Cas | Attendu |
|----|-----|---------|
| NR-01 | `./bin/hestia-agent --health` conf V1 | Première ligne `OK`/`WARNING`/`FAILED` selon contrat |
| NR-02 | Codes sortie 0,6,7 | Inchangés sémantiquement |
| NR-03 | Seconde instance | `EXIT_ALREADY_RUNNING` |
| NR-04 | `--health` | **Ne contacte pas** le réseau / VPS / HA (contrat V1) |
| NR-05 | Format logs | `<ISO8601>Z [LEVEL] message` |

---

## 6. Validation terrain

### 6.1 Prérequis terrain

- Nœud L8 (ou équivalent)
- SNZB-06P24 appairé et visible HA (ou banc qui simule présence HA)
- Agent build EPIC-001 déployé
- Endpoint ingest joignable depuis le nœud

### 6.2 Scénarios terrain

| ID | Action | Attendu |
|----|--------|---------|
| TR-01 | Déclencher présence SNZB | Événement normalisé sur Serveur |
| TR-02 | Couper WAN nœud 10+ min (grace test) | File locale ; pas de crash |
| TR-03 | Rétablir WAN | Sync complète ; dédup OK |
| TR-04 | `--health` sur nœud | Conforme V1 (+ infos flux en détail si ajoutées **après** la 1re ligne) |

### 6.3 Résultat terrain

| Champ | Valeur |
|-------|--------|
| Date | |
| Nœud / commit Agent | |
| Commit `hestia` | |
| TR-01 … TR-04 | PASS / FAIL |
| Notes | |

---

## 7. Définition de « Epic testée »

L’EPIC-001 est **testée** lorsque :

1. Tous les UT des lots mergés sont verts.  
2. IT-MQTT, IT-HA, IT-SYNC pertinents passent.  
3. FT-001 → FT-010 passent (ou dérogation écrite dans CHECKLIST écarts).  
4. NR-01 → NR-05 passent.  
5. Au moins TR-01 PASS (terrain ou banc équivalent documenté).

---

## 8. Outillage suggéré (non normatif)

| Besoin | Exemple |
|--------|---------|
| MQTT | `mosquitto_pub` / `mosquitto_sub` |
| API | `curl` + token |
| HA | Developer tools → States |
| Isolation VPS | firewall / stop service API |

Les choix d’outils de test automatisés (bats, PHPUnit, etc.) restent au dépôt concerné — ce plan impose les **cas**, pas le framework.
