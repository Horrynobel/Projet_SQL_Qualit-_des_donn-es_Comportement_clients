# Projet SQL – Qualité des données & Comportement clients

Ce projet regroupe plusieurs requêtes SQL avancées réalisées sur les bases **WideWorldImporters** et **SQLPlayground**.[file:3][file:6][file:2][file:7]  
L’objectif est de vérifier la cohérence entre commandes et factures, de mesurer les pertes potentielles, d’appliquer une mise à jour ciblée, et d’analyser le comportement d’achat des clients.[file:3][file:6][file:2][file:7] 

## Environnement

- Base principale : `WideWorldImporters` (tables Sales.*). [file:3][file:6][file:2]  
- Base secondaire d’exercice : `SQLPlayground` (tables Customer, Product, Purchase). [file:7]  

---

## Q1 – Contrôle de cohérence Commandes vs Factures

**Fichier :** `Q1-SQL-1.sql`  
Cette requête compare, pour chaque client, le nombre de commandes, le nombre de factures, et les montants totaux des commandes vs des factures, puis calcule l’écart absolu entre les deux valeurs.[file:3]  

**Ce que la requête représente :**

- Agrégation par client (CustomerID, CustomerName). [file:3]  
- Comptage des commandes et des factures distinctes. [file:3]  
- Somme des valeurs au niveau des lignes de commande et des lignes de facture. [file:3]  
- Calcul de la différence absolue pour mettre en évidence les clients présentant des écarts de facturation. [file:3]  

**Où et quand l’utiliser :**

- Audit de **cohérence** entre commandes et factures dans un contexte de facturation. [file:3]  
- Suivi de la **qualité des données** et détection d’erreurs de saisie ou de process (factures manquantes, montants incorrects, etc.). [file:3]  
- Point de départ pour des analyses de **pertes potentielles** par client ou par segment. [file:3]  

---

## Q2 – Identification et mise à jour d’une ligne de facture ciblée

**Fichier :** `Q2-SQL2.sql`  

La première partie de la requête identifie la première facture du client 1060, calcule le total de cette facture et renvoie les informations associées.[file:2]  
La seconde partie met à jour **uniquement la première InvoiceLine** de cette première facture, en augmentant le `UnitPrice` de 20 unités monétaires.[file:2]  

**Ce que la requête représente :**

- Sélection de la **première facture** d’un client donné via un `SELECT TOP(1)` sur `Sales.Invoices`. [file:2]  
- Jointures avec `Sales.InvoiceLines` pour calculer le montant total de la facture. [file:2]  
- Mise à jour ciblée d’une seule ligne de facture (`InvoiceLineID`) à l’aide d’une sous-requête corrélée. [file:2]  

**Où et quand l’utiliser :**

- Cas d’usage de **correction manuelle** ou d’ajustement tarifaire sur une ligne de facture spécifique. [file:2]  
- Démonstration de **maîtrise des UPDATE avec sous-requêtes** en minimisant les risques d’impacter d’autres lignes. [file:2]  
- Situation de test pour valider des triggers, des contraintes ou l’impact d’une modification de prix. [file:2]  

---

## Q4 – Identification des clients les plus “perdants” par catégorie

**Fichier :** `Q4-SQL4.sql`  

Cette requête calcule d’abord la perte par commande (différence entre valeur commandée et valeur facturée), puis agrège ces pertes au niveau client, et enfin identifie, pour chaque **catégorie de clients**, celui qui génère la perte la plus élevée.[file:6]  

**Ce que la requête représente :**

- CTE `OrderLoss` : calcul de la **perte par commande** = somme des lignes de commande – somme des lignes de facture (avec gestion des factures manquantes via `COALESCE`). [file:6]  
- CTE `CustomerLoss` : agrégation de la perte totale par client, avec le détail de la catégorie client. [file:6]  
- CTE `MaxLossPerCategory` : détermination de la **perte maximale par catégorie**. [file:6]  
- Requête finale : retourne, pour chaque catégorie, le client qui a la perte la plus élevée, triée de la plus grande à la plus faible. [file:6]  

**Où et quand l’utiliser :**

- Analyse de **risque financier** par segment de clientèle. [file:6]  
- Identification des clients à **fort enjeu** pour des actions correctives (process, contrôle, accompagnement). [file:6]  
- Construction d’indicateurs pour un **dashboard finance / contrôle de gestion** orienté pertes et écarts de facturation. [file:6]  

---

## Q5 – Clients qui ont acheté tous les produits et en grande quantité

**Fichier :** `Q5-SQL5.sql`  

Cette requête retourne les clients qui répondent à deux critères :  
1) Ils ont acheté **tous les produits** présents dans la table Product.  
2) Leur quantité totale achetée (tous produits confondus) est **supérieure à 50**. [file:7]  

**Ce que la requête représente :**

- Utilisation de `NOT EXISTS` imbriqué pour vérifier qu’il n’existe **aucun produit** que le client n’a pas acheté (logique de division relationnelle). [file:7]  
- Sous-requête avec `GROUP BY` et `HAVING SUM(pu.Qty) > 50` pour filtrer les clients à **fort volume d’achat**. [file:7]  
- Identification des clients les plus **engagés** ou **fidèles** en termes de couverture de gamme et de quantité. [file:7]  

## Fichiers de sortie

- `Q1-output-query.csv` : résultat de la requête Q1 (écarts commandes vs factures par client). [file:1]  
- `Q2-output-2.csv` : résultat du contrôle sur la facture mise à jour pour le client 1060. [file:4]  
- `Q4-output.csv` : liste des clients à plus forte perte par catégorie, prête pour une analyse ou un dashboard. [file:5]  
- `Q5-output-5.csv` : clients SQLPlayground répondant aux critères “tous les produits + > 50 en quantité”. [file:8]  


