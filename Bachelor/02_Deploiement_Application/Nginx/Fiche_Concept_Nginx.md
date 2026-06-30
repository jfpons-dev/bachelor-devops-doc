# Nginx — Fiche Concept (Architecture DevOps)

## Description rapide
Nginx est un serveur web haute performance, reverse proxy et load balancer. Dans une architecture conteneurisée, il se positionne en première ligne devant les services applicatifs (Python, Node.js, etc.) et joue le rôle de point d'entrée unique.

---

## Quand l'utiliser ?

### Cas sans Nginx (inutile)
- **Dev / Test unitaire rapide** : tu testes une API en local avec `docker run -p 3000:3000`. Une couche Nginx ne ferait qu'alourdir le debug.
- **Workers / tâches de fond** : un conteneur qui parse des logs ou exécute des scripts sans exposer de port réseau.
- **Microservices internes** : les conteneurs d'un même réseau Docker se joignent directement par leur nom DNS. Nginx n'a rien à faire ici.

### Cas où Nginx est indispensable
- **Production (normes ANSSI)** : on n'expose jamais un processus brut (Node, Python) directement sur internet. Nginx gère le Rate Limiting et protège contre les attaques DDoS.
- **Centralisation SSL/TLS (HTTPS)** : Nginx porte le certificat (ex : Let's Encrypt), déchiffre le HTTPS et transmet en HTTP interne aux conteneurs. Chaque service n'a plus à gérer les clés.
- **Performances — fichiers statiques** : Nginx sert le HTML/CSS/JS à très haute vitesse, déchargeant ainsi l'application backend.

---

## Avantages en local (poste de développement)

### 1. Parité dev/prod pour les tests E2E (Playwright)
Les cookies sécurisés et certains en-têtes HTTP ne fonctionnent qu'en HTTPS. Avec Nginx local + certificat auto-signé, Playwright teste l'application dans les conditions exactes de la production.

### 2. Éliminer les erreurs CORS
Front sur le port `3000`, back sur le port `8000` → le navigateur bloque les requêtes cross-origin. Nginx écoute sur un port unique et redirige :
- `localhost:8080/` → Frontend
- `localhost:8080/api/` → Backend

Une seule origine, zéro CORS.

### 3. URLs nommées (Virtual Hosts)
En combinant Nginx avec `/etc/hosts`, on remplace `localhost:8080` par des domaines fictifs : `http://mon-app.local`, `http://client1.dev.local`.

---

## Tableau de synthèse

| Scénario local | Nginx ? | Raison |
| :--- | :---: | :--- |
| Dev / test rapide d'un script | ❌ | Routage direct de port (`-p`), simplicité maximale |
| Test E2E complexe (cookies, SSL) | ✅ | Reproduire fidèlement l'environnement cible |
| Projet multi-conteneurs (Front + Back) | ✅ | Port unique, pas de CORS |

---

## Exemple concret — Multi-conteneurs avec Docker Compose

### `nginx.conf`
```nginx
server {
    listen 80;
    server_name mon-app.local;

    # Sert le frontend
    location / {
        proxy_pass http://frontend:3000;
        proxy_set_header Host $host;
    }

    # Redirige les appels API vers le backend
    location /api/ {
        proxy_pass http://backend:8000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
    }
}
```

### `docker-compose.yml`
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
```

> `expose` rend le port visible uniquement au réseau Docker interne (pas sur le Mac). Seul Nginx publie un port vers l'extérieur via `ports`. Voir [[Docker Network#`ports` vs `expose` vs `-p`]].

---

## Liens connexes
- [[Docker-compose]]
- [[Docker Network]]
- [[Volume et Bind Mount]]
- [[Docker Conteneurs]]
- [[Configuration du Poste de Travail]]
