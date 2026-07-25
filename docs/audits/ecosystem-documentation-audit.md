# Audit documentaire global — écosystème Hestia

**Date :** 2026-07-25  
**Dépôts couverts :** `hestia` · `hestia-installer` · `hestia-agent`  
**Référence conceptuelle :** `hestia/docs/HESTIA - DOCUMENT DE RÉFLEXION ARCHITECTURALE.md` (ci-après **RATIONALE**)  
**Nature :** audit uniquement — **aucune** modification, fusion, dépréciation ou suppression exécutée.

Prérequis de lecture : le RATIONALE est considéré comme **source de vérité conceptuelle** de l’écosystème. Les décisions architecturales qu’il porte **ne sont pas remises en question** ici.

---

## 1. Résumé exécutif

L’écosystème documentaire Hestia est **riche et globalement aligné** sur une même intention (assistant de vie / familial, domotique comme moyen, HA backend technique, Serveur = métier, Apps = présentation). En revanche, la **vérité conceptuelle est aujourd’hui fragmentée** entre deux « stacks » :

| Stack | Lieu | Contenu typique |
|-------|------|-----------------|
| **A — RATIONALE** | `hestia/docs/` | Cycle Observation → … → Mémoire ; invariants IF-001…012 ; responsabilités HA / Agent / Serveur / Apps ; gouvernance pré-ADR |
| **B — Modèles Installer** | `hestia-installer/docs/` | `FUNCTIONAL-VISION`, `ARCHITECTURE-CONCEPTUELLE`, `MODELE-INFORMATION`, `MODELE-DECISION`, `MODELE-IDENTITE`, `MODELE-HABITAT`, `MODELE-FOYER`, Module 70 |

**Constat central :** le stack B **redéveloppe** une grande partie du raisonnement du RATIONALE (philosophie, transformation de données, décision, place de l’IA, HA vs métier) avec un **vocabulaire parallèle** (« information utile », chaînes Capteur→…→Apps, modèles Habitat/Foyer) et sans renvoi systématique au RATIONALE. Ce n’est pas forcément contradictoire, mais cela empêche d’avoir **une seule SoT par concept**.

**hestia-agent** est documentairement **minimal** et **conforme** au RATIONALE pour la V1 (infra only, Backend = SoT métier, pas de logique métier) — recouvrement faible, pas de doublon philosophique.

**Priorité de consolidation (à valider, non exécutée) :**

1. Ancrer tous les docs « pourquoi » sur le RATIONALE.  
2. Positionner le stack Installer MODELE-* comme **spécialisations / déclinaisons**, pas comme secondes fondations.  
3. Garder ADR-020 / architecture-domotique / ecosysteme comme **décisions et cartes techniques** avec renvois.  
4. Traiter brief / architecture-reference-v1 comme **historiques**.

---

## 2. Cartographie documentaire des trois dépôts

### 2.1 `hestia` (`docs/`)

```text
docs/
├── HESTIA - DOCUMENT DE RÉFLEXION ARCHITECTURALE.md   ← RÉFÉRENCE CONCEPTUELLE
├── ecosysteme.md                                      ← carte multi-dépôts
├── architecture.md                                    ← couches applicatives dépôt
├── CONTEXTE-PROJET.md                                 ← contexte / stack
├── getting-started.md · deployment.md                 ← guides
├── adr/                                               ← ADR produit (014–020…)
├── produit/                                           ← specs / historique / catalogue
└── reports/                                           ← audits (dont le présent)
```

### 2.2 `hestia-installer` (`docs/`)

```text
docs/
├── FUNCTIONAL-VISION.md                               ← vision « comment ça marche »
├── ARCHITECTURE.md                                    ← architecture Installer (modules)
├── ROADMAP · BACKLOG · INDEX · INSTALL · …            ← pilotage / ops
├── ADR/                                               ← ADR Installer (001–008)
├── conception/
│   ├── ARCHITECTURE-CONCEPTUELLE.md                   ← carte des MODELE-*
│   ├── MODELE-INFORMATION · DECISION · IDENTITE
│   ├── MODELE-HABITAT · FOYER
│   ├── 70-cycle-vie-equipements.md                    ← SoT équipement
│   └── capteurs/ (MODELE-CAPTEUR, SNZB-06P24)
└── implementation/ · audit/ · releases/               ← preuves / plans
```

### 2.3 `hestia-agent`

```text
README.md
docs/ARCHITECTURE.md                                   ← contrat V1 infra daemon
```

