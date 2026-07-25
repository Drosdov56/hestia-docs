# Backlog officiel — inventaire

**IMP-000** · Chaque Feature apparaît **une seule fois**.  
Détail et tâches : fichiers `EPIC-NNN.md`.

Légende statut : `À faire` · `Partiel` · `Livré (hors scope)` · `Hors v1`

---

## Synthèse Epics

| Epic | Titre | Phase | Statut |
|------|-------|-------|--------|
| [EPIC-001](EPIC-001.md) | Agent métier — observation & synchronisation | P0 | À faire |
| [EPIC-002](EPIC-002.md) | Backend — SoT équipements (Module 70) | P1 | À faire |
| [EPIC-003](EPIC-003.md) | Hestia Admin — assistant de mise en service | P2 | À faire |
| [EPIC-004](EPIC-004.md) | PoC — événement → information utile | P3 | À faire |
| [EPIC-005](EPIC-005.md) | Hub familial & notifications | P4 | À faire |
| [EPIC-006](EPIC-006.md) | Pipeline information & mémoire | P5 | À faire |
| [EPIC-007](EPIC-007.md) | Décision métier | P6 | À faire |
| [EPIC-008](EPIC-008.md) | Habitat & Foyer | P7 | À faire |
| [EPIC-009](EPIC-009.md) | Identité des personnes | P7 | À faire |
| [EPIC-010](EPIC-010.md) | Corrélation multi-signaux & caméras | P8 | À faire |
| [EPIC-011](EPIC-011.md) | Résilience hors-ligne & administration distante | P9 | À faire |
| [EPIC-012](EPIC-012.md) | Référentiels capteurs | P8 | À faire |
| [EPIC-013](EPIC-013.md) | Intelligence artificielle transversale | P10 | À faire |
| [EPIC-014](EPIC-014.md) | Multi-logement / multi-nœud | — | Hors v1 |

---

## Features (index unique)

| ID | Feature | Epic |
|----|---------|------|
| F-001 | Collecte événements HA / MQTT | EPIC-001 |
| F-002 | Normalisation vers modèle Hestia | EPIC-001 |
| F-003 | Filtrage des informations pertinentes | EPIC-001 |
| F-004 | Sync Agent ↔ Serveur (événements) | EPIC-001 |
| F-005 | Réplique locale Agent | EPIC-001 |
| F-006 | Transitions techniques auto (`active`↔`offline`) | EPIC-001 |
| F-007 | Entité `Equipment` + `hestia_device_id` | EPIC-002 |
| F-008 | Machine d’états Module 70 | EPIC-002 |
| F-009 | Ancre physique & `protocol_bindings` | EPIC-002 |
| F-010 | Nom logique SoT Backend + propagation | EPIC-002 |
| F-011 | Remplacement deux fiches | EPIC-002 |
| F-012 | Reprises (panne, MQTT, réinstall HA/Z2M…) | EPIC-002 |
| F-013 | Checks techniques multi-couches (UX-003) | EPIC-003 |
| F-014 | Rapport de validation fonctionnelle | EPIC-003 |
| F-015 | Collecte infos métier Admin | EPIC-003 |
| F-016 | Admission `detected`→`pending_provisioning` | EPIC-003 |
| F-017 | Appairage / permit-join via Agent | EPIC-003 |
| F-018 | Chaîne minimale Capteur→…→Hub (1 type) | EPIC-004 |
| F-019 | Formulation d’une information utile | EPIC-004 |
| F-020 | Affichage Hub (module home) | EPIC-005 |
| F-021 | Notifications utilisateurs | EPIC-005 |
| F-022 | Sélection / typologie d’informations | EPIC-006 |
| F-023 | Identité d’une information | EPIC-006 |
| F-024 | Mémoire utile (sélection / durée) | EPIC-006 |
| F-025 | Mémoire technique vs utile | EPIC-006 |
| F-026 | Chaîne contexte → corrélation → décision | EPIC-007 |
| F-027 | Trois natures de décisions | EPIC-007 |
| F-028 | Explicabilité des décisions | EPIC-007 |
| F-029 | Structure habitat (pièces, zones…) | EPIC-008 |
| F-030 | Localisation des équipements | EPIC-008 |
| F-031 | Personnes, rôles, relations du foyer | EPIC-008 |
| F-032 | Preuves ≠ identité ≠ décision d’ID | EPIC-009 |
| F-033 | Fusion / confiance d’identification | EPIC-009 |
| F-034 | Corrélation multi-capteurs | EPIC-010 |
| F-035 | Caméras en confirmation | EPIC-010 |
| F-036 | Capteur ouverture porte/fenêtre | EPIC-010 |
| F-037 | File d’attente offline + dédup | EPIC-011 |
| F-038 | Autorité config VPS vs télémétrie nœud | EPIC-011 |
| F-039 | Réinstallation nœud / récup config | EPIC-011 |
| F-040 | Mises à jour à distance du nœud | EPIC-011 |
| F-041 | Contrat documentaire MODELE-CAPTEUR | EPIC-012 |
| F-042 | Exploitation SNZB-06P24 (capacités) | EPIC-012 |
| F-043 | IA perception vs compréhension | EPIC-013 |
| F-044 | Enrichissement modules par IA | EPIC-013 |
| F-045 | Identifiants multi-nœud / multi-logement | EPIC-014 |

---

## Couverture documentaire

| Source hestia-docs | Epics couvertes |
|--------------------|-----------------|
| Constitution | EPIC-004, 006, 007, 011, 013 (invariants / cycle / IA / mémoire) |
| ADR-020 | EPIC-001, 005, 013 |
| ADR-018 | EPIC-001, 005, 010, 011 |
| ADR-004 | EPIC-003 |
| ADR-005 / Module 70 | EPIC-001, 002, 003, 011, 014 |
| architecture-domotique | EPIC-001, 004, 005, 010, 011, 012 |
| FUNCTIONAL-VISION | EPIC-001–005, 013, 014 |
| MODELE-INFORMATION | EPIC-004, 006 |
| MODELE-DECISION | EPIC-007, 010 |
| MODELE-HABITAT / FOYER | EPIC-008 |
| MODELE-IDENTITE | EPIC-009 |
| MODELE-CAPTEUR / SNZB-06P24 | EPIC-012, 004 |
| Glossaire | transversal (vocabulaire) |
| Archive / audits | **aucune** Feature nouvelle |
