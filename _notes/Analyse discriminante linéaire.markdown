---
title: Analyse discriminante linéaire
tags: Apprentissage-non-supervisé
---

L'analyse discriminante linéaire (ADL) est une méthode de classification proche de la [[régression logistique::Régression logistique]]. 

Elle est appelée en anglais *linear discriminant analysis*.

## Principe
Soit une variable réponse $$Y$$ possédant $$k$$ classes possibles. L'analyse discriminante linéaire consiste à modéliser la distribution de X pour chaque classe de la variable réponse et d'utiliser le [[théorème de Bayes]] pour en déduire $$Pr(Y=k \vert X=x)$$

On peut donc utiliser le théorème de Bayes pour estimer la valeur des paramètres $$P(X)$$, $$P(Y)$$, $$ P(X \vert Y) $$ et donc déterminer la vraisemblance $$Pr(Y=k \vert X=x)$$ pour chaque Y. 

Selon la valeur que prendra $$x$$, on attribuera à l'observation la classe $$k$$ de $$Y$$ correspondant à la valeur maximum de $$Pr(Y=k \vert X=x)$$.

## Quand l'utiliser ?

- Lorsque les classes sont séparées distinctement, l'estimation de paramètres peut être instable dans le cas la [[régression logistique::Régression logistique]]. L'ADL ne rencontrera pas ce type de problème.

- Si n est faible est que la distribution des prédicteurs X suit approximativement une loi normale, l'ADL est plus stable que la [[régression logistique]].

- Lorsque nous la variable réponse prend plus de deux classes, l'ADL est plus appropriée.

## Théorie

### ADL avec p = 1 (une seule variable explicative)

Soit :
- $$\pi_k$$ la probabilité marginale qu'une observation aléatoire de $$Y$$ fasse partie de la classe $$k$$.

- $$f_k(X) = P(X=x \vert Y=k)$$ la *fonction de vraisemblance* de $$X$$ pour une observation de $$Y$$ ayant la classe $$k$$. 
	
	En d'autres termes, $$f_k(x)$$ sera élevée s'il existe une forte probabilité qu'une observation de la $$k^{eme}$$ classe soit égale à $$x$$. Inversement, si observer $$X = x$$ est peu probable pour une observation de classe $$k$$, alors $$f_k(x)$$ sera faible.
	
On notera $$p_k(X) =P(Y=k \vert X)$$

Selon le [[théorème de Bayes]], on a :

$$p_k(X) = \dfrac{\pi_{k}f_{k}(x)}{ \sum_{t=1}^k \pi_{l} f_{l}(x)}$$

Si l'on suppose que $$f_k(x)$$ suit une [[loi normale]] de loi ($$\mu_k, \sigma^2_k$$) où $$\sigma^2 = \sigma^2_k$$ pour tout $$k$$ (hypothèse d'homoscédasticité), alors on peut estimer $$p_k(X)$$ pour chaque $$k$$.

Après manipulations[[Voir [ceci](https://web.archive.org/web/20190214175740/http://www.math.u-bordeaux.fr/~mchave100p/wordpress/wp-content/uploads/2013/10/Analyse_discrim.pdf) et "An introduction to statistical learning (Hastie et al.)", p. 139 pour la démonstration.
::rsn]], l'ADL assigne la classe $$k$$ à l'observation $$X=x$$ pour laquelle 

$$\hat{\delta}_k(x) = x \cdot \frac{\hat{\mu}_k}{\hat{\sigma}^2} - \frac{\hat{\mu}^2_k}{2\hat{\sigma}^2} + log(\hat{\pi}_k)$$

est le plus grand. Le classificateur ainsi obtenu sépare les observations sur une ligne de partage, ce qui explique le nom d'*analyse discriminante linéaire*.

Voit que dans ce cas, la distinction se fait selon une simple rêgle d'équidistance aux deux moyennes.

### ADL avec p > 1

