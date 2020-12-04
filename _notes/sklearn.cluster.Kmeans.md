---
title: sklearn.cluster.Kmeans
tags: sklearn
etat: printemps
---

`sklearn.cluster.Kmeans` applique l'algorithme des [[K-neighbors]].

L'algorithme K-means est un algorithme de [[classification]] catégorisant les observations en fonction de leur distance au point moyen ([[Centroïde\|centroïde]]) de la classe. La catégorisation des points peut changer au fur et à mesure des itérations, jusqu'à convergence. 

#### Initialisation et usage :

On spécifie avec `n_cluster=x` le nombre de [[Centroide\|centroides]] qui génèreront des clusters en conséquence.

```python
f = Kmeans(n_clusters = 2)
```

L'algorithme est lancé ainsi :

```python
# Importation de KMeans :
from sklearn.cluster import KMeans

X = np.array([ [1, 2], [1, 4], [1, 0],
              [10, 2], [10, 4], [10, 0] ])

# Creating the classifier, with custom parameters :
cluster_model = KMeans(n_clusters=2)

# Applying the classifier to our data :
classifier = cluster_model.fit(X)

# On peut à présent classifier des nouveaux point en utilisant le modèle :
classifier.predict([0,0],[12,3])

> array([1, 0], dtype=int32)
```

Deux méthodes globales sont utilisées ici :

- `.fit(X)`: associe le modèle au paramètres préétablis et aux données fournies. Dans le cas d'une régression, par exemple, cela revient à calculer la valeur de $\beta$.
- `.predict(X)`: applique le modèle aux données en input. Toujours de la cas d'une régression, cela correspond au calcul de $\beta X$.

On peut de plus utiliser `cluster_model.fit_predict(data)`, dans quel cas on exécute l'algorithme K-means sur `data`et la méthode retourne un array correspondant aux clusters assignés au points de `data`.

On peut combiner les méthodes :

```python
clusters = KMeans(n_clusters=K).fit_predict(data)
```

#### Mesure de la précision

`k_means_model.inertia_`retourne l'[[inertie]] associée à l'exécution de l'objet Kmeans.

De même,

```python
inertia = KMeans(n_clusters=K).predict(data).inerta_
````

Exécute les KMeans et retourne l'inertie. Il est conseiller de l'inspecter graphiquement en appliquant l'algorithme pour \[n, k] clusters :
```python
k_values = range(1, 10)
inertia_values = [KMeans(k).fit(X).inertia_
                  for k in k_values]

plt.plot(k_values, inertia_values)
plt.xlabel('K')
plt.ylabel('Inertia')
plt.show()
```

![Coude](../assets/img/kmeans-coude.png#center)

