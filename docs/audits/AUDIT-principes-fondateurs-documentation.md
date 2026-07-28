# Audit documentaire — couverture des principes fondateurs

> **Audit daté / non normatif.**  
> Les constats ci-dessous décrivent l’état observé au `2026-07-26`. Ils ne remplacent pas `docs/INDEX.md`, `docs/backlog/` ni `docs/gouvernance/`.

| Attribut | Valeur |
|----------|--------|
| **Date** | 2026-07-26 |
| **Périmètre** | `hestia-docs` (SoT) + entrées locales `hestia` / `hestia-agent` / `hestia-installer` |
| **Gouvernance** | [DECISION-0001](../gouvernance/DECISION-0001-DOCUMENTATION.md) |
| **Nature** | Audit — **aucune création** de document de principe ; actions = compléter / indexer / clarifier l’existant |
| **Critère de succès** | Documentation plus simple, cohérente, sans duplication |

---

## 1. Verdict

**La documentation actuelle couvre déjà correctement l’essentiel des principes fondateurs.**  
**Aucune création de document n’est nécessaire** pour porter ces principes.

Les manques sont des **trous d’indexation / glossaire / formulation**, pas l’absence de fondations. Les corriger en **complétant** Glossaire + INDEX (+ éventuellement une phrase dans la revue AUTO-001 déjà existante) suffit.

Formulation absolue « **toute** administration passe par le serveur » : **ne pas l’introduire telle quelle** — elle contredirait ADR-020 (UI HA admin) et l’OPS local (`hestia-ops`). Préférer la formulation déjà présente dans AUTO-001 / architecture-domotique : *administration métier et identité via le serveur ; mise en service et OPS nœud = exceptions / canaux techniques*.

---

## 2. Cartographie — où chercher quoi

Répartition déjà définie par README + DECISION-0001 + Glossaire § Documents — rôles :

| Besoin | Aller à | Ne pas chercher dans |
|--------|---------|----------------------|
| **Pourquoi / invariants** | Constitution (niveau 0) | EPIC, INSTALL, audits |
| **Vocabulaire** | [GLOSSAIRE.md](../gouvernance/GLOSSAIRE.md) | Redéfinir ailleurs |
| **Décisions acceptées** | ADR transverses (`docs/adr/`) | Constitution (sauf invariants) |
| **Flux passerelle / nœud** | [architecture-domotique.md](../architecture/architecture-domotique.md) | Agent ARCHITECTURE (détail runtime) |
| **Présence / reconnexion nœud** | [AUTO-001-reconnection…](../architecture/AUTO-001-reconnection-autonome-noeud.md) + [revue](../architecture/AUTO-001-revue-architecture.md) | Constitution (pas encore le détail nœud) |
| **Équipements SoT** | Module 70 + ADR-004/005 | Agent |
| **Personnes / identité** | MODELE-IDENTITE | Module 70 |
| **Vision PoC / phases** | FUNCTIONAL-VISION | Constitution |
| **Pilotage dev** | backlog/ (EPIC, ROADMAP) | Constitution |
| **OPS nœud** | `hestia-installer/docs/INSTALL.md` | hestia-docs (pas de dossier OPS dédié — **volontaire**) |
| **Contrat Agent runtime** | `hestia-agent/docs/ARCHITECTURE.md` | Redire concepts vers hestia-docs |
| **App VPS / PWA** | `hestia/docs/` | Concepts transverses |

**Portails :** `hestia-docs/README.md` → `docs/INDEX.md` → thématiques.  
**Écart observé au 2026-07-26 :** INDEX **n’indiquait pas encore** AUTO-001.  
**Statut actuel :** point résolu dans `docs/INDEX.md`.

---

## 3. Couverture par principe

Légende **Suffisant** : Oui = utilisable tel quel comme référence ; Partiel = présent mais à clarifier / indexer ; Non = absent.

### Vision

