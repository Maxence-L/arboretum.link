---
title: np.flatnonzero()
tags : Numpy
etat: hiver
---

`np.flatnonzero()` retourne les indices des valeurs différentes de 0 dans un array numpy 1D.

On peut sélectionner ces valeurs de la manière suivante :

```python
import numpy as np

non_zero_indices = np.flatnonzero(np_vector)
non_zero_vector = np_vector[non_zero_indices]
````

