# PROJECT STATE — HESTIA

Version : 1.6  
Dernière mise à jour : 2026-07-31 (clôture EPIC-003)  
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

Statut général : **état stable** — **AUTO-002 CLÔTURÉ** · **UI-001 CLÔTURÉ** · **UI-002 CLÔTURÉ** · **EPIC-002 CLÔTURÉ** · **EPIC-003 CLÔTURÉ** (2026-07-31).

Chantier actif : **aucun**.

Dernier chantier terminé : **EPIC-003** — Assistant de mise en service UX-003 (lots A→D, F-013→F-017).

Prochain chantier (backlog) : **aucun inscrit**. Ne pas inventer ni démarrer de chantier sans demande explicite.

Capacités figées après EPIC-003 :

- assistant complet de mise en service disponible ;
- parcours : détection → admission → validation → mise en service → appairage ;
- intégration avec `hestia-agent` documentée comme opérationnelle (checks techniques, permit-join) ;
- registre / SoT / machine d’états / nom logique / remplacement (EPIC-002) inchangés et réutilisés.

Références clôture AUTO-002 :

- Suivi : `hestia-docs/docs/backlog/execution/AUTO-002.md`
- Spec : `AUTO-002-supervision-administration-noeuds.md`
- Identité : `AUTO-002F-identite-cycle-vie-noeud.md` · **ADR-021** (Accepté)
- Terminal WS : **ADR-023** (Accepté) — relay = transport

Références clôture UI-001 : voir section **UI-001 — CLÔTURE OFFICIELLE** ci-dessous.

Références clôture UI-002 : voir section **UI-002 — CLÔTURE OFFICIELLE** ci-dessous.

Références clôture EPIC-002 : voir section **EPIC-002 — CLÔTURE OFFICIELLE** ci-dessous.

Références clôture EPIC-003 : voir section **EPIC-003 — CLÔTURE OFFICIELLE** ci-dessous.

---

# INFRASTRUCTURE (FACTS UNIQUEMENT — sources dépôt)