### 2.4 Carte des rôles documentaires (écosystème)

```text
                    RATIONALE (hestia)
                   (pourquoi / invariants)
                            │
          ┌─────────────────┼─────────────────┐
          ▼                 ▼                 ▼
   ADR produit        FUNCTIONAL-VISION    MODELE-* Installer
   (hestia)           + ARCHI-CONCEPT.     (déclinaisons)
          │                 │                 │
          ▼                 ▼                 ▼
   architecture-      Module 70 / ADR      Agent ARCHITECTURE
   domotique /        Installer            (contrat V1)
   ecosysteme
```

---

## 3. Tableau des recouvrements

Légende recouvrement : **nul** · **faible** · **moyen** · **important** · **quasi total**

### Documents à fort enjeu (liés au RATIONALE)

| Dépôt | Chemin | Statut | Recouvrement | Nature dominante | Recommandation |
|-------|--------|--------|--------------|------------------|----------------|
| hestia | `docs/HESTIA - DOCUMENT DE RÉFLEXION ARCHITECTURALE.md` | Référence active | — | Référence | **Conserver tel quel** |
| hestia | `docs/adr/ADR-020 - Positionnement…Home Assistant.md` | ADR accepté | **Important** | Redondant + complémentaire (forme ADR) | **Conserver avec renvoi** |
| hestia | `docs/produit/architecture-domotique.md` | Spec active | **Moyen→important** | Complémentaire + redondant (philosophie) | **Conserver avec renvoi** |
| hestia | `docs/ecosysteme.md` | Référence multi-dépôts | **Moyen** | Complémentaire | **Conserver avec renvoi** |
| hestia | `docs/architecture.md` | Spec dépôt | **Moyen** | Complémentaire | **Conserver avec renvoi** |
| hestia | `docs/CONTEXTE-PROJET.md` | Contexte actif | **Faible→moyen** | Complémentaire | **Conserver avec renvoi** |
| hestia | `docs/produit/architecture-reference-v1.md` | Historique | **Moyen** | Historique / partiellement obsolète | **Déprécier** / **Archiver** |
| hestia | `docs/produit/brief-demarrage-hub-familial.md` | Historique | **Faible→moyen** | Historique | **Déprécier** / **Archiver** |
| hestia | `docs/produit/registre-adr.md` | Registre | **Faible→moyen** | Complémentaire | **Conserver avec renvoi** |
| hestia | `docs/adr/ADR-018-…agent-passerelle.md` | ADR accepté | **Faible→moyen** | Complémentaire | **Conserver avec renvoi** |
| installer | `docs/FUNCTIONAL-VISION.md` | Référence vision | **Important** | Redondant (philosophie) + complémentaire (PoC/roadmap locale) | **Conserver avec renvoi** ; **fusion ultérieure envisageable** (philosophie) |
| installer | `docs/conception/ARCHITECTURE-CONCEPTUELLE.md` | Carte modèles | **Important** | Redondant (philosophie) + complémentaire (navigation MODELE-*) | **Conserver avec renvoi** ; **fusion ultérieure envisageable** avec RATIONALE pour le « pourquoi » |
| installer | `docs/conception/MODELE-INFORMATION.md` | Réf. conceptuelle | **Important** | Redondant (cycle info) + complémentaire (typologie) | **Conserver avec renvoi** ; **fusion ultérieure envisageable** |
| installer | `docs/conception/MODELE-DECISION.md` | Réf. conceptuelle | **Important** | Redondant (décision / confiance / IA) | **Conserver avec renvoi** ; **fusion ultérieure envisageable** |
| installer | `docs/conception/MODELE-IDENTITE.md` | Réf. conceptuelle | **Moyen** | Complémentaire (identité personne) | **Conserver avec renvoi** |
| installer | `docs/conception/MODELE-HABITAT.md` | Réf. conceptuelle | **Faible→moyen** | Complémentaire | **Conserver avec renvoi** |
| installer | `docs/conception/MODELE-FOYER.md` | Réf. conceptuelle | **Faible→moyen** | Complémentaire | **Conserver avec renvoi** |
| installer | `docs/conception/70-cycle-vie-equipements.md` | Spec normative gelée | **Faible** | Complémentaire (équipement) | **Conserver tel quel** (SoT équipement) |
| installer | `docs/conception/capteurs/MODELE-CAPTEUR.md` | Contrat doc | **Faible** | Complémentaire | **Conserver tel quel** |
| installer | `docs/ADR/ADR-004…` / `ADR-005…` | ADR Installer | **Faible→moyen** | Complémentaire | **Conserver avec renvoi** (cadre RATIONALE) |
| installer | `docs/ARCHITECTURE.md` | Archi Installer | **Nul→faible** | Complémentaire (modules install) | **Conserver tel quel** |
| agent | `docs/ARCHITECTURE.md` | Contrat V1 | **Faible** | Complémentaire | **Conserver avec renvoi** |
| agent | `README.md` | Entrée dépôt | **Faible** | Complémentaire | **Conserver tel quel** |

