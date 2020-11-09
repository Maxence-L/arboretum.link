---
title: sklearn.impute - Gérer les valeurs manquantes
tags: sklearn
etat: printemps
---

.impute est une sous-bibliothèque de sklearn utile pour [[Gérer les valeurs manquantes|gérer les valeurs manquantes]].

### SimpleImputer()

Elle contient notamment la classe SimpleImputer(). Celle-ci permet de réaliser les principales opération d'imputation, comme le remplacement par la moyenne ou la médiane. 

Comme les autres classes classes, elle s'utilise avec les méthodes .fit() et .transform()

- .fit(X) 'calibre' la classe sur les données X. Dans le cas d'une imputation par moyenne, elle attribue donc à chaque colonne de X une moyenne qui reservira lors de l'imputation

- .transform(X') applique l'imputation des valeurs manquantes selon les paramètres précisés lors de l'initialisation de la classe, ainsi que les données passées dans .fit(). On peut passer des données alternatives dans .transform() : l'imputation se fera sur la base des paramètres des données passées dans .fit()

- .fit_transform(X) réalise les deux opérations d'un coup, sur X. L'objet SimpleImputer créé garde en mémoire les paramètres et l'on peut le réutiliser pour effectuer une opération .transform() si l'on le souhaite.

```python
import numpy as np
from sklearn.impute import SimpleImputer
imp_mean = SimpleImputer(missing_values=np.nan, strategy='mean')
imp_mean.fit([[7, 2, 3], [4, np.nan, 6], [10, 5, 9]])

X = [[np.nan, 2, 3], [4, np.nan, 6], [10, np.nan, 9]]
print(imp_mean.transform(X))

> 	[[ 7.   2.   3. ]
	 [ 4.   3.5  6. ]
 	 [10.   3.5  9. ]]
````

A noter toutefois si l'on utilise des [[dataframe\|dataframes]] : .impute renvoie un aray et retire le nom des colonnes, il faut reconvertir le résultat et les rajouter par la suite.

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
