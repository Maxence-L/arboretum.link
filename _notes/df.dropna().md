---
title: df.dropna()
tags : pandas
etat : hiver
---

`df.dropna()` permet de supprimer les lignes contenant une valeur `np.nan` d'un dataframe.

Dans le cas d'un jeu de données contenant des valeurs "?" correspondant à une information manquante :

```python
# Convert '?' to NaN
df[df == '?'] = np.nan

# Print shape of original DataFrame
print("Shape of Original DataFrame: {}".format(df.shape))

> Shape of Original DataFrame: (435, 17)

# Drop missing values and print shape of new DataFrame
df = df.dropna()

# Print shape of new DataFrame
print("Shape of DataFrame After Dropping All Rows with Missing Values: {}".format(df.shape))

> Shape of DataFrame After Dropping All Rows with Missing Values: (232, 17)
````