### Documents à recouvrement faible / nul (hors consolidation conceptuelle)

Guides ops, plans, releases, INT-001 plans/rapports, L8, CHANGELOG, WORKFLOW, INSTALL, ROADMAP/BACKLOG (pilotage), SNZB-06P24 (référentiel matériel), ADR techniques Installer (001, 002, 003 réseau, 006 MQTT, 007, 008), ADR hestia UI/tests/permissions (014–017, 019), specs UI/notifications, catalogue, backlog produit, getting-started, deployment.

→ Recommandation dominante : **conserver tel quel**.

---

## 4. Analyse détaillée (documents à enjeu)

### 4.1 Référence — RATIONALE (`hestia`)

**Rôle :** pourquoi Hestia est conçu ainsi ; cycle cognitif ; invariants ; décisions ; épreuves ; gouvernance.  
**Statut :** document fondateur, validé sur le fond.  
**Recouvrement :** référence.  
**Recommandation :** **conserver tel quel**.

---

### 4.2 `hestia` — ADR-020

**Rôle :** décision acceptée HA vs Hestia.  
**Recouvrement :** **important** (finalité, 4 acteurs, HA remplaçable, UI).  
**Nature :** redondant sur le fond du RATIONALE ; complémentaire comme ADR court.  
**Recommandation :** **conserver avec renvoi**.

### 4.3 `hestia` — architecture-domotique / ecosysteme / architecture / CONTEXTE

**Rôle :** flux domotique, carte dépôts, couches applicatives, contexte.  
**Recouvrement :** moyen (philosophie + chaîne acteurs).  
**Nature :** complémentaire sur le « où / comment » ; redondant sur le « pourquoi ».  
**Recommandation :** **conserver avec renvoi**.

### 4.4 `hestia` — historiques (brief, architecture-reference-v1)

**Nature :** historique / partiellement obsolète (hub, modules catalogue comme centre).  
**Recommandation :** **déprécier** puis **archiver** (après validation).

### 4.5 `hestia-installer` — FUNCTIONAL-VISION

**Rôle :** « Comment fonctionne Hestia ? » côté Installer / vision produit.  
**Recouvrement :** **important** — assistant de vie, anti-UI-HA, événement → information utile, IA transversale, acteurs, PoC.  
**Nature :** **redondant** avec RATIONALE + ADR-020 sur la philosophie ; **complémentaire** pour le cadrage PoC / phases Installer.  
**Recommandation :** **conserver avec renvoi** ; **fusion ultérieure envisageable** de la seule partie philosophique.

### 4.6 `hestia-installer` — ARCHITECTURE-CONCEPTUELLE + MODELE-INFORMATION + MODELE-DECISION

**Rôle :** carte et modèles du cycle information / décision.  
**Recouvrement :** **important** — quasi parallèle au cycle RATIONALE (observation/connaissance/interprétation/décision) et à la place de l’IA.  
**Nature :** **redondant** au niveau fondateur ; **complémentaire** si repositionnés comme **spécifications dérivées** (typologies, chaînes détaillées, habitat/foyer).  
**Risque :** seconde « constitution » conceptuelle sans lien au RATIONALE.  
**Recommandation :** **conserver avec renvoi** obligatoire vers RATIONALE ; **fusion ultérieure envisageable** pour unifier les chaînes lexicales.

### 4.7 `hestia-installer` — MODELE-IDENTITE / HABITAT / FOYER

**Recouvrement :** moyen / faible-moyen.  
**Nature :** **complémentaires** (sous-domaines absents ou seulement esquissés dans le RATIONALE).  
**Recommandation :** **conserver avec renvoi** (cadrage : spécialisations sous RATIONALE).

### 4.8 `hestia-installer` — Module 70 / ADR-005 / ADR-004

