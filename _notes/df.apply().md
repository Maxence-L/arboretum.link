---
title: df.apply()
tags: pandas
etat: hiver
---

La fonction **df.apply()** est l'équivalent pandas de [[map()]], elle permet d'appliquer une fonction à toutes les lignes ou colonnes d'un [[dataframe]].

Elle se couple bien à une [[Fonction lambda\|fonction lambda]] ad-hoc.

## Utilisation de la fonction

## Cas d'usage
### Appliquer apply() à une seule colonne[^1] :

[^1]: https://stackoverflow.com/questions/34962104/how-can-i-use-the-apply-function-for-a-single-column

On a un [[dataframe]] `df`:

a|b|
-|-|
1|2|
2|3|
3|4|
4|5|

On veut que l'opération `x = x + 1` soit appliquée à toutes les lignes de la colonne a :

```python
df['a'] = df['a'].apply(lambda x: x + 1)
````

a|b
-|-
2|2
3|3
4|4
5|5