| Principe | Déjà documenté | Document | Suffisant | Action recommandée |
|----------|----------------|----------|-----------|-------------------|
| Hestia est un assistant de vie | Oui | Constitution **IF-001** ; Glossaire § Produit | Oui | **Rien à faire** |
| Technologie au service de l’usage | Oui | Constitution Conclusion / IF-001 / IF-011 ; Glossaire « Domotique = moyen » | Oui | **Rien à faire** |
| Simplicité avant sophistication | Partiel | Constitution **IF-012** (« simplicité exigence », pas la formule exacte) ; formule proche dans AUTO-001-revue | Partiel | **Compléter un document existant** — une ligne au Glossaire renvoyant à IF-012 : « simplicité > sophistication / complexité non recherchée pour elle-même ». *Pas* de nouveau doc. |

### Architecture

| Principe | Déjà documenté | Document | Suffisant | Action recommandée |
|----------|----------------|----------|-----------|-------------------|
| Serveur = source de vérité | Oui | Constitution **IF-005** ; Glossaire « Serveur Hestia » ; Module 70 | Oui | **Rien à faire** (garder la nuance « SoT **métier** », pas vérité du monde) |
| Mini-PC = nœud autonome | Oui | architecture-domotique §2/§7/§12 ; ADR-018 ; AUTO-001 | Oui | **Rien à faire** |
| Toute administration passe par le serveur | Partiel / formulation absolue **Non** | Fragments : AUTO-001-revue (« Admin via le serveur ») ; EPIC-011 ; architecture-domotique §7.2 | Partiel | **Compléter un document existant** — clarifier dans Glossaire *ou* architecture-domotique § admin : *métier/identité via serveur* ; *OPS local / UI HA = canaux techniques ou transitionnels*, pas le produit famille. **Ne pas** figer l’absolu « toute ». Pas de nouveau doc. |
| Nominal sans intervention locale | Oui | AUTO-001 ; architecture-domotique §7.2/§12 ; exceptions mise en service documentées | Oui | **Rien à faire** |
| Survivre aux coupures | Oui | Constitution Résilience + scénarios 2–3 ; architecture-domotique §12 ; EPIC-011 ; Agent L6 | Oui | **Rien à faire** |
| Remplacement matériel ≠ perte d’identité logique | Partiel | Équipements : architecture-domotique §7.1 + Module 70 ; Nœud : AUTO-001-revue Sujets 2–3 | Partiel | **Compléter un document existant** — Glossaire : entrée `node_id` + renvoi « identité logique ⊥ matériel (équipement *et* nœud) ». ADR identité nœud = déjà prévu revue AUTO-001 (plus tard), **pas** maintenant. |

### Identité

| Principe | Déjà documenté | Document | Suffisant | Action recommandée |
|----------|----------------|----------|-----------|-------------------|
| Distinction node_id / équipements / utilisateurs | Partiel | Équipements + personnes : Glossaire + MODELE-IDENTITE ; `node_id` : AUTO-001-revue seulement | Partiel | **Compléter un document existant** — Glossaire § Identité : ajouter `node_id` et tableau à 3 niveaux |
| Identité logique indépendante du matériel | Partiel | Équipements oui ; nœud dans AUTO-001-revue | Partiel | Même action Glossaire (une phrase + renvoi AUTO-001-revue) |
| Responsabilités du serveur | Oui | Constitution ; ADR-020 ; Glossaire | Oui | **Rien à faire** |
| Responsabilités de l’Agent | Oui | Constitution ; ADR-020 ; Glossaire ; agent ARCHITECTURE | Oui | **Rien à faire** |

### Documentation (répartition)

| Principe | Déjà documenté | Document | Suffisant | Action recommandée |
|----------|----------------|----------|-----------|-------------------|
| Le lecteur sait où chercher | Oui, avec trou INDEX | README niveaux 0–5 ; Glossaire § Documents ; DECISION-0001 ; INDEX | Partiel | **Compléter un document existant** — INDEX : liens AUTO-001 (spec + revue + exécution) ; optionnel : ligne « OPS → installer INSTALL.md » |

