# Rapport final — Migration documentaire vers hestia-docs

> **Rapport historique de migration.**  
> Ce document clôture la migration initiale vers `hestia-docs`. La gouvernance et l’état courant doivent désormais être lus dans `docs/INDEX.md`, `docs/backlog/` et `docs/gouvernance/`.

**Date de clôture :** 2026-07-25

---

## STATUT

**MIGRATION DOCUMENTAIRE :** TERMINÉE

**ARCHITECTURE DOCUMENTAIRE :** GELÉE à la date de migration

**SOURCE DE VÉRITÉ :** hestia-docs  
https://github.com/Drosdov56/hestia-docs

**Gouvernance :** [DECISION-0001-DOCUMENTATION.md](docs/gouvernance/DECISION-0001-DOCUMENTATION.md)

Aucune action restante **dans le chantier de migration initial**.

---

## 1. Résultat

| Cible | Statut |
|-------|--------|
| `hestia-docs` = SoT documentaire transverse | Atteint |
| `hestia` = doc applicative locale + stubs | Atteint |
| `hestia-installer` = doc installeur + stubs | Atteint |
| `hestia-agent` = doc agent + renvoi constitution | Atteint |
| Terminologie `hestia_device_id` | Alignée |
| Dépôt GitHub `Drosdov56/hestia-docs` | Créé (`origin` configuré) |
| DECISION-0001 | Publiée |

---

## 2. Documents déplacés vers hestia-docs

### Depuis hestia

Constitution ; ecosysteme ; architecture-domotique ; ADR-020 ; ADR-018 ; archives (brief, architecture-reference-v1) ; audits.

### Depuis hestia-installer

FUNCTIONAL-VISION ; ARCHITECTURE-CONCEPTUELLE ; MODELE-* ; MODELE-CAPTEUR ; SNZB-06P24 ; Module 70 ; ADR-004 ; ADR-005.

### Créé dans hestia-docs

README ; INDEX ; GLOSSAIRE ; CLASSIFICATION-DEFINITIVE ; DECISION-0001 ; PROVENANCE ; ce rapport.

---

## 3. Harmonisation

- Niveaux documentaires figés (Constitution → décisions → cartes → modèles → vision → archive).
- Intros recentrées sur Constitution / glossaire.
- Identifiant métier équipement : **`hestia_device_id`** (terme officiel unique).
- Stubs et index locaux pointent vers https://github.com/Drosdov56/hestia-docs.

---

## 4. Documentation locale conservée

- **hestia** : architecture applicative, CONTEXTE, guides, ADR UI/permissions, specs produit locales.
- **hestia-installer** : ARCHITECTURE modules, INSTALL, ROADMAP, BACKLOG, ADR locaux 001–003/006–008, implementation, releases.
- **hestia-agent** : contrat V1 daemon.

---

## 5. Clôture

Le chantier documentaire est **définitivement clos**.  
L’architecture documentaire est **gelée** par DECISION-0001.
