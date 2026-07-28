# AUTO-002 — Supervision et administration des nœuds Hestia

| Attribut | Valeur |
|----------|--------|
| **Lot** | AUTO-002 |
| **Spécification d’architecture (normative)** | [`docs/architecture/AUTO-002-supervision-administration-noeuds.md`](../../architecture/AUTO-002-supervision-administration-noeuds.md) |
| **Statut** | **Ouvert** — 002A→002E **faits / validés terrain** ; 002F restant |
| **Prérequis** | AUTO-001 (A→F) terminé |
| **Dépôts** | `hestia`, `hestia-agent`, `hestia-docs` |
| **Alignement** | EPIC-011 (administration distante) |

> Ce fichier est le **suivi d’exécution**. Le comportement attendu est défini dans la spécification ci-dessus.

## 1. Objectif

Permettre à un administrateur d’exploiter un parc de mini-PC Hestia **via le serveur**, sans SSH nominal : inventaire, tableau de bord, observabilité, diagnostics, administration distante, cycle de vie.

## 2. Sous-modules

| Lot | Intitulé | Statut |
|-----|----------|--------|
| AUTO-002A | Inventaire du nœud | Fait |
| AUTO-002B | Tableau de bord | Fait |
| AUTO-002C | Observabilité | Fait |
| AUTO-002D | Diagnostics | Fait |
| AUTO-002E | Administration distante | **Fait / validé terrain** (2026-07-28 soir) |
| AUTO-002F | Cycle de vie du nœud | À faire (prochaine session) |

## 3. Ordre de développement recommandé

```text
002F + 002A  →  002C + 002B  →  002E-1 + 002D  →  002E-2/3
```

## 4. ADR prévues

ADR-021 (identité nœud) · ADR-022 (commandes) · **ADR-023 (terminal WS sortant) — Accepté** · ADR-024 (artifacts) · ADR-025 (frontière Agent/métier)

## 5. Critères de clôture (rappel spec §9)

1. Parc 10+ nœuds admin sans SSH nominal.
2. Dashboard < 2× période heartbeat.
3. Diag téléchargeable < 5 min après commande.
4. Token par nœud + révocation.
5. Audit trail commandes.
6. Non-régression AUTO-001.

## 6. Exécution terrain / validation

| Session | Contenu | Verdict |
|---------|---------|---------|
| 2026-07-28 | ADR-023 + API sessions + ws-relay + Agent + UI + ops | Conception figée ; code livré |
| 2026-07-28 (soir) | Validation terrain complète E-1+E-2 + correctifs cycle de vie / SPA / reports | **AUTO-002E CLÔTURÉ** — terminal + reconnexion OK sur `hestia-bmax` |

### SHA de clôture AUTO-002E (soir)

| Dépôt | SHA |
|-------|-----|
| hestia | `6a451f9632ebf020e74d7742db18864c04350200` |
| hestia-agent | `cc9e315b7b3a524f9afcb2629e64f0cefcf6769b` |
| hestia-installer | `c7460ba706fd2f4eda84536576007a8f01b0d369` |
| hestia-docs | `8f037ea0ca9daf5b3500d73f5f201e7fdb7282c6` |

## 7. Lien amont

- AUTO-001 clôturé techniquement ; pilier Résilience : clôture G10 (cold boot secteur) en attente séparée.
- AUTO-001F fournit le canal commandes sûres — base de 002E.
