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
| Représentations distribuées | Vectorisation fondée sur le contexte ou la similarité entre les mots | Modéliser des relations complexes | Très gourmandes en performance et nécessitent des outils supplémentaires (Tensorflow) pour analyser des larges volumes|   |


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



> À noter : Pour des corpus de grande taille, on peut utiliser `HashingVectorizer` qui hache le mot à son index, réduisant ainsi la mémoire utilisée (toutes les informations n'ont pas besoin d'être chargées dans la mémoire). Il n'y a toutefois pas de réversion au stade précédent dans ce cas `.inverse_transform` et l'on ne peut pas pondérer par fréquence inverse.

```pythpn
from sklearn.feature_extraction.text import CountVectorizer

vectorizer = CountVectorizer()
vectors = vectorizer.fit_transform(corpus)
````


### Compressed Sparse Row

On obtient une [matrice creuse](https://en.wikipedia.org/wiki/Sparse_matrix) (*sparse matrix*) où la plupart des éléments égaux à 0. Un exemple d'une telle matrice :

$$
\left(\begin{array}{cccccc}
10 & 0 & 0 & 0 & 0 & 0 \\
0 & 30 & 0 & 40 & 0 & 0 \\
0 & 0 & 0 & 60 & 70 & 0 \\
0 & 0 & 0 & 0 & 0 & 80
\end{array}\right)
$$

Le stockage se fait dans un format *[[Manipuler les formats longs et larges de données\|long]]* particulier, appelé matrice creuse compressée (*Compressed Sparse Row - CSR*). 

Elle correspond schématiquement aux données suivantes :

```
Value         = [ 10 20 30 40 50 60 70 80 ]
COL_INDEX = [  0  1  1  3  2  3  4  5 ]   
ROW_INDEX = [  0  2  4  7  8 ]
````

Sklearn renvoie un objet de type `csr_matrix`, qui correspond à une matrice creuse.

```python
print(type(vectors))

> <class scipy.sparse.csr.csr_matrix>

print(vectors)

> (0, 108644)       4
(0, 110106)     1
(0, 57577)      2
(0, 24398)      2
(0, 79534)      1
(0, 100942)     1
(0, 37154)      1


:       :
(11313, 55901)  1
(11313, 93448)  1
...
````

On peut consulter la [doc](https://docs.scipy.org/doc/scipy/reference/generated/scipy.sparse.csr_matrix.html) de scipy à ce sujet. Elle détient des attributs qui permettent d'accéder à ses données. Par exemple :

- `.indices`: indices 

On peut 

Pour éviter les problèmes de compatibilité, il est possible de la convertir ensuite au format [[array]] :

```python
vectors_np_matrix = vectors.toarray()

print(vectors_np_matrix)

> [ [0 0 0 ... 0 0 0]
 [0 0 0 ... 0 0 0]
 [0 0 0 ... 0 0 0]
 ...
 [0 0 0 ... 0 0 0]
 [0 0 0 ... 0 0 0]
 [0 0 0 ... 0 0 0] ]
  ````

## One-hot-encoding

(à faire)

## TF-IDF (*term frequency - inverse document frequency*)

La vectorisation TF-IDF donne à chaque mot un poids selon son originalité et son importance dans le document.

On compare la fréquence avec laquelle le mot apparait dans le document à la fréquence inverse avec laquelle il apparaît au moins une fois dans les documents comparés.

