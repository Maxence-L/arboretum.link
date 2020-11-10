---
title: dict.update()
tags: stdlib
etat : hiver
---

.update() est une méthode appliquée sur un [[dictionnary|dictionnaire]] qui met à jour les clés du dictionnaire sur lequel elle est appelée avec les clés d'un autre dictionnaire spécifié en paramêtre[^1].

[^1]: https://www.programiz.com/python-programming/methods/dictionary/update

```python
d = {1: "one", 2: "three"}
d1 = {2: "two"}

# updates the value of key 2
d.update(d1)
d

{1: 'one', 2: 'two'}
```

Si la valeur spécifiée dans le dictionnaire en paramêtre n'est pas contenu dans le dictionnaire initial, elle sera rajoutée :

```python
d1 = {3: "three"}

# adds element with key 3
d.update(d1)
d

{1: 'one', 2: 'two', 3: 'three'}
```



