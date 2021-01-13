---
title: Conversion des données catégorielles
tags : ml-pratique
etat : printemps
---

Certaines colonnes de données peuvent être composées de valeurs qualitatives : nationalité, sexe, lieu... Elle ne sont pas exploitables en l'état. On doit transformer la colonne qualitative en plusieurs colonnes composées de variables binaires.

Avant de commencer la transformation, il peut être utile d'examiner le lien entre la variable qualitative non-transformée et la variable objectif. On peut le faire en créant une boite à moustache :

```python
# Import pandas
import pandas as pd

# Read 'gapminder.csv' into a DataFrame: df
df = pd.read_csv('gapminder.csv')

# Create a boxplot of life expectancy per region
df.boxplot('life', 'Region', rot=60)

plt.show()
````

![](../assets/img/gapminder_catégorielle.png#center)

On constate que l'espérance de vie varie fortement avec la localisation géographique. Il est donc indiqué de créer des variables binaires indiquant le lie de résidence.

## Avec pandas : pd.get_dummies()

```python
# Create dummy variables: df_region
df_region = pd.get_dummies(df)

# Print the columns of df_region
print(df_region.columns)


````

On le résultat suivant :
```
> Index(['population', 'fertility', 'HIV', 'CO2', 'BMI_male', 'GDP', 'BMI_female', 'life', 'child_mortality', 'Region_America', 'Region_East Asia & Pacific', 'Region_Europe & Central Asia', 'Region_Middle East & North Africa', 'Region_South Asia', 'Region_Sub-Saharan Africa'], dtype='object')
```

On peut utiliser l'option `drop_first = True` pour supprimer la première colonne ainsi créée et éviter les problèmes de colinéarité.

