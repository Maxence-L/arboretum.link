---
title: Dbscan
tags: Apprentissage-non-supervisé
etat: printemps
---

L'algorithme DBSCAN sert à classifier des données. Il est particulièrement utile lorsque les données se regroupent par forme de densité importante en prenant des formes diverses qui ne sont pas reconnaissables par un [[K-neighbors]] classique, se fondant sur les centroïdes des formes.

Initialisation avec [[sklearn.cluster.DBSCAN]] : 
```python
from sklearn.cluster import DBSCAN
dbscan_model = DBSCAN(eps=epsilon, min_samples=min_points, metric=metric_function)
```

Dans le cas de données consistant en des cercles concentriques :


![dbscan-concentrique1.png](/assets/img/dbscan-concentrique1.png#center)


La forme aura du mal à être calculée par un K-Means utilisant les centroïdes, étant donné que chaque forme possède le même centre.


![dbscan-centroides.png](/assets/img/dbscan-centroides.png#center)


#### Fonctionnement

L'algorithme DBSCAN fonctionne ainsi :

1. On sélectionne un point aléatoire dans les données.
2. On obtient une liste de points voisins ayant une distance `epsilon` à ce point.
3. Si moins de `min_points` voisins sont découverts, on répète l'étape 1. en utilisant un point aléatoire différent. Si tous les points restants n'ont pas de voisins ou sont déjà dans un groupe, on les classifie dans un groupe unique (*outliers*)
4. Nous répétons les étapes 2. et 3. pour tous les points voisins au point de départ découverts et l'on les ajoute à un groupe.
5. Lorsque le groupe/cluster n'a plus de points à ajouter, on répète les étapes de 1 à 5 pour tous les points de données.

La méthode *sklearn.cluster* utilisée est [[sklearn.cluster.DBSCAN]].

 Note : `epsilon` correspond à l'option `eps`.
 
```python
from sklearn.cluster import DBSCAN

cluster_model = DBSCAN(eps=epsilon, min_samples=min_points)
rock_clusters = cluster_model.fit_predict(rocks)

colors = [['g', 'y', 'k'][cluster] for cluster in rock_clusters]
plt.scatter(x_coordinates, y_coordinates, color=colors)
plt.show()
```

Comme on peut le voir, les clusters sont correctement regroupés :

![dbscan-clusters.png](/assets/img/dbscan-clusters.png#center)

#### DBSCAN vs [[K-neighbors]]

- DBSCAN est utile dans le cas où les groupes de données sont denses et ont des formes courbées. 

- L'algorithme ne nécessite pas de trouver le nombre optimal de clusters.

- Il est possible de filtrer les outliers situés à l'extérieur des clusters. **Dans ce cas, DBSCAN leur donne un ID de -1 : il ne peut être regroupé dans un des clusters existant**

- DBSCAN ne peut être appliqué à de nouvelles données, comme dans le cas des K-Means. Il faut combiner des anciennes et les nouvelles données puis refaire tourner l'algorithme.

- DBSCAN ne dépendant pas de la moyenne du carré de la distance, il est possible de l'utiliser pour des données ne remplissant pas toutes les conditions des [[normes d'un vecteur::Norme d'un vecteur]].

On peut d'ailleurs préciser la norme utilisée pour calculer la distance entre deux points. Si l'on prend la [[distance de Manhattan]] :

```python
def manhattan_distance(point_a, point_b):
    num_blocks = np.sum(np.absolute(point_a - point_b))
    return num_blocks
````

On peut préciser que l'on souhaite utiliser cette fonction de distance dans le calcul de l'algorithme, avec l'option `metric` :

```python
points = [ [35, 5], [33, 6], [37, 4], [40, 7], [45, 5] ]
clusters = DBSCAN(eps=1, min_samples=3,
                  metric=manhattan_distance).fit_predict(points)
```

#### Limitations :

L'algorithme DBSCAN étant utile pour calculer des clusters de données réparties avec une densité similaire et définie au préalable, il peut être difficile de déterminer en avance le paramètre `epsilon`, notamment lorsque les données changent.

Par exemple, si l'on cherche à identifier les villes dans un pays, on rencontrera un problème : les habitations ne sont pas réparties aussi densément en fonction des régions. Si l'on souhaite réutiliser le modèle pour un pays différent, il faudra ré-estimer `eps` et `min_samples` pour ce pays en particulier. 

Un savoir spécifique au secteur analysé devra être ajouté, du fait qu'il n'existe pas de méthode supérieure pour déterminer la valeur correcte des paramètres.
