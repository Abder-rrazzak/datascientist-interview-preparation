Nous passons au **MODULE 18 — SQL avancé pour Data Scientist**, avec un objectif précis : être capable de résoudre des problèmes de données réalistes pendant un entretien SentiLink.

Le SQL est particulièrement important ici, car le poste demande explicitement **PostgreSQL** et l'analyse de gros volumes de données.

---

# MODULE 18 — SQL avancé : PostgreSQL + Fraud Analytics

## 🎯 Objectifs

À la fin de ce module, vous devez maîtriser :

* `JOIN`
* `GROUP BY`
* `HAVING`
* `CASE WHEN`
* CTE
* Window Functions
* `ROW_NUMBER`
* `RANK`
* `LAG`
* `LEAD`
* `SUM() OVER`
* moyennes glissantes
* détection d'anomalies
* optimisation SQL
* `EXPLAIN ANALYZE`
* pièges liés aux données temporelles

---

# 1. Jeu de données utilisé pendant le module

Imaginons les tables suivantes.

### `customers`

```text
customer_id
name
country
created_at
```

### `transactions`

```text
transaction_id
customer_id
amount
timestamp
device_id
ip_address
is_fraud
```

### `devices`

```text
device_id
first_seen
```

---

# 2. JOIN — Niveau fondamental

Le recruteur demande :

> Find all transactions with the customer's country.

```sql
SELECT
    t.transaction_id,
    t.customer_id,
    t.amount,
    c.country
FROM transactions t
JOIN customers c
    ON t.customer_id = c.customer_id;
```

Le principe :

```text
transactions
      │
      │ customer_id
      ▼
customers
```

---

# 3. INNER JOIN vs LEFT JOIN

### INNER JOIN

Retourne uniquement les correspondances.

```sql
SELECT *
FROM transactions t
INNER JOIN customers c
    ON t.customer_id = c.customer_id;
```

### LEFT JOIN

Conserve toutes les transactions même si le client n'est pas trouvé.

```sql
SELECT *
FROM transactions t
LEFT JOIN customers c
    ON t.customer_id = c.customer_id;
```

---

# 🎤 Question d'entretien

> **When would you use LEFT JOIN instead of INNER JOIN?**

### Réponse idéale

> I would use a LEFT JOIN when I need to preserve all records from the left table, even when there is no matching record in the right table. For example, when checking data quality, I might want to identify transactions whose customer information is missing.

---

# 4. Détection de problèmes de qualité

Très bon exemple d'entretien.

```sql
SELECT
    t.transaction_id,
    t.customer_id
FROM transactions t
LEFT JOIN customers c
    ON t.customer_id = c.customer_id
WHERE c.customer_id IS NULL;
```

Vous trouvez ainsi les transactions dont le client n'existe pas dans `customers`.

---

# 5. GROUP BY

Question :

> What is the average transaction amount per customer?

```sql
SELECT
    customer_id,
    AVG(amount) AS avg_amount
FROM transactions
GROUP BY customer_id;
```

---

# 6. GROUP BY + HAVING

Question :

> Find customers who made more than 100 transactions.

```sql
SELECT
    customer_id,
    COUNT(*) AS transaction_count
FROM transactions
GROUP BY customer_id
HAVING COUNT(*) > 100;
```

### Pourquoi pas `WHERE` ?

Parce que :

```text
WHERE
 ↓
GROUP BY
 ↓
HAVING
```

`WHERE` filtre les lignes.

`HAVING` filtre les groupes.

---

# 7. CASE WHEN

Très important pour la Data Science.

Supposons que vous vouliez catégoriser les transactions :

```sql
SELECT
    transaction_id,
    amount,
    CASE
        WHEN amount < 50 THEN 'low'
        WHEN amount < 500 THEN 'medium'
        ELSE 'high'
    END AS amount_category
FROM transactions;
```

Résultat :

| amount | category |
| -----: | -------- |
|     20 | low      |
|    150 | medium   |
|    900 | high     |

---

# 8. Calculer le taux de fraude

Très bonne question d'entretien.

