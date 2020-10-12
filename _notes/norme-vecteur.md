---
title: Norme d'un vecteur
tags: Maths
---
La norme est un élément de la définition d'un vecteur correspondant à sa longueur.

## Définition

La norme d'un vecteur correspond à la longueur d'un vecteur, c'est-à-dire la distance qui sépare les deux points qui définissent le vecteur.

La définition formelle est la suivante[^1] :

[[Si $$\overrightarrow{u} \in I\!R^n$$ alors la **Norme** ou la **Magnitude** de $$\overrightarrow{u}$$ est définie comme la longueur ou la magnitude du vecteur et peut être calculée en utilisant la formule $$\vert\vert\vec{u}\vert\vert=\sqrt{u_{1}^{2}+u_{2}^{2}+\ldots+u_{n}^{2}}$$::wrap]]

[^1]: http://mathonline.wikidot.com/the-norm-of-a-vector

![distance](/assets/img/distance_vecteur.png#center)

> **Trouvons la norme du vecteur $$\overrightarrow{u} = (2,-2,3,-4)$$**.
> 
>Du fait que $$\overrightarrow{u} \in I\!R^4$$ on calcule $$\vert\vert\vec{u}\vert\vert=\sqrt{u_{1}^{2}+u_{2}^{2}+u_{3}^{2}+u_{4}^{2}} = \sqrt{4+4+9+16} = \sqrt{33}$$. La norme du vecteur $$\overrightarrow{u}$$ est donc de 33.

Plus généralement, la distance euclidienne entre deux points $$p$$ et $$q$$ se calcule ainsi :

$$d(p, q)=\sqrt{\sum_{i=1}^{d}\left\vert p_{i}-q_{i}\right\vert^{2}}$$

## Généralisation

### Les mesures de distance
Les mesures de distance sont des éléments distincts des scores de [[similarité]] (comme le coefficient de corrélation par exemple). En particulier, la distance entre deux vecteurs croit à mesure que la similarité leur décroit.

Une mesure de distance est une **métrique** (*metric* en anglais) si elle vérifie les propriétés suivantes :

- *Positivité* : $$d(x,y) \geqslant 0$$ pour tout $$x$$ et $$y$$.
- *Identité* :  $$d(x,y) = 0$$ si et seulement si $$x=y$$.
- *Symétrie* : $$d(x,y) = d(y,x)$$ pour tout $$x$$ et $$y$$.
- *Inégalité triangulaire* : $$d(x,y ) \geqslant d(z,y)$$ pour tout $$x$$, $$y$$, $$z$$.

On voit donc que la [[similarité cosinus]] n'est pas une *métrique* : elle ne valide pas la condition de positivité et d'identité.

La norme euclidienne vu dans la partie précédente est un cas spécifique d'une famille plus générale de mesure de distances (ou norme), $$L_k$$ :

$$d_{k}(p, q)=\sqrt[k]{\sum_{i=1}^{d}\left \vert p_{i}-q_{i}\right\vert^{k}}=\left(\sum_{i=1}^{d}\left\vert p_{i}-q_{i}\right\vert^{k}\right)^{1 / k}$$

La distance euclidienne correspond à $$L_2$$. La forme du cercle correspondant une distance équivalent depuis le point origine change avec $$k$$ :

![vecteur unité](/assets/img/vecteur-unite.png#center)

Par conséquent, le choix d'une métrique de distance peut fortement modifier la valeur de la longueur d'un vecteur ou d'une distance entre deux points. Par exemple, si l'on prend deux points aux coordonnées $$p_1 = (2,0)$$ et $$p_2 = (2, 1.99)$$ à l'origine :

- Pour $$k = 1$$, leur distance est de $$2$$  et $$3.99$$.
- Pour $$k = 2$$, leur distance est de $$2$$ et $$2.82136$$.
- Pour $$k = 1000$$, leur distance est de $$2$$ et $$2.00001$$.
- Pour $$k = \infty$$, leur distance est de $$2$$ et $$2$$.

Comme on le voit, $$L_\infty$$ renvoie la valeur correspondant à la plus grande distance dimensionnelle parmi les coordonnées du vecteur.

## Normalisation d'un vecteur

Il est parfois nécessaire de comparer l'orientation de vecteurs, en neutralisant donc leur longueur. Par exemple, en si l'on veut comparer le sens de deux articles de presse, leur longueur n'est pas importante.
 
On peut alors normaliser un vecteur, c'est-à-dire lui donner une norme égale à un, **en divisant ses éléments par sa magnitude**. Si la norme $$L_2$$ est utilisée, la position possible du vecteur se répartit sur une sphère-unité de centre $$(0,0)$$ et de rayon $$1$$ :

![norme L2](/assets/img/norme-L2.png#center)

Cela permet de comparer les vecteurs sur la base de leur direction, et de calculer leur [[similarité cosinus]], par exemple.

Toutefois, si l'on désire calculer la distance entre deux vecteurs normalisés, on ne peut utiliser la [[similarité cosinus]], qui n'est pas une distance, comme vu précédemment. La [[distance cosinus]] $$1 - \vert cost(p,q)\vert$$ est effectivement une mesure de distance, mais ne satisfait pas la propriété d'inégalité triangulaire.

Il sera préférable[^2] d'exploiter la *distance angulaire* :

$$d(p, q)=1-\frac{\arccos (\cos (p, q))}{\pi}$$

$$arrcos()$$ est ici l'inverse de la fonction $$cos^{-1}()$$ et $$\pi$$ est le plus grand angle, en radians.

## Pratique

Le package [[scipy.spatial.distance]] permet de calculer facilement des distances[^3]

[^3]: https://docs.scipy.org/doc/scipy/reference/spatial.distance.html

La distance euclidienne entre deux arrays se calcule avec la méthode *[[spatial.distance]].euclidian()* : 
```python
from scipy.spatial import distance
distance.euclidean([1, 0, 0], [0, 1, 0])

>1.4142135623730951
```
---



[^2]: The Data Science Design Manual, S. Skienna