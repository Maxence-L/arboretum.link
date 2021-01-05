---
title : Importer et exporter des données CSV dans une base SQL
tags : SQL
etat : été
toc : true
---

La commande centrale pour l'import et l'export des données SQL est [`COPY`](https://www.postgresql.org/docs/9.1/sql-copy.html).

## Import

On peut importer les données d'un fichier dans une table déjà existante. Il faut donc [[Création de tableau SQL\|définir les colonnes]] (et leur [[Types de données SQL\|type]]) avant de procéder à l'import.

Un exemple simple d'import :

```SQL
COPY table_name
FROM 'C:/YourDirectoryl/your_file.csv'
WITH (FORMAT CSV, HEADER, DELIMITER '|', QUOTE '''');
````

`WITH` permet de définir les caractéristiques de la base importée.

- Trois formats sont disponibles : `CSV`, `TEXT`, `BINARY`. Des précisions sur chacun des formats sont dans la [doc](https://www.postgresql.org/docs/9.1/sql-copy.html) de PostgreSQL.

- `HEADER` permet d'indiquer que la première ligne d'un fichier CSV correspond à un header. On peut aussi y insérer un booléen : `HEADER true` ou  `false` (par défaut si `HEADER`n'est pas précisé dans la commande.). SQL appariera les données avec leur nom de colonne dans la table.

- `DELIMITER` permet de définir le caractère qui sépare les colonnes. Il correspond à une virgule par défaut avec un fichier `CSV` et une tabulation avec un fichier `TEXT`.

- `QUOTE` définit le qualificatif textuel, permettant d'échapper les caractères problématiques pour un import de CSV, comme la virgule par exemple (qui serait sans cela prise en compte comme séparateur). PostgreSQL utilise par défaut le guillemets anglais (`"`). On [utilise](https://stackoverflow.com/questions/12856377/the-correct-copy-command-to-load-postgresql-data-from-csv-file-that-has-single-q) `''''` pour un guillemet simple.

### Importer un sous-ensemble des colonnes présentes dans le tableau

Il peut arriver que le fichier CSV que l'on désire importer soit incomplet et ne présente pas toutes les colonnes du tableau receveur. Dans ce cas, la commande `COPY` retournera une erreur si l'on ne lui précise entre parenthèses pas quelles colonnes sont importées :

```SQL
COPY table_name (col3, col4, col6)
FROM 'C:/YourDirectoryl/your_file.csv'
WITH (FORMAT CSV, HEADER, DELIMITER '|', QUOTE '''');
```

Les colonnes manquantes apparaitront alors comme vides.

##### Définir des valeurs par défaut pour remplacer les valeurs manquantes lors de l'import

Une astuce pour définir des valeurs par défaut consiste à importer les données dans un tableau temporaire, puis à l'insérer dans le tableau de destination avec les valeurs par défaut. Exemple :

```SQL
-- Création du tableau de destination
CREATE TABLE supervisor_salaries (
town varchar(30),
county varchar(30),
supervisor varchar(30),
start_date date,
salary money,
benefits money
);

-- Création du tableau temporaire
CREATE TABLE supervisor_salaries_temp (LIKE supervisor_salaries);

-- Importation du fichier CSV dans le tableau temporaire
COPY supervisor_salaries_temp (town, supervisor, salary)
FROM '/Users/scripts/supervisor_salaries.txt'
WITH (FORMAT CSV, HEADER);

-- Insertion du tableau temporaire + la valeur par défaut de 'county'
INSERT INTO supervisor_salaries(town, county, supervisor, salary)
SELECT town, 'Some county', supervisor, salary
FROM supervisor_salaries_temp;

select * from supervisor_salaries;
````

| town | county | supervisor | start\_date | salary | benefits |
| :--- | :--- | :--- | :--- | :--- | :--- |
| Anytown | Some county | Jones | NULL | $27,000.00 | NULL |
| Bumblyburg | Some county | Baker | NULL | $24,999.00 | NULL |
| Moetown | Some county | Smith | NULL | $32,100.00 | NULL |
| Bigville | Some county | Kao | NULL | $31,500.00 | NULL |
| New Brillig | Some county | Carroll | NULL | $72,690.00 | NULL |

## Export

### Exporter toutes les données du tableau

L'export du tableau complet, le plus simple, se fait avec ``COPY table_name TO '/path/' WITH (options*);`

```SQL
-- Export au format CSV
COPY supervisor_salaries 
TO '/Users/Me/scripts/test.txt'
WITH (FORMAT CSV, HEADER, DELIMITER '|');
````

### Exporter certaines colonnes seulement

Dans ce cas, on ajoute le nom des colonnes entre parenthèses : `COPY table_name (col1,col3,col4)``

```SQL
COPY supervisor_salaries (tow, county, supervisor)
TO '/Users/Me/scripts/test.txt'
WITH (FORMAT CSV, HEADER, DELIMITER '|');
````

### Exporter les résultats d'une requête

On exporte les résultats d'une requête en la précisant entre parenthèses : `COPY (full_request)`

```SQL
COPY (
	SELECT supervisor, salary, benefits
	FROM supervisor_salary
	WHERE salary BETWEEN 30000 AND 50000)
TO '/Users/Me/scripts/test.txt'
WITH (FORMAT CSV, HEADER, DELIMITER '|');
````

