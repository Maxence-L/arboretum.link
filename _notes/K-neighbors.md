---
title: K-neighbors
tags : Apprentissage-non-supervisé
etat : hiver
---

L'algorithme dit des *K-neighbors* regroupe une famille d'algorithmes utilisés pour classifier des données de manière non-supervisée (sans variable réponse). Le seul hyperparamètre que l'on passera au modèle sera celui du nombre de classes que l'on souhaite obtenir.

## Fonctionnement de l'algorithme K-Means
L'algorithme K-means est un algorithme de [[classification non-supervisée]] (*clustering*) très courant et souvent parmis les premiers du genre abordés. Par exemple, [Chris McKinlay l'a utilisé pour son étude des profils sur OKCupid](https://www.wired.com/2014/01/how-to-hack-okcupid/). 

L'algorithme catégorise les observations en fonction de leur distance au point moyen ([[Centroïde\|centroïde]]) de la classe. Une fois qu'une classe a été attribuée à chaque point, on peut recalculer la centroïde et réitérer la procédure, jusq'à convergence. \[A développer]

On mesure la performance de l'algorithme au moyen de l'[[Inertia|inertie]] du résultat. L'inertie correspond à la densité des clusters et est égale à la somme du carré de la distance entre chaque point et le centre de son cluster. 

Cette mesure sert par la suite à déterminer le nombre optimal de clusters, au moyen d'un graphique en coude :

![elbow](/assets/img/kmeanselbow.png#center)
<div align="center">
  Inertie de la classification en fonction du nombre de clusters
</div>

Quelques remarques :

- La convergence n'assure pas l'optimalité et en fonction des valeurs des points de départ, l'algorithme peut converger vers des états plus ou moins pertinents. Il peut être utile de faire tourner plusieurs fois l'algorithme et de comparer les résultats.

- L'algorithme K-means nécessite que les points se situent dans un [[Norme d'un vecteur\|espace remplissant les conditions de distance "métrique"]], afin de calculer leur distance respective.

- Le [[Silhouette Score\|score de silhouette]] peut aussi être utilisé afin de déterminer si les clusters sont aggrégés correctement.

### Utilisation dans sklearn :

On peut utiliser :
- [[sklearn.cluster.Kmeans - Kmeans facile\| sklearn.cluster.Kmeans]] pour réaliser une première classification d'un jeu de données.



## Généralisation des K-Means

On peut ajouter un second hyperparamètre à l'algorithme en remplaçant la fonction d'évaluation des clusters. L'algorithme reste similaire toutefois, avec une mesure fondée principalement sur la distance entre les points ainsi qu'une réévaluation à chaque itération jusqu'à convergence.

### K-neighbors

(A complêter)

- [[sklean.neighbors.KNeighborsClassifier]]