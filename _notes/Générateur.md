---
title: Générateur
tags : stdlib
etat : hiver
---
Les générateurs sont des objets **[[Différence itérable-itérateur-itération\|itérables]]** qui ne gardent pas les données en mémoire mais les **génèrent** au fur et à mesure que l'on y accède. De ce fait, ils ne sont utilisable qu'une fois.

```python
my_generation = ((x*x) for x in range(3))
for i in my_generation:
  print(i)
# 0
# 1
# 4
```