---
title : Heatmaps
tags : Visualisations
etat : hiver
---

Les *Heatmaps* permettent de comparer les valeurs associées à deux attributs, par exemple la corrélation entre deux variables.

Le package [[seaborn]] permet d'en créer facilement. Ici, avec [`df.corr()`](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.corr.html) produisant une matrice de corrélation :

```python
sns.heatmap(df.corr(), square=True, cmap='RdYlGn')
````

![](/assets/img/heatmap.png#center)

Les nombreuses options peuvent être trouvées sur la [page d'aide](https://seaborn.pydata.org/generated/seaborn.heatmap.html) de Seaborn.