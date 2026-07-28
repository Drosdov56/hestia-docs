# PROJECT STATE — HESTIA

Version : 1.0  
Dernière mise à jour : 2026-07-28  
Responsable : Équipe Hestia *(à compléter)*

---

# RÈGLE FONDAMENTALE

**PROJECT-STATE.md est la référence officielle de reprise entre les conversations** — toutes sessions ChatGPT, Cursor ou autre assistant incluses.

Toute nouvelle conversation commence par la fourniture de ce document.

L'assistant considère son contenu comme l'état officiel du projet et reprend directement le chantier actif.

L'assistant ne redémarre pas par un audit global, une reconstruction historique ou une remise en question de l'état du projet, sauf si un fait nouveau (commit, diff, document, décision explicite) contredit ce document.

En cas de divergence :

1. Les faits nouveaux priment.
2. PROJECT-STATE.md est mis à jour.
3. PROMPT-REPRISE.md est régénéré à partir de PROJECT-STATE.md.

**Discipline de clôture :** la clôture d'un chantier comprend obligatoirement la mise à jour de PROJECT-STATE.md.

---

# CONTRAT DE REPRISE

À chaque nouvelle conversation :

1. Lire PROJECT-STATE.md entièrement.
2. Le considérer comme la référence opérationnelle.
3. Ne pas refaire d'audit global sans demande explicite.
4. Reprendre directement le chantier actif.
5. Mettre à jour PROJECT-STATE.md avant la clôture de la session.

---

# ÉTAT GLOBAL

Statut général : consolidation documentaire prête à committer ; AUTO-001 clos ; AUTO-002 ouvert.

Chantier actif : finalisation Git consolidation documentaire trans-dépôts.

Dernier chantier terminé : gouvernance documentaire trans-dépôts.

Prochain chantier : AUTO-002 — supervision et administration des nœuds.

---

# DÉPÔTS

## hestia

HEAD : `14513ae`  
Working tree : modifié, non commité (doc + code Nodes)  
État : en attente commit/push

---

## hestia-installer

HEAD : `33eaf93`  
Working tree : modifié, non commité (doc)  
État : en attente commit/push

---

## hestia-agent

HEAD : `b5c7bed`  
Working tree : modifié, non commité (doc)  
État : en attente commit/push

---

## hestia-docs

HEAD : `6483054`  
Working tree : modifié + non suivi, non commité  
État : en attente commit/push

---

# DÉCISIONS STRUCTURANTES

1. `hestia-docs` = Source de vérité transverse — [DECISION-0001](DECISION-0001-DOCUMENTATION.md).
2. `hestia-installer/docs/ROADMAP.md` = feuille de route locale ; pilotage produit → `hestia-docs/docs/backlog/ROADMAP.md`.
3. Documents historiques = archives marquées, non normatives.
4. AUTO-001 clos / validé terrain 2026-07-27 — suivi : `docs/backlog/execution/AUTO-001.md`.
5. AUTO-002 ouvert — spec : `docs/architecture/AUTO-002-supervision-administration-noeuds.md`.
6. Écosystème = 3 dépôts applicatifs + 1 dépôt documentaire.
7. Agent natif systemd ; HTTPS sortant vers le serveur.
8. Home Assistant encapsulé sur le nœud.
9. Procédures OPS → `hestia-installer/docs/INSTALL.md` ; statut produit → `hestia-docs`.
10. Pas de duplication conceptuelle entre dépôts.
11. SW PWA : bump `CACHE_NAME` obligatoire sous `client/` (courant : `hestia-v0.8.23`).
12. 32 tests E2E Playwright.
13. EPIC-001 livré.
14. Continuité IA : `docs/gouvernance/PROJECT-STATE.md` + `PROMPT-REPRISE.md`.

---

# POINTS OUVERTS

- Commits atomiques + push `origin/main` sur les 4 dépôts.
- Séparation commits doc / code AUTO-001 dans `hestia`.
- Responsable PROJECT-STATE à renseigner.
- G10 cold boot secteur — en attente séparée.

---

# BLOCAGES

- Working trees sales sur les 4 dépôts — ne pas démarrer AUTO-002 avant commit/push propre.

---

# PROCHAINE SESSION

Objectifs immédiats :

1. Committer et pousser la consolidation documentaire (commits atomiques par dépôt).
2. Vérifier liens Markdown et cohérence README post-push.
3. Ouvrir AUTO-002A une fois les dépôts propres.

---

# À NE PAS REFAIRE

- Audit documentaire global sans demande explicite.
- Omettre `hestia-docs` du périmètre trans-dépôts.
- Mélanger documentation et développement dans un même commit.
- Dupliquer roadmap/backlog produit hors de `hestia-docs`.
- Démarrer un chantier sur working tree sale.

---

# JOURNAL DES SESSIONS

| Date | Résumé | Décisions | Commit(s) | SHA | Résultat |
|------|--------|-----------|-----------|-----|----------|
| 2026-07-28 | Création PROJECT-STATE + PROMPT-REPRISE | Continuité IA officialisée | — | — | Initialisation |
