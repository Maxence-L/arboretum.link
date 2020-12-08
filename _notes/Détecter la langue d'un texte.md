---
title : Détecter la langue d'un texte
tags : NLP
etat : hiver
---

Il peut être utile de trouver la langue d'un texte, afin de pouvoir le comparer à d'autres textes de langue similaire ou de savoir quel modèle linguistique utiliser pour l'analyser.

De nombreuses bilbiothèques python existent à ce sujet, avec des résultats variables. Une réponse de [StackOverflow](https://stackoverflow.com/questions/39142778/python-how-to-determine-the-language) tente de lister les options disponibles, toutefois, toutes ne sont pas aussi efficaces / facilement installables / rapides.

[`langid`](https://github.com/saffsd/langid.py) est facile d'utilisation et semble assez efficace : voilà comment l'exploiter :

```python
#!pip install langid
import langid

# On écrit d'abord une petite fonction afin de vérifier que la valeur analysée est bien textuelle et comprend plus de quelques caractères :

def lang_detect(text, str_limit=10) :
    if isinstance(text, str) and len(text) > str_limit :
	
	# la méthode à utiliser est langid.classify(text)
	# Elle retourne un tuple (langue estimée, probabilité non-normalisée associée)
	# On doit donc spécifier la première valeur
	
        return langid.classify(text)[0]
		
    else:
        return np.nan
    
data['lang'] = data.text.apply(lang_detect)
```

