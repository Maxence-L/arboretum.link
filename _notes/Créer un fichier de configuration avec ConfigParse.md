---
title:  Créer un fichier de configuration avec ConfigParse
tags: stdlib
toc: true
etat: printemps
--- 

ConfigParse est un module de python permettant de définir des paramètres d'un programme par défaut et de laisser les utilisateurs les modifier. Le module est particulièrement utile couplé à [[Argparse - créer une interface en ligne de commande\|Argparse]].

## Le fichier config.ini

Les valeurs de configuration du programme sont stockées dans un fichier `.ini`, typiquement `config.ini`. Il est structuré de la manière suivante :

```ini
[DEFAULT]
ServerAliveInterval = 45
Compression = yes
CompressionLevel = 9
ForwardX11 = yes

[bitbucket.org]
User = hg
````

On a ici deux sections, dont le titre est entre crochets : `DEFAULT`, `bitbucket.org`, les paies (clé, valeur) étant stockées à chaque ligne en dessous.

Voici un exemple de la structure du fichier :

```
[Simple Values]
key=value
spaces in keys=allowed
spaces in values=allowed as well
spaces around the delimiter = obviously
you can also use : to delimit keys from values

[All Values Are Strings]
values like this: 1000000
or this: 3.14159265359
are they treated as numbers? : no
integers, floats and booleans are held as: strings
can use the API to get converted values directly: true

[Multiline Values]
chorus: I'm a lumberjack, and I'm okay
    I sleep all night and I work all day

[No Values]
key_without_value
empty string value here =

[You can use comments]
# like this
; or this

# By default only in an empty line.
# Inline comments can be harmful because they prevent users
# from using the delimiting characters as parts of values.
# That being said, this can be customized.

    [Sections Can Be Indented]
        can_values_be_as_well = True
        does_that_mean_anything_special = False
        purpose = formatting for readability
        multiline_values = are
            handled just fine as
            long as they are indented
            deeper than the first line
            of a value
        # Did I mention we can indent comments, too?
```


## Initialisation

On initialise un objet de type `Configparser()` qui pourra lire le fichier `config.ini`:

```python
import configparser

config = configparser.ConfigParser()
config.read('config.ini')

> ['config.ini']
````

> **À noter :**
> Si l'on décide de laisser des clés sans valeurs (section `No Values`), il faudra passer l'option `allow_no_value=True` lors de l'initialisation de `ConfigParser()`:
>
>  ```python
>  ConfigParser(allow_no_value=True)
>  ````

## Accès aux données

On peut consulter les sections disponibles :

```python
config.sections()

> ['bitbucket.org']
````

Comme on le voit, seule la section `bitbucket.org` apparait. La section `DEFAULT` est un peu particulière : ses composants se propagent et sont accessibles à toutes les autres sections[^1].

[^1]: https://stackoverflow.com/questions/124692/what-is-the-intended-use-of-the-default-section-in-config-files-used-by-configpa

On accède aux composants soit avec des crochets, soit en utilisant `.get(section, clé)`[^2] :

[^2]: https://docs.python.org/3/library/configparser.html#legacy-api-examplese

```python
config['DEFAULT']['Compression']

> 'yes'

config.get('bitbucket.org', 'Compression')

> 'yes'
```

On notera que les données renvoyées sont toujours au format `string`: il faudra les convertir si l'on désire les utiliser sous un type différent.

Un objet `.ConfigParser()` initialisé étant un itérable, il est possible de parser ses clés dans une boucle `for` :

```python
for key in config['DEFAULT']:
    print(f"{key} : {config['DEFAULT'][key]}")

> serveraliveinterval : 45
> compression : yes
> compressionlevel : 9
> forwardx11 : yes
```

## Enregistrement des données

L'enregistrement de valeur dans le fichier de configuration se fait au moyen de la méthode `.set(section, key, value)` :

```python
config.set('bitbucket.org', 'user', 'arboretum')

config.get('bitbucket.org', 'user')

> 'arboretum'
`````

On peut aussi utiliser les crochets : `config[section][key] = value`

```python
config['bitbucket.org']['user'] = 'arborio'
config['bitbucket.org']['user']

> 'arborio'
```

### Si le programme est un package

Il faut préciser au packager (`setup.py`) le fichier `config.ini` à ajouter au package. On écrit la ligne suivante dans `setup.py`:

```python
package_data={'my_package': ['config.ini',]}
````

À noter que pour qu'il soit inclut au package, le fichier `config.ini` devra être dans le fichier racine.

Afin que Confiparse retrouve le fichier lors de l'exécution, il est nécessaire de définir de manière claire le chemin vers `config.ini` :

```python
config_filename = os.path.join(os.path.dirname(os.path.realpath(__file__)), 'config.ini')
config.read(config_filename)
`````
