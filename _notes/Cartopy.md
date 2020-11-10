---
title : Cartopy
tags: Cartographie
etat: été
---
Cartopy est un package permetant de réaliser des projections cartographiques. Il succède à Basemap et s'intègre aux visualisations de Matplotlib.

> **À noter :** Cartopy a tendance à faire planter les notebooks.

On l'importe de la manière suivante[^1] :

```python
!apt-get -qq install python-cartopy python3-cartopy
import cartopy

import matplotlib.pyplot as plt
import cartopy.crs as ccrs                   # import projections
import cartopy.feature as cf                 # import features
````

Les deux objets importés sont :

- `cartopy.crs`, (CRS : *coordinate reference system*) qui permet de transformer les données pour les adapter à des projections cartographiques.* C'est le package que l'on utilisera pour déterminer la projection utilisée.

- `cartopy.feature`, contient les éléments que l'on peut utiliser pour donner du contexte aux cartes, comme la forme des pays, la ligne côtière, le relief, les noms de villes, etc...

## Initialisation

Cartopy s'intégrant à pyplot, on retrouve la même syntaxe. Utiliser l'option `projection =` remplace l'objet 'axes' par un [GeoAxes](https://scitools.org.uk/cartopy/docs/latest/matplotlib/geoaxes.html#cartopy.mpl.geoaxes.GeoAxes) propre à Cartopy. On initialise le système de coordonnées de la manière suivante :

```python
ax = plt.axes(projection = ccrs.Mercator())
````

