---
title : Insérer des données dans un tableau SQL
tags : SQL
etat : hiver
---

Une fois le tableau [[Création de tableau SQL\|créé]], on peut y ajouter es données avec la commande `INSERT` :

```SQL
INSERT INTO teachers (first_name, last_name, school, hire_date, salary)

VALUES 	('Janet' 'Smith', 'F.D. Roosevelt HS', '2011-10-30', 36200),
		('Lee', 'Reynolds', 'F.D. Roosevelt HS', '1993-05-22', 65000)
		('Samuel', 'Cole', 'Myers Middle School','2005-08-01', 43500),
		('Samantha', 'Bush', 'Myers Middle School' '2011-10-30', 36200),
		('Betty', 'Diaz', 'Myers Middle School', '2005-08-30', 43500) ,
		('Kathleen', 'Roush', 'F.D. Roosevelt HS', '2010-10-22', 38500);
```

La commande `INSERT INTO table_name (vars*)` permet de spécifier la table ainsi que le nom des colonnes qui seront remplies.

La commande `VALUES (values*)` accepte les valeurs à ajouter au tableau. Elles doivent être dans l'ordre donné par la commande `INSERT` et les lignes sont séparées par des parenthèses + virgules.

Quelques points :

- Le texte est défini par des guillemets : `'text'`.
- Les nombres (entiers et décimaux) peuvent être écrits directement.
- Les dates sont au [format](https://xkcd.com/1179/) **YYYY-MM-DD**.

