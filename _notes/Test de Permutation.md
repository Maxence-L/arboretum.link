---
title: Test de Permutation
tags : Probabilités
etat : printemps
---
Un test de permutation est une méthode de la famille du [[Test Bootstrap]] permet de comprendre si la distribution d'un échantillon est significativement différente d'un autre.

Comme tous les tests, le test de permutation tente de répondre à la question : 

> **L'effet que l'on constate existe t'il ou est-il uniquement dû à la chance ?** 

Pour répondre à cette question, on utilise deux hypothèses : H0, selon laquelle l'effet est uniquement dû à la chance, et H1, selon lequel l'effet existe. 

La probabilité $P(Effet|H0)$ correspond à la [[p-valeur]] : **la probabilité que l'on constate le résultat observé sachant qu'en réalité il n'est dû qu'au hasard, sachant H0 vraie**, les données ayant une dispersion naturelle. Si la p-valeur est faible, alors on peut conclure que l'effet observé n'est dû qu'à la chance.


## Théorie

Si l'échantillon testé est issu de la même distribution que l'autre, la différence entre les deux échantillons n'est pas sensée être significative. On va donc mélanger les deux échantillons - comme s'ils étaient issus de la même distribution - et tirer deux nouveaux échantillons un grand nombre de fois, dont on comparera les caractéristiques.

Si la répartition de notre échantillon testé est significativement différente des échantillons tirés, alors il est possible de conclure qu'il vient d'une distribution différente.

 ## Exemple expliqué

Imaginons que l'on a acheté un sac de billes contenant en grande majorité des billes bleues et quelques billes rouges. Un ami possède un sac de billes contenant en majorité des billes rouges et quelques billes bleues. Les sacs de billes viennent-ils du même vendeur ? 

Pour répondre à cette question, on va mélanger les deux sacs de billes et les diviser à nouveau un grand nombre de fois, puis comparer les proportions obtenues pour chaque itération avec la répartition rencontrée dans les sac de billes initiaux. 

Si cette répartition initiale est rencontrée régulièrement, il y a peu de chances que les sacs viennent d'un échantillon différent. Si au contraire, leur répartion est plus rare, il y une plus faible chance que cela soit soit dû au hasard, ce qui nous inciterait donc à conclure qu'ils viennent de vendeurs différents.

En pratique, on observe la proportion de billes bleues (ou la moyenne si l'on veut) et l'on réalise la différence en valeur absolue entre les deux tirages des billes mélangées. A la fin de l'itération, on observe la proportion de tirages présentent une différence plus importante que l'échantillon de départ, ce qui nous donne notre p-valeur.

## Application dans python

```python
import numpy as np
import matplotlib.pyplot as plt

def permutation_test(first_array, second_array, resampling=30000, print_result=True, seed=0):

    np.random.seed = seed

    H0_diff = abs(first_array.mean() - second_array.mean())

    total_array = np.hstack([first_array, second_array])

    mean_diffs = []

    for _ in range(resampling):
        
        np.random.shuffle(total_array)

        random_first_array = total_array[:first_array.shape[0]]
        random_second_array = total_array[first_array.shape[0]:]

        mean_diff = abs(random_first_array.mean() - random_second_array.mean())
        mean_diffs.append(mean_diff)
    
    nb_extreme_values = 0

    for min_diff in mean_diffs:
        if min_diff > abs(H0_diff):
            nb_extreme_values += 1

    p_value = nb_extreme_values / len(mean_diffs)

    if print_result : print(f"P-value is {p_value:.2f}")
    return p_value

```

#### Réferences :
https://livebook.manning.com/book/data-science-bookcamp/chapter-7/v-4/124
https://thomasleeper.com/Rcourse/Tutorials/permutationtests.html
https://allendowney.blogspot.com/2011/05/there-is-only-one-test.html
https://www.jwilber.me/permutationtest/
https://faculty.psy.ohio-state.edu/myung/personal/course/826/bootstrap_hypo.pdf