**Rôle :** SoT **équipement** (identité, états, bindings).  
**Recouvrement :** faible avec le RATIONALE (le RATIONALE parle peu du cycle de vie équipement).  
**Nature :** **complémentaire** — bonne séparation SoT.  
**Recommandation :** **conserver tel quel**.

### 4.9 `hestia-agent` — ARCHITECTURE.md / README

**Rôle :** contrat daemon V1.  
**Recouvrement :** faible — affirme Backend = SoT métier, pas de logique métier V1.  
**Nature :** **complémentaire** et aligné.  
**Recommandation :** **conserver avec renvoi** (une ligne vers RATIONALE + ADR-020 pour le rôle cible Agent au-delà de la V1).

---

## 5. Concepts dupliqués

| Concept | Présent dans | Commentaire |
|---------|--------------|-------------|
| Hestia ≠ plateforme / app domotique | RATIONALE, ADR-020, ecosysteme, architecture-domotique, FUNCTIONAL-VISION, ARCHITECTURE-CONCEPTUELLE, historiques | Définition **tripliquée** (hestia + installer + historiques) |
| HA = backend technique / « que se passe-t-il ? » | RATIONALE, ADR-020, ecosysteme, architecture-domotique, FUNCTIONAL-VISION | Même idée, formulations proches |
| Serveur = métier / signification | RATIONALE, ADR-020, architecture.md | Aligné |
| Agent = normalisation / pas métier familial | RATIONALE, ADR-020, architecture-domotique, agent ARCHITECTURE (V1 plus strict) | Aligné avec nuance V1 |
| Apps = présentation, pas interprétation | RATIONALE, ADR-020, architecture-domotique | Aligné |
| Cycle observation → … → décision / info utile | RATIONALE ; MODELE-INFORMATION ; MODELE-DECISION ; FUNCTIONAL-VISION § | **Double pipeline conceptuel** (vocabulaire différent) |
| Mémoire = choix / pas tout stocker | RATIONALE (mémoire technique vs utile) ; MODELE-INFORMATION (durée / sélection) ; SNZB §6 | Idée partagée, SoT claire = RATIONALE |
| IA perception vs compréhension | RATIONALE ; MODELE-INFORMATION / DECISION (IA consomme, ne définit pas) | Compatible ; à rattacher au RATIONALE |
| HA remplaçable | RATIONALE, ADR-020 | Dupliqué utilement (ADR) |
| Source of Truth métier = Serveur / Backend | RATIONALE ; Module 70 ; agent ARCHITECTURE | Aligné ; vocabulaire Serveur vs Backend |
| Identité équipement | architecture-domotique (UUID) ; Module 70 (`hestia_device_id`) | Dupliqué **entre dépôts** avec lexique dual |

---

## 6. Divergences terminologiques

| Notion | RATIONALE (`hestia`) | Ailleurs | Risque |
|--------|----------------------|----------|--------|
| Finalité produit | Assistant de vie | « Assistant familial contextuel » (hestia ADR/ecosysteme) ; « assistant de vie » (Installer) | Faible — synonymes |
| Pipeline cognitif | Observation → Connaissance → Interprétation → Décision → Action → Mémoire | Capteur → Signal → HA → Entités → Sélection → Modèle → Moteur → Info utile → Apps (MODELE-INFORMATION) | **Moyen** — deux grammaires |
| Sortie humaine | Connaissance / interprétation / information mémorisée | « Information utile » (FUNCTIONAL-VISION, MODELE-*) | Moyen — à mapper explicitement |
| Cœur métier | Serveur Hestia | Backend Hestia (Module 70, agent) ; API/`core` (architecture.md) | Faible si glossaire |
| Identité équipement | Peu détaillée | UUID (architecture-domotique) vs `hestia_device_id` (Module 70) | **Moyen** |
| Agent | Runtime local + réactions immédiates possibles | V1 agent = idle infra only ; ADR-020 « aucune logique métier » | Faible si niveaux V1 vs cible |
| Invariants | IF-001…IF-012 nommés | « Principes » / « règles non négociables » Installer (modules) | Faible — scopes différents |
| Document d’entrée conceptuelle | RATIONALE | ARCHITECTURE-CONCEPTUELLE + FUNCTIONAL-VISION | **Important** — concurrence de porte d’entrée |

---

## 7. Contradictions éventuelles

Aucune contradiction **qui invaliderait** le RATIONALE n’a été trouvée. Tensions à clarifier lors de la consolidation :

