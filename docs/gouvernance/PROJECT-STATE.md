# PROJECT STATE — HESTIA

Version : 1.1  
Dernière mise à jour : 2026-07-28 (soir — clôture session)  
Responsable : Équipe Hestia *(à compléter)*

---

# RÈGLE FONDAMENTALE

**PROJECT-STATE.md est la référence officielle de reprise entre les conversations** — toutes sessions ChatGPT, Cursor ou autre assistant incluses.

Toute nouvelle conversation commence par la fourniture de ce document.

L'assistant considère son contenu comme l'état officiel du projet et reprend directement le chantier actif.

L'assistant ne redémarre pas par un audit global, une reconstruction historique ou une remise en question de l'état du projet, sauf si un fait nouveau (commit, diff, document, décision explicite) contredit ce document.

En cas de divergence :

1. Les faits nouveaux priment.
2. PROJECT-STATE.md est mis à jour.
3. PROMPT-REPRISE.md est régénéré à partir de PROJECT-STATE.md.

**Discipline de clôture :** la clôture d'un chantier comprend obligatoirement la mise à jour de PROJECT-STATE.md.

---

# CONTRAT DE REPRISE

À chaque nouvelle conversation :

1. Lire PROJECT-STATE.md entièrement.
2. Le considérer comme la référence opérationnelle.
3. Ne pas refaire d'audit global sans demande explicite.
4. Reprendre directement le chantier actif.
5. Mettre à jour PROJECT-STATE.md avant la clôture de la session.

---

# ÉTAT GLOBAL

Statut général : **état stable figé** après validation terrain majeure AUTO-002E (2026-07-28 soir).

Chantier actif : **aucun** (pas de nouveau chantier ce soir).

Dernier chantier terminé : **AUTO-002E** — administration distante (E-1 commandes + E-2 terminal WS) — **clôturé et validé terrain**.

Prochain chantier (prochaine session) : **AUTO-002F** — cycle de vie du nœud (tokens par nœud). Ne pas démarrer ce soir.

Epic future notée (non démarrée) : **UI-001** — refonte ergonomique de l’administration des nœuds.

---

# INFRASTRUCTURE (FACTS UNIQUEMENT — sources dépôt)

> Ne pas confondre **VPS Ionos** (`/var/www/hestia`) et **nœud mini-PC** (`/opt/hestia`).  
> Sources : `hestia/docs/deployment.md`, `hestia/docs/CONTEXTE-PROJET.md`, `hestia-docs/docs/ecosysteme/ecosysteme.md`, `hestia-docs/docs/architecture/architecture-domotique.md`, ADR-023, `hestia-installer` (`/opt/hestia/*`), `hestia-agent/systemd/hestia-agent.service`, AUTO-001 exécution, `hestia-installer/docs/releases/v1.0.0.md`.

## Machine A — Serveur applicatif (VPS)

| Champ | Valeur documentée |
|-------|-------------------|
| Rôle | Serveur Hestia : PWA + API REST + (ADR-023) `hestia-ws-relay` |
| Hébergeur | Ionos — IP `217.154.6.120` |
| URL publique | `https://hestia.serpette.fr` |
| OS | NON DOCUMENTÉ (PHP/Apache documentés ; version Ubuntu VPS NON DOCUMENTÉE dans les dépôts) |
| Nom d'hôte | NON DOCUMENTÉ |
| Chemins | Dépôt `/var/www/hestia` ; DocumentRoot `/var/www/hestia/core/public` ; relay `/var/www/hestia/services/ws-relay` |
| Dépôt Git | `hestia` |
| Apache | Oui (2.4, prod) |
| PHP | Oui (API ; CLI pour relay) |
| WS Relay | Oui — unit `hestia-ws-relay.service`, loopback `127.0.0.1:8765` |
| hestia-agent | Non (pas sur le VPS) |
| Home Assistant | Non |
| Mosquitto | Non |
| Zigbee2MQTT | Non |
| SSH | `ssh -p 2222 root@217.154.6.120` |

## Machine B — Nœud (mini-PC foyer)

| Champ | Valeur documentée |
|-------|-------------------|
| Rôle | Nœud local autonome : Agent + HA + MQTT + Zigbee2MQTT |
| Emplacement | Mini-PC BMAX (réf. terrain) |
| OS | Ubuntu Server **26.04 LTS** x86_64 |
| Nom d'hôte documenté | `hestia` (release installer / rapport J1 Agent) |
| `node_id` documenté | `hestia-bmax` (suivi AUTO-001) — **distinct** du hostname |
| IP LAN documentée | `10.80.157.203` |
| Chemins produit | `/opt/hestia/agent`, `/opt/hestia/homeassistant`, `/opt/hestia/mosquitto`, `/opt/hestia/zigbee2mqtt` |
| Dépôts Git | Runtime : `hestia-agent` ; déploiement : `hestia-installer` |
| Apache | Non (périmètre VPS) |
| PHP API Hestia | Non |
| WS Relay | Non (client WS sortant côté Agent uniquement) |
| hestia-agent | Oui — `/opt/hestia/agent`, unit `hestia-agent.service` |
| Home Assistant | Oui — conteneur / config sous `/opt/hestia/homeassistant` |
| Mosquitto | Oui — `/opt/hestia/mosquitto` |
| Zigbee2MQTT | Oui — `/opt/hestia/zigbee2mqtt` |
| SSH | Documenté comme accessible vers l’hôte `hestia` / IP `10.80.157.203` (ex. `ssh … hestia`) ; **port SSH nœud NON DOCUMENTÉ** (défaut OpenSSH implicite dans les exemples) |

