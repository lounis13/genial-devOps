# TP 01

--- 

## Database

### Basics

- Build Image

```shell
    docker build -t database .
```

- Images

```shell
    docker image ls
    # REPOSITORY        TAG       IMAGE ID       CREATED      SIZE
    # postgres_db_img   14.1      89ef1a2c0745   6 days ago   209MB
```

- Run image

```shell
    docker run -d -p 5432:5432 --name database database
    # e0fe584199afb2b630eca8a9eb920bd293159307793a48e96930a44542fc8de2
```

- Containers

```shell
    docker ps
    # CONTAINER ID   IMAGE      COMMAND                  CREATED          STATUS          PORTS                    NAMES
    # cf46a3887b19   database   "docker-entrypoint.s…"   24 seconds ago   Up 23 seconds   0.0.0.0:5432->5432/tcp   database

```

### Init database

- Create network

```shell
  docker network create app-network
  # docker network ls
  # b35903a8a266   app-network                           bridge    local
```

- Start adminer

```shell
    docker run \
    -p "8090:8080" \
    --net=app-network \
    --name=adminer \
    -d \
    adminer

```

- Why should we run the container with a flag -e to give the environment variables?
    - L'utilisation du drapeau -e lors de l'exécution d'un conteneur Docker permet de définir des variables
      d'environnement au sein de ce conteneur. C'est particulièrement utile pour les paramètres de configuration qui
      peuvent varier entre les environnements (par exemple, entre le développement, les tests et la production).
    - Dans le contexte de notre Dockerfile, il serait préférable de définir des variables telles que `POSTGRES_USER`
      et `POSTGRES_PASSWORD` lors de l'exécution du conteneur plutôt que dans le Dockerfile lui-même. La raison est que
      la valeur des variables d'environnement définies dans le Dockerfile est intégrée à l'image Docker, ce qui signifie
      qu'elles sont les mêmes pour tous les conteneurs basés sur cette image. En revanche, les variables d'environnement
      définies avec `-e` lors de l'exécution du conteneur peuvent varier d'un conteneur à l'autre.
    - En outre, il est généralement préférable de ne pas inclure de données sensibles (comme des mots de passe) dans une
      image Docker pour des raisons de sécurité.
  ```shell
    docker run -d -p 5432:5432 -e POSTGRES_USER=usr -e POSTGRES_PASSWORD=pwd --name database database
```


### Persist data

## Backend API


## Http server

## Link application

## Publish