| ID | Tension | Documents | Lecture proposée (non normative ici) |
|----|---------|-----------|--------------------------------------|
| C1 | Deux constitutions conceptuelles | RATIONALE vs MODELE-* + ARCHITECTURE-CONCEPTUELLE | Stack Installer = dérivé ; RATIONALE = SoT |
| C2 | Agent sans métier vs réactions locales | RATIONALE · ADR-020 · agent V1 | Distinguer réaction technique locale / interprétation familiale |
| C3 | UUID vs `hestia_device_id` | architecture-domotique · Module 70 | Harmonisation lexicale (hors remise en cause RATIONALE) |
| C4 | « Hub / modules » historiques vs cycle cognitif | brief · architecture-reference-v1 · RATIONALE | Historiques non normatifs |
| C5 | FUNCTIONAL-VISION « référence fonctionnelle du projet » vs RATIONALE « niveau le plus élevé » | Installer · hestia | Concurrence de titre — à trancher par gouvernance doc |

---

## 8. Documents historiques

| Document | Dépôt | Traitement recommandé (après validation) |
|----------|-------|------------------------------------------|
| `produit/brief-demarrage-hub-familial.md` | hestia | **Déprécier** → **Archiver** |
| `produit/architecture-reference-v1.md` | hestia | **Déprécier** → **Archiver** |
| Plans L4/L5/L6, rapports INT clôturés | installer | **Archiver** (preuves) — hors RATIONALE |
| `docs/reports/architecture-rationale-audit.md` | hestia | Audit partiel antérieur — **conserver** comme archive d’audit |

---

## 9. Recommandations

Catégories autorisées uniquement. **Aucune exécution.**

### 9.1 Gouvernance documentaire

| Action | Recommandation |
|--------|----------------|
| SoT conceptuelle unique | **Conserver tel quel** le RATIONALE ; en faire la cible de tous les renvois « pourquoi » |
| Porte d’entrée Installer | ARCHITECTURE-CONCEPTUELLE / FUNCTIONAL-VISION : **conserver avec renvoi** obligatoire vers RATIONALE ; **fusion ultérieure envisageable** des intros philosophiques |
| SoT équipement | Module 70 + ADR-005 : **conserver tel quel** |
| SoT multi-dépôts ops | ecosysteme.md : **conserver avec renvoi** |
| Historiques hub | **Déprécier** puis **archiver** |

### 9.2 Documents qui devraient surtout renvoyer vers le RATIONALE

(après validation manuelle — **ne pas créer les liens automatiquement dans cette mission**)

- `hestia/docs/adr/ADR-020…`
- `hestia/docs/produit/architecture-domotique.md` (section philosophie)
- `hestia/docs/ecosysteme.md` §1
- `hestia/docs/architecture.md` (intro)
- `hestia/docs/CONTEXTE-PROJET.md` §1
- `hestia-installer/docs/FUNCTIONAL-VISION.md` §1–2
- `hestia-installer/docs/conception/ARCHITECTURE-CONCEPTUELLE.md` §2
- `hestia-installer/docs/conception/MODELE-INFORMATION.md` / `MODELE-DECISION.md` (préambule)
- `hestia-agent/docs/ARCHITECTURE.md` (rôle cible vs V1)

### 9.3 Plan de consolidation suggéré (non exécuté)

1. Valider le RATIONALE comme **unique constitution** écosystème.  
2. Ajouter renvois (mission séparée).  
3. Glossaire unique : Observation/Connaissance/… ↔ Information utile / chaînes MODELE-*.  
4. Repositionner MODELE-* comme **annexes spécialisées** (habitat, foyer, identité personne, info, décision).  
5. Déprécier / archiver brief + architecture-reference-v1.  
6. Harmoniser UUID / `hestia_device_id` (mission dédiée).  
7. Ne **supprimer** aucun fichier sans validation explicite ultérieure.

### 9.4 Ce que cet audit n’est pas

- Une remise en cause des invariants du RATIONALE.  
- Une exécution de nettoyage.  
- Une fusion des MODELE-* dans le RATIONALE.

---

## 10. Conformité mission

| Exigence | Statut |
|----------|--------|
| Trois dépôts explorés | Oui |
| RATIONALE = référence | Oui |
| Aucune modification hors ce rapport | Oui (seul livrable créé) |
| Pas de suppression / fusion / ADR modifié | Oui |
| Recommandations limitées aux 5 catégories | Oui |

---

*Fin du rapport — audit documentaire global de l’écosystème Hestia.*
