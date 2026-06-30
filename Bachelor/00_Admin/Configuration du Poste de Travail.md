

## Variables d'environnement & Shell (Zsh)
Pour rendre une variable persistante, il faut l'ajouter dans le fichier de configuration de Zsh qui est lu à chaque démarrage de l'application iTerm2.

### Rendre gcloud CLI persistant
1. Ouvre le fichier de configuration :
`nano ~/.zshrc`

2. Ajoute cette ligne d'export tout en bas du fichier :
`export PATH="/opt/homebrew/share/google-cloud-sdk/bin:$PATH"`

3. Sauvegarde (`Ctrl+O` puis `Entrée`) et quitte l'éditeur (`Ctrl+X`).

4. Recharge la configuration pour l'appliquer sans fermer le terminal :
`source ~/.zshrc`

**Vérification :**
`which gcloud` // Doit renvoyer le chemin /opt/homebrew/...

## Utilitaires CLI pratiques
### Visualiser l'arborescence (Tree)
Pour l'installer via Homebrew :
`brew install tree`

Pour l'utiliser en ignorant les dossiers techniques (comme .obsidian ou .git) :
`tree -I ".obsidian|.git"`

### Ouvrir le Finder depuis le terminal
Pour ouvrir l'interface graphique directement dans le dossier où tu te trouves :
`open .`