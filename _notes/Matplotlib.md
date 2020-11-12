---
title: matplotlib
tags : Visualisations
etat : hiver
---
Matplotlib est une bilbliothèque de visualisation de données qui s"intègre avec Pandas et d'autres bibliothèques, comme Seaborn.

Cet excellent article du blog Practical Business Python en fait un bonne porte d'entrée dans la bibliothèque : [Effectively Using Matplotlib](https://pbpython.com/effective-matplotlib.html)

## Installation

Le package s'appelle `matplotlib.pyplot` et s'écrit `plt` habituellement.

```python
import pandas as pd
import matplotlib.pyplot as plt
````

## Utilisation

### Introduction
Un graphique `plt` est créé à partir de deux types d'éléments : 

- L'élément `Axes` représente le graphique et comprend tous les sous-éléments, comme les axes x et y, les points, la légende, le titre, etc.

- L'élément `figure` représente l'image finale. Il sera utile pour déterminer la taille de l'image et le nombre d'éléments `Axes` compris dans le graphique.

Les dénominations des éléments d'un graphique sont les suivantes :

![](../assets/img/anatomy-of-a-figure.png#center)

On initialise les éléments `figure` et `axes` avec la méthode `plt.subplots()` :

```python
fig, ax = plt.subplots()
````

Cela nous permet de customiser les graphiques générés par la méthode `.plot` d'un dataframe. C'est particulièrement pratique, car on peut faire un petit graphique "pour voir" et le nettoyer/l'améliorer rapidement sans avoir à tout réécrire :

````python
fig, ax = plt.subplots()
top_10.plot(kind='barh', y="Sales", x="Name", ax=ax)
ax.set_xlim([-10000, 140000])
ax.set_xlabel('Total Revenue')
ax.set_ylabel('Customer');
````

### Style

Des styles prédéfinis sont disponibles. on peut en consulter la liste avec `plt.style.available` ou voir la gallerie sur le site de [matplotlib](https://matplotlib.org/3.1.1/gallery/style_sheets/style_sheets_reference.html).

Pour en utiliser un, il suffit d'utiliser la ligne suivante :

```python
plt.style.use('ggplot')
```

Un tuto est disponible sur le site de matplotlib, portant sur la modification du style des graphiques : [Customizing Matplotlib with style sheets and rcParams](https://matplotlib.org/3.3.2/tutorials/introductory/customizing.html).





