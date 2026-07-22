Nous allons maintenant étudier la partie qui distingue les **bons développeurs SQL** des **Data Scientists expérimentés**.

Dans des entreprises comme **SentiLink**, **Stripe**, **Amazon**, **Meta** ou **Google**, les bases de données contiennent souvent **des centaines de millions, voire des milliards de lignes**. Une requête qui prend **30 minutes** peut parfois être optimisée à **quelques secondes**.

L'optimisation SQL est donc un sujet incontournable en entretien.

---

# MODULE 2 – Chapitre 12 : Optimisation SQL

# Objectifs

À la fin de ce chapitre, vous serez capable de :

* Comprendre pourquoi une requête est lente.
* Utiliser correctement les index.
* Lire un plan d'exécution (`EXPLAIN`).
* Écrire des requêtes performantes.
* Répondre aux questions d'entretien avancées.

---

# 1. Pourquoi une requête est-elle lente ?

Les causes les plus fréquentes sont :

* Absence d'index.
* `SELECT *`.
* Mauvais type de JOIN.
* Trop de sous-requêtes corrélées.
* Fonctions appliquées sur des colonnes indexées.
* Tri (`ORDER BY`) sur des colonnes non indexées.
* Tables très volumineuses.

---

# 2. Les index

Imaginez un dictionnaire.

Pour trouver le mot **"Fraud"**, vous n'ouvrez pas chaque page jusqu'à le trouver. Vous utilisez l'ordre alphabétique.

Un **index SQL** fonctionne de la même manière.

Sans index :

```text
Lire chaque ligne
```

Avec index :

```text
Accès direct
```

---

## Exemple

Table :

```
Transactions
```

10 millions de lignes.

Recherche :

```sql
SELECT *
FROM Transactions
WHERE customer_id = 145678;
```

### Sans index

La base parcourt les 10 millions de lignes (**Full Table Scan**).

Complexité :

```
O(n)
```

---

### Avec index

La base utilise directement l'index.

Complexité approximative :

```
O(log n)
```

Le gain de performance peut être considérable.

---

# Création d'un index

```sql
CREATE INDEX idx_customer
ON Transactions(customer_id);
```

---

## Question d'entretien

Pourquoi un index accélère-t-il les recherches ?

### Réponse attendue

Parce qu'il permet au moteur de base de données de localiser rapidement les lignes recherchées sans parcourir toute la table.

---

# 3. Les inconvénients des index

Question classique.

Les index :

✅ accélèrent les lectures.

Mais

❌ ralentissent :

* INSERT
* UPDATE
* DELETE

Pourquoi ?

Parce qu'il faut également mettre à jour les index.

---

## Question

Faut-il indexer toutes les colonnes ?

Réponse

Non.

Les index consomment de la mémoire et ralentissent les écritures.

On privilégie généralement :

* les colonnes utilisées dans `WHERE` ;
* les colonnes de `JOIN` ;
* les colonnes de `ORDER BY` ;
* les colonnes de `GROUP BY` (selon les cas).

---

# 4. EXPLAIN

Très fréquent en entretien.

```sql
EXPLAIN
SELECT *
FROM Transactions
WHERE customer_id=100;
```

Le moteur indique son plan d'exécution.

Il peut révéler :

* Full Table Scan
* Index Scan
* Nested Loop
* Hash Join
* Sort
* Aggregate

---

## Exemple simplifié

```
Seq Scan
```

Signifie :

Lecture complète de la table.

---

```
Index Scan
```

Signifie :

Utilisation d'un index.

---

## Question

Pourquoi utiliser `EXPLAIN` ?

Réponse

Pour comprendre comment le moteur exécute une requête et identifier les points de ralentissement.

---

# 5. Éviter SELECT *

Mauvaise pratique

```sql
SELECT *
FROM Transactions;
```

Bonne pratique

```sql
SELECT
transaction_id,
amount
FROM Transactions;
```

---

Pourquoi ?

Car :

* moins de données sont lues ;
* moins de données sont transférées ;
* moins de mémoire est utilisée.

---

# 6. Les fonctions sur les colonnes indexées

Supposons

```sql
WHERE YEAR(date_transaction)=2026;
```

L'index sur `date_transaction` risque de ne pas être utilisé.

Préférer :

```sql
WHERE date_transaction >= '2026-01-01'
AND date_transaction < '2027-01-01'
```

Très important.

---

# Question

Pourquoi éviter les fonctions sur une colonne indexée ?

Réponse

Parce qu'elles peuvent empêcher le moteur d'utiliser l'index.

---

# 7. OR vs UNION ALL

Requête

```sql
WHERE ville='Paris'
OR ville='Lyon'
```

Dans certains cas, cela peut être moins performant.

Alternative

```sql
SELECT ...
WHERE ville='Paris'

UNION ALL

SELECT ...
WHERE ville='Lyon'
```

Selon le SGBD et les index disponibles, cette réécriture peut être plus efficace.

---

# 8. EXISTS vs IN

Question classique.

```sql
SELECT *

FROM Customers

WHERE customer_id IN

(

SELECT customer_id

FROM Transactions

)
```

---

Alternative

```sql
SELECT *

FROM Customers c

WHERE EXISTS

(

SELECT 1

FROM Transactions t

WHERE t.customer_id=c.customer_id

)
```

---

## Différence

`EXISTS`

* s'arrête dès qu'une correspondance est trouvée ;
* est souvent plus performant pour de grands volumes.

`IN`

* peut être plus simple pour de petites listes ;
* dépend de l'optimiseur du SGBD.

