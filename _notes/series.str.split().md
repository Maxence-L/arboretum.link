---
title: series.str.split()
tags: pandas
etat: printemps
---

.str.split() agit sur les données de format *string* en les séparant selon une clé spécifiée. 

**La méthode s'appelle sur les series** et non les [[dataframe\|dataframes]]. Pour l'utiliser lorsque l'objet est un dataframe, il est nécessaire d'utiliser l'index pour que l'objet sur lequel str.split est appelé soit une series.

La clé par défaut est ' ' :

```python
s = pd.Series("I am a man",
				"I am a woman")
				
s.str.split()

[I, am, a, man]
[I, am, a, woman]
```

Avec une clé différente :

```python
s = pd.Series("I am a man",
				"I am a woman")
				
s.str.split('a')

[I am, man]
[I am, woman]
```

Si l'on précise l'option *expand=True*, la méthode génère un Dataframe avec chaque élément séparé dans une colonne :

```python
s = pd.Series("I am a man",
				"I am a woman")
				
s.str.split('a', expand = True)

1		2
I am	man
I am 	woman
```
