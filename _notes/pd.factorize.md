---
title: pd.factorize()
tags: pandas
etat: printemps
---
Cette méthode est utile pour passer d'une variable textuelle à une variable numérique. Elle peut être utilisée comme une méthode de classe `pd.factorize(values)` ou comme une méthode appelée sur un objet [[pd.Series]] ou [[pd.Dataframe]].

La méthode retourne un [[tuple]] comportant deux éléments: 

- `uniques` : Un array numérique où chaque chiffre correspond à un facteur

- `codes`: Un array portant le nom de chaque facteur. Le premier élément correspondra au chiffre 0 de `uniques`, le second au chiffre un et ainsi de suite.

Par exemple, si l'on a la liste suivante : `['bleu', 'vert', 'bleu', 'jaune', 'vert', 'bleu']` :

```python
import pandas as pd

data = ['bleu', 'vert', 'bleu', 'jaune', 'vert', 'bleu']
codes, uniques = pd.factorize(data)
codes

> array([0, 1, 0, 2, 1, 0])

uniques

> array(['bleu', 'vert', 'jaune'], dtype=object)
```

Si l'on appelle `.factorize()` sur un objet Series, la méthode retourne, de même, un tuple :

```python
data_series = pd.Series(data)
data_series

>   0     bleu
    1     vert
    2     bleu
    3    jaune
    4     vert
    5     bleu
    dtype: object

data_series.factorize()

> (array([0, 1, 0, 2, 1, 0]), Index(['bleu', 'vert', 'jaune'], dtype='object'))

type(data_series.factorize())

> tuple
`````

On peut dès lors insérer le premier élément dans un [[pd.Dataframe]] en spécifiant l'index `[0]` :

```python
df['color_index'] = data_series.factorize()[0]

# Ou si la variable est déjà dans le Dataframe :

df['color_index'] = df.['color'].factorize()
`````

