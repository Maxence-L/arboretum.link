---
title: Mise à l'échelle des données - standardisation et normalisation
tags: Maths
etat : Printemps
---

La mise à l'échelle consiste à altérer les données afin de réduire leur variance ou leur valeur absolue. Elle est utile pour les cas suivants :

- Lorsque les variables d'un jeu de données ont des ordres de grandeur différents, ces derniers peuvent fausser les estimations des algorithmes d'apprentissage automatique.

- Les classificateurs utilisant la distance euclidienne seront affectés si un des composant est distribué sur un espace beaucoup plus grand que les autres. 

- Il sera difficile de comparer les coefficients d'une régression linéaire si les variables sur lesquels ils s'appliquent n'ont pas d'échelle comparable. 

- La [[gradient descent\|descente du gradient]] est plus rapide si les données partagent la même échelle. (*rescaled*).


## Normalisation 'Min-Max'

C'est la méthode la plus simple, qui met à l'échelle les données de manière à ce qu'elles soient bornées entre $[0, 1]$. Le $max(x)$ sera égal à 1 et le $min(x)$ sera égal à 0.

Elle consiste à soustraire, pour chaque variable, la valeur minimum et à diviser le résultat par l'écart maximum rencontré :

$$x_{norm} = \frac{x - min(x)}{max(x) - min(x)}$$

Où $x$ est la valeur originelle. 

Si l'on veut définir une échelle arbitraire $[a,b]$, on peut utiliser la formule suivante :

$$x' = a + \frac{(x-min(x))(b-a)}{max(x)-min(x)}$$

> **À noter** :  Il est recommandé de supprimer les valeurs aberrantes dans les données avant d'utiliser cette technique. En effet, l'écart $max(x) - min(x)$ sera défini par les valeurs extrêmes.

## Standardisation (*Normalisation Z-score*)
La standardisation donne à une variable une moyenne nulle et un écart-type de 1, similaire à une [[Loi normale]] centrée réduite. 

Elle est utilisée notamment pour calculer les statistiques de [[test statistique\|test]] comme celle de [[test de student\|student]].

On la calcule en retranchant à la variable sa moyenne et en divisant le résultat par l'[[écart-type]] :

$$x_{stand} = \frac{x-\mu}{\sigma}$$

ou $x$ est la variable de départ, $\mu$ est sa moyenne et $\sigma$ son écart-type estimé.

Cette transformation assigne à la variable une majorité de valeurs comprises entre $[-1, 1]$, le résultat étant moins dépendant des valeurs aberrantes, contrairement à la normalisation min-max.


## Transformation Box-Cox

