---
title: Selenium
toc: true
tags: web-scraping
etat: été
---
Selenium est un outil émulant un navigateur et permettant de simuler automatiquement l'interaction d'un utilisateur avec un site web depuis un navigateur. Il est utilisé pour le test de sites web mais peut être aussi exploité pour le scraping.

## Installation
 
Bien qu'il soit possible d'utiliser d'autres navigateurs, on utilisera Chrome pour les exemples de cette note. Si Chrome est installé sur le poste, on peut ensuite installer *chromedriver* qui fait la connexion entre selenium et chrome :

```bash
brew cask install chromedriver
````

[^1]: https://intoli.com/blog/running-selenium-with-headless-chrome/

On peut à présent paramétrer chrome :

```python
from selenium import webdriver

options = webdriver.ChromeOptions()

# set the window size
options.add_argument('window-size=1200x600')

# initialize the driver
driver = webdriver.Chrome(chrome_options=options)
```

Un premier test :

```python
driver = webdriver.Chrome(chrome_options=options)
driver.get('http://pythonscraping.com/pages/javascript/ajaxDemo.html')
time.sleep(3)

print(driver.find_element_by_id('content').text)
driver.close()

> Here is some important text you want to retrieve!
> A button to click!
````

> **À noter** : On peut aussi utiliser Chrome pour afficher les pages web en version "headless"[^1], ce qui utilise moins de ressources. On ajoutera pour ce faire l'option suivante : 
> `options.add_argument('headless')`

### Gestion du navigateur

Si l'accès au navigateur pose problème (version en emplacement, généralement), on peut utiliser le package `webdriver_manager.chrome` qui téléchargera une version compatible avec Selenium.

```python
from selenium import webdriver
from selenium.webdriver.chrome.options import Options
from webdriver_manager.chrome import ChromeDriverManager
import selenium.common.exceptions as selexcept

def __start_webdriver():

	try:
		options = Options()
		options.add_argument('window-size=1200x1000')
		
		return webdriver.Chrome(chrome_options=options)

	except selexcept.SessionNotCreatedException:
		
		from webdriver_manager.chrome import ChromeDriverManager
		options = Options()
		options.add_argument('window-size=1200x1000')
		
		return webdriver.Chrome(ChromeDriverManager().install(), chrome_options=options)
```


## Navigation
Atteindre une URL :
```python
driver.get("https://selenium.dev")
````

Lire l'url actuelle :
```python
driver.current_url
````

Revenir en arrière :
```python
driver.back()
````

Rafraichir :
```python
driver.refresh()
````

Obtenir le titre de la page :
```python
driver.title()
````

Quitter le navigateur :
```python
driver.quit()
````

Il est important de quitter à la fin de l'exécution afin de libérer la mémoire. Une bonne technique pour s'en assurer et la suivante :
```python
try:
    #WebDriver code here...
finally:
    driver.quit()
```


## Recherche du contexte
L'objet *webdriver* correspond au navigateur, il contiendra les pages chargées.

On localise les éléments d'intérêt avec la méthode `driver.find_element(By.XX, "cheese")` de l'instance *WebDriver*. Cette méthode retourne un objet de type *WebElement*. À la place de `XX`, on peut préciser l'attribut recherché : XPath, nom de classe (`CLASS_NAME`), etc.

```python
driver.find_element(By.ID, "cheese")
````

![](/assets/img/selenium-tags.png#center)

Il est aussi possible d'utiliser la méthode `find_element_by_class_name('xxxx')` - il existe une méthode de ce type pour chaque catégorie d'attribut.

### Chercher des éléments imbriqués (*nested*)

On peut recherche des éléments plus précis au sein des objets *WebElement* en utilisant une méthode similaire :
```python
cheese = driver.find_element(By.ID, "cheese")
cheddar = cheese.find_element_by_id("cheddar")
````

On peut accélérer l'opération en spécifiant les niveaux recherchés :

```python
cheddar = driver.find_element_by_css_selector("#cheese #cheddar")
````

Si l'on veut obtenir une liste d'éléments contenus dans un sélecteur [[CSS]], **il faut mettre "element" au pluriel** :

```python
<ol id=cheese>
 <li id=cheddar>…
 <li id=brie>…
 <li id=rochefort>…
 <li id=camembert>…
</ul>

mucho_cheese = driver.find_elements_by_css_selector("#cheese li")
````

Cela permet notamment de réaliser une [[itération]] sur la [[liste]] d'éléments obtenue :

```python
elements = driver.find_elements_by_css_selector("span.name")
for element in range(len(elements)):
    elements[element].click()
    elements = driver.find_elements_by_css_selector("span.name")
```

Au cas où l'on utiliserait cette méthode sans réaliser une boucle[[ for]] pour en extraire le contenu, il faut préciser le niveau désiré par un index `[0]`, sans quoi Selenium renverra une erreur.

Le tableau suivant résume les sélecteurs disponibles[^2] :

