---
title : Les sous-requêtes SQL
tags : SQL
etat : hiver
---

Les sous-requêtes SQL sont déclarées  comme des requêtes entre paranthèses, nichées à l'intérieur d'autres requêtes. Elles servent à produire des données pour l'exécution de la requête principale.

Les sous-requêtes SQL ont pour résultat des valeurs individuelles ou une série de lignes.

## Syntaxe

```SQL
SELECT column_names
FROM table_name1
WHERE value IN (SELECT column_name
		FROM table_name2
		WHERE condition)
````

En pratique, on a la table suivante :

| company | street | city | st | zip | grant_date | activities | dbas |  
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | 
| 121 In-Flight Catering LLC | 45 Rason Road | Inwood | NY | 11096 | 2016-06-27 | Meat Processing, Poultry Processing | NULL |  
| 165368 C. Corporation  | 5617 Hoover Street, Suite A | Houston | TX | 77092 | 2014-06-30 | Meat Processing | Long Phung Food Products |  
| 1732 Meats LLC | 6250 Baltimore Pike | Yeadon | PA | 19050 | 2015-09-08 | Meat Processing, Poultry Processing | NULL |  
| 1st Original Texas Chili Company, Inc. | 3313 N. Jones Street | Fort Worth | TX | 76106 | 2003-11-28 | Meat Processing | T.C. Foods; Texas Chili |
|...|...|...|...|...|...|...|...|

On appllique la requête suivante :

```SQl
SELECT street, city, st
FROM meat_poultry_egg_inspect
WHERE zip in (SELECT zip
    FROM meat_poultry_egg_inspect
    WHERE zip LIKE '2780%');
````

| street | city | st |
| :--- | :--- | :--- |
| 5645 Deans Street | Bailey | NC |
| 1331 Wesleyan | Rocky Mount | NC |
| 751 Fenner Rd. | Rocky Mount | NC |
| 7251 North US Hwy 301 | Battleboro | NC |
| 1951 Centura Highway | Rocky Mount | NC |
| 225 John Hancock Road | Taunton | MA |
| 615 N. Church Street | Rocky Mount | NC |

On le voit, cela permet de réaliser des requêtes beaucoup plus fines. On peut de cette mainière consulter des tableaux différents du tableau de la requête principale. Il suffit de changer le nom de la table après `FROM` dans la sous-requête.

## `EXISTS (sous-requête)`

La clause `EXISTS` vérifie l'existence de résultats dans la sous-requête associée. Si des résultats existent elle retourne `TRUE`.

Syntaxe : 

```SQL
SELECT column-names
FROM table-name
WHERE EXISTS
	(SELECT column-name
	FROM table-name
	WHERE condition)
````

[Exemple](https://www.dofactory.com/sql/where-exists) :

```SQL
SELECT CompanyName
FROM Supplier
WHERE EXISTS
		(SELECT ProductName
		FROM Product
		WHERE SupplierId = Supplier.Id
			AND UnitPrice > 100)
````