```sql
SELECT
    COUNT(*) AS total_transactions,
    SUM(CASE WHEN is_fraud = 1 THEN 1 ELSE 0 END) AS fraud_count,
    AVG(CASE WHEN is_fraud = 1 THEN 1.0 ELSE 0.0 END) AS fraud_rate
FROM transactions;
```

Si vous avez :

```text
1 000 000 transactions
2 000 frauds
```

alors :

```text
fraud_rate = 0,2 %
```

---

# 9. Fraude par pays

```sql
SELECT
    c.country,
    COUNT(*) AS transactions,
    SUM(CASE WHEN t.is_fraud = 1 THEN 1 ELSE 0 END) AS frauds,
    AVG(
        CASE
            WHEN t.is_fraud = 1 THEN 1.0
            ELSE 0.0
        END
    ) AS fraud_rate
FROM transactions t
JOIN customers c
    ON t.customer_id = c.customer_id
GROUP BY c.country
ORDER BY fraud_rate DESC;
```

---

# 10. CTE — Common Table Expression

Une CTE permet de rendre une requête complexe plus lisible.

Exemple :

> Find customers whose fraud rate is above 5%.

```sql
WITH customer_stats AS (
    SELECT
        customer_id,
        COUNT(*) AS total_transactions,
        SUM(
            CASE
                WHEN is_fraud = 1 THEN 1
                ELSE 0
            END
        ) AS fraud_count
    FROM transactions
    GROUP BY customer_id
)

SELECT
    customer_id,
    total_transactions,
    fraud_count,
    fraud_count * 1.0 / total_transactions AS fraud_rate
FROM customer_stats
WHERE fraud_count * 1.0 / total_transactions > 0.05;
```

---

# 11. Pourquoi utiliser une CTE ?

Réponse d'entretien :

> **CTEs improve readability and make complex queries easier to reason about. They allow me to break a large transformation into logical steps.**

Mais attention :

> Une CTE n'est pas automatiquement synonyme de meilleure performance.

C'est surtout un outil de **structuration et lisibilité**, même si PostgreSQL peut optimiser certaines CTE selon le contexte et la version.

---

# 12. Window Functions

C'est probablement **le sujet SQL le plus important à maîtriser pour votre entretien**.

Une Window Function permet de faire des calculs sur un ensemble de lignes tout en conservant chaque ligne.

---

## Exemple

```sql
SELECT
    customer_id,
    transaction_id,
    amount,
    AVG(amount) OVER (
        PARTITION BY customer_id
    ) AS customer_avg
FROM transactions;
```

Vous obtenez :

| customer | transaction | amount | customer_avg |
| -------: | ----------: | -----: | -----------: |
|        1 |         101 |    100 |          200 |
|        1 |         102 |    300 |          200 |
|        2 |         103 |     50 |           75 |
|        2 |         104 |    100 |           75 |

Contrairement à :

```sql
GROUP BY customer_id
```

nous n'avons pas perdu les transactions individuelles.

---

# 13. `ROW_NUMBER()`

Question extrêmement fréquente :

> Find the most recent transaction for every customer.

```sql
WITH ranked AS (
    SELECT
        *,
        ROW_NUMBER() OVER (
            PARTITION BY customer_id
            ORDER BY timestamp DESC
        ) AS rn
    FROM transactions
)

SELECT *
FROM ranked
WHERE rn = 1;
```

---

# 14. Pourquoi `ROW_NUMBER()` ?

Pour chaque client :

```text
customer 1
transaction A → 1
transaction B → 2
transaction C → 3
```

Puis :

```sql
WHERE rn = 1
```

permet de conserver uniquement la dernière transaction.

---

# 15. `RANK()` vs `ROW_NUMBER()`

Question classique.

Supposons :

```text
scores
100
100
90
```

### `ROW_NUMBER()`

```text
1
2
3
```

### `RANK()`

```text
1
1
3
```

### `DENSE_RANK()`

```text
1
1
2
```

À retenir :

```text
ROW_NUMBER
→ numéro unique

RANK
→ même rang + trous

DENSE_RANK
→ même rang sans trous
```

---

# 16. `LAG()`

