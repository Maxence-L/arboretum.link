---
title: set()
tags: stdlib
etat: Printemps
---

Les sets sont des objets similaires aux [[dictionnaires]] mais ne contenant pas de clé et ne disposant dont pas d'ordre, contrairement aux [[listes]][^1].

[^1]: https://www.w3schools.com/python/python_sets.asp

On les déclare avec le constructeur ``set()`` ou avec des doubles accolades :

```python
myset = {12,13,14}
mysetset = set(12,13,14)

myset == mysetset

> True
```

A noter toutefois que les doubels accolades ne fonctionnent que pour ajouter des éléments singuliers (1,2,3...) et non des variables : `myset = {numbers}` ne fonctionnera pas, ou alors il faudra ajouter un [[unpacker]] : `*` : `myset = {*numbers}`

On ajoute une valeur au moyen de la méthode `.add()`. Si l'on désire en ajouter plus d'une, il faut utiliser `.update()`

```python
numbers = [1, 2, 3, 1, 2, 3, 4, 1]
unique_numbers = set()
for number in numbers:
    unique_numbers.add(number)
````

```python
numbers = [1, 2, 3, 1, 2, 3, 4, 1]
unique_numbers = set()
unique_numbers.update(numbers)
```

#### Le sets sont utiles pour la gestion des *doublons* :

Ils ne peuvent contenir deux fois la même valeur, ce qui en fait un outil très intéressant pour les situations où l'on veut supprimer facilement des doublons d'une liste. 

Exemple :
```python
numbers = [10, 20, 30, 40, 30, 40, 50]
print(len(numbers))

> 7

unique_numbers = set(numbers)
print(len(unique_numbers))

>5


numbers_bis = set([10, 20, 100, 60, 30, 40, 50])
````

On peut utiliser \| pour retourner l'union de deux sets :
```python
all_numbers = numbers_bis | numbers

print(all_numbers)

> {100, 40, 10, 50, 20, 60, 30}
```

