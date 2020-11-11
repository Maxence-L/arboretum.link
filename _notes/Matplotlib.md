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

 Bien que l'on puisse uti


