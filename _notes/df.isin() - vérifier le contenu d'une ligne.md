---
title: df.isin() - vérifier le contenu d'une ligne
tags: pandas
etat: printemps
---

df.isin(*liste*) permet de vérifier pour chaque ligne du [[dataframe]] (ou de la colonne, si l'on appelle la méthode dessus) si elle contient un des éléments spécifiés en paramètres et renvoie un booléen en fonction de la réponse.

**En fonction de l'objet passé en paramètre (dict, Series, Dataframe...), le matching ne se fera pas de la même manière.** Voir la doc[^1] pour plus de précisions, notamment si l'on utilise autre chose qu'une liste.

[^1]: https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.isin.html

**A savoir aussi : df.isin() revoie *True* s'il s'agit d'un élément exact. Si l'on veut trouver un pattern, il faut utiliser [[str.contains()]]**

Par exemple, si l'entrée est "Etats-Unis d'Amérique", df.isin() retournera un *False* pour cette entrée.


La méthode retourne un [[dataframe]] ou une [[series]] (en fonction de l'objet sur lequel elle est appelée) composée de booléens, qui peuvent être ensuite utilisés pour réaliser une sélection avec [[df.loc]] par exemple.

```python
df = pd.DataFrame({'num_legs': [2, 4], 'num_wings': [2, 0]},
                  index=['falcon', 'dog'])
df

>         num_legs  num_wings
> falcon         2          2
> dog            4          0
````

On recherche les animaux ayant zéro ou deux membres :

```python
df.isin([0, 2])

>		num_legs  num_wings
> falcon      True       True
> dog        False       True
````

Cette méthode est utile pour réaliser des sélections conditionnelles :

```python
df[df['num_legs'].isin([2])]

>         num_legs  num_wings
> falcon         2          2
````

Si l'on désire chercher *NOT IN* au lieu de *IN*, il faut préciser [[~]] en préfixe à la condition[^2] :
[^2]: https://stackoverflow.com/questions/19960077/how-to-filter-pandas-dataframe-using-in-and-not-in-like-in-sql

```python
```python
df[~df['num_legs'].isin([2])]

>         num_legs  num_wings
> falcon         2          2*
````

