# Vision fonctionnelle — Hestia

**Niveau documentaire :** vision opérationnelle (comment ça marche / PoC).  
**Ne remplace pas** la [Constitution](../constitution/HESTIA%20-%20DOCUMENT%20DE%20RÉFLEXION%20ARCHITECTURALE.md) ni le [glossaire](../gouvernance/GLOSSAIRE.md).

Répond à : *Comment fonctionne Hestia dans la pratique (phases, acteurs, PoC) ?*

| Ne remplace pas | Document |
|-----------------|----------|
| Pourquoi / invariants | [Constitution](../constitution/HESTIA%20-%20DOCUMENT%20DE%20RÉFLEXION%20ARCHITECTURALE.md) |
| Définitions | [GLOSSAIRE.md](../gouvernance/GLOSSAIRE.md) |
| Pilotage Installer | `hestia-installer` → [ROADMAP.md](https://github.com/Drosdov56/hestia-installer/blob/main/docs/ROADMAP.md) |
| Travaux techniques Installer | `hestia-installer` → [BACKLOG.md](https://github.com/Drosdov56/hestia-installer/blob/main/docs/BACKLOG.md) |
| Spec cycle de vie équipements | [70-cycle-vie-equipements.md](../equipements/70-cycle-vie-equipements.md) |
| ADR équipements | [ADR-004](../adr/ADR-004-mise-en-service-equipements.md), [ADR-005](../adr/ADR-005-cycle-vie-equipements.md) |

**Mise à jour :** 2026-07-25 (migration hestia-docs)

---

## 1. Ancrage constitutionnel

Les fondements (assistant de vie, domotique = moyen, HA = backend technique, cycle cognitif, responsabilités des acteurs) sont définis **uniquement** dans la Constitution et le glossaire.

Ce document **ne redéfinit pas** ces notions. Il décrit la vision opérationnelle, le cadrage PoC et les horizons d’acteurs.

---

## 2. Vision du produit (opérationnelle)

Hestia est déployé, côté nœud, sur un **mini-PC unique** (détail réseau / pile : ADR-003 dans `hestia-installer`).

### Valeur ajoutée (rappel opérationnel)

L’objectif n’est **pas** de collecter des événements pour eux-mêmes, mais de produire des **informations utiles** (voir glossaire : mapping avec le cycle Observation → … → Mémoire).

### Contraintes opérationnelles V1 (Installer)

- un foyer · une machine · un administrateur · services limités ;
- simplicité opérationnelle, charge cognitive minimale ;
- **Hestia** = seule couche visible pour l’administrateur du nœud ;
- Home Assistant et Zigbee2MQTT = backends techniques ([ADR-004](../adr/ADR-004-mise-en-service-equipements.md)).
---

## 3. Objectifs

| Objectif | Statut |
|----------|--------|
| Déployer et maintenir un nœud technique fiable | ✅ Fondations (Installer v1.0.0, L8) |
| Connecter HA au broker sans action manuelle | ✅ INT-001 |
| Transformer un événement en information utile pour un humain | 🎯 **Cible PoC** (voir §15) |
| Mettre en service les équipements via Hestia Admin | ⚪ Décidé — non livré (UX-003) |
| Hub familial | ⚪ Phase ROADMAP — à construire |

---

## 4. Acteurs de l’écosystème

L’écosystème Hestia **dépasse** la seule personne accompagnée.

Liste **volontairement ouverte** — non exhaustive. Ce ne sont **pas** des fonctionnalités V1.  
Objectif : que l’architecture **ne ferme aucune** évolution future.

| Catégorie | Intention (vision) |
|-----------|-------------------|
| Personne accompagnée | Au centre du suivi / du confort |
| Utilisateur actif | Interagit régulièrement avec Hestia |
| Utilisateur occasionnel | Usage ponctuel |
| Utilisateur passif | Consultation / affichage uniquement |
| Observateur | Suit sans configurer |
| Proche (aidant) | Famille ou entourage |
| Administrateur familial | Configure nœud et équipements (profil V1 documenté) |
| Professionnel | Santé ou autre regard métier |
| Intervenant | Prestataire ponctuel |
| Structure | EHPAD, résidence autonomie, etc. |

**V1 :** parcours opérationnel ADR concentré sur l’**administrateur** du nœud ([ADR-003](../../../hestia-installer/docs/ADR/ADR-003-architecture-reseau-pile-domotique.md), [ADR-004](../adr/ADR-004-mise-en-service-equipements.md)). Le reste = horizons d’architecture, pas backlog.

---

## 5. Profils et permissions

**Ne pas recréer le modèle ici.** Il est conservé tel que déjà défini dans le dépôt.

Références :

| Sujet | Document |
|-------|----------|
| Administrateur / foyer mono-admin (V1) | [ADR-003](../../../hestia-installer/docs/ADR/ADR-003-architecture-reseau-pile-domotique.md) |
| Profil administrateur (mise en service) | [ADR-004](../adr/ADR-004-mise-en-service-equipements.md) · [ARCHITECTURE](../../../hestia-installer/docs/ARCHITECTURE.md) |
| Droits host / Docker | [ADR-002](../../../hestia-installer/docs/ADR/ADR-002-groupe-docker.md) |
| Campagnes USER / ADMIN | [ADR-008](../../../hestia-installer/docs/ADR/ADR-008-validation-assistee.md) |
| Métadonnées équipement (ex. propriétaire) | [Module 70](../equipements/70-cycle-vie-equipements.md) |
| Capacités nœud (capabilities) | `lib/capabilities.sh` / precheck |

En résumé : les **profils**, **permissions** et **droits par module / composante** existent déjà dans ces sources. Ce document les **référence**, il ne les redéfinit pas.

L’élargissement aux catégories du §4 se fera **sans casser** ce socle, lorsque le produit l’exigera.

---

## 6. Intelligence artificielle

La **vision IA fait partie du projet Hestia**.

L’IA **n’est pas un module indépendant**.  
C’est une **capacité transversale** appelée à enrichir progressivement **tous** les modules.

Formalisation ADR : encore à venir — retard de formalisation, pas d’absence de vision.

### Rôle (principes — sans implémentation)

- analyse comportementale ;
- détection d’anomalies ;
- corrélation d’événements ;
- aide à la décision ;
- assistance aux proches ;
- explications compréhensibles.

### Place dans le produit

| | |
|--|--|
| **V1 / PoC** | Pas d’exigence de livrer un moteur IA ; démontrer d’abord la **valeur** événement → information utile |
| **Ensuite** | Enrichissement progressif des modules — sans fermer l’architecture |

---

## 7. Principe d’évolution

Le projet est aujourd’hui une **preuve de concept**.

- Développer pour des **besoins réellement observés**.
- Garder l’architecture **compatible** avec des évolutions plus larges si elles deviennent pertinentes.
- **Anticiper** l’évolution **sans** développer prématurément les fonctionnalités.

Toute évolution future devra respecter :

1. **Compatibilité avec la V1** ;
2. **Simplicité** ;
3. **Extensibilité** ;
4. **Absence de sur-ingénierie**.

Pas de dérive vers un produit complexe tant que le PoC n’est pas validé.

---

## 8. Architecture fonctionnelle (rappel)

```
Fondations (livrées)
  Installer · HA · MQTT · Z2M · Agent infra
        ↓
Plateforme (amorcée)
  Agent métier · Backend · API
        ↓
Administration
  Hestia Admin (UX-003)
        ↓
Hub familial / Exploitation
  Information utile pour l’humain
```

Détail des responsabilités : [ADR-004](../adr/ADR-004-mise-en-service-equipements.md), [ADR-005](../adr/ADR-005-cycle-vie-equipements.md), [ROADMAP](../../../hestia-installer/docs/ROADMAP.md).

---

## 9. Modules / composantes

| Composante | Rôle | Statut |
|------------|------|--------|
| Installer | Déploie / valide / désinstalle le nœud | ✅ v1.0.0 |
| Home Assistant | Backend d’états | ✅ |
| Mosquitto | Transport MQTT local | ✅ |
| Zigbee2MQTT | Passerelle Zigbee (transparente) | ✅ |
| Agent infra | Daemon prêt pour le métier | ✅ |
| Agent métier | Sync, transitions techniques | ⚪ |
| Backend | SoT fonctionnel | ⚪ |
| Hestia Admin | Décisions métier / UX-003 | ⚪ |
| Hub familial | Information utile, notifications | ⚪ |

---

## 10. Sécurité et administration (références)

Principes déjà tranchés — voir ADR, ne pas dupliquer :

- MQTT localhost (ADR-003) ;
- pas de groupe docker silencieux (ADR-002) ;
- campagnes assistées sans NOPASSWD (ADR-008) ;
- secrets non affichés en clair (UX-001) ;
- fin Installer = plateforme ; MS équipements = Admin (ADR-004) ;
- cycle de vie / `hestia_device_id` (Module 70).

---

## 11. Interfaces (état)

| Interface | Statut |
|-----------|--------|
| Installer + rapports | ✅ |
| Onboarding HA (stratégie B) | ✅ guidé |
| Hestia Admin | ⚪ |
| Hub familial | ⚪ |

---

## 12. Communication (cible PoC)

```
Capteur → Home Assistant → Hestia Agent → Backend → Hub familial
         → Information utile → (éventuelle) notification
```

Aujourd’hui : Capteur → HA (via Z2M/MQTT) est **réel** ; Agent infra est **déployé** ; Backend / Hub / notification Hestia sont **à construire**.

---

## 13. Cycle de vie équipements

Référence normative : [Module 70](../equipements/70-cycle-vie-equipements.md) / [ADR-005](../adr/ADR-005-cycle-vie-equipements.md).  
Ne pas recopier la machine d’états ici.

---

## 14. Hors périmètre de ce document

Ce document **ne décide pas** :

- des futures fonctionnalités détaillées ;
- du modèle économique ;
- d’un produit commercial ;
- du multi-clients ;
- du multi-établissements.

En revanche, l’**architecture actuelle doit rester compatible** avec ces évolutions si elles deviennent pertinentes un jour.

Périmètre Installer v1.0 : [ADR-007](../../../hestia-installer/docs/ADR/ADR-007-perimetre-v1.md).

---

## 15. Preuve de concept — valeur Hestia

### Objectif

Le PoC **ne démontre pas la technique**.  
Il démontre le **bénéfice utilisateur**.

Ce n’est **pas** une démo d’administration.  
C’est une démonstration de **valeur** : un événement réel devient une information utile pour un humain.

### Scénario de référence

```
Capteur
  ↓
Home Assistant
  ↓
Agent
  ↓
Backend
  ↓
Hub
  ↓
Information utile
  ↓
Notification éventuelle
```

### Briques disponibles aujourd’hui

Capteur (ex. SNZB-06P) · HA · MQTT/Z2M · Agent infra · nœud qualifié L8.

### Briques à compléter pour le scénario

Agent métier · Backend · Hub · formulation de l’information utile · notification.

### Chemin court

1. S’appuyer sur le nœud déjà vivant (Fondations).
2. Brancher le **chemin minimal** Agent → Backend → Hub pour **un** type d’événement.
3. Produire **une** information claire + optionnellement **une** notification.
4. Reporter Admin / multi-acteurs / IA avancée **après** validation de cette valeur.

---

## 16. Cohérence documentaire

| Document | Rôle |
|----------|------|
| [ROADMAP](../../../hestia-installer/docs/ROADMAP.md) | Où en est-on ? (phases, %) |
| **FUNCTIONAL-VISION** (ici) | Comment ça fonctionne ? (vision) |
| [BACKLOG](../../../hestia-installer/docs/BACKLOG.md) | Quoi construire techniquement ? |
| [ADR/](../adr/) | Quelles décisions sont figées ? |

Pas de duplication des checklists techniques ni des lots ROADMAP.

---

## 17. Références

[ROADMAP](../../../hestia-installer/docs/ROADMAP.md) · [ADR-003](../../../hestia-installer/docs/ADR/ADR-003-architecture-reseau-pile-domotique.md) · [ADR-004](../adr/ADR-004-mise-en-service-equipements.md) · [ADR-005](../adr/ADR-005-cycle-vie-equipements.md) · [ADR-007](../../../hestia-installer/docs/ADR/ADR-007-perimetre-v1.md) · [Module 70](../equipements/70-cycle-vie-equipements.md) · [SNZB-06P24](../modeles/capteurs/SNZB-06P24.md) · [BACKLOG](../../../hestia-installer/docs/BACKLOG.md) (UX-002, UX-003)
