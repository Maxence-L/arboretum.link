---
title: Régression logistique
tags : Apprentissage-supervisé
etat : printemps
---

La régression logistique est une forme de régression estimant la probabilité qu'une variable binaire soit égale à 1 sachant $X$ : $Pr(Y = 1 \vert X = x)$. 

En y ajoutant un seuil de décision, on en fait un [[Classificateur linéaire\|classificateur linéaire]] permettant de séparer deux classes. Pour des solutions à plus de deux classes, on lui préfère en général l'[[Analyse discriminante\|analyse discriminante]][^1].

[^1]: Bien que ce soit possible, voir https://www.stat.cmu.edu/~cshalizi/uADA/12/lectures/ch12.pdf, partie 12.2.2 par exemple.

Grand classique de l'économétrie, longtemps envié, rarement dépassé !

## Explication du modèle

La [[Régression linéaire\|régression linéaire]] classique ne permet pas d'estimer une probabilité, ses résultats n'étant pas bornés entre 0 et 1. 

On résout ce problème en utilisant la fonction logistique, dont les résultats sont compris entre $0$ et $1$ pour tout $X$, ce qui permet de modéliser $p(X)$  :

$$
p(X)=\frac{e^{\beta_{0}+\beta_{1} X}}{1+e^{\beta_{0}+\beta_{1} X}}
$$

![](assets/img/linearvslogistic.png#center)

<div align="center">
  Estimation (en bleu) de la probabilité de défaut en fonction du solde du compte en banque. A gauche, régression linéaire, à droite, régression logistique. Source : ISLR (Tibshirani et al.).
</div>

*Méthode d'estimation par le maximum de vraisemblance : à complêter (je viens de déménager et mon livre d'économétrie est encore dans un carton)*

A noter que l'on peut utiliser les mêmes techniques de [[Régularisation\|régularisation]] que pour la [[Régression linéaire\|régression linéaire]].

### Classification

Afin de maximiser la précision, on peut choisir $Y = 1$ lorsque $p(x) \geq 0,5$ et $Y = 0$ lorsque $p(x) < 0,5$, ce qui correspond au point où $P(y=1 \mid x)=P(y=0 \mid x)=\frac{1}{2}$.

Dans le cadre de la fonction logistique $p(X)=\frac{e^{\beta_{0}+\beta_{1} X}}{1+e^{\beta_{0}+\beta_{1} X}}$, cela revient à choisir $Y=1$ lorsque $\beta_{0}+\beta_{1} X$ est supérieur à 0 et $Y=0$ lorsque $\beta_{0}+\beta_{1} X$ est égal à 1. **On crée de fait un classificateur linéaire.**

![linear boundary](/assets/img/linearboundary.png#center)
<div align="center">
  Source : scikit-learn.org
</div>

Il existe certains cas où les faux négatifs (ou les faux positifs) sont problématiques. Par exemple, condamner un innocent (faux positif) ou ne pas détecter une appendicite (faux négatif).

On pourra alors accroitre le poids d'une classe et choisir un classificateur linéaire différent de 0,5. [Une bonne réponse sur SO à ce sujet.](https://datascience.stackexchange.com/questions/49573/how-to-plot-logistic-regression-decision-boundary)

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

# Training accuracy :
lr.score(X_train,y_train))

# Testing accuracy :
lr.score(X_test, Y_test))

# Predicted probabilities for the first observation :
lr.predict_proba(X_test[0])
````

