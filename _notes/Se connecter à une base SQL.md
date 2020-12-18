---
title: Se connecter à une base SQL
tags: sql
etat: printemps
toc: true
---
Il est possible de se connecter à une base de données [[SQL]] avec le package [[sqlite3]] ou [[sql alchemy]].

## SQLite

Dans le cas de [[sqlite3]], associé à une base [[SQLite]]:

```python
import sqlite3
# connect to the database
db_name = sqlite3.connect('population_data.db')
```

Pour interagir avec la base, on doit créer un objet 'curseur' et ensuite spécifier les requêtes à la base au moyen de la méthode .execute() :

```python
c = db_name.cursor()
c.execute("DROP TABLE IF EXISTS gdp")
```

Lorsque les modifications nécessaires ont été réalisées, on enregistre (commit) les changements :

```python
db_name.commit()
````

Et on ferme la base SQLite :

```python
my_db.close()
```

Plus :
https://docs.python.org/3/library/sqlite3.html

## SQL

Si l'on utilise une base de données SQL comme [[MySQL]] ou [[PostgreSQL]], on utilisera le package [[sql alchemy]] pour se connecter à la base. Cette connexion se fait au moyen de la fonction [[create_engine()]] :

```python
from sqlalchemy import create_engine
db_name = create_engine('data.db')
```

Noter que l'on peut se connecter à une base SQLite avec SQL Alchemy.

## Charger une base dans un objet pandas

Pandas possède une fonction [[pd.read_sql()]] permettant d'effectuer des requêtes SQL :

```python
# run a query
pd.read_sql('SELECT * FROM population_data', db_name)
```

On peut téléverser des données à une base de données [[SQL]] ou [[sqlite3]]au moyen de [[df.to_sql()]]
```python
df.to_sql(name='my_table', con=cxtn, if_exists='replace', index=False)
````
