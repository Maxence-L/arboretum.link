---
title: Régression logistique
tags : Apprentissage-supervisé
etat : printemps
---

La régression logistique estime la probabilité qu'une variable binaire soit égale à 1 sachant $X$ : $Pr(Y = 1 \vert X = x)$. 

En y ajoutant un seuil de décision, on en fait un [[Classificateur linéaire\|classificateur linéaire]] permettant de séparer deux classes. Pour des solutions à plus de deux classes, on lui préfère en général l'[[Analyse discriminante\|analyse discriminante]][^1].

[^1]: Bien que ce soit possible, voir https://www.stat.cmu.edu/~cshalizi/uADA/12/lectures/ch12.pdf, partie 12.2.2 par exemple.

Grand classique de l'économétrie, longtemps envié, rarement dépassé !

## Explication du modèle

La [[Régression linéaire\|régression linéaire]] classique ne permet pas d'estimer une probabilité, ses résultats n'étant pas bornés entre 0 et 1. On résout ce problème en utilisant la fonction logistique, dont les résultats sont compris entre 0 et 1 pour tout $X$, ce qui permet de modéliser $p(X)$  :

$$
p(X)=\frac{e^{\beta_{0}+\beta_{1} X}}{1+e^{\beta_{0}+\beta_{1} X}}
$$

![](/assets/img/linearvslogistic.png#center)

<div align="center">
	<p>
  Estimation (en bleu) de la probabilité de défaut en fonction du solde du compte en banque. A gauche, régression linéaire, à droite, régression logistique. Source : ISLR (Tibshirani et al.).
	</p>
</div>

*Méthode d'estimation par le maximum de vraisemblance : à compléter.

> **A noter :** On peut utiliser les mêmes techniques de [[Régularisation\|régularisation]] que pour la [[Régression linéaire\|régression linéaire]].

### Classification

Afin de maximiser la précision, choisira $Y = 1$ lorsque le score $p(x)$ associé à l'observation est égal à 0,5 et $Y = 0$ lorsque $p(x) < 0,5$, ce qui correspond au point où $P(y=1 \mid x)=P(y=0 \mid x)=\frac{1}{2}$.

Dans le cadre de la fonction logistique $p(X)=\frac{e^{\beta_{0}+\beta_{1} X}}{1+e^{\beta_{0}+\beta_{1} X}}$, cela revient à choisir $Y=1$ lorsque $\beta_{0}+\beta_{1} X$ est supérieur à 0 et $Y=0$ lorsque $\beta_{0}+\beta_{1} X$ est égal à 1. **On crée de fait un classificateur linéaire.**

> A noter : On peut toutefois choisir une limite de décision (*decision boundary*) différente en fonction de l'évènement à détecter. Pour réduire les faux négatifs, on choisira une limite inférieure à 0,5, au prix d'un nombre accru de faux positifs. Un bon moyen de choisir est de consulter la [[Evaluer les performances d'un classificateur\|courbe ROC]] du modèle. [Une bonne réponse sur SO](https://datascience.stackexchange.com/questions/49573/how-to-plot-logistic-regression-decision-boundary) au sujet de la régression logistique.

### Interprétation

Si le modèle s'inspire de la régression linéaire, il n'est pas aussi explicatif que celle-ci. 

En effet, comme on le constate, une augmentation de $x_i$ de une unité ne correspond pas à une variation de la probabilité $p(X=x_i)$ égale à $\beta_{1i}$. 

Néanmoins, on peut toujours comparer les effets $\beta_i$ entre eux (à condition que les variables soient [[Mise à l'échelle des données - standardisation et normalisation\|normalisées]]) et le signe de $\beta_i$ nous laisse une indication pertinente à propos de l'effet de la variable $X_i$ sur $p(X)$.

## Utilisation avec scikit-learn

On utilise [sklearn.linear_model.LogisticRegression](https://scikit-learn.org/stable/modules/generated/sklearn.linear_model.LogisticRegression.html). :

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

