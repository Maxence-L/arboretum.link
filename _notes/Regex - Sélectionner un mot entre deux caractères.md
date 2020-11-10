---
title : Regex - Sélectionner un mot entre deux caractères
tags : Regex
etat : hiver
---

On veut sélectionner le mot ```pomme``` dans la phrase ```La [pomme] est tombée de l'arbre.```

Pour ce faire, on utilise l'expression régulière ```(?<=\[)(.*?)(?=\])``` 