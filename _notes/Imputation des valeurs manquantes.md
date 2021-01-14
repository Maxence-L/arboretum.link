---
title: Imputation des valeurs manquantes
tags: ml-pratique
etat: printemps
---
Les valeurs manquantes d'une observation posent problème pour son utilisation et son évaluation dans un modèle. Elles doivent être remplacées ou supprimées.

Si une variable comprend un grand nombre de valeurs manquantes, il convient d'interroger un expert métier sur l'origine de cette perte d'information, qui peut, en soit, révéler une information.  

Plusieurs options sont disponibles pour résoudre le problème :

- Ne pas utiliser la variable concernée par un grand nombre de valeurs manquantes, si son usage n'est pas indispensable, ou la remplacer par une variable proche mais sans valeur manquante.

- Remplacer la valeur manquante par une valeur déterminée, soit statistiquement soit par une source externe (aussi appelé *imputation*).

- Traiter la valeur manquante comme une valeur à part entière, contenant une certaine information. Dans le cas des variables numérique, on peut les découper en classe et y ajouter une classe "valeur manquante"

Le module [sklearn.impute](https://scikit-learn.org/stable/modules/classes.html#module-sklearn.impute) est utile pour l'aspect pratique de cette étape. [[sklean]] propose un excellent tutoriel sur son site : [Imputation of missign values](https://scikit-learn.org/stable/modules/impute.html)

Il est aussi possible d'utiliser [sklearn.preprocessing.Imputer](https://scikit-learn.org/0.16/modules/generated/sklearn.preprocessing.Imputer.html), dont le résultat est de plus haut niveau et permet de l'intégrer dirrectement à un pipeline. On explique son fonctionnement à la [[Imputation des valeurs manquantes#Intégrer l'imputation à un pipeline avec imputer\|fin de la note]].

## Imputation simple : mode, moyenne, médiane

La méthode la plus simple d'imputation consiste à remplacer une valeur manquante par la valeur la plus fréquente (dans le cas d'une variable qualitative), la moyenne ou la médians (variable quantitative).

**Cette méthode fonctionne bien pour des techniques de régression mais pose problème pour d'autres cas, en créant un groupe important de valeurs autour d'un point précis.** 

Dans le cas des K-Means, on risque par conséquent de se retrouver avec un cluster d'observations comportant des valeurs manquantes ayant toutes la même valeur imputée, donc très proches.

![Effet de l'imputation](../assets/img/imputation-vm.png#center)

<div align="center">
	<p>
		<i> Effet de l'imputation sur la distribution des données </i>
</p>
</div>

### Avec sklearn : SimpleImputer()

La bibliothèque `.impute` de sklearn contient la classe SimpleImputer(). Celle-ci permet de réaliser les principales opérations d'imputation, comme le remplacement par la moyenne ou la médiane. 

Comme les autres classes, elle s'utilise avec les méthodes .fit() et .transform()

- .fit(X) 'calibre' la classe sur les données X. Dans le cas d'une imputation par moyenne, elle attribue donc à chaque colonne de X une moyenne qui resservira lors de l'imputation

- .transform(X') applique l'imputation des valeurs manquantes selon les paramètres précisés lors de l'initialisation de la classe, ainsi que les données passées dans .fit(). On peut passer des données alternatives dans .transform() : l'imputation se fera sur la base des paramètres des données passées dans .fit()

- .fit_transform(X) réalise les deux opérations d'un coup, sur X. L'objet SimpleImputer créé garde en mémoire les paramètres et l'on peut le réutiliser pour effectuer une opération .transform() si l'on le souhaite.

```python
import numpy as np
from sklearn.impute import SimpleImputer
imp_mean = SimpleImputer(missing_values=np.nan, strategy='mean')
imp_mean.fit([ [7, 2, 3], [4, np.nan, 6], [10, 5, 9] ])

X = [ [np.nan, 2, 3], [4, np.nan, 6], [10, np.nan, 9] ]
print(imp_mean.transform(X))

> 	[	[ 7.   2.   3. ]
	 	[ 4.   3.5  6. ]
 	 	[10.   3.5  9. ] ]
````

 A noter toutefois si l'on utilise des données issues de dataframe : `SimpleImputer()` renvoie un array et retire le nom des colonnes. Il faut donc convertir le résultat et le rajouter au dataframe.**

```python
# Make copy to avoid changing original data (when imputing)
X_train_plus = X_train.copy()
X_valid_plus = X_valid.copy()

# Make new columns indicating what will be imputed
for col in cols_with_missing:
    X_train_plus[col + '_was_missing'] = X_train_plus[col].isnull()
    X_valid_plus[col + '_was_missing'] = X_valid_plus[col].isnull()

# Imputation
my_imputer = SimpleImputer()
imputed_X_train_plus = pd.DataFrame(my_imputer.fit_transform(X_train_plus))
imputed_X_valid_plus = pd.DataFrame(my_imputer.transform(X_valid_plus))

# Imputation removed column names; put them back
imputed_X_train_plus.columns = X_train_plus.columns
imputed_X_valid_plus.columns = X_valid_plus.columns

print("MAE from Approach 3 (An Extension to Imputation):")
print(score_dataset(imputed_X_train_plus, imputed_X_valid_plus, y_train, y_valid))
`````


## Imputation multivariée

### Estimation

Plusieurs méthodes d'estimation multivariées sont possibles pour remplacer les valeurs manquantes. L'idée est de chercher des observations complètes proches pour servir de réference aux valeurs manquantes, ce qui évite l'effet d'agrégation autour de la moyenne.

- La régression linéaire

![](../assets/img/regression-imputation.png#center)

<div align="center">
	<p>
		<i>Imputation par la moyenne et par une régression [1]</i>
</p>
</div>

- [[Arbre de décision]]

- Classification (dans le cas de variable qualitative) - par [[Analyse discriminante\|analyse discriminante]], par exemple.

- [[K-neighbors]] : On crée des groupes d'individus et l'on impute à la valeur manquante la moyenne de son groupe. (utiliser [sklearn.impute.KNNImputer](https://scikit-learn.org/stable/modules/generated/sklearn.impute.KNNImputer.html))

### Estimation itérative

La méthode [sklearn.impute.IterativeImputer](https://scikit-learn.org/stable/modules/generated/sklearn.impute.IterativeImputer.html#sklearn.impute.IterativeImputer)  consiste à utiliser l'ensemble des variables complètes pour imputer les valeurs manquantes d'une variable, puis à utiliser l'ensemble des variables (y compris la variable ayant reçu de nouvelles valeurs) pour estimer les valeurs manquantes de la prochaine variable.

Un peut faire cette opération pour $n$ itérations, afin de lisser les divergences liées à l'ordre dans lesquelles les variables sont estimées. L'utilisation est expliquée sur le site [scikit-learn](https://scikit-learn.org/stable/modules/impute.html#multivariate-feature-imputation).

## Intégrer l'imputation à un pipeline avec `.imputer`

L'imputeur du module `preprocessing` fonctionne comme un transformateur classique (que l'on peut retrouver, par exemple, pour le calcul de l'ACP) et propose donc des méthodes `.fit()` et `.fit_transform()`. Cela permet d'éviter les manipulation d'arrays.

L'imputation simple est toutefois la seule proposée : on a accès aux trois méthodes suivantes :

`mean, median, most_frequent`

L'intégration à un [[Les pipelines de traitement de données\|pipeline]] se fait ainsi :

```python
# Import the Imputer module
from sklearn.preprocessing import Imputer
from sklearn.svm import SVC
from sklearn.pipeline import Pipeline

# Setup the Imputation transformer: imp
imp = Imputer(missing_values='NaN', strategy='most_frequent', axis=0)

# Instantiate the SVC classifier: clf
clf = SVC()

# Setup the pipeline with the required steps: steps
steps = [('imputation', imp),
        ('SVM', clf)]

pipeline = Pipeline(steps)
````

On peut aussi directement déclarer l'imputeur dans le pipeline :

```python
# Import necessary modules
from sklearn.preprocessing import Imputer
from sklearn.pipeline import Pipeline
from sklearn.regression import ElasticNet
from sklearn.preprocessing import StandardScaler

# Setup the pipeline steps: steps
steps = [
    ('imputation', Imputer(missing_values='NaN', strategy='mean', axis=0)),
    ('scaler', StandardScaler()),
    ('elasticnet', ElasticNet())
    ]

# Create the pipeline: pipeline
pipeline = Pipeline(steps)

# Specify the hyperparameter space
parameters = {'elasticnet__l1_ratio' : np.linspace(0,1,30)}

# Create train and test sets
X_train, X_test, y_train, y_test = train_test_split(X,y, test_size=0.4, random_state=42)

# Create the GridSearchCV object: gm_cv
gm_cv = GridSearchCV(pipeline, param_grid=parameters)

# Fit to the training set
gm_cv.fit(X_train,y_train)

# Compute and print the metrics
r2 = gm_cv.score(X_test, y_test)
print("Tuned ElasticNet Alpha: {}".format(gm_cv.best_params_))
print("Tuned ElasticNet R squared: {}".format(r2))
````


