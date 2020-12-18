---
title: Correction de Bonferroni
tags: Probabilités
etat : hiver
---

La correction de Bonferroni vise à baisser la valeur alpha afin de prendre en compte l'accroissement des [[Erreur de type 1\|erreurs de type 1]] possibles du fait de la multiplication des expériences. 

Compte-tenu de la nature de la [[p-valeur]], répèter les expériences peut permettre de trouver une valeur significativement différente de l'hypothèse H0, alors que l'hypothèse H0 est vraie. 

En particulier, si la valeur $\alpha$ est = 0.05, alors en moyenne sur 100 expériences, 5 d'entre elles auront un résultat significativement différent.

La correction de Bonferonni préconise d'utiliser une valeur $\alpha$ égale à la valeur alpha initiale, divisée par le nombre d'expériences, lorsque l'on cherche des occurrences significativement différentes.

Par exemple, si la moyenne nationale par classe au bac philo est de 12/20, si l'on échantillonne 100 classes, certaines auront des résultats élevés dont la p-valeur est > à 0.05 du fait du hasard, sans que cela soit dû à la capacité des élèves ou à des méthodes d'enseignement particulièrement efficace. Cela pourrait être dû, par exemple, au fait que le professeur a particulièrement bien traité le sujet demandé au bac.

Si l'on utilise $\alpha = 0.5$, alors on considérera qu'une classe a eu des résultats significativement différents si sa p-valeur est égale à $\frac{\alpha}{100}$, soit 0.0005. 

Un point à noter : il est nécessaire d'utiliser le nombre d'expériences *prévues* et non *réalisées*. Autrement, un comportement opportuniste peut consister à adapter la valeur $\alpha$ à chaque expérience, en la baissant au fur et à mesure que les expériences sont réalisées.