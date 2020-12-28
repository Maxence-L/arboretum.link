---
title : Sauvegarder un tableau SQL au format CSV
tags: SQL
etat: hiver
---

On peut enregistrer un tableau SQL avec `COPY ... TO ... WITH ...` :

```SQL
# On crée une base
CREATE TABLE char\_data\_types (  
    char\_column char(10),  
 	varchar\_column varchar(10),  
 	text\_column text  
);  
  
INSERT INTO char\_data\_types  
VALUES  
 ('abc', 'abc', 'abc'),  
 ('defghi', 'defghi', 'defghi');
 
# On la sauvegarde
COPY char\_data\_types TO '/Users/Me/scripts/typetest.txt'  
WITH (FORMAT CSV, HEADER, DELIMITER '|');
````



