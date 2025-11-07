# ansible-examples

Ce répo contient une introduction à Ansible et des exemples de playbook.
Les exemples tournent sur des installation debian netinstall, le seul
prérequis étant l'installation du package ssh et l'autorisation de connexion
root avec password. Cet accès sera utilisé pour configurer le user ansible
et la connexion ssh (via Ansible), puis désactivé.

Dans `/etc/ssh/sshd_config`, mettre :

```
PermitRootLogin yes
```

## Preliminaire : préparer le noeud pour être géré par Ansible

Prerequis : copier la clef SSH publique du control node dans le fichier `files/authorized_keys`

Cette étape vise à préparer un serveur nouvellement installé à être géré par
Ansible. Le paramétrage sera fait par Ansible, via une connexion root. Le playbook
`configure_ansible.yaml` contient tout le nécessaire.

* installation du package sudo
* création du user `ansible`
* copy de la clef publique ssh pour autoriser les connexions sans mot de passe
* paramétrage du user `ansible` pour sudo

Ce premier playbook doit se lancer en root. Si le fichier inventaire contient la variable
`ansible_user`, celle-ci surchargera la directive `remote_user` du playbook, il faudra alors
la spécifier sur la ligne de commande :

```
ansible-playbook configure_ansible.yaml -i inventory.ini -e ansible_user=root
```


## TP1 : installation d'un serveur web avec Ansible

Le playbook `tp1-webserver.yaml` contient toute la configuration nécessaire pour installer le serveur web
et paramétrer une page web qui affiche le nom du serveur et ses adresses ip.

* Chargement d'un fichier de variables situé dans `vars/main.yaml`
* Installation du package `nginx`
* Dépot via le moteur de template du fichier `templates/index.html.j2` dans le dossier `/var/www/html` du serveur distant

Le contenu du fichier `index.html` illustre l'utilisation des variables :

* la variable `message_accueil`, paramétrée dans le fichier `vars/main.yaml` et chargé dans le playbook avec la directive `var_files` qui référence le fichier
* la variable de faits `ansible_facts` qui contient les informations relatives au noeud sur lequel l'installation est en cours.