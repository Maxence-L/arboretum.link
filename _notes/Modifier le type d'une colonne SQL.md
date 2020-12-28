---
title : Modifier le type d'une colonne SQL avec CAST
tags : SQL
etat : hiver
---

On peut modifier le [[Types de données SQL\|type]] d'une colonne dans un autre type compatible avec la fonction `CAST()`. Il est possible de `CAST`er un `integer` en `text` mais pas l'inverse.

La syntaxe est la suivante : `CAST(colname as type)`. On peut aussi utiliser les doubles points `::`, solution plus lisible : `colname::type`

Exemple avec le tableau `date_time_types` :

```SQL
SELECT timestamp_column, CAST(timestamp_column AS varchar(10))
FROM date_time_types;

SELECT timestamp_column::varchar(10)
FROM date_time_types;
````



