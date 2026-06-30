# Debian — Gestion des paquets (APT)

## Opérations courantes

```bash
apt update                  # synchroniser les index des dépôts
apt upgrade                 # mettre à jour tous les paquets installés
apt install -y <paquet>     # installer un paquet (-y évite la confirmation)
apt remove <paquet>         # désinstaller un paquet (garde les configs)
apt purge <paquet>          # désinstaller + supprimer les fichiers de config
apt autoremove              # supprimer les dépendances devenues orphelines
```

## Recherche et information

```bash
apt search <mot-clé>        # chercher un paquet par nom ou description
apt show <paquet>           # afficher les détails d'un paquet
dpkg -l                     # lister tous les paquets installés
dpkg -l | grep <nom>        # filtrer la liste
```

## Dans un conteneur Docker

Les images Docker sont minimales : `apt update` est toujours nécessaire avant d'installer quoi que ce soit, même curl.

```bash
apt update && apt install -y curl wget vim
```

> Voir [[Docker Conteneurs]] pour le cycle de vie des conteneurs.

---
#DevOps #Linux #Debian
