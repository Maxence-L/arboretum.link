---
title: __repr__
tags: stdlib
etat : printemps
---

__repr__() , pour "représentation", est une [[Méthode spéciale\|méthode spéciale]] similaire mais pas équivalente à [[__str__()]]. Elle est appelée lorsque l'utilisateur tape le nom de l'objet, typiquement dans la console :

```python
import datetime
today = datetime.date.today()
today

'datetime.date(2017, 2, 2)'
```

Définir __repr__() dans un classe permet de contrôler l'objet qui est retourné lorsque l'utilisateur appelle une instance de l'objet. Par exemple : 

```python
class Car:
    def __init__(self, color, mileage):
        self.color = color
        self.mileage = mileage

    def __repr__(self):
        return '__repr__ for Car'
```

Cette méthode spéciale est particulièrement utile pour le débogage : dans le cas de [[datetime]], elle renvoie les paramètres qui permettent d'obtenir l'instance spécifique de la classe.

Il est donc important que la valeur retournée soit **claire** et ne comporte **pas d'ambiguité**.

Quel lien avec [[__str__()]] ? Lorsque [[__str__()]] n'est pas disponible, le système se repose sur la valeur de __repr__()

### astuce

Comment obtenir facilement une liste des arguments passés dans l'instance de la classe, pour les reproduire dans __repr__() ?

```python
def __repr__(self):
	class_args = list(self.__dict__.values()) # list instance args value
	return 'class_name('+' ,'.join(str(i) for i in class_args) # returns a clean string
```

Voir aussi :
https://dbader.org/blog/python-repr-vs-str