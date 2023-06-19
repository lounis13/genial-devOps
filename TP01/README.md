# TP 01

___
***Lounis BOULDJA    |   TP 01    |     devOps 2022 2023***
___

## Database

- **Création d'un réseau :**
  ```shell
  docker network create app-network
  # docker network ls
  # b35903a8a266   app-network  bridge    local
  ```
  La commande `docker network create app-network` est utilisée pour créer un nouveau réseau. Les conteneurs peuvent communiquer entre eux en utilisant ce réseau.

- **Copie des scripts SQL dans l'image Docker :**
  ```dockerfile
  COPY ./sql-scripts/ /docker-entrypoint-initdb.d/
  ```
  La directive COPY dans le Dockerfile est utilisée pour copier des fichiers ou des répertoires de l'hôte dans l'image Docker. Ici, elle copie tous les scripts SQL de l'hôte du répertoire ./sql-scripts/ vers le répertoire /docker-entrypoint-initdb.d/ dans l'image Docker. Ce répertoire est spécifique aux images Docker de PostgreSQL et tous les scripts SQL et shell qu'il contient sont automatiquement exécutés lors du démarrage du conteneur.

- **Build Image**:
  ```shell
    docker build -t database-image . 
  ```
  La commande `docker build -t database-image .` est utilisée pour construire une image Docker à partir d'un Dockerfile. Ici, `-t` est utilisé pour marquer l'image avec un tag (database-image dans ce cas) pour une identification et une référence plus facile.

- **Afficher les images :** 
  ```shell
    docker image ls
    # REPOSITORY        TAG       IMAGE ID       CREATED      SIZE
    # database-image   latest    ffd4f4d0c027   6 seconds ago   209MB
  ```
  La commande `docker image ls liste` toutes les images Docker qui sont actuellement stockées sur votre machine. Des informations telles que le répertoire, le tag, l'ID de l'image, quand elle a été créée, et sa taille sont affichées.

-  **Démarrage du conteneur de la base de données en utilisant un volume :**
  ```shell
    docker run -d --name database --network app-network -v shared_data:/var/lib/postgresql/data database-image
    # docker ps 
    # CONTAINER ID   IMAGE            COMMAND                  CREATED          STATUS          PORTS      NAMES
    # 27a972335333   database-image   "docker-entrypoint.s…"   17 seconds ago   Up 16 seconds   5432/tcp   database
  ```
  La commande `docker run` est utilisée ici pour démarrer le conteneur de la base de données. Le flag -d exécute le conteneur en arrière-plan et le flag --name donne un nom au conteneur. --network spécifie le réseau (app-network dans ce cas) sur lequel le conteneur doit être exécuté. Le flag -v est utilisé pour monter un volume Docker (shared_data dans ce cas) à un chemin spécifique dans le conteneur (/var/lib/postgresql/data ici). Ceci est utilisé pour la persistance des données - les données stockées dans ce volume persistent même après l'arrêt ou la suppression du conteneur.
  
## Backend API

- **Pourquoi avons-nous besoin d'une construction multistage ?**
  La construction multistage permet de séparer l'environnement de construction du code Java de son environnement d'exécution, chacun ayant des images différentes. Chaque étape du Dockerfile est définie par une image de base, des instructions de copie et d'exécution.- 

- **Mise en cache des dépendances Maven :**
  ```
    RUN mvn dependency:go-offline
  ```
- **Définition de l'URL de la base de données :**
  ```yaml
  url: jdbc:postgresql://database:5432/db
  ```
  Cette propriété définit l'URL de connexion JDBC à la base de données PostgreSQL. Ici, "database" est le nom du conteneur Docker de la base de données qui a été démarré précédemment et est accessible via le réseau "app-network". Le numéro 5432 est le port sur lequel PostgreSQL écoute par défaut.

- **Construction de l'image Docker pour le backend:**
  ```shell
  docker build -t backend-image .
  ```

- **Exécution de l'image dans le réseau app-network :**
  ```shell
  docker run -d --network=app-network --name=backend backend-image
  # docker ps 
  # CONTAINER ID   IMAGE            COMMAND                  CREATED          STATUS          PORTS      NAMES
  # 940e5fe77450   backend-image    "/bin/sh -c 'java -j…"   7 seconds ago    Up 6 seconds               backend
  # 6bd40c2025b6   database-image   "docker-entrypoint.s…"   25 minutes ago   Up 25 minutes   5432/tcp   database
  ```

