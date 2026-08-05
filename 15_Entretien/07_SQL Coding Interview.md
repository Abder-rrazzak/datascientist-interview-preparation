Nous allons maintenant réaliser le **MODULE 30**, qui correspond à la partie **SQL Coding Interview**.

> Chez SentiLink, Stripe, Airbnb, Meta ou Uber, **le SQL est souvent l'épreuve qui élimine le plus de candidats**.

Pourquoi ?

Parce qu'un Data Scientist passe souvent **40 à 60 % de son temps à écrire du SQL**, pas du Machine Learning.

---

# MODULE 30 — SQL Coding Interview (Niveau SentiLink)

## Format

Je vais reproduire un véritable entretien.

Pour chaque exercice, nous verrons :

* l'énoncé ;
* le raisonnement attendu ;
* la solution ;
* les questions de relance ;
* les erreurs fréquentes.

---

# Exercice 1 — Top Customers

## Base de données

### customers

| customer_id | name  |
| ----------- | ----- |
| 1           | Alice |
| 2           | Bob   |
| 3           | John  |

### transactions

| transaction_id | customer_id | amount |
| -------------- | ----------- | -----: |
| 101            | 1           |    100 |
| 102            | 1           |    250 |
| 103            | 2           |     50 |
| 104            | 3           |    400 |
| 105            | 3           |    150 |

---

## Question

> **Return the total amount spent by each customer.**

---

## Réflexion attendue

Le recruteur veut entendre :

* une jointure entre les tables ;
* un regroupement par client ;
* une agrégation (`SUM`).

---

## Solution

```sql
SELECT
    c.customer_id,
    c.name,
    SUM(t.amount) AS total_amount
FROM customers c
JOIN transactions t
    ON c.customer_id = t.customer_id
GROUP BY
    c.customer_id,
    c.name;
```

---

## Question de relance

> **What happens if a customer has no transactions?**

Réponse :

Avec un `JOIN` (INNER JOIN), ce client n'apparaîtra pas.

Pour inclure tous les clients :

```sql
SELECT
    c.customer_id,
    c.name,
    COALESCE(SUM(t.amount), 0) AS total_amount
FROM customers c
LEFT JOIN transactions t
    ON c.customer_id = t.customer_id
GROUP BY
    c.customer_id,
    c.name;
```

---

# Exercice 2 — Latest Transaction

## Question

> **Return the latest transaction for each customer.**

---

## Solution idéale

```sql
WITH ranked AS (
    SELECT
        *,
        ROW_NUMBER() OVER (
            PARTITION BY customer_id
            ORDER BY transaction_date DESC
        ) AS rn
    FROM transactions
)

SELECT *
FROM ranked
WHERE rn = 1;
```

---

## Pourquoi `ROW_NUMBER()` ?

Le recruteur veut vérifier votre maîtrise des **Window Functions**.

---

## Question de relance

> **Why not use MAX(transaction_date)?**

Bonne réponse :

`MAX()` donne uniquement la date.

Il ne permet pas de récupérer directement toutes les autres colonnes de la ligne (montant, identifiant, etc.).

---

# Exercice 3 — Deuxième plus grosse transaction

Très classique.

## Question

> **Return the second largest transaction.**

---

## Solution

```sql
WITH ranked AS (
    SELECT
        amount,
        DENSE_RANK() OVER (
            ORDER BY amount DESC
        ) AS rnk
    FROM transactions
)

SELECT amount
FROM ranked
WHERE rnk = 2;
```

---

## Pourquoi `DENSE_RANK()` ?

Supposons :

| amount |
| -----: |
|   1000 |
|    900 |
|    900 |
|    800 |

`DENSE_RANK()` produit :

| amount | rank |
| -----: | ---: |
|   1000 |    1 |
|    900 |    2 |
|    900 |    2 |
|    800 |    3 |

Le "deuxième plus grand montant" est bien **900**.

---

# Exercice 4 — Détection de fraude

Table :

| customer | fraud |
| -------- | ----: |
| A        |     1 |
| A        |     0 |
| A        |     1 |
| B        |     0 |
| B        |     0 |

---

## Question

> **Compute the fraud rate for each customer.**

---

## Solution

```sql
SELECT
    customer,
    AVG(fraud) AS fraud_rate
FROM transactions
GROUP BY customer;
```

---

## Pourquoi cela fonctionne ?

Parce que :

```text
fraud

0 ou 1
```

La moyenne correspond directement à la proportion de fraudes.

Exemple :

```text
1

0

1
```

↓

```text
(1+0+1)/3

=

0.667
```

---

# Exercice 5 — Clients inactifs

Question très fréquente.

> **Find customers who made no transactions during the last 90 days.**

---

## Solution

```sql
SELECT
    c.customer_id,
    c.name
FROM customers c
LEFT JOIN transactions t
    ON c.customer_id = t.customer_id
    AND t.transaction_date >= CURRENT_DATE - INTERVAL '90 days'
WHERE t.transaction_id IS NULL;
```

---

## Pourquoi la condition de date est-elle dans le `JOIN` ?

C'est un piège classique.

Si vous mettez la condition dans le `WHERE`, vous transformez le `LEFT JOIN` en `INNER JOIN` et vous perdez les clients sans transaction récente.

---

# Exercice 6 — Running Total

Très fréquent.

