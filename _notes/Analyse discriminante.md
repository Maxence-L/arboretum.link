---
title: Analyse discriminante
tags : Apprentissage-supervisé
etat : printemps
---

L'analyse discriminante est une méthode de classification proche de la [[Régression logistique\|régression logistique]]. 

Elle est appelée en anglais *linear discriminant analysis*.

## Principe

Soit une variable réponse $Y$ possédant $k$ classes possibles. L'analyse discriminante linéaire consiste à modéliser la distribution de X pour chaque classe de la variable réponse et d'utiliser le [[Théorème-de-Bayes]] pour en déduire $Pr(Y=k \vert X=x)$

On peut donc utiliser le théorème de Bayes pour estimer la valeur des paramètres $P(X)$, $P(Y)$, $P(X \vert Y)$ et donc déterminer la vraisemblance $Pr(Y=k \vert X=x)$ pour chaque Y. 

Selon la valeur que prendra $x$, on attribuera à l'observation la classe $k$ de $Y$ correspondant à la valeur maximum de $Pr(Y=k \vert X=x)$.

## Théorie

### Analyse discriminante linéaire (ADL) avec p = 1

Soit :

- La probabilité $\pi_k$ qu'une observation aléatoire de $Y$ fasse partie de la classe $k$.

- La *fonction de vraisemblance* $f_k(X) = P(X=x \vert Y=k)$  de $X$ pour une observation de $$Y$$ ayant la classe $k$. 

> En d'autres termes, $f_k(x)$ sera élevée s'il existe une forte probabilité qu'une observation de la $k^{eme}$ classe soit égale à $x$. Inversement, si observer $X = x$ est peu probable pour une observation de classe $k$, alors $f_k(x)$ sera faible.


On notera $p_k(X) =P(Y=k \vert X)$ la probabilité que la variable $Y$ soit de la classe $k$ sachant $X$.

Selon le [[Théorème de Bayes]], on a :

$$p_k(X) = \dfrac{\pi_{k}f_{k}(x)}{ \sum_{t=1}^k \pi_{t} f_{t}(x)}$$

Si l'on suppose que $f_k(x)$ suit une [[Loi normale]] de loi ($\mu_k, \sigma^2_k$) où $\sigma^2 = \sigma^2_k$ pour tout $k$ ([[Homoscédasticité et Hétéroscédasticité\|hypothèse d'homoscédasticité]]), alors on peut estimer $p_k(X)$ pour chaque $k$ et assigner à $Y$ la classe $k$ pour laquelle $p_k(X)$ est la plus forte. 

En d'autres termes, on assigne à chaque observation la classe la plus probable.

Après manipulations[^1], l'ADL assigne la classe $k$ à l'observation $X=x$ pour laquelle $\hat{\delta}_k(x)$ est le plus grand, où :