## Http server

- **Un reverse proxy est nécessaire pour plusieurs raisons :**
  - Sécurité et anonymat : Le proxy inverse masque l'identité des serveurs backend et renforce ainsi la sécurité en empêchant les attaques directes contre vos serveurs.

  - Répartition de charge (load balancing) : Un proxy inverse peut distribuer les demandes entrantes vers plusieurs serveurs backend, contribuant ainsi à équilibrer la charge entre eux et à améliorer les performances.

  - Contrôle et surveillance : Il permet de surveiller et de contrôler le trafic, d'appliquer des politiques, de réaliser des authentifications, des mises en cache, etc.

  - SSL Termination : Dans le cadre de la mise en place d'une connexion sécurisée, le reverse proxy peut gérer la partie SSL (décodage des requêtes SSL), soulageant ainsi les serveurs backend de cette charge.

- **Création d'un Dockerfile pour le serveur HTTP :**
  ```
    FROM httpd:2.4
    COPY ./index.html /usr/local/apache2/htdocs/
    COPY ./httpd.conf /usr/local/apache2/conf/httpd.conf
  ```

- **Construction de l'image Docker pour le serveur HTTP :**
  ```shell
  docker build -t httpd-image .
  ```

- **Exécution de l'image dans le réseau app-network tout en exposant le port 80:**
  ```shell
  docker run -d --network=app-network --name=httpd -p 80:80 httpd-image
  ```

## Docker-compose

- **Commandes importantes de Docker Compose :**
  - `docker-compose up` : Cette commande crée et démarre tous les services définis dans le fichier docker-compose.yml. Si vous ajoutez le drapeau -d, les services seront démarrés en arrière-plan (mode détaché).

  - `docker-compose down` : Cette commande arrête et supprime tous les services définis dans le fichier docker-compose.yml. Il est souvent utilisé en combinaison avec docker-compose up pour s'assurer que vous travaillez avec une version fraîche de l'environnement.

  - `docker-compose ps` : Cette commande liste tous les services actuellement en cours d'exécution.

  - `docker-compose build` : Cette commande construit (ou reconstruit) les services. Il est utile si vous avez modifié le Dockerfile et que vous voulez mettre à jour l'image.

  - `docker-compose logs` : Cette commande affiche les journaux des conteneurs. Vous pouvez spécifier le nom d'un service pour n'afficher que ses journaux.


- **Explication du fichier docker-compose.yml :**
  - backend : ce service construit une image à partir du Dockerfile dans le répertoire ./backend/simple-api-student et dépend du service database. Il est également connecté au réseau app-network.
  - database : ce service construit une image à partir du Dockerfile dans le répertoire ./database et monte un volume pour la persistance des données. Il est également connecté au réseau app-network.
  - httpd : ce service construit une image à partir du Dockerfile dans le répertoire ./httpd. Il expose le port 80 et dépend du service backend. Il est également connecté au réseau app-network.
  - adminer : ce service utilise l'image Docker officielle d'Adminer. Il expose le port 8090 et dépend du service database. Il est également connecté au réseau app-network.

  Le fichier définit également un réseau, appelé app-network. Tous les services de votre application communiquent sur ce réseau. 
  
  - Pour executer le docker compose :
  ```shell
    docker-compose up
  ```

## Publication
- **Commandes pour publier les images :**
  - Connexion au registre Docker :
    ```shell
      docker login
    ```
  - Publication des images
    ```shell
    docker tag database-image DOCKERHUB_USERNAME/database-image
    docker push DOCKERHUB_USERNAME/database-image
    
    docker tag backend-image DOCKERHUB_USERNAME/backend-image
    docker push DOCKERHUB_USERNAME/backend-image
    ```


<footer style="margin-top: 2rem; width: 95%; display: flex; align-items: center; justify-content: space-between; box-shadow: rgba(0, 0, 0, 0.05) 0 6px 24px 0, rgba(0,0,0,0.06) 0 0 0 1px; border-radius: .5rem; padding: .4rem 1rem; background-color: rgba(255,255,255,0.07)">
    <b>👨‍💻 Lounis BOULDJA</b>
    <img alt="devOps" height="48" src="https://www.plunge.cloud/hs-fs/hubfs/cycle-devopsea2b.png?width=600&name=cycle-devops.png">
    <b><i>GENIAL</i>  🎓 2022 - 2023</b>
</footer>