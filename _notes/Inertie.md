---
title: Inertie
tags : Maths
etat : printemps
---

L'inertie, dans le cadre du [[clustering]] par [[K-means]], correspond à somme du carré de la distance entre les points de chaque cluster et leur point moyen.

Le but de l'algoithme [[K-means]] est de minimser l'inertie des clusters formés :

$$\sum^{n}_{i=0}min(\lvert\lvert x_{i}-\mu_{j}\rvert\rvert^{2})$$

Du fait de l'absence de normalisation des données au préalable, l'inertie peut augmenter fortement. De ce fait, il peut être utile de réduire le nombre de dimensions dans les données par une [[Principal component analysis|PCA]], ce qui permet par ailleurs d'accélérer les calculs.

Si l'on utilise [[sklearn.cluster.Kmeans - Kmeans facile\|sklearn.cluster.Kmeans]] pour générer des clusters au moyen d'un algorithme [[K-means]], on peut accéder aux valeurs d'un modèle généré ([[.fit()]]) avec l'attribut `.inertia_`.

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
![[Capture d’écran 2020-06-29 à 19.44.17.png]]

On peut dès lors choisir le nombre K de clusters qui minimise (K, inertie)