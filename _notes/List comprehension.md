---
title: List comprehension
tags: stdlib
toc: false
etat: hiver
---
La *list comprehension* permet de construire des [[listes]] issues d'opérations itératives directement lors de la définition de celles-ci. Les *list comprehensions* sont généralement plus rapide et élégantes.

La syntaxe est la suivante : 

`[expression for item in iterable]`

```python
[x*x for x in range(5)]

# [0, 1, 4, 9, 16]
```

On peut aussi ajouter des conditions et des branches else. Dans le cas d'une condition seule, elle vient à la fin :
```python
[x for x in range(10) if x % 2 == 0]

# [0, 2, 4, 6, 8]
```

Avec un if...else, la branche se situe devant le 'for' :
```python
["Odd" if x%2 == 0 else "Even" for x in range(10)]

# ['Even', 'Odd', 'Even', ...
```

On peut même l'utiliser pour créer des listes imbriquées. 
Par exemple, pour transposer :
```python
matrix = [ [1, 2], [3,4], [5,6], [7,8] ]
transposed = [ [row[i] for row in matrix] for i in range(2)]
print(transposed)

# [ [1, 3, 5, 7], [2, 4, 6, 8] ]
```

On peut réutiliser le même type de syntaxe pour le [[Dict comprehension]]