---
title: sqlite3
tags: sql
etat: printemps
---

Sqlite3 est un module python donnant accès à la base de données [[SQLite]]. Il est compris dans la [[Standard Library]] et ne nécessite donc pas de téléchargement.

L'aide standard : https://docs.python.org/3/library/sqlite3.html
Un bon tuto : https://www.sqlitetutorial.net/sqlite-python/

## Connexion à une base SQLite3

Il faut d'abord connecter une base de données SQLite au programme que l'on exécute. Si aucune base ne correspond au nom spécifié, SQLite créera automatiquement une nouvelle base de données.

On crée un objet de type `Connection` avec `.connect('data_name.db')` :

```python
import sqlite3
# connexion à la base
db_name = sqlite3.connect('population_data.db')
```

Si l'on veut sauvegarder le fichier dans un dossier particulier, il faut définir le chemin d'accès complet :
```python
db_name = sqlite3.connect('/Users/my-comp/population_data.db')
```

Une initialisation simple d'une classe exploitant une base SQLite :
```python
class dataBase:
    """This class is used connect to a sqlite db and store the data"""
    def __init__(self, filename, header):
        self.filename = filename
        self.header = header

        if os.path.isfile(self.filename):
            print("A database with the same name as specified exists and will be used")
        else:
            print("A new database will be created")
            
        self.connection = sqlite3.connect(filename)
        self.c = self.connection.cursor()
````

## Interaction
Pour interagir avec la base, on doit créer un objet de type 'curseur' à partir de l'objet-base et ensuite spécifier les requêtes à la base au moyen de la méthode .execute() :

```python
c = db_name.cursor()
c.execute("DROP TABLE IF EXISTS gdp")
```

Lorsque les changements nécessaires ont été réalisés, on enregistre (commit) les changements :
```python
db_name.commit()
````

## Fermeture de la base
On ferme la base SQLite :

```python
my_db.close()
```