Très utile pour la fraude.

Question :

> Compare each transaction with the customer's previous transaction.

```sql
SELECT
    customer_id,
    transaction_id,
    timestamp,
    amount,
    LAG(amount) OVER (
        PARTITION BY customer_id
        ORDER BY timestamp
    ) AS previous_amount
FROM transactions;
```

Résultat :

| customer | amount | previous_amount |
| -------: | -----: | --------------: |
|        1 |    100 |            NULL |
|        1 |    150 |             100 |
|        1 |    500 |             150 |

---

# 17. `LEAD()`

C'est l'inverse :

```sql
LEAD(amount) OVER (
    PARTITION BY customer_id
    ORDER BY timestamp
)
```

Elle permet de regarder **la transaction suivante**.

---

# 18. Feature de fraude avec `LAG()`

Nous pouvons calculer :

```text
amount_change
```

```sql
SELECT
    customer_id,
    timestamp,
    amount,
    LAG(amount) OVER (
        PARTITION BY customer_id
        ORDER BY timestamp
    ) AS previous_amount,

    amount -
    LAG(amount) OVER (
        PARTITION BY customer_id
        ORDER BY timestamp
    ) AS amount_change

FROM transactions;
```

---

# 19. Time Since Previous Transaction

Encore plus intéressant.

PostgreSQL permet de soustraire des timestamps.

```sql
SELECT
    customer_id,
    timestamp,
    timestamp -
        LAG(timestamp) OVER (
            PARTITION BY customer_id
            ORDER BY timestamp
        ) AS time_since_previous
FROM transactions;
```

Vous obtenez quelque chose comme :

```text
customer | timestamp | time_since_previous
---------|-----------|--------------------
1        | 10:00     | NULL
1        | 10:05     | 00:05
1        | 10:07     | 00:02
```

Cette feature peut devenir :

```text
seconds_since_previous_transaction
```

---

# 20. Détecter des transactions rapides

Question :

> Find transactions occurring less than 60 seconds after the previous transaction.

```sql
WITH transaction_history AS (
    SELECT
        *,
        timestamp -
        LAG(timestamp) OVER (
            PARTITION BY customer_id
            ORDER BY timestamp
        ) AS time_since_previous
    FROM transactions
)

SELECT *
FROM transaction_history
WHERE time_since_previous < INTERVAL '60 seconds';
```

🔥 Très bon exercice de fraude.

---

# 21. Fenêtres temporelles

Supposons que nous voulions :

> Number of transactions made by each customer during their previous 10 transactions.

On peut utiliser :

```sql
COUNT(*) OVER (
    PARTITION BY customer_id
    ORDER BY timestamp
    ROWS BETWEEN 9 PRECEDING AND CURRENT ROW
)
```

Mais attention :

```text
ROWS
```

signifie des **lignes**, pas une durée.

---

# 22. Une vraie fenêtre temporelle

Si l'objectif est :

> transactions in the previous 10 minutes

il faut réfléchir en termes de timestamp.

PostgreSQL permet des fenêtres basées sur `RANGE` dans certains cas, mais la syntaxe et le comportement doivent être maîtrisés selon le type de données et l'ordre choisi.

Une approche robuste pour un entretien peut être d'utiliser une jointure temporelle ou une sous-requête corrélée selon le volume et le plan d'exécution.

Par exemple :

```sql
SELECT
    t1.transaction_id,
    t1.customer_id,
    t1.timestamp,
    (
        SELECT COUNT(*)
        FROM transactions t2
        WHERE t2.customer_id = t1.customer_id
          AND t2.timestamp >= t1.timestamp - INTERVAL '10 minutes'
          AND t2.timestamp <= t1.timestamp
    ) AS transactions_last_10min
FROM transactions t1;
```

⚠️ Mais sur des dizaines de millions de lignes, cette approche peut être coûteuse.

C'est justement là que l'entretien devient intéressant.

---

# 23. Question d'optimisation

Le recruteur vous demande :

> **This query is too slow. What would you do?**

Ne répondez pas immédiatement :

> "I would add an index."

Votre démarche doit être :

