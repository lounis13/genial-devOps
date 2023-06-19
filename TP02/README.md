# TP 02

___
***Lounis BOULDJA    |   TP 02    |     devOps 2022 2023***
___

## Testcontainers
Est une bibliothèque Java qui permet de gérer des conteneurs Docker dans des tests d'intégration JUnit.

L'idée principale derrière Testcontainers est de pouvoir lancer des instances de bases de données, de serveurs web, de serveurs de messagerie, etc., dans des conteneurs Docker, et de les utiliser dans des tests d'intégration. Une fois les tests terminés, ces conteneurs sont automatiquement arrêtés et supprimés.

Cela permet de garantir que les tests sont effectués dans un environnement propre et isolé, et cela rend plus facile l'automatisation de l'ensemble du processus de test.

Voici quelques exemples de ce que vous pouvez faire avec Testcontainers :

- Lancer une base de données PostgreSQL dans un conteneur Docker et utiliser cette base de données dans vos tests d'intégration.
- Lancer un serveur Selenium dans un conteneur Docker pour effectuer des tests d'interface utilisateur automatisés.
- Lancer une instance de Apache Kafka dans un conteneur Docker pour tester l'intégration avec Kafka.

## CI (ci.yml)

- Le nom du workflow GitHub Actions est défini avec `name: ci`.

- Ce workflow est déclenché chaque fois que des modifications sont poussées sur les branches `master` et `develop`, ainsi qu'à chaque ouverture d'une pull request. C'est spécifié par `on`.

- Le répertoire de travail par défaut pour tous les jobs est défini avec `defaults`. Dans ce cas, le répertoire de travail est `./TP01/backend/simple-api-student`.

- Un job appelé `test-backend` est défini qui s'exécute sur un environnement Ubuntu 22.04, comme spécifié par `runs-on: ubuntu-22.04`.

- Le job contient plusieurs étapes. La première étape utilise l'action `actions/checkout@v2.5.0` pour récupérer le code source du dépôt GitHub sur la machine d'exécution.

- Ensuite, une étape est définie pour configurer JDK 17. Pour cela, l'action `actions/setup-java@v3` est utilisée avec quelques paramètres spécifiques : la distribution de Java est `temurin`, la version de Java est `17`, et la mise en cache est activée pour les dépendances Maven avec `cache: 'maven'`.

## La configuration du Quality Gate 

- Inscription à SonarCloud : Il est nécessaire de créer un compte gratuit sur SonarCloud, qui est un outil d'analyse de code en ligne. SonarCloud aide à maintenir la qualité du code en identifiant les problèmes potentiels et en fournissant des rapports détaillés.
- Création d'une organisation : Après s'être inscrit à SonarCloud, il faut créer une nouvelle organisation. Cette organisation servira de conteneur pour tous les projets analysés.
- Récupération des clés de projet et d'organisation : Lors de la création d'un nouveau projet au sein de l'organisation, SonarCloud génère des clés de projet et d'organisation. Ces clés seront nécessaires pour la configuration ultérieure.
- Ajout du script SonarCloud à main.yml : Le script suivant doit être ajouté à main.yml. Ce script exécutera l'analyse SonarCloud à chaque commit.
  ```shell
      mvn -B verify sonar:sonar -Dsonar.projectKey=devops-2023 -Dsonar.organization=devops-school -Dsonar.host.url=https://sonarcloud.io -Dsonar.login=${{ secrets.SONAR_TOKEN }}  --file ./simple-api/pom.xml
   ```
  Cette commande déclenche un build avec Maven, puis exécute l'analyse SonarCloud. Les clés de projet et d'organisation sont spécifiées avec les options -Dsonar.projectKey et -Dsonar.organization respectivement. L'URL de SonarCloud est spécifiée avec -Dsonar.host.url, et le token d'authentification avec -Dsonar.login. Le token d'authentification est stocké comme un secret GitHub, et est injecté à l'aide de la syntaxe ${{ secrets.SONAR_TOKEN }}.
- Vérification de l'analyse SonarCloud : Si la configuration a été correctement effectuée, il devrait être possible de voir le rapport d'analyse de SonarCloud après chaque commit.