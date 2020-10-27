---
title: df.duplicated()
tags: pandas
etat: printemps
---
Retourne un [[dataframe]] rempli de booléens, True si l'entrée existe en double (est dupliquée), False si la ligne est unique. 

Utile, par exemple dans le cas de données temporelles, pour savoir si les données sont propres. Comme [[df.isnull()|.isnull]] On peut l'utiliser avec [[df.any()]] ou [[df.sum()]] pour avoir une vue synthétique.

```python
df.duplicated()

> 304      False
  816      False
  1210     True
  1211     True
  1260     False
  1268     False
  2034     False
  2051     False
  2104     False
  2530     False
```

On peut l'associer à [[df.sum()]] pour connaître le nombre de doublons, ou à un index de [[dataframe]] pour faire une sélection.