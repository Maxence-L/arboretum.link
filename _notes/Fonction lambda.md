---
title : Fonction lambda
tags: stdlib
etat: hiver
---

Une fonction lambda prend la forme suivante : `objet = lambda paramètres : expression`.
Ce type d'écriture permet de spécifier rapidement des fonctions simples et de rendre le code plus facile à lire.
```python
x = lambda a, b : a * b
print(x(5, 6))

> 30
```

On peut créer des conditions if de la manière suivante :
```python
z = 5
fun = lambda x: False if x < 4 else True
print(fun(z))

> True
```

Elle est facilement couplable avec une méthode [[pd.apply()]]