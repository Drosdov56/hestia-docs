# DECISION-0001 — Gouvernance documentaire de l’écosystème Hestia

**Statut :** Acceptée — permanente  
**Date :** 2026-07-25  
**Emplacement :** `hestia-docs/docs/gouvernance/DECISION-0001-DOCUMENTATION.md`

---

## Décision

1. **`hestia-docs` est la seule source documentaire transverse officielle** (Source of Truth) de l’écosystème Hestia.
2. **Les concepts ne doivent plus être dupliqués** dans `hestia`, `hestia-installer` ou `hestia-agent`.
3. **Toute nouvelle ADR transverse** est créée dans `hestia-docs`.
4. **Toute évolution de la Constitution** (document de réflexion architecturale) est réalisée dans `hestia-docs`.
5. **Les autres dépôts ne contiennent que leur documentation locale**, éventuellement complétée par des stubs de renvoi vers `hestia-docs`.
6. **Toute exception** à ces règles doit être justifiée par une ADR.

---

## Conséquences

- Les dépôts applicatifs (`hestia`, `hestia-installer`, `hestia-agent`) documentent uniquement leur périmètre code / ops.
- Les stubs de renvoi ne portent aucun contenu conceptuel concurrent.
- Le glossaire et la Constitution dans `hestia-docs` font autorité sur le vocabulaire et les invariants.

Cette décision **gèle** l’architecture documentaire. Elle ne se modifie pas sans ADR explicite.
