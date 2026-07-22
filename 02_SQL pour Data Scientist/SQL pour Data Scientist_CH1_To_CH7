# MODULE 2 – SQL pour Data Scientist (Niveau Expert)

# Chapitre 1 – Architecture d'une base relationnelle

Prenons un système bancaire simplifié.

## Table Customers

| customer_id | name    | city      |
| ----------- | ------- | --------- |
| 1           | Alice   | Paris     |
| 2           | Bob     | Lyon      |
| 3           | Charlie | Marseille |

---

## Table Accounts

| account_id | customer_id | balance |
| ---------- | ----------- | ------: |
| 100        | 1           |    5000 |
| 101        | 2           |    9000 |
| 102        | 1           |     700 |

---

## Table Transactions

| transaction_id | account_id | amount | transaction_date |
| -------------- | ---------- | -----: | ---------------- |
| 1              | 100        |    120 | 2026-01-01       |
| 2              | 100        |    300 | 2026-01-02       |
| 3              | 101        |    500 | 2026-01-01       |

---

Les relations sont :

```
Customers
      │
      │ customer_id
      ▼
Accounts
      │
      │ account_id
      ▼
Transactions
```

---

# Question d'entretien

Quelle est la différence entre une clé primaire et une clé étrangère ?

## Réponse attendue

**Clé primaire (Primary Key)**

* identifie de manière unique chaque ligne d'une table ;
* ne peut pas être `NULL` ;
* doit être unique.

Exemple :

```sql
customer_id
```

---

**Clé étrangère (Foreign Key)**

Référence une clé primaire située dans une autre table.

Elle garantit l'intégrité référentielle.

Exemple :

```sql
Accounts.customer_id
```

fait référence à

```sql
Customers.customer_id
```

---

# Chapitre 2 – SELECT

Très simple mais énormément utilisé.

```sql
SELECT *
FROM Customers;
```

---

Sélectionner seulement certaines colonnes :

```sql
SELECT
    name,
    city
FROM Customers;
```

---

Renommer une colonne :

```sql
SELECT
    name AS customer_name
FROM Customers;
```

---

# Question

Pourquoi éviter :

```sql
SELECT *
```

## Réponse

Parce que :

* toutes les colonnes sont chargées ;
* la requête est plus lente ;
* davantage de données transitent sur le réseau ;
* la maintenance est plus difficile si le schéma évolue.

En production, il est préférable de sélectionner uniquement les colonnes nécessaires.

---

# Chapitre 3 – WHERE

Filtrer les lignes.

```sql
SELECT *
FROM Accounts
WHERE balance > 5000;
```

---

Plusieurs conditions

```sql
SELECT *
FROM Accounts
WHERE balance > 5000
AND customer_id = 2;
```

---

Utiliser OR

```sql
SELECT *
FROM Customers
WHERE city='Paris'
OR city='Lyon';
```

---

Recherche de texte

```sql
SELECT *
FROM Customers
WHERE name LIKE 'A%';
```

Commence par A.

---

# Question

Quelle différence entre

```sql
=
```

et

```sql
LIKE
```

### Réponse

* `=` recherche une égalité exacte.
* `LIKE` permet d'utiliser des caractères génériques (`%`, `_`) pour des recherches partielles.

---

# Chapitre 4 – ORDER BY

```sql
SELECT *
FROM Accounts
ORDER BY balance DESC;
```

---

Plusieurs colonnes

```sql
ORDER BY city,
         balance DESC;
```

---

# Chapitre 5 – LIMIT

Limiter le nombre de résultats.

```sql
SELECT *
FROM Transactions
LIMIT 10;
```

---

# Question

Pourquoi utiliser `LIMIT` pendant le développement ?

### Réponse

Pour tester rapidement une requête sur un petit échantillon sans lire toute la table.

---

# Chapitre 6 – GROUP BY

Le chapitre le plus important.

Montant moyen par client.

