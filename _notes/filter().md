---
title: filter()
tags : stdlib
etat : hiver
---
La méthode filter() construit un itérateur à partir d'un [[Différence itérable-itérateur-itération\|itérateur]]. L'itérateur ainsi créé ne renvoie que les éléments de l'itérable remplissant  les conditions précisé en paramètre.
_filter(function, iterable)_

```python
x = [1,2,3,4,5,6,7,8]

def pair(x) :
  if x%%2 = 0:
    return True
  else:
    return False
 
liste_paire = filter(pair, x)
set(liste_paire)

# {8, 2, 4, 6}
```