[Plusieurs variantes de calcul existent](https://en.wikipedia.org/wiki/Tf%E2%80%93idf#Definition). Voici la méthode canonique :

* $tf(t,d) = f_{t,d}$ correspond au nombre de fois où le terme $t$ est rencontré dans le document $d$.

* $idf(t,D) = \log \frac{N}{1+n_{t}}$.
  - $N$ correspond au nombre total de documents
  - $n_t$ au nombre de documents contenant le terme $t$. On y ajoute 1 pour éviter les problèmes de division par 0.
  - Le logarithme du ratio est utilisé afin d'éviter que les termes trop rares ne produisent des résultats trop importants.

Le résultat :

$$
tfidf(t,d) = \frac{f_{t, d}}{\log \frac{N}{n_{t}}}
$$

Cette mesure permet d'évaluer le potentiel sémantique de chaque mot :

- $tf(t,d)$ : Plus un mot est retrouvé dans un document, plus il y influe sur le sens du document, ce qui sera utile pour comparer celui-ci aux corpus.
- $idf(t,D$ : Moins un mot est utilisé au sein du corpus, plus il sera important pour évaluer la similitude entre deux documents où l'on le retrouve.

Les variantes viennent lisser les poids produits par la formule de départ. Par exemple, plutôt que de d'utiliser la fréquence brute $tf(t,d)= f_{t, d}$, on peut utiliser la fréquence relative au sein du document :

$$
tf(t,d)=f_{t, d} / \sum_{t^{\prime} \in d} f_{t^{\prime}, d}
$$

On génère la matrice contenant les poids tfidf avec le transformeur `TfidfVectorizer` de `sklearn.feature_extraction.text`:

```python
from sklearn.feature_extraction.text import TfidfVectorizer
tfidf_vectorizer = TfidfVectorizer()
tfidf_matrix = tfidf_vectorizer.fit_transform(data_nlp)
````

Le vocabulaire associé à la position du mot dans l'index est consultable en inspectant l'attribut `.vocabulary_` du transformeur :
 
 ```python
tfidf_vec = tfidf_vectorizer.fit(data_nlp).vocabulary_
print(tfidf_vec)

{'clue': 8629,
 'crazy': 10154,
 'pant': 33931,
 'fragility': 17132,
 'porcelain': 35870,
 'doll': 12709,
 'ig': 21954,
 'rini': 39010,
 'jump': 24277,
 'conclusion': 9231,
 'unto': 48633,
 'shall': 41297,
 ...}
````

Accessoirement, on peut utiliser ce code tiré d'un cours de [Datacamp](https://campus.datacamp.com/courses/preprocessing-for-machine-learning-in-python/selecting-features-for-modeling?ex=9), qui permet de ne conserver pour chaque texte que les `n` mots dont les scores sont les plus élevés.

```python
from sklearn.feature_extraction.text import TfidfVectorizer

# On initialise le transformateur
tfidf_vectorizer = TfidfVectorizer()

# On entraîne le transformateur sur le corpus (data_nlp)
tfidf_train = tfidf_vectorizer.fit(data_nlp)

# On crée un dictionnaire {index : mot}
vocab = {v:k for k,v in tfidf_vec.vocabulary_.items()}

# Cette fonction retourne les indexes des top_n mots de la ligne vector_index 
# ayant le poids tfidf le plus élevé :
def return_weights(vocab, original_vocab, vector, vector_index, top_n):
    zipped = dict(zip(vector[vector_index].indices, vector[vector_index].data))
    
    # Let's transform that zipped dict into a series
    zipped_series = pd.Series({vocab[i]:zipped[i] for i in vector[vector_index].indices})
    
    # Let's sort the series to pull out the top n weighted words
    zipped_index = zipped_series.sort_values(ascending=False)[:top_n].index
    return [original_vocab[i] for i in zipped_index]

print(return_weights(vocab, tfidf_vec.vocabulary_, text_tfidf, 3, 5))

> [40507, 40394, 44197, 3928, 12690]

print([vocab[key] for key in [40507, 40394, 44197, 3928, 12690]]

> ['schütze', 'schaukeln', 'stroud', 'bartimäus', 'doherty']
````

```python
# Cette fonction retourne les indices des top_n mots de chaque ligne, pour tout le corpus.
def words_to_filter(vocab, original_vocab, vector, top_n):

    filter_list = []
    for i in range(0, vector.shape[0]):
    
        filtered = return_weights(vocab, original_vocab, vector, i, top_n)
        filter_list.extend(filtered)
    # On utilise un set pour éviter les doublons
    return set(filter_list)

# On obtient une liste d'indices
filtered_words = words_to_filter(vocab, tfidf_vec.vocabulary_, text_tfidf, 3)

print(filtered_words)

> {32770,
 32774,
 8,
 10,
 12,
 32780,
 ...
 }
````

```python
# En convertissant filtered_words en une liste, on peut l'utiliser pour retirer les mots 
# dont la valeur tf_idf est trop faible.
filtered_text = text_tfidf[:, list(filtered_words)]
print(filtered_text.shape)

> (8979, 13309)

# La matrice vectorisée initiale :
print(tfidf_vectorizer.fit_transform(data_nlp).shape)

>(8979, 52631)

# On constate que l'on a supprimé trois quarts des mots.
```

## Word2Vec

([à complêter](https://datascientest.com/nlp-word-embedding-word2vec))

### Mise en oeuvre avec [[Spacy]]

Spacy facilite grandement les manipulations de données et la vectorisation des lignes. 

Une fois l'objet `nlp()` instancié avec du texte, on peut accéder à l'attribut `.vector` qui correspond à un vecteur Word2Vec contenant 300 paramètres :

```python
a = nlp(data_nlp[1])

a.doc.vector

> array([-3.21556963e-02,  2.17638351e-02, -1.38545722e-01, -4.77672182e-02,
       -6.52147382e-02, -2.39986577e-03, -1.01446500e-03,  1.90733224e-02,
       -9.15176515e-03,  1.29499006e+00, -1.51797950e-01, -6.41339123e-02,
	   ...]
	   
a.doc.vector.shape

> (300,)
````

Ce vecteur n'est pas [[Norme d'un vecteur\|normalisé]] :

```python
# Norme du vecteur
np.linalg.norm(a.doc.vector)

> 2.5447857

# Norme du vecteur une fois normalisé
np.linalg.norm(a.doc.vector/np.linalg.norm(a.doc.vector))

> 1.0

# Distance entre deux vecteurs :

b = nlp(data_nlp[4])
np.linalg.norm(a.doc.vector - b.doc.vector)

> 1.488689
````

La similarité se calcule avec la méthode `.similarity(autre_vecteur)` :

```python
a.similarity(b)

> 0.8405074023837766
````



Références :

- [Applied Text Analysis with Python, Benjamin Bengfort, Rebecca Bilbro, Tony Ojeda](https://www.oreilly.com/library/view/applied-text-analysis/9781491963036/)
- [*Data Science Bookcamp*, de Leonard Apelstin](https://www.manning.com/books/data-science-bookcamp)

