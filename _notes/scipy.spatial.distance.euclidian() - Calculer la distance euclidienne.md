---
title : scipy.spatial.distance.euclidian()
tags : scipy
etat : hiver
---

Permet de calculer la [[Norme d'un vecteur|distance euclidienne]] entre deux arrays.

Les deux premiers paramètres comprennent des arrays 1D. Le troisième, optionnel, permet de donner des poids à chaque axe des arrays passés.

```python
from scipy.spatial import distance
distance.euclidean([1, 0, 0], [0, 1, 0])
> 1.4142135623730951
````

