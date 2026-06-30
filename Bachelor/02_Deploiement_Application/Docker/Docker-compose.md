# Docker Compose — L'Orchestrateur Local

Docker Compose pilote plusieurs conteneurs via un fichier `docker-compose.yml`. Une seule commande démarre toute l'architecture de façon reproductible.

> Voir [[Fiche_Concept_Docker#4. Docker-compose (L'Orchestrateur Local)]] pour le concept.

## Commandes essentielles

```bash
docker compose up -d              # démarrer tous les services en arrière-plan
docker compose down               # arrêter et supprimer les conteneurs
docker compose down -v            # idem + supprimer les volumes nommés
docker compose logs -f            # suivre les logs de tous les services
docker compose logs backend       # logs d'un service spécifique
docker compose ps                 # état des services
docker compose exec backend bash  # ouvrir un shell dans un service actif
docker compose build              # reconstruire les images sans démarrer
docker compose restart backend    # redémarrer un service sans tout couper
```


---

## Structure d'un `docker-compose.yml`

```yaml
services:
  <nom_service>:
    image: <image>                      # OU build: <chemin_vers_le_dockerfile>
    ports:
      - "<port_mac>:<port_conteneur>"   # publié sur le Mac
    expose:
      - "<port>"                        # visible uniquement dans le réseau Docker interne
    volumes:
      - "<dossier_mac>:<dossier_conteneur>"
    environment:
      - NOM_VARIABLE=valeur
    depends_on:
      - <autre_service>
```

---

## Exemple complet — Nginx + Front + Back + Base de données

```yaml
services:
  nginx:
    image: nginx:alpine
    ports:
      - "8080:80"
    volumes:
      - ./nginx.conf:/etc/nginx/conf.d/default.conf
    depends_on:
      - frontend
      - backend

  frontend:
    build: ./frontend
    expose:
      - "3000"

  backend:
    build: ./backend
    expose:
      - "8000"
    environment:
      - DATABASE_URL=postgres://user:password@db:5432/myapp
    depends_on:
      - db

  db:
    image: postgres:16-alpine
    volumes:
      - postgres_data:/var/lib/postgresql/data
    environment:
      - POSTGRES_USER=user
      - POSTGRES_PASSWORD=password
      - POSTGRES_DB=myapp

volumes:
  postgres_data:    # volume nommé géré par Docker (persiste entre les restarts)
```

> Pour la config Nginx correspondante, voir [[Fiche_Concept_Nginx#Exemple concret — Multi-conteneurs avec Docker Compose]]. Pour les volumes, voir [[Volume et Bind Mount]]. Pour `ports` vs `expose` et le DNS interne, voir [[Docker Network]].

---


---
#DevOps #Docker
