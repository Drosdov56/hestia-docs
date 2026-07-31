# EPIC-004 — PoC : événement → information utile

| Attribut | Valeur |
|----------|--------|
| **Phase** | P3 |
| **Statut** | **TERMINÉ / CLÔTURÉ** (2026-07-31) |
| **Dépôts** | `hestia` (Backend + Hub + notifications) ; `hestia-agent` (prérequis émission / hors lots A–D code) |
| **Prérequis** | EPIC-001, EPIC-002 (équipement connu) ; EPIC-003 recommandé |
| **Bloque** | EPIC-005, EPIC-006 |
| **Exécution** | [`execution/EXEC-EPIC-004.md`](execution/EXEC-EPIC-004.md) |
| **Tip code** | `2fd0cce` (`hestia`) |

## Sources

- [FUNCTIONAL-VISION](../vision/FUNCTIONAL-VISION.md) §3, §12, §15 — **cible PoC**
- [MODELE-INFORMATION](../modeles/MODELE-INFORMATION.md)
- [Constitution](../constitution/HESTIA%20-%20DOCUMENT%20DE%20RÉFLEXION%20ARCHITECTURALE.md) — cycle Observation→… ; information utile
- [Glossaire](../gouvernance/GLOSSAIRE.md) — mapping cycle / information utile
- [architecture-domotique.md](../architecture/architecture-domotique.md) §11
- [SNZB-06P24](../modeles/capteurs/SNZB-06P24.md) — scénario matériel de référence

## Objectif

Démontrer le **bénéfice utilisateur** : un événement réel devient **une** information claire pour un humain (notification optionnelle). Pas une démo d’administration.

## Features — implémentées et validées

| Feature | Contenu | Lot | Commit (`hestia`) |
|---------|---------|-----|-------------------|
| **F-018** | Chaîne minimale Capteur → Backend → Hub (+ notif. optionnelle) | A, C, D | `d570e22` · `adeae82` · `2fd0cce` |
| **F-019** | Formulation d’une information utile | B | `41b5e4a` |

### Lots A→D

| Lot | Objet | Commit (`hestia`) |
|-----|-------|-------------------|
| **A** | Ingest métier présence | `d570e2277f491d0f18f46f25fd0cbeea66dff62d` |
| **B** | Formulation information utile | `41b5e4a5a74a5f735fd54c7962f1c5bcbe41753f` |
| **C** | Surface Hub minimale | `adeae820b1f11011d4b2b5475e8a31db9bc189cc` |
| **D** | Notification optionnelle | `2fd0cce0723f1c74a70a64362b949ab25eaa6f49` |

### F-018 — Chaîne minimale Capteur → Hub (1 type) ✅

- Ingest présence rattaché à `hestia_device_id` (SoT)
- Affichage Hub des informations utiles déjà formulées
- Notification optionnelle (in-app enregistrée, sans envoi réel)

### F-019 — Formulation d’une information utile ✅

- Gabarit déterministe (présence) à partir du contexte métier SoT
- Aucun jargon HA / MQTT / Zigbee2MQTT exposé
- Pas de moteur décisionnel ni d’IA

## Résultat fonctionnel

Premier **flux fonctionnel complet** disponible côté `hestia` :

```text
événement métier (présence)
  → information utile (formulation)
  → affichage Hub
  → notification optionnelle
```

## Critères de done Epic — atteints

- Scénario PoC §15 reproductible jusqu’à Hub (+ notification optionnelle) côté Backend / PWA.
- Valeur utilisateur validée avant généralisation Admin multi-équipements / IA (EPIC-005+).
