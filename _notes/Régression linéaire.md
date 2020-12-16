---
title : Régression linéaire
tags : Apprentissage-supervisé
etat : hiver
---

La régression linéaire est une technique de base de l'analyse de données permettant d'estimer la relation linéaire qu'il peut exister entre deux variables.


## Mise en pratique avec [[sklean]] :

### Séparation entre un jeu de données de test et d'entrainement

Avec `train_test_split` de `sklearn.model_selection` : 

```python
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size = 0.3, random_state=42)
```

### Modèle

[[sklearn]] contient bien évidemment une classe propre à la régression linéaire, `LinearRegression()`, faisant partie du module `sklearn.linear_model`.

```python
# Import LinearRegression
from sklearn.linear_model import LinearRegression

# Create the regressor: reg
reg = LinearRegression()

# Fit the model to the data
reg.fit(X_train, y_train)
````

### Evaluation

On calcule le [[R2]] avec la méthode `.score` et l'écart-type de l'erreur au carré avec `sklearn.metrics - mean_squared_error`.

```python
from sklearn.metrics import mean_squared_error

# Compute and print R^2 and RMSE
print("R^2: {}".format(reg.score(X_test, y_test)))
rmse = np.sqrt(mean_squared_error(y_test, y_pred))
print("Root Mean Squared Error: {}".format(rmse))
````

### Evaluation par [[validation croisée]]

Pour éviter que le split ait une trop forte influence sur le résultat, on partage les données en plusieurs segment et l'on réalise l'estimation sur chacun d'entre eux. On mesure ensuite le [[R2]] moyen.

```python
from sklearn.model_selection import cross_val_score

# Create a linear regression object: reg
reg = LinearRegression()

# Compute 5-fold cross-validation scores: cv_scores
cv_scores = cross_val_score(reg, X, y , cv=5)

# Print the 5-fold cross-validation scores
print(cv_scores)

print("Average 5-Fold CV Score: {}".format(np.mean(cv_scores)))
````

### Visualisation

On peut tracer dans [[Matplotlib]] la droite correspondant à la relation entre une variable explicative et la variable objectif. On utilise ici `np.linspace` :

```python
# Create the prediction space
prediction_space = np.linspace(min(X_train), max(X_train)).reshape(-1,1)

# Compute predictions over the prediction space: y_pred
y_pred = reg.predict(prediction_space)

# Plot regression line
fig, ax = plt.subplots()
plt.plot(x=X_train, y=y_train, kind='scatter', ax=ax)
ax.plot(prediction_space, y_pred, color='black', linewidth=3)
plt.show()
````

![](/assets/img/regression_ex.png#center)






