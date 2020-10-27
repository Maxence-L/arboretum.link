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

Pour extraire les données des dictionnaires et les transférer à un dataframe :

```python
gc = GeonamesCache()
cities = gc.get_cities()
num_cities = len(cities)

city_list = [city for city in cities.values()]
array_city_list = np.array(city_list)
df_city = pd.DataFrame(data=array_city_list[0], index=[0])

for i in range(1,len(array_city_list)):
    df_city = df_city.append(array_city_list[i], ignore_index = True)
    if i % 500 == 0 :
        print(i)
		
df_city.to_csv('cities_file.csv')
````

