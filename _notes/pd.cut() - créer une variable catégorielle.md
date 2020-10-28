---
title: pd.cut() - créer une variable catégorielle
tags: pandas
etat: printemps
---

pd.cut() assigne pour chaque ligne d'une variable quantitative une étiquette correspondant à un interval donné.

La [syntaxe](https://pandas.pydata.org/docs/reference/api/pandas.cut.html) est la suivante : 

```python
pd.cut(x, 
	bins, 
	right=True, 
	labels=None, 
	retbins=False, 
	precision=3, 
	include_lowest=False, 
	duplicates='raise', 
	ordered=True)
```

Exemple : On a une variable `df['age']` qui contient l'âge des utilisateurs. On désire la séparer en intervales de 5 ans pour tracer un graphique en bâtons.

 ```python
df['age_cat'] = pd.cut(df['age'], 
    bins=[x for x in range(0,100, 5)],
    labels=[x for x in range(5,100, 5)], 
    right=True)
```

- `bins` correspond aux intervales de classification : [0,5], [5,10], [10,15] ...

- `labels` correspond aux étiquettes à apposer sur la ligne catégorisée - l'occurrence il s'agit de l'âge maximal de la catégorie. 

- `right` correspond à la la borne à prendre en compte. `right = True` pour une borne supérieure.
> **À  noter** : la liste `labels` doit contenir `len(bins) - 1` étiquettes, du fait de la présence d'une borne inférieure et d'une borne supérieure dans `bins`, contrairement à `labels`.

On peut ensuite créer un diagrame en bâtons facilement, avec [[df.groupby() -\|df.groupby()]] :

```python
ages.groupby('age_cat').sum().plot.bar()
````

Un tuto utile sur SO :
https://stackoverflow.com/questions/52753613/grouping-categorising-ages-column-in-python-pandas