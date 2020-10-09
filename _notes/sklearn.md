---
title: sklearn
tags: #sklearn #ml-pratique
---

#ml #scikit-learn #meta
Scikit-Learn est une bibliothèque de code utilisée pour appliquer les principaux algorithmes de machine learning à des jeux de données.

Chaque technique correspond à un objet, sur lequel on peut agir en définissant les paramètres de départ, en l'appliquant à des données et en utilisant le modèle ainsi construit pour prédire de nouvelles données.

[Le glossaire](https://scikit-learn.org/dev/glossary.html#glossary-of-common-terms-and-api-elements)

## Principes de modélisation

Les étapes de construction d'un modèle sont les suivantes :

1. Définition du modèle : c'est le moment où l'on choisit quel algorithme l'on utilisera pour analyser les données.

2. Ajustement du modèle (*model fit*) aux données et définition des paramètres. Dans le cas d'une régression, par exemple, cela revient à calculer la valeur de $$\beta$$. 

3. Prédiction : on utilise le modèle pour produire une estimation des de la variable cible. Toujours de la cas d'une régression, cela correspond au calcul de $$\beta X$$.

4. Evaluation : On calcule la précision et divers indicateurs sur la fiabilité de la prédiction en fonction des paramètres de départ.

L'utilisation de sklearn est la suivante pour chaque étape :

1. On choisit la classe correspondant à l'algorithme désiré dans la bibliothèque de [sklearn](https://scikit-learn.org/stable/user_guide.html) et l'on crèe un objet en l'initialisant.

2.  On utilise la méthode `fit()` en passant les paramètres du modèle (données, valeurs diverses...)

3. On utilise `predict()` en passant les données-cibles de test. Le résultat est soit une estimation de la valeur de $$Y$$ ou de sa classe, dans le cas d'un algorithme de [[classification]].

4. On peut évaluer la prédiction au moyen de [[sklearn.metrics]] en séparant les données avec [[sklearn.model_selection.train_test_split]]

Exemple dans le cas des [[K-Means]] :

```python
# Importation de KMeans :
from sklearn.cluster import KMeans

X = np.array([[1, 2], [1, 4], [1, 0],
              [10, 2], [10, 4], [10, 0]])

# Creating the classifier, with custom parameters :
cluster_model = KMeans(n_clusters=2)

# Applying the classifier to our data :
classifier = cluster_model.fit(X)

# On peut à présent classifier des nouveaux point en utilisant le modèle :
classifier.predict([0,0],[12,3])

> array([1, 0], dtype=int32)
```

## Sauvegarde du modèle
Il est possible d'enregistrer le modèle dans la RAM avec le module [[pickle]]. On le sauvegarde avec `f = pickle.dumps(model)`et on le charge avec `pickle.loads(f)`:

```python
import pickle

f = pickle.dumps(classifier)
classifier2 = pickle.loads(f)

classifier2.predict([0,0],[12,3])

> array([1, 0], dtype=int32)
````

On peut aussi enregistrer le modèle sur le disque avec [[joblib]][^1] `joblib.dump`et `joblib.load``

```python
from joblib import dump, load
dump(f, 'filename.joblib')

classifier3 = load('filename.joblib')
````





[^1]: https://joblib.readthedocs.io/en/latest/persistence.html]
