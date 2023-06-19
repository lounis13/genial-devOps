# TP 02

___
***Lounis BOULDJA    |   TP 02    |     devOps 2022 2023***
___

## Qu'est-ce que les Testcontainers ?
Les Testcontainers sont une bibliothèque Java qui permet de gérer des conteneurs Docker dans des tests JUnit. C'est comme avoir des environnements d'exécution (comme une base de données PostgreSQL) encapsulés dans des conteneurs Docker, qui démarrent et s'arrêtent automatiquement pendant les tests. C'est un outil essentiel pour des tests d'intégration robustes et reproductibles.

## Automatisation du Cycle de Développement avec GitHub Actions
Voici une explication détaillée du processus automatisé avec trois [workflows](..%2F.github%2Fworkflows):

- [ci.yml](..%2F.github%2Fworkflows%2Fci.yml) : Cette action GitHub s'exécute lorsqu'un événement "push" ou une "pull request" est déclenchée sur les branches "master" ou "develop". Il s'agit du premier niveau de l'automatisation, qui exécute les tests de l'application en utilisant Maven et analyse le code source avec SonarCloud.

- [backend-build-and-push.yml](..%2F.github%2Fworkflows%2Fbackend-build-and-push.yml) : Ce workflow est déclenché à la suite du workflow "ci", uniquement s'il est terminé avec succès et seulement sur la branche "master". Ce niveau d'automatisation prend en charge la connexion à DockerHub grâce aux identifiants de l'utilisateur, puis il construit l'image Docker pour le backend de l'application à partir du Dockerfile et la pousse vers DockerHub. C'est donc une étape dépendante du succès du workflow précédent.

- [db-http-build-and-push.yml](..%2F.github%2Fworkflows%2Fdb-http-build-and-push.yml) : Ce workflow est déclenché indépendamment de la réussite des workflows précédents, et cela lors d'un événement "push" sur la branche "master". Il réalise une connexion à DockerHub, construit les images Docker pour la base de données et le serveur httpd de l'application à partir de leurs Dockerfiles respectifs, et les pousse vers DockerHub.

Ainsi, ces workflows assurent une automatisation du processus de test, de build et de déploiement de l'application. Le second workflow est particulièrement intéressant, car il crée une dépendance de réussite avec le premier, garantissant ainsi l'intégrité et la fiabilité du code avant sa mise en production.


<footer style="margin-top: 2rem; width: 95%; display: flex; align-items: center; justify-content: space-between; box-shadow: rgba(0, 0, 0, 0.05) 0 6px 24px 0, rgba(0,0,0,0.06) 0 0 0 1px; border-radius: .5rem; padding: .4rem 1rem; background-color: rgba(255,255,255,0.07)">
    <b>👨‍💻 Lounis BOULDJA</b>
    <img alt="devOps" height="48" src="https://www.plunge.cloud/hs-fs/hubfs/cycle-devopsea2b.png?width=600&name=cycle-devops.png">
    <b><i>GENIAL</i>  🎓 2022 - 2023</b>
</footer>