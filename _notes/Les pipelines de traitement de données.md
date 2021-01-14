---
title: Les pipelines de traitement de données
tags : ml-pratique
etat : hiver
---

Le chemin pour arriver à résultat comprenant de nombreuses étapes, il est possible de les lier entre elles pour faciliter leur exécution. Cet estimateur composite est appelé **pipeline**.

Cela permet par ailleurs d'éviter les erreurs liées à la manipulation des données et les problèmes d'augmentation d'espace occupé dans la mémoire vive.

## Pipeline sklearn

[[sklean]] possède une classe [`Pipeline`](https://scikit-learn.org/stable/modules/compose.html#pipeline) prenant en paramètre des transformateurs et des modèles de la même bibliothèque. 

Une fois instanciée, on peut manipuler l'objet `pipeline` composite comme un modèle sklearn classique et donc utiliser les méthodes `.fit()` et `.predict()` dessus. 

### Construction du pipeline sklearn

Un objet `.pipeline()` prend en paramètre une liste de [[tuple\|tuples]] : `[(clé, valeur), (clé, valeur),...]`. 

- Les clés sont les noms que l'on attribue aux composants du pipeline et qui permettent d'y accéder par la suite.
- Les valeurs correspondent aux objets sklearn qui sont intégrés au pipeline, avec leurs arguments (hors variables *input*.

Exemple :

```python
from sklearn.pipeline import Pipeline
from sklearn.svm import SVC
from sklearn.decomposition import PCA

estimators = [('reduce_dim', PCA()), ('clf', SVC())]
pipe = Pipeline(estimators)

pipe

> Pipeline(steps=[('reduce_dim', PCA()), ('clf', SVC(clf__C=10))])
````

On accède aux composants en précisant leur clé dans l'index :

```python
pipe.steps[0]
>('reduce_dim', PCA())

pipe[0]
> PCA()

pipe['reduce_dim']
> PCA()
````

On peut modifier les paramètres avec `.set_params()` et en spécifiant le nom du paramètre.

```python
pipe.set_params(clf__C=10)
````

Ce qui est utile si l'on réalise une *[[Evaluation des modèles en pratique#Ajuster les hyper paramètres avec la grid search\|grid search]]* :

```python
from sklearn.linear_model import LogisticRegression
from sklearn.grid_search import GridSearchCV

param_grid = {'reduce_dim' : ['passthrough', PCA(5), PCA(10)],
                  'clf' : [SVC(), LogisticRegression()],
                  'clf__C' : [0.1, 10, 100]}

grid_search = GridSearchCV(pipe, param_grid=param_grid)
````

Dans ce cas, on compare des méthodes de réduction de la dimension (`reduce_dim`) : ACP à 5 et à 10 composantes, mais aussi aucune : on utilise alors le mot-clé `passthrough` pour le signifier.

### Utilisation du pipeline

On active le pipeline avec `.fit(X_train,y_train)`. Cela va utiliser la méthode `.fit()`sur chaque estimateur, et `transform()`sur chaque transformateur, dans l'ordre définit lors de l'instanciation du pipeline.

Au cas où certaines étapes de transformation seraient particulièrement longues à calculer, il est possible de les garder en mémoire dans un dossier ou un objet [joblib.memory](https://joblib.readthedocs.io/en/latest/auto_examples/memory_basic_usage.html). Il suffit de préciser le dossier d'enregistrement avec l'option `memory`:

```python
from tempfile import mkdtemp
from shutil import rmtree

from sklearn.decomposition import PCA
from sklearn.svm import SVC
from sklearn.pipeline import Pipeline

estimators = [('reduce_dim', PCA()), ('clf', SVC())]

cachedir = mkdtemp()

pipe = Pipeline(estimators, memory=cachedir)

pipe
> Pipeline(memory=...,
 steps=[('reduce_dim', PCA()), ('clf', SVC())])

# Suppression de l'objet :
rmtree(cachedir)
````

Un transformeur gardé en mémoire ne peut être inspecté directement par la suite. Il est nécessaire d'utiliser l'attribut `.named_steps['my_step'].attribut_cherché` :

```python
cachedir = mkdtemp()
pca2 = PCA()
svm2 = SVC()
cached_pipe = Pipeline([('reduce_dim', pca2), ('clf', svm2)],
                       memory=cachedir)
cached_pipe.fit(X_digits, y_digits)

print(cached_pipe.named_steps['reduce_dim'].components_)

> [[-1.77484909e-19 ... 4.07058917e-18]]