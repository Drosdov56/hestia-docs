# AUTO-002 — Supervision et administration des nœuds Hestia

| Attribut | Valeur |
|----------|--------|
| **Lot** | AUTO-002 |
| **Spécification d’architecture (normative)** | [`docs/architecture/AUTO-002-supervision-administration-noeuds.md`](../../architecture/AUTO-002-supervision-administration-noeuds.md) |
| **Identité nœuds (002F)** | [`AUTO-002F-identite-cycle-vie-noeud.md`](../../architecture/AUTO-002F-identite-cycle-vie-noeud.md) · [`ADR-021`](../../adr/ADR-021-identite-credentials-noeud.md) |
| **Statut** | **CLÔTURÉ** (2026-07-29) — 002A→002F terminés |
| **Prérequis** | AUTO-001 (A→F) terminé |
| **Dépôts** | `hestia`, `hestia-agent`, `hestia-installer`, `hestia-docs` |
| **Alignement** | EPIC-011 (administration distante) |

> Ce fichier est le **suivi d’exécution**. Le comportement attendu est défini dans les spécifications ci-dessus.

## 1. Objectif

Permettre à un administrateur d’exploiter un parc de mini-PC Hestia **via le serveur**, sans SSH nominal : inventaire, tableau de bord, observabilité, diagnostics, administration distante, cycle de vie et identité par nœud.

## 2. Sous-modules

| Lot | Intitulé | Statut |
|-----|----------|--------|
| AUTO-002A | Inventaire du nœud | **Fait** |
| AUTO-002B | Tableau de bord | **Fait** |
| AUTO-002C | Observabilité | **Fait** |
| AUTO-002D | Diagnostics | **Fait** |
| AUTO-002E | Administration distante | **Fait / validé terrain** (2026-07-28 soir) |
| AUTO-002F | Cycle de vie / identité du nœud | **Fait** (F1→F6, 2026-07-29) |

## 3. Ordre réalisé (rappel)

```text
002A → 002C + 002B → 002E-1 + 002D → 002E-2 → 002F (F1…F6)
```

## 4. ADR

| ADR | Statut | Rôle |
|-----|--------|------|
| **ADR-021** | **Accepté** | Identité / credentials nœud (`node_id` permanent, token par nœud, hash-only) |
| **ADR-023** | **Accepté** | Terminal WS sortant ; relay = transport (hors identité) |
| ADR-022 / 024 / 025 | Prévus / hors clôture 002 | Non bloquants pour clôture AUTO-002 |

## 4bis. Découpage AUTO-002F — terminé

| Phase | Intitulé | Statut | Dépôt principal |
|-------|----------|--------|-----------------|
| F1 | Conception identité / lifecycle | **Fait** | hestia-docs |
| — | ADR-021 | **Accepté** | hestia-docs |
| F2 | Auth credential individuel + binding | **Fait** | hestia (`9f46062`) |
| F3 | API / admin identité & credentials | **Fait** | hestia (`213145a`) |
| F4A | Protocole bootstrap serveur | **Fait** | hestia (`e334181`) |
| F4B | Installer bootstrap / pose / replace | **Fait** | hestia-installer (`0b7d002`) |
| F5 | Agent hostname + contrat credential | **Fait** | hestia-agent (`a749a3f`) |
| F6 | Retrait token global + modèle définitif | **Fait** | hestia (`09d3522`) |

### Architecture définitive identité des nœuds

- `node_id` : identité logique **permanente** (autorité serveur).
- `display_name` : label admin **mutable** ; hors auth.
- `hostname` : observation OS **mutable** ; hors auth.
- **Un token actif unique par nœud** ; secret hash-only côté serveur ; clair one-shot à l’émission.
- **Présence** `ONLINE`/`OFFLINE` **orthogonale** au `lifecycle_state`.
- Remplacement matériel = **même `node_id`** + rotation du token.
- Relay WS (**ADR-023**) : aucune logique d’identité / credentials.

### Suppression définitive du token global

- Plus de `ingest.node_token` / dual-mode `MODE_GLOBAL` (F6).
- Auth Agent exclusive : Bearer → `NodeCredential` → `node_id` → lifecycle (`NodeAuthenticator`).
- Conf serveur `ingest` : plus que `require_https` (plus de secret parc).

## 5. Critères de clôture (spec §9) — bilan

| # | Critère | Verdict |
|---|---------|---------|
| 1 | Parc admin sans SSH nominal | **OK** (002A–E terrain + 002F) |
| 2 | Dashboard < 2× période heartbeat | **OK** (002B) |
| 3 | Diag téléchargeable | **OK** (002D) |
| 4 | Token par nœud + révocation | **OK** (002F F2–F6) |
| 5 | Audit trail commandes | **OK** (002E) ; audit credentials F6 |
| 6 | Non-régression AUTO-001 | **OK** (présence / heartbeat adaptés F6) |

## 6. Exécution / validation

| Session | Contenu | Verdict |
|---------|---------|---------|
| 2026-07-28 | ADR-023 + API sessions + ws-relay + Agent + UI | Conception figée ; code livré |
| 2026-07-28 (soir) | Validation terrain E-1+E-2 | **AUTO-002E CLÔTURÉ** (`hestia-bmax`) |
| 2026-07-29 | F1 conception + ADR-021 | Spec + décision Acceptée |
| 2026-07-29 | F2→F6 implémentation (3 dépôts applicatifs) | Auth, admin, bootstrap, Agent, retrait token global |
| 2026-07-29 | Clôture documentaire AUTO-002 | **AUTO-002 CLÔTURÉ** |

### SHA de clôture AUTO-002 (2026-07-29)

| Dépôt | SHA | Notes |
|-------|-----|-------|
| hestia | `24945e2a36e233dc7d80496c9de97f91e17deba4` | Tip main (F6 `09d3522` + scripts VPS `24945e2`) |
| hestia-agent | `a749a3f5edc5039d2492b16955c342334b16f1e3` | F5 tip |
| hestia-installer | `0b7d00299b0ab71e0e145c5ee5301a39c257e9e3` | F4B tip |
| hestia-docs | `aa40e2dbe18e5102c38351e848ae485b26a34aee` | Clôture documentaire AUTO-002 |

### SHA historiques (rappels)

| Jalon | hestia | agent | installer | docs |
|-------|--------|-------|-----------|------|
| AUTO-002E soir | `6a451f9` | `cc9e315` | `c7460ba` | `78e057f` |

## 7. Lien amont / suite

- AUTO-001 clôturé ; G10 cold boot secteur reste un suivi séparé.
- **Prochain chantier produit (backlog) :** **UI-001** — ne pas démarrer sans demande explicite.
- Ne pas rouvrir ADR-021 / ADR-023 ni remettre un token global.
