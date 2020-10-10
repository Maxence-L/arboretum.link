---
title : Packager un projet python
tags : #python
---

Packager un projet python permet de rendre les scripts qu'il contient exécutables de manière standardisée par un autre utilisateur. 

Organiser son code sous la forme d'un package a plusieurs avantages[^1] :

[^1]: https://the-hitchhikers-guide-to-packaging.readthedocs.io/en/latest/introduction.html#benefits-of-packaging

- Le téléchargement et/ou l'ajout automatique des dépendances, c'est-à-dire les packages annexes nécessaires pour que notre programme s'exécute.
- La gestion des packages installés sur l'environnement, leur version, leur auteur, etc...
- La désinstallation facilitée des packages installés.
- La recherche et l'installation au moyen d'un gestionnaire de packages, de type  [[pip]] ou [[conda]].

## La structure d'un projet
### Arborescence
On structure un projet de la manière suivante :
````
projet
|
|__ setup.py
|__.gitignore
|__requirements.txt
|__LICENSE
|__README.md
|
|__ nom_du_package
     |
     |__utils.py
	 |____main__.py
     |____init__.py
````

Les fichiers relatifs à l'installation et au déploiement du package sont situés à la racine de l'arborescence; les fichiers relatifs au fonctionnement du package lui-même sont dans le dossier `nom_du_package`.

### Les fichiers relatifs au fonctionnement du package
On retrouve dans le dossier `nom_du_package` trois fichiers : `utils.py`, `__main__.py` et `__init__.py`.

#### \_\_main__.py
`__main.py__` est le fichier par défaut qui sera exécuté par python lors de l'utilisation du package. Il contient donc les indications relatives à la mise en oeuvre du programme. On y ajoute souvent une fonction `main()` ainsi qu'une exécution conditionnelle si il est exécuté en tant que [[__main__]] (ce qui devrait être le cas) :
```python
def main():
	print('hello world')

if __name__ == '__main__':
    main()
````

#### \_\_init__.py
Les dossiers contenant un script [[__init__]].py sont considérés par python comme étant des packages et les modules qu'ils contiennent deviennent importables[^2] s'ils ont été installés.

[^2]: https://stackoverflow.com/questions/448271/what-is-init-py-for

Par exemple, si l'on a :
````
|__ nom_du_package
     |
	 |____main__.py
     |____init__.py
	 |
	 |__utils
	 	|
		|____init__.py
		|__database.py
	 
````

On pourra [[import|importer]] `database.py` avec la phrase suivante :
```python
import utils.database
````

Si `database.py`était situé dans le dossier `nom_du_package`, on aurait pu l'importer avec :
```python
import .database
````

Par ailleurs, il est possible d'inclure du code dans `__init__.py`qui sera exécuté au moment de l'import du module.

#### utils.py

`utils.py`correspond aux modules supplémentaires - il est conseillé de ne pas concentrer son programme dansun seul script si celui-ci dépasse une certaine taiile.

### Les fichiers relatifs au déploiement du package
Comme on peut le voir dans l'arborescence, un certain nombres de fichiers sont présents afin d'aider le packaging du projet et sa distribution :

```
|
|__.gitignore
|__requirements.txt
|__LICENSE
|__README.md
|__ setup.py
```

#### .gitignore

Si l'on utilise un gestionnaire de version comme [[git]], il y aura probablement des fichiers que l'on ne souhaite pas inclure dans la package, notamment s'il est uploadé sur internet.

On utilise le fichier `.gitignore` pour préciser quels fichiers [[git]] devra ignorer. Un exemple des fichiers les plus ignorés[^6] :

[^6]: https://gist.github.com/octocat/9257657

```.gitignore
# Compiled source #
###################
*.com
*.class
*.dll
*.exe
*.o
*.so
*.pyc

# Packages #
############
# it's better to unpack these files and commit the raw source
# git has its own built in compression methods
*.7z
*.dmg
*.gz
*.iso
*.jar
*.rar
*.tar
*.zip

# Logs and databases #
######################
*.log
*.sql
*.sqlite

# OS generated files #
######################
.DS_Store
.DS_Store?
._*
.Spotlight-V100
.Trashes
ehthumbs.db
Thumbs.db
````

Comme on s'en doute en regardant ci-dessus, on peut spécifier les extensions avec un astérisque en préfixe (ex : \*.exe) et les noms des fichiers ou dossiers - écrits simplement, en entier (ex : script.py).

