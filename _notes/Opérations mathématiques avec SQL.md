---
title: Opérations mathématiques avec SQL
tags : SQL
etat : été
toc : true
---

SQL peut être utilisé pour réaliser des calculs simples ainsi que de calculer des statistiques descriptives.

## Opérateurs mathématiques

Opérateur|Description
-|-
+|Addition
-|Soustraction
\*|Multiplication
/|Division
%|Modulo
^|Puissance
\|/|Racine carrée
\|\|/|Racine cubique
!|Factorielle

D'autres fonctions existent, comme `ROUND()`, complémentaire aux opérateurs.

Un manière simple de tester les opérateurs est d'utiliser `SELECT` :

```SQL
SELECT 4 + 3;
SELECT 4 - 2:
SELECT 2 * 3;
```

On obtient le résultat suivant pour la première requête :

| ?column? |
| :--- |
| 4 |

## Calculs et types de données

Les calculs se font entre [[Types de données SQL\|types de données]]  `number`. Quels types sont produits si les opérations utilisent des données de format différent ?

- Deux `integer` donnent un `integer`
- Un `numeric` conjugué avec un autre nombre donne un `numeric`.
- Un nombre à virgule flottante retourne un `double precision`.

> A noter : la puissance, les racines et les factorielles retournent des types `numeric` ou `double precision`.

On peut utiliser `CAST` pour modifier le type de données retournées. Plus sur le sujet dans la [[Types de données SQL\|note dédiée]].

```SQL
SELECT 11.0 / 6;
````

| ?column? |
| :--- |
| 1.8333333333333333 |

Avec `CAST` :
```SQL
SELECT CAST(11.0 / 6 AS integer);
````

| int4 |
| :--- |
| 2 |

## Effectuer des calculs sur des colonnes

Les calculs sur les colonnes peuvent être faits lors de la [[SELECTionner des données SQL\|sélection]] ou lors d'une [[Insérer des données dans un tableau SQL\|insertion]] :

On a la table `supervisor_salaries` suivante :

| town | supervisor | salary | benefits |
| :--- | :--- | :--- | :--- |
| Anytown | Jones | $27,000.00 | $10,000.00 |
| Bumblyburg | Baker | $24,999.00 | $10,000.00 |
| Moetown | Smith | $32,100.00 | $10,000.00 |
| Bigville | Kao | $31,500.00 | $10,000.00 |
| New Brillig | Carroll | $72,690.00 | $10,000.00 |

Sélection simple :

```SQL
SELECT benefits * salary AS total_compensation
FROM supervisor_salaries;
````

| total\_compensation |
| :--- |
| $37,000.00 |
| $34,999.00 |
| $42,100.00 |
| $41,500.00 |
| $82,690.00 |

Avec une insertion de colonne :

```SQL
update supervisor_salaries
    SET total_comp = salary + benefits 
	WHERE salary IS NOT NULL AND benefits IS NOT NULL;

SELECT * from supervisor_salaries;
````

| town | supervisor | salary | benefits | total\_comp |
| :--- | :--- | :--- | :--- | :--- |
| Anytown | Jones | $27,000.00 | $10,000.00 | $37,000.00 |
| Bumblyburg | Baker | $24,999.00 | $10,000.00 | $34,999.00 |
| Moetown | Smith | $32,100.00 | $10,000.00 | $42,100.00 |
| Bigville | Kao | $31,500.00 | $10,000.00 | $41,500.00 |
| New Brillig | Carroll | $72,690.00 | $10,000.00 | $82,690.00 |

## Calculer des statistiques descriptives

SQL vient avec des fonctions permettant d'obtenir des statistiques descriptives :

- Moyenne : `AVG()`
- Percentiles (et donc médiane) : `percentile_cont(n)` pour des percentiles continus et `percentile_dic(n)` pour des percentiles discrets (égal à la valeur d'une observation)


