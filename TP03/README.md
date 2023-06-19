# TP 03

___
***Lounis BOULDJA    |   TP 03    |     devOps 2022 2023***
___


## 3-1 Inventaire et Commandes de Base
On crée un inventaire spécifique à Ansible ([ansible/inventories/setup.yml](./ansible/inventories/setup.yml)), qui renseigne l'utilisateur SSH (dans ce cas, centos), le chemin vers la clé privée SSH. On peut vérifier la connectivité avec ces hôtes grâce à la commande `ansible all -i inventories/setup.yml -m ping`.

On utilise ensuite les "faits" (facts) pour recueillir des informations sur l'hôte, par exemple pour obtenir la distribution de l'OS avec `ansible all -i inventories/setup.yml -m setup -a "filter=ansible_distribution*"`.

De plus, Ansible permet de définir l'état souhaité de l'application. Par exemple, pour supprimer le serveur Apache httpd, on utilise `ansible all -i inventories/setup.yml -m yum -a "name=httpd state=absent" --become`.

## [3-2 Playbooks](ansible%2Fplaybook.yml)
Dans le playbook avancé, on utilise une série de rôles pour organiser et déployer les différentes parties de l'application. Chaque rôle correspond à une fonctionnalité ou à un processus spécifique. Voici les rôles utilisés :

[docker](./ansible/roles/docker) : ce rôle gère l'installation et la configuration de Docker sur les serveurs cibles. Il garantit que Docker est correctement installé et fonctionne comme prévu.

[create-network](./ansible/roles/create-network) : ce rôle est responsable de la création et de la gestion des réseaux Docker. Il s'assure que les conteneurs Docker nécessaires pour l'application sont correctement connectés et peuvent communiquer entre eux.

[database](./ansible/roles/database) : ce rôle gère la base de données de l'application. Il s'assure que la base de données est correctement configurée, lancée et prête à être utilisée par l'application.

[app](./ansible/roles/app) : ce rôle déploie l'application proprement dite. Il s'assure que l'application est correctement configurée, lancée et prête à recevoir des requêtes.

[front](./ansible/roles/front) : ce rôle gère le déploiement de la partie front-end de l'application. Il s'assure que le front-end est correctement déployé et fonctionne correctement avec le reste de l'application.

[httpd](./ansible/roles/httpd) : ce rôle gère le serveur proxy HTTP. Il est configuré pour diriger les requêtes vers le front-end ou l'API en fonction de leur chemin.

## Lancement du déploiement avec Ansible
Pour déployer l'application sur le serveur, la commande suivante est utilisée :

```shell
ansible-playbook -i inventories/setup.yml playbook.yml
```
Une fois le playbook exécuté avec succès, tous les rôles spécifiés dans le playbook seront appliqués aux hôtes de l'inventaire. Cela inclut l'installation de Docker, la création du réseau, le lancement de la base de données, de l'application, du front-end, et du serveur HTTPD.

Enfin, une fois le déploiement terminé, l'application sera accessible à l'adresse suivante : http://lounisboulja.takima.cloud/. Il s'agit de l'URL du serveur où l'application a été déployée.



<footer style="margin-top: 2rem; width: 95%; display: flex; align-items: center; justify-content: space-between; box-shadow: rgba(0, 0, 0, 0.05) 0 6px 24px 0, rgba(0,0,0,0.06) 0 0 0 1px; border-radius: .5rem; padding: .4rem 1rem; background-color: rgba(255,255,255,0.07)">
    <b>👨‍💻 Lounis BOULDJA</b>
    <img alt="devOps" height="48" src="https://www.plunge.cloud/hs-fs/hubfs/cycle-devopsea2b.png?width=600&name=cycle-devops.png">
    <b><i>GENIAL</i>  🎓 2022 - 2023</b>
</footer>