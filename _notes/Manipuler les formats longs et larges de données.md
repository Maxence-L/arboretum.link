---
title : Manipuler les formats longs et larges de données
tags: pandas
etat: printemps
toc: true
---

Les données tabulaires peuvent se présenter sous deux aspects : long et large. 

## Définition

### Format long

Dans le cas des données dites longues, chaque ligne correspond à un **attribut** d'une observation dont l'**identifiant** et la **valeur** associés  sont situés dans une colonne à part. On a donc un grand nombre de lignes, égal à attributs\*observations. 

La structure est semblable à un dictionnaire, avec des paires clé/valeur emboitées. Elle est pratique lorsque l'on récolte des données dont on ne connait pas d'avance tous les attributs possibles.

|id|attribut|valeur|
|-|-|-|
1|taille|185|
1|poids|85|
1|yeux|bleus|
2|taille|155|
2|poids|55|
...|...|...|

Le passage d'un format long à un format large se fait avec les méthodes [[df.pivot()]] et [[df.pivot_table()]].

### Format large

Les données dites larges comportent une colonne pour chaque attribut et une ligne pour chaque observation. Contrairement au format long, les données larges peuvent contenir des valeurs manquantes.

Ce format facilite l'analyse statistique et est considéré comme plus lisible : toutes les informations sur un même individu se trouvent sur la même ligne.

|id|taille|poids|yeux|
|-|-|-|-|
|1|185|58|bleus|
|2|155|55|NaN|
|...|...|...|...|

Le passage d'un format large à un format large est fait avec [[pd.melt()]] et [[pd.wide_to_long()]]

## Transposer un dataframe

On peut utiliser la méthode `df.set_index('colonne')`, qui fera passer une colonne en index, conjointement avec [[df.transpose()]] pour transposer un dataframe. Les valeurs passées en index deviendront les noms des colonnes.

Un exemple avec un dataframe `fifa_players` :

```python
fifa_players
```

| name| age | height | weight  | nationality | club                |
|-------------------|-----|--------|---------|-------------|---------------------|
| Lionel Messi      | 32  | 170    | 72      | Argentina   | FC Barcelona        |
| Cristiano Ronaldo | 34  | 187    | 83      | Portugal    | Juventus            |
| Neymar da Silva   | 27  | 175    | 68      | Brazil      | Paris Saint-Germain |
| Jan Oblak         | 26  | 188    | 87      | Slovenia    | Atlético Madrid     |
| Eden Hazard       | 28  | 175    | 74      | Belgium     | Real Madrid         |

On fait passer 'name' en index, on sélectionne les variables qui nous intéressent et on transpose les données :

```python
fifa_transpose = fifa_players.set_index('name')[ ['height', 'weight']  ].transpose()
`````

| name   | Lionel Messi | Cristiano Ronaldo | Neymar da Silva | Jan Oblak | 175 |
|--------|--------------|-------------------|-----------------|-----------|-----|
| height | 170          | 187               | 175             | 188       | 74  |
| weight | 72           | 83                | 68              | 87        | 74  |



## "Pivoter" un jeu de données - format long vers format large

### df.pivot() - pivot simple

La fonction [[df.pivot()]] correspond à la modification de données opérée par les tableaux croisés dynamiques sur Excel. Elle permet de passer d'un format long à un format court :

