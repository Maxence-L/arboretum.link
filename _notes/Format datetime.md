---
title: datetime
tags: pandas
etat : hiver
---

pd.to_datetime() est utilisée pour transposer une donnée au format temporel.

```python
parsed_date = pd.to_datetime('January 1st, 2017')
````

On peut accéder ensuite aux éléments en utilisant les attributs suivants :

```python
parsed_date.month

> 1
```

```python
parsed_date.year

> 2017
````

```python
parsed_date.second

> 0
```

Voir l'aide de pd.to_datetime() pour plus de détails, mais il est possible de spécifier le format en argument afin de pallier les difficultés liées, par exemple, aux date américaines :

```python
parsed_date = pd.to_datetime('5/3/2017 5:30')
parsed_date.month

> 5
```

On définit le format en précisant *format='%d/%m/%Y %H:%M'* (par exemple) en argument dans pd.to_datetime()

```python
parsed_date = pd.to_datetime('3/5/2017 5:30', format='%m/%d/%Y %H:%M')
parsed_date.month

> 3
```