## Anti-confusion obligatoire

1. **`/var/www/hestia` = VPS Ionos** (interface Web + API).  
2. **`/opt/hestia` = mini-PC nœud** (Agent, HA, MQTT, Z2M).  
3. L’hôte nommé **`hestia` (Ubuntu 26.04, `/opt/hestia`) est le mini-PC**, **pas** le VPS.  
4. Pour vérifier le déploiement de l’**interface Web** : SSH **VPS** (`2222` / `217.154.6.120`), pas le mini-PC.

## Schéma de communication (documenté)

```text
Navigateur / Apps Hestia
        │  HTTPS (same-origin)
        ▼
Apache 2.4 (VPS) ──► PWA + API PHP (/var/www/hestia)
        │
        ├── /api/v1/*          → PHP (métier, sessions, tickets)
        └── /api/v1/.../ws     → proxy_wstunnel → hestia-ws-relay (127.0.0.1:8765)
                                      ▲
Agent (mini-PC) ── HTTPS heartbeat / ingest ──► API
Agent (mini-PC) ── WSS sortant ───────────────► relay (terminal)
Agent ←→ Home Assistant / Mosquitto / Zigbee2MQTT  (locaux au nœud)
```

---

# DÉPÔTS (SHA finaux — 2026-07-28 soir)

## hestia

HEAD : `6a451f9632ebf020e74d7742db18864c04350200` (`6a451f9`)  
Working tree : propre hors `services/ws-relay/scripts/` (scripts ops locaux non versionnés)  
État : synchronisé origin/main

---

## hestia-agent

HEAD : `cc9e315b7b3a524f9afcb2629e64f0cefcf6769b` (`cc9e315`)  
Working tree : clean  
État : synchronisé origin/main

---

## hestia-installer

HEAD : `c7460ba706fd2f4eda84536576007a8f01b0d369` (`c7460ba`)  
Working tree : clean  
État : synchronisé origin/main

---

## hestia-docs

HEAD : `4a653391b59049573bb7dcebdc6464faf152b7f4` (`4a65339`)  
Working tree : clean  
État : synchronisé origin/main

---

# AUTO-002E — CLÔTURE OFFICIELLE

| Attribut | Valeur |
|----------|--------|
| Statut | **CLÔTURÉ / VALIDÉ TERRAIN** (2026-07-28 soir) |
| Périmètre | E-1 commandes admin distantes + E-2 terminal interactif (ADR-023) |
| Suivi exécution | [`docs/backlog/execution/AUTO-002.md`](../backlog/execution/AUTO-002.md) |

## Validations terrain confirmées

- Infrastructure VPS vs mini-PC clarifiée (fin de confusion `/var/www` vs `/opt`).
- Déploiement complet VPS (API, relay, Apache WSS, PWA).
- Routeur SPA + menu « Nœuds Hestia » opérationnels.
- Parc nœuds, heartbeat, fiche nœud opérationnels.
- WS Relay + appariement Agent ↔ Relay validés.
- Terminal interactif validé en conditions réelles (`hestia-bmax`).
- Reconnexion après fermeture + cycle de vie sessions corrigés et revalidés.

## Anomalies du jour et résolutions

| # | Anomalie | Cause | Correctif (SHA) |
|---|----------|-------|-----------------|
| 1 | `/module/settings/nodes` → « Module en préparation » | VPS en HEAD ancien : `router.js` / `menu.js` sans branche nodes | Redéploiement SPA (source déjà dans AUTO-002B) + SW |
| 2 | Terminal bloqué « authentification… » (1ʳᵉ fois) | Agent mini-PC sans handlers `shell_session` (`type not allowed`) | Déploiement Agent AUTO-002E + `python websockets` (pydeps) |
| 3 | Params session vides après parse | `commands_parse_delivery` capturé via `$(…)` (sous-shell) | `hestia-agent` `02ee8fe` |
| 4 | `ws_url` agent en `https://` | `agentWsUrl()` utilisait le schéma HTTP | `hestia` `33d8e91` (`wss://`) |
| 5 | Reconnexion impossible après 1ʳᵉ session | `command_report` déqueue dans sous-shell du heartbeat → `finished` jamais appliqué ; file bloquée | `hestia-agent` `cc9e315` (peek+ack) + `hestia` `6a451f9` (finalize commande à la fermeture session) |
| 6 | Impression de reload / session perdue UI | Enter / re-render fiche sans isolation terminal | `nodes-terminal.js` / `nodes.js` (preventDefault, form isolé, close WS) — inclus dans `6a451f9` |

