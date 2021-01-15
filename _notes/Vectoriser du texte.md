---
title: Vectoriser le texte
tags: NLP ml-pratique
etat: automne
toc : True
---

## Introduction
L'apprentissage machine exploitant des variables numériques, il est nécessaire de convertir un texte à analyser en une représentation vectorielle. Cette représentation contient le contenu du texte, mais peut aussi comprendre des propriétés comme la longueur, l'auteur la source ou la date de publication.

Les textes peuvent être ainsi analysés en comparant leur représentation vectorielle à d'autres et en obtenir une mesure de [[Similarité\|similarité]] sous forme de distance.

De nombreuses méthodes existent. Parmi les plus simples on a :

- Fréquence lexicale
- One-hot-encoding
- TF-IDF : *Term Frequency–Inverse Document Frequency* (fréquence lexicale, fréquence inverse textuelle)

D'autres approches exploitant les réseaux de neurones existent, appelées "représentations distribuées[^1]" (*distributed representations*). Elles se fondent sur le contexte ou la similarité sémantique entre les mots.

![context_vector](/assets/img/context_vector.png#center)
<div align="center">
	<p> <i>
Un vecteur contextuel issu d'un vecteur lexical.  <a href="https://www.aclweb.org/anthology/W06-2501.pdf">Source.</a></i>
	</p>
</div>


[^1]: https://hal.archives-ouvertes.fr/tel-02504543/document

### Présentation des techniques

| Technique de vectorisation      | Fonction                                           | Utile pour                           | Remarques                                                                        |   |
|-----------------------------|----------------------------------------------------|-------------------------------------|---------------------------------------------------------------------------------------|---|
| Fréquence lexicale             | Comptage de la fréquence                            | Modèles bayésiens                    | Les mots les plus fréquents ne sont pas nécessairement les plus informatifs                                  |   |
| One-Hot Encoding            | Binarisation de l'occurence d'un mot (0, 1)                   | Réseaux de neurones                     | Tous les mots sont équidistants, ce qui rend la normalisation importante                               |   |
| TF–IDF                      | Normalise les fréquences lexicales entre les documents      | Flexible                     | Des mots moyennement fréquents ne sont pas nécessairement représentatifs du sujet du texte                |   |
| Représentations distribuées | Codage fondé sur le contexte ou la similarité entre les mots | Modéliser des relations complexes | Très gourmands en performance et nécessitent des outils supplémentaires (Tensorflow) pour analyser des larges volumes|   |


## La préparation de l'analyse avec le bag-of-words

L'approche *Bag-of-words* consiste à attribuer à chaque document un vecteur de longueur égale à la taille du vocabulaire du corpus de textes analysés. Pour simplifier le calcul, on peut ordonner la liste selon l'ordre alphabétique ou créer un dictionnaire reliant chaque mot à sa position dans une liste.

![](/assets/img/bag-of-word.png#center)

<div align="center">
	<p>
  Source : <a href="https://https://www.oreilly.com/library/view/applied-text-analysis/9781491963036/ch04.html">Applied Text Analysis with Python</a>
	</p>
</div>

## Quels packages sont disponibles ? 

- NLTK : Créé pour les données textuelles, mais important en terme d'espace disque
- Scikit-Learn : Moins flexible, plus rapide et plus léger
- Gensim
- [[Spacy]] : Facile d'utilisation, plutôt rapide mais moins flexible.

Les exemples inclus ici utilisent scikit-learn, mais on peut aller voir [cet article](https://www.oreilly.com/library/view/applied-text-analysis/9781491963036/ch04.html) pour plus d'exemples.

## Vectorisation fréquentielle

La vectorisation la plus simple : on attribue à chaque élément du vecteur une valeur égale au nombre de récurrences dans le texte du mot correspondant. On peut soit garder des nombres entiers soit [[Norme d'un vecteur\|normaliser le vecteur]].

![](/assets/img/fréquence.png#center)
<div align="center">
	<p>
  Source : <a href="https://https://www.oreilly.com/library/view/applied-text-analysis/9781491963036/ch04.html">Applied Text Analysis with Python</a>
</p>
</div>

Scikit-learn propose la méthode `CountVectorizer` du module `sklearn.feature_extraction`. Le modèle utilisé tokenise et normalise les textes par la même occasion. On passe dans `.fit` un itérable, une liste de strings ou de fichiers et crée un dictionnaire du corpus.

La méthode `.transform()` change chaque document en un array *sparce* et les combine dans une matrice :

```pythpn
from sklearn.feature_extraction.text import CountVectorizer

vectorizer = CountVectorizer()
vectors = vectorizer.fit_transform(corpus)
````

> À noter : Pour des corpus de grande taille, on peut utiliser `HashingVectorizer` qui hache le mot à son index, réduisant ainsi la mémoire utilisée (toutes les informations n'ont pas besoin d'être chargées dans la mémoire). Il n'y a toutefois pas de réversion au stade précédent dans ce cas `.inverse_transform` et l'on ne peut pas pondérer par fréquence inverse.

## One-hot-encoding

(à faire)

## TF-IDF (*term frequency - inverse document frequency*)

La vectorisation TF-IDF mesure pour chaque mot son originalité. On compare la fréquence avec laquelle le mot apparait dans le document à la fréquence inverse avec laquelle il apparaît au moins une fois dans les documents comparés.

[[Plusieurs variantes de calcul existent::https://en.wikipedia.org/wiki/Tf%E2%80%93idf#Definition]]. La méthode canonique :

* $tf(t,d) = f_{t,d}$ correspond au nombre de fois où le terme $t$ est rencontré dans le document $d$.
* $idf(t,D) = \log \frac{N}{n_{t}}$. 
  * $N$ correspond au nombre total de documents et $n_t$ au nombre de documents contenant le terme $t$.

$$
tfidf(t,d) = \frac{f_{t, d}}{\log \frac{N}{n_{t}}}
$$

Où :





### L'enregistrement de la matrice TF

Si l'on analyse plusieurs textes (ce qui est généralement le cas), on aura alors une grande matrice, où chaque colonne correspondra à un mot et chaque ligne à un texte comparé. On l'appelle une matrice creuse compressée (*Compressed Sparse Row - CSR*).

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



Références :

- [Applied Text Analysis with Python, Benjamin Bengfort, Rebecca Bilbro, Tony Ojeda](https://www.oreilly.com/library/view/applied-text-analysis/9781491963036/)
- [*Data Science Bookcamp*, de Leonard Apelstin](https://www.manning.com/books/data-science-bookcamp)

[^1]: Exemple tiré de *Data Science Bookcamp*, de Leonard Apelstin, utilisant le `newsgroup` dataset (`from sklearn.datasets import fetch_20newsgroups`)