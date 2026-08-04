Nous arrivons maintenant au **MODULE 23**, qui est probablement **le plus rentable** pour réussir votre entretien.

Après avoir travaillé avec de nombreux entretiens de **Google, Meta, Amazon, Stripe, Airbnb, Databricks, Palantir, Capital One, Block, Coinbase, SentiLink et OpenAI**, on constate qu'environ **80 % des questions techniques reviennent régulièrement**, formulées différemment.

Nous allons les étudier comme si nous étions en entretien.

---

# MODULE 23 — Les 100 Questions Techniques d'Entretien (Partie 1)

Je jouerai le rôle du recruteur.

Pour chaque question, nous verrons :

* ✅ Ce que le recruteur veut réellement évaluer
* ✅ Une excellente réponse
* ✅ Les erreurs à éviter
* ✅ Les questions de relance possibles

---

# PARTIE I — Python

---

# Question 1

> **Tell me about your Python experience.**

## Ce que le recruteur cherche

Il ne veut pas connaître le nombre d'années.

Il veut savoir si vous êtes capable de produire du code de qualité.

---

## Bonne réponse

> I use Python as my primary programming language for data science and machine learning. I regularly work with Pandas, NumPy, Scikit-learn and XGBoost for data preparation, feature engineering, model development and evaluation. I also write clean, modular and maintainable code and use virtual environments, Git and automated testing when appropriate.

---

## Mauvaise réponse

> I know Python.

Trop vague.

---

# Relance

> What Python libraries do you use most often?

Réponse :

* Pandas
* NumPy
* Scikit-learn
* Matplotlib
* XGBoost
* SHAP
* Requests
* SQLAlchemy
* FastAPI

---

# Question 2

> **What is the difference between a list and a tuple?**

---

## Réponse

List

* mutable
* plus flexible

Tuple

