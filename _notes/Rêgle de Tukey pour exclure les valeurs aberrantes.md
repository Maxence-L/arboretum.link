---
title: Rêgle de Tukey pour exclure les valeurs aberrantes
tags : Probabilités
etat : printemps
---

La règle de Tukey, propose de retirer les valeurs comprises hors de l'intervale :

\[Q1 - 1.5 x IQR ; Q3 +1.5 x IQR]

Où Q1 et Q3 sont le premier et troisième quartile (25%, 75%) et où IQR (*interquartile range*) est égale à Q3 - Q1.

Cet écart correspond approximativement à ±3𝜎[^1], ce qui est suffisant pour détecter les valeurs extrêmes sans être trop sévère non plus.

Un exemple d'implémentation avec [[df.query()]]:

```python
Q1 = df['gdp'].quantile(0.25)
Q3 = df['gdp'].quantile(0.75)
IQR = Q3 - Q1

max_value_gdp = Q3 + 1.5 * IQR
min_value_gdp = Q1 - 1.5 * IQR

gdp_outliers = df.query('(gdp > @max_value_gdp) or (gdp < @min_value_gdp)')
```

[^1]: https://math.stackexchange.com/questions/966331/why-john-tukey-set-1-5-iqr-to-detect-outliers-instead-of-1-or-2