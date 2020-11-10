---
title: Similarité
tags: Maths
etat: hiver
---

Le but des méthodes d'analyse de la similarité est de créer une mesure unique permettant de comparer des vecteurs entre eux. 

Ces techniques trouvent leur utilité en complément des algorithmes de [[clustering]], pour faciliter leur fonctionnement et pour évaluer leurs résultats.

## Similarité et Distance de Jaccard

Soit deux sets de vecteurs. La *Similarité de Jaccard* (*Jaccard Similarity* - $J(s_1,s2)$) des sets $s_1$ et $s_2$ est définie selon le ratio entre l'intersection et l'union des éléments de chaque vecteur compris dans le set :

$$
J(A, B)=\frac{|A \cap B|}{|A \cup B|}
$$

La Similarité de Jaccard est par définition comprise entre 0 et 1 et est particulièrement utile pour les ensembles binaires (0,1).

Elle devient une réelle [[Norme d'un vecteur\|mesure de distance]][^1] $d(s_1,s_2)$, appelée la *Distance de Jaccard* avec la formule suivante qui vérifie la condition d'identité[^1] :

$$
d(s_1,s_2) = 1 - J(s_1,s_2)
$$

[^1]: *Identité* : $d(x,y) = 0$ si et seulement si $x=y$.

Cette mesure est utile pour comparer la différence brut de lexique entre deux texte, par exemple. 

Elle est aussi utile pour comparer le résultat de deux algorithmes de clustering non-supervisé : plus il sera similaire, plus $d(s_1,s_2)$ sera faible (beaucoup d'éléments font partie des mêmes groupes de classification)

### Application

La distance de Jaccard peut être calculée avec [sklearn.neighbors.DistanceMetrics](https://scikit-learn.org/stable/modules/generated/sklearn.neighbors.DistanceMetric.html).

```python
from sklearn.neighbors import DistanceMetric
dist = DistanceMetric.get_metric('jaccard')

X = [ 	[1, 0, 0],
    	[1, 1, 1] ]

dist.pairwise(X)

> array([[0.        , 0.66666667],
       [0.66666667, 0.        ]])
````

