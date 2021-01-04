---
title : Clés et contraintes dans SQL
tags : SQL
etat : hiver
toc : true
---

## Clés primaires
Une *clé primaire* correspond à une colonne ou une série de colonnes dont la valeur est unique et identifie chaque ligne du tableau.

Cette *clé primaire* est une *contrainte* et impose deux rêgles à la (ou aux) colonne(s) qui représentent la clé :

- Chaque clé doit avoir une valeur unique pour chaque ligne
- Aucune valeur de la clé ne peut être nulle.

Les clés primaires permettent de garantier l'intégrité relationnelle (*referential integrity*) : on s'assure ainsi qu'il est possible d'appairer les observations de différentes tables.

Il est possible d'utiliser une colonne déjà existante (*clé naturelle*) ou d'en créer une (*clé synthétique*). Une création classique de clé synthétique consiste à ajouter toutes les valeurs de la ligne en une seule colonne. 

Par exemple pour un tableau récapitulant les modèles de voitures avec les colonnes *marque, modèle, puissance, couleur*, on aura la clé synthétique *RenaultTwingo100cvRouge*.

### Déclaration de clé primaire
#### Clé primaire naturelle simple (une seule colonne)

Il existe deux manières de déclarer une clé primaire simple :

Contrainte attachée à une colonne :

```SQL
CREATE TABLE natural_key_example (
	license_id varchar(10) CONSTRAINT license_key PRIMARY KEY ,
	first_name varchar(50),
	last_name varchar(50)
);
````

On aura alors la clé appelée `license_key` qui se basera sur la colonne `license_id`.

Contrainte attachée à une table :

```SQL
CREATE TABLE natural_key_example (
	license_id varchar(10),
	first_name varchar(50),
	last_name varchar(50),
	CONSTRAINT license_key PRIMARY KEY (license_id)
);
````

Le code suivant renvoie désormais une erreur :

```SQL
INSERT INTO natural\_key\_example (license\_id, first\_name, last\_name)  
VALUES ('T229901', 'Lynn', 'Malero');  
  
INSERT INTO natural\_key\_example (license\_id, first\_name, last\_name)  
VALUES ('T229901', 'Sam' ,'Tracy');
````

````
ERROR: duplicate key value violates unique constraint "license_key"
Détail : Key (license_id)=(T229901) already exists.
````

#### Clé primaire naturelle composite

On utilise la déclaration suivante :

```SQL
CREATE TABLE natural_key_composite_example (
	student_id varchar(10),
	school_day date,
	present boolean,
	CONSTRAINT student_key PRIMARY KEY (student_id, school_day)
);
````

Comme on le voit il suffit de rajouter des colonnes entre parenthèses lors de la délcaration `CONSTRAINT`.


#### Clé primaire synthétique s'auto-incrémentant

Les [[Types de données SQL\|types]] `serial` (`smallserial, serial,bigserial`) sont utiles pour créer des clés primaire s'auto-incrémentant à mesure que l'on insère des données dans le tableau. Il faut alors les utiliser pour une colonne et les déclarer comme clé primaire :

```SQL
CREATE TABLE surrogate_key_example (
    order_number bigserial,
    product_name varchar(50),
    order_date date,
    CONSTRAINT order_key PRIMARY KEY (order_number)
);

INSERT INTO surrogate_key_example (product_name, order_date)
VALUES  ('Beachball Polish', '2015-03-17'),
        ('Wrinkle De-Atomizer', '2017-05-22'),
        ('Flux Capacitor', '1985-10-26');
SELECT * FROM surrogate_key_example;
````

| order\_number | product\_name | order\_date |
| :--- | :--- | :--- |
| 1 | Beachball Polish | 2015-03-17 |
| 2 | Wrinkle De-Atomizer | 2017-05-22 |
| 3 | Flux Capacitor | 1985-10-26 |


## Clés étrangères (*foreign keys*)

Les clés étrangères imposent la contrainte de devoir exister dans une autre table que celle où elle est déclarée. Cela permet d'éviter les données orphelines sans relation avec les lignes des autres tableaux de notre base.

### Déclaration de clé étrangère

Application :

```SQL
CREATE TABLE licenses (
	license_id varchar(10),
	first_name varchar(50),
	last_name varchar(50),
	CONSTRAINT licenses_key PRIMARY KEY (license_id)
) ;

CREATE TABLE registrations (
	registration_id varchar(10),
	registration_date date,
	license_id varchar(10) REFERENCES licenses (license_id),
	CONSTRAINT registration_key PRIMARY KEY (registration_id, license_id)
);
````

