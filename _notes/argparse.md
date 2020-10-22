---
title : argparse
tags : stdlib
toc : true
etat : été
---

Argparse est un module de python permettant au programme d'interagir avec la ligne de commande. on peut, par exemple, passer des arguments et spécifier quelle fonction on désire exécuter.

## Installation
Le module s'appelle `argparse` et est inclus dans la bibliothèque standard de python[^1].

[^1]: https://docs.python.org/3/library/argparse.html#name-or-flags

```python
import argparse
````

## Initialisation
On initialise un objet de classe `.ArgumentParser()`:
```python
parser = argparse.ArgumentParser()
````

[Il est possible de passer des options](https://docs.python.org/3/library/argparse.html#argumentparser-objects) dans `.ArgumentParser()`, qui vont notamment définir le mode de fonctionnement de la fonction `--help`.


## Paramétrage

L'objet `parser` peut traiter les commandes définies à l'avance. Pour cela, on ajoute des arguments possibles avec `parser.add_argument()`. 

[De nombreuses options sont disponibles](https://docs.python.org/3/library/argparse.html#argumentparser-objects), les plus courantes sont les suivantes :

- Les *arguments positionnels* sont des arguments attendus sans que l'on donne leur nom. Ils se définissent simplement :

```python
parser.add_argument('name')
parser.add_argument('age')

args = parser.parse_args()

print(f'{args.name} is {args.age} years old')
```

```bash
positional_arg.py Peter 23
> Peter is 23 years old
````

- Les *arguments optionnels* sont définis en ajoutant le préfixe `--`. Ils peuvent bénéficier de commandes raccourcies en une lettre, avec `-`comme préfixe (ex : `-o, --options`). Il est nécessaire de taper leur nom en ligne de commande si l'on veut les utiliser.


- *required = True* permet de définir des arguments optionnels obligatoires.


- *default* : la valeur par défaut à attribuer à cet argument. On peut le coupler avec [[configparse::Créer un fichier de configuration avec ConfigParse]] 🔥 pour gérer plus facilement la configuration du programme, et laisser à l'utilisateur le pouvoir de la changer (ex : `default=config['global']['email']`).


- *dest* : donne un nom interne à l'argument différent du nom utilisé pour l'appeler. Par exemple, si l'on précise `'-n', dest = 'now'` on utilisera `-n` pour l'argument en ligne de commande mais on fera référence à sa valeur comme `args.now`.


- *help* : Description à afficher pour cet argument si l'on appelle la commande `--help`. (ex : `help='bla bla'`)


- *action* : Explique à `ArgumentParser` que faire si l'on a entré en ligne de commande l'argument. Les plus utiles [parmi d'autres](https://docs.python.org/3/library/argparse.html#argumentparser-objects) :


	- *store* enregistre la valeur passée. C'est l'action par défaut
	
	
	- *store_true* ou *store_false* enregistre `True` ou `False` si l'on a passé l'argument. Cela permet de créer des switchs.


- *type* : Cette option détermine le type d'argument passé. Si l'argument est différent du type précisé, le programme renverra une erreur. Sinon, il enregistrera l'argument au type donné.


On peut trouver d'excellents exemples [ici](http://zetcode.com/python/argparse/).

## Accès

La méthode `parse_args()` est utilisée pour - parser les arguments 🤯. 

L'appeler inspectera la ligne de commande et traitera les arguments en fonction du paramétrage de l'objet `parser`. On enregistre le résultat dans un objet :

```python
args = parser.parse_args()
```

On peut aussi passer des arguments directement, lors du développement, par exemple :
````
args = parser.parse_args(['run', 'myfile'])
````

Les arguments traités par `parse_args()` sont accessibles par leur nom (`args.nom`) :

```python
import argparse

parser = argparse.ArgumentParser()
   
parser.add_argument('-n', dest='now', action='store_true', help="shows now")

args = parser.parse_args()

# we can refer to the flag
# by a new name
if args.now:

    now = datetime.datetime.now()
    print(f"Now: {now}")
````

A noter que l'on peut y accéder à la manière d'un dictionnaire en utilisant [[vars()]] :
```python
args_obj = vars(parser.parse_args())
````

On peut y accéder en spécifiant le nom de l'argument :
```python
args_obj['name']

> 'Peter'
`````

## Sous-programme
Notre programme peut posséder plusieurs fonctions, pour lesquelles il faudra potentiellement passer des arguments différents. 

La mise en oeuvre demande d'instancier un objet enfant de parser avec la commande `parser.add_subparsers(dest='subroutine')`:

```python
parser = argparse.ArgumentParser()
subparsers = parser.add_subparsers(dest='subroutine')
```

Pour créer des parsers propres à une fonction, on crée pour chacun un nouvel objet à partir de `subparsers` par la méthode `subparsers.add_parser('mon_parser, help='...')` les parsers relatif à chaque fonction subalterne de notre programme. 

On peut adjoindre à ces parsers une série d'arguments avec la méthode `.add_argument(...)` appelé sur l'objet 

La logique de la ligne de commande pourra ainsi se structurer ainsi :

```
parser_obj
|
|___ arg1
|___ arg2
|___ arg3
|
|___ subparsers_obj
	|
	|___ subparser1_obj
	|	|___ parser1_arg1
	|	|___ parser1_arg2
	|
	|___ subparser_2_obj
		|___ parser2_arg1
		|___ parser2_arg2
````

Le code correspondant à cette organisation est le suivant :

```python
import argparse

parser = argparse.ArgumentParser()

parser.add_argument('--arg1', action='store')
parser.add_argument('--arg2', action='store')
parser.add_argument('--arg3', action='store')

subparsers_obj = parser.add_subparsers(dest='subparsers_obj')

subparser1_obj = subparsers_obj.add_parser('subparser1_obj')
subparser1_obj.add_argument('--parser1_arg1', action='store')
subparser1_obj.add_argument('--parser1_arg2', action='store')


subparser2_obj = subparsers_obj.add_parser('subparser2_obj')
subparser2_obj.add_argument('--parser2_arg1', action='store')
subparser2_obj.add_argument('--parser2_arg2', action='store')

arg_obj = vars(parser.parse_args())
print("\nLes arguments enregistrés sont les suivants : ")
print(arg_obj)

print("\nLe subparser utilisé est :")
print(arg_obj['subparsers_obj'])
````

Voilà le résultat en ligne de commande :

```bash
python prog.py 

> Les arguments enregistrés sont les suivants : 
> {'arg1': None, 'arg2': None, 'arg3': None, 'subparsers_obj': None}

> Le subparser utilisé est :
> None
```

Comme prévu, sans ajouter de commande liée aux subparsers, argparse ne traite que les arguments du niveau `root`.

```bash
python prog.py subparser1_obj

> Les arguments enregistrés sont les suivants : 
> {'arg1': None, 'arg2': None, 'arg3': None, 'subparsers_obj': 'subparser1_obj', 'parser1_arg1': None, 'parser1_arg2': None}

> Le subparser utilisé est :
> subparser1_obj

python prog.py subparser2_obj

> Les arguments enregistrés sont les suivants : 
> {'arg1': None, 'arg2': None, 'arg3': None, 'subparsers_obj': 'subparser2_obj', 'parser2_arg1': None, 'parser2_arg2': None}

> Le subparser utilisé est :
> subparser2_obj
````

En fonction du subparser qui est spécifié, argparse traite ses arguments.

Comme on le voit, le parser utilisé est accessible avec `arg_obj['subparsers_obj']`, ce qui est très pratique si l'on veut créer une condition qui active la fonction associée. 

Par exemple :
```python
if args_obj['subparsers_obj'] == 'run':
	run()
````


