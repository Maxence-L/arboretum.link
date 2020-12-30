---
title : JOINdre des tables SQL entre elles
tags : SQL
etat : hiver
toc : true
---

SQL repose sur le concept de bases de données *relationnelles* : les tableaux peuvent être reliés entre eux et présenter des facettes différentes des mêmes entités. 

Ainsi, un individu pourra avoir ses données fiscales enregistrées dans un tableau et ses données de santé dans un autre. Un identifiant individuel, appelé "clé" (*key*), reliera les tableaux entre eux. De même, une base de données enregistrant les données de consommation électrique journalières pourra être connectée avec une autre recensant les conditions météorologique afin de croiser les données et d'analyser leur relation possible.

La commande `JOIN`permet de relier entre elles les bases de données en produisant un tableau possédant chacune des colonnes des deux tableeaux de départ. Elle existe en plusieurs parfums, en fonction de la sélection des lignes que l'on souhaite réaliser. Pour la suite des exemples, on a les tableaux suivants :

`schools_left`

| id | left\_school |
| :--- | :--- |
| 1 | Oak Street School |
| 2 | Roosevelt High School |
| 5 | Washington Middle School |
| 6 | Jefferson High School |

`schools_right`

| id | right\_school |
| :--- | :--- |
| 1 | Oak Street School |
| 2 | Roosevelt High School |
| 3 | Morrison Elementary |
| 4 | Chase Magnet Acadeny |
| 6 | Jefferson High School |

## JOIN

`JOIN` produit un nouveau tableau dont les lignes sont une intersection des deux tableaux de départ. Par exemple, si on le tableau 1 a les identifiants de ligne (1,2,3,4,7) et le tableau 2 a (2,3,5,6,7), le tableau d'arrivée aura (2,3,7). 

La syntaxe de la commande `JOIN` est `FROM table1 JOIN table2 ON table1.key1_col = table2.key2_col` :

```SQL
SELECT * FROM table a 
	JOIN table 
	ON table_a.key1_col = table_b.key2_col;
```

Les colonnes-clés sont précisées par `table.key`, comme on peut le voir. On la désigne aussi par `INNER JOIN`.

> A noter : l'opérateur `=` n'est pas obligatoire : on peut aussi en utiliser d'autres, comme  `>=` par exemple. Voir la note [[SELECTionner des données SQL\|sur le sujet]].

Exemple :

```SQL
SELECT * 
FROM schools_left JOIN schools_right 
ON schools_left.id = schools_right.id;
````

| id | left\_school | id | right\_school |
| :--- | :--- | :--- | :--- |
| 1 | Oak Street School | 1 | Oak Street School |
| 2 | Roosevelt High School | 2 | Roosevelt High School |
| 6 | Jefferson High School | 6 | Jefferson High School |

On le voit, seules les lignes communes aux deux tableaux sont représentées.

### Sélectionner des colonnes particulières

Les colonnes trouvées dans le tableau d'arrivée sont précisées après `SELECT`. Pour éviter les ambiguités (par exemple, si les deux tableaux possèdent des colonnes synonymes), il faut préciser le `nom_du_tableau.nom_colonne`.

En effet, la requête suivante renvoie une erreur :

```SQL
SELECT id
FROM schools_left LEFT JOIN schools_right
ON schools_left.id = schools_right.id;
````

La manière correcte : 

```SQL
SELECT schools_left.id,
	schools_left.left_school,
	schools_right.right_school
FROM schools_left LEFT JOIN schools_right
ON schools_left.id schools_right.id;
````

id|left_school| right\_school|
|-|-|-|
1|Oak Street School|Oak Street School
2|Roosevelt High School|Roosevelt High School
5|Washington Middle School| |
6| Jefferson High School |Jefferson High School

Il est également possible de changer le nom de la colonne en utilisant `AS`: `SELECT schools_left.id AS left_id, ...`

## LEFT JOIN et RIGHT JOIN

`LEFT JOIN` et `RIGHT JOIN` produisent un tableau contenant toutes les lignes d'une table et des lignes vides pour celles qui ne sont pas communes aux deux tableaux. Le tableau de référence (qui restera complet) est désigné par la commande `LEFT` (le premier tableau) ou `RIGHT` (le second).

Avec `LEFT_JOIN` :

```SQL
SELECT * 
FROM schools_left LEFT JOIN schools_right 
ON schools_left.id = schools_right.id;
````

| id | left\_school | id | right\_school |
| :--- | :--- | :--- | :--- |
| 1 | Oak Street School | 1 | Oak Street School |
| 2 | Roosevelt High School | 2 | Roosevelt High School |
| 5 | Washington Middle School | NULL | NULL |
| 6 | Jefferson High School | 6 | Jefferson High School |

Avec `RIGHT_JOIN`:

| id | left\_school | id | right\_school |
| :--- | :--- | :--- | :--- |
| 1 | Oak Street School | 1 | Oak Street School |
| 2 | Roosevelt High School | 2 | Roosevelt High School |
| NULL | NULL | 3 | Morrison Elementary |
| NULL | NULL | 4 | Chase Magnet Acadeny |
| 6 | Jefferson High School | 6 | Jefferson High School |

## FULL OUTER JOIN

`FULL OUTER JOIN` produit un tableau contenant l'union des lignes de chacun des deux tableaux :

```SQL
SELECT * 
FROM schools_left FULL OUTER JOIN schools_right 
ON schools_left.id = schools_right.id;
````

| id | left\_school | id | right\_school |
| :--- | :--- | :--- | :--- |
| 1 | Oak Street School | 1 | Oak Street School |
| 2 | Roosevelt High School | 2 | Roosevelt High School |
| 5 | Washington Middle School | NULL | NULL |
| 6 | Jefferson High School | 6 | Jefferson High School |
| NULL | NULL | 4 | Chase Magnet Acadeny |
| NULL | NULL | 3 | Morrison Elementary |

## CROSS JOIN

`CROSS JOIN` est un peu particulier et s'apparente au [[Produit cartésien\|produit cartésien]]. Il retourne un tableau présentant toutes les combinaisons possibles de lignes entre les deux tableaux :

```SQL
SELECT * 
FROM schools_left CROSS JOIN schools_right;
````

| id | left\_school | id | right\_school |
| :--- | :--- | :--- | :--- |
| 1 | Oak Street School | 1 | Oak Street School |
| 1 | Oak Street School | 2 | Roosevelt High School |
| 1 | Oak Street School | 3 | Morrison Elementary |
| 1 | Oak Street School | 4 | Chase Magnet Acadeny |
| 1 | Oak Street School | 6 | Jefferson High School |
| 2 | Roosevelt High School | 1 | Oak Street School |
| 2 | Roosevelt High School | 2 | Roosevelt High School |
| 2 | Roosevelt High School | 3 | Morrison Elementary |
| 2 | Roosevelt High School | 4 | Chase Magnet Acadeny |
| 2 | Roosevelt High School | 6 | Jefferson High School |
| 5 | Washington Middle School | 1 | Oak Street School |
| 5 | Washington Middle School | 2 | Roosevelt High School |
| 5 | Washington Middle School | 3 | Morrison Elementary |
| 5 | Washington Middle School | 4 | Chase Magnet Acadeny |
| 5 | Washington Middle School | 6 | Jefferson High School |
| 6 | Jefferson High School | 1 | Oak Street School |
| 6 | Jefferson High School | 2 | Roosevelt High School |
| 6 | Jefferson High School | 3 | Morrison Elementary |
| 6 | Jefferson High School | 4 | Chase Magnet Acadeny |
| 6 | Jefferson High School | 6 | Jefferson High School |


