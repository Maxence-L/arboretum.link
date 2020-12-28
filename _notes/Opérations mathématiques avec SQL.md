---
title: Opérations mathématiques avec SQL
tags : SQL
etat : printemps
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

