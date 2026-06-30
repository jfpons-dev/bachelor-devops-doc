# Docker — Big Picture

> Vue d'ensemble avant de plonger dans le détail. Hub du thème : [[Docker]].

![[Pasted image 20260630160332.png]]

---

## Le flux en une phrase

Tu **écris une recette** (`Dockerfile`), tu **fabriques un moule** (image), tu **stockes ou récupères** ce moule (registry), tu **lances des instances** (conteneurs), et **Compose** les relie via **réseau** et **volumes** pour former une application complète.

---

## Pipeline build → run

```
Dockerfile  ──build──►  Image  ──push/pull──►  Registry
                            │
                         docker run
                            ▼
                        Conteneur
```

| Étape | Objet | Note |
| :--- | :--- | :--- |
| 1. Écrire | `Dockerfile` | [[Dockerfile]] |
| 2. Construire | Image locale | [[Docker Images]] |
| 3. Distribuer | Registry (Docker Hub…) | [[Docker Registries]] |
| 4. Exécuter | Conteneur | [[Docker Conteneurs]] |

---

## Architecture multi-services (Compose)

Quand une app = plusieurs conteneurs, **Docker Compose** orchestre le tout en une commande (`docker compose up`).

```
Mac (localhost:8080)
        │
        ▼ ports
    ┌─────────┐
    │  Nginx  │  ← point d'entrée unique ([[Fiche_Concept_Nginx]])
    └────┬────┘
         │ réseau Docker interne ([[Docker Network]])
    ┌────┴────┐
    ▼         ▼
 Frontend   Backend ──► DB
              │
         volumes ([[Volume et Bind Mount]])
```

| Brique | Rôle | Note |
| :--- | :--- | :--- |
| **Compose** | Chef d'orchestre | [[Docker-compose]] |
| **Network** | DNS interne, isolation (`expose` vs `ports`) | [[Docker Network]] |
| **Volumes** | Données qui survivent au conteneur | [[Volume et Bind Mount]] |
| **Nginx** | Reverse proxy, SSL, CORS | [[Fiche_Concept_Nginx]] |
| **Makefile** | Raccourcis (`make up`, `make down`) | [[Makefile]] |

---

## Carte des concepts

| Concept | Analogie | Commande / fichier | Note |
| :--- | :--- | :--- | :--- |
| Dockerfile | Recette | `docker build` | [[Dockerfile]] |
| Image | Moule (classe) | `docker pull`, `docker images` | [[Docker Images]] |
| Conteneur | Instance (objet) | `docker run`, `docker ps` | [[Docker Conteneurs]] |
| Registry | Dépôt d'images | `docker push` | [[Docker Registries]] |
| Compose | Orchestrateur | `docker-compose.yml` | [[Docker-compose]] |
| Network | VLAN isolé | `expose`, `ports`, `-p` | [[Docker Network]] |
| Volume | Persistance | bind mount / volume nommé | [[Volume et Bind Mount]] |

> Théorie détaillée (VM vs Docker, lexique) : [[Fiche_Concept_Docker]]

---

## Parcours de lecture recommandé

1. [[Fiche_Concept_Docker]] — Comprendre *pourquoi* Docker
2. [[Docker Images]] + [[Docker Conteneurs]] — Premières commandes
3. [[Dockerfile]] — Construire ta propre image
4. [[Docker-compose]] — Lier plusieurs services
5. [[Docker Network]] + [[Volume et Bind Mount]] — Réseau et persistance
6. [[Fiche_Concept_Nginx]] — Architecture prod-like en local
7. [[Makefile]] — Automatiser le workflow

---

## Rappels clés

- **Image ≠ conteneur** : le moule est immuable, l'instance est éphémère.
- **`localhost` dans un conteneur** = le conteneur lui-même, pas un autre service → utiliser le **nom de service** (`backend`, `db`).
- **Un seul `ports` vers l'extérieur** (souvent Nginx) ; le reste en `expose` interne.
- **Les données importantes** sortent du conteneur via des **volumes**.

---
#DevOps #Docker
