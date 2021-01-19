---
title: Spacy
tags: NLP
toc: true
etat: été
---
Bienvenue sur la méganote Spacy 🔥 ! SpaCy est une bibliothèque python facilitant les étapes du NLP. 

La documentation de spaCy est, je dois le dire, absolument la meilleure que j'ai pu lire sur le web :

* [[Pour bien commencer::https://spacy.io/usage]]
* [[La documentation de l'API::https://spacy.io/api]]
* [[Les modèles disponibles::https://spacy.io/models]]

[Une cheat-sheet est disponible.](https://datacamp-community-prod.s3.amazonaws.com/29aa28bf-570a-4965-8f54-d6a541ae4e06)

## Installation

Installation de spacy :

```bash
!pip install spacy
````

Il faut penser à installer le pack de langage désiré [parmi ceux disponibles](https://spacy.io/usage/models) pour l'analyse. Pour exemple avec le modèle "lourd" (> 750 Mo)[^1]

[^1]: Voir [ce ticket](https://github.com/explosion/spaCy/issues/4577)

```bash
!python -m spacy download en_core_web_lg
```

On peut alors importer spaCy, ainsi que le modèle contenant les règles de tokenisation ainsi que le pipeline de transformation :

```python
import spacy
import en_core_web_lg

# On charge le modèle de base avec .load("en_core_web_sm")
nlp = en_core_web_lg.load()
```

On peut toutefois importer la classe correspondant au champ lexical d'une langue :

```python
from spacy.lang.en import English
nlp = English()
```

Certaines parties de spaCy, comme la reconnaissance d'attributs _contextuels_ (POS, dep, ents) ne sera, dans ce cas, pas possible.

## Trouver des mots, des phrases, des noms et des concepts

### Nettoyage et tokenisation

On instancie un objet `nlp` - le modèle - en passant du texte en paramètre. Cela entraine le nettoyage de celui-ci, sa tokenisation et la labellisation de ses composants :

```python
# On charge le texte dans un objet Doc :
doc = nlp("Apple is looking at buying U.K. startup for $1 billion")

# Composants du texte, accessibles en terminant leur attribut par '_' : 
for token in doc:
    print(token.i, token.text, token.lemma_, token.pos_, token.tag_, token.dep_,
            token.shape_, token.is_alpha, token.is_stop)
```

Les attributs des tokens, traités par spaCy, sont disponibles en accédant aux propriétés de l'objet. Les attributs terminant par un _underscore_ sont au format texte, les autres sont des facteurs d'identification (index, [[hash]]...)

Description des attributs :

* `.token.i` : index du token dans le texte
* `.text` : le texte original de l'objet `.token`
* `.lemma_` : la forme de base du mot (similaire à la racine)
* `.pos_` : "Part-Of-Speech" tag, correspond à la forme grammaticale du mot (adjectif, adverbe...)
* `.dep_` : lien de dépendance (préposition, auxiliaire...)
* `.shape_` : la forme du mot (capitalisation, ponctuation, chiffres...)
* `.alpha_` : le token est est-il un mot alphabétique
* `.stop_` : le token fait il partie des mots 'stop' (les mots les plus communs)
* `.head` : token parent d'un point de vue syntaxique ("orange" dans "orange bleue") - c'est un token gigogne, on accède à son texte par `.head.text`

> **A noter** :
> Il est possible d'avoir une explication des labels avec `spacy.explain()` :
>
> ```python
> spacy.explain("VBZ")
> 
> > 'verb, 3rd person singular present'
> ```

Le résultat :

| i | TEXT | LEMMA | POS | TAG | DEP | SHAPE | ALPHA | STOP |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 1 | Apple | Apple | PROPN | NNP | nsubj | Xxxxx | True | False |
| 2 | is | be | AUX | VBZ | aux | xx | True | True |
| 3 | looking | look | VERB | VBG | ROOT | xxxx | True | False |
| 4 | at | at | ADP | IN | prep | xx | True | True |
| 5 | buying | buy | VERB | VBG | pcomp | xxxx | True | False |
| 6 | U.K. | U.K. | PROPN | NNP | compound | X.X. | False | False |
| 7 | startup | startup | NOUN | NN | dobj | xxxx | True | False |
| 8 | for | for | ADP | IN | prep | xxx | True | True |
| 9 | $ | $ | SYM | $ | quantmod | $ | False | False |
| 10 | 1 | 1 | NUM | CD | compound | d | False | False |
| 11 | billion | billion | NUM | CD | pobj | xxxx | True | False |

> **A noter** :
> Il est possible de sélectionner une tranche (_slice_) de plusieurs mots en en spécifiant l'index : `doc[0:3]`.
> Une bonne pratique est de le stocker dans un objet Span.

### Named entities

Il est possible d'accéder aux "named entity" (- aux noms propres) avec la propriété `.ents`, spaCy reconnaissant ces objets automatiquement :

```python
doc = nlp("Apple is looking at buying U.K. startup for $1 billion")

for ent in doc.ents:
    print(ent.text, ent.start_char, ent.end_char, ent.label_)
```

Description des propriétés :

* `.text` : le texte original de l'objet `.ent`
* `.start_char` : l'index du début de l'entité dans le `doc
* `.end_char` : l'index de la fin de l'entité
* `.label_` : le type d'entité

Le résultat :

| TEXT | START | END | LABEL | DESCRIPTION |
| --- | --- | --- | --- | --- |
| Apple | 0 | 5 | ORG | Companies, agencies, institutions. |
| U.K. | 27 | 31 | GPE | Geopolitical entity, i.e. countries, cities, states. |
| $1 billion | 44 | 54 | MONEY | Monetary values, including unit. |

> **A noter** :
> Il est possible de passer dans spaCy des règles [customisées de tokenisation](https://spacy.io/usage/linguistic-features#tokenization0) au cas où l'on rencontrerai un problème lié à la mauvaise tokenisation des mots, qui empêcherait leur reconnaissance.

Le composant correspondant à la reconnaissance des entités s'appelle `ner`. D'autres composants peuvent être utilisés :

- `EntityRuler` : permet de reconnaitre des named entities dans des spans, c'est à dire des groupes de mots.
```python
from spacy.pipeline import EntityRuler
nlp.add_pipe(entity_ruler, before="ner")
````

- `Spaczz` qui fait du "fuzzy matching", une technique plus permissive qui permet de compenser les fautes d'orthographe et de matcher plusieurs mots.
````
from spaczz.pipeline import SpaczzRuler
nlp.add_pipe(spaczz_ruler, before="ner")
````


### Matching

#### Matcher

On peut matcher des parties du texte, de manière similaire au [[regex]] mais en ajoutant la possibilité de matcher des mots possédant certains attributs.

Par exemple, dans le texte "Pierre boit de l'eau", on peut matcher "Pierre"+Verbe, ce qui n'est pas faisable avec une expression régulière.

Il faut importer le module `spacy.matcher`:

```python
from spacy.matcher import Matcher

nlp = spacy.load('en_core_web_sm')

# Initialisation du matcher avec le vocabulaire partagé
matcher = Matcher(nlp.vocab)
```

On définit un motif (*pattern*) sous forme d'une liste de dictionnaires :

```python
pattern = [{'ORTH': 'iPhone'}, {'IS_DIGIT': True}]
matcher.add('IPHONE_PATTERN', None, pattern)
```

Ici, `ORTH` correspond au texte du token ('iphone'). On peut utiliser aussi `'TEXT`'.

On peut le remplacer par l'attribut que l'on désire chercher, par exemple `'LOWER':'arbre'` ou `'POS':'VERB'`.

* La liste des attributs de type lexeme : https://spacy.io/api/lexeme#attributes
* La liste des attributs de type POS : https://spacy.io/api/annotation#pos-en
* La liste des attributs de type Named Entity : https://spacy.io/api/annotation#named-entities

> **A noter** :
> Les noms des attributs (`is_digit` par exemple) doivent être écrits en majuscule

On ajoute le motif avec la méthode `.add(pattern_name, on_match, patterns*)`[^1](https://spacy.io/api/matcher#add)

On peut utiliser (similaire au [[regex]]) des opérateurs :

| - | Description |
| --- | --- |
| {'OP': '!'} | Negation: match 0 times |
| {'OP': '?'} | Optional: match 0 or 1 times |
| {'OP': '+'} | Match 1 or more times |
| {'OP': '*'} | Match 0 or more times |

On ajoute alors l'opérateur au dictionnaire de l'élément :

```python
# On matche un adjectif + un ou deux noms
pattern = [{'POS': 'ADJ'}, {'POS': 'NOUN'}, {'POS': 'NOUN', 'OP': '?'}]
```

**Le matcher retourne un tuple comprenant trois éléments** : `match_id`, qui correspond à la valeur hash du nom du motif, `start`, qui correspond au début de l'index du span matché et `end`, sa fin.

On utilise le matcher :

```python
matches = matcher(doc)
print('Matches:', [doc[start:end].text for match_id, start, end in matches])

> Matches: ['iPhone 6']
```

#### PhraseMatcher

Il est également possible de matcher des objets de type `doc`et `span` avec le module [PhraseMatcher](https://spacy.io/api/phrasematcher#_title). C'est particulièrement utile lorsque l'on veut matcher une liste d'expressions composées de plusieurs mots, par exemple des noms de pays ("Arabie Saoudite").l

```python
# Import the PhraseMatcher and initialize it
from spacy.matcher import PhraseMatcher
matcher = PhraseMatcher(nlp.vocab)

# Create pattern Doc objects and add them to the matcher
# This is the faster version of: [nlp(country) for country in COUNTRIES]
patterns = list(nlp.pipe(COUNTRIES))
matcher.add('COUNTRY', None, *patterns)

# Call the matcher
matches = matcher(doc)
```

On peut ensuite ajouter les matchs (en l'occurrence, des noms de pays) à la liste de Named Entities :

```python
# Create a doc and find matches in it
doc = nlp(text)

# Iterate over the matches
for match_id, start, end in matcher(doc):
    # Create a Span with the label for "GPE" and overwrite the doc.ents
    span = Span(doc, start, end, label='GPE')
    doc.ents = list(doc.ents) + [span]
    
    # Get the span's root head token
    span_root_head = span.root.head
    # Print the text of the span root's head token and the span text
    print(span_root_head.text, '-->', span.text)
	
>     [('Namibia', 'GPE'), ('South Africa', 'GPE')]
```

### Visualisations

On peut visualiser la relation de dépendance (`.dep_`) ainsi en exploitant les [visualisations](https://spacy.io/usage/visualizers) intégrées :

```python
from spacy import displacy

nlp = spacy.load("en_core_web_sm")
doc = nlp("This is a sentence.")
displacy.serve(doc, style="dep")
```

![dependency tree](/assets/img/dependency_tree_spacy.png#center)

## Analyse de données

### Structure des données dans spaCy

![structure données](/assets/img/spacy-structure-donnees.png#center)

#### String

Chaque mot du texte reçoit une valeur hash la reliant à un mot dans le catalogue de mot `StringStore`., à la manière d'un index dans un [[bag-of-words]].

On peut le consulter ainsi :

```python
# Look up the hash for the word "cat"
cat_hash = nlp.vocab.strings["cat"]
print(cat_hash)

> 5439657043933447811

# Look up the cat_hash to get the string
cat_string = nlp.vocab.strings[cat_hash]
print(cat_string)

> cat
```

On ne peut toutefois pas chercher à partir d'un hash un mot non sauvegardé auparavant. Il faudra donc le rajouter avant de pouvoir le chercher.

> **A noter** :
> L’hashage d'un mot est différent pour chaque texte.

#### Vocab

Le `vocab` correspond au [lexemes](https://spacy.io/api/lexeme#_title) et fait le lien entre le `doc` et le `StringStore`.

`vocab`contient pour chaque mot des informations **indépendantes du contexte** :

* `lexeme.text`
* `lexeme.orth`- la valeur hash (l'index, en quelque sorte) qui le relie au dictionnaire
* Des attributs lexicaux comme `lexeme.is_alpha`
* Aucun attribut contextuel de type `POS`, `dep`, `ents`...

#### Doc

L'objet `doc`contient le `vocab` et les tokens, qui représentent le résultat de l'analyse du texte par spaCy et contiennent les informations relatives à leur valeur `string` mais aussi à leur relation avec les autres tokens.

On construit un objet `doc`de la manière suivante :

```python
from spacy.lang.en import English
nlp = English()

# Importation de la classe Doc
from spacy.tokens import Doc

# Les éléments du doc : les mots et les espaces
words = ['Hello', 'world', '!']
spaces = [True, False, False]

# Create a doc manually
doc = Doc(nlp.vocab, words=words, spaces=spaces)
```

Comme on le voit ci-dessus, l'objet `Doc` prend en paramètres `nlp.vocab` correspondant à la langue du texte, les mots et les espaces qui les séparent.

#### Span

Un objet `span` est un sous-ensemble de `Doc`correspondant à une tranche de mots du `doc`.

![Span](/assets/img/spacy-span.png#center)

On le déclare avec le constructeur `Span(doc, start, end)` où `start`et `end` correspondent à l'index de début et de fin de l'expression dans le doc.

```python
# Import the Doc and Span classes
from spacy.tokens import Doc, Span

# The words and spaces to create the doc from
words = ['Hello', 'world', '!']
spaces = [True, False, False]

# Create a doc manually
doc = Doc(nlp.vocab, words=words, spaces=spaces)

# Create a span manually
span = Span(doc, 0, 2)

print(span)

> 'Hello world'
```

Une fois l'objet `Span` importé et définit, on peut l'ajouter, par exemple, à l'objet `doc.ents`. Dans ce cas, spaCy reconnaitre le contenu du `span` comme une named entity.

```python
# Create a span with a label
span_with_label = Span(doc, 0, 2, label="GREETING")

# Add span to the doc.ents
doc.ents = [span_with_label]

print([(ent.text, ent.label_)

> [('Hello world', 'GREETING')]
```

En règle générale, il vaut mieux utiliser `span`et `doc` un maximum pour éviter la conversion en strings, qui est moins efficace et prend plus de temps.

### Analyse de similarité

#### Vectorisation du texte

SpaCy peut réaliser des analyses de similarité. En anglais, le texte est vectorisé selon l'algorithme [GloVe](https://spacy.io/models/en#en_core_web_md) [par défaut](https://spacy.io/models/en#en_core_web_md). Il est par ailleurs [possible](https://spacy.io/usage/vectors-similarity#converting) d'utiliser des algorithmes alternatifs ([[Word2Vec]]), par exemple.

Les modèles utilisés pour analyser le texte ne sont pas tous équivalent : si l'on veut vectoriser un texte en les utilisant, il faut charger le modèle `md` (medium) ou `lg` (large - 746mo en anglais)

On peut consulter les valeurs calculée du vecteur d'un `token` ou d'un `doc` avec `doc.vector`ou `doc[index].vector`.




Spacy facilite grandement les manipulations de données et la vectorisation des lignes. 

Une fois l'objet `nlp()` instancié avec du texte, on peut accéder à l'attribut `.vector` qui correspond à un vecteur Word2Vec contenant 300 paramètres :

```python
a = nlp("We shall prevail")


a.doc.vector

> array([-3.21556963e-02,  2.17638351e-02, -1.38545722e-01, -4.77672182e-02,
       -6.52147382e-02, -2.39986577e-03, -1.01446500e-03,  1.90733224e-02,
       -9.15176515e-03,  1.29499006e+00, -1.51797950e-01, -6.41339123e-02,
	   ...]
	   
a.doc.vector.shape

> (300,)
````

Ce vecteur n'est pas [[Norme d'un vecteur\|normalisé]]. On accède à sa [[Norme d'un vecteur\|norme]] avec `doc.vector_norm`. :

```python
# Norme du vecteur
np.linalg.norm(a.doc.vector)

> 4.845292

a.doc.vector_norm

> 4.845291884565563

# Norme du vecteur une fois normalisé
np.linalg.norm(a.doc.vector/np.linalg.norm(a.doc.vector))

> 0.99999994

# Distance entre deux vecteurs :

b = nlp("A computer fell off the apple tree")
np.linalg.norm(a.doc.vector - b.doc.vector)

> 4.6305118
````

La similarité se calcule avec la méthode `.similarity(autre_vecteur)` :

```python

````

#### Similarité

SpaCy propose par défaut la méthode `doc.similarity(doc comparé)` qui produit la [[similarité cosinus]]. On peut l'appliquer à un document ou à un Span.

```python
# A partir d'un doc :
a.similarity(b)

> 0.456237800465105

doc = nlp("This was a great restaurant. Afterwards, we went to a really nice bar.")

# Spans pour "great restaurant" et "really nice bar"
span1 = doc[3:5]
span2 = doc[12:15]

# Similarité entre les spans
similarity = span1.similarity(span2)
print(similarity)

> 0.702384635043887
```

## Pipelines

### Composants

Lorsque l'on crée un objet `doc`à partir d'un texte, spaCy démarre un pipeline pour extraire les informations de ce dernier et classifier ces éléments.

![pipeline](/assets/img/spacy-pipeline.png#center)

| NOM | COMPOSANT | PRODUIT | DESCRIPTION |
| --- | --- | --- | --- |
| tokenizer | Tokenizer | Doc | Segmente le text en tokens. |
| tagger | Tagger | Doc\[i\].tag | Attribue les tags \[\[POS\]\]. |
| parser | DependencyParser | Doc\[i\].head, Doc\[i\].dep, Doc.sents, Doc.noun_chunks | Attribue les étiquettes (labels) de dépendance. |
| ner | EntityRecognizer | Doc.ents, Doc\[i\].ent_iob, Doc\[i\].ent_type | Détecte et étiquette les named entities. |
| textcat | TextCategorizer | Doc.cats | Attribue une étiquette aux documents. |
| … | custom components | Doc._.xxx, Token._.xxx, Span._.xxx | Assigne des attributs, des méthodes ou des propriétés custom. |

Chaque modèle définira dans ses métadonnées (`meta.json`) les composants de son pipeline de traitement :

```json
{
	"lang":"en",
	"name":"core_web_sm",
	"pipeline": ["tagger", "parser", "ner"]
}
```

> **A noter** :
> On peut accéder aux noms des composants du pipeline en appelant `nlp.pipe_names` :
>
> ```python
> print(nlp.pipe_names)
> > ["tagger", "parser", "ner"]
> ```
>
> Et aux tuples `(nom, composant)` avec `nlp.pipeline`:
>
> ```python
> [('tagger', <spacy.pipeline.Tagger>), 
>  ('parser', <spacy.pipeline.DependencyParser>), 
>  ('ner', <spacy.pipeline.EntityRecognizer>)]
> ```

#### Ajouter ses propres composants

Il est possible de créer des fonctions de traitement du texte personnalisées et de les ajouter au pipeline. Elles seront alors exécutées avec les autres composants.

Une fois la fonction définie, on l'ajoute avec la méthode `nlp.add_pipe`. Il est possible d'ajouter une option définissant son ordre de passage dans l'exécution du pipe :

| Argument | Description | Example |
| --- | --- | --- |
| last | If True, add last | nlp.add_pipe(component, last=True) |
| first | If True, add first | nlp.add_pipe(component, first=True) |
| before | Add before component | nlp.add_pipe(component, before='ner') |
| after | Add after component | nlp.add_pipe(component, after='tagger') |

Un exemple ou l'on renvoie la longueur du document :

```python
# Create the nlp object
nlp = spacy.load('en_core_web_sm')

# Define a custom component
def custom_component(doc):

	# Print the doc's length    
	print('Doc length:' len(doc))

	# Return the doc object
	return doc

# Add the component first in the pipeline
nlp.add_pipe(custom_component, first=True)

# Print the pipeline component names
print('Pipeline:', nlp.pipe_names)

Pipeline: ['custom_component', 'tagger', 'parser', 'ner']

# Process a text
doc = nlp("Hello world!")

> Doc length: 3
```

### Extensions

On peut définir des attributs (comme `.POS_`par exemple), des propriétés et des méthodes personnalisés. Ils sont accessibles par la propriété `._.` :

```python
doc._.title = 'My document'
token._.is_color = True
span._.has_color = False
```

On peut les enregistrer dans les objets `Doc`, `Token` ou `Span` avec le setteur `.set_extension`. Le niveau d'objet dans lequel on enregistrera l'extension sera celui sur lequel on l'appellera.

Par exemple, si l'on veut analyser tout un document, on utiliser `doc`. En revanche, si on veut analyser le contenu d'un `span`, ce sera ce niveau qui sera choisi.

```python
# Import global classes
from spacy.tokens import Doc, Token, Span

# Set extensions on the Doc, Token and Span
Doc.set_extension('title', default=None)
Token.set_extension('is_color', default=False)
Span.set_extension('has_color', default=False)
```

Il existe trois types d'extensions :

#### Attributs

Ils sont définis par l'utilisateur et peuvent être modifiés directement  à tout moment :

```python
from spacy.tokens import Token

# Set extension on the Token with default value
Token.set_extension('is_color', default=False)
doc = nlp("The sky is blue.")

# Overwrite extension attribute value
doc[3]._.is_color = True
```

#### Propriétés

Elles nécessitent de définir des méthodes `getter`et `setter`. Ces dernières seront appelées pour produire ou définir la valeur au moment où l'on y accède.

```python
from spacy.tokens import Token
# Define getter function
def get_is_color(token):
	colors = ['red', 'yellow', 'blue']
	return token.text in colors
	
# Set extension on the Token with getter
Token.set_extension('is_color', getter=get_is_color)
doc = nlp("The sky is blue.")

print(doc[3]._.is_color, '-', doc[3].text)

> blue - True
```

#### Méthodes

On peut assigner une fonction, qui devient une méthode de l'objet et permet de passer des arguments. Il faut toutefois à ce moment utiliser l'option `method`au lieu de `getter` :

```python
from spacy.tokens import Doc

# Define method with arguments
def has_token(doc, token_text):
	in_doc = token_text in [token.text for token in doc]
	
# Set extension on the Doc with method
Doc.set_extension('has_token', method=has_token)
doc = nlp("The sky is blue.")

print(doc._.has_token('blue'), '- blue')
print(doc._.has_token('cloud'), '- cloud')

> True - blueFalse - cloud
```

Un exemple de méthode : on cherche les named entities et l'on renvoie l'url Wikipédia qui leur correspond :

```python
def get_wikipedia_url(span):
    # Get a Wikipedia URL if the span has one of the labels
    if span.label_ in ('PERSON', 'ORG', 'GPE', 'LOCATION'):
        entity_text = span.text.replace(' ', '_')
        return "https://en.wikipedia.org/w/index.php?search=" + entity_text

# Set the Span extension wikipedia_url using get getter get_wikipedia_url
Span.set_extension('wikipedia_url', getter=get_wikipedia_url)

doc = nlp("In over fifty years from his very first recordings right through to his last album, David Bowie was at the vanguard of contemporary culture.")
for ent in doc.ents:
    # Print the text and Wikipedia URL of the entity
    print(ent.text, ent._.wikipedia_url)
```

Enfin, un exemple d'utilisation croisée entre composant et propriété custom. On crée un composant qui va matcher les pays, leur assigner un label `.ents`.

Par la suite, la propriété, lorsqu'elle sera appelée sur les `.ents` comportant ce label, renverra la capitale du pays en question.

```python
def countries_component(doc):
    # Create an entity Span with the label 'GPE' for all matches
    doc.ents = [Span(doc, start, end, label='GPE')
                for match_id, start, end in matcher(doc)]
    return doc

# Add the component to the pipeline
nlp.add_pipe(countries_component)

# Register capital and getter that looks up the span text in country capitals
Span.set_extension('capital', getter=lambda span: capitals.get(span.text))

# Process the text and print the entity text, label and capital attributes
doc = nlp("Czech Republic may help Slovakia protect its airspace")
print([(ent.text, ent.label_, ent._.capital) for ent in doc.ents])
```

### Performance

#### Traitement des textes

Si l'on désire traiter plusieurs textes, on fera appel à la fonction [nlp.pipe](https://spacy.io/api/language#pipe) plutôt que d'utiliser une [[Comprehension\|list comprehension]].

```python
texts = ["This is a text", "These are lots of texts", "..."]

docs = [nlp(text) for text in texts] # Ne pas faire
docs = list(nlp.pipe(texts)) # Faire
```

On peut aussi utiliser cette technique pour les motifs qui serviront à `PhraseMatcher` :

```python
people = ['David Bowie', 'Angela Merkel', 'Lady Gaga']

# Create a list of patterns for the PhraseMatcher
patterns = list(nlp.pipe(people))
```

`nlp.pipe()` peut traiter des données de type Tuple, si l'on passe en argument `as_tuple=True`:

```python
# Import the Doc class and register the extensions 'author' and 'book'
from spacy.tokens import Doc
Doc.set_extension('book', default=None)
Doc.set_extension('author', default=None)

for doc, context in nlp.pipe(DATA, as_tuples=True):
    # Set the doc._.book and doc._.author attributes from the context
    doc._.book = context['book']
    doc._.author = context['author']
    
    # Print the text and custom attribute data
    print(doc.text, '\n', "— '{}' by {}".format(doc._.book, doc._.author), '\n')
```

On peut utiliser cette méthode pour passer au texte des informations contextuelles, comme le numéro de page, un identifiant, une position...

> **A noter** :
> L'output de `nlp.pipe()` est un [[générateur]] : il faut appeler [[list]] dessus pour l'exploiter.

#### Choix des composants

Il est possible de désactiver les composants qui ne nous sont pas utiles afin d'accélérer le traitement. On rappelle que l'on peut accéder à la liste des composants avec `nlp.pipe_names`.

```python
# Désactiver tagger et parser
with nlp.disable_pipes('tagger', 'parser'):
	doc = nlp(text)
	print(doc.ents)
```

On peut aussi utiliser la méthode `nlp.make_doc(text)` pour procéder uniquement à la tokenisation :

```python
doc = nlp.make_doc("Bonjour tout le monde")
```

## Entrainer un modèle d'étiquetage de données

On peut utiliser spaCy pour modifier le modèle de reconnaissance d'attributs ou détecter des types de Named Entities. Cela permet d'améliorer les systèmes de règles de type regex mais nécessite un grand nombre de données étiquetées au préalable. Un exellent modèle pour commencer est disponible sur [github](https://github.com/explosion/spaCy/blob/master/examples/training/train_ner.py#L75).

![model](/assets/img/spacy-model.png#center)

Les étapes sont les suivantes :

1. Initialisation des poids du modèle avec \`nlp.begin_training\`\`
2. Prédiction de quelques exemples avec les poids actuels avec \`nlp.update\`\`
3. Comparaison des prédiction avec les vrais labels
4. Calcul de nouveaux poids pour améliorer la prédiction
5. Mise à jour des poids
6. Retour à l'étape 2

### Comment obtenir des données d'entrainement ?

#### Des outils externes

[Brat](http://brat.nlplab.org/) et [Prodigy](https://prodi.gy/) sont des outils qui peuvent être utiles pour étiqueter les mots, pour les enregistrer dans le modèle.

#### Le module Matcher

Le module `matcher` est aussi bon moyen d'obtenir des données d'entrainement, en définissant des règles pour étiqueter les données de manière similaire au [[regex]].

##### Exemple

On a les données d'entrainement suivantes :

    How to preorder the iPhone X
    iPhone X is coming
    Should I pay $1,000 for the iPhone X?
    The iPhone 8 reviews are here
    Your iPhone goes up to 11 today
    I need a new phone! Any tips?

Dans l'exemple ci-dessous, on crée un nouveau type de named entity, appelé `GAGDGET`, qui pourra ensuite être utilisé par le modèle.

```python
TRAINING_DATA = []

# Two tokens whose lowercase forms match 'iphone' and 'x'
pattern1 = [{'LOWER': 'iphone'}, {'LOWER': 'x'}]

# Token whose lowercase form matches 'iphone' and an optional digit
pattern2 = [{'LOWER': 'iphone'}, {'IS_DIGIT': True, 'OP': '?'}]

# Add patterns to the matcher
matcher.add('GADGET', None, pattern1, pattern2)

# Create a Doc object for each text in TEXTS
for doc in nlp.pipe(TEXTS):
    # Match on the doc and create a list of matched spans
    spans = [doc[start:end] for match_id, start, end in matcher(doc)]
    # Get (start character, end character, label) tuples of matches
    entities = [(span.start_char, span.end_char, 'GADGET') for span in spans]
    
    # Format the matches as a (doc.text, entities) tuple
    training_example = (doc.text, {'entities': entities})
    # Append the example to the training data
    TRAINING_DATA.append(training_example)
    
print(*TRAINING_DATA, sep='\n')
```

L'output comprenant les données étiquetées est le suivant :

```python
('How to preorder the iPhone X', {'entities': [(20, 28, 'GADGET')]})
('iPhone X is coming', {'entities': [(0, 8, 'GADGET')]})
('Should I pay $1,000 for the iPhone X?', {'entities': [(28, 36, 'GADGET')]})
('The iPhone 8 reviews are here', {'entities': [(4, 12, 'GADGET')]})
('Your iPhone goes up to 11 today', {'entities': []})
('I need a new phone! Any tips?', {'entities': []})
```

### Fonctionnement

Une fois les données étiquetées obtenu, on procède ainsi pour entraîner le modèle :

* Pour un nombre _n_ d'itérations :
  * Mélange des données d'entrainement pour éviter que le modèle fasse des prédictions fondées sur l'ordre des échantillons.
  * Division des données en plusieurs échantillons
  * Entraînement et mise à jour du modèle pour chaque échantillon
* Enregistrement du modèle

Un exemple à partir de l'exemple ci-dessus :

```python
# Start the training
nlp.begin_training()

# Loop for 10 iterations
for itn in range(10):
    # Shuffle the training data
    random.shuffle(TRAINING_DATA)
    losses = {}
    
    # Batch the examples and iterate over them
    for batch in spacy.util.minibatch(TRAINING_DATA, size=2):
        texts = [text for text, entities in batch]
        annotations = [entities for text, entities in batch]
        
        # Update the model
        nlp.update(texts, annotations, losses=losses)
        print(losses)

# Save the model
nlp.to_disk(path_to_model)
```

### Problèmes possibles :

#### "Catastrophic forgetting"

Le modèle peut sur-optimiser la détection d'un type de données et donc "oublier" (sous-optimiser) d'autres données en même temps. Par exemple, si l'on entraine uniquement un modèle sur des données comprenant l'étiquette "PAYS", on risque d'oublier l'étiquette "PERSONNE".

C'est en général dû au fait que l'on a fourni au modèle de nouvelles données ne comprenant pas les anciennes étiquettes. La solution est de mélanger aux nouvelles données des anciennes comprenant la ou les étiquettes manquantes.

#### Données contextuelles

Le modèle peut avoir du mal à détecter de manière fiable des données reposant sur un contexte.

Il vaut donc mieux définir des étiquettes ne dépendant pas du contexte. Par exemple, pas "Chaussure_de_sport" ou "Chaussure_de_randonnées" mais simplement "Chaussure".

#### Problèmes de tokenisation

L'entrainement repose sur l'idée que l'on montre à Spacy une entity qui correspond à un token du texte. Si l'entity ne correspond à aucun token détecté, cela peut poser problème. 

C'est souvent le cas avec les mots du langage qui ne correspondent pas à la rêgle de tokenisation du module `tagger` : par exemple, les mots ayant un `-` tiret, comme 'Jean-Claude'. 

Il faut donc, dans ce cas là, définir un pattern[^1] pour trouver les mots correspondant et regrouper (`merge`) le span qui y correspond:

[^1]: https://stackoverflow.com/questions/50752266/spacy-tokenize-quoted-string/50775597#50775597

```python
pattern = pattern = [{'IS_ALPHA': True}, {'ORTH': '-'}, {'IS_ALPHA': True}]
matcher = Matcher(nlp.vocab)
matcher.add('QUOTED', None, pattern)

def mon_petit_tiret(doc):

	matched_spans = []
    matches = matcher(doc)
    
	for match_id, start, end in matches:
        span = doc[start:end]
        matched_spans.append(span)
		
    for span in matched_spans:  # merge into one token after collecting all matches
        with doc.retokenize() as retokenizer:
            retokenizer.merge(span)
    return doc

# On ajoute le module au pipeline :
nlp.add_pipe(mon_petit_tiret, after='tagger')
````



#### En savoir plus sur l'analyse de données avec spaCy :

https://spacy.io/usage/training pour plus de détails.

***