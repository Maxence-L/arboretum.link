---
title : Test Bootstrap
tags : Probabilités
etat : été
---

Les tests classiques tel que le test de Student nécessitent un minimum de calculs, les hypothèses concernant la distribution théorique des données ne sont pas toujours vérifiée. Bien souvent, nous ne connaissons pas la variance théorique et une approximation par la loi des grands nombres n'est pas possible.

 L'intérêt des tests utilisant le rééchantillonage est de tirer parti de la puissance de calcul des ordinateurs modernes pour réaliser des simulations agnostiques sur la forme que peut prendre le jeu de données.
 
 ## Théorie
 
 Le Bootstrapping consiste à tester les moments d'un échantillon en le rééchantillonnant aléatoirement avec remise un grand nombre de fois et en analysant la distribution des résultats. La méthode utilisée relève des [[techniques de Monte-Carlon]] et est similaire au [[Test de Permutation]].

La procédure simule la distribution originale un grand nombre de fois à partir de l'échantillon et compare H0 avec la distribution des échantillons simulés. Si H0 se situe à l'extrémité de cette distribution, on conclut que les caractéristiques des données sont significativement différentes de ce qui est supposé dans H0.

## Exemple expliqué :

Par exemple, si l'on désire tester si la moyenne d'une variable aléatoire dont on ne dispose que d'un échantillon est égale à $x$, on va rééachantillonner avec remise 10 000 fois les données dont on dispose et calculer pour chaque échantillon sa valeur moyenne.

![](/assets/img/distribution simulée.png#center)



Une fois que les 10 000 moyennes sont calculées, on peut comparer leur distribution à la valeur $x$ de H0. Si cette valeur se trouve dans les queues de la distribution, tel que $P(moyenne >= \lvert x \rvert) <= \alpha$, on rejette le test : il est considéré comme trop improbable que la moyenne de la distribution obtenue soit uniquement due au hasard.

A noter, étant donné que la distribution de l'échantillon peut être [[skewness|asymétrique]], i est préférable de prendre la moyenne des p-valeurs et non chacune (gauche et droite).

En conséquence, on sélectionnera $P(moyenne >= \lvert x \rvert) <= \alpha$ et non $P(moyenne >= x ) <= \alpha/2$ ET $P(moyenne <= x ) <= \alpha/2$


## Application dans python[^1] :
[^1]: https://livebook.manning.com/book/data-science-bookcamp/chapter-7/v-4/73

Partons d'une population de poissons dont on mesure la taille moyenne. On désire savoir si celle-ci est significativement différente de la moyenne de 37cm observée dans la nature (H0 : moyenne = 37cm) :

```python
fish_lengths = np.array([46.7, 17.1, 2.0, 19.2, 7.9, 15.0, 43.4,
                         8.8, 47.8, 19.5, 2.9, 53.0, 23.5, 118.5,
                         3.8, 2.9, 53.9, 23.9, 2.0, 28.2])
assert fish_lengths.mean() == 27
```

On peut à présent réaliser 150k rééchantillonages avec remise et observer le résultat graphiquement :
```python
np.random.seed(0)
sample_means = [np.random.choice(fish_lengths,
                                size=20,
                                replace=True).mean()
               for _ in range(150000)]
likelihoods, bin_edges, _ = plt.hist(sample_means, bins='auto',
                                      edgecolor='black', density=True)
plt.xlabel('Binned Sample Mean')
plt.ylabel('Relative Likelihood')
plt.show()
```
![[Pasted image 9.png]]

Comme on le voit, les moyennes se sont ordonnées normalement. On peut à présent calculer la p-valeur associée à une taille de 27cm +/- 10 cm (soit la différence entre la moyenne observée et la moyenne théorique). Elle correspond en conséquence à la probabilité d'observer une moyenne égale à celle observée si la moyenne réelle de l'échantillon est bien de 37.

```python
number_extreme_values = 0
for sample_mean in sample_means:
    if not 17 < sample_mean < 37:
        number_extreme_values += 1

p_value =  number_extreme_values / len(sample_means)
print(f"P-value is approximately {p_value:.2f}")

> P-value is approximately 0.10
````

La p-valeur associée est de 0.10. On ne rejete pas l'hypothèse H0 : la taille moyenne des poissons est de 37cm, au niveau $\alpha = 0.05$

## Pourquoi un boostrap et non un test de student ?
Un test de student suppose que, bien que l'on ne connaisse pas la variance réelle de l'échantillon testé, la distribution suit une loi normale. Le bootstrap ne suit pas cette affirmation et utilise la fonction de densité de l'échantillon observée.

En pratique, le bootstrap fonctionne bien si l'on dispose d'un échantillon suffisamment grand pour ne pas avoir trop de biais dans la fonction de densité et un écart représentatif des valeurs possibles dans l'échantillon.

Dans le cas d'une échantillon très réduit, un test de permutation peut être plus représentatif.

Voir cette réponse pour plus de détails :
[Stack Overflow](https://stats.stackexchange.com/questions/128987/why-would-i-want-to-bootstrap-when-computing-an-independent-sample-t-test-how#:~:text=First%20is%20that%20bootstrap%20is,of%20the%20small%20sample%20size.&text=Both%20the%20t%2Dtest%20and,generating%20the%20data%20is%20normal.)

## Script prêt à utiliser :

```python
import numpy as np

def bootstrap_test(tested_array, H0_mean, sampling = 10_000, seed=0, print_result=True):

  """This function outputs the probability of obtaining the observed difference between the mean of the tested_array and the H0_mean, given that the HO_mean is true (P-value).
  Type hints :
    tested_array : np.array()
    HO_mean : int or float
    sampling : int
    seed : int"""

  np.random.seed(seed)

  simulated_sample_means = [np.random.choice(tested_array,
                                  size=tested_array.shape[0],
                                  replace=True).mean()
                                  for _ in range(sampling)]

  array_mean = tested_array.mean()
  interval = [array_mean - abs(array_mean - H0_mean), array_mean + abs(array_mean - H0_mean)]

  number_extreme_values = 0
  for sample_mean in simulated_sample_means:
      if not interval[0] < sample_mean < interval[1]:
          number_extreme_values += 1

  p_value =  number_extreme_values / len(simulated_sample_means)

  if print_result == True :  print(f"P-value is approximately {p_value:.2f}")
  
  return p_value

# Pour tester :
fish_lengths = np.array([46.7, 17.1, 2.0, 19.2, 7.9, 15.0, 43.4,
                         8.8, 47.8, 19.5, 2.9, 53.0, 23.5, 118.5,
                         3.8, 2.9, 53.9, 23.9, 2.0, 28.2])

bootstrap_test(fish_lengths, 37, sampling = 1000, print_result = True)
````
