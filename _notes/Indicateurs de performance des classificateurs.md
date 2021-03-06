---
title : Indicateurs de performance des classificateurs
tags : ml-pratique
etat : printemps
toc : true
---

Comment évaluer une classification ? Selon le contexte, on le but de la classification peut être différent. Par exemple :

- Un système de justice peut chercher à établir la culpabilité d'un suspect mais en évitant au maximum de condamner un innocent.
- Un fabricant de voiture cherchera lors de son contrôle qualité des freins à identifier absolument toutes les pièces défecteuses au prix de devoir changer certaines pièces correctes mais identifiées comme à risque.
- Un médecin peut chercher à identifier les malades tout en évitant de prescrire un traitement à des personnes présentant des symptômes mais n'étant pas atteint.

Plusieurs outils existent pour aider le practicien à comprendre le résultat d'un modèle et à sélectionner les paramêtres qui correspondent à son objectif.

## La matrice de confusion
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
	<p>
  Résultat d'une classification attribuant le genre masculin à tous les individus dont la taille est supérieur à 168cm. /n 
		Source: The Data Science Design Manual, S. Skienna.
</p>
</div>

En reprenant les exemples précédemment cités :

- Dans le cas de la justice, par exemple, il peut être plus important de minimiser le nombre de **faux positifs** (innocents condamnés) que de **vrai positifs** (coupables inculpés).
- Le fabricant de voitures cherchera à minimiser le nombre de **faux négatifs** lors du contrôle qualité des freins afin d'éviter les accidents mortels. Le nombre de **vrais négatifs** sera moins important.
- Le médecin devra équilibrer le taux de **faux positifs** avec celui de **vrai positifs**, en fonction du bénéfice/risque général du traitement et de ses effets secondaires.

#### Dans sklearn :

