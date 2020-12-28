---
title : Types de données SQL
tags : SQL
etat : été
toc : true
---

Cette note a pour but d'expliquer les types de données rencontrée dans un tableau.

## String

Les données composées de caractères conviennent pour du texte, des nombres ou des symboles. Les types 'caractères' incluent :

#### char(*n*)
Chaque observation aura une longueur *n* fixe. Si le contenu ne remplis pas la longueur complêtement, le reste sera rempli avec des espaces. C'est un bon format si l'on veut stocker des données à longueur fixe (comme un code postal, par exemple)

#### varchar(*n*)
Les observations possèdent une longueur variable de taille *n* maximum. Cela permet d'économiser de l'espace.

#### text
Un type comprenant une longueur variable de caractères, sans limite de taille (maximum 1 Go dans PostgreSQL). Ce type n'est pas compris dans SQL standard, mais on le retrouve dans MySQL ou Microsoft SQL.

> A noter : Le choix de variable caractère n'affecte pas la performance de PostgreSQL.

## Nombres

Les types "nombres" sont considérés comme des nombres et peuvent donc être utilisés pour réaliser des calculs, ce qui est assez pratique.

#### integer
Correspond à des nombres entiers. Trois types sont disponibles, selon la taille du nombre stocké :

|Type de données|Taille de stockage|Intervalle|
|--|-----|---|
|`smallint`|2 octets|−32768 à +32767|
|`integer`|4 octets|−2147483648 à +2147483647|
|`bigint`|8 octets|−9223372036854775808 à +9223372036854775807|

`bigint` prend un peu plus de place mais est le plus fiable.


#### Nombres auto-incrémentés

Les valeurs des colonnes de type sériel (*serial type*) sont auto-incrémenés à chaque fois que l'on rajoute une ligne. Cela en fait de bons choix pour obtenir des colonnes d'identification des lignes.

Exemple de déclaration de colonne sérielle :

```SQL
CREATE TABLE people (
	id serial,
	person_name varchar(100)
);
````

A chaque fois qu'une valeur `person_name` est ajoutée à la table, la colonne `id` s'incrémentera de 1.

Les types sériels disponibles :

|Type de données|Taille|Intervalle|
|-|-|-|
|smallserial|2 octets|1 to 32767|
|serial|4 octets|1 to 2147483647|
|bigserial|8 octets|1 to 9223372036854775807|

### Nombres décimaux

Ils correspondent aux nombres non-entiers dotés d'une virgule. Celles-ci peut être flottante (*floating-point*) ou fixe (*fixed-point*).

#### Virgule fixe
Le type de nombre à virgule fixe (aussi appelé à précision arbitraire -*arbitrary precision*) est défini par `numeric(precision, scale)` ou `decimal(precision, scale)`.

- `precision` donne le nombre maximum de chiffres au total dans le nombre.
- `scale` donne le nombre maximum de chiffres après la virgule (par défaut 0).

Sans précision, SQL enregistrera les chiffres avec la *précision* et la *scale* au maximum possible.

#### Virgule flottante

Les types de nombres à virgule flottante sont aussi appelés à *précision variable*. Deux types existent, différent selon la précision voulue :

- `real` : 6 chiffres dans le nombre
- `double precision` : 15 chiffres dans le nombre

Attention toutefois : les opérations sur les nombres à virgule flottante sont [inexactes](https://docs.oracle.com/cd/E19957-01/806-3568/ncg_goldberg.html) lorsque répétées un grand nombre de fois, du fait des problèmes d'arrondi.

Les types décimaux disponibles sont les suivants :

|Types de données|Taille|Type de stockage|Intervalle maximum|
|-|-|-|
|numeric, decimal|variable|virgule fixe|Jusqu'à 131072 chiffres avant la virgule et 16383 après|
|real|4 octets|virgule flottante|6 chiffres dans  le nombre|
|double precision|8 octets|virgule flottante|15 chiffres dans le nombre|

Conseils pour l'usage de type "nombre" :

- Toujours utiliser `integer` lorsque c'est possible

- Si les calculs doivent être exacts (dans le cas de transactions financières par example), utiliser `numeric`ou `decimal`, avec une précision suffisamment grande.

- Si la base contient moins d'un million de lignes, toujours utiliser un grand format (`bigint`) par exemple).

## Date et heure

Avoir un format temporel dédié permet de faire de manipuler les dates de manière aisée sans avoir à se préoccuper des problème de calendrier (années bissextiles par exemple) ou de zonage.

Les types suivants sont disponibles :

- `timestamp`: Enregistre l'heure et la date. Il est préférable d'ajouter `WITH TIME ZONE` afin de pouvoir comparer des observations entre elles.
- `date` : Enregistre seulement...la date.
- `time` : Enregistre l'heure. A utiliser `WITH TIME ZONE`.
- `interval` : Permet de représenter une unité de temps sous la forme d'une quantité. N'enregistre pas le début ou la fin, seulement l'intervale entre les deux.

Exemple :

```SQL
CREATE TABLE date_time_types  
(  
    timestamp_column timestamp with time zone,  
 	interval_column interval  
);  
  
INSERT INTO date_time_types  
VALUES  
 ('2018-12-31 01:00 EST','2 days'),  
 ('2018-12-31 01:00 -8','1 month'),  
 ('2018-12-31 01:00 Australia/Melbourne','1 century'),  
 (now(),'1 week');  
  
SELECT * FROM date_time_types;
````

|timestamp_column|interval_column|
|-|-|
|2018-12-31 06:00:00.000000|0 years 0 mons 2 days 0 hours 0 mins 0.00 secs|
|2018-12-31 09:00:00.000000|0 years 1 mons 0 days 0 hours 0 mins 0.00 secs|
|2018-12-30 14:00:00.000000|100 years 0 mons 0 days 0 hours 0 mins 0.00 secs|
|2020-12-28 01:17:51.457367|0 years 0 mons 7 days 0 hours 0 mins 0.00 secs|

## Divers

- `boolean` : `True`ou `False``
- `geometry` ou `geography` permet d'enregistrer des coordonnées géométriques (vecteurs...) ou géographiques.
- `JSON` - [pour les JSON](https://stackoverflow.com/questions/9207404/whats-best-sql-datatype-for-storing-json-string)

