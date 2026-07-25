# Audit documentaire — recouvrements avec le document de réflexion architecturale

**Date :** 2026-07-25  
**Périmètre :** `C:\Users\web\hestia\docs\` (intégralité)  
**Document de référence analysé :** `docs/HESTIA - DOCUMENT DE RÉFLEXION ARCHITECTURALE.md`  
**Alias demandé dans le brief :** `ARCHITECTURE-RATIONALE.md` (fichier non présent sous ce nom exact)  
**Nature de la mission :** audit uniquement — **aucune** modification, suppression, fusion ou dépréciation exécutée.

---

## 1. Résumé exécutif

Le dossier `docs/` du dépôt `hestia` contient **22 fichiers Markdown**. Le document de réflexion architecturale (ci-après **RATIONALE**) est le niveau conceptuel le plus élevé : cycle Observation → Connaissance → Interprétation → Décision → Action → Mémoire, responsabilités HA / Agent / Serveur / Applications, invariants IF-001…IF-012, décisions structurantes, gouvernance.

**Constats principaux :**

1. **Recouvrement important** avec `adr/ADR-020` (positionnement HA vs Hestia, responsabilités des quatre acteurs) et, dans une moindre mesure, avec `produit/architecture-domotique.md` + `ecosysteme.md` + `architecture.md` (chaîne Capteurs → HA → Agent → Serveur → Apps, philosophie « pas une plateforme domotique »).
2. **Documents historiques** (`brief-demarrage-hub-familial.md`, `architecture-reference-v1.md`) déjà marqués comme non normatifs ; ils recouvrent partiellement la philosophie (simplicité, pérennité, anti-domotique) mais dans un vocabulaire antérieur (« hub », modules catalogue) — **complémentaires / partiellement obsolètes**, pas à supprimer sans validation.
3. **Peu de contradictions frontales** : l’alignement dominant est « Hestia = assistant / HA = backend technique / UI HA ≠ UI utilisateur ». Points de tension à clarifier ultérieurement (voir §4) : vocabulaire UUID équipement vs modèles Installer ; place exacte de la logique métier sur le Serveur vs formulations ADR-018 ; Agent « sans logique métier » vs réactions locales immédiates du RATIONALE.
4. **Documents hors sujet conceptuel** (démarrage, déploiement, UI, permissions, tests, urgence) : recouvrement **nul à faible** — candidats à **conserver tel quel**.
5. **ADR potentiellement impactés** lors d’un futur nettoyage : surtout **ADR-020**, **ADR-018**, et les entrées **ADR-0001 / ADR-0002 / ADR-0003** du registre historique — non pour les invalider, mais pour y **renvoyer explicitement** le RATIONALE comme socle de raisonnement.

**Recommandation globale (non exécutée) :** adopter le RATIONALE comme **référence conceptuelle unique** ; faire pointer ADR-020 / architecture-domotique / ecosysteme vers lui pour le « pourquoi » ; garder les docs techniques pour le « comment / où » ; traiter les historiques en dépréciation documentaire douce après validation humaine.

---

## 2. Tableau des documents analysés

| Chemin | Rôle actuel | Recouvrement | Recommandation (à valider) |
|--------|-------------|--------------|----------------------------|
| `docs/HESTIA - DOCUMENT DE RÉFLEXION ARCHITECTURALE.md` | Référence conceptuelle (RATIONALE) | — (référence) | Conserver ; éventuellement alias / renommage `ARCHITECTURE-RATIONALE.md` après validation |
| `docs/adr/ADR-020 - Positionnement de Hestia vis-à-vis de Home Assistant.md` | ADR accepté — rôles HA / Agent / Serveur / Apps | **Important** | Conserver avec renvoi vers RATIONALE |
| `docs/produit/architecture-domotique.md` | Spec comportementale passerelle / flux / hors-ligne | **Moyen à important** | Conserver avec renvoi ; fusion partielle éventuelle du « pourquoi » |
| `docs/ecosysteme.md` | Cartographie multi-dépôts | **Moyen** | Conserver avec renvoi (philosophie §1) |
| `docs/architecture.md` | Architecture applicative dépôt `hestia` | **Moyen** | Conserver avec renvoi |
| `docs/CONTEXTE-PROJET.md` | Contexte applicatif / stack / infra | **Faible à moyen** | Conserver avec renvoi (résumé §1) |
| `docs/produit/architecture-reference-v1.md` | Historique fondateur (non normatif) | **Moyen** | Déprécier / renvoi ; fusion ultérieure optionnelle |
| `docs/produit/brief-demarrage-hub-familial.md` | Brief historique | **Faible à moyen** | Déprécier / conserver historique |
| `docs/produit/registre-adr.md` | Mémoire ADR (dont ADR-0001… ) | **Faible à moyen** | Conserver ; lier ADR-0001/0002 au RATIONALE |
| `docs/adr/ADR-018-architecture-domotique-agent-passerelle.md` | ADR accepté — adoption archi domotique | **Faible à moyen** | Conserver avec renvoi |
| `docs/produit/catalogue-fonctionnel.md` | Modules fonctionnels produit | **Faible** | Conserver tel quel |
| `docs/produit/backlog.md` | Backlog produit | **Faible** | Conserver tel quel |
| `docs/produit/README.md` | Index édition doc produit | **Nul** | Conserver ; ajouter entrée RATIONALE après validation |
| `docs/getting-started.md` | Guide démarrage dépôt | **Nul** | Conserver tel quel |
| `docs/deployment.md` | Déploiement VPS | **Nul** | Conserver tel quel |
| `docs/adr/ADR-014-strategie-test.md` | ADR tests | **Nul** | Conserver tel quel |
| `docs/adr/ADR-015-routing-history-mode-degrade.md` | ADR routing PWA | **Nul** | Conserver tel quel |
| `docs/adr/ADR-016-refonte-permissions-modules-utilisateurs.md` | ADR permissions | **Nul à faible** | Conserver tel quel |
| `docs/adr/ADR-017-gestion-profils-interface.md` | ADR profils UI | **Nul à faible** | Conserver tel quel |
| `docs/adr/ADR-019-mode-urgence-alerte-relais.md` | ADR urgence | **Faible** | Conserver tel quel |
| `docs/produit/spec-ui-001-dashboard-v1.md` | Spec UI dashboard | **Nul** | Conserver tel quel |
| `docs/produit/spec-notifications.md` | Spec notifications | **Faible** | Conserver tel quel |

---

## 3. Analyse détaillée document par document

### 3.1 Document de référence

**Chemin :** `C:\Users\web\hestia\docs\HESTIA - DOCUMENT DE RÉFLEXION ARCHITECTURALE.md`

**Rôle :** Document fondateur pré-ADR (validé sur le fond) — raisonnement, invariants, décisions structurantes, scénarios d’épreuve, gouvernance.

**Note de nommage :** le brief d’audit cite `ARCHITECTURE-RATIONALE.md`. Ce nom de fichier **n’existe pas** dans `docs/`. Toute recommandation de « lien vers ARCHITECTURE-RATIONALE.md » doit, jusqu’à décision de renommage/alias, pointer vers le chemin réel ci-dessus.

---

### 3.2 `docs/adr/ADR-020 - Positionnement de Hestia vis-à-vis de Home Assistant.md`

| | |
|--|--|
| **Rôle** | ADR accepté (2026-07-17) figent la répartition HA / Agent / Serveur / Applications et le caractère interchangeable de HA |
| **Recouvrement** | **Important** |
| **Parties concernées** | Finalité « assistant familial » ; HA = « que se passe-t-il ? » ; Serveur = « que signifie ? » ; Agent = normalisation / sync / autonomie sans métier familial ; Apps = seule UI utilisateur ; HA UI admin only ; HA remplaçable ; multi-sources |
| **Nature** | **Redondant** sur le fond des responsabilités et de la finalité ; **complémentaire** comme décision ADR datée et opérationnelle (conséquences positives/négatives, couplage). **Non contradictoire** sur l’essentiel |
| **Recommandation** | **Conserver avec renvoi** vers le RATIONALE pour le raisonnement long ; l’ADR reste la décision courte et normative « produit vs backend » |

---

### 3.3 `docs/produit/architecture-domotique.md`

| | |
|--|--|
| **Rôle** | Spec du comportement passerelle / flux Capteurs→…→Apps, matériel, hors-ligne, identité UUID, caméras, réseau |
| **Recouvrement** | **Moyen à important** |
| **Parties concernées** | §1–2 philosophie assistant vs plateforme ; chaîne HA→Agent→Serveur→Apps ; corrélation multi-signaux (§4) vs interprétation RATIONALE ; autonomie nœud vs résilience Agent ; UUID équipements (technique) |
| **Nature** | **Complémentaire** sur le détail opérationnel (matériel, sync, réseau). **Redondant** sur la philosophie et la chaîne des acteurs. Tension **légère** possible : UUID « technique » ici vs identité équipement normative ailleurs (Installer Module 70) — hors RATIONALE mais à garder en tête |
| **Recommandation** | **Conserver avec renvoi** ; éventuelle **fusion ultérieure** des seuls paragraphes philosophiques vers le RATIONALE (après validation) |

---

### 3.4 `docs/ecosysteme.md`

| | |
|--|--|
| **Rôle** | Référence unique multi-dépôts (`hestia` / installer / agent) |
| **Recouvrement** | **Moyen** |
| **Parties concernées** | §1 philosophie (« assistant familial », questions HA vs Hestia) ; schéma Capteurs→HA→Agent→API→Apps |
| **Nature** | **Complémentaire** (organisation Git / rôles dépôts). **Redondant** sur 1–2 phrases de positionnement produit |
| **Recommandation** | **Conserver avec renvoi** (une ligne vers RATIONALE dans §1) |

---

### 3.5 `docs/architecture.md`

| | |
|--|--|
| **Rôle** | Architecture applicative du dépôt `hestia` (couches PWA/API/BDD/passerelle, same-origin, stack) |
| **Recouvrement** | **Moyen** |
| **Parties concernées** | Tableau des couches et phrase de répartition HA vs Hestia ; Agent = abstraction ; Apps sans logique métier domotique |
| **Nature** | **Complémentaire** (implémentation dépôt). **Redondant** sur le positionnement. Aligné avec RATIONALE « composants servent l’architecture conceptuelle » |
| **Recommandation** | **Conserver avec renvoi** |

---

### 3.6 `docs/CONTEXTE-PROJET.md`

| | |
|--|--|
| **Rôle** | Contexte projet pour reprise (stack réelle, infra, chantiers) |
| **Recouvrement** | **Faible à moyen** |
| **Parties concernées** | §1 résumé assistant familial / non-plateforme domotique / HA encapsulé |
| **Nature** | **Complémentaire**. Léger **redondant** en introduction |
| **Recommandation** | **Conserver avec renvoi** |

---

### 3.7 `docs/produit/architecture-reference-v1.md`

| | |
|--|--|
| **Rôle** | Document fondateur **historique**, déjà non normatif |
| **Recouvrement** | **Moyen** |
| **Parties concernées** | « Pas un logiciel de domotique » ; simplicité / pérennité / modularité / résilience ; couches client/serveur ; modules catalogue |
| **Nature** | **Partiellement obsolète** (vision « hub de modules » plus large que le cycle cognitif actuel). **Complémentaire** comme archive. Pas de contradiction frontale majeure avec IF-001 / IF-012 |
| **Recommandation** | **Déprécier** (déjà amorcé) + renvoi RATIONALE ; **ne pas supprimer** sans validation |

---

### 3.8 `docs/produit/brief-demarrage-hub-familial.md`

| | |
|--|--|
| **Rôle** | Brief initial historique |
| **Recouvrement** | **Faible à moyen** |
| **Parties concernées** | Vision hub / anti-app-domotique ; simplicité ; trois couches client/serveur/données |
| **Nature** | **Historique / partiellement obsolète** (vocabulaire « Hub Familial », découpage 3 couches vs 4 acteurs RATIONALE) |
| **Recommandation** | **Déprécier** / conserver archive ; lien optionnel vers RATIONALE |

---

### 3.9 `docs/produit/registre-adr.md`

| | |
|--|--|
| **Rôle** | Registre ADR (mémoire « pourquoi ») — ADR-0001 finalité, ADR-0002 architecture générale, etc. |
| **Recouvrement** | **Faible à moyen** |
| **Parties concernées** | ADR-0001 (assistant vs domotique) ; ADR-0002 (architecture générale) ; principes proches de la pérennité / simplicité |
| **Nature** | **Complémentaire** (format ADR court). **Redondant** avec RATIONALE + ADR-020 sur la finalité. Hiérarchie RATIONALE → ADR du RATIONALE lui-même est cohérente |
| **Recommandation** | **Conserver** ; ajouter renvois croisés ADR-0001 / registre ↔ RATIONALE après validation ; **ADR impactés** : 0001, 0002 (et lecture croisée 0003 couches UI) |

---

### 3.10 `docs/adr/ADR-018-architecture-domotique-agent-passerelle.md`

| | |
|--|--|
| **Rôle** | ADR accepté — officialise `architecture-domotique.md` |
| **Recouvrement** | **Faible à moyen** |
| **Parties concernées** | Séparation composants ; autonomie ; identité UUID ; hors-ligne |
| **Nature** | **Complémentaire** (acte ADR). Délègue le détail au doc produit. Aligné globalement |
| **Recommandation** | **Conserver avec renvoi** (RATIONALE pour le cadre cognitif ; architecture-domotique pour le détail) |

---

### 3.11 `docs/produit/catalogue-fonctionnel.md`

| | |
|--|--|
| **Rôle** | Catalogue des modules (Dashboard, Agenda, Maison, etc.) |
| **Recouvrement** | **Faible** |
| **Parties concernées** | Modules « Maison / Caméras » comme surfaces produit, non le cycle cognitif |
| **Nature** | **Complémentaire** (quoi construire côté UX). Pas de redondance structurelle avec le RATIONALE |
| **Recommandation** | **Conserver tel quel** |

---

### 3.12 `docs/produit/backlog.md`

| | |
|--|--|
| **Rôle** | Intentions / chantiers produit |
| **Recouvrement** | **Faible** (scénarios capteurs / alertes) |
| **Nature** | **Complémentaire** |
| **Recommandation** | **Conserver tel quel** |

---

### 3.13 Documents à recouvrement nul ou négligeable

| Chemin | Rôle | Recouvrement | Recommandation |
|--------|------|--------------|----------------|
| `docs/getting-started.md` | Démarrage dépôt | Nul | Conserver tel quel |
| `docs/deployment.md` | Déploiement VPS | Nul | Conserver tel quel |
| `docs/produit/README.md` | Index édition | Nul (index) | Conserver ; **devenir un lien** vers RATIONALE dans la table d’écosystème (après validation) |
| `docs/produit/spec-ui-001-dashboard-v1.md` | Spec UI | Nul | Conserver tel quel |
| `docs/produit/spec-notifications.md` | Spec notif | Faible (canal d’« action informationnelle ») | Conserver tel quel |
| `docs/adr/ADR-014-strategie-test.md` | Tests | Nul | Conserver tel quel |
| `docs/adr/ADR-015-routing-history-mode-degrade.md` | Routing | Nul | Conserver tel quel |
| `docs/adr/ADR-016-…permissions…` | Permissions | Nul à faible | Conserver tel quel |
| `docs/adr/ADR-017-…profils…` | Profils UI | Nul à faible | Conserver tel quel |
| `docs/adr/ADR-019-mode-urgence…` | Urgence | Faible (décision / action) | Conserver tel quel |

---

## 4. Liste des contradictions éventuelles

Aucune contradiction **bloquante** identifiée entre le RATIONALE et les ADR acceptés principaux. Points de **tension / clarification** (pas des invalidations) :

| # | Sujet | Documents | Commentaire |
|---|--------|-----------|-------------|
| T1 | Agent « aucune logique métier familiale » vs réactions locales immédiates (lumière, sirène) | RATIONALE (Agent) · ADR-020 · architecture-domotique | Compatible si l’on distingue **réaction locale sans contexte familial** vs **interprétation / décision métier** — à formuler explicitement dans les renvois futurs |
| T2 | « Serveur = cœur métier / Source of Truth métier » vs formulations centrées « VPS + règles » | RATIONALE · architecture.md · architecture-domotique · ADR-020 | Alignés sur le fond ; vocabulaire à homogénéiser (Serveur Hestia / API / core) |
| T3 | Identité équipement UUID (architecture-domotique) vs modèles Installer (`hestia_device_id`, Module 70) | architecture-domotique · (hors `docs/` hestia : Installer) | Pas une contradiction interne au RATIONALE ; risque de **double vocabulaire** dans l’écosystème |
| T4 | Vision historique « hub de modules » vs architecture cognitive | architecture-reference-v1 · brief · RATIONALE | Historique **obsolète comme norme** ; déjà signalé non normatif |
| T5 | Domotique « un module parmi d’autres » (registre ADR-0001) vs « source parmi d’autres » (RATIONALE / ADR-020) | registre-adr · ADR-020 · RATIONALE | Nuance de formulation, **même intention** |

**Principes anciens potentiellement obsolètes (comme norme) :**

- centralité d’un « Hub Familial » nommé ainsi (brief) ;
- architecture décrite **uniquement** comme assemblage de modules catalogue sans cycle cognitif (architecture-reference-v1) ;
- toute lecture qui ferait de Home Assistant l’UI produit (aucune doc active ne le fait encore ; le RATIONALE et ADR-020 l’interdisent).

---

## 5. ADR potentiellement impactés (lecture / renvois futurs)

| ADR / entrée | Impact envisagé (sans exécution) |
|--------------|----------------------------------|
| **ADR-020** | Impact **fort** — devrait citer le RATIONALE comme socle de raisonnement |
| **ADR-018** | Impact **moyen** — citer RATIONALE + architecture-domotique |
| **Registre ADR-0001** | Impact **moyen** — aligner le renvoi « finalité » sur RATIONALE / IF-001 |
| **Registre ADR-0002 / ADR-0003** | Impact **faible à moyen** — clarifier couches logicielles vs cycle cognitif |
| ADR-014…019, 015–017 | Impact **faible / nul** sur le RATIONALE |

---

## 6. Documents candidats à devenir principalement des liens

Après validation manuelle uniquement :

1. **Paragraphes philosophiques** de `ecosysteme.md` §1, `architecture.md` (intro), `CONTEXTE-PROJET.md` §1 → remplacer la prose dupliquée par un **renvoi** au RATIONALE (garder le reste du fichier).
2. **`produit/README.md`** → ajouter une ligne « réflexion architecturale / invariants » pointant vers le RATIONALE.
3. **`architecture-reference-v1.md` / `brief-…`** → rester archives ; bandeau « voir RATIONALE » déjà partiellement présent via ecosysteme — à renforcer.

Ces actions sont des **propositions**, non des opérations réalisées.

---

## 7. Proposition de plan de nettoyage (sans exécution)

Ordre suggéré **après validation humaine** :

| Étape | Action proposée | Risque |
|-------|-----------------|--------|
| 0 | Décider du **nom canonique** (`ARCHITECTURE-RATIONALE.md` vs titre actuel) et de l’emplacement (`docs/` vs `docs/conception/`) | Faible si redirect/lien |
| 1 | Ajouter en tête d’**ADR-020** un renvoi « Raisonnement : RATIONALE » | Faible |
| 2 | Ajouter renvois courts dans `ecosysteme.md`, `architecture.md`, `architecture-domotique.md`, `CONTEXTE-PROJET.md` | Faible |
| 3 | Renforcer bandeaux **historique / non normatif** sur brief + architecture-reference-v1 | Faible |
| 4 | Mettre à jour `produit/README.md` + éventuellement `registre-adr.md` (liens ADR-0001) | Faible |
| 5 | (Optionnel) Extraire / fusionner les doublons philosophiques hors des docs techniques | Moyen — relecture humaine obligatoire |
| 6 | (Optionnel) Clarifier T1 (Agent : réaction vs métier) dans RATIONALE ou ADR-020 | Moyen |
| 7 | **Ne pas supprimer** de fichier avant validation explicite | — |

**Hors périmètre de cet audit (signalé seulement) :** la documentation riche de `hestia-installer` (`FUNCTIONAL-VISION`, `ARCHITECTURE-CONCEPTUELLE`, `MODELE-*`, Module 70) n’appartient pas à `hestia/docs/` mais présente des recouvrements conceptuels majeurs avec le RATIONALE. Un **second audit croisé multi-dépôts** est recommandé avant toute consolidation globale.

---

## 8. Conformité à la mission

| Exigence | Statut |
|----------|--------|
| Aucune modification des documents sources | Respectée (seul livrable : ce rapport) |
| Aucune suppression / déplacement / fusion exécutée | Respectée |
| Rapport d’analyse uniquement | Respectée |
| Recommandations soumises à validation manuelle | Respectée |

---

*Fin du rapport d’audit.*