```sql
SELECT
    customer_id,
    AVG(balance)
FROM Accounts
GROUP BY customer_id;
```

---

Nombre de comptes

```sql
SELECT
    customer_id,
    COUNT(*)
FROM Accounts
GROUP BY customer_id;
```

---

Montant maximum

```sql
SELECT
    customer_id,
    MAX(balance)
FROM Accounts
GROUP BY customer_id;
```

---

# Fonctions d'agrégation

Vous devez parfaitement connaître :

```sql
COUNT()
```

```sql
SUM()
```

```sql
AVG()
```

```sql
MIN()
```

```sql
MAX()
```

---

# Question

Quelle différence entre

```sql
COUNT(*)
```

et

```sql
COUNT(balance)
```

### Réponse

`COUNT(*)`

→ compte toutes les lignes.

`COUNT(balance)`

→ compte uniquement les lignes où `balance` n'est pas `NULL`.

---

# Chapitre 7 – HAVING

Question très fréquente.

Supposons que nous souhaitions uniquement les clients ayant plus de deux comptes.

```sql
SELECT
    customer_id,
    COUNT(*)
FROM Accounts
GROUP BY customer_id
HAVING COUNT(*) > 2;
```

---

# Question

Quelle différence entre WHERE et HAVING ?

### Réponse

**WHERE**

* filtre les lignes **avant** l'agrégation.

**HAVING**

* filtre les groupes **après** l'agrégation.

Exemple :

```sql
WHERE balance > 1000
```

puis

```sql
GROUP BY customer_id
```

puis

```sql
HAVING COUNT(*) > 2
```

---

# Exercice

Vous avez une table `transactions`.

| transaction_id | customer_id | amount |
| -------------- | ----------- | -----: |
| 1              | 101         |    120 |
| 2              | 101         |    300 |
| 3              | 102         |    500 |
| 4              | 101         |    200 |
| 5              | 103         |     50 |

Écrire une requête qui retourne :

* le `customer_id`
* le nombre de transactions
* le montant total

uniquement pour les clients ayant effectué au moins deux transactions.

### Solution

```sql
SELECT
    customer_id,
    COUNT(*) AS nb_transactions,
    SUM(amount) AS total_amount
FROM transactions
GROUP BY customer_id
HAVING COUNT(*) >= 2;
```

---

# Questions d'entretien (niveau débutant/intermédiaire)

1. Quelle différence entre `COUNT(*)` et `COUNT(colonne)` ?
2. Pourquoi éviter `SELECT *` en production ?
3. À quoi sert `GROUP BY` ?
4. Quelle différence entre `WHERE` et `HAVING` ?
5. Quelle différence entre `AVG()` et `SUM()` ?
6. Quand utiliser `LIKE` ?
7. Que fait `ORDER BY` ?
8. À quoi sert `LIMIT` ?
9. Quelle différence entre une clé primaire et une clé étrangère ?
10. Comment calculer le montant moyen des transactions par client ?

---

# Ce que les recruteurs recherchent

À ce stade, ils veulent s'assurer que vous maîtrisez les bases sans hésitation. Les erreurs les plus fréquentes concernent :

* la confusion entre `WHERE` et `HAVING` ;
* l'utilisation systématique de `SELECT *` ;
* l'oubli du `GROUP BY` avec des fonctions d'agrégation.

---

# Dans la prochaine partie

Nous aborderons les sujets qui différencient un candidat **junior** d'un candidat **intermédiaire/confirmé** :

* **INNER JOIN, LEFT JOIN, RIGHT JOIN, FULL JOIN** (avec schémas et cas pratiques) ;
* **SELF JOIN** ;
* **Sous-requêtes** ;
* **CTE (Common Table Expressions)** ;
* **Window Functions** (`ROW_NUMBER`, `RANK`, `DENSE_RANK`, `LAG`, `LEAD`, `SUM OVER`, etc.), qui sont parmi les questions les plus fréquentes dans les entretiens SQL pour les postes de Data Scientist.
