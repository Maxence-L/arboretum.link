---
title: Préparer Ansible en ligne de commande
tags : DevOps
etat : printemps
toc: true
---

Ansible est un outil d'automatisation permettant le déploiement d'une infrastructure sur un ou plusieurs serveurs à la fois.

Ansible est exécuté depuis un serveur maître dit *node manager* ou encore *control node* qui déploiera (*push* : maître vers cible) l'infrastructure sur les *nodes*, les serveurs-cible. 

Ansible est dit *agentless* : on a pas besoin de l'installer sur les *nodes* et seuls SSH et Python y sont nécessaires.

Cette page est issue des notes que prises avec le cours d'[OpenClassrooms](https://openclassrooms.com/fr/courses/2035796-utilisez-ansible-pour-automatiser-vos-taches-de-configuration?status=published) sur le sujet et adaptée à la mise en place d'un notebook Jupyterlab sur un VPS.

## Installation sur le *node manager*

On va d'abord importer sur la session **root** du serveur maître de quoi créer un environnement virtuel :

```bash
apt-get update
apt install python3-virtualenv sshpass
````

On crée un utilisateur pour Ansible :

```bash
adduser user-ansible
````

Et on s'y connecte :

```bash
su - user-ansible
````

On peut alors créer un virtualenv et y installer Ansible :

```bash
# Création de l'environnement
virtualenv ansible

# Activation de l'environnement
source ansible/bin/activate

# Installation avec pip
pip install ansible
````

On peut vérifier la version installée :

```bash
ansible --version

> ansible 2.10.5
	  config file = None
	  configured module search path = ['/home/user-ansible/.ansible/plugins/modules', '/usr/share/ansible/plugins/modules']
	  ansible python module location = /home/user-ansible/ansible/lib/python3.8/site-packages/ansible
	  executable location = /home/user-ansible/ansible/bin/ansible
	  python version = 3.8.5 (default, Jul 28 2020, 12:59:40) [GCC 9.3.0]
````

On peut voir les commandes Ansible installés :

````bash
~/ansible/bin$ ls ansible*

>	ansible             ansible-console  ansible-inventory  ansible-test
	ansible-config      ansible-doc      ansible-playbook   ansible-vault
	ansible-connection  ansible-galaxy   ansible-pull
````

- `ansible` permet d'accéder aux commandes Ansible accessibles depuis la ligne de commande.
- `ansible-config` permet d'accéder à la configuration d'Ansible.
- `ansible-doc` donne accès à la documentation.


## Mise en place d'une connexion avec le *node*

Les étapes suivantes sont nécessaires :

1. Définir un **inventaire** des nodes.
2. Vérifier la **connexion SSH** entre le node manager et les nodes.
3. Lancer un **ping** avec Ansible sur les nodes.
4. Vérifier que **Python** est installé sur les nodes.
5. Créer un **simple utilisateur** sur les nodes.
6. Attribuer les **droits sudo** à cet utilisateur.
7. Créer une **paire de clés** **SSH** pour cet utilisateur.
8. Copier la **clé publique SSH** sur les nodes.

Pour rendre les appelations plus simples dans la suite, le *node manager* sera [appelé](https://fr.wikipedia.org/wiki/Ma%C3%AEtre-esclave) *serveur maître* ou *serveur primaire* et le *node* où les logiciels sont déployés sera nommé *serveur esclave* ou *serveur secondaire*.

### 1. Définition de l'inventaire des nodes

On a dans notre cas deux serveurs disponibles : le serveur maître et le serveur esclave sur lequel seront installés conda, jupyter et les packages. Pour commencer, on modifie le fichier *hosts* du serveur maître pour que le nom du serveur esclave corresponde à son adresse IP. Ainsi, si son adresse change, on pourra modifier uniquement le fichier *hosts* et nom le script Ansible.

On va éditer le fichier `/etc/hosts`. Il faut pour cela se connecter en `root`.

```bash
nano etc/hosts
````

Et on rajoute la ligne suivante : `xx.xxx.xxx.xxx jupylab1` où `xx..` correspond à l'IP du serveur esclave.

