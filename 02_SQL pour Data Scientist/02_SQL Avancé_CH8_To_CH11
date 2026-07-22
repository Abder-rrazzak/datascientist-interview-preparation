Nous arrivons maintenant au **cœur des entretiens SQL**. Si vous maîtrisez les chapitres suivants (**JOIN, CTE et Window Functions**), vous serez capable de résoudre plus de **80 % des exercices SQL**

---

# MODULE 2 – SQL Avancé

# Chapitre 8 – Les JOIN

## Pourquoi les JOIN ?

Dans une base de données normalisée, les informations sont réparties dans plusieurs tables.

Exemple :

## Customers

| customer_id | name    |
| ----------- | ------- |
| 1           | Alice   |
| 2           | Bob     |
| 3           | Charlie |

---

## Accounts

| account_id | customer_id | balance |
| ---------- | ----------- | ------: |
| 100        | 1           |    5000 |
| 101        | 2           |    8000 |
| 102        | 1           |     700 |

---

Nous souhaitons afficher :

| name | balance |

Impossible sans JOIN.

---

# INNER JOIN

L'INNER JOIN retourne uniquement les lignes ayant une correspondance dans les deux tables.

```sql
SELECT
    c.name,
    a.balance
FROM Customers c
INNER JOIN Accounts a
ON c.customer_id = a.customer_id;
```

Résultat :

| name  | balance |
| ----- | ------: |
| Alice |    5000 |
| Alice |     700 |
| Bob   |    8000 |

Charlie n'apparaît pas car il n'a pas de compte.

---

## Schéma

```
Customers      Accounts

   ○──────○
```

Uniquement l'intersection.

---

## Question d'entretien

Quand utiliser un INNER JOIN ?

### Réponse

Lorsqu'on souhaite uniquement les enregistrements présents dans les deux tables.

---

# LEFT JOIN

Très fréquent.

```sql
SELECT
    c.name,
    a.balance
FROM Customers c
LEFT JOIN Accounts a
ON c.customer_id = a.customer_id;
```

Résultat :

| name    | balance |
| ------- | ------: |
| Alice   |    5000 |
| Alice   |     700 |
| Bob     |    8000 |
| Charlie |    NULL |

Charlie apparaît même sans compte.

---

## Schéma

```
Customers      Accounts

██████○
```

Toute la table de gauche.

---

## Question

Quand utiliser un LEFT JOIN ?

Réponse :

Lorsque toutes les lignes de la table de gauche doivent être conservées, même si aucune correspondance n'existe dans la table de droite.

---

# RIGHT JOIN

L'inverse du LEFT JOIN.

Peu utilisé en pratique.

La plupart des développeurs préfèrent inverser les tables et utiliser un LEFT JOIN.

---

# FULL OUTER JOIN

Retourne :

* toutes les lignes de gauche
* toutes les lignes de droite

Même sans correspondance.

---

# Question d'entretien

Quelle différence entre :

```
INNER

LEFT

RIGHT

FULL
```

Vous devez être capable de l'expliquer sans hésitation.

---

# Exercice

Tables :

Clients

| id |
| -- |
| 1  |
| 2  |
| 3  |

Transactions

| id |
| -- |
| 1  |
| 2  |

Quels résultats obtient-on avec :

```
INNER JOIN

LEFT JOIN

FULL JOIN
```

---

# SELF JOIN

Très apprécié.

Table Employees

| id | manager_id |
| -- | ---------- |
| 1  | NULL       |
| 2  | 1          |
| 3  | 1          |
| 4  | 2          |

Nous voulons afficher :

| Employé | Manager |

```sql
SELECT
    e.id,
    m.id AS manager
FROM Employees e
LEFT JOIN Employees m
ON e.manager_id = m.id;
```

---

# Question

Pourquoi un SELF JOIN ?

Réponse

Parce que la table se référence elle-même.

Très fréquent pour :

* organigrammes
* catégories
* hiérarchies

