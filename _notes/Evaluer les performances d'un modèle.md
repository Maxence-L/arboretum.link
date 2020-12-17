---
title : Evaluer les performances d'un modèle
tags : ml-pratique
etat : printemps
---

## Le cas de la classification

Comment évaluer une classification ? Plusieurs outils existent pour aider le practicien à comprendre le résultat d'un modèle.

### La matrice de confusion
Les possibilités pour un modèle de classification sont au nombre de quatre :

- Faux positif (*False positive*)  : on classifie un patient comme bien portant alors qu'il est bien malade. Correspond à une erreur de type I.
- Faux négatif (*False negative*) : on classifie un patient comme bien malade alors qu'il est bien portant. Correspond à une erreur de type II.
- Vrai positif (*True positive*) : on classifie un patient comme malade et il est effectivement malade.
- Vrai négatif (*True negative*) : on classifie un patient comme bien portant et il est bien portant.

Ces possibilités sont traditionnellement représentées dans une *matrice de confusion* :

|-|Prédit : vrai|Prédit : faux|
|-|-|-|
|Réel : vrai|vrai positif|faux négatif (erreur de type I)|
|Réel : faux|faux positif (erreur de type II)|vrai négatif|

![](/assets/img/confusion-graph.png#center)
<div align="center">
  Résultat d'une classification attribuant le genre masculin à tous les individus dont la taille est supérieur à 168cm. Source: The Data Science Design Manual, S. Skienna.
</div>

Selon les nécessités de l'étude, on peut chercher à optimiser certains éléments plutôt que d'autres :
- Dans le cas de la justice, par exemple, il peut être plus important de minimiser le nombre de faux positifs (innocent condamnés) que de vrai positifs. 
- Pendant une épidémie, on cherchera à minimiser le nombre de faux négatifs afin d'éviter que la maladie se répande.

### La justesse, la précision et le rappel

On dérive de la matrice de confusion trois mesures : la justesse (*accuracy[^1]*); la précision et le rappel. Chaque mesure est [importante pour saisir l'efficacité](http://www.bouletcorp.com/2015/03/01/le-mythe-de-la-caverne/) d'un modèle de classification.

[^1]: Traduction française selon [Google, Inc](https://developers.google.com/machine-learning/crash-course/classification/accuracy?hl=fr)

#### Justesse
La justesse désigne la part de prédictions correctes sur l'ensemble des prédictions réalisées par le modèle :

$$
\text { Justesse }=\frac{VP+VN}{VP+VN+FP+FN}
$$

La justesse permet d'identifier un classificateur trop aléatoire. Elle ne reflète toutefois pas la réalité lorsque les classes sont déséquilibrées. Il existe en effet de nombreux cas où l'évènement à détecter est rare et la précision seule du classificateur n'est pas suffisante.

 Par exemple, si 99% des patients venant pour une migraine n'ont rien et que 1% ont un cancer, un modèle systématiquement comme résultat "rien" aura 99% de réussite (justesse = 0.99) malgré qu'il ne réalise en réalité aucune prédiction.

#### Précision
La précision est une mesure de base et se définit ainsi : 

$$
Précision = \frac{VP}{VP+FP}
$$

Elle correspond au nombre d'observations correctement classifiées parmi l'ensemble des prédictions.

La précision permet d'exclure les classificateurs insensibles : classifier 100% des patients comme "bien-portants"  donne une précision de 0. En effet, le modèle est alors incapable d'identifier les 1% de patients cancéreux.

Toutefois, un classificateur trop parcimonieux détectant à coup sûr seulement 10% des patients cancéreux obtiendra une précision de $\frac{0.001}{0.001+0} = 1$, alors que 90% des cas qu'il fallait classifier sont ignorés.

#### Rappel 

On utilise donc comme complément le rappel (*recall*), aussi appelé *sensitivité* pour une classe $i$ se définit ainsi :

$$
Rappel = \frac{VP}{VP+FN}
$$

Il correspond à la proportion d'observations appartenant à la classe $i$ correctement détectées. 

Un rappel élevé implique que la classificateur présente peu de faux négatifs. En revanche, le rappel ne mesure pas le taux de faux positifs : il sera égal à un si le classificateur assigne tous les patients à la catégorie "cancéreux" : $\frac{0.01}{0.01+0} = 1$

> **À noter** : Si l'on a plus de deux classes, il est utile de calculer le score de rappel pour chacune d'entre elles, certaines classes pouvant être mieux identifiées que d'autres.

#### Score F

Le *F-score* combine la précision et le rappel, permettant d'obtenir une mesure unique plus facilement interprétable (bien que l'on ne puisse à mon avis faire l'économie du rappel et de la précision). 

Il qui mesure la performance générale d'un classificateur. Le *F-score* est fondé sur la [moyenne harmonique](https://www.mathweb.fr/euclide/2018/08/31/les-differentes-moyennes) des deux indicateurs.

$$
\begin{aligned}
F_{1} &=\frac{2}{\frac{1}{\text { rappel }} \times \frac{1}{\text { précision }}}=2 \times \frac{\text { précision } \times \text { rappel }}{\text { précision }+\text { rappel }} \\
&=\frac{\mathrm{VP}}{\mathrm{VP}+\frac{1}{2}(\mathrm{FP}+\mathrm{FN})}
\end{aligned}
$$

Un modèle parfait présente un score F égal à 1.

##### $F Bêta
La moyenne harmonique du score F peut être pondérée afin de de donner plus d'importance à un indicateur. On parle alors de score *F-beta* ($F_\beta$). Il se calcule ainsi :

$$
\begin{aligned}
F_{\beta} &=\left(1+\beta^{2}\right) \times \frac{\text { précision } \times \text { rappel }}{\left(\beta^{2} \times \text { précision }\right)+\text { rappel }} \\ \\ \\
&=\frac{\left(1+\beta^{2}\right) VP}{\left.\left(1+\beta^{2}\right) V P+\beta^{2} FN+FP\right)}
\end{aligned}
$$

> Le choix de $\beta$ déterminera le poids que l'on souhaite donner à chacune des deux mesures. Un $\beta$ inférieur à 1 privilégiera la précision (si $\beta =0$ , alors $F_\beta = Précision$ et supérieur à 1 le rappel.

En pratique lors de la sélection de modèle, si l'on veut **minimiser les faux positifs**, on sélectionnera un $\beta$ inférieur à 1. Une forte précision implique un faible taux de faux positifs.

En revanche, si l'on veut **miniser les faux négatifs**, on sélectionnera un $\beta$ supérieur à 1. Un fort rappel implique un faible taux de faux négatifs.

> À noter :
> - La justesse est trompeuse lorsque les classes ont des tailles significativement différentes.
>  
> - Le rappel est égal à la justesse si les classes sont équilibrées. Il peut donc être utile de rééquilibrer les classes lors de l'entrainement d'un modèle.
>  
> - Une forte précision est difficile à atteindre si les classes sont déséquilibrées.
>  
> - Le score F est meilleur que chaque mesure, mais les quatres mesures sont nécessaire à l'évaluation d'un classificateur.

Une astuce pour améliorer la précision au prix d'une baisse du rappel et de prévoir une option "je ne sais pas" pour le modèle, qui lui permet de ne classifier que les cas faciles et non les cas difficiles pour lesquels la probabilité de faux positif est plus élevée. 

Par exemple, un patient proche de la limite de décision obtiendra une réponse de type "risque de cancer" plutôt que "cancer", limitant ainsi le risque de mauvais diagnostic.