![](../assets/img/cartopy-vide.png#center)

Des axes sont créés utilisant la projection de Mercator, mais la carte est vide. On ajoute un objet issu de `cartopy.feature` (ici appelé `cf`) : la géographique côtière, avec la fonction `.add_feature()`

```python
ax.add_feature(cf.COASTLINE)
ax.set_title("Title")
plt.show()
````
![](../assets/img/cartopy-fond.png#center)

Il suffit de changer la projection pour modifier le style de carte :

```python
ax = plt.axes(projection = ccrs.LambertConformal())  
ax.add_feature(cf.COASTLINE)                 
ax.set_title("Title")                        
plt.show()
````

![](../assets/img/cartopy-projection.png#center)

[D'autres projections sont possibles](https://scitools.org.uk/cartopy/docs/v0.15/crs/projections.html)

## Méthodes utiles :

On peut définir la zone montrée au moyen de `.set_extent()`, en précisant les 

### Enregistrement de la carte

On peut enregistrer la carte en appelant `plt.savefig('path')` avant `plt.show()`:

```python
plt.savefig('my_map.png')
plt.show()
````

### Ajout d'un fond de carte sous forme d'image :

Cartopy possède par défaut un fond de carte représentant le relief :

```python
ax.stock_img()
````

![](../assets/img/cartopy-relief.png#center)

### Définir les limites de la carte :

Cartopy adapte par défaut les limites des axes aux données projetées sur le graphique. Si l'on veut définir nos propres limites, on peut utiliser `ax.set_extent([x0, x1, y0, y1], crs=coordinate_system)`, qui permet de définir les limites de la carte. Par défaut, le système de coordonnées est géodétique.

```python
ax.set_extent([-180, -46.5, 8, 60], ccrs.Geodetic())
```

### Ajout de tracés géographiques avec cartopy.feature

cartopy.feature rend aisé l'ajout de tracés géographiques en les téléchargeant directement depuis une URL, ce qui évite d'avoir un package trop imposant.

Les paramètres de la classe .feature peuvent être trouvés sur le site [scitools.org.uk](https://scitools.org.uk/cartopy/docs/latest/matplotlib/feature_interface.html)

```python
import cartopy.feature as cf

ax.add_feature(cf.OCEAN) # Ocean polygons.
ax.add_feature(cf.LAND) 
ax.add_feature(cf.LAKES)
ax.add_feature(cf.BORDERS) # Country boundaries.
ax.add_feature(cf.COASTLINE)
ax.add_feature(cf.RIVERS)
ax.add_feature(cf.STATES)
````

Ces tracés sont utiles, mais l'intérêt de Cartopy est de permettre le chargement de tracés supplémentaires. On peut utiliser la méthode [cf.NaturalEarthFeature()](https://scitools.org.uk/cartopy/docs/latest/matplotlib/feature_interface.html#cartopy.feature.NaturalEarthFeature) pour obtenir des rasters du jeux de données [NaturalEarthData](https://www.naturalearthdata.com/features/).  

Par exemple, pour obtenir les lignes de provinces, on l'utilise en de la manière suivante :

```python
states_provinces = cfeature.NaturalEarthFeature(
    category='cultural',
    name='admin_1_states_provinces_lines',
    scale='50m',
    facecolor='none')
```
![](../assets/img/cartopy-states.png#center)

Pour obtenir un tracé plus fin que l'option basique `cf.coastline`, on peut utiliser :
```python
land_50m = cfeature.NaturalEarthFeature('physical', 'land', '50m',
                                        edgecolor='k',
                                        facecolor=cf.COLORS['land'])
````

![](../assets/img/Cartopy-coastline.png#center)

À quoi correspond l'option `facecolor=cf.COLORS['land']` ? 

`facecolor` permet de définir la couleur de remplissage des tracés utilisés, tandis que `cf.COLORS['land']` est un dictionnaire par défaut de cartopy.feature contenant des valeurs utilisables par défaut (*'land', 'land_alt1', 'water'*). Il est par conséquent possible de définir sois-même les valeurs en utilisant cette option.

![](../assets/img/Cartopy-ocean.png#center)

Les paramètres à spécifier pour préciser le ficher de carte à utiliser sont : *category, name, scale, facecolor*. Comment savoir quels fichiers sont disponibles et quel est le nom (*name*) ? [Un commentateur sur SO y répond](https://stackoverflow.com/questions/62402312/how-do-i-know-what-naturalearthfeature-names-are-available-for-cartopy) : il suffit d'aller voir sur le github de [natural-earth-vector](https://github.com/nvkelso/natural-earth-vector/tree/master/50m_cultural).

[La carte shaded relief arcGIS](https://kbkb-wx-python.blogspot.com/2019/05/add-arcgis-map-service-image-to-cartopy.html)

[La carte shaded relief google](https://www.geophysique.be/2014/02/25/shaded-relief-map-in-python/)

### Rajouter des points sur la carte

On utilise pour cela `plt.scatter(x = lattitude, y = longitude, transform= ccrs.Geodetic())`

Les paramètres *latitude* et *longitude* contiennent les coordonnées que l'on utilisera pour afficher les points sur la carte. 

> **À noter :** Préciser `transform=ccrs.Geodetic())` permet à Cartopy de savoir quel système de coordonnées on utilise, pour ensuite les transformer au format de la projection utilisé. 

```python
coordinates = [(39.9526, -75.1652), (37.7749, -122.4194),
               (40.4406, -79.9959), (38.6807, -108.9769),
               (37.8716, -112.2727), (40.7831, -73.9712)]
			   
latitude, longitude = np.array(coordinates).T
plt.scatter(x = longitude, y = latitude, transform=ccrs.Geodetic())
```

![](../assets/img/cartopy-geodetic.png#center)

#### Exemples

Un bon exemple de code concernant *natural-earth-vector* le mettant en œuvre se trouve ici : 
https://scitools.org.uk/cartopy/docs/latest/gallery/feature_creation.html

La galerie de Cartopy est excellente :
https://scitools.org.uk/cartopy/docs/v0.15/gallery.html

On peut modifier de manière créative les features :
https://coderzcolumn.com/tutorials/data-science/cartopy-basic-maps-scatter-map-bubble-map-and-connection-map#3.1



[^1]: Parfois l'installation peut rencontrer des problèmes de fait de shapely : https://stackoverflow.com/questions/60111684/geometry-must-be-a-point-or-linestring-error-using-cartopy
