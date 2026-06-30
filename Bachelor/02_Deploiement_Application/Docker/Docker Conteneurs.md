# Docker Conteneurs — Commandes

> Voir [[Fiche_Concept_Docker#3. Conteneur (L'Instance)]] pour le concept.

---

## Lancer un conteneur

### Lancer une image
```bash
docker run debian:11
```

> [!NOTE] On n'est pas obligé de faire un ```docker pull```, la commande ```docker run``` le fera automatiquement si besoin
> 
### Mode interactif (jetable)

```bash
docker run -it debian:11
```
Le conteneur s'arrête à la sortie (`exit`). L'image source reste intacte.

### Mode détaché (`-d` ou `--detach`)
Pour faire tourner un service sans bloquer le terminal :
```bash
docker run -d -p <port_mac>:<port_conteneur> <image>
# Exemple :
docker run -d -p 8080:80 nginx
```

> `-p` publie un port du conteneur vers le Mac. Voir [[Docker Network#`ports` vs `expose` vs `-p`]].

### Exécuter une commande puis supprimer le conteneur (--rm)
```bash
docker run --rm mon-debian-custom curl https://google.com
```

**Remarque :** Ici on a lancé une image custom, exécuté une commande et nettoyé le conteneur.

### Nommer un conteneur (--name)
```docker run --name web-app -d -p 9000:80 nginx:1.23```


---

## Gérer les conteneurs existants

### Relancer ou accéder à un conteneur arrêté
```bash
docker start <id_ou_nom>
docker exec -it <id_ou_nom> bash

# Raccourci : start + attach en une seule commande
docker start -i <id_ou_nom>
```

### Lister les conteneurs
```bash
docker ps       # conteneurs actifs uniquement
docker ps -a    # tous les conteneurs, y compris arrêtés
```

### Voir les logs
```bash
docker logs IDCONTAINER
```

---

## À l'intérieur d'un conteneur

### Gestion des paquets (Debian/Ubuntu)
```bash
apt update            # synchroniser les index des dépôts
apt install -y curl   # -y évite la confirmation interactive
```

> Voir aussi [[Debian]] pour le détail des commandes APT.

### Quitter un conteneur
```bash
exit
```

> Les modifications faites à l'intérieur (paquets installés, fichiers créés) sont isolées dans ce conteneur. L'image d'origine reste intacte.

---

## Maintenance et nettoyage

```bash
docker system df           # espace disque utilisé par Docker
docker system prune        # supprime conteneurs arrêtés + images orphelines
```

> Pour supprimer les images non utilisées : voir [[Docker Images#Supprimer des images]].

---

## Liens connexes

- [[Docker Images]] — Télécharger et gérer les images
- [[Docker-compose]] — Orchestrer plusieurs conteneurs
- [[Docker Network]] — Réseau isolé, DNS, ports
- [[Volume et Bind Mount]] — Persister les données

---
#DevOps #Docker