Le module [sklearn.metrics](https://scikit-learn.org/stable/modules/classes.html?highlight=metrics#module-sklearn.metrics) contient de nombreuses mesures pour évaluer les modèles. La matrice de confusion est accessible avec `from sklearn.metrics import confusion_matrix`.

```python
from sklearn.metrics import confusion_matrix
y_true = [2, 0, 2, 2, 0, 1]
y_pred = [0, 0, 2, 2, 0, 2]

confusion_matrix(y_true, y_pred)

array([ [2, 0, 0],
        [0, 0, 1],
        [1, 0, 2] ])
````

Il existe par ailleurs une méthode pour l'afficher de manière plus "propre" : [sklearn.metrics.plot_confusion_matrix](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.plot_confusion_matrix.html#sklearn.metrics.plot_confusion_matrix). 

Voici un exemple issu du [tutoriel dédié sur le site de sklearn](https://scikit-learn.org/stable/auto_examples/model_selection/plot_confusion_matrix.html#sphx-glr-auto-examples-model-selection-plot-confusion-matrix-py) : 

![](/assets/img/normalized_confusion.png#center)


## La justesse, la précision et le rappel

On dérive de la matrice de confusion trois mesures : la justesse (*accuracy[^1]*), la précision et le rappel. Chaque mesure est [importante pour saisir l'efficacité](http://www.bouletcorp.com/2015/03/01/le-mythe-de-la-caverne/) d'un modèle de classification.

[^1]: Traduction française selon [Google, Inc](https://developers.google.com/machine-learning/crash-course/classification/accuracy?hl=fr)

### Justesse

La justesse désigne la part de prédictions correctes sur l'ensemble des prédictions réalisées par le modèle :

$$
\text { Justesse }=\frac{VP+VN}{VP+VN+FP+FN}
$$

La justesse permet d'identifier un classificateur trop aléatoire. Elle ne reflète toutefois pas la réalité lorsque les classes sont déséquilibrées. Il existe en effet de nombreux cas où l'évènement à détecter est rare et la précision seule du classificateur n'est pas suffisante.

 Par exemple, si 99% des patients venant pour une migraine n'ont rien et que 1% ont un cancer, un modèle systématiquement comme résultat "rien" aura 99% de réussite (justesse = 0.99) malgré qu'il ne réalise en réalité aucune prédiction.

### Précision

La précision (*precision*) est une mesure de base et se définit ainsi : 

$$
Précision = \frac{VP}{VP+FP}
$$

Elle correspond au nombre d'observations correctement classifiées parmi l'ensemble des prédictions.

La précision permet d'exclure les classificateurs insensibles : classifier 100% des patients comme "bien-portants"  donne une précision de 0. En effet, le modèle est alors incapable d'identifier les 1% de patients cancéreux.

Toutefois, un classificateur trop parcimonieux détectant à coup sûr seulement 10% des patients cancéreux obtiendra une précision de $\frac{0.001}{0.001+0} = 1$, alors que 90% des cas qu'il fallait classifier sont ignorés.

### Rappel 

On utilise donc comme complément le rappel (*recall*), aussi appelé *sensitivité* pour une classe $i$ se définit ainsi :

$$
Rappel = \frac{VP}{VP+FN}
$$

Il correspond à la proportion d'observations appartenant à la classe $i$ correctement détectées. 

Un rappel élevé implique que le classificateur présente peu de faux négatifs. En revanche, le rappel ne mesure pas le taux de faux positifs : il sera égal à un si le classificateur assigne tous les patients à la catégorie "cancéreux" : $\frac{0.01}{0.01+0} = 1$

> **À noter** : Si l'on a plus de deux classes, il est utile de calculer le score de rappel pour chacune d'entre elles, certaines classes pouvant être mieux identifiées que d'autres.

### Score F

Le *F-score* combine la précision et le rappel, permettant d'obtenir une mesure unique plus facilement interprétable (bien que l'on ne puisse à mon avis faire l'économie du rappel et de la précision). 

Il qui mesure la performance générale d'un classificateur. Le *F-score* est fondé sur la [moyenne harmonique](https://www.mathweb.fr/euclide/2018/08/31/les-differentes-moyennes) des deux indicateurs.

$$
\begin{aligned}
F_{1} &=\frac{2}{\frac{1}{\text { rappel }} \times \frac{1}{\text { précision }}}=2 \times \frac{\text { précision } \times \text { rappel }}{\text { précision }+\text { rappel }} \\
&=\frac{\mathrm{VP}}{\mathrm{VP}+\frac{1}{2}(\mathrm{FP}+\mathrm{FN})}
\end{aligned}
$$

Un modèle parfait présente un score F égal à 1.

### F Bêta

La moyenne harmonique du score F peut être pondérée afin de donner plus d'importance à un indicateur. On parle alors de score *F-beta* ($F_\beta$). Il se calcule ainsi :

$$
\begin{aligned}
F_{\beta} &=\left(1+\beta^{2}\right) \times \frac{\text { précision } \times \text { rappel }}{\left(\beta^{2} \times \text { précision }\right)+\text { rappel }} \\ \\ \\
&=\frac{\left(1+\beta^{2}\right) VP}{\left.\left(1+\beta^{2}\right) V P+\beta^{2} FN+FP\right)}
\end{aligned}
$$

> Le choix de $\beta$ déterminera le poids que l'on souhaite donner à chacune des deux mesures. Un $\beta$ inférieur à 1 privilégiera la précision (si $\beta =0$ , alors $F_\beta = Précision$ et supérieur à 1 le rappel.

En pratique lors de la sélection de modèle, si l'on veut **minimiser les faux positifs**, on sélectionnera un $\beta$ inférieur à 1. Une forte précision implique un faible taux de faux positifs.

En revanche, si l'on veut **miniser les faux négatifs**, on sélectionnera un $\beta$ supérieur à 1. Un fort rappel implique un faible taux de faux négatifs.

> Remarques :

> - Comme on l'a vu dans l'exemple, la justesse est trompeuse lorsque les classes ont des tailles significativement différentes.
>  
> - Le rappel est égal à la justesse si les classes sont équilibrées. Il peut donc être utile de rééquilibrer les classes lors de l'entrainement d'un modèle.
>  
> - Une forte précision est difficile à atteindre si les classes sont déséquilibrées.
>  
> - Le score F est plus synthétique que chaque mesure, mais l'examination des quatre mesures est nécessaire à l'évaluation d'un classificateur.

Une astuce pour améliorer la précision au prix d'une baisse du rappel et de prévoir une option "je ne sais pas" pour le modèle, qui lui permet de ne classifier que les cas faciles et non les cas difficiles pour lesquels la probabilité de faux positif est plus élevée. 

Par exemple, un patient proche de la limite de décision obtiendra une réponse de type "risque de cancer" plutôt que "cancer", limitant ainsi le risque de mauvais diagnostic et incitera le praticien à effectuer des tests plus détaillés.

#### Dans sklearn :

Un [excellent guide](https://scikit-learn.org/stable/modules/model_evaluation.html#metrics-and-scoring-quantifying-the-quality-of-predictions) existe pour présenter les mesures disponibles dans le module `sklearn.metrics`.

Habituellement, on utilisera `classification_report()`qui produit les principales mesures présentées ci-dessus :

```python
from sklearn.metrics import classification_report
y_true = [0, 1, 2, 2, 0]
y_pred = [0, 0, 2, 1, 0]
target_names = ['class 0', 'class 1', 'class 2']

print(classification_report(y_true, y_pred, target_names=target_names))
```
```
>             precision    recall  f1-score   support

     class 0       0.67      1.00      0.80         2
     class 1       0.00      0.00      0.00         1
     class 2       1.00      0.50      0.67         2

    accuracy                           0.60         5
   macro avg       0.56      0.50      0.49         5
weighted avg       0.67      0.60      0.59         5
````


## Courbe ROC

Les algorithmes de classification possèdent généralement des hyperparamètres pouvant être ajustés par l'utilisateur afin de s'approcher des résultats désirés.

Par exemple, dans le cas d'une [[Régression logistique\| régression logistique]], à partir de quelle limite $p$ classifie-t-on l'observation dans la classe $i$ ?

La courbe ROC (*Receiver-Operator Characteristic*) permet de visualiser le résultat issu des ajustements possibles afin de choisir le paramètre convenant à l'utilisateur.

![](../assets/img/roc_curve.png#center)

<div align="center">
	<p>
  Courbe ROC. Source : utilisateur MartinThoma, Wikipedia
	</p>
</div>

La courbe ROC représente, pour chaque valeur que prend le paramètre du modèle, le taux de vrai et de faux positifs. Si les classes sont parfaitement séparées et que le classificateur se trouve entre les deux, on obtient un point $(FP,VP) = (0,1)$ situé en haut à gauche du cadran correspondant à un classificateur parfait.

En conséquence, plus le coude de la courbe se rapproche de ce point, plus le classificateur est performant. Cela permet par ailleurs de comparer les méthodes de classification, comme sur le graphique. La droite diagonale correspond à un classificateur aléatoire.

La courbe ROC représente le dilemme rencontré par le critère de classification : si l'on est plus généreux pour augmenter le taux de vrai positif, on risque en conséquence d'augmenter le taux de faux positifs. On peut donc visualiser la relation entre les deux valeurs et trouver la valeur qui représente le meilleur compromis pour l'utilisateur.

### *AUC : Area under curve*

L'AUC correspond à l'aire située sous la courbe et est utilisée pour mesurer la qualité de la prédiction du classificateur ou le comparer à d'autre. Le classificateur parfait possède une AUC de $100\% \times 100\% = 1$ : plus le classificateur est performant, plus son AUC se rapproche de 1.

#### Dans sklearn :

On utilisera [`roc_curve()`](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html#sklearn.metrics.roc_auc_score) :

```python
# Import necessary modules
from sklearn.metrics import roc_curve
from sklearn.linear_model import LogisticRegression

# Compute predicted probabilities: y_pred_prob
y_pred_prob = logreg.predict_proba(X_test)[:,1]

# Generate ROC curve values: fpr, tpr, thresholds
fpr, tpr, thresholds = roc_curve(y_test, y_pred_prob)

# fpr : Taux de faux positifs
# tpr : Taux de vrai positifs
# thresholds : Critère de décision

# Plot ROC curve
plt.plot([0, 1], [0, 1], 'k--')
plt.plot(fpr, tpr, label='Logistic Regression')
plt.xlabel('False Positive Rate')
plt.ylabel('True Positive Rate')
plt.title('ROC Curve')
plt.show()
```

![](/assets/img/roc_curve_plt.png#center)

L'utilisateur parcimonieux de son temps pourra exploiter [`plot_roc_curve()`](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.plot_roc_curve.html) pour s'épargner la manipulation sur matplotlib.

Un exemple de courbe ROC avec [[Evaluation des modèles en pratique\|validation croisée]], issue d'un [guide sur le site de sklearn](https://scikit-learn.org/stable/auto_examples/model_selection/plot_roc_crossval.html#sphx-glr-auto-examples-model-selection-plot-roc-crossval-py) :

![](/assets/img/roc_cross_valid.png#center)

Le score AUC se calculer avec `roc_auc_score(y_test, y_pred_prob)`.

On peut par ailleurs calculer automatiquement les scores AUC lors d'une validation croisée :

```python
# Import necessary modules
from sklearn.model_selection import cross_val_score
from sklearn.metrics import roc_auc_score

# Compute predicted probabilities: y_pred_prob
y_pred_prob = logreg.predict_proba(X_test)[:,1]

# Compute and print AUC score
print("AUC: {}".format(roc_auc_score(y_test, y_pred_prob)))

# Compute cross-validated AUC scores: cv_auc
cv_auc = cross_val_score(logreg, X, y, cv=5, scoring='roc_auc')

# Print list of AUC scores
print("AUC scores computed using 5-fold cross-validation: {}".format(cv_auc))

> AUC: 0.8254806777079764
> AUC scores computed using 5-fold cross-validation: [0.80148148 0.8062963  0.81481481 0.86245283 0.8554717 ]
````