![](/assets/img/format-long-court.png#center)

Les paramètres principaux sont les suivants :

- `Index` définit quel sera l'**identifiant** de chaque observation. Dans le cas ci-dessus, ce sera `Year`.

- `columns` définit la colonne de la table longue dont les **attributs** seront utilisés pour former les colonnes de la table d'arrivée large. Ici, `Name`.

- `values` correspond aux **valeurs** associées à la paire attribut * identifiant.

On a le [[pd.Dataframe]] suivant :

```python
fifa_players
````

| - | movement          | overall   | attacking | attacking |
|---|-------------------|-----------|-----------|-----------|
| 0 | L. Messi          | shooting  | 92        | 70        |
| 1 | Cristiano Ronaldo | shooting  | 93        | 89        |
| 2 | L. Messi          | passing   | 92        | 92        |
| 3 | Cristiano Ronaldo | passing   | 82        | 83        |
| 4 | L. Messi          | dribbling | 96        | 88        |
| 5 | Cristiano Ronaldo | dribbling | 89        | 84        |

On le "pivote" :

```python
fifa_players.pivot(index = 'movement', columns= 'name', values = 'overall')
````

| name      | Cristiano Ronaldo | L. Messi |
|-----------|-------------------|----------|
| movement  |                   |          |
| dribbling | 89                | 96       |
| passing   | 82                | 92       |
| shooting  | 93                | 92       |

On peut passer plusieurs attributs en valeurs, ce qui crée des index de colonnes emboités :

```python
fifa_over_attack = fifa_players.pivot(index='name', 
                                     columns='movement', 
                                     values=['overall', 'attacking'])
`````

|                   | shooting  |         |          | shooting  |         |          |
|-------------------|-----------|---------|----------|-----------|---------|----------|
| movement          | dribbling | passing | shooting | dribbling | passing | shooting |
| name              |           |         |          |           |         |          |
| Cristiano Ronaldo | 89        | 82      | 93       | 84        | 83      | 89       |
| L. Messi          | 96        | 92      | 92       | 88        | 92      | 70       |

> **À noter :** Si le [[dataframe]] contient des doublons, [[df.pivot()]] retournera une [[ValueError]]. On peut les supprimer avec [[df.drop()]] ou [[df.drop_duplicates()]]

### [df.pivot_table()](https://pandas.pydata.org/pandas-docs/stable/user_guide/reshaping.html#pivot-tables) - pivot avec agrégation

La méthode [[df.pivot_table()]] nous permet de définir une fonction agrégative, comme [[df.mean()]], pour agréger les valeurs correspondant au même **index**.

![](/assets/img/pivot_table.png#center)

Les paramètres principaux sont les suivants :

- `Index` définit quel sera l'**identifiant** de chaque observation. Dans le cas ci-dessus, ce sera `Year`. 

> **À noter** : Il est possible de définir des index hiérarchiques, par exemple `[nom, age]`. En conséquence, l'index contiendra deux colonnes, et une ligne pour chaque combinaison des variables indexées.

- `columns`définit la colonne de la table longue dont les **attributs** seront utilisés pour former les colonnes de la table d'arrivée large. Ici, `Name`.

- `values` correspond aux **valeurs** associées à la paire attribut x identifiant.

- `aggfunc`donne la fonction utilisée pour agréger les valeurs. On peut passer une méthode [[dataframe]], mais aussi une fonction étrangère, comme [[np.sum()]] ou même une liste de fonctions associées à la colonne de valeurs à traiter, sous forme de dictionnaire : `{'D': np.mean, 'E': np.mean}`.

> **À noter** : Si l'on ajoute l'option `margins=True`, des [agrégats partiels](https://pandas.pydata.org/pandas-docs/stable/user_guide/reshaping.html#adding-margins) (comme dans un tableau de comptabilité, par exemple) seront calculés et placés dans une colonne et une ligne en marge, nommées `All`.

On a la table suivante :

| id     | name                              | age | height | weight | nationality | club          |
|--------|-----------------------------------|-----|--------|--------|-------------|---------------|
| 192448 | Marc-André ter Stegen             | 27  | 187    | 85     | Germany     | FC Barcelona  |
| 177003 | Luka Modrić                       | 33  | 172    | 66     | Croatia     | Real Madrid   |
| 176580 | Luis Alberto Suárez Díaz          | 32  | 182    | 86     | Uruguay     | FC Barcelona  |
| 194765 | Antoine Griezmann                 | 28  | 176    | 73     | France      | FC Barcelona  |
| 182521 | Toni Kroos                        | 29  | 183    | 76     | Germany     | Real Madrid   |
| 200145 | Carlos Henrique Venancio Casimiro | 27  | 185    | 84     | Brazil      | Real Madrid   |
| 165153 | Karim Benzema                     | 31  | 185    | 81     | France      | Real Madrid   |
| 205600 | Samuel Umtiti                     | 25  | 182    | 75     | France      |  FC Barcelona |
| ...    | ...                               | ... | ...    | ...    | ...         | ...           |

On cherche la taille maximum des joueurs, par nationalité, pour chaque club :

```python
tall_players_fifa = fifa_players.pivot_table(index='nationality', 
                                     columns='club', 
                                      values='height', 
                                      aggfunc='max')
````

| club        | FC Barcelona | Real Madrid |
|-------------|--------------|-------------|
| nationality |              |             |
| Brazil      | 190          | 186         |
| Croatia     | 184          | 172         |
| France      | 190          | 191         |
| Germany     | 187          | 183         |
| Uruguay     | 182          | 182         |

## "Fondre" un jeu de données - format large vers format long

Le passage d'un jeu de données large en long se fait avec la fonction `df.melt()` :

![df.melt](../assets/img/manipulation-melt.png#center)

La méthode `df.melt()` prend comme principal argument `id_vars`. On peut y passer plusieurs identifiants sous forme de liste, comme pour les valeurs, choisies avec l'argument `value_vars`. On peut spécifier le nom des colonnes relatives aux valeurs au moyen des arguments `var_name` et `value_name` :

![](../assets/img/melt-id-vars.png#center)



## Ressources

- [Un tuto intéressant dans la doc pandas](https://pandas.pydata.org/docs/getting_started/intro_tutorials/07_reshape_table_layout.html?highlight=wide)
- https://pandas.pydata.org/pandas-docs/stable/user_guide/reshaping.html

## Questions :
Quels sont les trois principaux arguments de `.pivot()` ?

[[`index`, `columns`, `values`::srs]]

Quel argument de `pivot_table()` correspond à la fonction qui sera utilisée sur les valeurs ?

[[`aggfunc`::srs]]

Quel argument de `pivot_table()` permet d'obtenir les marges d'un tableau ?

[[margins=True::srs]]

Quelle méthode est utile pour transposer un [[dataframe]] ?

[[df.transpose()::srs]]

Comment passer plusieurs fonctions d'agrégation dans `pivot_tables()` ?

[[Sous forme de dictionnaire, en associant la colonne à transformer avec la fonction::srs]]