> Ne pas confondre **VPS Ionos** (`/var/www/hestia`) et **nœud mini-PC** (`/opt/hestia`).  
> Sources : `hestia/docs/deployment.md`, `hestia/docs/CONTEXTE-PROJET.md`, `hestia-docs/docs/ecosysteme/ecosysteme.md`, `hestia-docs/docs/architecture/architecture-domotique.md`, ADR-023, ADR-021, `hestia-installer` (`/opt/hestia/*`), `hestia-agent/systemd/hestia-agent.service`, AUTO-001 / AUTO-002 exécution, `hestia-installer/docs/releases/v1.0.0.md`.

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
        ├── /api/v1/*          → PHP (métier, sessions, tickets, auth nœud)
        └── /api/v1/.../ws     → proxy_wstunnel → hestia-ws-relay (127.0.0.1:8765)
                                      ▲
Agent (mini-PC) ── HTTPS heartbeat / ingest (Bearer credential nœud) ──► API
Agent (mini-PC) ── WSS sortant ─────────────────────────────────────────► relay (terminal)
Agent ←→ Home Assistant / Mosquitto / Zigbee2MQTT  (locaux au nœud)
```

---

# DÉPÔTS (SHA — état au 2026-07-31)

## hestia

HEAD : tip **EPIC-003-D** `b93d209`  
Working tree : clean  
État : synchronisé origin/main  
Jalons 002F : F2 `9f46062` · F3 `213145a` · F4A `e334181` · F6 `09d3522`  
UI-001 : B `af1dae4` · C `1d4f8e2` · D `ea5a667` · E `91aaeae` · F `62c54fc` · G `aa6f3ad`  
UI-002 : A `abff25c` · B `d7e22c9` · C `0634fdd` · D `f7c481d`  
EPIC-002 : A `153dff4` · B `b59ed07` · C `58f5755` · D `c73a7d5`  
EPIC-003 : A `499e535` · B `22f2bf8` · C `ac22e51` · D `b93d209`  
SW PWA : `hestia-v0.8.41`

---

## hestia-agent

HEAD : `a749a3f5edc5039d2492b16955c342334b16f1e3` (`a749a3f`)  
Working tree : clean  
État : synchronisé origin/main  
Jalons 002F : F5 tip `a749a3f`

---

## hestia-installer

HEAD : `0b7d00299b0ab71e0e145c5ee5301a39c257e9e3` (`0b7d002`)  
Working tree : clean  
État : synchronisé origin/main  
Jalons 002F : F4B tip `0b7d002`

---

## hestia-docs

HEAD : tip clôture EPIC-003 (ce commit)  
Working tree : clean  
État : synchronisé origin/main

---

# AUTO-002 — CLÔTURE OFFICIELLE

| Attribut | Valeur |
|----------|--------|
| Statut | **CLÔTURÉ** (2026-07-29) |
| Périmètre | 002A inventaire · 002B dashboard · 002C observabilité · 002D diagnostics · 002E admin distante + terminal · 002F identité / lifecycle |
| Suivi | [`docs/backlog/execution/AUTO-002.md`](../backlog/execution/AUTO-002.md) |

## Architecture définitive — identité des nœuds (ADR-021 / AUTO-002F)

| Concept | Règle |
|---------|-------|
| `node_id` | Identité logique **permanente** ; autorité serveur |
| `display_name` | Mutable ; **hors auth** |
| `hostname` | Observationnel (Agent) ; **hors auth** |
| Credential | **Un token actif / nœud** ; hash-only serveur ; clair one-shot |
| Présence | `ONLINE`/`OFFLINE` **≠** `lifecycle_state` |
| Remplacement | Même `node_id` + rotation token |
| Token global | **Supprimé définitivement** (F6) — plus de `ingest.node_token` |
| Relay | ADR-023 inchangé — **aucune** logique identité |

## Validations retenues

- Terrain AUTO-002E (2026-07-28 soir) : parc, heartbeat, terminal, reconnexion sur `hestia-bmax`.
- Campagnes API F2/F3/F4A/F6 (`test_nodes_auth_f2`, `test_nodes_admin_f3`, `test_nodes_bootstrap_f4a`, `test_nodes_identity_f6` + suite nodes).
- Agent F5 : `tests/presence/test_identity_auto002f5.sh`.
- Installer F4B : tests bootstrap + CI (`2aac00a`).

## AUTO-002E — rappel (sous-clôture 2026-07-28)

Conservé pour historique : terminal WS, anomalies du jour et SHA `6a451f9` / `cc9e315` — détail dans le journal et `execution/AUTO-002.md`.

---

# UI-001 — CLÔTURE OFFICIELLE

| Attribut | Valeur |
|----------|--------|
| Statut | **TERMINÉ** — **VALIDÉ AVEC RÉSERVES** (2026-07-30) |
| Dépôt | `hestia` (PWA admin nœuds) |
| Périmètre | Lots **B→G** — présentation uniquement (pas d’API / pas de logique métier) |
| Tech Committee | Réserves arbitrées → backlog **UI-002** ; **pas de réouverture de UI-001** |
| SW | `hestia-v0.8.33` |

## Commits de clôture (hestia)

| Lot | SHA | Contenu |
|-----|-----|---------|
| **UI-001-B** | `af1dae4` | Fiche nœud en onglets |
| **UI-001-C** | `1d4f8e2` | Synthèse opérationnelle en cartes |
| **UI-001-D** | `ea5a667` | Pilotage hiérarchisé par impact |
| **UI-001-E** | `91aaeae` | Identité en cartes |
| **UI-001-F** | `62c54fc` | Observabilité en cartes |
| **UI-001-G** | `aa6f3ad` | Parc en cartes de supervision |

## Synthèse des apports

- Parc en cartes  
- Fiche à onglets  
- Synthèse opérationnelle  
- Pilotage hiérarchisé  
- Identité clarifiée  
- Observabilité restructurée  

## Réserves

Transformées en backlog **UI-002** (Consolidation ergonomique) — lots indépendants issus des décisions **ACCEPTÉES** du Tech Committee. Éléments REPORTÉS / REJETÉS hors UI-002. **UI-002 CLÔTURÉ** (2026-07-31).

---

# UI-002 — CLÔTURE OFFICIELLE

| Attribut | Valeur |
|----------|--------|
| Statut | **CLÔTURÉ** (2026-07-31) |
| Dépôt | `hestia` (PWA admin nœuds) |
| Périmètre | Lots **A→D** — consolidation ergonomique uniquement (pas d’API / pas de logique métier / ADR-021 / ADR-023 inchangés) |
| Backlog | [`docs/backlog/UI-002.md`](../backlog/UI-002.md) |
| SW | `hestia-v0.8.37` |

## Commits de référence (hestia)

| Lot | SHA | Contenu |
|-----|-----|---------|
| **UI-002-A** | `abff25c` | Consolidation ergonomique du Parc |
| **UI-002-B** | `d7e22c9` | Observabilité lisible |
| **UI-002-C** | `0634fdd` | Accessibilité |
| **UI-002-D** | `f7c481d` | Hygiène CSS / JS |

## Synthèse des lots A→D

- **A** — Parc allégé (densité, redondances, navigation)
- **B** — Dates formatées, JSON repliable, Diagnostic plus lisible
- **C** — Navigation clavier, ARIA des onglets
- **D** — Code mort retiré, factorisation légère, helpers locaux

Comportement métier de UI-001 conservé. Lots indépendants, chacun validé séparément.

---

# EPIC-002 — CLÔTURE OFFICIELLE

| Attribut | Valeur |
|----------|--------|
| Statut | **TERMINÉ / CLÔTURÉ** (2026-07-31) |
| Dépôt | `hestia` (API / core — SoT équipements) |
| Périmètre | Lots **A→D** — F-007→F-012 (Module 70 / ADR-005) |
| Backlog | [`docs/backlog/EPIC-002.md`](../backlog/EPIC-002.md) |
| Exécution | [`docs/backlog/execution/EXEC-EPIC-002.md`](../backlog/execution/EXEC-EPIC-002.md) |
| Tip | `c73a7d5` |

## Commits de référence (hestia)

| Lot | SHA | Contenu |
|-----|-----|---------|
| **EPIC-002-A** | `153dff4` | Fondation SoT `Equipment` (F-007, F-009) |
| **EPIC-002-B** | `b59ed07` | Machine d’états (F-008) |
| **EPIC-002-C** | `58f5755` | Nom logique + `pending_ops` (F-010) |
| **EPIC-002-D** | `c73a7d5` | Remplacement + reprises (F-011, F-012) |

## Synthèse

- Registre équipements conforme Module 70  
- Source of Truth Backend opérationnelle  
- Machine d’états opérationnelle (transitions centralisées)  
- Nom logique SoT + opérations différées (sans exécution auto Agent)  
- Remplacement deux fiches et reprises §6.8 disponibles  

---

# EPIC-003 — CLÔTURE OFFICIELLE

| Attribut | Valeur |
|----------|--------|
| Statut | **TERMINÉ / CLÔTURÉ** (2026-07-31) |
| Dépôt | `hestia` (PWA Admin + API) |
| Périmètre | Lots **A→D** — F-013→F-017 (assistant mise en service UX-003 / ADR-004) |
| Backlog | [`docs/backlog/EPIC-003.md`](../backlog/EPIC-003.md) |
| Exécution | [`docs/backlog/execution/EXEC-EPIC-003.md`](../backlog/execution/EXEC-EPIC-003.md) |
| Tip | `b93d209` |
| SW | `hestia-v0.8.41` |

## Commits de référence (hestia)

| Lot | SHA | Contenu |
|-----|-----|---------|
| **EPIC-003-A** | `499e535` | Détection et admission (F-016) |
| **EPIC-003-B** | `22f2bf85ee35069e434022763cb21c2f00748a31` | Validation technique (F-013, F-014) |
| **EPIC-003-C** | `ac22e512790701ffc75cd0eb8f8bb0d92d0a02e2` | Parcours de mise en service (F-015) |
| **EPIC-003-D** | `b93d2096fc3e6c633e3f9c30262f643554b8ef29` | Appairage via Agent (F-017) |

## Synthèse

- Assistant complet de mise en service disponible  
- Parcours : détection → admission → validation → mise en service → appairage  
- Intégration `hestia-agent` opérationnelle (orchestration checks / permit-join)  
- Aucune étape métier n’exige l’UI HA ou Z2M  

---

# DÉCISIONS STRUCTURANTES

1. `hestia-docs` = Source de vérité transverse — [DECISION-0001](DECISION-0001-DOCUMENTATION.md).
2. `hestia-installer/docs/ROADMAP.md` = feuille de route locale ; pilotage produit → `hestia-docs/docs/backlog/ROADMAP.md`.
3. Documents historiques = archives marquées, non normatives.
4. AUTO-001 clos / validé terrain 2026-07-27 — suivi : `docs/backlog/execution/AUTO-001.md`.
5. **AUTO-002 CLÔTURÉ** (2026-07-29) — specs `AUTO-002-*.md` · `AUTO-002F-*.md`.
6. **ADR-023** accepté : terminal distant = WebSocket sortant + `hestia-ws-relay` (aucune logique métier dans le relay).
7. **ADR-021** accepté : identité nœud permanente + token par nœud + hash-only ; présence ≠ lifecycle ; **token global retiré**.
8. Écosystème = 3 dépôts applicatifs + 1 dépôt documentaire.
9. Agent natif systemd ; HTTPS/WSS sortant vers le serveur.
10. Home Assistant encapsulé sur le nœud.
11. Procédures OPS → `hestia-installer/docs/INSTALL.md` ; statut produit → `hestia-docs`.
12. Pas de duplication conceptuelle entre dépôts.
13. SW PWA : bump `CACHE_NAME` obligatoire sous `client/` (courant : `hestia-v0.8.41`).
14. 32 tests E2E Playwright.
15. EPIC-001 livré.
16. Continuité IA : `docs/gouvernance/PROJECT-STATE.md` + `PROMPT-REPRISE.md`.
17. **UI-001 CLÔTURÉ** (2026-07-30) — VALIDÉ AVEC RÉSERVES ; tip `aa6f3ad` ; réserves → **UI-002**.
18. **UI-002 CLÔTURÉ** (2026-07-31) — consolidation ergonomique lots A→D ; tip `f7c481d` ; SW `hestia-v0.8.37`.
19. **EPIC-002 CLÔTURÉ** (2026-07-31) — SoT équipements Module 70 ; tip `c73a7d5` ; F-007→F-012.
20. **EPIC-003 CLÔTURÉ** (2026-07-31) — Assistant mise en service UX-003 ; tip `b93d209` ; F-013→F-017.

---

# POINTS OUVERTS

- Responsable PROJECT-STATE à renseigner.
- G10 cold boot secteur — en attente séparée.
- Idle timeout session terminal : non exercé bout-en-bout de façon dédiée (fermeture volontaire + finalize OK).
- Déploiement VPS / nœud des commits F2–F6 + UI-001 + UI-002 + EPIC-002 + EPIC-003 : à confirmer ops si non déjà appliqué en prod (hors clôture code).
- Consommation Agent des `pending_ops` équipements : hors EPIC-002 (exécution terrain différée).
- Aucun prochain chantier produit inscrit — attendre une demande explicite.

---

# BLOCAGES

- Aucun pour la clôture documentaire EPIC-003.

---

# PROCHAINE SESSION

Objectifs immédiats :

1. **Aucun chantier actif** — UI-001, UI-002, EPIC-002 et EPIC-003 clos ; ne pas les rouvrir sauf anomalie critique.
2. Ne pas inventer ni démarrer de prochain chantier sans demande explicite.
3. Ne pas rouvrir ADR-021 / ADR-023 / ADR-004 / ADR-005 ; ne pas réintroduire de token global.
4. Ne pas remettre de logique métier dans `hestia-ws-relay`.
5. Respecter la cartographie VPS vs mini-PC avant tout SSH.

---

# À NE PAS REFAIRE

- Audit documentaire global sans demande explicite.
- Omettre `hestia-docs` du périmètre trans-dépôts.
- Mélanger documentation et développement dans un même commit.
- Dupliquer roadmap/backlog produit hors de `hestia-docs`.
- Démarrer un chantier sur working tree sale.
- Remettre de la logique métier dans `hestia-ws-relay`.
- Réintroduire `ingest.node_token` / auth globale parc.
- Confondre hostname `hestia` / `/opt/hestia` (mini-PC) avec le VPS.
- Confondre `node_id`, `display_name` et `hostname`.

---

# JOURNAL DES SESSIONS

| Date | Résumé | Décisions | Commit(s) | SHA | Résultat |
|------|--------|-----------|-----------|-----|----------|
| 2026-07-31 | Clôture officielle EPIC-003 | EPIC-003 TERMINÉ ; assistant MS disponible | hestia-docs | *(ce commit)* | EPIC-003 clos |
| 2026-07-31 | EPIC-003 A→D (code hestia) | F-013→F-017 UX-003 | hestia | `499e535`…`b93d209` | TERMINÉ |
| 2026-07-31 | Clôture officielle EPIC-002 | EPIC-002 TERMINÉ ; SoT équipements opérationnelle | hestia-docs | `a1b3ebd` | EPIC-002 clos |
| 2026-07-31 | EPIC-002 A→D (code hestia) | F-007→F-012 Module 70 | hestia | `153dff4`…`c73a7d5` | TERMINÉ |
| 2026-07-31 | Clôture officielle UI-002 | UI-002 CLÔTURÉ ; aucun chantier actif | hestia-docs | `5926c82` | UI-002 clos |
| 2026-07-31 | UI-002 A→D (code hestia) | Consolidation ergonomique admin nœuds | hestia | `abff25c`…`f7c481d` | CLÔTURÉ |
| 2026-07-30 | Clôture doc UI-001 + backlog UI-002 | UI-001 VALIDÉ AVEC RÉSERVES ; réserves → UI-002 | hestia-docs | *(ce commit)* | UI-001 clos |
| 2026-07-30 | UI-001 B→G (code hestia) | Refonte ergonomique admin nœuds | hestia | `af1dae4`…`aa6f3ad` | VALIDÉ AVEC RÉSERVES |
| 2026-07-29 | Clôture AUTO-002 (doc) + sync 4 dépôts | AUTO-002 terminé ; UI-001 suivant | hestia-docs | `702daeb` (`aa40e2d`) | A→F clos |
| 2026-07-29 | AUTO-002F F2→F6 (code) | Token par nœud ; retrait token global | hestia / agent / installer | `24945e2` / `a749a3f` / `0b7d002` | Modèle identité définitif |
| 2026-07-29 | F1 + ADR-021 | Conception + décision Acceptée | hestia-docs | (inclus clôture) | Prérequis F2 |
| 2026-07-28 (soir) | Clôture AUTO-002E terrain | État stable ; UI-001 noté | hestia / agent / docs | `6a451f9` / `cc9e315` / `78e057f` | Terminal + reconnexion OK |
| 2026-07-28 | AUTO-002E livré + 1ʳᵉ validation VPS | ADR-023 ; relay systemd+Apache | hestia/agent/installer/docs | `526bbb7` / `3836bde` / `c7460ba` | Relay+WSS+API OK |
| 2026-07-28 | Continuité IA officialisée | PROJECT-STATE + PROMPT-REPRISE | gouvernance | `28222a5` | OK |
| 2026-07-28 | AUTO-002A inventaire nœud | Registre + API admin | hestia | `ec501f4` | 10+11+11 tests API OK |
