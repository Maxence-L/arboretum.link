---
title: K-neighbors
tags : Apprentissage-non-supervisé
etat : hiver
---

L'algorithme dit des *K-neighbors* regroupe une famille d'algorithmes utilisés pour classifier des données de manière non-supervisée (sans variable réponse). Le seul hyperparamètre que l'on passera au modèle sera celui du nombre de classes que l'on souhaite obtenir.

## Fonctionnement de l'algorithme K-Means

L'algorithme K-means est un algorithme de [[classification]] (*clustering*) très courant et souvent parmis les premiers du genre abordés. Par exemple, [Chris McKinlay l'a utilisé pour son étude des profils sur OKCupid](https://www.wired.com/2014/01/how-to-hack-okcupid/). 

L'algorithme catégorise les observations en fonction de leur distance au point moyen ([[Centroïde\|centroïde]]) de la classe. Une fois qu'une classe a été attribuée à chaque point, on peut recalculer la centroïde et réitérer la procédure, jusq'à convergence. \[A développer]

On mesure la performance de l'algorithme au moyen de l'[[Inertia|inertie]] du résultat. L'inertie correspond à la densité des clusters et est égale à la somme du carré de la distance entre chaque point et le centre de son cluster. 

Cette mesure sert par la suite à déterminer le nombre optimal de clusters, au moyen d'un graphique en coude :

![elbow](/assets/img/kmeanselbow.png#center)
<div align="center">
	<p>
  Inertie de la classification en fonction du nombre de clusters
</p>
</div>

Quelques remarques :

- La convergence n'assure pas l'optimalité et en fonction des valeurs des points de départ, l'algorithme peut converger vers des états plus ou moins pertinents. Il peut être utile de faire tourner plusieurs fois l'algorithme et de comparer les résultats.

- L'algorithme K-means nécessite que les points se situent dans un [[Norme d'un vecteur\|espace remplissant les conditions de distance "métrique"]], afin de calculer leur distance respective.

- Le [[Silhouette Score\|score de silhouette]] peut aussi être utilisé afin de déterminer si les clusters sont aggrégés correctement.

### Utilisation dans sklearn :

On peut utiliser :
- [[sklearn.cluster.Kmeans\| sklearn.cluster.Kmeans]] pour réaliser une première classification d'un jeu de données.

## Généralisation des K-Means

On peut ajouter un second hyperparamètre à l'algorithme en remplaçant la fonction d'évaluation des points. L'algorithme reste similaire toutefois, avec une mesure fondée principalement sur la distance entre les points ainsi qu'une réévaluation à chaque itération jusqu'à convergence.

### K-neighbors

Dans le cas des K-neigbors, on cherche les K points les plus proches du point à catégoriser et l'on lui attribue la classe correspondant à la classe majoritaire parmi ces voisin (système de vote). 

> A noter : Cette technique prend en paramètre une valeur-objectif (la classe des points utilisée pour le vote).

![kneighbors](/assets/img/kneigbors.png#center)

<div align="center">
	<p>
 Source : <a href="https://fr.wikipedia.org/wiki/M%C3%A9thode_des_k_plus_proches_voisins">Wikipedia</a>
</p>
</div>

L'échantillon de test (cercle vert) pourrait être classé soit dans la première classe de carré bleu ou la seconde classe de triangles rouges. Si k = 3 (cercle en ligne pleine) il est affecté à la seconde classe car il y a deux triangles et seulement un carré dans le cercle considéré. Si k = 5 (cercle en ligne pointillée) il est affecté à la première classe (3 carrés face à deux triangles dans le cercle externe).

On retrouve cet algorithme dans [[sklean]] avec [sklean.neighbors.KNeighborsClassifier](https://scikit-learn.org/stable/modules/generated/sklearn.neighbors.KNeighborsClassifier.html#sklearn.neighbors.KNeighborsClassifier)

```python
from sklearn.neighbors import KNeighborsClassifier

# Create and fit the model with 6 neighbors
knn = KNeighborsClassifier(n_neigbors=6)
knn.fit(X_train, y_train)

# Predict on the test features, print the results
pred = knn.predict(X_test)[0]
print("Prediction for test example 0:", pred)

> Prediction for test example 0: 1.0

# Mean accuracy of the model :
knn.score(X_test,y_test)

> 0.91
````

On peut tester la performance en fonction du nombre de k-voisins en testant les configurations :

```python
# Setup arrays to store train and test accuracies
neighbors = np.arange(1, 9)
train_accuracy = np.empty(len(neighbors))
test_accuracy = np.empty(len(neighbors))

# Loop over different values of k
for i, k in enumerate(neighbors):
    # Setup a k-NN Classifier with k neighbors: knn
    knn = KNeighborsClassifier(k)

    # Fit the classifier to the training data
    knn.fit(X_train, y_train)
    
    #Compute accuracy on the training set
    train_accuracy[i] = knn.score(X_train, y_train)

    #Compute accuracy on the testing set
    test_accuracy[i] = knn.score(X_test, y_test)

# Generate plot
plt.title('k-NN: Varying Number of Neighbors')
plt.plot(neighbors, test_accuracy, label = 'Testing Accuracy')
plt.plot(neighbors, train_accuracy, label = 'Training Accuracy')
plt.legend()
plt.xlabel('Number of Neighbors')
plt.ylabel('Accuracy')
plt.show()
```
![](/assets/img/k-voisins-perf.png#center)