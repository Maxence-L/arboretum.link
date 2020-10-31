---
title: Manipuler les données regroupées en une seule colonne
tags: pandas
etat : hiver
---

Il peut arriver que qu'une colonne d'un [[dataframe]] ou d'une série colonnes contiennent plusieurs informations que l'on souhaite séparer lors de la préparation des données. 

Par exemple, une colonne peut contenir une suite d'observations comme `Homme, 20 ans`, `Femme, 45 ans`, `Femme, 15 ans`, etc. On peut aussi avoir une colonne de type [[liste]], qu'il faudra répartir sur plusieurs colonnes.