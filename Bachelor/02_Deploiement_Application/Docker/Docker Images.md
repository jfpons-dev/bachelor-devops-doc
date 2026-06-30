# Docker Images — Commandes

> Voir [[Fiche_Concept_Docker#2. Image Docker (Le Moule)]] pour le concept.

---

## Télécharger une image

Pour récupérer une image depuis un [[Docker Registries]] :

Exemple pour [[Fiche_Concept_Nginx]] :
```bash
docker pull nginx:1.23   # image:tag
```

## Lister les images locales

```bash
docker images
```

## Construire une image custom

```bash
docker build -t mon-app:1.0 .
```

> Voir [[Dockerfile]] pour la syntaxe du fichier de build.

## Supprimer des images

```bash
docker rmi debian:11              # supprimer une image spécifique
docker system prune -a            # supprimer toutes les images non utilisées
```

---

## Liens connexes

- [[Docker Conteneurs]] — Lancer et gérer les conteneurs à partir d'une image
- [[Docker Registries]]
- [[Dockerfile]]

---
#DevOps #Docker
