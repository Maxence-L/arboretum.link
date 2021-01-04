---
layout: post
title: Reférences
permalink: /refs/
content-type: eg
---

Cette page référence les ressources que j'utilise ou que j'ai utilisé et mes impressions à leur sujet.

## Data Science

### *An Introduction to Statistical Learning*, Tibshirani et al.

Un livre de référence pour tout débutant approchant la Data Science. Une partie de la popularité de cet ouvrage tient du fait qu'il est [disponible en ligne gratuitement](http://faculty.marshall.usc.edu/gareth-james/ISL/) et qu'il est souvent recommandé par des practiciens plus expérimentés. Ses graphiques très expressifs et ses explications simples permettent de comprendre rapidement les concepts enseignés.

Le livre contient de nombreux exemples et exercices de programmation avec R et utilisant une bibliothèque désuète - une mise à jour serait méritée de ce point de vue. On peut toutefois trouver des [courageux lecteurs l'ayant "traduit" en python](https://github.com/JWarmenhoven/ISLR-python). Se pencher sur le code, pas toujours commenté, est un bon exercice pratique pour comprendre le fonctionnement des méthodes utilisées.

Cet ouvrage reste toutefois superficiel à certains égards : une maîtrise complète des méthodes présentées, notamment mathématiques, nécessitera d'aller chercher plus loin que ce qui y est expliqué. Il reste une bonne référence, rapide à prendre en main, au quotidien.

### *The Data Science Design Manual*, Steven S. Skienna

Un excellent livre pour découvrir la Data Science. Je l'ai lu d'une traite lorsque j'ai commencé à étudier le sujet. Skienna est un brillant professeur dont on aimerait être l'élève et qui sait traiter de sujets pointus tout en expliquant leur utilité et leur contexte.

L'ouvrage contient des explications généralement comprenant peu de mathématiques mais très imagées, des exemples, des cas pratiques et des exercices. Je me surprends souvent à y revenir après avoir lu en détail l'aspect théorique d'une méthode afin de la recontextualiser.

Le livre est disponible sur Amazon à un prix modique compte tenu de la qualité de l'ouvrage (relié et couleurs), l'auteur a [mis en ligne ses cours](https://www3.cs.stonybrook.edu/~skiena/data-manual/lectures/), qui promettent d'être très intéressants.

### *Datascience Bookcamp, Leonard Apelstin*

J'ai acheté ce livre qui semblait donner une explication en détail des tests de type bootstrap et de leur application en programmation. Il n'est pas encore terminé, il reste un chapitre à écrire et l'auteur ne semble pas avoir fait de mise à jour depuis mars 2020.

Le livre est centré sur l'application de concepts de machine learning : on crée l'algorithme avec [[numpy]] puis il est donné la bibliothèque spécialisée facilitant le travail. C'est un bon "pont" entre la théorie et la pratique, étant beaucoup plus détaillé que les articles de blogs sur les sujets qu'il aborde. Le chapitre sur l'étude textuelle a le mérite d'accompagner le lecteur pas-à-pas et est une excellente introduction sur le sujet.

On peut regretter toutefois que le code soit difficile à exécuter : la bibliothèque géographique utilisée est obsolète, le chapitre sur le NLP demande beaucoup de mémoire vive et fait facilement planter les machines avec moins de 8 Go de RAM.

L'éditeur, Manning, fait continuellement des promotions : achetez le à -50%.

### *Data Science : cours et exercices*, Amini et al.

Un livre approchant la théorie et les algorithmes de la Data Science, publié dans une nouvelle collection chez Eyrolles et que j'ai découvert dans la librairie éponyme.

L'ouvrage est à la fois théorique et superficiel sur de nombreux sujets. Tous les problèmes sont approchés par la voie mathématique, sans réelles démonstrations ou discussion sur les différences entre les méthodes proposées. Par exemple, la régression ridge est mentionnée sans expliquer pourquoi l'utiliser plutôt qu'une autre technique de régularisation.

De même, de nombreuses solutions pointues sont exposées, mais pas les techniques liées aux arbres de décision, très utilisés dans le domaine. J'aurais préféré un chapitre sur ce sujet plutôt que sur la "visualisation interactive d'information", d'autant que le livre est imprimé en noir et blanc sur un papier mat. Cet oubli est peut-être est dû à l'auteur principal du livre, qui dans ses ouvrages précédents[^1] sur le sujet n'aborde pas non plus les forêts aléatoires.

[^1] "Apprentissage machine - de la théorie à la pratique - Massih-Ressa Amini" (2015) par exemple.

Le livre est donc parfois lacunaire et réservé à des lecteurs expérimentés. Il présente toutefois l'intérêt de donner la définition formelle de certaines méthodes d'apprentissage, ce qui peut en faire une bonne référence si l'on désire mettre en œuvre certaines techniques avancées.

## Sites web, tutos

### *[Datacamp](https://datacamp.com)*

*Learn Data Science from the comfort of your browser, at your own pace with DataCamp's video tutorials & coding challenges on R, Python, Statistics & more.*

Datacamp est un site de tutoriels sur la Data Science et l'analyse de données en général. L'abonnement est assez cher, mais baisse lors d'offres promotionnelles, notamment fin novembre. On peut faire baisser encore plus les prix en prenant un VPN et en se localisant dans un pays à faibles revenus (ex: Inde ou Ukraine, par exemple) - l'abonnement en réduction pour une année passe alors à 99$.

En pratique, il contient de nombreux tutoriels de programmation sur les méthodes de base. Je suis assez sceptique de cette technique d'apprentissage : si l'on aborde beaucoup de librairies et de concepts statistiques, ils sont assez peu expliqués. Il suffit la plupart du temps de coller le code des slides ou de l'adapter à la marge.

Il conviendra donc bien à des personnes très au point du point de vue de la théorie, à l'aise en programmation mais souhaitant des exemples de pratiques standard en Data Science du point de vue de la programmation. Fuyez si vous comptez apprendre à programmer cette manière !

Je conseille de prendre des notes et de faire des recherches annexes tout en suivant des tutoriels pour pouvoir réutiliser le code ensuite, comme ce que je fais avec ce site (beaucoup d'exemples sur python viennent de Datacamp) et [Obsidian](obsidian.md) au quotidien. Ce site me permet surtout de savoir quoi étudier et non comme référence d'apprentissage.

À noter que l'approche est parfois différente selon les tutoriels et que leur code commence à vieillir. Les "projets guidés" sont inutiles et très fastidieux de mon point de vue.

### *Udacity*

Udacity est un site proposant des formations rapides pour une grande diversité de métiers "tech", notamment en programmation. J'ai profité des offres "1 mois gratuit" pour explorer leur curriculum.

Mon avis : **à fuir**. Le seul intérêt des exercices est d'être ardus, les explications étant inexistantes, cela qui oblige l'élève que je suis à chercher par lui-même le contenu.

L'aspect mathématique/probabiliste est très mal abordé : je ne vois pas quelle connaissance du test bootstrap un élève pourra tirer d'une vidéo de 5 minutes sur le sujet. Les forêts aléatoires sont appliquées sans que l'on explique le concept, tout est survolé, mais pour un prix stratosphérique.

## Maths

### [*Introduction to Linear Algebra for Applied Machine Learning with Python*](https://pabloinsente.github.io/intro-linear-algebra), Pablo Caceres

Un article très long récapitulant d'une manière inhabituellement vivante les principaux concepts de l'algèbre linéaire, dans la perspective de son application pour le machine learning. Je l'ai lu pour me rafraichir les idées sur le sujet.

Les puristes reprocheront probablement à l'auteur son manque de rigueur, mais j'ai beaucoup apprécié cet article pour remettre à jour mes connaissances sur le sujet, d'autant qu'il aborde des concepts plutôt avancés, comme les [valeurs-propres](https://pabloinsente.github.io/intro-linear-algebra#eigenthings) ou la [décomposition en valeurs singulières](https://pabloinsente.github.io/intro-linear-algebra#singular-value-decomposition).

Il ne manque que des exercices, corrigés. C'est néanmoins une bonne ressource pour ensuite aborder des ouvrages plus sérieux.

### *Linear Algebra*, Jim Hefferson

(à complêter)

## Probabilités

### *Bayesian Statistics the Fun Way*, Will Kurt

De même que la ressource précédente, j'ai choisi cet ouvrage pour me remettre à jour en probabilités. L'approche est très didactique et ne présuppose pas de connaissances particulière dans le domaine, ce qui est très agréable compte tenu du grand nombre de livres présupposant que le lecteur vient tout juste de sortir de Terminale S ou de troisième année de Licence.

Les exercices sont simples et aident à appliquer les idées présentées, le ton est léger et l'on n’a pas vraiment l'impression de lire un livre de probabilités. Le contenu n'est toutefois pas immense et une fois fini, d'autres ouvrages devront prendre le relais. C'est en résumé un bon livre pour un étudiant curieux (parlant anglais couramment cela dit...) ou un professionnel souhaitant entretenir ses connaissances. L'édition (No Starch Press) est excellente.

## Programmation

### *Book of R*, Tilman M. Davies

Le livre grâce auquel j'ai réellement compris le fonctionnement de R et qui m'a permis d'être efficace professionnellement avec ce langage. R présente deux inconvénients : il est utilisé principalement par des statisticiens et comprend des packages extrêmement utilisés (le tidyverse) dont la syntaxe diffère du langage.

Les ouvrages sur le sujet sont donc souvent centrés sur les statistiques (et non la programmation), et l'utilisation systématique de dplyr complique l'acquisition des bases de la manipulation d'objets dans R, un langage et un idiome cohabitant en permanence.

*Book of R* est d'abord centré sur R et permet d'apprendre les concepts comme n'importe quel langage orienté-objet sans ambiguïté. Le ton de l'auteur est assez froid mais les explications sont claires tout comme les exercices. J'eu apprécié qu'il fut publié au moment de mes études !

### Practical SQL: A Beginner's Guide to Storytelling with Data, *Anthony DeBarros*

Un excellent livre d'introduction à SQL. Les exemples des notes du site au sujet d'SQL viennent de ce livre  : je me rééxplique en français l'ouvrage à mesure que j'avance dans la lecture. 

L'écriture de l'auteur est claire et très joviale et l'on aborde tous les points important pour se débrouiller ensuite professionnellement avec les bases de données. Le livre aborde même des sujets techniques qui dépassent la seule introduction au langage. C'est donc une bonne référence qui évoluera avec le lecteur. Par ailleurs, étant publié chez NoStarch, l'édition est excellente.

### *Python Workout*, Reuven M. Lerner

Un livre d'exercices en apparence simples sur Python, mais dont la résolution est expliquée en détail et approfondie à chaque fois. Une excellente manière de s'entrainer au quotidien et de découvrir certaines fonctionnalités méconnues ou des solutions habiles à certaines questions.
