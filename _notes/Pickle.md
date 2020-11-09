---
title: Pickle
tags : stdlib
etat : été
---
[Pickle](https://docs.python.org/3/library/pickle.html)  est un module permettant de [sérialiser](https://fr.wikipedia.org/wiki/S%C3%A9rialisation) des objets pythons en vue de leur conservation et leur réutilisation à plus long terme que la seule durée de la session. 

La sérialisation de l'objet sont une forme d'une chaine de caractères permet à python de reconstruire l'objet lors d'une session ultérieure, ou de le transmettre à un autre script.

## Utilisation
Quand utiliser pickle ? Plusieurs cas de figures classiques[^1] :

- Enregistrer l'état d'un programme afin de le redémarrer là où l'on s'est arrêté (comme une sauvegarde de jeu vidéo, par exemple)

- Transmettre des données d'un programme à un autre situé à un autre point du réseau ou s'exécutant en même temps.
- Enregistrer des objets Python dans une base de données
- Convertir un objet python en chaîne de caractères (*string*), de façon à l'enregistrer dans un dictionnaire, pour des buts de cache, par exemple.

Des données *pickled* pouvant contenir un programme nocif pour le système, l'envoi de données sous ce format doit se faire de manière sécurisée, c'est à dire en s'assurant qu'elles ne puissent pas être modifiées pendant le transport[^2].

[^1]:https://stackoverflow.com/questions/3438675/common-use-cases-for-pickle-in-python
[^2]:https://blog.nelhage.com/2011/03/exploiting-pickle/
## Fonctionnement
### pickle.dump() : sauvegarde d'un objet
Un fois le module importé, on peut sérialiser les objets manipulés par Python en appelant la méthode `pickle.dump(objet, fichier)` où `fichier`correspond à la destination de stockage de l'objet sérialisé. Il s'agit d'un [[open()|fichier ouvert]] par Python.

```python
import pickle

# On définit l'objet et le nom du fichier de stockage
my_obj = {'a':1,'b':2,'c':3}

#On ouvre le fichier de stockage en mode écriture ('wb')
file = open('saved_data.pkl', 'wb')

# On peut à présent sauver l'objet
pickle.dump(my_obj,file)

# On ferme le fichier
file.close()
```

Le type de fichier '.pkl' corrrespond à un fichier créé par Pickle[^3]. On utilise comme mode d'ouverture `wb`, ce qui correspond à *write raw_binary* un format ne comprenant pas de codage unicode. En conséquence, il faut[^4] utiliser `rb`, soit *read raw binary*, sinon l'on rencontrera une erreur de décodage `UnicodeDecodeError: 'utf-8' codec can't decode byte 0x80 in position 0: invalid start byte`.

[^3]:https://fileinfo.com/extension/pkl
[^4]: https://stackoverflow.com/questions/32957708/python-pickle-error-unicodedecodeerror

### pickle.load() : chargement d'un objet
Inversement, on peut dé-sérialiser un objet et le charger dans la mémoire en appelant `pickle.load(fichier)` sur un [[open()|fichier ouvert]].

```python
# On reprend l'exemple précédent
# On ouvre le fichier sauvegardé en mode lecture
file = open('saved_data.pkl', 'rb')

# On charge le fichier ouvert dans my_loaded_obj
my_loaded_obj = pickle.load(file)

my_loaded_obj

> {'a':1,'b':2,'c'3}
````

### Modèles sklearn
On peut utiliser pickle pour [[sklearn#Sauvegarde du modèle|enregistrer]] des modèles [[sklearn]] préalablement entraînés pour une réutilisation ultérieure. [L'aide en ligne](https://scikit-learn.org/stable/modules/model_persistence.html#model-persistence) explique plus spécifiquement la procédure.



