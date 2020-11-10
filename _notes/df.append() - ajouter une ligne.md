---
title: df.append() - ajouter une ligne
tags: pandas
etat: hiver
---
df.append() est une méthode permettant d'ajouter une ligne de données ou un [[dataframe]] à un autre dataframe.

df.append() ne possède pas d'option spécifiant `inplace=True`, il faut penser à réattribuer le résultat à un dataframe pour conserver la manipulation.

La syntaxe est la suivante :

```python
df_city = df_city.append(my_dict, ignore_index = True)
```
