---
title: Associer des noms de lieux à des coordonnées géographiques
tags: Cartographie
etat: printemps
---
Cette note recense les bibliothèques python et les services permettant de trouver les coordonnées géographiques (lat, long) de noms de lieux.

## Geopy

## Geonamescache

Geonamescache[^1] est une bibliothèque contenant un dictionnaire regroupant grand nombre de nom de lieux, ainsi que leurs caractéristiques géographiques (point moyen, pays, nom, etc...).

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

Pour obtenir un dictionnaire `{nom: caractéristiques}` des villes, on exploite une [[Comprehension\|dict comprehension]] :

```python
cities = {city['name']:city for city in gc.get_cities().values() if city['countrycode'] == 'GB'}
```

## Association de Geopy et de Geonamescache :

Geonamescache ne nécessite pas de connexion à une api, et est moins complète que GeoPy mais plus rapide. Il peut donc être utile de combiner les deux dans un programme.

Dans le cas d'un programme cherchant la localisation d'utilisateurs de l'application OKCupid :

```python
# Initialisationd des bibliothèques de géolocalisation
geolocator = Nominatim(user_agent="ok_cupidon")
gc = GeonamesCache()

# On récupère les villes anglaises de geonamescache
gb_cities = {city['name']:city for city in gc.get_cities().values() if city['countrycode'] == 'GB'}


unfound = dict()
none_city = set()

def find_geoloc(city, latlong_index):
    """La fonction cherche d'abord dans geonamescache puis dans geopy,
	si elle ne l'a pas trouvé, le nom de la ville et retourne 'NaN' si
	elle est inconnue, les coordonnées sinon.""" 
	
    latlong = ['latitude','longitude']
    
    if city in gb_cities.keys():
        return gb_cities[city][latlong[latlong_index]]

    elif city in unfound.keys():
        return unfound[city][latlong_index]

    else :
        try:
            city_geo = geolocator.geocode(f"{city}, UK")[1]
            unfound[city] = city_geo
            return unfound[city][latlong_index]
			
        except: TypeError
            # Some unknown names return 'None'
        none_city.add(city)
        return 'NaN'

location['latitude'] = location['content'].apply(find_geoloc, args=[0])
location['longitude'] = location['content'].apply(find_geoloc, args=[1])

print(none_city)
print(f" There are {location['content'].isin(none_city).sum()} profiles that couldn't be located")
````
