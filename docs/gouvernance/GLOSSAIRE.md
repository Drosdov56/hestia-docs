# Glossaire officiel — écosystème Hestia

**Statut :** Normatif pour le vocabulaire transverse  
**Constitution :** [Document de réflexion architecturale](../constitution/HESTIA%20-%20DOCUMENT%20DE%20RÉFLEXION%20ARCHITECTURALE.md)  
**Règle :** une seule définition officielle par concept. Les autres documents **renvoient** ici ou à la Constitution ; ils ne redéfinissent pas.

---

## Produit

| Terme | Définition officielle | Synonymes acceptés (non concurrents) |
|-------|----------------------|--------------------------------------|
| **Hestia** | Assistant de vie / assistant familial contextuel. **N’est pas** une plateforme ni une application domotique. | « Assistant familial contextuel » = même concept |
| **Domotique** | Moyen technique et source d’information, **pas** la finalité du produit. | — |
| **Home Assistant (HA)** | Backend domotique local technique. Répond à « que se passe-t-il dans la maison ? ». Remplaçable. UI non destinée aux utilisateurs finaux. | Backend technique |

---

## Acteurs (responsabilités)

| Terme | Définition officielle | Notes |
|-------|----------------------|-------|
| **Serveur Hestia** | Cœur métier : signification, comptes, historique, notifications, règles, API. **Source de vérité métier.** | Aussi nommé « Backend Hestia » dans Module 70 / agent — **même rôle** |
| **hestia-agent** | Runtime local du nœud : abstraction / normalisation technique, sync, health. **Pas** d’interprétation familiale métier (V1 : infra only). | Réactions techniques locales ≠ métier familial |
| **Applications Hestia** | Présentation (PWA / Android). **Pas** d’interprétation métier. | — |
| **hestia-installer** | Installation et maintenance du nœud ; déploie l’Agent ; **ne possède pas** le code Agent. | — |

---

## Cycle cognitif (Constitution)

Chaîne officielle (Constitution) :

**Observation → Connaissance → Interprétation → Décision → Action → Mémoire**

| Étape | Définition courte |
|-------|-------------------|
| Observation | Donnée perçue / signal retenu |
| Connaissance | Observation contextualisée / structurée |
| Interprétation | Sens pour la personne / le foyer |
| Décision | Conclusion, alerte ou recommandation |
| Action | Effet produit (humain, système, notification…) |
| Mémoire | Sélection de ce qui mérite d’être conservé (pas tout stocker) |

### Mapping avec la chaîne MODELE-INFORMATION

| Constitution | MODELE-INFORMATION (spécialisation) |
|--------------|-------------------------------------|
| Observation | Capteur → Signal → (HA / entités) → Sélection |
| Connaissance / Interprétation | Modèle → Information utile |
| Décision | Voir MODELE-DECISION |
| Mémoire | Durée / sélection des informations utiles |

**« Information utile »** = formulation opérationnelle de la sortie compréhensible destinée aux applications ; elle **s’inscrit** dans le cycle Constitution, elle ne le remplace pas.

---

## Identité & équipements

| Terme | Définition officielle | SoT |
|-------|----------------------|-----|
| **Identité personne** | Preuves ≠ identité ≠ décision d’identification | MODELE-IDENTITE |
| **Identité équipement** | Identifiant métier stable de l’équipement dans Hestia | Module 70 / ADR-005 |
| **`hestia_device_id`** | Identifiant métier immuable d’un équipement. Terme officiel unique — ne pas utiliser « UUID » pour désigner cet identifiant. | Module 70 / ADR-005 |
| **`node_id`** | Identité **logique** d’un **nœud** Hestia (Agent / mini-PC). **Indépendant du matériel** (pas dérivé MAC/série à chaque boot). **Distinct** de `hestia_device_id` (équipement) et de l’identité personne. Sert à identifier le nœud auprès du serveur (présence, ingest, registre). | AUTO-001 ; conf Agent |

Les trois niveaux d’identité ne se confondent pas : **personne** · **nœud (`node_id`)** · **équipement (`hestia_device_id`)**.

---

## Administration (périmètres)

| Terme | Définition officielle | Notes |
|-------|----------------------|-------|
| **Administration fonctionnelle (Hestia)** | Pilotage métier et produit via le **serveur** Hestia (comptes, règles, équipements SoT, état des nœuds côté API, décisions familiales). Canal nominal pour l’admin foyer. | Aligné SoT serveur (IF-005) |
| **Administration OPS / maintenance** | Exploitation technique du **nœud** : installateur, `hestia-ops`, health local, conteneurs, diagnostics. Canal de **mise en service** et de **maintenance** — pas le produit famille. | Voir `hestia-installer` (INSTALL / OPS-001) |
| **UI Home Assistant (admin)** | Réservée aux administrateurs pour le backend technique local (équipements HA, diagnostics). **Pas** l’interface utilisateurs finaux. | ADR-020 |

Ces canaux sont **complémentaires**, pas interchangeables. On ne formule pas « toute administration passe par le serveur » : l’OPS nœud et l’UI HA admin restent des canaux techniques légitimes.

---

## Documents — rôles

| Document | Rôle |
|----------|------|
| Constitution (réflexion architecturale) | Niveau 0 — pourquoi / invariants |
| ADR transverses | Décisions acceptées |
| ARCHITECTURE-CONCEPTUELLE | Carte des modèles (pas une constitution) |
| MODELE-* | Spécialisations sous Constitution |
| FUNCTIONAL-VISION | Vision « comment ça marche » + cadrage PoC |
| Module 70 | SoT normative équipements |