---

# 9. Index composite

Très demandé.

Requête fréquente

```sql
WHERE customer_id=...

AND transaction_date=...
```

Créer deux index séparés n'est pas toujours optimal.

Créer un index composite :

```sql
(customer_id, transaction_date)
```

---

Question

Pourquoi ?

Réponse

Parce que les deux colonnes sont souvent utilisées ensemble dans les filtres.

---

# 10. Partitionnement

Imaginez

```
Transactions
```

avec

3 milliards de lignes.

On peut partitionner :

```
2024

2025

2026
```

Une requête sur 2026 ne lira que la partition correspondante.

---

# Question

Pourquoi partitionner une table ?

Réponse

Pour réduire la quantité de données parcourues lors des requêtes.

---

# Cas pratique SentiLink

Table

```
Transactions
```

Colonnes

```
customer_id

transaction_date

amount

device

fraud
```

Question

Vous recherchez

```sql
WHERE customer_id=...

AND transaction_date>=...

ORDER BY transaction_date
```

Quel index créeriez-vous ?

Réponse

```text
(customer_id, transaction_date)
```

Il accélère :

* le filtre sur `customer_id` ;
* la plage de dates ;
* l'ordre chronologique.

---

# Les erreurs SQL les plus fréquentes

## Erreur 1

```sql
SELECT *
```

Toujours éviter en production.

---

## Erreur 2

Utiliser une fonction sur une colonne indexée.

---

## Erreur 3

Oublier les index sur les clés de jointure.

---

## Erreur 4

Utiliser des sous-requêtes corrélées quand un `JOIN` ou une `Window Function` serait plus efficace.

Exemple à éviter :

```sql
SELECT
    c.customer_id,
    (
        SELECT COUNT(*)
        FROM Transactions t
        WHERE t.customer_id = c.customer_id
    ) AS nb_transactions
FROM Customers c;
```

Préférer :

```sql
SELECT
    c.customer_id,
    COUNT(t.transaction_id) AS nb_transactions
FROM Customers c
LEFT JOIN Transactions t
    ON c.customer_id = t.customer_id
GROUP BY c.customer_id;
```

---

# Exercice de performance

La requête suivante est très lente :

```sql
SELECT *
FROM Transactions
WHERE YEAR(transaction_date) = 2026
ORDER BY amount;
```

### Questions

1. Quels sont les problèmes de cette requête ?
2. Comment l'optimiser ?

### Réponse attendue

Problèmes :

* `SELECT *` récupère toutes les colonnes.
* `YEAR(transaction_date)` peut empêcher l'utilisation d'un index.
* `ORDER BY amount` peut nécessiter un tri coûteux si aucun index n'est adapté.

Version optimisée :

```sql
SELECT
    transaction_id,
    customer_id,
    amount,
    transaction_date
FROM Transactions
WHERE transaction_date >= '2026-01-01'
  AND transaction_date < '2027-01-01'
ORDER BY amount;
```

Et si cette requête est très fréquente, envisager :

* un index sur `transaction_date` ;
* ou un index composite selon les filtres réellement utilisés.

---

# Questions d'entretien (niveau avancé)

1. Pourquoi un index accélère-t-il une requête ?
2. Pourquoi ne faut-il pas indexer toutes les colonnes ?
3. À quoi sert `EXPLAIN` ?
4. Différence entre `Index Scan` et `Sequential Scan` ?
5. Pourquoi éviter `SELECT *` ?
6. Pourquoi une fonction sur une colonne indexée peut-elle dégrader les performances ?
7. Quand utiliser un index composite ?
8. Différence entre `EXISTS` et `IN` ?
9. Qu'est-ce que le partitionnement ?
10. Comment analyser une requête SQL lente ?

---

# Cas d'entretien complet (inspiré de SentiLink)

Vous disposez d'une table `transactions` contenant **500 millions de lignes** avec les colonnes :

* `transaction_id`
* `customer_id`
* `device_id`
* `transaction_date`
* `amount`
* `country`
* `fraud`

Une requête utilisée en production est devenue très lente :

```sql
SELECT
    customer_id,
    SUM(amount) AS total_amount
FROM transactions
WHERE transaction_date >= '2026-01-01'
  AND transaction_date < '2026-02-01'
  AND country = 'US'
GROUP BY customer_id
ORDER BY total_amount DESC
LIMIT 100;
```

### Ce que le recruteur attend

Vous devriez expliquer une démarche structurée :

1. Examiner le plan d'exécution avec `EXPLAIN`.
2. Vérifier les index existants.
3. Évaluer l'intérêt d'un index composite, par exemple `(country, transaction_date)` ou `(transaction_date, country)` selon la sélectivité et le SGBD.
4. Vérifier si la table est partitionnée par date.
5. Contrôler le coût du `GROUP BY` et du `ORDER BY`.
6. Mesurer les performances avant et après chaque modification.

---

## Fin du Module SQL

Vous maîtrisez désormais les principaux sujets SQL rencontrés en entretien :

* Requêtes de base
* Agrégations
* JOIN
* Sous-requêtes
* CTE
* Window Functions
* Optimisation
* Index
* Plans d'exécution
* Cas pratiques orientés Data Science

Le prochain module sera consacré au **Machine Learning**, où nous aborderons en profondeur :

* les algorithmes de classification et de régression ;
* la préparation des données ;
* la validation croisée ;
* l'optimisation des hyperparamètres ;
* l'interprétation des modèles ;
* et des études de cas de **détection de fraude**
