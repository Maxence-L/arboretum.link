---
title : SELECTionner des données SQL
tags : SQL
etat : hiver
toc : true
---

## Introduction
### Obtenir l'intégralité du tableau
La commande `SELECT` permet de sélectionner les données d'un tableau. L'instruction la plus simple retournant l'intégralité d'un tableau :

```SQL
SELECT * FROM my_table;
````

- Le nom du tableau est précisé avec le mot-clé `FROM`
- L'opérateur `*` retourne tous les éléments disponibles.

### Obtenir un sous-ensemble des colonnes du tableau
On remplace `*`par le nom des colonnes qui nous intéresse :

```SQL
SELECT some_column, another_column, amazing_colum FROM table_name;
````

## Ordonner le résultat de la sélection

On donne un ordre à la section avec la commande `ORDER BY col`, exprimée après la commande `SELECT`. Par défaut, l'ordre est ascendant, mais l'on peut préciser `DESC` si l'on désire un ordre descendant.

```SQL
SELECT first_name, last_name, salary
FROM teachers
ORDER BY salary DESC;
`````

| first_name | last_name | salary |
|------------|-----------|--------|
| Lee        | Reynolds  | 65000  |
| Samuel     | Cole      | 43500  |
| Betty      | Diaz      | 43500  |
| Kathleen   | Roush     | 38500  |
| Janet      | Smith     | 36200  |
| Samantha   | Bush      | 36200  |

### Quel ordre est donné aux variables textuelles ?
L'ordre dépend des paramètres linguistiques du serveur (le paramètre *locale*) et le jeu de caractère utilisé (UTF-8, par exemple). On peut consulter ces paramètres avec la commande `SHOW ALL` et inspecter la valeur `lc_collate`.

Pour le jeu UTF-8, l'ordre est le suivant :
1. Points, parenthèses et opérateurs mathématiques
2. Nombre de 1 à 9
3. Ponctuation supplémentaire (comme `?` par exemple)
4. Lettres capitales de A à 7
5. Symboles de ponctuation moins courants, comme les crochets ou `_`
6. Lettres minuscules de a à z
7. Symboles de ponctuation additionnels, charactères spéciaux, alphabet étendu

Par conséquent, le mot `Arboretum`sera devant le mot `arboretum` en ordre descendant.

## Filtrer les lignes de la sélection

On ajoute une ou plusieurs conditions relatives aux lignes à afficher avec la commande `WHERE` :

```SQL
SELECT last_name, school, hire_date
FROM teachers
WHERE school = 'Myers Middle School';
````

| last_name | school              | hire_date  |
|-----------|---------------------|------------|
| Cole      | Myers Middle School | 2005-08-01 |
| Bush      | Myers Middle School | 2011-10-30 |
| Diaz      | Myers Middle School | 2005-08-30 |

Les opérateurs suivants sont disponibles :

| Opérateur | Fonction                                         | Exemple                              |
|-----------|--------------------------------------------------|--------------------------------------|
| =         | égal à                                           | WHERE school ='Baker Middle'         |
| <> or !=  | non égal à                                       | WHERE school <> 'Baker Middle'       |
| >         | Supérieur à                                      | WHERE salary > 20000                 |
| <         | Inférieur à                                      | WHERE salary < 60500                 |
| >=        | Supérieur ou égal à                              | WHERE salary >= 20000                |
| <=        | Inférieur ou égal à                              | WHERE salary <= 60500                |
| BETWEEN   | Dans l'intervalle                                | WHERE salary BETWEEN 20000 AND 40000 |
| IN        | Apparie un des valeurs d'un jeu                  | WHERE last_name IN ('Bush',"'Roush') |
| LIKE      | Apparie selon un modèle                          | WHERE first_name LIKE ' Sam%'        |
| ILIKE     | Apparie selon un modèle  (insensible à la casse) | WHERE first_name ILIKE sam%'         |
| NOT       | Inverse d'une condition                          | WHERE first_name NOT ILIKE sam%'     |

Un exemple :
```SQL
SELECT first_name, last_name, school
FROM teachers
WHERE first_name = Janet';
````

On obtient en résultat tous les professeurs dont le prénom commence par 'Janet'.

```SQL
SELECT first_nane, last_name, hire_date
FROM teachers
WHERE hire_date < 2000-01-01';
```

On obtient tous les professeurs embauchés après le 1er janvier 2000.
```SQL
SELECT first_name, last_name, school, salary
FROM teachers
WHERE salary BETWEEN 40000 AND 65000;
````

On obtient tous les professeurs dont les salaires sont entre 40K et 65K.

> A noter :  `BETWEEN` est inclusif. `BETWEEN 40000 AND 65000`est donc équivalent à $[40000, 65000]$.

### Filtrer par correspondance incomplète

Les opérateurs `LIKE` et `ILIKE` permettent de passer des opérateurs de filtrage similaires au [[regex]].

> A noter : `LIKE` est sensible à la casse, contrairement à `ILIKE`. Ce dernier est toutefois spécifique à PostgreSQL.

Deux opérateurs sont disponibles :

- Pourcentage (`%`) : Apparie un ou plusieurs caractères supplémentaires. `LIKE 'c%'` appariera `croissant` ou `courtisant`. `LIKE '%tion%'` appariera `sélectionner` ou `attentionné`.

- [Trait bas](http://gdt.oqlf.gouv.qc.ca/ficheOqlf.aspx?Id_Fiche=9472174) (`_`) : Apparie un seul caractère. `LIKE '_ach_'` appariera `Vache` et `Hache` par exemple mais pas `Vaches` ou `Haches`.

Exemple d'utilisation avec `WHERE` :
```SQL
SELECT first_name
FROM teachers
WHERE first_name LIKE 'sam%';
````

### Opérateurs AND et OR
Il est possible de combiner des filtres avec les conditions `AND` et `OR`.

```SQL
SELECT * FROM teachers
WHERE school = 'Myers Middle School'
		AND salary < 40000;

SELECT * FROM teachers
WHERE last_name = 'Cole'
		OR last_name = 'Bush';

SELECT * FROM teachers
WHERE school = 'F.D. Roosevelt HS'
	AND (salary < 38000 OR salary > 40000);
````

> A noter : Il faut énoncer une condition à la fois. C'est fastidieux, surtout si l'on a plusieurs valeurs à sélectionner.

### Retirer les doublon

On utilise le mot-clé `DISTINCT` associé aux colonnes qui nous intéressent. Cette fonction est utile pour comprendre les valeurs possibles d'une variable, par exemple.

```SQL
SELECT DISTINCT school
FROM teachers;
````

Le résultat est le suivant :

| school              |
|---------------------|
| F.D. Roosevelt HS   |
| Myers Middle School |

On peut aussi appliquer `DISTINCT` à plusieurs colonnes ; les lignes retournées correspondent aux paires uniques.

```SQL
SELECT DISTINCT school, salary
FROM teachers:
````

| school              | salary |
|---------------------|--------|
| Myers Middle School | 43500  |
| Myers Middle School | 36200  |
| F.D. Roosevelt HS   | 65000  |
| F.D. Roosevelt HS   | 38500  |
| F.D. Roosevelt HS   | 36200  |