```text
1. EXPLAIN
       ↓
2. EXPLAIN ANALYZE
       ↓
3. Identify bottleneck
       ↓
4. Check indexes
       ↓
5. Check joins
       ↓
6. Check filters
       ↓
7. Check data volume
       ↓
8. Optimize
       ↓
9. Benchmark again
```

---

# 24. EXPLAIN ANALYZE

Exemple :

```sql
EXPLAIN ANALYZE
SELECT *
FROM transactions
WHERE customer_id = 123;
```

PostgreSQL vous donne des informations sur :

* execution time ;
* scans ;
* joins ;
* estimated rows ;
* actual rows ;
* index usage.

---

# 25. Sequential Scan vs Index Scan

Si PostgreSQL fait :

```text
Seq Scan
```

il parcourt beaucoup ou toutes les lignes.

Un index peut permettre :

```text
Index Scan
```

sur une recherche sélective.

Exemple :

```sql
CREATE INDEX idx_transactions_customer
ON transactions(customer_id);
```

---

# 26. Mais attention aux index

Question d'entretien :

> **Should we index every column?**

Réponse :

> No. Indexes improve read performance for certain queries but increase storage requirements and can slow down inserts and updates. I would create indexes based on actual query patterns and verify their usefulness with execution plans.

⭐ Excellente réponse.

---

# 27. Index composite

Supposons que vous faites souvent :

```sql
SELECT *
FROM transactions
WHERE customer_id = 123
  AND timestamp >= NOW() - INTERVAL '30 days';
```

Un index intéressant peut être :

```sql
CREATE INDEX idx_customer_timestamp
ON transactions(customer_id, timestamp);
```

L'ordre des colonnes compte.

---

# 28. Pourquoi `(customer_id, timestamp)` ?

Parce que la requête filtre d'abord sur :

```text
customer_id
```

puis sur :

```text
timestamp
```

L'index correspond donc bien au pattern.

---

# 29. Question très importante : `COUNT(*)` vs `COUNT(column)`

### `COUNT(*)`

Compte les lignes.

```sql
COUNT(*)
```

### `COUNT(column)`

Ignore les `NULL`.

```sql
COUNT(device_id)
```

Si :

```text
device_id
---------
A
B
NULL
```

alors :

```text
COUNT(*) = 3
COUNT(device_id) = 2
```

---

# 30. Question de fraude

> Find customers who used more than 5 devices in the last 30 days.

On peut commencer par :

```sql
SELECT
    customer_id,
    COUNT(DISTINCT device_id) AS device_count
FROM transactions
WHERE timestamp >= NOW() - INTERVAL '30 days'
GROUP BY customer_id
HAVING COUNT(DISTINCT device_id) > 5;
```

Très bonne feature :

```text
unique_devices_30d
```

---

# 31. Une autre feature intéressante

> How many customers used the same device?

```sql
SELECT
    device_id,
    COUNT(DISTINCT customer_id) AS customer_count
FROM transactions
GROUP BY device_id
HAVING COUNT(DISTINCT customer_id) > 5;
```

Vous pouvez ensuite transformer cela en feature :

```text
device_customer_count
```

---

# 32. Détection d'un appareil suspect

Supposons que nous souhaitons calculer :

```text
device_fraud_rate
```

```sql
SELECT
    device_id,
    COUNT(*) AS transaction_count,
    SUM(
        CASE WHEN is_fraud = 1 THEN 1 ELSE 0 END
    ) AS fraud_count,
    AVG(
        CASE WHEN is_fraud = 1 THEN 1.0 ELSE 0.0 END
    ) AS fraud_rate
FROM transactions
GROUP BY device_id;
```

Puis :

```text
device_fraud_rate
```

peut être injecté dans le modèle.

---

# 33. ⚠️ Mais attention au Leakage

C'est ici que vous pouvez vraiment impressionner le recruteur.

La requête précédente utilise :

```text
toutes les transactions
```

Donc elle pourrait utiliser des fraudes **futures**.

Pour une feature de scoring au temps `T`, il faut utiliser uniquement :

```text
events < T
```

---

