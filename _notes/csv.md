---
title: csv
tags: stdlib
etat : hiver
---

Les fichiers *.csv* correspondent à des fichiers texte représentant des données sous forme tabulaire. Les colonnes sont séparées par des caractères de ponctuation et les lignes par des retours à la ligne.

Un en-tête (*header*) peut être spécifié en première ligne afin de donner le nom des colonnes. 

On peut généralement spécifier un caractère différent de la virgule : le point-virgule (souvent le cas pour Excel), une tabulation (référencé \t dans les strings Python) ou deux points (:, courant pour Linux), par exemple.

Un code simple de création de csv :
```python
def create_db(self):
	if os.path.isfile(self.filename):
    	print("A file with the same name already exists.")
        print("The data collected will be appended to it")
        # We append the file so that we do no lose previous data.
        # Duplicates can be dealt after.
    csv_file = open(self.filename, 'a+',newline='')
    try:
        writer = csv.writer(csv_file)
        writer.writerow(self.header)
     finally:
         pass
````

Si l'on utilise le package [[pandas]], on peut ouvrir des fichiers .csv avec la fonction `pd.read_csv(filename)`:

```python
import pandas as pd 
data = pd.read_csv("filename.csv") 
````

Des [options](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.read_csv.html) sont disponibles pour définir des paramètres d'importation (séparateur, header, etc...).