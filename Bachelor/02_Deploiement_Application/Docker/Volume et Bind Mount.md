# Volume et Bind Mount — La persistance

Par défaut, les données créées dans un conteneur disparaissent quand il est supprimé. Les volumes permettent de conserver ces données au-delà du cycle de vie du conteneur.

> Voir [[Fiche_Concept_Docker#5. Volume et Bind Mount (La persistance)]] pour le concept.

---

## Deux mécanismes distincts

| Type | Syntaxe | Géré par | Cas d'usage |
| :--- | :--- | :--- | :--- |
| **Bind mount** | `./dossier_mac:/chemin/conteneur` | Toi (chemin explicite sur le Mac) | Config locale (`nginx.conf`), code en dev, artefacts de test |
| **Volume nommé** | `nom_volume:/chemin/conteneur` | Docker | Données de BDD, fichiers dont tu ne veux pas gérer le chemin |

---

## Bind mount — exemple

```bash
# Monter un dossier local dans le conteneur
docker run -d -p 8080:80 \
  -v ./nginx.conf:/etc/nginx/conf.d/default.conf \
  nginx:alpine
```

Dans `docker-compose.yml` :

```yaml
services:
  nginx:
    image: nginx:alpine
    volumes:
      - ./nginx.conf:/etc/nginx/conf.d/default.conf   # bind mount
```

> Toute modification du fichier sur le Mac est immédiatement visible dans le conteneur.

---

## Volume nommé — exemple

```yaml
services:
  db:
    image: postgres:16-alpine
    volumes:
      - postgres_data:/var/lib/postgresql/data   # volume nommé

volumes:
  postgres_data:    # déclaré ici, géré par Docker
```

```bash
docker volume ls              # lister les volumes
docker volume inspect postgres_data
docker compose down -v        # supprimer conteneurs + volumes nommés
```

---

## Bonnes pratiques

- **BDD en prod** → volume nommé (Docker gère l'emplacement, persistance garantie).
- **Fichiers de config** → bind mount (tu édites directement depuis ton éditeur).
- **Rapports de test Playwright** → bind mount vers un dossier `./reports` sur le Mac pour récupérer les artefacts après `docker compose down`.

---

## Liens connexes

- [[Docker-compose]]
- [[Fiche_Concept_Nginx#Exemple concret — Multi-conteneurs avec Docker Compose]]

---
#DevOps #Docker
