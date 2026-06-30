# Docker Registries

Un **registry** est un dépôt distant qui stocke et distribue les images Docker. C'est l'équivalent d'un npm registry, mais pour les images.

![[Pasted image 20260630114554.png]]

---

## Oui — il faut un service dédié

On ne peut **pas** stocker une image Docker sur n'importe quel site web ou dans un repo Git classique. Il faut un **registry** : une plateforme spécialisée qui expose une API pour `push` / `pull`.

```
Image locale  ──docker push──►  Registry (service dédié)  ──docker pull──►  Autre machine
```

| Ce que ce n'est **pas** | Ce que c'est |
| :--- | :--- |
| Un dossier sur ton Mac | Un service hébergé (`hub.docker.com`, `ghcr.io`…) |
| Un repo Git (`github.com/user/app`) | Un registry lié au code (`ghcr.io/user/app`) |
| Un serveur FTP / site vitrine | Un moteur qui gère versions, tags, droits d'accès |

Chaque registry a sa **propre URL**. Docker doit savoir **où** aller :

```bash
docker pull nginx:1.23                    # Docker Hub par défaut
docker pull ghcr.io/owner/app:1.0         # GitHub Container Registry
docker pull europe-west1-docker.pkg.dev/… # Google Artifact Registry
```

> **Repo Git ≠ Registry.** GitHub stocke le *code* ; pour les *images*, il faut passer par `ghcr.io` (ou un autre registry). Même logique : AWS a CodeCommit (code) et ECR (images).

### Les registries du Bachelor

| Site dédié | URL registry | Gratuit / courant en dev |
| :--- | :--- | :--- |
| **Docker Hub** | `docker.io` (implicite) | ✅ Oui |
| **GitHub Packages** | `ghcr.io` | ✅ Oui (avec compte GitHub) |
| **Google Artifact Registry** | `*.pkg.dev` | Projets cloud |
| **AWS ECR** | `*.amazonaws.com` | Projets cloud |

---

## Docker Hub (par défaut)

Le plus gros et celui par défaut : https://hub.docker.com/

```bash
docker pull nginx:1.23              # image publique
docker pull mon-user/mon-app:1.0    # image perso (publique ou privée)
```

| Visibilité | Qui peut `pull` ? |
| :--- | :--- |
| **Publique** | Tout le monde |
| **Privée** | Utilisateurs autorisés après `docker login` |

---

## GitHub Container Registry (`ghcr.io`)

Les images Docker peuvent être stockées **à côté du code** sur GitHub, dans **GitHub Packages**.

> **Repo GitHub** = le code source (`.git`)  
> **ghcr.io** = les images Docker buildées à partir de ce code

### Nommage d'une image

```
ghcr.io/<owner>/<image>:<tag>

# Exemples
ghcr.io/jfpons/mon-app:1.0
ghcr.io/jfpons/mon-app:latest
```

L'image est souvent liée à un repo `github.com/jfpons/mon-app`, mais le nom n'est pas obligatoirement identique.

### Visibilité (public vs private)

| Type d'image | Comportement |
| :--- | :--- |
| **Publique** | `docker pull` sans authentification |
| **Privée** | Liée aux droits du repo GitHub — seuls les collaborateurs autorisés peuvent pull/push |

La visibilité se règle dans GitHub : **Packages** → ton package → **Package settings** → *Change visibility*.

![[Pasted image 20260630154105.png]]

---

## Authentification — repo privé

Pour pull ou push une image privée sur `ghcr.io`, il faut s'authentifier avec un **Personal Access Token (PAT)**.

### 1. Créer un token GitHub

GitHub → **Settings** → **Developer settings** → **Personal access tokens**

Scopes nécessaires :
- `read:packages` — télécharger une image privée
- `write:packages` — publier une image
- `delete:packages` — supprimer (optionnel)

![[Pasted image 20260630154714.png]]

### 2. Se connecter au registry

```bash
# Username = ton identifiant GitHub (pas l'email)
# Password = le PAT (pas ton mot de passe GitHub)
docker login ghcr.io -u <ton-user-github>
```

### 3. Pull une image privée

```bash
docker pull ghcr.io/<owner>/<image>:<tag>
```

### 4. Push une image vers un repo privé

```bash
docker build -t mon-app:1.0 .
docker tag mon-app:1.0 ghcr.io/<owner>/mon-app:1.0
docker push ghcr.io/<owner>/mon-app:1.0
```

> Le package apparaît ensuite dans l'onglet **Packages** du repo GitHub.

---

## Autres registries (cloud / entreprise)

| Registry | Site / URL | Usage |
| :--- | :--- | :--- |
| **Google Artifact Registry** | `console.cloud.google.com` | Projets GCP / GKE |
| **AWS ECR** | `console.aws.amazon.com/ecr` | Projets AWS |
| **Harbor** (self-hosted) | Ton propre serveur | Entreprise, air-gapped |

---

## Commandes utiles (rappel)

```bash
docker login ghcr.io          # s'authentifier (GitHub)
docker login                  # s'authentifier (Docker Hub)
docker tag <source> <cible>   # préparer le nom pour le registry
docker push <image>           # publier
docker pull <image>           # télécharger
```

---

## Liens connexes

- [[Docker Images]] — `pull`, `images`, `rmi`
- [[Dockerfile]] — construire l'image avant le push
- [[Big Picture]] — où le registry s'inscrit dans le flux global

---
#DevOps #Docker
