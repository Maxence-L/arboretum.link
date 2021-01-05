---
title : Création de tableau SQL
tags : SQL
etat : hiver
---

## Créer un nouveau tableau

Un tableau SQL est un objet utilisé pour stocker des donner, tel un récipient. Le tableau SQL est un objet contenu dans une base de données qui regroupera plusieurs tableaux (comme des récipients dans un placard).

Lors de la création d'un tableau SQL, on définit les variables qui en font partie : quel est leur nom, leur [types](https://www.w3schools.com/sql/sql_datatypes.asp) et leur longueur (si besoin).

La commande essentielle est `CREATE TABLE name` :

```SQL
CREATE TABLE employee (
	
	id INT PRIMARY KEY NOT NULL,
	first_name VARCHAR(25),
	last_name VARCHAR(50),
	team VARCHAR(50),
	hire_date DATE,
	salary numeric

);
````

- `id` correspond à un identifiant unique qui utilisé comme [clé primaire non-nulle](https://www.sqlite.org/lang_createtable.html#rowid).
- `first_name`, `last_name` et `team` correspondent à des variables texte de longueur précisée entre parenthèses.
- `hire_date` est de type `date` et permet de représenter des données temporelles.
- `salary` est au format `numeric`.

Il est par ailleurs possible d'ajouter des [[Clés et contraintes dans SQL\|contraintes ou de créer des clés]] lors de la création d'un tableau..

Une fois le tableau créée on peut [[Insérer des données dans un tableau SQL\| y insérer des données]].

## Créer un tableau de sauvegarde

Lorsque l'on procède à des importantes [[Modifier les données d'un tableau SQL\|modifications]] du tableau, il peut être indiqué de sauvegarder l'ancien.

La syntaxe est simple :

```SQL
CREATE TABLE table_backup AS
SELECT * FROM table;
````

On peut vérifier le résultat de la manière suivante :

```SQL
SELECT
	(SELECT count(*) FROM meat_poultry_egg_inspect) AS original,
	(SELECT count(*) FROM meat_poulty_egg_inspect_bac) AS backup;
```

> A noter : Si un index était présent, il faut le recréer sur le nouveau tableau.