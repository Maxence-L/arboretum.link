---
title: pd.concat() - ajouter des lignes et des colonnes
tags: pandas
etat : printemps
---

La concaténation de table simple consiste à ajouter les lignes ou les colonnes d'un [[dataframe]]) un autre.

En pratique, on définit une liste les [[dataframe]]s que l'on veut concaténer et l'on appelle pd.concat() avec la liste en argument :

```python
frames = (df1, df2, df3)

result = pd.concat(frames)
```

- On peut passer *axis=0* ou *1* en paramètre si l'on veut réaliser une concaténation verticale ou horizontale. **A noter qu'il est aussi possible d'utiliser [[df.append() - ajouter une ligne\|df.append()]] pour réaliser une concaténation verticale (axis=0)**
- On peut préciser *keys=\['x', 'y', 'z']* si l'on veut que l'index garde la trace des tables initiales :
La concaténation de table simple consiste à ajouter les lignes ou les colonnes d'un [[dataframe]]) un autre.

En pratique, on définit une liste les [[dataframe\|dataframes]] que l'on veut concaténer et l'on appelle pd.concat() avec la liste en argument :

```python
frames = (df1, df2, df3)

result = pd.concat(frames)
```

- On peut passer *axis=0* ou *1* en paramètre si l'on veut réaliser une concaténation verticale ou horizontale. **A noter qu'il est aussi possible d'utiliser [[df.append() - ajouter une ligne\|df.append()]] pour réaliser une concaténation verticale (axis=0)**
- On peut préciser *keys=\['x', 'y', 'z']* si l'on veut que l'index garde la trace des tables initiales :

 - On peut préciser *join='outer'* ou *'inner'* si les dataframes ne sont pas exactement raccord. Outer fera une union des deux dfs, inner une intersection, sur la base des valeurs de l'index.

![](/assets/img/pd_concat-table.png#center)

Pour plus d'exemples et de possilités :

https://pandas.pydata.org/pandas-docs/stable/user_guide/merging.html
 - On peut préciser *join='outer'* ou *'inner'* si les dataframes ne sont pas exactement raccord. Outer fera une union des deux dfs, inner une intersection, sur la base des valeurs de l'index.

Pour plus d'exemples et de possilités :

https://pandas.pydata.org/pandas-docs/stable/user_guide/merging.html