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

On peut modifier les paramètres avec `.set_params()` et **en spécifiant le nom du paramètre de la manière suivante : `step_name__parameter_name`**.

```python
pipe.set_params(clf__C=10)
````

Ici, on a donc l'étape `clf` et le paramètre `C`.

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

> [-1.77484909e-19 ... 4.07058917e-18]
````

Un exemple complet pour s'y retrouver :

```python
# Setup the pipeline
steps = [('scaler', StandardScaler()),
         ('SVM', SVC())]

pipeline = Pipeline(steps)

# Specify the hyperparameter space
parameters = {'SVM__C':[1, 10, 100],
              'SVM__gamma':[0.1, 0.01]}

# Create train and test sets
X_train, X_test, y_train, y_test = train_test_split(X,y,
                 test_size=0.2,
                 random_state=21)

# Instantiate the GridSearchCV object: cv
cv = GridSearchCV(pipeline, param_grid=parameters)

# Fit to the training set
cv.fit(X_train,y_train)

# Predict the labels of the test set: y_pred
y_pred = cv.predict(X_test)

# Compute and print metrics
print("Accuracy: {}".format(cv.score(X_test, y_test)))
print(classification_report(y_test, y_pred))
print("Tuned Model Parameters: {}".format(cv.best_params_))
````

## Pipeline pandas

[[pandas]] propose une méthode `df.pipe()` associée à un objet dataframe, qui permet de chaîner les opérations réalisées. Il est en cela similaire à l'opérateur `%>%` que l'on retrouve dans le package [magrittr](https://r4ds.had.co.nz/pipes.html) du langage R.

On l'utilise ainsi :

```python
df.pipe(h)
	.pipe(g, arg1=a)
	.pipe(fun, arg2=b, arg3=c)
````

Ce qui est équivalent aux fonctions emboitées suivantes :

```python
fun(g(h(df), arg1=a), arg2=b, arg3=c)
```

L'exemple est tiré de la [documentation](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.pipe.html), que l'on peut consulter sur le sujet.