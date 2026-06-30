# Docker — Fiche Concept

> Point d'entrée théorique. Vue d'ensemble : [[Big Picture]]. Pour les commandes, voir [[Docker Images]] et [[Docker Conteneurs]].

---

## VM vs Docker

**Définition :** Docker est une solution de **virtualisation au niveau du système d'exploitation** (conteneurisation), contrairement à la virtualisation matérielle classique utilisée par les machines virtuelles (VM).

**Argumentation technique :**

- **Les VM (Hyperviseur) :** Chaque instance possède son propre noyau (Kernel) et virtualise le matériel (CPU, RAM, disque). C'est une isolation lourde qui entraîne une consommation importante de ressources et des temps de démarrage longs.

- **Docker (Conteneur) :** Docker partage le **noyau Linux de la machine hôte**. Il n'y a pas de virtualisation matérielle. Docker utilise les fonctionnalités natives du noyau (notamment les **Namespaces** pour l'isolation de la vue système et les **Cgroups** pour la limitation des ressources) pour créer des environnements isolés et légers.

**Avantages clés :**

- **Légèreté :** Absence de noyau invité (quelques Mo vs plusieurs Go).
- **Rapidité :** Démarrage instantané (quelques millisecondes).
- **Portabilité :** Le conteneur exécute le même processus indépendamment de l'infrastructure sous-jacente, garantissant une cohérence entre les environnements de développement et de production.

---

## Lexique fondamental

### 1. [[Dockerfile]] (La Recette)

C'est un simple fichier texte qui contient la liste des instructions séquentielles pour construire un environnement de bout en bout.

* **Le pont technique :** C'est l'évolution moderne d'un long script d'installation Bash. En développement, c'est conceptuellement très proche de ton fichier de configuration initial dans Playwright. Il définit l'état de base attendu : quel OS, quels paquets installer, quels ports ouvrir.

### 2. Image Docker (Le Moule)

C'est un modèle en lecture seule (immuable) créé après l'exécution d'un `Dockerfile`. Elle contient le système d'exploitation minimaliste, le code de l'application et l'intégralité de ses dépendances figées dans le temps.

* **Le pont technique :** En administration matérielle, c'est l'exact équivalent d'un "master" ou d'une image Ghost que l'on clonerait sur un parc de postes. En programmation orientée objet, l'Image représente la **Classe**.

Ces images sont stockées sur [[Docker Registries]].

*Voir [[Docker Images]]*

### 3. Conteneur (L'Instance)

C'est un processus isolé et en cours d'exécution, créé à partir d'une Image. Il est éphémère par défaut (s'il s'éteint ou est supprimé, toutes ses données internes non sauvegardées sont détruites).

* **Le pont technique :** Si l'Image est la Classe, le Conteneur est l'**Objet** instancié en mémoire vive. Tu peux faire tourner simultanément des dizaines de conteneurs (objets) parfaitement identiques à partir d'une seule image (classe).

*Voir [[Docker Conteneurs]]*

### 4. [[Docker-compose]] (L'Orchestrateur Local)

C'est un outil (piloté par le fichier `docker-compose.yml`) qui permet de configurer, lier et lancer des applications nécessitant plusieurs conteneurs simultanés en une seule commande.

* **Le pont technique :** Lorsqu'une campagne de tests E2E nécessite qu'une API, un front-end et une base de données tournent en synergie, Compose agit comme un chef d'orchestre. Il s'assure que chaque brique démarre de manière reproductible sur le même sous-réseau virtuel.

### 5. [[Volume et Bind Mount]] (La persistance)

C'est un mécanisme permettant d'externaliser et de sauvegarder les données générées par un conteneur pour survivre à sa destruction.

* **Le pont technique :** C'est grâce aux volumes que l'on extrait les artefacts critiques (comme les rapports HTML ou les traces d'erreurs) après la mort d'un conteneur de test.

### 6. [[Docker Network]] (Le Réseau Isolé)

C'est un réseau virtuel généré par le moteur Docker pour permettre aux conteneurs de communiquer entre eux de manière sécurisée. La grande force de ce réseau est d'intégrer une résolution DNS automatique basée sur le nom du conteneur.

* **Le pont technique :** C'est un VLAN isolé. Si la base de données écoute sur le port `5432` dans ce réseau virtuel, seul le conteneur applicatif pourra y accéder en interne, sécurisant ainsi l'architecture face à l'extérieur.

*Voir [[Docker Network]] pour `ports` vs `expose` et les commandes.*

---

#DevOps #Docker
