---
title : Modifier les données d'un tableau SQL
tags : SQL
etat : printemps
toc : true
---

Au cas où, penser à faire  [[Créer un tableau (table) SQL\|une sauvegarde de la table]] ! Cette note triate de la modification des données d'un tableau, pas de [[Insérer des données dans un tableau SQL\|l'insertion de nouvelles données]].

## Modifier la structure d'un tableau

La clause `ALTER TABLE` permet de modifier la structure d'un tableau.

Ajout de colonne :

```SQL
ALTER TABLE table ADD COLUMN column data_type;
````

Suppression de colonne :

```SQL
ALTER TABLE table DROP COLUMN column;
````

Changement du type de donnée d'une colonne :

```SQL
ALTER TABLE table ALTER COLUMN column SET DATA TYPE data_type;
```

Ajout d'une [[Clés et contraintes dans SQL\|contrainte]] pour une colonne :

```SQL
ALTER TABLE table ALTER COLUMN column SET NOT NULL;
````

Retrait d'une contrainte attribuée à une colonne :

```SQL
ALTER TABLE table ALTER COLUMN column DROP NOT NULL;
````

## Modifier les valeurs d'un tableau

La clause `UPDATE` est utilisée pour modifer les données d'une colonne, pour toutes les lignes ou seulement celles remplissant une condition particulière.

La syntaxe simple est la suivante :

```SQL
UPDATE table
SET column = value;
```

On peut modifier plusieurs colonnes d'un coup :

```SQL
UPDATE table
SET column_a = value,
	column_b = value;
````

L'insertion de valeur peut être conditionnelle en ajoutant une clause `WHERE` :

```SQL
UPDATE table
SET column = value
WHERE criteria;
````

On peut aussi copier des valeurs d'une autre colonne de cette manière :

```SQL
UPDATE table
SET column = another_col
WHERE criteria;
````

Et enfin recourir à des données issues de tableaux différents :

Syntaxe PostgreSQL :

```SQL
UPDATE table
SET column = table_b.column
FROM table b
WHERE criteria;
````

Syntaxe ANSI utilisant une [[Les sous-requêtes SQL\|sous-requête]]:

```SQL
UPDATE table
SET column = (SELECT column
				FROM table b
				WHERE table.column = table_b.colum)
WHERE EXISTS (SELECT column
				FROM table_b
				WHERE table.column = table_b.colum);
````


