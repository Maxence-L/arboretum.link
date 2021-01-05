---
title : Opérations sur données aggrégées dans SQL
tags : SQl
etat : hiver
---

SQL donne la possibilité d'aggréger les données par groupes avec `GROUP BY col`. On peut ensuite appliquer des fonctions sur chacun de ces groupes.

## Utilisation de `GROUP BY`

### Sans fonction

La clause `GROUP BY` seule a un effet similaire à `DISTINCT` : elle supprime les doublons et retourne les paires uniques.

Avec deux colonnes, par exemple :

```SQL
SELECT city, stabr
FROM pls_fy2014_pupld14a
GROUP BY city, stabr
ORDER BY city, stabr;
````

| city | stabr |
| :--- | :--- |
| ABBEVILLE | AL |
| ABBEVILLE | LA |
| ABBEVILLE | SC |
| ABBOTSFORD | WI |
| ABERDEEN | ID |
| ABERDEEN | SD |
| ABERNATHY | TX |
| ABILENE | KS |
| ABILENE | TX |
| ABINGDON | IL |
| ABINGDON | VA |
|....|...|
| ZEELAND | MI |
| ZEIGLER | IL |
| ZELIENOPLE | PA |
| ZENDA | KS |
| ZIA PUEBLO | NM |
| ZION | IL |
| ZIONSVILLE | IN |
| ZUMBROTA | MN |
| ZUNI | NM |

### Avec une fonction

Exemple avec `count(*)`, qui compte le nombre de lignes dans la sélection :

```SQl
SELECT stabr, count(*)
FROM pls_fy2014_pupld14a
GROUP BY stabr
ORDER BY count(*) DESC;
````

| stabr | count |  
| :--- | :--- |  
| NY | 756 |  
| IL | 625 |  
| TX | 556 |  
| IA | 543 |  
| PA | 455 |  
| MI | 389 |  
| WI | 381 |  
| MA | 370 |  
| KS | 328 |  
| NJ | 296 |
|...|...|

Fonctions sont habituellement utilisées pour décrire les groupes :

- `count(*)` : compte le nombre de lignes dans la sélection
- `sum(col)` : somme les valeurs de chaque ligne de la sélection (colonne x groupe)
- `min(col), max(col)` : retourne la valeur minimum ou maximum de la sélection (colonne x groupe)

## Filtrer les groupes affichés avec `HAVING`

`HAVING` permet de filtrer en fin de requête les groupes retournés selon une condition.

L'avantage de `HAVING` est que le filtrage peut être le résultat d'une fonction appelée sur chaque groupe, une des colonnes de la sélection ou la colonne aggrégative.

Exemple pour un filtre sur la colonne aggrégative :

```SQL
SELECT stabr, count(*)
FROM pls_fy2014_pupld14a
GROUP BY stabr
HAVING stabr LIKE 'A%'
ORDER BY count(*) DESC;
````

| stabr | count |
| :--- | :--- |
| AL | 224 |
| AZ | 91 |
| AK | 85 |
| AR | 58 |
| AS | 1 |

Sur le résultat d'une fonction appelée sur chaque groupe :

```SQL
SELECT stabr, count(*)
FROM pls_fy2014_pupld14a
GROUP BY stabr
HAVING count(*) > 500
ORDER BY count(*) DESC;
````

| stabr | count |
| :--- | :--- |
| NY | 756 |
| IL | 625 |
| TX | 556 |
| IA | 543 |

> A noter : [En rêgle générale](https://waytolearnx.com/2018/08/difference-entre-having-et-where.html), `HAVING` vient après `GROUP BY` et permet de filtrer les résultats, tandis que `WHERE` vient en amont (avant `GROUP BY`, donc) et [[SELECTionner des données SQL\|permet de filtrer ce qui est inclu dans la sélection]].
