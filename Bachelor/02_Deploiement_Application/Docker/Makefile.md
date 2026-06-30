# 🛠️ Note : Le Makefile (Automatisation des commandes)

## 📌 Concept

Le `Makefile` est un fichier de configuration utilisé par l'utilitaire `make`. Il sert de "raccourci" pour exécuter des séries de commandes longues ou complexes. C'est le standard pour standardiser le workflow dans un projet technique.

## 🚀 Pourquoi l'utiliser ?

1. **Productivité :** Plus besoin de taper des commandes Docker complexes manuellement.
2. **Standardisation :** Toute l'équipe (ou ton futur "toi") utilise les mêmes commandes (`make up`, `make down`).
3. **Documentation vivante :** Le Makefile documente explicitement comment démarrer et gérer le projet.

## 📝 Exemple de structure (`Makefile`)

```makefile
# Raccourcis pour Docker Compose
.PHONY: up down ps shell logs

# Lancer les services
up:
	docker compose up -d

# Stopper les services
down:
	docker compose down

# Vérifier l'état
ps:
	docker compose ps

# Accéder au conteneur
shell:
	docker compose exec mon-app bash

# Voir les logs
logs:
	docker compose logs -f
```

## 💡 Points clés

- **`.PHONY`** : Déclare les noms des commandes pour éviter les conflits avec des dossiers/fichiers réels. Il indique à `make` qu'il doit chercher la commande et non pas un dossier.

- **Tabulation** : **IMPORTANT !** Les commandes sous les cibles (ex: `docker compose up -d`) doivent impérativement être précédées d'une **tabulation** (pas d'espaces).

- **Flexibilité** : Tu peux ajouter n'importe quelle commande shell dans ton Makefile (test, déploiement, nettoyage, etc.).


[[Oiseaux]]
## 🔗 Liens connexes

- [[Docker-compose]]
- [[Docker Conteneurs]]
