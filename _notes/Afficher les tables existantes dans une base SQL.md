---
title: Afficher les tables existantes dans une base SQL
tags: SQL
etat: printemps
---

Dans le cas d'[[SQLite]], avec le module [[SQLite3]] :


```python
    def show_tables(connection_object):
        script = """
					SELECT name FROM sqlite_master 
                    WHERE type ='table' 
					AND name NOT LIKE 'sqlite_%';
				 """
				 
        connection_object.execute(script)
        print(connection_object.fetchall())
````

Les noms des tables sont stockés dans `sqlite_master` sous la colonne `name`. 

On peut donc les requêter et obtenir le résultat avec `.fetchall()` appelé sur un objet `.connection()`