Locator | Description
------------|-----------
class name	| Locates elements whose class name contains the search value (compound class names are not permitted)
css selector |	Finds elements by their class, id, or tag name, using the \#idName, .className, tagName convention.
id	| Locates elements whose ID attribute matches the search value
name |	Locates elements whose NAME attribute matches the search value
link text |	Locates anchor elements whose visible text matches the search value
partial link text |	Locates anchor elements whose visible text contains the search value. If multiple elements are matching, only the first one will be selected.
tag name |	Locates elements whose tag name matches the search value
xpath |	Locates elements matching an XPath expression

[^2]: https://www.selenium.dev/documentation/en/getting_started_with_webdriver/locating_elements/

Il est par ailleurs possible de sélectionner des éléments situés de manière relative à d'autres éléments sélectionnés. Dans le cas d'un formulaire de mot de passe :

![](/assets/img/selenium-user-pwd.png#center)

```python
#from selenium.webdriver.support.relative_locator import with_tag_name
passwordField = driver.find_element(By.ID, "password")
emailAddressField = driver.find_element(with_tag_name("input").above(passwordField))
````

Les méthodes sont *.above() .below() .toLeftOff() .toRightOf() .near()*.

## Interactions

On remplit un champ avec du texte de la façon suivante :

```python
name = "Charles"
driver.find_element(By.NAME, "name").send_keys(name)
````

Drag-drop :

```python
source = driver.find_element(By.ID, "source")
target = driver.find_element(By.ID, "target")
ActionChains(driver).drag_and_drop(source, target).perform()
````

Et, bien sûr, **cliquer sur un élément :**

```python
driver.find_element(By.CSS_SELECTOR, "input[type='submit']").click()
````

## Attente

Utiliser `time.sleep()` permet de passer les temps d'attente, notamment pour l'affichage de certains pop-ups ou éléments de la page.

Plus de précisions peuvent être trouvées ici :

https://www.selenium.dev/documentation/en/webdriver/waits/

ou p. 169 du livre "Web Scraping with Python".

## Récupération les informations

On peut utiliser `.text`pour obtenir la valeur string d'un élément :

```python
print(driver.find_element_by_id('content').text)
````

Attention toutefois : si l'on a utilisé ```.find_elements``` (avec un s), l'objet est une liste et l'on doit donc préciser l'index pour appeler ```.text```.

Si l'information à récupérer est le nom de la classe, on pourra appeler ```.get_attribute("class")``` avec le nom de l'attribut à la place de `class`.

On peut aussi accéder à l'attribut *.page_source* pour obtenir la page html, qui pourra être parsée avec [[BeautifulSoup]] :

```python
pageSource = driver.page_source
bs = BeautifulSoup(pageSource, 'html.parser')
print(bs.find(id='content').get_text())
````

## Gestion des exceptions et des erreurs

La structure des sites webs visités peut être parfois imprévisible et il faut donc que le programme s'adapte et évite de rencontrer une erreur, ce qui fera planter le *driver*.

On peut donc réaliser une gestion des erreurs avec des [[try ... except ... finally]]. 

Une [liste et disponible dans la documentation de Selenium](https://www.selenium.dev/selenium/docs/api/py/common/selenium.common.exceptions.html) donnant tous les noms des exceptions possibles.

**Il faut toutefois penser à importer les erreurs avant de les utiliser**, notamment le module `selenium.common.exceptions`.

```python
import selenium.common.exceptions as selexcep!t
```

On peut ensuite utiliser les erreurs, comme `selexcept.NoSuchElementException`.

## Gestion des cookies

On peut utiliser le module [[pickle]] pour enregistrer les cookies en vue d'une réutilisation lors de futures sessions.

```python
# On nomme le fichier sauvegardé 'cookies.pkl'
pickle.dump(driver.get_cookies(), open("cookies.pkl", "wb"))
````

Les cookies peuvent être réutilisés avec [[pickle\|pickle.load()]]. 

**Attention toutefois à atteindre l'adresse désirée avec le webdriver avant d'ajouter les cookies**. Dans le cas contraire, on obtiendra une erreur de type :

`selenium.common.exceptions.InvalidCookieDomainException: Message: invalid cookie domain`

On ajoute les cookies avec la méthode `.add_cookie(cookie)` : 

```python
driver.get(self.website)
for cookie in self.cookies:
	self.driver.add_cookie(cookie)
````

## Petits scripts pratiques

Pour scroller une page sans fin :

```python
# Scroll to unveil all questions
SCROLL_PAUSE_TIME = 0.5

# Get scroll height
last_height = self.driver.execute_script("return document.body.scrollHeight")

 # Scroll down to bottom
 self.driver.execute_script("window.scrollTo(0, document.body.scrollHeight);")

# Wait to load page
time.sleep(SCROLL_PAUSE_TIME)

# Calculate new scroll height and compare with last scroll height
new_height = self.driver.execute_script("return document.body.scrollHeight")
if new_height == last_height:
	break
	last_height = new_height
````
