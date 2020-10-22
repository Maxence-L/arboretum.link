---
title: Chaines de markov
tag: Probabilités
etat: hiver
---

Les chaines de markov sont des modèles mathématiques modélisant pour un objet le fait de passer d'un état à un autre.

Par exemple, un humain peut avoir plusieurs états possibles : manger, dormir, travailler, se divertir. Chaque heure, on inspecte dans quel état est l'humain. Une chaîne de Markov donnera la probabilité de passer d'un de ces états à un autre.

Un exemple de chaîne de Markov pour deux états possibles :

![Source setosa.io](/assets/img/chaine_markov_setosa.io.png#center)

Dans ce cas, on quatre transitions sont possibles : A vers B, B vers A, A vers A, B vers B. On peut faire une carte des probabilités $$P(Y \vert X)$$ (où X est l'état de départ et Y l'état d'arrivée) de transitions entre états, sous la forme d'une [[matrice de passage]] :

$$X\downarrow Y \to$$|A|B|
-|-|-
A|$$p_{a,a}$$|$$p_{a,b}$$|
B|$$p_{b,a}$$|$$p_{b,b}$$|
