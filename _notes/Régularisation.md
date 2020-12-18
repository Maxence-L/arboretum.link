---
title : Régularisation
tags: Apprentissage-supervisé
etat : hiver
---

La régularisation consiste à modifier la fonction de coût d'une régression. Le but est de changer le comportement de l'estimateur des moindres carrés afin de pénaliser les coefficients trop importants ou de réduire le nombre de variables explicatives au strict minimum.

## Application avec [[sklearn]]

Le module `sklearn.linear_model` contient des modèles prenant en compte les principales formules de régularisation. L'usage n'est pas différent de la mise en œuvre d'une [[Régression linéaire\|régression linéaire]] classique.

### Lasso

On utilise la classe `Lasso`. La pénalité est précisée avec `alpha` et l'on peut normaliser automatiquement les variables avec `normalize=True`. À noter que l'attribut `alpha` peut être modifié en y accédant avec `.alpha`.

```python
from sklearn.linear_model import Lasso

X_train, X_test, y_train, y_test = train_test_split(X, y,    test_size = 0.3, random_state=42)

lasso = Lasso(alpha=0.1, normalize=True)

lasso.fit(X_train, y_train)

lasso_pred = lasso.predict(X_test)

lasso.score(X_test, y_test)
````

Les coefficients peuvent être visualisés en faisant référence à l'attribut `.coef_` :

```python
lasso_coef = lasso.fit(X, y).coef_

_ = plt.plot(range(len(names)), lasso_coef)
_ = plt.xticks(range(len(names)), names, rotation=60)
_ = plt.ylabel('Coefficients')plt.show()
````

### Régression Ridge

On utilise la classe `Ridge`. Tout comme 

````python
from sklearn.linear_model import Ridge

X_train, X_test, y_train, y_test = train_test_split(X, y,    test_size = 0.3, random_state=42)

ridge = Ridge(alpha=0.1, normalize=True)

ridge.fit(X_train, y_train)

ridge_pred = ridge.predict(X_test)ridge.score(X_test, y_test)
````

On peut examiner le résultat pour plusieurs valeurs d'alpha en bouclant :

```python
# Import necessary modules
from sklearn.linear_model import Ridge
from sklearn.model_selection import cross_val_score
import matplotlib.pyplot as plt

# Display function

def display_plot(cv_scores, cv_scores_std):
    fig = plt.figure()
    ax = fig.add_subplot(1,1,1)
    ax.plot(alpha_space, cv_scores)

    std_error = cv_scores_std / np.sqrt(10)

    ax.fill_between(alpha_space, cv_scores + std_error, cv_scores - std_error, alpha=0.2)
    ax.set_ylabel('CV Score +/- Std Error')
    ax.set_xlabel('Alpha')
    ax.axhline(np.max(cv_scores), linestyle='--', color='.5')
    ax.set_xlim([alpha_space[0], alpha_space[-1]])
    ax.set_xscale('log')
    plt.show()

# Setup the array of alphas and lists to store scores
alpha_space = np.logspace(-4, 0, 50)
ridge_scores = []
ridge_scores_std = []

# Create a ridge regressor: ridge
ridge = Ridge(normalize=True)

# Compute scores over range of alphas
for alpha in alpha_space:

    # Specify the alpha value to use: ridge.alpha
    ridge.alpha = alpha
    
    # Perform 10-fold CV: ridge_cv_scores
    ridge_cv_scores = cross_val_score(ridge, X,y, cv=10)
    
    # Append the mean of ridge_cv_scores to ridge_scores
    ridge_scores.append(np.mean(ridge_cv_scores))
    
    # Append the std of ridge_cv_scores to ridge_scores_std
    ridge_scores_std.append(np.std(ridge_cv_scores))

# Display the plot
display_plot(ridge_scores, ridge_scores_std)
````

Le résultat :

![](/assets/img/ridge_loop.png#center)
