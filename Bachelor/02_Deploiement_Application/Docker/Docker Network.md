# Docker Network — Le réseau isolé

Docker crée un réseau virtuel pour que les conteneurs communiquent entre eux sans exposer chaque service sur le Mac.

> Voir [[Fiche_Concept_Docker#6. Docker Network (Le Réseau Isolé)]] pour le concept.

---

## Principe

- Chaque conteneur reçoit une **adresse IP interne** sur un réseau Docker dédié.
- Docker fournit un **DNS intégré** : un conteneur peut joindre un autre par son **nom de service** (`backend`, `db`, `nginx`…).
- C'est un **VLAN isolé** : la BDD sur le port `5432` n'est pas accessible depuis ton Mac, seulement depuis les autres conteneurs du même réseau.

```
Mac (hôte)
  │
  │  ports: "8080:80"  ← seul point d'entrée vers l'extérieur
  ▼
┌─────────────────────────────────────┐
│  Réseau Docker (bridge)           │
│                                     │
│  nginx ──► frontend:3000            │
│    │                                │
│    └──► backend:8000 ──► db:5432   │
└─────────────────────────────────────┘
```

---

## `ports` vs `expose` vs `-p`

| Mécanisme | Où | Visible depuis le Mac ? | Usage |
| :--- | :--- | :---: | :--- |
| `ports` (Compose) | `docker-compose.yml` | ✅ | Publier un service vers l'extérieur (ex : Nginx sur `8080`) |
| `expose` (Compose) | `docker-compose.yml` | ❌ | Déclarer un port **interne** au réseau Docker |
| `-p` (`docker run`) | ligne de commande | ✅ | Équivalent de `ports` en mode `run` |

```yaml
services:
  backend:
    expose:
      - "8000"          # accessible via http://backend:8000 depuis un autre conteneur

  nginx:
    ports:
      - "8080:80"       # accessible via http://localhost:8080 depuis le Mac
```

> Exemple complet avec Nginx : [[Fiche_Concept_Nginx#Exemple concret — Multi-conteneurs avec Docker Compose]].

---

## Résolution DNS par nom de service

Dans Compose, le nom du service devient un hostname DNS :

```yaml
environment:
  - DATABASE_URL=postgres://user:password@db:5432/myapp
#                                          ^^
#                              nom du service, pas localhost
```

Dans `nginx.conf` :

```nginx
proxy_pass http://backend:8000;   # pas localhost:8000
```

**Erreur fréquente :** utiliser `localhost` dans un conteneur pointe vers **le conteneur lui-même**, pas vers un autre service.

---

## Commandes essentielles

```bash
docker network ls                          # lister les réseaux
docker network inspect <nom_reseau>        # voir les conteneurs connectés et leurs IP
docker network create mon-reseau           # créer un réseau custom
docker network rm mon-reseau               # supprimer un réseau

# Lancer un conteneur sur un réseau précis
docker run -d --name api --network mon-reseau mon-app:1.0

# Connecter un conteneur existant à un réseau
docker network connect mon-reseau <conteneur>
```

### Tester la connectivité entre conteneurs

```bash
# Depuis le conteneur nginx, vérifier que le backend répond
docker compose exec nginx ping backend
docker compose exec nginx curl http://backend:8000/health
```

---

## Avec Docker Compose

Par défaut, Compose crée un réseau nommé `<dossier>_default`. Tous les services du fichier y sont connectés automatiquement.

```yaml
services:
  backend:
    build: ./backend
    expose:
      - "8000"
    depends_on:
      - db

  db:
    image: postgres:16-alpine

# Réseau custom (optionnel)
networks:
  frontend_net:
  backend_net:

services:
  nginx:
    networks:
      - frontend_net
  backend:
    networks:
      - frontend_net
      - backend_net
  db:
    networks:
      - backend_net    # db isolée du frontend
```

> `depends_on` attend le **démarrage** du conteneur, pas que le service soit prêt (la BDD peut mettre quelques secondes à accepter les connexions).

---

## Types de réseaux

| Driver | Comportement | Cas d'usage |
| :--- | :--- | :--- |
| **bridge** (défaut) | Réseau isolé avec DNS interne | Dev local, Compose |
| **host** | Partage la stack réseau du Mac | Perf max, plus d'isolation |
| **none** | Aucune connectivité réseau | Conteneur totalement isolé |

```bash
docker run --network host nginx    # écoute directement sur les ports du Mac
docker run --network none alpine   # pas de réseau
```

---

## Bonnes pratiques

- **Ne publier que la porte d'entrée** (souvent Nginx) via `ports`. Garder BDD et API en `expose` uniquement.
- **Utiliser les noms de service** dans les URLs internes (`db`, `backend`), jamais `localhost`.
- **Segmenter les réseaux** en prod pour isoler la BDD des services frontaux (réseaux Compose multiples).

---

## Liens connexes

- [[Docker-compose]]
- [[Docker Conteneurs#Mode détaché (`-d` ou `--detach`)]]
- [[Fiche_Concept_Nginx]]
- [[Volume et Bind Mount]]

---
#DevOps #Docker
