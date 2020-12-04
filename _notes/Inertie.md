---
title: Inertie
tags : Maths
etat : printemps
---

L'inertie, dans le cadre du [[clustering]] par [[K-neighbors]], correspond à somme du carré de la distance entre les points de chaque cluster et leur point moyen.

Le but de l'algoithme [[K-neighbors]] est de minimser l'inertie des clusters formés :

$$\sum^{n}_{i=0}min(\lvert\lvert x_{i}-\mu_{j}\rvert\rvert^{2})$$

Du fait de l'absence de normalisation des données au préalable, l'inertie peut augmenter fortement. De ce fait, il peut être utile de réduire le nombre de dimensions dans les données par une [[ACP - Analyse par Composantes principales\|ACP]], ce qui permet par ailleurs d'accélérer les calculs.

Si l'on utilise [[sklearn.cluster.Kmeans\|sklearn.cluster.Kmeans]] pour générer des clusters au moyen d'un algorithme [[K-neighbors]], on peut accéder aux valeurs d'un modèle généré ([[.fit()]]) avec l'attribut `.inertia_`.

Cela permet notamment d'utiliser la technique du coude pour calculer le nombre optimal de clusters :

```python
k_values = range(1, 10)
inertia_values = [KMeans(k).fit(darts).inertia_
                  for k in k_values]

plt.plot(k_values, inertia_values)
plt.xlabel('K')
plt.ylabel('Inertia')
plt.show()
````

On peut dès lors choisir le nombre K de clusters qui minimise (K, inertie)