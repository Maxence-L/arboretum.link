---
title: Obtenir toutes les clés d'un dictionnaire
tags: stdlib
etat: printemps
---

La fonction zip() prend en paramètre un ou des itérables, et retourne un [[Différence itérable-itérateur-itération\|itérateur]] sous forme de tuple, basé sur les objets itérables passés.

- Lorsque l'on ne passe aucun paramètre, ```zip()``` ne retourne qu'un itérateur vide
- 
```python
resultat = zip()

[]
```

- Lorsque l'on passe un seul itérable, ```zip()``` retourne un itérateur produisant un tuple pour chaque élément de l'itérable.
- 
```python
x = [1,2,3]
resultat = zip(x)
print(set(resultat))

# {(2,), (3,), (1,)}
```

- Lorsque l'on passe plusieurs itérables, ```zip()``` retourne un itérateur produisant un tuple contenant un élément de chaque l'itérable passé. Il se termine lorsqu'un des itérables est épuisé. 

En pratique, si deux itérables sont passés  dans ```zip()```, un contenant trois éléments et l'autre cinq, l'itérateur retourné contiendra 3 tuples. Il s'arrête en effet lorsque l'itérable le plus court est épuisé.

```python
numbersList = [1, 2, 3]
str_list = ['one', 'two']
numbers_tuple = ('ONE', 'TWO', 'THREE', 'FOUR')
 
print(set(result))

# {(2, 'two', 'TWO'), (1, 'one', 'ONE')}
```