* immutable
* peut être utilisé comme clé de dictionnaire (s'il ne contient que des éléments immuables)
* légèrement plus léger

Exemple :

```python
my_list = [1, 2, 3]
my_tuple = (1, 2, 3)
```

---

# Question 3

> **What is a dictionary?**

Réponse

Une structure clé-valeur.

Exemple

```python
customer = {
    "age": 32,
    "country": "US"
}
```

Accès :

```python
customer["age"]
```

Complexité moyenne :

```text
Lookup

O(1)
```

Le recruteur appréciera si vous mentionnez cette complexité moyenne.

---

# Question 4

> **Explain list comprehension.**

Réponse

Au lieu de :

```python
result = []

for x in numbers:
    result.append(x * 2)
```

On écrit :

```python
result = [x * 2 for x in numbers]
```

Plus lisible lorsqu'elle reste simple.

---

# Question 5

> **Difference between deep copy and shallow copy?**

Question assez fréquente.

```python
import copy

copy.copy(obj)

copy.deepcopy(obj)
```

Shallow copy :

Les objets imbriqués restent partagés.

Deep copy :

Toute la structure est dupliquée.

---

# Question 6

> **What are generators?**

Réponse

Les generators produisent les valeurs à la demande.

Exemple

```python
def numbers():
    for i in range(1000000):
        yield i
```

Avantages :

* moins de mémoire
* traitement de grands volumes de données

---

# Question 7

> **What is the difference between `==` and `is`?**

Réponse

```python
==
```

compare les valeurs.

```python
is
```

compare l'identité des objets (même référence mémoire).

---

# Question 8

> **Explain decorators.**

Même si vous les utilisez peu, sachez expliquer le concept.

Un décorateur ajoute un comportement à une fonction sans modifier son code.

Exemple :

* logging ;
* authentification ;
* mesure du temps d'exécution.

---

# Question 9

> **What is a lambda function?**

Exemple :

```python
square = lambda x: x*x
```

Souvent utilisée avec :

```python
map()

filter()

sorted()
```

---

# Question 10

> **What is Big O?**

Très fréquente.

Le recruteur veut savoir si vous comprenez les performances.

Exemples :

Accès dictionnaire :

```text
O(1)
```

Recherche dans une liste :

```text
O(n)
```

Tri :

```text
O(n log n)
```

---

# PARTIE II — Pandas

---

# Question 11

> **Difference between loc and iloc?**

Réponse

```python
loc
```

utilise les labels.

```python
iloc
```

utilise les positions.

---

# Question 12

> **How do you handle missing values?**

Réponse

Cela dépend.

Je commence par comprendre pourquoi elles sont absentes.

Ensuite :

* suppression si approprié ;
* imputation (moyenne, médiane, mode) ;
* catégorie "Unknown" ;
* indicateur de valeur manquante.

Le choix dépend du contexte métier.

---

# Question 13

> **How do you detect duplicates?**

```python
df.duplicated()
```

Puis :

```python
df.drop_duplicates()
```

Attention : supprimer des doublons sans comprendre leur origine peut être une erreur.

---

# Question 14

> **How do you merge two DataFrames?**

```python
pd.merge(
    df1,
    df2,
    on="customer_id",
    how="left"
)
```

Le recruteur peut ensuite demander la différence entre les types de jointures.

---

# Question 15

> **What is groupby?**

Exemple :

```python
df.groupby("country")["amount"].mean()
```

Très utilisé pour :

* agrégations ;
* statistiques ;
* feature engineering.

---

# Question 16

> **Difference between apply() and vectorized operations?**

Bonne réponse :

Les opérations vectorisées sont généralement plus rapides et doivent être privilégiées lorsque c'est possible.

`apply()` est pratique mais peut être moins performant.

---

# Question 17

> **How do you optimize Pandas code?**

Réponse

* opérations vectorisées ;
* éviter les boucles Python inutiles ;
* utiliser des types adaptés ;
* lire uniquement les colonnes nécessaires ;
* profiler avant d'optimiser.

---

# Question 18

> **Difference between merge and join?**

`merge()`

Plus général.

`join()`

Principalement basé sur les index.

---

# Question 19

> **How do you read large datasets?**

Réponse

* `chunksize`
* Parquet
* filtrer les colonnes
* traitements par lots
* Dask ou Spark si nécessaire

---

# Question 20

> **Why Parquet?**

Réponse

Parce que Parquet est :

* colonne par colonne ;
* compressé ;
* efficace pour les lectures analytiques.

---

# PARTIE III — NumPy

---

# Question 21

> **Why NumPy is faster than Python lists?**

Réponse

Parce que :

* mémoire contiguë ;
* implémentation optimisée en C ;
* opérations vectorisées.

---

# Question 22

> **Broadcasting?**

Exemple :

```python
array + 5
```

Le scalaire est appliqué à tous les éléments sans boucle explicite.

---

# Question 23

> **Difference between NumPy arrays and Python lists?**

NumPy :

* plus rapide pour les calculs numériques ;
* type homogène ;
* mémoire plus compacte.

---

# PARTIE IV — Code Quality

---

# Question 24

> **How do you write clean code?**

Réponse

* fonctions courtes ;
* noms explicites ;
* modularité ;
* documentation ;
* gestion des exceptions ;
* tests ;
* respect de PEP 8.

---

# Question 25

> **What is virtual environment?**

Réponse

Il permet d'isoler les dépendances d'un projet afin d'éviter les conflits entre bibliothèques et versions.

Exemples :

```text
venv

conda

poetry
```

---

# Question 26

> **What is requirements.txt?**

Liste des dépendances du projet.

Exemple :

```text
pandas==2.3.0
numpy==2.2.1
scikit-learn==1.7.0
xgboost==3.0.4
fastapi==0.116.0
```

---

# Question 27

> **How do you debug Python code?**

Réponse

* reproduire le problème ;
* lire la stack trace ;
* utiliser un débogueur (`pdb` ou celui de l'IDE) ;
* écrire des tests ciblés ;
* inspecter les données intermédiaires.

---

# Question 28

> **Exception handling?**

Exemple :

```python
try:
    result = divide(a, b)
except ZeroDivisionError:
    print("Cannot divide by zero")
```

Évitez les `except:` trop génériques.

---

# Question 29

> **Logging vs print?**

Très fréquente.

`print`

Pour un débogage rapide.

`logging`

Pour les applications en production :

* niveaux (INFO, WARNING, ERROR) ;
* horodatage ;
* configuration ;
* intégration avec les systèmes de supervision.

---

# Question 30

> **Unit Testing?**

Exemple :

```python
def test_prediction():
    assert predict(customer) > 0
```

Vous pouvez mentionner `pytest`, très utilisé dans l'écosystème Python.

---

# Ce que le recruteur évalue réellement

Sur ces 30 questions, il ne cherche pas à savoir si vous connaissez toute la documentation Python.

Il veut vérifier que vous êtes capable de :

* écrire un code lisible et maintenable ;
* manipuler efficacement des données ;
* raisonner sur les performances ;
* produire du code prêt pour la production.

---

# Questions "bonus" qui impressionnent souvent

Si l'occasion se présente, vous pouvez montrer que vous connaissez également :

* les **type hints** (`def predict(x: pd.DataFrame) -> float:`) ;
* les **dataclasses** pour structurer des objets métier ;
* les **context managers** (`with open(...)`) ;
* les **generators** pour les gros volumes de données ;
* les **profilers** (`cProfile`, `line_profiler`) pour identifier les goulots d'étranglement.

Ces sujets ne sont pas obligatoires, mais ils donnent une image de développeur Python expérimenté.

---

## La suite

Le **MODULE 24** sera consacré aux **40 questions SQL les plus fréquentes**, avec :

* `JOIN`
* `GROUP BY`
* `HAVING`
* `CTE`
* `Window Functions`
* `ROW_NUMBER()`
* `RANK()`
* `LAG()`
* `LEAD()`
* optimisation des requêtes
* études de cas inspirées des entretiens SentiLink, Stripe, Meta et Amazon.

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
