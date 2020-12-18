Cette phrase de code active le code situé en dessous si le fichier source du programme est exécuté en temps que programme principal.

### Pourquoi ?

Lorsque python lit un fichier source, il initialise certaines variables qui seront relatives à ce fichier. Parmi celles-ci, '[[__main__]]' est assignée à [[__name__]] si le fichier-source en question est le fichier principal. 

Cela permet de différencier l'exécution d'un code en fonction de l'usage qui en est fait.

#### Exemple :

fichier foo.py
```python
print("before import")
import math

print("before functionA")
def functionA():
    print("Function A")

print("before functionB")
def functionB():
    print("Function B {}".format(math.sqrt(100)))

print("before __name__ guard")
if __name__ == '__main__':
    functionA()
    functionB()
print("after __name__ guard")
```

Si foo.py est exécuté comme fichier principal, alors il exécutera function(A) function(B).

> À noter :
> if __name__ == '\_\_main__': doit toujours être à la fin du fichier**