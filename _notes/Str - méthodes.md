---
title : Str - méthodes
tags: stdlib
etat: hiver
toc: true
---

Cette note regroupe les explications des méthodes liées au objets de type `string` dans Python. Une référence est donnée pour chaque méthode.

## str.split

Sépare un objet `string` et retourne une liste contenant les parties :

```python
'ab cd ef'.split()

> ['ab', 'cd', 'ef']
````

Comme on le voit, le séparateur par défaut est l'espace `' '`. On peut le modifier en en passant un autre entre parenthèses :

```python
'abTcdTef'.split('T')

> ['ab', 'cd', 'ef']
````

> À noter :
> On peut l'utiliser avec [[df.explode()]] pour séparer des valeurs qui auraient été regroupées dans un ligne d'un [[dataframe]] !

## str.join()

Regroupe des objets `string` en un seul, avec ou sans séparateur.

```python
''.join(['ab', 'cd', 'ef'])

> 'abcdef'

'*'.join(['ab', 'cd', 'ef'])

> 'ab*cd*ef'
````

## str.isdigit()

Renvoie un booléen `True` si l'objet `string` est non-vide et contient un caractère numérique compris entre 0 et 9.

```python
'5'.isdigit()

> True
````



#### Reférence :
[str.split() - docs.python.org](https://docs.python.org/3/library/stdtypes.html?#str.split)
[str.join() - docs.python.org](https://docs.python.org/3/library/stdtypes.html?#str.join)
[str.isdigit() - docs.python.org](https://docs.python.org/3/library/stdtypes.html#str.isdigit)