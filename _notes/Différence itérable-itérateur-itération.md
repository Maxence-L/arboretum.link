---
title: Différence itérable-itérateur-itération
tags : stdlib
etat: hiver
---

- L'**itération** est une opération consistant à agir sur un élément après l'autre, séquentiellement.
- Un **itérable** est un objet contenant une méthode *[[__iter__]]* retournant un **itérateur** ou pouvant être utilisé avec un index séquentiel partant de zéro et donnant *IndexError* lorsqu'il n'est plus valide. En résumé, un **itérable** est un objet permettant d'obtenir un **itérateur**.
- Un **itérateur** est un objet contenant une méthode *[[__next__]]* et sur lequel on peut procéder à des **itérations**. 