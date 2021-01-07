---
title: Régression logistique
tags : Apprentissage-supervisé
etat : été
---

La régression logistique estime la probabilité qu'une variable binaire soit égale à 1 sachant $X$ : $Pr(Y = 1 \vert X = x)$. 

En y ajoutant un seuil de décision, on en fait un [[Classificateur linéaire\|classificateur linéaire]] permettant de séparer des classes entre elles. Pour des solutions à plus de deux classes, on lui préfère en général l'[[Analyse discriminante\|analyse discriminante]][^1], bien que ce soit possible en pratique, comme on va le voir.

[^1]: Voir https://www.stat.cmu.edu/~cshalizi/uADA/12/lectures/ch12.pdf, partie 12.2.2, par exemple.

Grand classique de l'économétrie, longtemps envié, rarement dépassé !

## Explication du modèle

La [[Régression linéaire\|régression linéaire]] classique ne permet pas d'estimer une probabilité qu'une observation appartienne à une classe donnée, ses résultats n'étant pas bornés entre 0 et 1. En pratique, cela n'empèche pas de réaliser une classification, mais les résultats seront instables lors du test du modèle.

![](/assets/img/linearvslogistic.png#center)

<div align="center">
	<p>
  Estimation (en bleu) de la probabilité de défaut en fonction du solde du compte en banque. A gauche, régression linéaire, à droite, régression logistique. Source : ISLR (Tibshirani et al.).
	</p>
</div>

On souhaite donc trouver quelle est la classe la plus probable pour chaque observation. Toute observation appartenant à une classe[^2], on attribuera à chaque observation la classe ayant la [cote](http://www.granddictionnaire.com/ficheOqlf.aspx?Id_Fiche=26529673) la plus élevée, c'est à dire la classe la plus probable comparée aux autres.

[^2]: Même les sans-classes, qui forment de facto une classe ! Imparable.

Dans le cas simple où l'on a deux classes possibles, cela reviendra à calculer le ratio des deux probabilités :

$$
cote = \frac{\operatorname{P}(G=1 \mid X=x)}{\operatorname{P}(G=2 \mid X=x)}
$$

Ce qui revient à :

$$
cote = \frac{\operatorname{P}(G=1 \mid X=x)}{1-\operatorname{Pr}(G=1 \mid X=x)}
$$

Comment estimer la probabilité $\operatorname{P}(G=1 \mid X=x)$ de telle manière qu'elle soit comprise entre 0 et 1 ? On exploite la fonction logistique, qui possède ces propriétés :

$$
\operatorname{P}(G=1 \mid X=x)=\frac{\exp \left(\beta_{0}+\beta^{T} x\right)}{1+\exp \left(\beta_{0}+\beta^{T} x\right)}
$$

Sachant que :

$$
\operatorname{P}(G=2 \mid X=x)=\frac{1}{1+\exp \left(\beta_{0}+\beta^{T} x\right)}
$$

Ses paramètres $\theta = \beta_{0}+\beta^{T}x$ sont isolables en réalisant la transformation *logit*,  $\log [p /(1-p)]$ suivante :

$$
\log \frac{\operatorname{Pr}(G=1 \mid X=x)}{\operatorname{Pr}(G=2 \mid X=x)}=\beta_{0}+\beta^{T} x
$$

Cette propriété est très commode, car elle se rapproche de la régression linéaire et donne la contribution au modèle de chaque variable, ce qui permet une bonne interprétabilité.

Le cas à K > 2 classe est une généralisation de ce modèle :

$$
\log \frac{\operatorname{Pr}(G=K-1 \mid X=x)}{\operatorname{Pr}(G=K \mid X=x)}=\beta_{(K-1) 0}+\beta_{K-1}^{T} x
$$

On calcule alors la cote de chaque classe par rapport à la dernière classe. En pratique, le choix de dénominateur est libre et n'influe pas sur le résultat. La probabilité postérieure associée à chaque classe est exprimée ainsi :

$$
\operatorname{Pr}(G=k \mid X=x)=p_{k}(x ; \theta)
$$

Où $\theta$ correspond aux paramètres du modèle $\theta=\left\{\beta_{10}, \beta_{1}^{T}, \ldots, \beta_{(K-1) 0}, \beta_{K-1}^{T}\right\}$, comme expliqué plus haut.


## Estimation du modèle

On utilise l'estimateur du [[maximum de vraisemblance]] pour chercher la valeur $\theta$. La log-vraisemblance s'exprime ainsi :

$$
\ell(\theta)=\sum_{i=1}^{N} \log p_{g_{i}}\left(x_{i} ; \theta\right)
$$

Dans le cas à deux classes (plus simple), où la variable objectif $y_i$ est binaire (0 ou 1) et $p_{1}(x ; \theta)=p(x ; \theta)$, $p_{2}(x ; \theta)=1-p(x ; \theta)$, on a :

$$
\begin{aligned}
\ell\left(\beta_{0}, \beta\right) &=\sum_{i=1}^{n} y_{i} \log p\left(x_{i}\right)+\left(1-y_{i}\right) \log(1-p\left(x_{i}\right)) \\
&=\sum_{i=1}^{n} \log(1-p\left(x_{i}\right))+\sum_{i=1}^{n} y_{i} \log \frac{p\left(x_{i}\right)}{1-p\left(x_{i}\right)} \\
&=\sum_{i=1}^{n} \log(1-p\left(x_{i}\right))+\sum_{i=1}^{n} y_{i}\left(\beta_{0}+x_{i} \cdot \beta\right) \\
&=\sum_{i=1}^{n}-\log(1+e^{\beta_{0}+x_{i} \cdot \beta})+\sum_{i=1}^{n} y_{i}\left(\beta_{0}+x_{i} \cdot \beta\right)
\end{aligned}
$$

La maximisation de la log-vraisemblance se fait en dérivant son expression en fonction de ses paramètres.  On cherche ensuite pour chaque équation dite de *score* le point où elles atteignent 0 :

$$
\begin{aligned}
\frac{\partial \ell}{\partial \beta_{j}} &=-\sum_{i=1}^{n} \frac{1}{1+e^{\beta_{0}+x_{i} \cdot \beta}} e^{\beta_{0}+x_{i} \cdot \beta} x_{i j}+\sum_{i=1}^{n} y_{i} x_{i j} \\
&=\sum_{i=1}^{n}\left(y_{i}-p\left(x_{i} ; \beta_{0}, \beta\right)\right) x_{i j}
\end{aligned}
$$

> **A noter** : Le coefficient $\beta_{k0}$ est égal à la répartition à priori de $y_i$.

L'approche de la valeur des paramètres du modèle se fait au moyen de l'algorithme de Newton-Raphson, que l'on peut retrouver p120-121 d'[ESLII](https://web.stanford.edu/~hastie/Papers/ESLII.pdf).

> **A noter :** On peut utiliser les mêmes techniques de [[Régularisation\|régularisation]] que pour la [[Régression linéaire\|régression linéaire]].

## Classification

Afin de maximiser la précision, choisira $Y = 1$ lorsque le score $p(x)$ associé à l'observation est égal à 0,5 et $Y = 0$ lorsque $p(x) < 0,5$, ce qui correspond au point où $P(y=1 \mid x)=P(y=0 \mid x)=\frac{1}{2}$.

Dans le cadre de la fonction logistique $p(X)=\frac{e^{\beta_{0}+\beta_{1} X}}{1+e^{\beta_{0}+\beta_{1} X}}$, cela revient à choisir $Y=1$ lorsque $\beta_{0}+\beta_{1} X$ est supérieur à 0 et $Y=0$ lorsque $\beta_{0}+\beta_{1} X$ est égal à 1. **On crée de fait un classificateur linéaire.**

> A noter : On peut choisir une limite de décision (*decision boundary*) différente en fonction de l'évènement à détecter. Pour réduire les faux négatifs, on choisira une limite inférieure à 0,5, au prix d'un nombre accru de faux positifs. Un bon moyen de choisir est de consulter la [[Evaluer les performances d'un classificateur\|courbe ROC]] du modèle. Voir aussi cette [bonne réponse sur SO](https://datascience.stackexchange.com/questions/49573/how-to-plot-logistic-regression-decision-boundary) sur l'aspect mathématique et graphique de cette question.

## Interprétation

Si le modèle s'inspire de la régression linéaire, il n'est pas aussi explicatif que celle-ci. 

En effet, comme on le constate, une augmentation de $x_i$ de une unité ne correspond pas à une variation égale à $\beta_{1i}$ de la probabilité $p(X=x_i)$ , mais à la côte de celle-ci.

Néanmoins, on peut toujours comparer les effets $\beta_i$ entre eux (à condition que les variables soient [[Mise à l'échelle des données - standardisation et normalisation\|normalisées]]) et le signe de $\beta_i$ nous laisse une indication pertinente à propos de l'effet de la variable $X_i$ sur $p(X)$.

## Sélection du modèle

L'interprétation des coefficients peut être complexifiée par la présence de variables correlées entre elles qui peuvent remplacer les variables explicatives ayant une relation de causalité et non seulement de corrélation.

Il semble y avoir des [divergences](https://stats.stackexchange.com/questions/18638/model-selection-logistic-regression) sur la meilleure manière de choisir ses variables.

Plusieurs stratégies sont possibles, certaines algorithmiques, d'autres plus analytiques. Le choix dépendra du temps, des ressources à disposition, mais aussi de la culture de practicien (*machine learning* versus économétrie) et du sujet étudié.

- Supprimer la variable la moins significative du modèle puis rééstimer ses paramètres, jusqu'à ce que toutes les variables soient significatives.

- Estimer le modèle avec chaque variable en moins (soit $k-1$ variable à chaque fois), et choisir le modèle dont le $R^2$ est le plus élevé, puis répèter avec $k-2$ variables et ainsi de suite.

- Tester les coefficients avec les tests de **Rao** (inclusion d'un coefficient) et de **Wald** (exclusion d'un coefficient). [Plus de détails dans ce lien.](http://www.stat.umn.edu/geyer/8112/notes/tests.pdf)

- Comparer le score [[AIC]] des modèles un à un. $A I C=-2 \times \ln (\text { vraisemblance })+2 k$ où $k$ est égal au nombre de variables du modèle. On choisit le score AIC le plus faible.

Le package Python "Statsmodel" est très complet sur le sujet. [Un bon guide pour avancer rapidement.](http://eric.univ-lyon2.fr/~ricco/tanagra/fichiers/fr_Tanagra_Python_Regression_Logistique.pdf)

## Utilisation simple avec scikit-learn

On utilise [sklearn.linear_model.LogisticRegression](https://scikit-learn.org/stable/modules/generated/sklearn.linear_model.LogisticRegression.html). Voir le guide référencé ci-dessus, qui comprend le fonctionnement de sklearn en plus de détails.

 ```python
from sklearn import datasets
from sklearn.model_selection import train_test_split

from sklearn.linear_model import LogisticRegression

digits = datasets.load_digits()
X_train, X_test, y_train, y_test = train_test_split(digits.data, digits.target)

lr = LogisticRegression()
lr.fit(X_train,y_train)

# Précision d'entrainement :
lr.score(X_train,y_train))

# Précision de test :
lr.score(X_test, Y_test))

# Probabilités estimées pour la première observation :
lr.predict_proba(X_test[0])

# Régularisation

````

