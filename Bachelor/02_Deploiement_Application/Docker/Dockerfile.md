# Dockerfile — La Recette

Un Dockerfile est un fichier texte qui liste les instructions pour construire une image Docker. Chaque instruction crée une couche dans l'image finale.

> Voir [[Fiche_Concept_Docker#2. Image Docker (Le Moule)]] pour le lien Image ↔ Dockerfile.

---

## Instructions essentielles

| Instruction | Rôle |
| :--- | :--- |
| `FROM` | Image de base (point de départ obligatoire) |
| `WORKDIR` | Définir le répertoire de travail dans le conteneur |
| `COPY` | Copier des fichiers depuis la machine hôte vers le conteneur |
| `RUN` | Exécuter une commande pendant le build (ex : installer des paquets) |
| `EXPOSE` | Déclarer le port écouté par l'application (documentation) |
| `ENV` | Définir une variable d'environnement |
| `CMD` | Commande lancée au démarrage du conteneur |

---

## Exemple — Application Node.js

Exemple 1
```dockerfile
FROM node:20-alpine

WORKDIR /app

# Copier les dépendances en premier (optimise le cache Docker)
COPY package*.json ./
RUN npm ci --only=production

# Copier le reste du code source
COPY . .

EXPOSE 3000

CMD ["node", "server.js"]
```

Exemple 2 
```dockerfile
FROM node:19-alpine

COPY package.json /app/
COPY src /app/

WORKDIR /app

RUN npm install

CMD ["node", "server.js"]
```

---

## Commandes de build

```bash
docker build -t mon-app:1.0 .   # construire l'image avec un tag
docker run -d -p 3000:3000 mon-app:1.0
docker images                    # lister les images disponibles
```

> Le `.` dans `docker build` désigne le dossier courant comme contexte de build.

---

## Bonnes pratiques

- **`COPY package*.json` avant `COPY . .`** : les couches sont mises en cache. Si le code change mais pas `package.json`, `npm ci` ne se réexécute pas → build plus rapide.
- **Images `alpine`** : `node:20-alpine` est ~5× plus légère que `node:20`. Toujours préférer pour la prod.
- **Un processus par conteneur** : séparer l'API, la BDD et le front en services distincts, orchestrés par [[Docker-compose]].

---
#DevOps #Docker