Table :

| customer | amount |
| -------- | -----: |
| A        |    100 |
| A        |    200 |
| A        |     50 |

---

## Résultat attendu

| amount | cumulative |
| -----: | ---------: |
|    100 |        100 |
|    200 |        300 |
|     50 |        350 |

---

## Solution

```sql
SELECT
    customer,
    amount,
    SUM(amount) OVER (
        PARTITION BY customer
        ORDER BY transaction_date
    ) AS running_total
FROM transactions;
```

---

# Exercice 7 — Time Since Previous Transaction

Très populaire en FinTech.

---

## Solution

```sql
SELECT
    customer_id,
    transaction_date,
    transaction_date -
    LAG(transaction_date) OVER (
        PARTITION BY customer_id
        ORDER BY transaction_date
    ) AS time_since_previous
FROM transactions;
```

---

## Pourquoi cette feature est-elle utile ?

Parce qu'une rafale de transactions en quelques secondes ou minutes peut être un indicateur de fraude.

---

# Exercice 8 — Détection de doublons

Question :

> **Find duplicate transactions.**

---

## Solution

```sql
SELECT
    customer_id,
    amount,
    transaction_date,
    COUNT(*) AS occurrences
FROM transactions
GROUP BY
    customer_id,
    amount,
    transaction_date
HAVING COUNT(*) > 1;
```

---

# Exercice 9 — Top 3 clients par pays

Question difficile.

---

## Solution

```sql
WITH ranked AS (
    SELECT
        country,
        customer_id,
        SUM(amount) AS total_amount,
        ROW_NUMBER() OVER (
            PARTITION BY country
            ORDER BY SUM(amount) DESC
        ) AS rn
    FROM transactions
    GROUP BY
        country,
        customer_id
)

SELECT *
FROM ranked
WHERE rn <= 3;
```

---

# Les pièges préférés des recruteurs

## Piège 1

Utiliser :

```sql
SELECT *
```

en production.

Toujours sélectionner uniquement les colonnes nécessaires.

---

## Piège 2

Oublier la clause :

```sql
ON
```

dans un `JOIN`.

Cela produit un **produit cartésien**, souvent catastrophique.

---

## Piège 3

Utiliser `HAVING` au lieu de `WHERE` sans raison.

Règle :

* `WHERE` filtre les lignes avant l'agrégation ;
* `HAVING` filtre les groupes après l'agrégation.

---

## Piège 4

Ne pas gérer les valeurs `NULL`.

Le recruteur peut demander :

> **What happens if amount is NULL?**

Vous devez savoir que les fonctions d'agrégation comme `SUM()` ignorent les `NULL`, tandis que des fonctions comme `COUNT(column)` ne comptent pas les valeurs `NULL`.

---

# Questions rapides ("Rapid Fire")

Le recruteur peut enchaîner :

### Q1

**Difference between COUNT(*) and COUNT(column)?**

**Réponse :**

* `COUNT(*)` compte toutes les lignes.
* `COUNT(column)` compte uniquement les lignes où cette colonne n'est pas `NULL`.

---

### Q2

**Difference between WHERE and HAVING?**

* `WHERE` : avant l'agrégation.
* `HAVING` : après l'agrégation.

---

### Q3

**Difference between RANK and DENSE_RANK?**

* `RANK()` laisse des "trous" après des égalités.
* `DENSE_RANK()` ne laisse pas de trous.

---

### Q4

**When would you use a CTE?**

Pour rendre une requête complexe plus lisible, réutiliser un résultat intermédiaire et simplifier la maintenance.

---

### Q5

**How would you optimize a slow query?**

Réponse structurée :

1. Examiner le plan d'exécution (`EXPLAIN`).
2. Vérifier les index.
3. Éviter `SELECT *`.
4. Réduire le volume de données traité le plus tôt possible.
5. Revoir les jointures et les agrégations.

---

# Étude de cas SentiLink

Le recruteur termine avec une question ouverte :

> **We receive one billion transactions every day. How would you compute fraud features efficiently?**

### Réponse attendue

Je structurerais ma réponse ainsi :

1. **Identifier les features** (par exemple : nombre de transactions sur 1 h, 24 h, 7 jours, montant moyen, nombre d'appareils utilisés).
2. **Calcul incrémental** plutôt que de recalculer l'historique complet.
3. **Partitionnement** des données par date et/ou client.
4. **Traitement distribué** (Spark, Flink ou équivalent) pour les calculs massifs.
5. **Feature Store** pour partager les mêmes features entre l'entraînement et la production.
6. **Cache** pour les features temps réel à faible latence.
7. **Surveillance** de la qualité et de la fraîcheur des features.

Cette réponse montre que vous pensez à la fois **algorithmes**, **architecture** et **production**, ce qui est exactement le profil recherché chez SentiLink.

---

## Évaluation attendue

Si vous maîtrisez les exercices de ce module sans aide, vous serez au niveau attendu pour la majorité des entretiens SQL de Data Scientist dans des entreprises de haut niveau.

### Prochain module : **MODULE 31 — Python avancé pour Data Scientists**

Nous traiterons des questions de codage plus complexes (algorithmes, structures de données, générateurs, décorateurs, complexité, traitement de gros volumes de données et optimisation), avec des exercices du niveau Amazon, Google et SentiLink.
