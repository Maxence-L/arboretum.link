---
title: df.to_sql()
tags: pandas
etat: printemps
---
Transfère un [[dataframe]] vers une base de données [[SQL]].

La syntaxe est la suivante :

```python
cxtn = sqlite3.connect('my_db')

df.to_sql(name='my_table', con=cxtn, if_exists='replace', index=False)
```

Dans l'exemple ci-dessus, si la base de données 'my_db' n'existe pas, sqlite3.connect() la créera pour nous.

L'option *if_exists=* spécifie le comportement si une table au même nom existe dans la base de données spécifiée.

*'replace'* : Si la table existe déjà dans la base de données, le téléversement l'écrase avant de replacer les valeurs. 
*'append'* : les valeurs de 'my_table' sont ajoutées à l'existante
*'fail'* : retourne un *Value Error*.

L'option *index=* permet de spécifier si l'on veut créer une colonne pour y stocker l'index du dataframe.

Pour plus d'options de format :
https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.to_sql.html?highlight=to_sql#pandas.DataFrame.to_sql