---
title: BeautifulSoup
tags: web-scraping
toc: true
etat: printemps
---

BeautifulSoup permet de parser les fichiers Html et d'en extraire le contenu de manière ordonnée, en suivant les tags de la page.

La doc : https://www.crummy.com/software/BeautifulSoup/bs4/doc/

## Installation

On l'importe de la manière suivante :

```python
from bs4 import BeautifulSoup
```

## Parsing
On peut ensuite parser un document html, produit notamment par le package [[requests]] :

```python
soup = BeautifulSoup(r.text, 'lxml')
```

Plusieurs parseurs sont possibles : si l'on désire utilise [[lxml]], plutôt qu'[[html5lib]], il faut l'importer.

## Navigation

La méthode *.find_all()* permet de chercher par tag Html (premier argument), mais aussi par [classe CSS](https://www.crummy.com/software/BeautifulSoup/bs4/doc/#searching-by-css-class) (second argument) :

```python
summaries = soup.find_all("div", {"class":"course-summary-card"})
```

L'objet ainsi créé contient un objet string composé des tags de premier niveau désirés et qui peut être parcouru de manière récursive. Pour le visualiser, il est plus aisé d'utiliser *.prettify()* associé à *print()*. Si l'on désire voir le premier élément div de `summaries` :

```python
print(summaries[0].prettify())

<div _ngcontent-sc191="" class="course-summary-card row row-gap-medium catalog-card nanodegree-card ng-star-inserted">
 <ir-catalog-card _ngcontent-sc191="" _nghost-sc194="">
  <div _ngcontent-sc194="" class="card-wrapper is-collapsed">
   <div _ngcontent-sc194="" class="card__inner card mb-0">
    <div _ngcontent-sc194="" class="card__inner--upper">
     <div _ngcontent-sc194="" class="image_wrapper hidden-md-down">
      <a _ngcontent-sc194="" href="/course/applying-data-science-to-product-management--nd030-1">
       <!-- -->	   
...
```

On peut aussi sélectionner les éléments par tags et par classe CSS, avec la méthode *select()*, en précisant en string " " les tags à retenir. On peut aussi chercher par classe CSS en mettant un point devant :

```python
print(summaries[0].select_one("div .card-content" ))
````

On peut utiliser l'attribut [.children](https://www.crummy.com/software/BeautifulSoup/bs4/doc/#navigating-using-tag-names) et [.findChildren](https://stackoverflow.com/questions/6287529/how-to-find-children-of-nodes-using-beautifulsoup) pour atteindre les sous-éléments d'un div.

La méthode *.get_text()* renvoie uniquement le texte contenu dans l'élément, sans balisage HTML :
```python
print(summaries[0].select_one("div .card-content" ).get_text())

> New School of Business Applying Data Science to Product ManagementSkills CoveredData Science, Product Management, Data Visualization, Product Strategyintermediate
````

On peut ainsi aisément récupérer, par exemple, les titres :
```python
summaries[0].select_one("h3").get_text().strip()
````

[[.strip()]] sert à retirer les caractères 'espace' devant et derrière le string produit.

Utiliser *.select_one()* renvoie le premier élément rencontré qui correspond aux critères énoncés.

## Extraction

On peut extraire les données en bouclant dans les arbres de tags Html de la page étudiée. Un exemple :

```python
courses = []

for summary in summaries:
    # append name and school of each summary to courses list
    title = summary.select_one("h3").get_text().strip()
    school = summary.select_one("h4").get_text().strip()
    courses.append((title, school))
```
