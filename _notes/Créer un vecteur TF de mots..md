---
title: Créer un vecteur TF de mots
tags: NLP ml-pratique
etat: hiver
---

Une fois la [[tokenisation]] et la lemmatisation du texte réalisée, on peut passer à son éventuelle vectorisation avant analyse. Pour cela, on va simplement conter le nombre de mots dans le texte et reporter le résultat dans un vecteur dont chaque coordonnée correspondra à un mot.

## L'enregistrement de la matrice TF sous forme de ligne creuse compressée (*Compressed Sparse Row - CSR*)

Si l'on analyse plusieurs textes (ce qui est généralement le cas), on aura alors une grande matrice, où chaque colonne correspondra à un mot et chaque ligne à un texte comparé.

Cela donne une [[matrice creuse::https://en.wikipedia.org/wiki/Sparse_matrix]] (*sparse matrix*) où la plupart des éléments égaux à 0. Un exemple d'une telle matrice :

$$
\left(\begin{array}{cccccc}
10 & 20 & 0 & 0 & 0 & 0 \\
0 & 30 & 0 & 40 & 0 & 0 \\
0 & 0 & 50 & 60 & 70 & 0 \\
0 & 0 & 0 & 0 & 0 & 80
\end{array}\right)
$$

Le stockage se ferait au format *[[Manipuler les formats longs et larges de données\|long]]* :

```
V         = [ 10 20 30 40 50 60 70 80 ]
COL_INDEX = [  0  1  1  3  2  3  4  5 ]   
ROW_INDEX = [  0  2  4  7  8 ]
````

Bien qu'il soit facile de créer une matrice TF pour ensuite la convertir au format *CSR*, [[sklearn]] s'en charge pour nous, avec la classe `CounVectorizer`du module `sklearn.feature_extraction.text` :

Importation :
```python
from sklearn.feature_extraction.text import CountVectorizer
vectorizer = CountVectorizer()
````

Utilisation[^1] :
```python
tf_matrix = vectorizer.fit_transform(newsgroups.data)
print(tf_matrix)

> (0, 108644)       4
(0, 110106)     1
(0, 57577)      2
(0, 24398)      2
(0, 79534)      1
(0, 100942)     1
(0, 37154)      1
(0, 45141)      1
(0, 70570)      1
(0, 78701)      2
(0, 101084)     4
(0, 32499)      4
(0, 92157)      1
(0, 100827)     6
(0, 79461)      1
(0, 39275)      1
(0, 60326)      2
(0, 42332)      1
(0, 96432)      1
(0, 67137)      1
(0, 101732)     1
(0, 27703)      1
(0, 49871)      2
(0, 65338)      1
(0, 14106)      1
:       :
(11313, 55901)  1
(11313, 93448)  1
...
````

```python
print(type(tf_matrix))

> <class 'scipy.sparse.csr.csr_matrix'>
````

On voit que sklearn renvoie un objet de type `csr_matrix`, qui correspond à une matrice creuse. Il est recommandé, pour éviter les problèmes de compatibilité, de la convertir au format [[array]] :

```python
tf_np_matrix = tf_matrix.toarray()
print(tf_np_matrix)

> [ [0 0 0 ... 0 0 0]
 [0 0 0 ... 0 0 0]
 [0 0 0 ... 0 0 0]
 ...
 [0 0 0 ... 0 0 0]
 [0 0 0 ... 0 0 0]
 [0 0 0 ... 0 0 0] ]
  ````





[^1]: Exemple tiré de *Data Science Bookcamp*, de Leonard Apelstin, utilisant le `newsgroup` dataset (`from sklearn.datasets import fetch_20newsgroups`)