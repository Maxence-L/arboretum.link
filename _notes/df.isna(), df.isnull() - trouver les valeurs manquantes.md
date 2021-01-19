---
title: df.isna(), df.isnull() - trouver les valeurs manquantes
tags : pandas
etat : hiver
---
Retourne un [[dataframe]] rempli de booléens, True si l'entrée existe et False si elle est `NaN`. On peut l'utiliser avec [[df.sum()]] ou [[df.any()]] pour obtenir un résumé de l'état de la colonne ou de la ligne.

```python
df.isnull().head()
```

|   |     0 |     1 |     2 |     3 |     4 |
|---|------:|------:|------:|------:|------:|
| 0 | False | False | False | False | False |
| 1 | False |  True | False | False | False |
| 2 | False | False | False |  True | False |
| 3 |  True | False | False |  True | False |
| 4 | False | False | False | False | False |

Obtenir les lignes *d'une seule colonne* où au moins une valeur est manquante :

```python
df[df['nom_col'].isna()]
````

Obtenir les lignes où au moins une valeur est manquante *parmis toutes les colonnes* :

```python
df[df.isna().any(axis=1)]
````