**Créer un nouveau document :** **non** (aucun principe orphelin sans foyer naturel).  
**Déplacer / Fusionner :** **non** pour l’instant (voir §5 — rationalisation légère seulement).

---

## 4. Redondances, contradictions, obsolescence

### Redondances (acceptables vs à surveiller)

| Élément | Nature | Action |
|---------|--------|--------|
| IF-001 / Glossaire / ADR-020 « assistant de vie » | Redondance **volontaire** + renvois | **Rien à faire** |
| Responsabilités Agent/Serveur (Constitution, ADR-020, Glossaire, ecosysteme) | Idem | **Rien à faire** |
| ROADMAP hestia-docs vs ROADMAP installer | Rôles **distincts** (produit vs installeur) | **Compléter** éventuellement INDEX installer / ecosysteme d’une phrase « deux ROADMAP, deux rôles » si confusion terrain — sinon rien |
| Stubs hestia / installer → hestia-docs | Conforme DECISION-0001 | **Rien à faire** |

### Contradictions / tensions

| Tension | Lecture correcte | Action |
|---------|------------------|--------|
| « Admin via serveur » vs `hestia-ops` / UI HA | Canaux **différents** : métier vs OPS technique vs admin HA transitionnel | Clarifier dans Glossaire / architecture-domotique (**compléter**), pas fusionner les docs |
| SoT serveur vs télémétrie nœud (architecture-domotique §12) | SoT **métier** ≠ autorité des events bruts | Déjà documenté — **Rien à faire** |
| Module 70 §8 « Agent historique » | Marqué remplacé | **Rien à faire** (déjà annoté) |

### Obsolescence

| Document | Statut | Action |
|----------|--------|--------|
| `docs/archive/*` | Non normatif | **Rien à faire** |
| `docs/audits/*` pré-migration | Preuve historique | **Rien à faire** (ne pas en faire des sources de features) |
| INDEX sans AUTO-001 | Incomplet | **Compléter INDEX** |

---

## 5. Recommandations de simplification

1. **Ne pas** créer de « Manifeste des principes » ni de second glossaire.
2. **Ne pas** élever AUTO-001 au niveau Constitution : garder spec chantier + revue ; faire remonter au Glossaire uniquement le vocabulaire stable (`node_id`).
3. **Ne pas** introduire l’invariant absolu « toute administration = serveur » sans ADR de périmètre (sinon contradiction avec ADR-020 / OPS).
4. Maintenir la règle DECISION-0001 : concepts dans hestia-docs ; runtime dans les dépôts code.

---

## 6. Liste très courte des modifications proposées

| # | Action | Document cible | Contenu |
|---|--------|----------------|---------|
| 1 | **Compléter** | `docs/gouvernance/GLOSSAIRE.md` | Entrée **`node_id`** ; rappel simplicité (renvoi IF-012) ; phrase admin métier vs OPS/HA |
| 2 | **Compléter** | `docs/INDEX.md` | Liens AUTO-001 (architecture + revue + exécution) ; renvoi OPS → installer |
| 3 | *(Optionnel, plus tard)* | ADR identité nœud | Déjà listé dans revue AUTO-001 — **après** Phase 1 / avec lot F ; **pas** requis pour clôturer cet audit |

**Hors scope immédiat :** fusion de documents, déplacement de Constitution, nouveau portail Vision.

---

## 7. Conclusion

| Question | Réponse |
|----------|---------|
| Les principes sont-ils couverts ? | **Oui**, pour l’essentiel, dans Constitution + Glossaire + architecture-domotique + AUTO-001 |
| Faut-il créer un document ? | **Non** |
| Que faire ? | **Trois compléments courts** (Glossaire + INDEX) listés §6 |
| Lots AUTO-001A–F ? | **Non bloqués** par cet audit documentaire |

> **Formule de succès :** la documentation actuelle porte déjà correctement ces principes. Aucune création de document n’est nécessaire ; seules des complétions ciblées de documents existants sont recommandées.
