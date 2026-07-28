# AUTO-002 — Supervision et administration des nœuds Hestia

| Attribut | Valeur |
|----------|--------|
| **Lot** | AUTO-002 |
| **Spécification d’architecture (normative)** | [`docs/architecture/AUTO-002-supervision-administration-noeuds.md`](../../architecture/AUTO-002-supervision-administration-noeuds.md) |
| **Statut** | **Ouvert** — conception livrée ; implémentation non démarrée |
| **Prérequis** | AUTO-001 (A→F) terminé |
| **Dépôts** | `hestia`, `hestia-agent`, `hestia-docs` |
| **Alignement** | EPIC-011 (administration distante) |

> Ce fichier est le **suivi d’exécution**. Le comportement attendu est défini dans la spécification ci-dessus.

## 1. Objectif

Permettre à un administrateur d’exploiter un parc de mini-PC Hestia **via le serveur**, sans SSH nominal : inventaire, tableau de bord, observabilité, diagnostics, administration distante, cycle de vie.

## 2. Sous-modules

| Lot | Intitulé | Statut |
|-----|----------|--------|
| AUTO-002A | Inventaire du nœud | À faire |
| AUTO-002B | Tableau de bord | À faire |
| AUTO-002C | Observabilité | À faire |
| AUTO-002D | Diagnostics | À faire |
| AUTO-002E | Administration distante | À faire |
| AUTO-002F | Cycle de vie du nœud | À faire |

## 3. Ordre de développement recommandé

```text
002F + 002A  →  002C + 002B  →  002E-1 + 002D  →  002E-2/3
```

## 4. ADR prévues

ADR-021 (identité nœud) · ADR-022 (commandes) · ADR-023 (terminal WS sortant) · ADR-024 (artifacts) · ADR-025 (frontière Agent/métier)

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
| — | Aucune validation implémentation à ce stade | — |

## 7. Lien amont

- AUTO-001 clôturé techniquement ; pilier Résilience : clôture G10 (cold boot secteur) en attente séparée.
- AUTO-001F fournit le canal commandes sûres — base de 002E.
