---
title: Ecrire un script shell
tags: Shell
etat: printemps
---
Un script shell est un fichier texte en .sh contenant les informations à passer à un terminal qui les exécutera. 

Cheat sheet : https://devhints.io/bash

3 éléments sont importants pour qu'un script shell fonctionne et soit utilisable :
- Ecrire un script qui ne produise pas d'erreur
- Donner au script les permissions nécessaires afin de le rendre exécutable.
- Placer le script dans un dossier où le système pourra le trouver.

### Écrire un script

Un fichier Bash commence par un *shebang* ```#!/bin/bash``` indiquant le chemin vers l'interpréteur Bash que l'on utilisera. 

On peut commenter le script avec le caractère \#

`echo 'Hello world'` retourne le message `Hello World` dans la console.

Un programme simple :

```shell
#!/bin/bash
# This is our first script. 
echo 'Hello World!' # This is a comment
````

### Lui donner les permissions nécessaires pour l'exécution

On doit passer `chmod` afin de donner la permission pour exécuter le script. 755 correspond aux scripts que tout le monde peut exécuter et 700 aux scripts exécutables uniquement par le possesseur du script.

```shell
chmod 755 hello_world
```

### Emplacement du script

On peut exécuter le script une fois les permissions définies :

```shell
./hello_world

> Hello World!
````

Toutefois si l'on exécute :

```shell
hello_world

> hello_world: command not found
````

Si l'on veut que `hello_world` s'exécute, il faut associer son nom à une variable de chemin `PATH`. A chaque exécution de programme de type `hello_world`, le système recherche parmi une liste de dossier contenu dans la variable `PATH` si le nom du programme exécuté existe. On voit ces variables avec la commande `echo $PATH`.

On peut modifier la liste de ces fichiers en modifiant le fichier `.bash_profile` (sur OSX, `.bashrc` sur Linux) et en y incluant `export PATH=~/bin:"$PATH"` où `~/bin` correspond au dossier `/bin` que l'on veut indexer. Une fois la modification réalisée, on peut appeler le programme par son nom :

```shell
hello_world

> Hello World !
````
