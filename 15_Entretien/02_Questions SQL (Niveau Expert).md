Nous arrivons au **MODULE 24 — SQL**, qui est souvent **la partie la plus importante d'un entretien Data Scientist**.

Chez **SentiLink**, le SQL est utilisé quotidiennement pour :

* analyser des milliards de transactions ;
* construire des features de fraude ;
* investiguer des cas suspects ;
* alimenter les modèles de Machine Learning ;
* produire des analyses pour les équipes Risk, Product et Operations.

Dans beaucoup d'entreprises, **un excellent niveau en SQL est aussi important que le Machine Learning**.

---

# MODULE 24 — SQL (Niveau Expert)

## Objectifs

À la fin de ce module, vous devrez être capable de :

* écrire des requêtes SQL complexes sans aide ;
* comprendre les plans d'exécution de base ;
* utiliser les Window Functions ;
* créer des features pour le Machine Learning ;
* résoudre des exercices d'entretien.

---

# Partie 1 — Les bases indispensables

## Question 1

> **What is SQL?**

Bonne réponse :

> SQL (Structured Query Language) is the standard language used to query, manipulate and manage relational databases. It allows us to retrieve, aggregate, update and analyze structured data efficiently.

---

## Les principales commandes

```sql
SELECT
FROM
WHERE
GROUP BY
HAVING
ORDER BY
LIMIT
JOIN
WITH
```

Vous devez les maîtriser parfaitement.

---

# Partie 2 — Exemple de base de données

Supposons les tables suivantes.

## Customers

| customer_id | name  | country |
| ----------- | ----- | ------- |
| 1           | Alice | US      |
| 2           | Bob   | UK      |
| 3           | John  | US      |

---

## Transactions

| transaction_id | customer_id | amount | fraud |
| -------------- | ----------- | ------ | ----- |
| 10             | 1           | 100    | 0     |
| 11             | 1           | 900    | 1     |
| 12             | 2           | 50     | 0     |
| 13             | 3           | 1200   | 1     |

---

# Question 2

> **Retrieve all customers.**

```sql
SELECT *
FROM customers;
```

---

# Question 3

> **Retrieve only US customers.**

```sql
SELECT *
FROM customers
WHERE country = 'US';
```

---

# Question 4

> **Retrieve fraudulent transactions.**

```sql
SELECT *
FROM transactions
WHERE fraud = 1;
```

---

# Partie 3 — GROUP BY

Question très fréquente.

> **Average transaction amount by country**

```sql
SELECT
country,
AVG(amount)
FROM customers c
JOIN transactions t
ON c.customer_id = t.customer_id
GROUP BY country;
```

---

# GROUP BY

Le recruteur pose souvent :

> **Why do we need GROUP BY?**

Réponse :

Il permet de calculer des agrégations par groupe.

Exemples :

* moyenne ;
* somme ;
* nombre ;
* maximum ;
* minimum.

---

# HAVING

Question classique.

Quelle différence entre :

```sql
WHERE
```

et

```sql
HAVING
```

### WHERE

Filtre les lignes **avant** l'agrégation.

### HAVING

Filtre les groupes **après** l'agrégation.

Exemple :

```sql
SELECT
customer_id,
COUNT(*)
FROM transactions
GROUP BY customer_id
HAVING COUNT(*) > 5;
```

---

# Partie 4 — Les JOIN

La question la plus fréquente en SQL.

---

## INNER JOIN

Ne garde que les correspondances.

```sql
SELECT *
FROM customers c
INNER JOIN transactions t
ON c.customer_id = t.customer_id;
```

---

## LEFT JOIN

Conserve tous les clients.

Même sans transaction.

```sql
SELECT *
FROM customers c
LEFT JOIN transactions t
ON c.customer_id = t.customer_id;
```

---

## RIGHT JOIN

Conserve toutes les lignes de la table de droite.

Moins utilisé.

---

## FULL OUTER JOIN

Conserve toutes les lignes des deux tables.

Très utile pour les analyses de qualité de données.

---

# Question d'entretien

> **Explain the difference between INNER JOIN and LEFT JOIN.**

Bonne réponse :

> INNER JOIN returns only matching rows between both tables, while LEFT JOIN returns all rows from the left table and matching rows from the right table, filling unmatched values with NULL.

---

# Partie 5 — CTE (Common Table Expressions)

Très fréquente.

Exemple :

```sql
WITH customer_stats AS (

SELECT
customer_id,
AVG(amount) avg_amount

FROM transactions

GROUP BY customer_id

)

SELECT *

FROM customer_stats

WHERE avg_amount > 500;
```

---

## Pourquoi utiliser un CTE ?

Réponse

* améliore la lisibilité ;
* découpe une requête complexe ;
* facilite la maintenance.

---

# Partie 6 — Window Functions

C'est ici que les entretiens deviennent intéressants.

---

## ROW_NUMBER()

Supposons :

| customer | amount |
| -------- | ------ |
| A        | 100    |
| A        | 500    |
| A        | 700    |

On veut numéroter.

```sql
SELECT
customer_id,
amount,

ROW_NUMBER()

OVER(
PARTITION BY customer_id

ORDER BY amount DESC

)

FROM transactions;
```

Résultat :

| amount | row_number |
| ------ | ---------- |
| 700    | 1          |
| 500    | 2          |
| 100    | 3          |

---

## RANK()

Supposons :

```text
1000

900

900

800
```

Résultat :

```text
1

2

2

4
```

Il y a un "trou" après les égalités.

---

## DENSE_RANK()

Même exemple :

```text
1000

900

900

800
```

Résultat :

```text
1

2

2

3
```

Pas de trou.

---

