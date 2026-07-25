# ADR-018 — Architecture domotique : agent, passerelle et capteurs

## Statut

Accepté.

> Cadre : [Constitution](../constitution/HESTIA%20-%20DOCUMENT%20DE%20RÉFLEXION%20ARCHITECTURALE.md) · détail : [architecture-domotique.md](../architecture/architecture-domotique.md).

## Contexte

Hestia s'étend au-delà de la PWA/API existante pour couvrir la surveillance domestique réelle (capteurs Zigbee, caméras, présence). Matériel déjà commandé (mini PC, passerelle SONOFF ZBDongle-P, premier capteur de présence) — ce chantier est actif, pas une vision différée.

## Décision

Adoption de l'architecture détaillée dans [`architecture-domotique.md`](../architecture/architecture-domotique.md) :

- passerelle locale autonome (mini PC) ↔ VPS Hestia ↔ tablette d'affichage ;
- trois composants strictement séparés (§8 du document détaillé) ;
- principes non négociables dès l'origine : identité stable des équipements par `hestia_device_id`, mises à jour entièrement à distance, résilience hors connexion avec synchronisation automatique.

ADR-0005 (limiter les dépendances externes) ne s'applique pas à l'agent domotique du mini PC — périmètre clarifié en §10 du document détaillé.

## Conséquences

- Le schéma 4 couches d'ADR-0002 / `architecture.md` est complété par la **passerelle** comme cinquième composant réel (mise à jour associée à cet ADR).
- Les modules `home` et `cameras` déjà présents dans `catalogue.json` (actuellement contenu statique / démo) sont les futurs points d'intégration de cette architecture côté PWA — pas de nouveau module à créer pour l'instant, juste un futur chantier d'implémentation.
- Détail technique complet : `docs/produit/architecture-domotique.md`.