---

# Chapitre 9 – Les sous-requêtes

Exemple

Montant supérieur à la moyenne.

```sql
SELECT *
FROM Transactions
WHERE amount >
(
SELECT AVG(amount)
FROM Transactions
);
```

---

Question

Quand utiliser une sous-requête ?

Réponse

Lorsqu'un résultat intermédiaire est nécessaire pour calculer le résultat final.

---

# Sous-requête dans SELECT

```sql
SELECT

customer_id,

(

SELECT COUNT(*)

FROM Transactions t

WHERE t.customer_id = c.customer_id

)

FROM Customers c;
```

---

# Sous-requête dans FROM

```sql
SELECT *

FROM

(

SELECT ...

)

AS temp
```

---

# Chapitre 10 – CTE (Common Table Expressions)

Très fréquent.

Syntaxe

```sql
WITH ventes AS (

SELECT *

FROM Transactions

)

SELECT *

FROM ventes;
```

---

Pourquoi utiliser une CTE ?

* lisibilité
* réutilisation
* maintenance
* débogage

---

## Exemple réel

Calculer le total par client.

```sql
WITH total AS (

SELECT

customer_id,

SUM(amount) total_amount

FROM Transactions

GROUP BY customer_id

)

SELECT *

FROM total

WHERE total_amount >1000;
```

---

## Question

Différence entre une CTE et une sous-requête ?

Réponse

Une CTE :

* est plus lisible ;
* peut être réutilisée plusieurs fois dans la même requête ;
* simplifie les requêtes complexes.

---

# Chapitre 11 – Les Window Functions

Le sujet préféré des recruteurs.

Si vous maîtrisez cette partie, vous serez déjà au-dessus de nombreux candidats.

---

Supposons

| client | montant |
| ------ | ------: |
| A      |     100 |
| A      |     200 |
| A      |     300 |
| B      |     150 |
| B      |     500 |

---

# ROW_NUMBER()

```sql
SELECT

client,

montant,

ROW_NUMBER()

OVER(

PARTITION BY client

ORDER BY montant DESC

)

FROM Transactions;
```

Résultat

| client | montant | row_number |
| ------ | ------: | ---------: |
| A      |     300 |          1 |
| A      |     200 |          2 |
| A      |     100 |          3 |
| B      |     500 |          1 |
| B      |     150 |          2 |

---

Question

À quoi sert ROW_NUMBER ?

Réponse

Attribuer un numéro unique dans chaque groupe.

---

# RANK()

Supposons

```
100

100

50
```

Résultat

```
1

1

3
```

Le rang 2 est sauté.

---

# DENSE_RANK()

Même exemple

```
100

100

50
```

Résultat

```
1

1

2
```

Pas de saut.

---

Question très fréquente

Différence entre

```
ROW_NUMBER

RANK

DENSE_RANK
```

---

# LAG()

Comparer avec la ligne précédente.

```sql
SELECT

date,

amount,

LAG(amount)

OVER(

ORDER BY date

)

FROM Transactions;
```

---

Exemple

| Jour | Montant |
| ---- | ------: |
| 1    |     100 |
| 2    |     200 |
| 3    |     300 |

Résultat

| Jour | Montant | Précédent |
| ---- | ------: | --------: |
| 1    |     100 |      NULL |
| 2    |     200 |       100 |
| 3    |     300 |       200 |

---

# LEAD()

Même principe

Mais ligne suivante.

---

# SUM OVER()

Très demandé.

Calcul du cumul.

```sql
SELECT

date,

amount,

SUM(amount)

OVER(

ORDER BY date

)

FROM Transactions;
```

Résultat

| Jour | Montant | Cumul |
| ---- | ------: | ----: |
| 1    |     100 |   100 |
| 2    |     200 |   300 |
| 3    |     300 |   600 |

---

Question

Pourquoi utiliser une Window Function plutôt qu'un GROUP BY ?

Réponse

