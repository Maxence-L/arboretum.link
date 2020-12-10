---
title : df.drop() - retirer une colonne d'un dataset
tags : pandas
etat :hiver
---

Méthode permettant de retirer une colonne d'un dataset. En pratique, on peut s'en servir pour séparer un jeu de données entre variable objectif et variables explicatives.

La syntaxe est la suivante : `df.drop(labels, axis=(0: ligne, 1: colonne, défaut 0), inplace ...)`

```python
df = pd.DataFrame(np.arange(12).reshape(3, 4),
                  columns=['A', 'B', 'C', 'D'])
				  
   A  B   C   D
0  0  1   2   3
1  4  5   6   7
2  8  9  10  11

df.drop(['B', 'C'], axis=1)

   A   D
0  0   3
1  4   7
2  8  11
````