On peut trouver un [exemplaire plus complet](https://github.com/github/gitignore/blob/master/Python.gitignore) sur github.

#### requirements.txt

Si l'on désire reproduire l'environnement d'exécution du package, on peut charger les dépendances en se réfèrrant à celles décrites dans le fichier `requirements.txt`.

Il prend la forme suivante :

````requirements.txt
selenium==3.141.0
setuptools~=49.2.0
bs4~=4.9.1
requests
````

On peut demander à utiliser la version exacte avec l'opérateur `==` ou une version compatible avec `~=`[^7]. On peut aussi ne pas préciser de version, comme dans le cas du package `requests`ci-dessus.

[^7]: https://stackoverflow.com/questions/39590187/in-requirements-txt-what-does-tilde-equals-mean

On l'exécute avec la commande `pip install -r requirements.txt`.

Pour obtenir une liste des packages utilisés par l'environnement, on peut utiliser
```bash
pip freeze > requirements.txt
`````

Il est aussi possible d'utiliser [pigar](https://github.com/damnever/pigar) si l'on désire restreindre les packages spécifiés dans requirements.txt aux seules importations du package.

Enfin, si l'on utilise [[Pycharm]], il est possible, après avoir créé le fichier `requirements.txt`, de retirer les packages non utilisés avec l'outil *sync python requirements* dans l'onglet *tool*.

#### LICENSE
Il s'agit de la du régime de propriété intellectuelle sous lequel le package est distribué. On peut choisir celui qui nous convient (généralement [[licence MIT]]) avec l'outil [choosealicence.com](https://choosealicense.com/). Il est au format texte.

#### README.md
Le Readme est un fichier texte résumant l'usage auquel se destine le programme, la méthode d'installation et son fonctionnement. Il est au format .md, si l'on veut le publier sur [[Github]].

#### setup.py
Le fichier `setup.py` a pour rôle d'appeler la fonction [[setup()]] de l'outil d'installation choisi qui créera un fichier installable. Il en existe plusieurs, mais l'on utilisera [[setuptools]][^3], recommandé par la [[Python Packaging Authority]][^4]

Une aide très complète est disponible ici : https://docs.python.org/3/distutils/index.html

La fonction [[setup()]] est appelée à la fin de la construction du package.

[^3]: https://setuptools.readthedocs.io/en/latest/userguide/index.html
[^4]: https://packaging.python.org/guides/tool-recommendations/

La fonction `setup()` comprend les paramètres relatifs aux métadonnées du projet, aux packages créés ainsi qu'aux dépendances qui doivent être installées.

##### Les métadonnées

Voici un exemple pratique de ce qui est inclu :

```python
name=("my_pckg",
      version="0.0.1",
      description="Mon petit package",
      author="Konrad Suse",
      author_email="wtf@wtf.net",
	  # packages created
      # install parameters...
      license="Apache 2.0")
````

[D'autres options sont disponibles](https://docs.python.org/3/distutils/setupscript.html#additional-meta-data) toutefois.

##### Les packages créés
On précise les packages créés par l'installation avec l'option `packages=['my_package', 'my_package.foo']`. En pratique, cela revient à expliquer à setuptools que le dossier `./my_packages` contient un fichier `__init__.py`qui constitue un package. 

Les sous-packages comme `my_package.foo`se trouvent dans le dossier `./my_package`.

Setuptools ne parcourt par récursivement les dossiers à la recherche de fichiers `__init__.py`, il faut donc spécifier explicitement le nom des packages.[^5]

[^5]: https://docs.python.org/3/distutils/setupscript.html#listing-whole-packages

##### Les dépendances

Définir les dépendances nécessaires dans `setup.py` permet à [[setuptools]] de les localiser sur Pypi et de les télécharger lors de l'installation du package[^5] (`python setup.py`, voir plus bas).

[^5]: https://setuptools.readthedocs.io/en/latest/userguide/dependency_management.html#build-system-requirement

On spécifie les dépendances `x` et `y` à installer avec le paramètre `install_require=["x","y"]` :

```python
name=("# meta parameters"
	  install_requires=["selenium", "pandas"])
````

On peut par ailleurs définir des packages optionnels à installer, pour des besoins de développement si par exemple les tests nécessitent une dépendance particulière :

```python
name=("# meta parameters"
	  install_requires=["selenium", "pandas"],
	  extras_require={
            "dev": ["test"])
````

On peut les installer avec la commande `pip install -e "test"`.

## Ajout de tests unitaires au package
https://python-packaging.readthedocs.io/en/latest/testing.html

## Création d'une distribution installable

### Distribution source (*sdist*)
Une fois que les fichiers précedemment cités sont dans le package, on peut créer la distribution source avec la commande `sdist` (*source distribution*).[^8]

[^8]: https://docs.python.org/3/distutils/introduction.html#distutils-simple-example

```bash
python setup.py sdist
````

Setuptools va créer un fichier en *.tar.gz* dans le dossier `sdist` comprenant les fichiers du package ainsi que des fichiers relatifs à son installation et à la gestion des dépendances. Le fichier source n'est toutefois pas encore compilé et le sera au moment de l'installation. 

### Distribution préconstruite (*wheel*)

On peut aussi créer une distribution préconstruite qui sera installable directement, (comprenant un fichier [*wheel*](https://realpython.com/python-wheels/)) sans passer par une exécution de `setup.py`. 

L'avantage est que le fichier di'nstallation sera plus rapide d'exécution et prendra moins de mémoire, du fait du prétraitement réalisé en amont.

On le crée avec la commande suivante :

```bash
python setup.py bdist_wheel
````

Si l'on désire que le package soit installabe sur différents OS, il faudra créer une distribution pour chacun. Des options supplémentaires sont disponible dans la [documentation officielle](https://docs.python.org/3/distutils/builtdist.html).

## Upload vers Pypi

## Utiliser un package maison
### Création de l'environnement virtuel
https://docs.python-guide.org/dev/virtualenvs/
### Exécution du package
```bash
pip install path/to/package/package.tar.gz
python -m package
````