On revient dans l'espace utilisateur `user-ansible` et l'on crée à présent le fichier inventaire Ansible en [`.ini`](https://fr.wikipedia.org/wiki/Fichier_INI) : 

```bash
source ansible/bin/activate
nano inventaire.ini
````

Et l'on y met le nom du serveur esclave : `jupylab1`

### 2. Vérifier la **connexion SSH** entre le maitre et l'esclave

On doit se connecter en SSH depuis le serveur maître afin d'enregistrer l'empreinte (*fingerprint*) du serveur esclave :

```bash
ssh root@jupylab1

The authenticity of host 'jupylab1 (xx.xxx.xxx.xxx)' can't be established.
RSA key fingerprint is SHA256:BpetmVp+7Absueuhdjkdsjfsdkjfsdjfskfmsdfsmlfkm.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added 'jupylab1,xx.xxx.xxx.xxx' (RSA) to the list of known hosts.
```

### 3. Ping du serveur esclave avec Ansible

On utilise un commande `ansible` pour pinger le serveur esclave et vérifier que la connexion fonctionne bien.

On passe la commande suivante depuis user-ansible@serveur-maitre :

```bash
ansible -i inventaire.ini -m ping jupylab1 --user root --ask-pass
````

La commande se décompose ainsi :

- `-i inventaire.ini` indique l'emplacement du fichier inventaire.ini.
- `-m ping` indique que l'on souhaite utiliser le module ping.
- `jupylab1` indique le serveur sur lequel s'applique l'action.
- `--user root` indique à Ansible de se connecter en `root`.
- `--ask-pass` indique de demander le mot de passe SSH.

```bash
# Résultat

>	jupylab1 | SUCCESS => {
		"ansible_facts": {
			"discovered_interpreter_python": "/usr/bin/python3"
		},
		"changed": false,
		"ping": "pong"
	}
```

Le serveur esclave a renvoyé une requête et répondu `pong` au ping.

> A noter : Les modules (comme ici `ping`) est utilisé pour exécuter une tâche ou une commande Ansible. On peut utiliser des modules proposés par Ansible (voir `ansible-doc --list`) ou en créer de nouveaux.

### 4. Vérification de l'installation de Python

On vérifie que Python3 est bien installé avec le module `raw` d'Ansible qui permet de passer des commandes bash au serveur esclave :

```bash
$ ansible -i inventaire.ini -m raw -a "apt install -y python3" jupylab1 --user root --ask-pass

>	jupylab1 | CHANGED | rc=0 >>
	Reading package lists... Done
	Building dependency tree
	Reading state information... Done
	python3 is already the newest version (3.8.2-0ubuntu2).
	0 upgraded, 0 newly installed, 0 to remove and 180 not upgraded.
	Shared connection to jupylab1 closed.
```

### 5. Créer un utilisateur `user-ansible` sur le serveur esclave

Il est une bonne pratique d'éviter au maximum d'exécuter des commandes en `root` et de créer un utilisateur dédié à Ansible, comme ce que l'on a fait sur le serveur primaire.

On commence par créer un mot de passe chiffré sur le serveur maître (`localhost`) avec le module `debug`. On demande à convertir en une chaine chiffrée SHA512 le mot de passe 'datapasswold'. `Sceretsalt` ajoute une inconnue au chiffrage.

```bash
ansible localhost -i inventaire.ini -m debug -a "msg={{'datpasswold'|password_hash('sha512', 'sceretsalt') }}"

>	localhost | SUCCESS => { "msg": "$6$sceretsalt$tBcfGEgifQpQZsg5CIGZ79XC55h5vHy7UWrys7cAF37KNCQQbm7iCvy58MlLQLaS2fLF6ZjqDVHhVrkMdRi0f0" }
```

On peut à présent créer l'utilisateur avec le module [`user`](https://docs.ansible.com/ansible/2.5/modules/user_module.html) :

```bash
ansible -i inventaire.ini -m user -a 'name=user-ansible password=$6$sceretsalt$tBcfGEgifQpQZsg5CIGZ79XC55h5vHy7UWrys7cAF37KNCQQbm7iCvy58MlLQLaS2fLF6ZjqDVHhVrkMdRi0f0' --user root --ask-pass jupylab1

>	jupylab1 | CHANGED => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "append": false,
    "changed": true,
    "comment": "",
    "group": 1000,
    "home": "/home/user-ansible",
    "move_home": false,
    "name": "user-ansible",
    "password": "NOT_LOGGING_PASSWORD",
    "shell": "/bin/sh",
    "state": "present",
    "uid": 1000
}
````

### Attribuer les droits sudo à user-ansible

Afin de pouvoir déployer nos logiciels, nous devons ajouter l'utilisateur `user-ansible` du serveur-esclave au groupe `sudo`. On utilise à nouveau le module `user` en mode ad-hoc :

```bash
$ ansible -i inventaire.ini -m user -a 'name=user-ansible groups=sudo append=yes' jupylab1 --user root --ask-pass
SSH password:
jupylab1 | CHANGED => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "append": true,
    "changed": true,
    "comment": "",
    "group": 1000,
    "groups": "sudo",
    "home": "/home/user-ansible",
    "move_home": false,
    "name": "user-ansible",
    "shell": "/bin/sh",
    "state": "present",
    "uid": 1000
````

### Créer une clé SSH pour se connecter sans mot de passe

On crée une clé SSH qui servira à se connecter sans mot de passe au serveur secondaire :

```bash
$ ssh-keygen -t ecdsa

>	Generating public/private ecdsa key pair.
	Enter file in which to save the key (/home/user-ansible/.ssh/id_ecdsa):
	Enter passphrase (empty for no passphrase):
	Enter same passphrase again:
	Your identification has been saved in /home/user-ansible/.ssh/id_ecdsa
	Your public key has been saved in /home/user-ansible/.ssh/id_ecdsa.pub
````

### Ajouter la clé sur le serveur secondaire

On peut à présent utiliser le module [authorized_keys](https://docs.ansible.com/ansible/2.4/authorized_key_module.html) pour ajouter la clé SSh à l'utilisateur `user-ansible`:

```bash
ansible jupylab1 -i inventaire.ini -m authorized_key -a 'user=user-ansible state=present key="{{ lookup("file", "/home/user-ansible/.ssh/id_ecdsa.pub") }}"' --user user-ansible --ask-pass --become --ask-become-pass
SSH password:
BECOME password[defaults to SSH password]:

jupylab1 | CHANGED => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": true,
    "comment": null,
    "exclusive": false,
    "follow": false,
    "key": "ecdsa-sha2-nistp256 AAAAxxxxxxxxx",
    "key_options": null,
    "keyfile": "/home/user-ansible/.ssh/authorized_keys",
    "manage_dir": true,
    "path": null,
    "state": "present",
    "user": "user-ansible",
    "validate_certs": true
}
````

Dans notre cas, le mot de passe SSH et Become correspondent au mot de passe Ansible. L'option `state=present` précise que l'on ajoute un fichier et `key="{{ lookup("file", "/home/user-ansible/.ssh/id\_ecdsa.pub") }}"` utilise la commande lookup pour le rechercher.

Le serveur est prêt à recevoir le déploiement.

