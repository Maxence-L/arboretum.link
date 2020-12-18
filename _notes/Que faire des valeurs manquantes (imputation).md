---
title: Que faire des valeurs manquantes (imputation)
tags: ml-pratique
etat: hiver
---
Les valeurs manquantes d'une observation posent problème pour son utilisation et son évaluation dans un modèle. Elles doivent être remplacées ou supprimées.

Si une variable comprend un grand nombre de valeurs manquantes, il convient d'interroger un expert métier sur l'origine de cette perte d'information, qui peut, en soit, révèler une information.  

Plusieurs options sont disponibles pour résoudre le problème :

- Ne pas utiliser la variable concernée par un grand nombre de valeurs manquantes, si son usage n'est pas indispensable, ou la remplacer par une variable proche mais sans valeur manquante.

- Remplacer la valeur manquante par une valeur déterminée, soit statistiquement soit par une source externe (aussi appelé *imputation*).

- Traiter la valeur manquante comme une valeur à part entière, contenant une certaine information. Dans le cas des variables numérique, on peut les découper en classe et y ajouter une classe "valeur manquante"

Le module [sklearn.impute](https://scikit-learn.org/stable/modules/classes.html#module-sklearn.impute) sera utile pour l'aspect pratique de cette étape. [[sklean]] propose un excellent tutoriel sur son site : [Imputation of missign values](https://scikit-learn.org/stable/modules/impute.html)

## Imputation simple

### Mode, moyenne, médiane

La méthode la plus simple d'imputation consiste à remplacer une valeur manquante par la valeur la plus fréquente (dans le cas d'une variable qualitative), la moyenne ou la médians (variable quantitative).

**Cette méthode fonctionne bien pour des techniques de régression mais pose problème pour d'autres cas, en créant un groupe important de valeurs autour d'un point précis.** 

Dans le cas des K-Means, on risque par conséquent de se retrouver avec un cluster d'observations comportant des valeurs manquantes ayant toutes la même valeur imputée, donc très proches.

![Effet de l'imputation](../assets/img/imputation-vm.png#center)

<div align="center">
	<p>
  Effet de l'imputation sur la distribution des données [1]
</p>
</div>

Voir [[sklearn.impute.SimpleImputer() - Imputation simple\|sklearn.impute]] pour la mise en oeuvre pratique.

## Imputation multivariée

### Estimation

Plusieurs méthodes d'estimation multivariées sont possibles pour remplacer les valeurs manquantes. L'idée est de chercher des observations complêtes proches pour servir de réference aux valeurs manquantes, ce qui évite l'effet d'aggrégation autour de la moyenne.

- La régression linéaire

![](../assets/img/regression-imputation.png#center)

<div align="center">
	<p>
  Imputation par la moyenne et par une régression [1]
</p>
</div>

- [[Arbre de décision]]

- Classification (dans le cas de variable qualitative) - par [[ADL]], par exemple.

- [[K-neighbors]] : On crée des groupes d'individus et l'on impute à la valeur manquante la moyenne de son groupe. (utiliser [sklearn.impute.KNNImputer](https://scikit-learn.org/stable/modules/generated/sklearn.impute.KNNImputer.html))

### Estimation itérative

La méthode [sklearn.impute.IterativeImputer](https://scikit-learn.org/stable/modules/generated/sklearn.impute.IterativeImputer.html#sklearn.impute.IterativeImputer)  consiste à utiliser l'ensemble des variables complêtes pour imputer les valeurs manquantes d'une variable, puis à utiliser l'ensemble des variables (y compris la variable ayant reçu de nouvelles valeurs) pour estimer les valeurs manquantes de la prochaine variable.

Un peut faire cette opération pour $n$ itérations, afin de lisser les divergences liées à l'ordre dans lesquelles les variables sont estimées. L'utilisation est expliquée sur le site [scikit-learn](https://scikit-learn.org/stable/modules/impute.html#multivariate-feature-imputation).




#### Références
[1] *Data Mining et Statistique Décisionnelle - L'intelligence des données*, Stéphane Tuffery, voir p. 46.