# Roadmap d’implémentation — Hestia

**Dérivé de :** FUNCTIONAL-VISION §8–15 · ADR-004/005/018/020 · Module 70 · modèles conceptuels · architecture-domotique  
**Principe :** fondations → valeur PoC → administration → modèles métier → enrichissements → horizons

---

## Phases

| Phase | Nom | Epics | Objectif |
|-------|-----|-------|----------|
| **P0** | Contrat & Agent métier | EPIC-001 | Brancher Agent → Backend (observation, sync) |
| **P1** | SoT équipements | EPIC-002 | Persister le modèle Module 70 côté Serveur |
| **P2** | Mise en service Admin | EPIC-003 | UX-003 — Hestia Admin |
| **P3** | Preuve de valeur (PoC) | EPIC-004 | Un événement → une information utile (+ notif optionnelle) |
| **P4** | Hub & exploitation | EPIC-005 | Affichage / consultation utilisateurs |
| **P5** | Pipeline information complet | EPIC-006 | MODELE-INFORMATION & Mémoire |
| **P6** | Décision | EPIC-007 | MODELE-DECISION |
| **P7** | Contexte spatial & social | EPIC-008, EPIC-009 | Habitat, Foyer, Identité personnes |
| **P8** | Corrélation & capteurs | EPIC-010, EPIC-012 | Multi-signaux, caméras, référentiels |
| **P9** | Résilience | EPIC-011 | Offline, file sync, admin distant |
| **P10** | IA & horizons | EPIC-013, EPIC-014 | IA transversale ; multi-logement (hors v1) |

---

## Ordre strict recommandé

```text
EPIC-001 → EPIC-002 → EPIC-003
              ↘
               EPIC-004  →  EPIC-005
                              ↓
                         EPIC-006 → EPIC-007
                              ↓
                    EPIC-008 ∥ EPIC-009
                              ↓
                         EPIC-010 → EPIC-012
                              ↓
                         EPIC-011
                              ↓
                    EPIC-013 → EPIC-014 (hors v1)
```

### Justifications (FUNCTIONAL-VISION §15)

1. S’appuyer sur le nœud déjà vivant.  
2. Chemin minimal Agent → Backend → Hub pour **un** type d’événement.  
3. Produire **une** information claire + notification optionnelle.  
4. Reporter Admin complet / multi-acteurs / IA **après** validation de la valeur — **nuance :** UX-003 (EPIC-003) est requis pour un parc métier cohérent ; le PoC (EPIC-004) peut démarrer avec un équipement déjà connu / provisionné manuellement si besoin, mais la cible normative reste Admin.

---

## Jalons de validation

| Jalon | Critère de succès | Source | Statut |
|-------|-------------------|--------|--------|
| J1 | Agent envoie un événement normalisé au Backend | ADR-020, architecture-domotique §11 | **Atteint** (EPIC-001 clôturée 2026-07-25) |
| J2 | Fiche `Equipment` + `hestia_device_id` persistés | Module 70, ADR-005 | — |
| J3 | Parcours Admin : détecté → actif (un capteur) | ADR-004, UX-003 | — |
| J4 | PoC : événement réel → information utile affichée | FUNCTIONAL-VISION §15 | — |
| J5 | Notification éventuelle reçue | FUNCTIONAL-VISION §12, §15 | — |
| J6 | Décision explicable sur informations utiles | MODELE-DECISION | — |
| J7 | Habitat + foyer structurés sans dépendre des capteurs | MODELE-HABITAT, MODELE-FOYER | — |

**Stabilisation terrain J1 (hors nouveau jalon) :** **clôturée** (2026-07-27) — cold boot / reboot / OPS — preuves dans `execution/AUTO-001.md` ; résumé Agent : `hestia-agent/docs/REPORT-J1-DEPLOY-HEALTH.md`. Enchaînement produit **J1 → J2** inchangé.

**AUTO-001 (présence nœud) :** **TERMINÉE / VALIDÉE** (2026-07-27, BMAX) — voir `execution/AUTO-001.md` (source de vérité).

**AUTO-002 (supervision / admin / identité nœuds) :** **TERMINÉE / CLÔTURÉE** (2026-07-29) — A→F ; voir `execution/AUTO-002.md`. Token global retiré ; ADR-021 + ADR-023.

**UI-001 (refonte ergonomique admin nœuds) :** **TERMINÉE** (2026-07-30) — **VALIDÉE AVEC RÉSERVES** (Tech Committee). Lots B→G sur `hestia` (`af1dae4`…`aa6f3ad`). Réserves → **UI-002** ; pas de réouverture.

---

## Epics / chantiers UI (backlog)

| ID | Intitulé | Statut | Notes |
|----|----------|--------|-------|
| **UI-001** | Refonte ergonomique de l’administration des nœuds | **Livré** | Parc cartes · fiche onglets · synthèse · pilotage · identité · observabilité. Tip `aa6f3ad`. |
| **UI-002** | Consolidation ergonomique de l’administration des nœuds | **À faire** | Lots indépendants issus des décisions **ACCEPTÉES** du Tech Committee UI-001. **Ne pas démarrer sans demande explicite.** |

### UI-002 — périmètre synthétique (ACCEPTÉES uniquement)

| Lot | Priorité | Contenu |
|-----|----------|---------|
| **UX-001** | Haute | Densité Parc ; libellés FR Pilotage ; allègement redondances Synthèse/Identité ; section Administration ; lien fiche |
| **UX-002** | Haute | Dates events ; JSON repliable ; carte Diagnostic Observabilité |
| **A11Y-001** | Moyenne | Navigation clavier / focus onglets fiche |
| **CSS-001** | Basse | Socle CSS cartes nodes ; `.settings-panel` ; nested cards |
| **TECH-001** | Basse | Hygiène JS bornée (code mort, helpers carte) — pas de refactor global |

Hors UI-002 : éléments **REPORTÉS** et **REJETÉS** du Tech Committee (confirmations structurées, split `nodes.js`, tri métier parc, nouvelles actions/API, design system global).

---

## Hors séquence / ne pas anticiper

| Sujet | Raison | Source |
|-------|--------|--------|
| Multi-logement / multi-nœud | Hors v1 | Module 70 §11 ; architecture-domotique §9 |
| Acteurs §4 au-delà de l’admin | Horizons, pas V1 | FUNCTIONAL-VISION §4 |
| Moteur IA livré en V1 | Pas d’exigence PoC | FUNCTIONAL-VISION §6 |
| Produit commercial / multi-clients | Hors document | FUNCTIONAL-VISION §14 |
