---
title : Evaluer les modèles en pratique
tags : ml-pratique
etat : hiver
toc : true
---

Cet article traite de la mise en pratique de l'évaluation de modèles. Pour les questions théoriques, voir :

- [[Indicateurs de performance des classificateurs]] : F-score, précision, rappel, courbe ROC...
- [[Evaluer les performance d'un modèle de prédiction de valeurs]] : R2, variance ...

## Séparer les données entre des données d'entrainement et de test

Pour tester notre modèle de manière simple, on peut séparer les données disponibles pour obtenir des données d'entrainement et des données de test.

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


## Validation croisée

La validation croisée est la généralisation de la séparation des données à l'ensemble du jeu disponible, afin d'éviter un biais lié au choix des données test. On divise dans ce cas les données en $k$ segments. Une fois les $k$ segments obtenus, on entraine le modèle pour prédire la variable objectif de chaque segment à partir des données des autres segments.

![](/assets/img/kfoldcv.png#center)

<div align="center">
	<p>
  Une représentation d'une validation croisée sur 5 découpes. Chaque découpe est utilisée comme jeu de validation et le reste sert à entrainer le modèle. L'erreur du modèle est estimée en calculant l'erreur moyenne pour les 5 estimations. Source : ISLR.
	</p>
</div>

## Ajuster les (hyper)paramètres avec la *grid search*

La plupart des modèles d'apprentissage machine possèdent des paramètres que l'on peut ajuster pour en améliorer la performance. Par exemple :

- [[Régression linéaire]] : quelles variables utiliser ?
- Regression ridge, lasso : Quelle valeur $\alpha$ ?
- [[K-neighbors]] : combien de `n_neighbors` ? Quelle méthode de calcul de la distance utiliser ?

On nomme ces paramètres "hyperparamètres" (aussi appelés méta-paramètres) car ils ne peuvent être déterminé par l'entrainement du modèle (comme la valeur $\beta$ dans le cas de la régression linéaire).

Le *grid search* consiste à tester les paramètres sur nos données en entraînant successivement plusieurs modèles et à comparer leurs résultats. Si l'on dispose d'un grand volume de données, cette étape se prête bien à la parallélisation des calculs.

Par exemple, pour un modèle dont les méta-paramètres sont *Alpha* et *C*, la *grid search* produira le tableau suivant.
![](/assets/img/grid_search_table.png#center)

<div align="center">
	<p>
  Source : Datacamp
	</p>
</div>

Dans ce cas, la valeur maximum mesurée (0.726) se situe pour *C* = 0.3 et *Alpha* = 0.2. Nous choisirons donc ce paramètre pour le modèle final.

### Avec sklearn :

Pour les besoins de l'exemple, on utilise les [[K-neighbors]]. La méthode [`GridSearchCV` ](https://scikit-learn.org/stable/modules/generated/sklearn.model_selection.GridSearchCV.html?highlight=grid%20search%20cv#sklearn.model_selection.GridSearchCV) applique une *grid search* et une validation croisée (5 découpes par défaut, mais modifiable en paramêtre).

```python
from sklearn.model_selection import GridSearchCV
param_grid = {'n_neighbors': np.arange(1, 50)}
knn = KNeighborsClassifier()

knn_cv
GridSearchCV(knn, param_grid, cv=5)

knn_cv.fit(X, y)
knn_cv.best_params_
````