# 34. Question Senior

> **How would you calculate a historical fraud rate without leaking future information?**

### Réponse

> I would calculate the fraud rate using only events that occurred before the prediction timestamp. For offline training, I would use point-in-time joins or time-aware aggregations so that each training example only sees information that would have been available at prediction time.

Très bonne réponse.

---

# 35. SQL + ML : pipeline complet

Vous pouvez maintenant imaginer :

```text
PostgreSQL
     │
     ▼
SQL feature engineering
     │
     ├── transaction_count_24h
     ├── avg_amount_30d
     ├── device_customer_count
     ├── time_since_previous
     └── historical_fraud_rate
     │
     ▼
Pandas
     │
     ▼
Feature validation
     │
     ▼
XGBoost
```

---

# 36. Exercice d'entretien — Niveau 1

### Question

> Find the top 3 transactions by amount for every customer.

Essayez d'abord seul.

### Solution

```sql
WITH ranked AS (
    SELECT
        *,
        ROW_NUMBER() OVER (
            PARTITION BY customer_id
            ORDER BY amount DESC
        ) AS rn
    FROM transactions
)

SELECT *
FROM ranked
WHERE rn <= 3;
```

---

# 37. Niveau 2

> Find the top 3 customers by total transaction amount in each country.

```sql
WITH customer_totals AS (
    SELECT
        c.country,
        t.customer_id,
        SUM(t.amount) AS total_amount
    FROM transactions t
    JOIN customers c
        ON t.customer_id = c.customer_id
    GROUP BY
        c.country,
        t.customer_id
),

ranked AS (
    SELECT
        *,
        ROW_NUMBER() OVER (
            PARTITION BY country
            ORDER BY total_amount DESC
        ) AS rn
    FROM customer_totals
)

SELECT *
FROM ranked
WHERE rn <= 3;
```

---

# 38. Niveau 3 — Très important

> Find customers whose current transaction is more than 5 times their historical average amount.

Nous devons éviter de comparer avec une moyenne qui inclut la transaction actuelle.

```sql
WITH history AS (
    SELECT
        *,
        AVG(amount) OVER (
            PARTITION BY customer_id
            ORDER BY timestamp
            ROWS BETWEEN UNBOUNDED PRECEDING
            AND 1 PRECEDING
        ) AS previous_avg_amount
    FROM transactions
)

SELECT
    *,
    amount / NULLIF(previous_avg_amount, 0)
        AS amount_ratio
FROM history
WHERE previous_avg_amount IS NOT NULL
  AND amount > 5 * previous_avg_amount;
```

### Pourquoi `NULLIF` ?

Pour éviter une division par zéro.

---

# 39. Niveau 4 — Fraude

> Find customers who made at least 5 transactions within a 10-minute period.

C'est un problème beaucoup plus intéressant.

Une approche :

```sql
SELECT
    t1.customer_id,
    t1.timestamp,
    COUNT(*) AS transaction_count
FROM transactions t1
JOIN transactions t2
    ON t1.customer_id = t2.customer_id
   AND t2.timestamp BETWEEN
       t1.timestamp - INTERVAL '10 minutes'
       AND t1.timestamp
GROUP BY
    t1.customer_id,
    t1.timestamp
HAVING COUNT(*) >= 5;
```

⚠️ Sur de très gros volumes, cette solution peut être coûteuse.

Le recruteur peut alors vous demander :

> **How would you optimize this query for billions of rows?**

---

# 40. Réponse d'optimisation

Vous devez réfléchir à plusieurs niveaux :

### 1. Index

```text
(customer_id, timestamp)
```

### 2. Partitionnement temporel

Par exemple :

```text
transactions_2026_01
transactions_2026_02
transactions_2026_03
```

ou partitionnement natif PostgreSQL par date.

### 3. Pré-agrégation

Calculer des statistiques de vélocité dans un système de streaming.

### 4. Architecture

Pour du temps réel :

```text
Kafka
 ↓
Flink
 ↓
Redis
```

plutôt que de recalculer les mêmes agrégations dans PostgreSQL pour chaque transaction.

