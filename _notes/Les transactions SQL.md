---
title : Les transactions SQL
tags : SQL
etat : hiver
---

Une transaction permet de regrouper les opérations et requêtes pour les exécuter en bloc à condition que tout soit conforme. Cela permet d'éviter qu'une erreur ou une interruption ne vienne corrompre les données avec une requête qui n'aurais pas été complêtement exécutée. 

Les transactions donnent à l'utilisateur d'SQL un moyen de réaliser les changements dans la table, de vérifier que tout est en ordre avant de finaliser la transaction ou de l'annuler.  Une transaction validée est dite [**archivée**](http://www.granddictionnaire.com/ficheOqlf.aspx?Id_Fiche=8870443) (*commit*). L'annulation des transaction est appelée [**retour en arrière**](http://www.granddictionnaire.com/ficheOqlf.aspx?Id_Fiche=18065431) (*rollback*).

Par exemple, un échange monétaire nécessite deux modifications pour être valide : la diminution du montant de la transaction sur le premier compte et l'ajout du montant sur le second. Si une erreur arrivait (compte non provisionné, perte de communication) entre les deux opérations, la transaction risquerait de créditer ou débiter à tort un des participants. En conditionnant l'archivage de l'échange à son exécution complête, on supprime le risque de non-conformité de la transaction.

On dit d'un système de transaction qu'il est fiable s'il respecte les critères **ACID** :

- **Atomicité** : les transactions ne sont pas divisables et doivent être exécutées en bloc.
- **Cohérence** : si une erreur apparaît, la transaction doit être abandonnée et aucun changement archivé dans la base.
- **Isolation** : les transactions doivent être isolées entre elles et ne pas interagir. En pratique, les modifications d'une transaction ouverte ne sont pas visibles par les autres transactions.
- **Durabilité** : les données ainsi archivées sont enregistrée de manière durable. On peut les récupérer en cas de problème, comme un arrêt du serveur. Elles ne sont donc pas stockées dans la RAM.

![Transaction SQL](../assets/img/transactionSQl.png#center)
<div align="center">
	<p>
		Une transaction SQL. Source : <a href="https://openclassrooms.com/fr/courses/1959476-administrez-vos-bases-de-donnees-avec-mysql/1970063-transactions">OpenClassrooms.</a>
	</p>
</div>

## Syntaxe

On place au début de la requête la clause `START TRANSACTION` (ANSI SQL) ou `BEGIN`(PostgreSQL). 

La fin est marquée par `COMMIT` si l'on souhaite terminer la transaction et archiver les changements, ou `ROLLBACK` si l'on souhaite revenir en arrière et annuler les modifications.

Chaque requête est exécutée séparément. Tant que l'on a pas donné d'instructions finales (`COMMIT`, `ROLLBACK`), on peut inspecter ou continuer de modifier le tableau dont les modifications sont temporaires.

Exemple :

```SQL
START TRANSACTION;

UPDATE table
SET ville = 'Mont-Saint-Michel'
WHERE ville = 'Mont St Michel';

SELECT ville
FROM table
WHERE ville LIKE 'Mont-S%';
````

A ce stade (après deux requêtes), on devrait avoir la liste des villes contenant 'Mont-S%' modifiée pour contenir 'Mont-Saint-Michel'

Si le résultat nous convient, on peut archiver la transaction avec la commande :

```SQL
COMMIT
```

ou revenir en arrière et annuler la modification avec :

```SQL
ROLLBACK
````

> A noter : La transaction ne sera visible par les autres utilisateurs qu'après archivage.

## Autocommit

Par défaut, toute requête exécutée est archivée. Pour modifier ce paramètre, on doit changer de mode ainsi :

```SQL
SET autocommit=0;
```

A présent, seule la commande `COMMIT` activera l'archivage des requêtes dans la base.

## Jalon de transaction

On peut poser des jalons de transaction, similaires à une sauvergarde dans un jeu vidéo, au cas où les requêtes tourneraient mal et l'on ne désirerait pas tout reprendre depuis le début. 

On peut alors demander un retour en arrière jusqu'au jalon de transaction spécifié :

```SQL
SAVEPOINT nom_jalon; 
-- Crée un jalon avec comme nom "nom_jalon"

ROLLBACK [WORK] TO [SAVEPOINT] nom_jalon; 
-- Annule les requêtes exécutées depuis le jalon "nom_jalon", WORK et SAVEPOINT ne sont pas obligatoires

RELEASE SAVEPOINT nom_jalon; 
-- Retire le jalon "nom_jalon" (sans annuler, ni valider les requêtes faites depuis)
````

## Requêtes non annulables

Certaines requêtes terminent automatiquement la transaction, en validant tous les changements réalisés. On les appelle requêtes ou commandes **DDL** (*Data definition language*).

Elles concernent toutes les opérations agissant sur la structure de la base de données et non les données elles-mêmes :

-  La création et suppression de bases de données : `CREATE DATABASE`, `DROP DATABASE`  ;
    
-  La création, modification, suppression de tables : `CREATE TABLE`, `ALTER TABLE`, `RENAME TABLE`, `DROP TABLE`  ;
    
-  La création, modification, suppression d'index : `CREATE INDEX`, `DROP INDEX`  ;
    
-  La création d'objets comme les procédures stockées, les vues, etc.

- La création ou la suppression d'utilisateurs

- La création d'une nouvelle transaction

- Le chargement de données avec `LOAD DATA` provoque une validation implicite.