---
title: Geonamescache
tags: Cartographie
etat: printemps
---
Geonamescache[^1] est une bibliothèque contenant un grand nombre de nom de lieux, ainsi que leur caractéristique géographique (point moyen, pays, nom, etc...), pour leur utilisation en local.

[^1]: https://github.com/yaph/geonamescache

On l'installe ainsi :
```python
!pip install geonamescache
from geonamescache import GeonamesCache
````

GeonamesCache() ainsi importé est passé dans un objet, et l'on peut ensuite utiliser ses méthodes pour accéder au contenu :

```python
gc = GeonamesCache()
cities = gc.get_cities()
````

On a utilisé ici `.get_cities()`, mais l'on peut aussi utiliser d'autres méthodes pour accéder à d'autres éléments géographiques. Les méthodes possibles sont les suivante :

- get_continents()
- get_countries()
- get_us_states()
- get_cities()
- get_countries_by_names()
- get_us_states_by_names()
- get_cities_by_name()
- get_us_counties()

Pourquoi y-a t'il des méthodes `by_name` ? L'objet créé par les méthodes de GeoNamesCache est un dictionnaire imbriqué de la forme suivante :

`{clé du pays/ville : {clé_nom : 'nom', clé_latitude : '45,9585', etc...}}`

Pour accéder au dictionnaire contenant les caratéristiques de Paris, par exemple, il faut préciser 

```python
cities[paris_key]
{name : 'Paris', lat: ...}
````

Si l'on veut accéder au nom de Paris, il faut donc faire une double indexation :

```python
cities[paris_key]['name']
{name : 'Paris', lat: ...}
````

Si l'on connait la clé, on peut aussi directement accéder au dictionnaire afférent en l'incluant en paramètre :

```python
gc.get_cities_by_name(city_name)
````

On peut extraire de la manière suivante les villes contenues dans le dictionnaire :

```python
cities = gc.get_cities()
us_cities = [city for city in cities.values()
             if city['countrycode'] == 'US']
num_us_cities = len(us_cities)
print(f"GeoNamesCache holds data for {num_us_cities} US cities.")
````

Pour obtenir un dictionnaire `{nom: caractéristiques}` des villes, on exploite le [[Dict comprehension]] :
```python
cities = {city['name']:city for city in gc.get_cities().values() if city['countrycode'] == 'GB'}
```