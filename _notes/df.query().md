---
title: df.query()
tags: pandas
etat: hiver
---
Méthode permettant de filtrer un [[dataframe]] avec une condition retournant un booléen.

```python
df_q = df.query('total > 1_000_000_000')
```

On peut accéder à des variables externes en précisant @ devant son nom :

```python
df_q = df('population > @max_value')
```

Afin d'éviter les problèmes de parsing lorsque l'on utilise plusieurs conditons, il peut être utile d'utiliser des parenthèses :

```python
df_q = df.query('(population > @max_value) or (population < @min_value)')
```