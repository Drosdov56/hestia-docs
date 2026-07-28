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

Statut général : quatre dépôts Git propres et synchronisés avec origin/main.

Chantier actif : AUTO-002F — cycle de vie du nœud (tokens par nœud).

Dernier chantier terminé : AUTO-002E — administration distante (E-1 commandes + E-2 terminal WS).

Prochain chantier : AUTO-002F.

---

# DÉPÔTS

## hestia

HEAD : `526bbb7`  
Working tree : clean *(hors scripts ops locaux non versionnés éventuels)*  
État : synchronisé origin/main

---

## hestia-installer

HEAD : `c7460ba`  
Working tree : clean  
État : synchronisé origin/main

---

## hestia-agent

HEAD : `3836bde`  
Working tree : clean  
État : synchronisé origin/main

---

## hestia-docs

HEAD : *(après commit gouvernance ci-dessous)*  
Working tree : clean  
État : synchronisé origin/main

---

# DÉCISIONS STRUCTURANTES

1. `hestia-docs` = Source de vérité transverse — [DECISION-0001](DECISION-0001-DOCUMENTATION.md).
2. `hestia-installer/docs/ROADMAP.md` = feuille de route locale ; pilotage produit → `hestia-docs/docs/backlog/ROADMAP.md`.
3. Documents historiques = archives marquées, non normatives.
4. AUTO-001 clos / validé terrain 2026-07-27 — suivi : `docs/backlog/execution/AUTO-001.md`.
5. AUTO-002 ouvert — spec : `docs/architecture/AUTO-002-supervision-administration-noeuds.md`.
6. **ADR-023** accepté : terminal distant = WebSocket sortant + service `hestia-ws-relay` (aucune logique métier dans le relay).
7. Écosystème = 3 dépôts applicatifs + 1 dépôt documentaire.
8. Agent natif systemd ; HTTPS/WSS sortant vers le serveur.
9. Home Assistant encapsulé sur le nœud.
10. Procédures OPS → `hestia-installer/docs/INSTALL.md` ; statut produit → `hestia-docs`.
11. Pas de duplication conceptuelle entre dépôts.
12. SW PWA : bump `CACHE_NAME` obligatoire sous `client/` (courant : `hestia-v0.8.23`).
13. 32 tests E2E Playwright.
14. EPIC-001 livré.
15. Continuité IA : `docs/gouvernance/PROJECT-STATE.md` + `PROMPT-REPRISE.md`.

---

# POINTS OUVERTS

- Responsable PROJECT-STATE à renseigner.
- G10 cold boot secteur — en attente séparée.
- Validation UI terminal + Agent réel sur nœud mini-PC (hors VPS) — à confirmer terrain.
- Idle timeout session : non exercé bout-en-bout sur VPS (fermeture volontaire + relay stdin/stdout OK).

---

# BLOCAGES

- Aucun.

---

# PROCHAINE SESSION

Objectifs immédiats :

1. Démarrer AUTO-002F (identité / token par nœud, révocation).
2. Optionnel : valider le terminal depuis l'UI admin + Agent sur le mini-PC.
3. Ne pas rouvrir la conception ADR-023 / architecture WS.

---

# À NE PAS REFAIRE

- Audit documentaire global sans demande explicite.
- Omettre `hestia-docs` du périmètre trans-dépôts.
- Mélanger documentation et développement dans un même commit.
- Dupliquer roadmap/backlog produit hors de `hestia-docs`.
- Démarrer un chantier sur working tree sale.
- Remettre de la logique métier dans `hestia-ws-relay`.

---

# JOURNAL DES SESSIONS

| Date | Résumé | Décisions | Commit(s) | SHA | Résultat |
|------|--------|-----------|-----------|-----|----------|
| 2026-07-28 | AUTO-002E clôturé (E-1+E-2) + validation VPS | ADR-023 ; relay systemd+Apache | hestia/agent/installer/docs | `526bbb7` / `3836bde` / `c7460ba` | Relay+WSS+API OK ; UI/agent nœud restant |
| 2026-07-28 | Continuité IA officialisée | PROJECT-STATE + PROMPT-REPRISE | gouvernance | `28222a5` | OK |
| 2026-07-28 | Remise en état Git 4 dépôts | 10 commits atomiques | H1–H4, I1, A1, D1–D4 | voir DÉPÔTS | OK |
| 2026-07-28 | AUTO-002A inventaire nœud | Registre + API admin | hestia | `ec501f4` | 10+11+11 tests API OK |
