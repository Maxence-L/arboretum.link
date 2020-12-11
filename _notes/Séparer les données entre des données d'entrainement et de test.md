---
title : Séparer les données entre des données d'entrainement et de test
tags : ml-pratique
etat : printemps
---

Pour tester notre régression de manière simple, on peut séparer les données disponibles pour obtenir des données d'entrainement et des données de test.

`sklearn.model_selection - train_test_split` permet cette procédure simplement.

La syntaxe est la suivante :

```python
train_test_split(X, y, 
				test_size = float, 
				random_state= int, 
				shuffle = bool, 
				stratify = array)
````

- `test_size` correspond à la proportion (entre 0 et 1) de données prélevées pour les données de test.

- `random_state` correspond à la `seed` utilisée pour mélanger les données

- `shuffle` correspond au fait de mélanger ou non les données avant de les séparer

- `stratify` contient un array d'étiquettes (*labels*) de même longueur que le jeu de données. On aura dans chaque jeu de donnée créé la même proportion de d'étiquettes que dans l'array donné en paramètre. Par exemple, si celui-ci contient 30% d'hommes et 70% de femmes, alors `(X_train, Sex_train)` et `(X_test, Sex_test)` seront composés à 30% d'hommes et à 70% de femmes.  

Un exemple en pratique :

```python
# Import necessary modules
from sklearn.neighbors import KNeighborsClassifier
from sklearn.model_selection import train_test_split

# Create feature and target arrays
X = digits.data
y = digits.target

# Split into training and test set
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size = 0.2, random_state=42, stratify=y)

# Create a k-NN classifier with 7 neighbors: knn
knn = KNeighborsClassifier(n_neigbors=7)

# Fit the classifier to the training data
knn.fit(X_train, y_train)

# Print the accuracy
print(knn.score(X_test, y_test))

> 0.9833333333333333
````
