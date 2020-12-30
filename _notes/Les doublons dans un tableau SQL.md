---
title : Les doublons dans un tableau SQL
tags : SQL
etat : printemps
toc : true
---

La gestion des doublons n'est pas toujours aisée avec SQL et internet regorge d'astuces à ce sujet.

Le tableau des exemples :

```SQL
CREATE table test (  
    champ1 integer,  
 champ2 varchar(10),  
 champ3 varchar(10)  
);  
  
INSERT INTO test (champ1, champ2, champ3) VALUES  
 (1,'deux','Pierre'),  
 (1,'deux','Léon'),  
 (4,'cinq','Hugo'),  
 (4,'cinq','Hugo'),  
 (7,'huit','Albert');
 ````

| champ1 | champ2 | champ3 |
| :--- | :--- | :--- |
| 1 | deux | Pierre |
| 1 | deux | Léon |
| 4 | cinq | Hugo |
| 4 | cinq | Hugo |
| 7 | huit | Albert |

## Retirer les doublons avec `DISTINCT`

On utilise le mot-clé `DISTINCT` associé aux colonnes qui nous intéressent. Cette fonction est utile pour comprendre les valeurs possibles d'une variable, par exemple.

```SQL
SELECT DISTINCT champ1
FROM test;
````

Le résultat est le suivant :

| champ1 |
| :--- |
| 4 |
| 7 |
| 1 |

On peut aussi appliquer `DISTINCT` à plusieurs colonnes ; les lignes retournées correspondent aux observations uniques.

```SQL
SELECT DISTINCT champ1, champ2
FROM test;
````

| champ1 | champ2 |
| :--- | :--- |
| 4 | cinq |
| 1 | deux |
| 7 | huit |


Plusieurs astuces existent pour lister les les doublons. Voir cet article de [sql.sh](https://sql.sh/55-requete-trouver-doublon). 

## Afficher les doublons

On peut aussi vouloir lister les doublons présents dans le tableau.

```SQL
select count(*) AS nbr_doublon, champ1
FROM test
GROUP BY champ1
HAVING COUNT(*) > 1;
````

| nbr\_doublon | champ1 |
| :--- | :--- |
| 2 | 4 |
| 2 | 1 |

On peut obtenir les doublons complets (toute la ligne est un doublon) en précisant l'intégralité des colonnes dans la commande `GROUP BY` :

```SQL
select count(*) AS nbr_doublon, champ1, champ2, champ3
FROM test
GROUP BY champ1, champ2, champ3
HAVING COUNT(*) > 1;
````

| nbr\_doublon | champ1 | champ2 | champ3 |
| :--- | :--- | :--- | :--- |
| 2 | 4 | cinq | Hugo |