⭐ Cette dernière réponse relie **SQL + architecture + ML**, ce qui est exactement ce que nous cherchons.

---

# 41. Ce que vous devez savoir par cœur

## Niveau obligatoire

```text
SELECT
WHERE
GROUP BY
HAVING
ORDER BY
JOIN
CASE
COUNT
SUM
AVG
MIN
MAX
```

## Niveau entretien Data Scientist

```text
CTE
ROW_NUMBER
RANK
DENSE_RANK
LAG
LEAD
PARTITION BY
OVER
```

## Niveau avancé

```text
EXPLAIN ANALYZE
Indexes
Composite indexes
Partitioning
Query optimization
Time-based queries
Point-in-time correctness
```

---

# 🎯 Test final du module SQL

Vous êtes maintenant face au recruteur.

Il vous donne :

```text
transactions
-------------------------
transaction_id
customer_id
timestamp
amount
device_id
ip_address
is_fraud
```

Et pose **5 questions**.

### Question 1

> Find the latest transaction for each customer.

### Question 2

> Find customers who made more than 10 transactions in the last 24 hours.

### Question 3

> Calculate each customer's average transaction amount before their current transaction.

### Question 4

> Find transactions whose amount is more than 5 times the customer's historical average.

### Question 5

> How would you optimize these queries if the table contains 5 billion rows?

---

# 🧠 Les réponses à retenir

### Q1

```sql
ROW_NUMBER() OVER (
    PARTITION BY customer_id
    ORDER BY timestamp DESC
)
```

### Q2

```sql
GROUP BY customer_id
HAVING COUNT(*) > 10
```

avec un filtre temporel.

### Q3

```sql
AVG(amount) OVER (
    PARTITION BY customer_id
    ORDER BY timestamp
    ROWS BETWEEN UNBOUNDED PRECEDING AND 1 PRECEDING
)
```

### Q4

Comparer :

```text
current_amount
```

à :

```text
historical_average
```

sans inclure la transaction actuelle.

### Q5

Penser :

```text
EXPLAIN ANALYZE
      ↓
Indexes
      ↓
Composite indexes
      ↓
Partitioning
      ↓
Pre-aggregation
      ↓
Caching
      ↓
Streaming architecture
```

---

# 🔥 Question piège finale

Le recruteur vous dit :

> **You have a query that takes 30 seconds. You add an index, but it still takes 30 seconds. Why?**

Ne dites pas :

> "The index doesn't work."

Il peut y avoir plusieurs raisons :

* la requête retourne une très grande partie de la table ;
* l'index n'est pas adapté au filtre ;
* mauvaise sélectivité ;
* fonction appliquée sur une colonne indexée ;
* mauvais ordre dans un index composite ;
* coût des JOIN ;
* tri coûteux ;
* agrégation massive ;
* estimation incorrecte du planner ;
* statistiques obsolètes ;
* I/O ;
* requête mal structurée.

La bonne démarche :

> **Measure → inspect execution plan → identify bottleneck → optimize → benchmark again.**

---

## 🚀 Étape suivante : MODULE 19 — Machine Learning avancé

Nous allons maintenant attaquer la partie **la plus importante de l'entretien Data Scientist** :

### Machine Learning de niveau entretien

Nous allons traiter :

```text
Classification
↓
Logistic Regression
↓
Decision Trees
↓
Random Forest
↓
XGBoost
↓
Cross Validation
↓
Hyperparameter Tuning
↓
Imbalanced Learning
↓
Calibration
↓
Threshold Optimization
↓
Feature Importance
↓
SHAP
↓
Model Interpretability
↓
Data Leakage
↓
Model Selection
```

Et surtout, nous allons faire un **cas complet de fraude**, depuis les données jusqu'au choix du seuil optimal, avec des questions de recruteur du type :

> **Why XGBoost instead of Logistic Regression?**

> **Why is accuracy useless here?**

> **Would you optimize for Recall or Precision?**

> **How would you choose the classification threshold?**

> **How would you explain a fraud prediction to a risk analyst?**

> **How do you know your model is actually learning fraud patterns and not memorizing customers?**