[^1]: $ln(p_k(X))$ - Voir [ceci](https://web.archive.org/web/20190214175740/http://www.math.u-bordeaux.fr/~mchave100p/wordpress/wp-content/uploads/2013/10/Analyse_discrim.pdf) et ["An introduction to statistical learning](https://faculty.marshall.usc.edu/gareth-james/ISL/ISLR%20Seventh%20Printing.pdf) (Hastie et al.)", p. 139 pour la démonstration.]

$$\hat{\delta}_k(x) = x \cdot \frac{\hat{\mu}_k}{\hat{\sigma}^2} - \frac{\hat{\mu}^2_k}{2\hat{\sigma}^2} + log(\hat{\pi}_k)$$

Le classificateur bayésien ainsi obtenu sépare les observations sur une ligne de partage, ce qui explique le nom d'*analyse discriminante linéaire*.

Il est recommandé de vérifier l'hypothèse de normalité et d'homoscédasticité avant de procéder à une ADL. On peut rectifier la normalité avec une [[Mise à l'échelle des données - standardisation et normalisation\|normalisation des données]] et l'[[Homoscédasticité et Hétéroscédasticité\|hétéroscédasticité]] avec l'utilisation d'une analyse discriminante quadratique, examinée plus bas.

Si $k=2$ et $\pi_1 = \pi_2$, alors le classificateur est à équidistance des deux moyennes :

 ![](/assets/img/ld-discrimination1.png#center)

*À gauche, on peut voir deux densités normales, la ligne en pointillés représentant la ligne de démarcation du classificateur. À droite, des observations générées à partir de ces fonctions de densité. La ligne pleine représente le classificateur bayésien estimé.*

### Analyse discriminante quadratique (ADQ)

(à compléter)

## Quand l'utiliser ?

- Lorsque les classes sont séparées distinctement, l'estimation de paramètres peut être instable dans le cas la [[Régression logistique\|régression logistique]]. L'ADL ne rencontrera pas ce type de problème.

- Si n est faible est que la distribution des prédicteurs X suit approximativement une loi normale, l'ADL est plus stable que la [[Régression logistique\|régression logistique]].

- Lorsque nous la variable réponse prend plus de deux classes, l'ADL est plus appropriée.

## Mise en pratique avec sklearn

### Installation

On importe classes [LinearDiscriminantAnalysis](https://scikit-learn.org/stable/modules/generated/sklearn.discriminant_analysis.LinearDiscriminantAnalysis.html) et [QuadraticDiscriminantAnalysis](https://scikit-learn.org/stable/modules/generated/sklearn.discriminant_analysis.QuadraticDiscriminantAnalysis.html#sklearn.discriminant_analysis.QuadraticDiscriminantAnalysis) du module [sklearn.discriminant_analysis](https://scikit-learn.org/stable/modules/classes.html#module-sklearn.discriminant_analysis).

```python
from sklearn.discriminant_analysis import LinearDiscriminantAnalysis
from sklearn.discriminant_analysis import QuadraticDiscriminantAnalysis
from sklearn.metrics import confusion_matrix, classification_report, precision_score
````

Pour cet exemple, on utilise les [données](https://github.com/JWarmenhoven/ISLR-python/raw/master/Notebooks/Data/Smarket.csv) relatives au mouvements à la hausse ou à la baisse du marché boursier, en fonction des variations des jours précédents.

```python
df = pd.read_csv('Smarket.csv', usecols=range(1,10), index_col=0, parse_dates=True)

X_train = df[:'2004'][ ['Lag1','Lag2'] ]
y_train = df[:'2004']['Direction']

X_test = df['2005':][ ['Lag1','Lag2'] ]
y_test = df['2005':]['Direction']

lda = LinearDiscriminantAnalysis()
pred = lda.fit(X_train, y_train).predict(X_test)
````


### Utilisation pour l'analyse discriminante linéaire

On instancie un objet `LinearDiscriminantAnalysis()` puis on peut utiliser sa méthode `.fit(X_train, y_train).predict(X_test))` :

```python
lda = LinearDiscriminantAnalysis()
pred = lda.fit(X_train, y_train).predict(X_test)
````

Une fois le modèle entraîné, les **probabilités marginales** relatives à chaque classe sont disponibles dans l'attribut `lda.priors_`.

L'attribut `lda.means_` contient un array matriciel représentant la moyenne de chaque variables explicatives pour chaque classe (sélection : `lda.means_[classe][variable]`)

L'attribut `lda.coef_` contient les coefficients des discriminants linéaires. A quoi cela correspond t-il ?

Le discriminant linéaire en x se calcule ainsi :

$$
\hat{\delta}_{2}(\vec{x})-\hat{\delta}_{1}(\vec{x})=\vec{x}^{T} \hat{\Sigma}^{-1}\left(\overrightarrow{\hat{\mu}}_{2}-\overrightarrow{\hat{\mu}}_{1}\right)-\frac{1}{2}\left(\overrightarrow{\hat{\mu}}_{2}+\overrightarrow{\hat{\mu}}_{1}\right)^{T} \hat{\Sigma}^{-1}\left(\overrightarrow{\hat{\mu}}_{2}-\overrightarrow{\hat{\mu}}_{1}\right)+\log \left(\frac{\pi_{2}}{\pi_{1}}\right)
$$

où $\vec{x}=(\operatorname{Lag} 1, \operatorname{Lag} 2)^{T}$. 

Les coefficients correspondent au terme $\hat{\Sigma}^{-1}\left(\overrightarrow{\hat{\mu}}_{2}-\overrightarrow{\hat{\mu}}_{1}\right)$, ce qui correspond à la pente de la courbe suivante[^2] :

[^2]: https://stats.stackexchange.com/questions/87479/what-are-coefficients-of-linear-discriminants-in-lda


![](/assets/img/score_adl.png#center)


Le score correspond ici à la multiplication entre $X$ et les coefficients :
`np.array(df.iloc[0,0:2])@lda.coef_.T`

On peut regarder la [[matrice de confusion]] :

```python
confusion_matrix(y_test, pred).T

> array([ [ 35,  35],
          [ 76, 106] ])
````

On peut regarder le [[rapport de classification]]:

```python
print(classification_report(y_test, pred, digits=3))
````

|x|precision|recall|f1-score|support|
|-|-|-|-|-|
|Down|0.500|0.315|0.387|111|
|Up|0.582|0.752|0.656|141|
|avg / total|0.546|0.560|0.538|252|


La méthode `lda.predict_proba(X_test)` permet d'obtenir la probabilité estimée associée à chaque classe pour le point de données :

```python
pred_p = lda.predict_proba(X_test)
pred_p[0:5]

> array([ 
[0.49017925, 0.50982075],
[0.4792185 , 0.5207815 ],
[0.46681848, 0.53318152],
[0.47400107, 0.52599893],
[0.49278766, 0.50721234] ])
````
Cette méthode est particulièrement utile car elle nous permet de choisir le seuil à partir duquel nous catégorisons chaque observation[^3].

[^3]: Voir p. 143 d'ISLR.

Si on applique un seuil à 50%, on obtient :

```python
np.unique(pred_p[:,1]>0.5, return_counts=True)
> (array([False,  True]), array([ 70, 182]))
# 70 observations sont classifiées dans la catégorie 1
````

À 50.5% :

```python
np.unique(pred_p[:,1]>0.5, return_counts=True)
> (array([False,  True]), array([ 107, 145]))
# 107 observations sont classifiées dans la catégorie 1
````

### Utilisation pour l'analyse discriminante  quadratique

On utilise la classe `QuadraticDiscriminantAnalysis()` et l'on entraine le modèle :

```python
qda = QuadraticDiscriminantAnalysis()
pred = qda.fit(X_train, y_train).predict(X_test)
````

La syntaxe est identique à l'adl :

```python
qda.priors_

> array([ 0.49198397,  0.50801603])

qda.means_

> array([
[ 0.04279022,  0.03389409],
[-0.03954635, -0.03132544]
])
````

L'objet `qda` ne contient pas de coefficient : le discriminant est quadratique.

```python
confusion_matrix(y_test, pred).T

> array([
[30,  20],
[81, 121]
])

print(classification_report(y_test, pred, digits=3))
````

|-|precision|recall|f1-score|support
|-|-|-|-|-
|Down|0.600|0.275|0.373|111
|Up|0.599|0.858|0.706|141
|avg / total|0.599|0.599|0.559|252

On peut noter que l'analyse discriminante quadratique permet ici d'obtenir une précision de 60%, ce qui est particulièrement élevé dans le cas des marchés financiers. Par ailleurs, l'ADQ est plus efficace que l'ADL.