# Question très fréquente

> **Difference between ROW_NUMBER, RANK and DENSE_RANK?**

Vous devez pouvoir répondre sans hésitation.

---

# LAG()

Permet d'accéder à la ligne précédente.

Exemple :

```sql
SELECT

customer_id,

timestamp,

LAG(timestamp)

OVER(
PARTITION BY customer_id

ORDER BY timestamp
)

FROM transactions;
```

Très utile pour :

```text
time_since_previous_transaction
```

---

# LEAD()

Accède à la ligne suivante.

Même principe.

---

# Partie 7 — SQL pour le Feature Engineering

Supposons que nous voulions créer :

```text
transactions_last_24_hours
```

On peut utiliser :

* Window Functions ;
* ou des requêtes temporelles adaptées au SGBD.

Le principe est de calculer des agrégats historiques **sans utiliser d'informations futures**, afin d'éviter le data leakage.

---

# Partie 8 — Étude de cas SentiLink

Question :

> Find customers having more than 10 transactions.

```sql
SELECT

customer_id,

COUNT(*) AS total_transactions

FROM transactions

GROUP BY customer_id

HAVING COUNT(*) > 10;
```

---

Question :

> Find the average fraud rate per country.

```sql
SELECT

country,

AVG(fraud) AS fraud_rate

FROM customers c

JOIN transactions t

ON c.customer_id = t.customer_id

GROUP BY country;
```

---

Question :

> Find each customer's latest transaction.

```sql
WITH ranked AS (

SELECT

*,

ROW_NUMBER()

OVER(

PARTITION BY customer_id

ORDER BY timestamp DESC

) AS rn

FROM transactions

)

SELECT *

FROM ranked

WHERE rn = 1;
```

Très classique en entretien.

---

# Partie 9 — Optimisation SQL

Question fréquente.

> **How do you optimize a SQL query?**

Réponse structurée :

1. Utiliser des index appropriés.
2. Éviter `SELECT *` lorsque seules quelques colonnes sont nécessaires.
3. Filtrer les données le plus tôt possible (`WHERE`).
4. Vérifier le plan d'exécution (`EXPLAIN` ou équivalent).
5. Limiter les jointures inutiles.
6. Utiliser des types de données adaptés.
7. Réduire les calculs répétitifs.

---

# Partie 10 — Index

Question :

> **What is an index?**

Réponse :

> An index is a data structure that speeds up data retrieval operations by allowing the database to locate rows efficiently, at the cost of additional storage and slower writes.

---

# Quand utiliser un index ?

Colonnes fréquemment utilisées dans :

* `WHERE`
* `JOIN`
* `ORDER BY`

Mais évitez d'indexer toutes les colonnes : chaque index a un coût en stockage et en mise à jour.

---

# Partie 11 — EXPLAIN

Question :

> **What is EXPLAIN used for?**

Réponse :

Il permet d'analyser le plan d'exécution d'une requête et d'identifier les opérations coûteuses (parcours complets de table, jointures inefficaces, etc.).

---

# Partie 12 — Les erreurs classiques

Évitez de :

* utiliser `SELECT *` en production sans nécessité ;
* oublier une condition de jointure (`ON`), ce qui crée un produit cartésien ;
* filtrer avec `HAVING` alors qu'un `WHERE` suffit ;
* utiliser une `LEFT JOIN` lorsqu'une `INNER JOIN` est suffisante ;
* négliger les index sur les colonnes critiques.

---

# Questions SQL très probables chez SentiLink

### Q1

**Find the latest transaction for every customer.**

→ `ROW_NUMBER()`

---

### Q2

**Calculate the average amount by country.**

→ `GROUP BY`

---

### Q3

**Difference between WHERE and HAVING?**

→ Avant vs après agrégation.

---

### Q4

**Difference between INNER and LEFT JOIN?**

→ Correspondances uniquement vs conservation de toutes les lignes de gauche.

---

### Q5

**Explain Window Functions.**

→ Calculs analytiques sans regrouper les lignes.

---

### Q6

**How would you detect duplicate transactions?**

Par exemple :

```sql
SELECT
customer_id,
amount,
timestamp,
COUNT(*) AS occurrences
FROM transactions
GROUP BY customer_id, amount, timestamp
HAVING COUNT(*) > 1;
```

Le choix des colonnes dépend de la définition métier d'un doublon.

---

### Q7

**How would you compute the time since the previous transaction?**

Réponse :

J'utiliserais `LAG()` pour récupérer le timestamp précédent, puis je calculerais la différence entre les deux dates.

---

### Q8

**How would you identify customers whose transaction amount is much higher than their usual spending?**

Réponse :

Je calculerais la moyenne historique des montants par client (ou une moyenne glissante si le contexte temporel l'exige), puis je comparerais chaque transaction à cette moyenne pour construire une feature comme `amount_to_average_ratio`.

---

# Ce que le recruteur veut vraiment

Le recruteur ne cherche pas quelqu'un qui mémorise des requêtes SQL.

Il veut voir que vous êtes capable de :

* explorer les données efficacement ;
* construire des features pour le Machine Learning ;
* raisonner sur les performances ;
* écrire des requêtes lisibles et maintenables ;
* comprendre les implications métier de vos analyses.

---

## 🚀 Prochain module : MODULE 25 — Statistiques pour Data Scientists

Nous couvrirons en profondeur :

* probabilités ;
* distributions ;
* espérance et variance ;
* intervalles de confiance ;
* tests d'hypothèses ;
* p-value ;
* A/B testing ;
* régression ;
* biais et variance ;
* questions de statistiques fréquemment posées en entretien chez SentiLink, Meta, Google et Amazon.