Le mot-clé `REFERENCES` permet, lors de la déclaration de la colonne, de la lier avec une clé étrangère, en l'occurence, `licenses_key`, dérivée de `license_id`.

Test :

```SQL
INSERT INTO licenses (license_id, first_name, last_name)
VALUES ('T229901' , 'Lynn', 'Malero');

INSERT INTO registrations (registration_id, registration_date, license_id)
VALUES ('A203391', '3/17/2017', 'T229901');

INSERT INTO registrations (registration_id, registration_date, license_id)
VALUES ('A75772', '3/17/2017', 'T000001');
````

```
[23503] ERROR: insert or update on table "registrations" violates foreign key constraint "registrations_license_id_fkey" 
Détail : Key (license_id)=(T000001) is not present in table "licenses".
````

### `CASCADE` : Suppression automatique des lignes apparentées

Lors de la suppression d'une ligne dans un tableau-parent, il est possible de supprimer automatiquement toutes les lignes dans le tableau-enfant. On utilise pour cela le mot-clé `CASCADE` lors de la déclaration de la colonne apparentée du tableau-enfant :

```SQL
CREATE TABLE registrations_child
(
    registration_id   varchar(10),
    registration_date date,
    license_id        varchar(10) REFERENCES licenses (license_id) ON DELETE CASCADE,
    CONSTRAINT registration_key PRIMARY KEY (registration_id, license_id)
);
````

## Contraintes

### `CHECK`

La contrainte `CHECK` permet de vérifier si la valeur respecte une condition attendue. En l'occurence, on utilise un [[SELECTionner des données SQL\|test logique]]. Au cas où le critère de serait pas rempli, la base de donnée retourne une erreur.

`CHECK (condition)`  se déclare lors de la création de la colonne comme une contrainte :

```SQL
CREATE TABLE check_constraint_example (
    user_id bigserial,
    user_role varchar(50),
    salary integer,
    CONSTRAINT user_id_key PRIMARY KEY (user_id),
    CONSTRAINT check_role_in_list CHECK (user_role IN('Admin', 'Staff')),
    CONSTRAINT check_salary_not_zero CHECK (salary > 0)
);
````

### `UNIQUE`

`UNIQUE` est similaire à une contrainte de type clé : chaque valeur de la colonne doit être unique. Toutefois, il est possible que la colonne contienne des valeurs `NULL`, contrairement à une colonne ayant une contrainte de type "clé".

```SQL
CREATE TABLE unique_constraint_example (
    contact_id bigserial CONSTRAINT contact_id_key PRIMARY KEY,
    first_name varchar(50),
    last_name varchar(50),
    email varchar(200),
    CONSTRAINT email_unique UNIQUE (email)
) ;
````

```SQL
INSERT INTO unique_constraint_example (first_name, last_name, email)
VALUES ('Samantha', 'Lee', 'slee@example.org');

INSERT INTO unique_constraint_example (first_name, last_name, email)
VALUES ('Betty', 'Diaz', 'bdiaz@example.org');

INSERT INTO unique_constraint_example (first_name, last_name, email)
VALUES ('Sasha', 'Lee', 'slee@example.org');
````

````
[23505] ERROR: duplicate key value violates unique constraint "email_unique" Détail : Key (email)=(slee@example.org) already exists.
`````

### `NOT NULL`

`NOT NULL` force les valeurs insérée dans la colonne contraintes à ne pas avoir la valeur nulle.

```SQL
CREATE TABLE not_null_example (
student_id bigserial,
first_name varchar(50) NOT NULL,
last_name varchar(50) NOT NULL ,
CONSTRAINT student_id_key PRIMARY KEY (student_id)
);
```

## Ajouter et retirer des contraintes après création du tableau

On utilise la commande `ALTER TABLE` pour modifier les contraintes d'un tableau. Les colonnes concernées doivent respecter la contrainte pour qu'elle soit ajoutée.

### Suppression de contrainte
Pour supprimer une clé (étrangère ou primaire) ou une contrainte de type `UNIQUE`, on utilise la commande suivante : `ALTER TABLE table_name DROP CONSTRAINT constraint_name;`

Par exemple :

```SQL
ALTER TABLE not_null_example DROP CONSTRAINT student_id_key;
````

Pour supprimer une contrainte `NOT NULL` :

```SQL
ALTER TABLE table_name ALTER COLUMN column_name DROP NOT NULL;
````

### Ajout de contrainte

Pour une contrainte de type "clé" :

```SQL
ALTER TABLE not_null_example ADD CONSTRAINT student_id_key PRIMARY KEY (student_id);
````

Pour une contrainte autre :
```SQL
ALTER TABLE not_null_example ALTER COLUMN first_name SET NOT NULL;
````

