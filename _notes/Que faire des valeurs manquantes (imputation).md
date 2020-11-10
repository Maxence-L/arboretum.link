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

Le module [sklearn.impute](https://scikit-learn.org/stable/modules/classes.html#module-sklearn.impute) sera utile pour l'aspect pratique de cette étape.

## Imputation simple

### Mode, moyenne, médiane

La méthode la plus simple d'imputation consiste à remplacer une valeur manquante par la valeur la plus fréquente (dans le cas d'une variable qualitative), la moyenne ou la médians (variable quantitative).

**Cette méthode fonctionne bien pour des techniques de régression mais pose problème pour d'autres cas, en créant un groupe important de valeurs autour d'un point précis.**

![Effet de l'imputation](../assets/img/imputation-vm.png#center)

<div align="center">
  Effet de l'imputation sur la distribution des données [1]
</div>

Voir [[sklearn.impute.SimpleImputer() - Imputation simple\|sklearn.impute]] pour la mise en oeuvre pratique.

### Estimation

Plusieurs méthodes d'estimation sont possibles pour remplacer les valeurs manquantes. L'idée est de chercher des observations complêtes proches pour servir de réference aux valeurs manquantes.

- La régression linéaire

![](../assets/img/regression-imputation.png#center)

<div align="center">
  Imputation par la moyenne et par une régression [1]
</div>

- [[Arbre de décision]]

- Classification (dans le cas de variable qualitative) - par [[ADL]], par exemple.

- [[K-Means]] : On crée des groupes d'individus et l'on impute à la valeur manquante la moyenne de son groupe. (utiliser [sklearn.impute.KNNImputer](https://scikit-learn.org/stable/modules/generated/sklearn.impute.KNNImputer.html))

## Imputation multiple

L'imputation multiple consiste à remplacer chaque valeur manquante par plusieurs valeurs plausibles. on obtient ainsi plusieurs tables de données complêtées sans valeur manquante et l'on ainsi comparer le résultat des analyses statistiques en fonction de la méthode d'imputation.

Voir [sklearn.impute.IterativeImputer](https://scikit-learn.org/stable/modules/generated/sklearn.impute.IterativeImputer.html#sklearn.impute.IterativeImputer) pour une application pratique.

#### Références
[1] *Data Mining et Statistique Décisionnelle - L'intelligence des données*, Stéphane Tuffery, voir p. 46.