## Commits de stabilisation fin de journée (à retenir)

| Dépôt | SHA court | Message |
|-------|-----------|---------|
| hestia | `33d8e91` | fix(nodes): ws_url agent en wss |
| hestia | `6a451f9` | fix(nodes): cycle de vie shell_session |
| hestia-agent | `02ee8fe` | fix(agent): params shell_session hors sous-shell |
| hestia-agent | `cc9e315` | fix(agent): ack command_report hors sous-shell |

---

# DÉCISIONS STRUCTURANTES

1. `hestia-docs` = Source de vérité transverse — [DECISION-0001](DECISION-0001-DOCUMENTATION.md).
2. `hestia-installer/docs/ROADMAP.md` = feuille de route locale ; pilotage produit → `hestia-docs/docs/backlog/ROADMAP.md`.
3. Documents historiques = archives marquées, non normatives.
4. AUTO-001 clos / validé terrain 2026-07-27 — suivi : `docs/backlog/execution/AUTO-001.md`.
5. AUTO-002 ouvert (002A→002E faits ; 002F restant) — spec : `docs/architecture/AUTO-002-supervision-administration-noeuds.md`.
6. **ADR-023** accepté : terminal distant = WebSocket sortant + service `hestia-ws-relay` (aucune logique métier dans le relay).
7. Écosystème = 3 dépôts applicatifs + 1 dépôt documentaire.
8. Agent natif systemd ; HTTPS/WSS sortant vers le serveur.
9. Home Assistant encapsulé sur le nœud.
10. Procédures OPS → `hestia-installer/docs/INSTALL.md` ; statut produit → `hestia-docs`.
11. Pas de duplication conceptuelle entre dépôts.
12. SW PWA : bump `CACHE_NAME` obligatoire sous `client/` (courant : `hestia-v0.8.27`).
13. 32 tests E2E Playwright.
14. EPIC-001 livré.
15. Continuité IA : `docs/gouvernance/PROJECT-STATE.md` + `PROMPT-REPRISE.md`.
16. **UI-001** inscrit en backlog (refonte ergonomique admin nœuds) — non démarré.

---

# POINTS OUVERTS

- Responsable PROJECT-STATE à renseigner.
- G10 cold boot secteur — en attente séparée.
- Idle timeout session : non exercé bout-en-bout de façon dédiée (fermeture volontaire + finalize OK).
- UI-001 (ergonomie terminal / admin nœuds) — backlog uniquement.
- Scripts ops locaux `hestia/services/ws-relay/scripts/` encore non versionnés (à trancher ultérieurement).

---

# BLOCAGES

- Aucun.

---

# PROCHAINE SESSION

Objectifs immédiats :

1. Démarrer **AUTO-002F** (identité / token par nœud, révocation) — uniquement quand explicitement demandé.
2. Ne pas rouvrir la conception ADR-023 / architecture WS.
3. Ne pas démarrer UI-001 ni AUTO-002F « en bonus ».
4. Respecter la cartographie VPS vs mini-PC avant tout SSH.

---

# À NE PAS REFAIRE

- Audit documentaire global sans demande explicite.
- Omettre `hestia-docs` du périmètre trans-dépôts.
- Mélanger documentation et développement dans un même commit.
- Dupliquer roadmap/backlog produit hors de `hestia-docs`.
- Démarrer un chantier sur working tree sale.
- Remettre de la logique métier dans `hestia-ws-relay`.
- Confondre hostname `hestia` / `/opt/hestia` (mini-PC) avec le VPS.

---

# JOURNAL DES SESSIONS

| Date | Résumé | Décisions | Commit(s) | SHA | Résultat |
|------|--------|-----------|-----------|-----|----------|
| 2026-07-28 (soir) | Clôture session : AUTO-002E validé terrain complet | État stable figé ; UI-001 noté ; pas de 002F ce soir | hestia / agent / docs | `6a451f9` / `cc9e315` / `4a65339` | Terminal + reconnexion OK |
| 2026-07-28 | AUTO-002E livré + 1ʳᵉ validation VPS | ADR-023 ; relay systemd+Apache | hestia/agent/installer/docs | `526bbb7` / `3836bde` / `c7460ba` | Relay+WSS+API OK ; UI/agent restant alors |
| 2026-07-28 | Continuité IA officialisée | PROJECT-STATE + PROMPT-REPRISE | gouvernance | `28222a5` | OK |
| 2026-07-28 | Remise en état Git 4 dépôts | 10 commits atomiques | H1–H4, I1, A1, D1–D4 | voir DÉPÔTS | OK |
| 2026-07-28 | AUTO-002A inventaire nœud | Registre + API admin | hestia | `ec501f4` | 10+11+11 tests API OK |
