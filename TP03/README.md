# TP 03

___
***Lounis BOULDJA    |   TP 03    |     devOps 2022 2023***
___

## Documentation des inventaires et commandes de base

Notre inventaire Ansible est défini dans le fichier `setup.yml` dans le répertoire `ansible/inventories`. Voici un aperçu de la structure de notre inventaire :

```yaml
all:
  vars:
    ansible_user: centos
    ansible_ssh_private_key_file: ~/id_rsa
  children:
    prod:
      hosts: lounisboulja.takima.cloud

```
