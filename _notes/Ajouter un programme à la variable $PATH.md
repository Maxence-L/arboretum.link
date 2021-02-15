---
title : Ajouter un programme à la variable $PATH
tags : Système
etat : hiver
---

## Solution temporaire :

(Sur Ubuntu) On commence par inspecter la variable `path`:

```bash
echo $PATH
````

Ce qui donne :

```bash
/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games
```

On peut à présent ajouter le dossier du programme à la variable :

```bash
export PATH=$PATH:/path/to/my/program
```

On vérifie :

```bash
echo $PATH
````

## Solution permanente :

Voir [l'aide d'Ubuntu](https://help.ubuntu.com/community/EnvironmentVariables#Persistent_environment_variables).