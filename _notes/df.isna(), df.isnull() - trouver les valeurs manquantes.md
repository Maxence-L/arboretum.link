---
title: df.isna(), df.isnull() - trouver les valeurs manquantes
tags : pandas
etat : hiver
---
Retourne un [[dataframe]] rempli de booléens, True si l'entrée existe et False si elle est NaN. On peut l'utiliser avec [[df.sum()]] ou [[df.any()]] pour obtenir un résumé de l'état de la colonne.
```python
df.isnull()
```

