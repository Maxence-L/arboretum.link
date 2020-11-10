---
title: Calculer des distances terrestres
tags: Cartographie
etat : hiver
---
L'espace entre les points sur le globe étant courbé, il est nécessaire d'utiliser quelques règles de géométrie dans l'espace pour calculer les distances.

Un bon article sur le sujet : https://www.movable-type.co.uk/scripts/latlong.html

Une mise en pratique python :

```python
from math import cos, sin, asin

def great_circle_distance(coord1, coord2, radius=6371):

    if np.array_equal(coord1, coord2):
        return 0.0

    coord1, coord2 = np.radians(coord1), np.radians(coord2)
    delta_x, delta_y = coord2 - coord1
    haversin = sin(delta_x / 2) ** 2 + np.product([cos(coord1[0]),
                                                   cos(coord2[0]),
                                                   sin(delta_y / 2) ** 2])
    return  2 * radius * asin(haversin ** 0.5)
````

La fonction retourne une distance en km entre deux positions (lat, long). Pour avoir la distance en miles, on peut remplacer `radius` par 3956. (rayon de la terre en miles).


