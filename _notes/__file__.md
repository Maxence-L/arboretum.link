---
title: __file__
tags: python-stdlib
toc: 'false'
etat: printemps
---
[[Variable globale]] renvoyant le chemin  relatif vers le fichier

Si on a un package organisé ainsi :

`````
\
|__ main.py
|
|__assets___|
   			|__option.py
`````

Exécuter `print(__file__)` dans main.py renvoie :

```python
print(__file__)

> main.py
`````

Exécuter `print(__file__)` dans option.py renvoie :

```python
print(__file__)

> assets/option.py
`````