La transformation Box-Cox fait partie des transformations appelées ["*power transform*"](http://onlinestatbook.com/2/transformations/tukey.html). Elles utilisent la puissance ou le log pour transformer rendre linéaire une courbe exponentielle. 

![log-transform](/assets/img/log_transform.png#center)
<div align="center">
	<p> <i>
	Population américaine. La courbe de droite est exprimée sur une échelle logarithmique. <a href="http://onlinestatbook.com/2/transformations/tukey.html"> Source </a> </i>
</p>
</div>

L'analyse des données est ainsi facilitée, le [[coefficient de corrélation]] se rapprochant de 1 et les variations suivant une loi normale :

![](/assets/img/transform_corr.png#center)

<div align="center">
	<p>
	Coefficient de corrélation en fonction de lambda.	
	<a href="http://onlinestatbook.com/2/transformations/tukey.html"> Source </a>
</p>
</div>

La transformation Box-Plot est une version particulière de *power transform* adaptée à la normalisation des données :

$$
x_{\lambda}^{\prime}=\frac{x^{\lambda}-1}{\lambda}
$$

Une observation minutieuse nous indique que lorsque $\lambda$ est égal à $0$, la transformation Box-Cox aboutit à une indétermination : elle est égale à 0/0.

Quelle est la limite de la transformation lorsque  $\lambda \rightarrow 0$ ?

On a :

$$
x_{\lambda}^{\prime}=\frac{e^{\lambda \log (x)}-1}{\lambda}
$$

$\lambda$ tendant vers $0$, on peut effectuer [[Développement des fonctions usuelles\|approximer]] la valeur $e^{\lambda \log (x)}$ par le développement de l'exponentielle :

$$
\frac{e^{\lambda \log (x)}-1}{\lambda} \approx \frac{\left(1+\lambda \log (x)+\frac{1}{2} \lambda^{2} \log (x)^{2}+\cdots\right)-1}{\lambda}
$$

Qui tend vers $log(x)$ lorsque $\lambda \rightarrow 0$.

Pour plus de simplicité, on donne donc à la transformation les valeurs suivantes en fonction de $\lambda$ :

$$
x_{\lambda}^{\prime}=\left\{\begin{array}{ll}
\log \left(x\right) & \text { si } \lambda=0 \\
\left(x^{\lambda}-1\right) / \lambda & \text { sinon }
\end{array}\right.
$$

On peut alors faire varier $\lambda$ afin de trouver la valeur pour laquelle la distribution s'approche le plus d'une loi normale au moyen d'un [[test de normalité]]. Cette étape est réalisée de manière algorithmique par l'ordinateur (utiliser [sklearn.preprocessing.power_transform](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.power_transform.html?highlight=box%20cox)).


## Normalisation au vecteur unitaire[^1]

Si ce qui nous intéresse se rapporte à la *direction* du point $p$ et non à sa distance à l'origine, on peut utiliser la normalisation à l'unité :

$\frac{p}{L_2(p)}$ où $L_2(p) = \sqrt{\sum\limits_{k=0}^n p^2_i}$

> **A noter** : Cette dénomination correspond à la [[Norme d'un vecteur\|norme d'un vecteur]] et non à la loi normale.

Voir la note sur la [[Norme d'un vecteur\|norme d'un vecteur]] pour une explication plus détaillée sur le sujet.

Cette normalisation est particulièrement utile dans le domaine du [[NLP]], et de la [[classification]] en général.

[^1]: Quelle est l'expression française pour 'unit-vector normalization' ?

## Mise à l'échelle de données dans python

Avec python, on pourra utiliser le module `sklearn.preprocessing`, et notamment sa fonction [`scaled(X)`](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.scale.html). Celle-ci centre et standardise les données pour $\sigma = 1$ :

```python
# Import scale
from sklearn.preprocessing import scale

# Scale the features: X_scaled
X_scaled = scale(X)

# Print the mean and standard deviation of the scaled features
print("Mean of Scaled Features: {}".format(np.std(X)))
print("Standard Deviation of Scaled Features: {}".format(np.std(X_scaled)))

> Standard Deviation of Unscaled Features: 2.7314972981668206e-15 
> Standard Deviation of Scaled Features: 0.9999999999999999
````

`scaled()` existe aussi comme transformeur, ce qui lui permet d'être utilisé dans un pipeline :

```python
# Import the necessary modules
from sklearn.preprocessing import StandardScaler
from sklearn.pipeline import Pipeline

# Setup the pipeline steps: steps
steps = [('scaler', StandardScaler()),
        ('knn', KNeighborsClassifier())]

# Create the pipeline: pipeline
pipeline = Pipeline(steps)

# Create train and test sets
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.3, random_state=42)

# Fit the pipeline to the training set: knn_scaled
knn_scaled = pipeline.fit(X_train, y_train)

# Instantiate and fit a k-NN classifier to the unscaled data
knn_unscaled = KNeighborsClassifier().fit(X_train, y_train)

# Compute and print metrics
print('Accuracy with Scaling: {}'.format(knn_scaled.score(X_test, y_test)))
print('Accuracy without Scaling: {}'.format(knn_unscaled.score(X_test, y_test)))

> Accuracy with Scaling: 0.7700680272108843 
> Accuracy without Scaling: 0.6979591836734694
````



## Reférences :

- [[Feature Scaling::https://en.wikipedia.org/wiki/Feature_scaling]] (Wikipedia)
- *Chapitre 10, p.303,[[../refs#The Data Science Design Manual Steven S Skienna\|The Data Science Manual, Skienna]]*
- *52., p38, [[Les Datasciences en 100 Questions, Y. Benzaki]]*
- [Vector Math for 3D Computer Graphic](https://chortle.ccsu.edu/VectorLessons/vectorIndex.html#07) (chortle.ccsu.edu)
- *Online Statistics Education* - disponible sur [onlinestatbook.com](http://onlinestatbook.com/2/transformations/box-cox.html)