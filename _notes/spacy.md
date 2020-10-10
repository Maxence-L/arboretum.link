---
title: spaCy
tags: nlp
---

SpaCy est une bibliothèque python facilitant les étapes du NLP.

## Introduction

### Installation
On l'importe de la manière suivante :
```bash
pip install spacy
````

Il faut penser à installer le pack de langage désiré [parmi ceux disponibles](https://spacy.io/usage/models) pour l'analyse :
```bash
python -m spacy download en
````

### Chargement du texte et nettoyage
Et on peut ensuite créer un objet en passant du texte en paramètre. Cela entrainera le nettoyage de celui-ci, sa tokenization et la labellisation de ses composants :

```python
import spacy

# On charge le modèle de base avec .load("en_core_web_sm") - on peut remplacer le langage.

nlp = spacy.load("en_core_web_sm")

# On charge le texte dans un objet Doc :

doc = nlp("Apple is looking at buying U.K. startup for $1 billion")

# Composants du texte, accessibles en terminant leur attribut par '_' : 
for token in doc:
    print(token.text, token.lemma_, token.pos_, token.tag_, token.dep_,
            token.shape_, token.is_alpha, token.is_stop)

````

Les éléments du texte, traités par spaCy, sont disponibles en accédant aux propriétés de l'objet.

Description des propriétés :
- `.text` : le texte original de l'objet `.token``
- `.lemma_`: la forme de base du mot (similaire à la racine)
- `.pos_` "Part-Of-Speech" tag, correspond à la forme grammaticale du mot (adjectif, adverbe...)
- `.dep_` : lien de dépendance (préposition, auxiliaire...)
- `.shape_` : la forme du mot (capitalisation, ponctuation, chiffres...)
- `.alpha_` : le token est est-il un mot alphabétique
- `.stop_`: le token fait il partie des mots 'stop' (les mots les plus communs)

> *A noter :*
> Il est possible d'avoir une explication des labels avec `spacy.explain()` :
> ```python
> spacy.explain("VBZ")
> > 'verb, 3rd person singular present'

Le résultat :


TEXT|LEMMA|POS|TAG|DEP|SHAPE|ALPHA|STOP
----|-----|---|---|---|-----|-----|----
Apple|Apple|PROPN|NNP|nsubj|Xxxxx|True|False
is|be|AUX|VBZ|aux|xx|True|True
looking|look|VERB|VBG|ROOT|xxxx|True|False
at|at|ADP|IN|prep|xx|True|True
buying|buy|VERB|VBG|pcomp|xxxx|True|False
U.K.|U.K.|PROPN|NNP|compound|X.X.|False|False
startup|startup|NOUN|NN|dobj|xxxx|True|False
for|for|ADP|IN|prep|xxx|True|True
\$|\$|SYM|\$|quantmod|\$|False|False
1|1|NUM|CD|compound|d|False|False
billion|billion|NUM|CD|pobj|xxxx|True|False


On peut visualiser la relation de dépendance (`.dep_`) ainsi en exploitant les [visualisateurs](https://spacy.io/usage/visualizers) intégrés :

```python
from spacy import displacy

nlp = spacy.load("en_core_web_sm")
doc = nlp("This is a sentence.")
displacy.serve(doc, style="dep")
````

![dependency tree](/assets/img/dependency_tree_spacy.png)

Il est possible d'accéder aux "named entity" (- aux noms propres) avec la propriété `.ents`, spaCy reconnaissant ces objets automatiquement :

```python
doc = nlp("Apple is looking at buying U.K. startup for $1 billion")

for ent in doc.ents:
    print(ent.text, ent.start_char, ent.end_char, ent.label_)
````
Description des propriétés :

- `.text` : le texte original de l'objet `.ent`
- `.start_char`: l'index du début de l'entité dans le `doc``
- `.end_char`: l'index de la fin de l'entité
- `.label` : le type d'entité

Le résultat :

TEXT|START|END|LABEL|DESCRIPTION
-|-|-|-|-
Apple|0|5|ORG|Companies, agencies, institutions.
U.K.|27|31|GPE|Geopolitical entity, i.e. countries, cities, states.
$1 billion|44|54|MONEY|	Monetary values, including unit.


### Tokenization
Lors du chargement du text, spaCy va le [tokeniser](Tokenisation), c'est à dire le séparer en éléments atomiques chacuns porteurs d'un vecteur sémantique.

```python
import spacy

nlp = spacy.load("en_core_web_sm")
doc = nlp("Apple is looking at buying U.K. startup for $1 billion")
for token in doc:
    print(token.text)
````




