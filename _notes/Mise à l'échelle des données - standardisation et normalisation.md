---
title: Mise à l'échelle des données - standardisation et normalisation
tags: Maths
etat : Printemps
---

La mise à l'échelle consiste à altérer les données afin de réduire leur variance ou leur valeur absolue. Elle est utile lorsque les variables d'un jeu de données ont des ordres de grandeur différents qui peuvent fausser les estimations des algorithmes d'apprentissage automatique.

Par exemple, les classificateurs utilisant la distance euclidienne seront affectés si un des composant est distribué sur un espace beaucoup plus grand que les autres. De même, il sera difficile de comparer les coefficients d'une régression linéaire si les variables sur lesquels ils s'appliquent n'ont pas d'échelle comparable. Enfin, le [[gradient descent]] est plus rapide si les données partagent la même échelle. (*rescaled*).

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

## Normalisation au vecteur unitaire[^1]

Si ce qui nous intéresse se rapporte à la *direction* du point $p$ et non à sa distance à l'origine, on peut utiliser la normalisation à l'unité :

$$ \frac{p}{L_2(p)}$$

où

$$L_2(p) = \sqrt{\sum\limits_{k=0}^n p^2_i}$$

Voir la note sur la [[Norme d'un vecteur]] pour une explication plus détaillée sur le sujet.

Cette normalisation est particulièrement utile dans le domaine du [[NLP]], et de la [[classification]] en général.

[^1]: Quelle est l'expression française pour 'unit-vector normalization' ?

## Reférences :
- [[Feature Scaling::https://en.wikipedia.org/wiki/Feature_scaling]] (Wikipedia)
- *Chapitre 10, p.303, [[The Data Science Manual, Skienna]]*
- *52., p38, [[Les Datasciences en 100 Questions, Y. Benzaki]]*
- [Vector Math for 3D Computer Graphic](https://chortle.ccsu.edu/VectorLessons/vectorIndex.html#07) (chortle.ccsu.edu)