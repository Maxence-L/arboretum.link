---
title: df.fillna()
tags: pandas
etat: hiver
---

Méthode permettant de remplacer les valeurs NaN par d'autres valeurs. 

On peut spécifier en valeur une valeur unique, mais aussi une [[series\|Série]] ou un [[dataframe]]. 

fillna() choisira dans ce cas la valeur à remplir en fonction de l'index de la ligne et de la colonne. Il est aussi possible de choisir un algorithme de remplacement (prochaine / précédente valeur par exemple).

```python
# Remplacer les valeurs manquantes par 0
df.fillna(0)
# Remplacer les valeurs manquantes par la moyenne
df.fillna(df.mean(), inplace=True)
```