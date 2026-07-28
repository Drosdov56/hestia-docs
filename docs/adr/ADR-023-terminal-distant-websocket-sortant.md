# ADR-023 — Terminal distant sans SSH entrant (WebSocket sortant)

**Statut :** Accepté  
**Date :** 2026-07-28  
**Emplacement :** `hestia-docs/docs/adr/`  
**Implémentation :** AUTO-002E-2  

> Cadre : [Constitution](../constitution/HESTIA%20-%20DOCUMENT%20DE%20RÉFLEXION%20ARCHITECTURALE.md) · [AUTO-002](../architecture/AUTO-002-supervision-administration-noeuds.md) · ADR-0005 · ADR-018 · ADR-020.

---

## Contexte

AUTO-002E exige un terminal interactif administrateur vers le nœud, sans SSH entrant ni port forwarding.

L’API Hestia est volontairement basée sur PHP + HTTP request/response (Apache / PHP-FPM). Un canal WebSocket longue durée ne peut pas vivre dans ce modèle sans le dénaturer.

Contraintes structurantes :

- conserver l’API REST comme source de vérité ;
- limiter les dépendances applicatives (ADR-0005) ;
- Agent sortant uniquement (AUTO-001 / ADR-018) ;
- ne pas déplacer de logique métier hors de l’API.

---

## Décision

1. **WebSocket = infrastructure spécialisée**, au même titre que MariaDB, Mosquitto ou Home Assistant — pas une dépendance du cycle de vie PHP-FPM.

2. **Service dédié** `hestia-ws-relay`, hébergé dans le dépôt `hestia` sous `services/ws-relay/`, géré par systemd. Son rôle exclusif :
   - authentifier les extrémités selon les décisions de l’API ;
   - apparier les sessions ;
   - relayer les flux ;
   - gérer les timeouts et la fermeture.

3. **API REST inchangée dans son rôle** : création de session, tickets, authentification, audit, file de commandes (`shell_session`), clôture.

4. **Flux Agent → Serveur sortant** : l’Agent initie la connexion WSS. Aucun port entrant sur le nœud.

5. **Same-origin WSS** via Apache (`mod_proxy_wstunnel`) vers le daemon en loopback. Pas de port WebSocket public distinct.

6. **Implémentation du relay** : PHP CLI à l’aide d’une bibliothèque WebSocket mature. Le choix exact de bibliothèque est une décision d’implémentation (hors ADR), isolée dans `services/ws-relay/` et hors de `core/`.

7. **Pas de dépôt séparé** pour le relay en V1 : cycle de release aligné avec le serveur Hestia.

8. **Hors périmètre** :
   - shell libre non allowlist ;
   - SSH / reverse tunnel / port forwarding ;
   - logique métier dans le relay ;
   - transformation de l’API PHP en serveur temps réel.

---

## Principes non négociables

Ces règles s’appliquent à toute évolution future du relay. Elles priment sur la commodité d’implémentation.

1. **Le relay ne contient aucune logique métier.**
2. **Le relay ne connaît pas les utilisateurs** (comptes, rôles, permissions métier).
3. **Le relay ne connaît pas les commandes** (catalogue, allowlist, classes de risque).
4. **Le relay ne prend aucune décision fonctionnelle** (qui peut quoi, quoi exécuter, quoi auditer comme fait métier).
5. **Toute décision appartient à l’API** (création/révocation de session, émission et validation des tickets, audit).
6. **L’Agent reste responsable de l’exécution locale** (PTY / shell restreint, allowlist, effets de bord sur le nœud).
7. **Le relay peut être redémarré sans altérer l’état métier** : l’état durable vit dans l’API (et le nœud) ; une coupure WS ferme la session transport, elle ne réécrit pas la politique ni l’historique métier.

Toute PR qui viole l’un de ces principes est refusée, même si elle « simplifie » un cas d’usage.

---

## Architecture cible (rappel)

```text
Admin UI ──WSS──► Apache (TLS, same-origin) ──ws──► hestia-ws-relay (127.0.0.1)
                                                      ▲
Agent ────WSS sortant─────────────────────────────────┘
                                                      │
                                              HTTP loopback
                                                      │
                                              API PHP REST
```

- L’API émet des tickets à courte durée pour Agent et Admin.
- Le relay valide chaque extrémité auprès de l’API (secret local loopback), puis apparie par `session_id`.
- Une fois la paire active, le relay transporte les frames sans les interpréter fonctionnellement.

Détail d’implémentation : AUTO-002E-2 / `services/ws-relay/`.

---

## Conséquences

- Nouveau service systemd sur le VPS, à superviser séparément de PHP-FPM.
- Apache doit proxifier les upgrades WebSocket (same-origin).
- L’Agent gagne un client WS sortant et un handler `shell_session`.
- `core/` reste sans dépendance tierce ; seule la surface REST s’étend (sessions / tickets / validation interne).
- Une dépendance WebSocket est acceptée **uniquement** dans `services/ws-relay/` (ADR-0005 : justification et isolation).
- Sécurité : tickets courts, appariement strict `session_id` ↔ `node_id`, allowlist Agent, audit API.
- Token par nœud (AUTO-002F / ADR-021) reste recommandé avant parc multi-nœuds en production.

---

## Références

- [AUTO-002 — Supervision et administration des nœuds](../architecture/AUTO-002-supervision-administration-noeuds.md) §4.2
- ADR-0005 — Limiter les dépendances externes
- ADR-018 — Architecture agent / passerelle
- ADR-020 — Positionnement Hestia / Home Assistant
- AUTO-001F — Canal de commandes distantes
