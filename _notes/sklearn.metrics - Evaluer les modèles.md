---
title: sklearn.metrics - Evaluer les modèles
tags: sklearn
etat: printemps
---

sklearn.metrics contient un très grand nombre de méthodes visant à réaliser des mesures sur la qualité des prédictions. Cette fiche a pour but de présenter les principales.

L'aide de [[sklearn]] sur le [sujet des mesures](https://scikit-learn.org/stable/modules/model_evaluation.html#model-evaluation) est disponible sur le site, tout comme la liste des [méthodes disponibles](https://scikit-learn.org/stable/modules/classes.html#module-sklearn.metrics). Les liens vers la page d'aide spécifique de chaque méthode est dans le titre.

Il est possible de réaliser des coupes dans les données avec [[sklearn.model_selection.train_test_split]] afin de réiterer la mesure de la performance du modèle avec des permutations différentes de données. Cela permet d'obtenir une estimation moyenne de la performance du modèle.

### Utilisation

En fonction de leur objet, les mesures peuvent fournir un indicateur (absolu ou relatif) pour évaluer le paramètre optimal d'un modèle. Ou même des algorithmes différents. Un exemple se rapportant aux [[Forêts aléatoires]], utilisant [[sklearn.tree.DecisionTreeRegressor]] :

```python
def get_mae(max_leaf_nodes, train_X, val_X, train_y, val_y):
    model = DecisionTreeRegressor(max_leaf_nodes=max_leaf_nodes, random_state=0)
    model.fit(train_X, train_y)
    preds_val = model.predict(val_X)
    mae = mean_absolute_error(val_y, preds_val)
    return(mae)
	
candidate_max_leaf_nodes = [5, 25, 50, 100, 250, 500]
scores = {leaf_size: get_mae(leaf_size, train_X, val_X, train_y, val_y) for leaf_size in candidate_max_leaf_nodes}
best_tree_size = min(scores, key=scores.get)
```

### Mesures
#### Ecart moyen absolu (*[.mean_absolute_error()](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_absolute_error.html#sklearn.metrics.mean_absolute_error)*)

L'écart absolu moyen est la moyenne de la valeur absolue de la distance des écarts entre les valeurs observées et celles de la prédiction.

L'utilisation est simple :

```python
from sklearn.metrics import mean_absolute_error
y_true = [3, -0.5, 2, 7]
y_pred = [2.5, 0.0, 2, 8]
mean_absolute_error(y_true, y_pred)

> 0.5
```

On peut aussi passer des arrays. :
```python
y_true = [[0.5, 1], [-1, 1], [7, -6]]
y_pred = [[0, 2], [-1, 2], [8, -5]]
mean_absolute_error(y_true, y_pred)

> 0.75
```

Il est possible de préciser si l'on veut donner un poids `x` à chaque observation pour le calcul de la moyenne avec`sample_weight=x`. Avec l'option `multioutput`, il est possible de modifier la sortie pour obtenir les erreurs individuelles et de donner un poids aux erreurs de chaque colonne.

#### Précision (*[.accuracy_score()](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html#sklearn.metrics.accuracy_score)*)

La [[Précision et rappel]] est utilisé pour mesurer la performance des modèles de [[clustering]]. Elle correspond à la fraction d’éléments correspondant effectivement à la classe prédite (classe réelle == classe prédite) parmi le total des éléments sélectionnés. 

L'utilisation est simple :

```python
from sklearn.metrics import accuracy_score

y_pred = [0, 2, 1, 3]
y_true = [0, 1, 2, 3]
accuracy_score(y_true, y_pred)

> 0.5

accuracy_score(y_true, y_pred, normalize=False)

> 2
````

A quoi correspond l'option `normalize`? Par défaut (`normalize=True`), *.accuracy_score()* renvoie la proportion d'éléments correctement classifiés. Spécifier `False` renvoie le nombre d'éléments correctement classifiés.
