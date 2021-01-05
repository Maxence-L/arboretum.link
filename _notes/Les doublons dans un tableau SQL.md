---
title : Les doublons dans un tableau SQL
tags : SQL
etat : printemps
toc : true
---

Trois types de doublons existent :

- **Doublon absolu** : Il correspond aux combinaisons identiques de valeurs sur toute une ligne. Par exemple, (1,0,0) et (1,0,0) sont des doublons absolus, (1,0,0) et (1,0,1) n'en sont pas, même s'ils partagent quelques mêmes valeurs.
- **Doublon relatif** : Il correspond à un doublon absolu, moins la clé (qui est différente).
- **Quasi doublon** : Il correspond aux lignes comportant des valeurs identiques. On peut ajuster la détection des quasi doublons en précisant quelles colonnes nous importent. 

On part du tableau suivant :

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


