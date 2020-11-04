---
title: Scinder et rassembler les données dans une colonne
tags: pandas
etat : printemps
toc: true
---

Comment scinder ou concaténer les colonnes d'un [[dataframe]] ou d'une série ?

## Series.str.split() - Scinder en plusieurs colonnes

La méthode [[series.str.split()]] s'appelle sur une colonne d'un [[dataframe]] ou une [[series]] et retourne dans sa forme simple une liste pour chaque ligne. En pratique on passe de :

|Users|
|-|
|`Homme,20 ans`|
|`Femme,45 ans`|
|`Femme,15 ans`|

à

|Users|
|-|
|`[Homme,20 ans]`|
|`[Femme,45 ans]`|
|`[Femme,15 ans]`|

avec en donnant `','` comme séparateur :

```python
df['users'].str.split(',')
````

L'option `expand=True` produit une colonne par produit :

```python
df[['sexe', 'age']] = df['users'].str.split(',', expand=True)
````

|sexe|age|
|-|-|
|`Homme`|`20 ans]`|
|`Femme`|`45 ans`|
|`Femme`|`15 ans`|

Avec Series.str.get(), on peut sélection l'index de la liste que l'on désire :

```python
df['sexe'] = df['users'].str.split(',').get(0)
```
|sexe|
|-|
|`Homme`|
|`Femme`|
|`Femme`|

> **À noter :** il est possible d'utiliser .str.split() sur un index, ce qui produira un index multiniveaux.

## Series.Str.cat()- Regrouper plusieurs colonnes en une seule.

La méthode [[series.str.split()]] s'appelle sur une colonne d'un [[dataframe]] ou une [[series]] et concatène les strings de deux colonnes :

```python
df['users'] = df['sexe'].str.cat(df['age'], sep=',')
````

|Users|
|-|
|`Homme,20 ans`|
|`Femme,45 ans`|
|`Femme,15 ans`|

> **À noter :** on peut aussi passer une liste ou une Series à la place de `df['age']`, dans .str.cat()

Cela peut être utile avec la fonction [[Manipuler les formats longs et larges de données\|pd.melt()]] si l'on a deux colonnes d'identifiant que l'on désire regrouper en une seule.

## df.explode() - Scinder une colonne en lignes supplémentaires

### Scinder une liste
La fonction `df.explode()` scinde une colonne de longueur $n$ contenant une **liste** de $k$ valeurs et produit une colonne de taille $n \cdot k$ :

```python
df['users'].explode()
````

-|users|
-|-|
0|`Homme`|
0|`20 ans`|
1|`Femme`|
1|`45 ans`|
2|`Femme`|
2|`15 ans`|

> **À noter :** Chaque ligne possède l'index de sa ligne originelle.

On peut exploiter cette particularité de l'index pour scinder une colonne tout en gardant ses identifiant, en utilisant [[df.merge()]] :

```python
cities_explode = cities['zip_code'].explode()
cities[ [ `city`, `country] ].merge(cities_explode, 
									left_index=True, 
									right_index=True)
````

![](../assets/img/df.explode().png)

On peut aussi le faire (plus rapidement) en précisant la colonne que l'on veut scinder :

```python
cities_explode = cities.explode('zip_code')
````

Attention toutefois, cette méthode peut produire des valeurs manquantes si des listes vides sont dans la colonne.

### Scinder une colonne `string` en plusieurs lignes

On chaine les méthodes [[df.assign()]], [[series.str.split()]], [[series.explode()]] :

```python
cities.assign(zip_code=cities['zip_code'].str.split(',')).explode(`zip_code`)
````


