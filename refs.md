---
layout: post
title: Reférences
permalink: /refs/
content-type: eg
---

Cette page référence les ressources que j'utilise ou que j'ai utilisé et mes impressions à leur sujet.

### *An Introduction to Statistical Learning* - Tibshirani et al.

Un livre de référence pour tout débutant approchant la Data Science. Une partie de la popularité de cet ouvrage tient du fait qu'il est [disponible en ligne gratuitement](http://faculty.marshall.usc.edu/gareth-james/ISL/) et qu'il est souvent recommandé par des practiciens plus expérimentés. Ses graphiques très expressifs et les explications simples permettent de comprendre rapidement les concepts enseignés.

Le livre contient de nombreux exemples et exercices de programmation avec R et utilisant une bibliothèque désuète - une mise à jour serait méritée de ce point de vue. On peut toutefois trouver des [courageux lecteurs l'ayant "traduit" en python](https://github.com/JWarmenhoven/ISLR-python). Se pencher sur le code, pas toujours commenté, est un bon exercice pratique pour comprendre le fonctionnement des méthodes utilisées.

Cet ouvrage reste toutefois superficiel à certains égards : une maîtrise complête des méthodes, notamment mathématique présentées nécessitera d'aller chercher plus loin que ce qui y est présenté. Il reste une bonne référence, rapide à prendre en main, au quotidien.

### *The Data Science Design Manual* - Steven S. Skienna

Un excellent livre pour découvrir la Data Science. Je l'ai lu d'une traite lorsque j'ai commencé à étudier le sujet. Skienna est un professeur brillant dont on aimerait être l'élève, qui sait traiter de sujets pointus tout en expliquant leur utilité et leur contexte.

L'ouvrage contient des explications généralement peu mathématiques mais très imagées, des exemples, des cas pratiques et des exercices. Je me surprend souvent à y revenir après avoir lu en détail l'aspect mathématique d'une méthode, afin de la recontextualiser ou de me rappeler ce que j'aurais pu éviter.

Le livre  est disponible sur Amazon à un prix modique compte tenu de la qualité de l'ouvrage (relié et couleurs), l'auteur a apparemment [mis en ligne ses cours](https://www3.cs.stonybrook.edu/~skiena/data-manual/lectures/), qui promettent d'être très intéressants.

### *[Datacamp](https://datacamp.com)*

*Learn Data Science from the comfort of your browser, at your own pace with DataCamp's video tutorials & coding challenges on R, Python, Statistics & more.*

Datacamp est un site de tutoriels sur la Data Science et l'analyse de données en général. L'abonnement est assez cher, mais baisse lors d'offres promotionnelles, notamment fin novembre. On peut faire baisser encore plus les prix en prenant un VPN et en se localisant dans un pays à faibles revenus (ex: Inde ou Ukraine, par exemple) - l'abonnement en réduction pour une année passe alors à 99$.

En pratique, il contient de nombreux tutoriels de programmation sur les méthodes de base. Je suis assez sceptique de cette technique d'apprentissage : les tutoriels sont denses et les concepts statistiques inexpliqués. 

Il conviendra donc bien à des personnes très au point d'un de vue mathématique, à l'aise en programmation mais souhaitant des exemples de bonnes pratiques en Data Science. Je conseille de prendre des notes au cours des tutoriels pour pouvoir réutiliser le code ensuite, comme ce que je fais avec ce site et [Obsidian](obsidian.md) au quotidien. Cela double toutefois la durée annoncée des tutoriels !

A noter que l'approche est parfois différente selon les tutoriels et que leur code commence à vieillir. Les "projets guidés" sont inutiles et très fastidieux de mon point de vue.

### Udacity

Udacity est un site proposant des formations rapides pour une grande diversité de métiers "tech", notamment en programmation. J'ai profité des offres "1 mois gratuit" pour explorer leur curriculum.

**A fuir**. Les exercices sont intéressants car assez ardus et les explications inexistantes, ce qui oblige l'élève que je suis à chercher par lui-même le contenu. 

L'aspect mathématique/probabiliste est très mal abordé : je ne vois pas quelle connaissance du test bootstrap un élève pourra tirer d'une vidéo de 5 minutes sur le sujet. Les forêts aléatoires sont appliquées sans que l'on explique le concept, tout est survolé, mais pour un prix stratosphérique en comparaison à Datacamp.

### Datascience Bookcamp, Leonard Apelstin

J'ai acheté ce livre qui semblait donner une explication en détail des tests de type bootstrap et de leur application en programmation. Il n'est pas encore terminé, il reste un chapitre à écrire et l'auteur ne semble pas avoir fait de mise à jour depuis mars 2020.

Le livre est centré sur l'application de concepts de machine learning : on crée l'algorithme avec [[numpy]] puis il est donné la bibliothèque spécialisée facilitant le travail. C'est un bon "pont" entre la théorie et la pratique, étant beaucoup plus détaillé que les articles de blogs sur les sujets qu'il aborde. 

On peut regretter toutefois que le code soit difficile à exécuter : la bibliothèque géographique utilisée est obsolète, le chapitre sur le NLP demande beaucoup de mémoire vive et fait facilement planter les machines avec moins de 8 Go de RAM.

L'éditeur, Manning, fait continuellement des promotions : achetez le à -50%.

### [Introduction to Linear Algebra for Applied Machine Learning with Python](https://pabloinsente.github.io/intro-linear-algebra), Pablo Insente

Un article très long récapitulant d'une manière inhabituellement vivante les principaux concepts de l'algèbre linéaire, dans la perspective de son application pour le machine learning.

Les puristes reprocheront probablement à l'auteur son manque de rigueur, mais j'ai beaucoup apprécié cet article pour remettre à jour mes connaissances sur le sujet, d'autant qu'il aborde des concepts plutôt avancés, comme les [valeurs-propres](https://pabloinsente.github.io/intro-linear-algebra#eigenthings) ou la [décomposition en valeurs singulières](https://pabloinsente.github.io/intro-linear-algebra#singular-value-decomposition).

Il ne manque que des exercices, corrigés. C'est néanmoins une bonne ressource pour ensuite aborder des ouvrages plus sérieux sur le sujet.

