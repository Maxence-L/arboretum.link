---
title:
tags : stdlib
etat :hiver
---

La fonction *iter()* retourne un objet **[[Différence itérable-itérateur-itération\|itérateur]]** à partir d'un objet compatible. Une liste, par exemple.

Le paramètre *sentinel* est utilisé si l'objet est "[[callable]]". L'itérateur s'arrête alors lors que la valeur retournée est la même que la sentinelle.
```python
x = iter(["apple", "banana", "cherry"])
```