Parce qu'une Window Function conserve le détail des lignes tout en calculant des agrégats.

`GROUP BY` réduit le nombre de lignes.

Exemple :

Avec `GROUP BY` :

| client | total |
| ------ | ----: |
| A      |   600 |

Avec `SUM() OVER()` :

| client | montant | total |
| ------ | ------: | ----: |
| A      |     100 |   600 |
| A      |     200 |   600 |
| A      |     300 |   600 |

---

# Cas pratique SentiLink

Table Transactions

| id | client | date  | montant |
| -- | ------ | ----- | ------: |
| 1  | 101    | 01/01 |     100 |
| 2  | 101    | 02/01 |     200 |
| 3  | 101    | 03/01 |     150 |
| 4  | 102    | 01/01 |     500 |

Question

Pour chaque transaction :

Afficher

* le client
* le montant
* le total des transactions du client
* le rang de la transaction par montant décroissant
* le montant précédent

Solution

```sql
SELECT
    client,
    montant,
    SUM(montant) OVER (
        PARTITION BY client
    ) AS total_client,
    ROW_NUMBER() OVER (
        PARTITION BY client
        ORDER BY montant DESC
    ) AS rang,
    LAG(montant) OVER (
        PARTITION BY client
        ORDER BY date
    ) AS montant_precedent
FROM Transactions;
```

---

# Questions d'entretien (niveau avancé)

1. Quelle différence entre INNER JOIN et LEFT JOIN ?
2. Quand utiliser un SELF JOIN ?
3. Qu'est-ce qu'une CTE ?
4. Quelle différence entre une CTE et une sous-requête ?
5. Expliquez `ROW_NUMBER()`.
6. Différence entre `ROW_NUMBER()`, `RANK()` et `DENSE_RANK()`.
7. À quoi sert `LAG()` ?
8. À quoi sert `LEAD()` ?
9. Quelle différence entre `GROUP BY` et `SUM() OVER()` ?
10. Comment calculer un cumul en SQL ?

---

# Exercice de niveau entretien (inspiré de la détection de fraude)

Vous disposez d'une table `transactions` :

| transaction_id | customer_id | transaction_date | amount |
| -------------- | ----------- | ---------------- | -----: |
| 1              | 101         | 2026-01-01       |    120 |
| 2              | 101         | 2026-01-03       |    250 |
| 3              | 101         | 2026-01-05       |     90 |
| 4              | 102         | 2026-01-02       |    500 |
| 5              | 102         | 2026-01-04       |    700 |

Écrivez une requête SQL qui retourne, pour chaque transaction :

* le client (`customer_id`) ;
* la date de transaction ;
* le montant (`amount`) ;
* le nombre total de transactions du client ;
* le montant total des transactions du client ;
* le rang de la transaction par montant décroissant pour ce client ;
* le montant de la transaction précédente ;
* la différence entre le montant actuel et le montant précédent.

> **Objectif pédagogique :** cet exercice combine `PARTITION BY`, `COUNT() OVER()`, `SUM() OVER()`, `ROW_NUMBER()` (ou `RANK()` selon le besoin), `LAG()` et des calculs dérivés. C'est exactement le type d'exercice que l'on retrouve dans les entretiens SQL de niveau Data Scientist.

---

## Prochaine étape : Optimisation SQL

Nous aborderons ensuite un sujet très apprécié en entretien technique :

* fonctionnement des **index** ;
* `EXPLAIN` et lecture des plans d'exécution ;
* optimisation des requêtes lentes ;
* choix entre index B-tree, hash et composites (selon le SGBD) ;
* pièges de performance (`SELECT *`, fonctions sur les colonnes indexées, `OR` mal utilisés, etc.) ;
* études de cas réels sur des tables contenant plusieurs dizaines de millions de lignes.

C'est cette partie qui permet souvent de distinguer un candidat capable d'écrire une requête correcte d'un candidat capable d'écrire une requête **performante**.
