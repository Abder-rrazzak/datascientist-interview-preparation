Nous allons passer à une étape **beaucoup plus pratique** : après avoir appris les concepts, il faut maintenant être capable de **résoudre des problèmes techniques sous pression**.

# MODULE 14 — Coding Interview : Python + SQL + Machine Learning

L'objectif est de vous entraîner comme lors d'un véritable entretien Data Scientist.

Pour chaque exercice, je vous donne :

1. 🎯 **Question du recruteur**
2. 🧠 **Comment réfléchir**
3. 💻 **Solution**
4. ⭐ **Réponse orale à donner**
5. ⚠️ **Piège à éviter**

---

# PARTIE 1 — Python

## Exercice 1 — Compter les fréquences

Le recruteur vous donne :

```python
transactions = [
    "A",
    "B",
    "A",
    "C",
    "B",
    "A"
]
```

Il demande :

> Écrivez une fonction qui retourne le nombre d'occurrences de chaque élément.

### Solution simple

```python
from collections import Counter

def count_transactions(transactions):
    return Counter(transactions)
```

Résultat :

```python
{
    "A": 3,
    "B": 2,
    "C": 1
}
```

### Réponse orale

> J'utiliserais `Counter` de la bibliothèque standard, car il est conçu précisément pour compter les occurrences et fournit une solution claire et efficace.

---

# Exercice 2 — Trouver les doublons

```python
transactions = [101, 102, 103, 101, 104, 102]
```

Question :

> Trouvez les transactions dupliquées.

### Solution

```python
def find_duplicates(values):
    seen = set()
    duplicates = set()

    for value in values:
        if value in seen:
            duplicates.add(value)
        else:
            seen.add(value)

    return duplicates
```

Résultat :

```python
{101, 102}
```

### Pourquoi utiliser `set` ?

Parce que la recherche :

```python
value in set
```

est en moyenne en **O(1)**.

---

# Question d'entretien

> Quelle est la complexité de votre algorithme ?

### Réponse

Le parcours est effectué une seule fois.

Donc :

```text
Temps : O(n)
Espace : O(n)
```

⭐ Cette question est très fréquente.

Il ne suffit pas de savoir coder.

Il faut savoir **analyser la complexité**.

---

# Exercice 3 — Deux sommes

Le recruteur demande :

> Étant donné une liste de nombres et une cible, trouvez deux nombres dont la somme est égale à la cible.

```python
nums = [2, 7, 11, 15]
target = 9
```

Réponse :

```text
2 + 7 = 9
```

### Mauvaise solution

Deux boucles imbriquées :

```python
for i in range(len(nums)):
    for j in range(i + 1, len(nums)):
        if nums[i] + nums[j] == target:
            return i, j
```

Complexité :

```text
O(n²)
```

---

### Solution optimale

```python
def two_sum(nums, target):
    seen = {}

    for i, num in enumerate(nums):
        complement = target - num

        if complement in seen:
            return seen[complement], i

        seen[num] = i

    return None
```

Complexité :

```text
Temps : O(n)
Espace : O(n)
```

### ⭐ Réponse orale

> J'utilise un dictionnaire pour mémoriser les valeurs déjà rencontrées. Pour chaque nombre, je recherche son complément dans le dictionnaire, ce qui permet de passer d'une complexité O(n²) à O(n).

---

# PARTIE 2 — Pandas

Supposons ce DataFrame :

```python
import pandas as pd

df = pd.DataFrame({
    "customer_id": [1, 1, 2, 2, 3],
    "amount": [100, 200, 50, 70, 500],
    "fraud": [0, 1, 0, 0, 1]
})
```

---

# Exercice 4 — Montant moyen par client

Question :

> Calculez le montant moyen des transactions pour chaque client.

```python
df.groupby("customer_id")["amount"].mean()
```

Résultat :

```text
customer_id
1    150
2     60
3    500
```

---

# Exercice 5 — Ajouter la moyenne à chaque ligne

Très important en Data Science.

On veut :

| customer_id | amount | avg_amount |
| ----------: | -----: | ---------: |
|           1 |    100 |        150 |
|           1 |    200 |        150 |
|           2 |     50 |         60 |
|           2 |     70 |         60 |
|           3 |    500 |        500 |

### Solution

```python
df["avg_amount"] = (
    df.groupby("customer_id")["amount"]
      .transform("mean")
)
```

### Pourquoi `transform()` plutôt que `mean()` ?

Parce que :

```python
groupby().mean()
```

réduit le nombre de lignes.

Alors que :

```python
groupby().transform()
```

retourne une série de même longueur que le DataFrame original.

⭐ Très bonne question d'entretien.

---

# Exercice 6 — Identifier une transaction anormalement élevée

Créer :

```text
amount_ratio
```

qui représente :

```text
montant transaction
-------------------
montant moyen client
```

### Solution

```python
df["amount_ratio"] = (
    df["amount"] / df["avg_amount"]
)
```

Une transaction :

```text
amount_ratio = 8
```

signifie que le montant est **8 fois supérieur à la moyenne habituelle du client**.

C'est une excellente feature pour un système de fraude.

---

# PARTIE 3 — SQL

Supposons :

### `customers`

```text
customer_id
name
country
```

### `transactions`

```text
transaction_id
customer_id
amount
transaction_date
is_fraud
```

---

# Exercice 7 — Clients ayant effectué au moins une fraude

```sql
SELECT DISTINCT c.customer_id, c.name
FROM customers c
JOIN transactions t
    ON c.customer_id = t.customer_id
WHERE t.is_fraud = 1;
```

---

# Exercice 8 — Montant total par client

```sql
SELECT
    customer_id,
    SUM(amount) AS total_amount
FROM transactions
GROUP BY customer_id;
```

---

# Exercice 9 — Top 10 clients par montant

```sql
SELECT
    customer_id,
    SUM(amount) AS total_amount
FROM transactions
GROUP BY customer_id
ORDER BY total_amount DESC
LIMIT 10;
```

---

# Exercice 10 — Clients ayant plus de 10 transactions

```sql
SELECT
    customer_id,
    COUNT(*) AS transaction_count
FROM transactions
GROUP BY customer_id
HAVING COUNT(*) > 10;
```

---

# Question d'entretien

Pourquoi `HAVING` et pas `WHERE` ?

### Réponse

Parce que `COUNT(*)` est calculé après le regroupement.

`WHERE` filtre les lignes avant le `GROUP BY`, tandis que `HAVING` filtre les groupes après l'agrégation.

---

# Exercice 11 — Dernière transaction de chaque client

C'est une question **très importante**.

```sql
WITH ranked_transactions AS (
    SELECT
        *,
        ROW_NUMBER() OVER (
            PARTITION BY customer_id
            ORDER BY transaction_date DESC
        ) AS rn
    FROM transactions
)

SELECT *
FROM ranked_transactions
WHERE rn = 1;
```

---

# Exercice 12 — Montant moyen des 7 derniers jours

Voici une question plus avancée.

```sql
SELECT
    customer_id,
    transaction_date,
    amount,
    AVG(amount) OVER (
        PARTITION BY customer_id
        ORDER BY transaction_date
        ROWS BETWEEN 6 PRECEDING AND CURRENT ROW
    ) AS rolling_avg
FROM transactions;
```

⚠️ Attention : `ROWS BETWEEN 6 PRECEDING` signifie les **6 lignes précédentes**, pas nécessairement les **7 derniers jours calendaires**.

Cette distinction peut impressionner un recruteur si vous la mentionnez.

---

# PARTIE 4 — Machine Learning

Passons maintenant à une situation réelle.

Votre dataset contient :

```text
10 000 000 transactions
```

dont :

```text
0.2 % frauduleuses
```

---

# Question 13

> Quel problème voyez-vous immédiatement ?

### Réponse

Nous avons un fort déséquilibre des classes.

Une accuracy très élevée pourrait être obtenue simplement en prédisant presque toujours la classe majoritaire.

Je privilégierais donc :

* Precision ;
* Recall ;
* F1 ;
* PR-AUC ;
* éventuellement ROC-AUC ;
* et surtout une métrique ou fonction de coût liée au métier.

---

# Question 14

> Comment découpez-vous vos données ?

### Mauvaise réponse

```python
train_test_split(X, y)
```

sans réfléchir.

### Meilleure réponse

Pour un problème temporel de fraude, je privilégierais généralement un **split temporel**.

Par exemple :

```text
Janvier → Juin
Training

Juillet
Validation

Août
Test
```

Pourquoi ?

Parce qu'en production, on entraîne sur le passé pour prédire le futur.

---

# Pourquoi le split aléatoire peut être dangereux ?

Supposons :

```text
Transaction A
10 janvier
```

et

```text
Transaction B
12 janvier
```

Si elles sont presque identiques et sont réparties aléatoirement entre train et test, le modèle peut bénéficier indirectement d'informations futures.

Cela peut conduire à une estimation trop optimiste.

---

# Question 15 — Data Leakage

Le recruteur demande :

> Qu'est-ce que le Data Leakage ?

### Réponse

Le Data Leakage survient lorsqu'une information qui ne serait pas disponible au moment de la prédiction est utilisée indirectement pour entraîner le modèle.

Exemple :

```text
fraud_confirmed
```

est connu trois jours après la transaction.

Si je l'utilise pour prédire la fraude au moment de la transaction :

❌ Leakage.

---

# Exemple très important

Vous créez :

```text
customer_fraud_rate
```

en utilisant **toutes les transactions historiques**, y compris celles postérieures à la transaction que vous essayez de prédire.

❌ Potentiellement du leakage temporel.

---

# Comment éviter cela ?

Chaque feature doit respecter :

> **Point-in-time correctness**

Autrement dit :

> Au moment où la prédiction est effectuée, cette information doit réellement être disponible.

⭐ C'est une réponse de niveau Senior.

---

# Question 16 — Feature Engineering

Le recruteur vous donne :

```text
customer_id
timestamp
amount
device_id
ip_address
country
```

Question :

> Donnez-moi 10 features.

### Réponse

### Temporelles

1. `hour`
2. `day_of_week`
3. `is_weekend`

### Comportementales

4. `transactions_last_10min`
5. `transactions_last_24h`
6. `avg_amount_last_30d`
7. `amount_deviation_from_customer_mean`

### Device

8. `new_device`
9. `device_customer_count`

### IP / géographie

10. `ip_customer_count`
11. `country_changed`

Vous pouvez même aller plus loin :

```text
time_since_last_transaction
```

ou

```text
number_of_devices_last_30_days
```

---

# Question 17 — Choix du modèle

Le recruteur demande :

> Pourquoi commencer avec une régression logistique alors que XGBoost est plus puissant ?

### Réponse idéale

> Je commencerais par une baseline simple et interprétable. Cela me permet d'établir une référence solide, de vérifier le pipeline de données et de quantifier ensuite l'apport réel de modèles plus complexes comme XGBoost. Si XGBoost apporte seulement une amélioration marginale au prix d'une complexité importante, le modèle plus simple pourrait rester préférable.

⭐ Très bonne réponse.

---

# Question 18 — Pourquoi XGBoost ?

### Réponse

Pour des données tabulaires, XGBoost est souvent très performant et capture efficacement les interactions non linéaires entre les variables. Il offre également des mécanismes de régularisation, gère bien de nombreux types de features et s'intègre facilement dans des pipelines de production.

---

# PARTIE 5 — Métriques de fraude

Supposons :

```text
10 000 transactions
```

Il y a :

```text
100 fraudes
```

Votre modèle détecte :

```text
90 fraudes
```

Mais bloque :

```text
500 transactions légitimes
```

---

## Calcul du Recall

```text
Recall = TP / (TP + FN)
```

Donc :

```text
90 / 100 = 90 %
```

Excellent Recall.

Mais...

---

## Precision

Le modèle a prédit :

```text
90 + 500 = 590
```

transactions comme frauduleuses.

Donc :

```text
Precision = 90 / 590
```

≈

```text
15.3 %
```

Très faible.

---

# Question

> Le modèle est-il bon ?

### Réponse

Pas nécessairement.

Il détecte 90 % des fraudes mais génère énormément de faux positifs.

Je dois donc connaître le coût respectif :

* d'une fraude non détectée ;
* d'un client légitime bloqué.

---

# PARTIE 6 — Architecture

Le recruteur demande :

> Concevez un système de scoring de fraude.

Vous pouvez répondre :

```text
                    CLIENT
                       │
                       ▼
                 API Gateway
                       │
                       ▼
                Load Balancer
                       │
             ┌─────────┴─────────┐
             ▼                   ▼
        FastAPI #1          FastAPI #2
             │                   │
             └─────────┬─────────┘
                       ▼
                  Feature Store
                       │
                       ▼
                    Redis
                       │
                       ▼
                 XGBoost Model
                       │
                       ▼
                  Fraud Score
                       │
            ┌──────────┴─────────┐
            ▼                    ▼
         Approve                Review
```

Puis :

```text
Kafka
  │
  ▼
Flink
  │
  ▼
Real-time features
```

et :

```text
S3
 │
 ▼
Training Pipeline
 │
 ▼
MLflow
 │
 ▼
Model Registry
```

---

# Question 19 — Latence

Le système doit répondre en moins de :

```text
100 ms
```

Vous observez :

```text
Feature computation = 80 ms
Model inference = 5 ms
Database = 100 ms
Network = 20 ms
```

Total :

```text
205 ms
```

Le modèle n'est donc pas le problème.

### Réponse Senior

> Je profilerais la chaîne complète avant de modifier le modèle. Ici, le calcul des features et surtout l'accès à la base représentent les principaux goulots d'étranglement. Je chercherais à pré-calculer les features, utiliser un Feature Store ou Redis pour les features temps réel et réduire les accès synchrones à la base.

---

# PARTIE 7 — Question très importante : Production

Le recruteur demande :

> Votre modèle a 95 % de Recall en test. Une semaine après son déploiement, il tombe à 80 %. Pourquoi ?

Vous devez penser immédiatement à :

```text
Data Drift
Concept Drift
Training-Serving Skew
Data Quality
Label Delay
Feature Pipeline Failure
Threshold Change
Population Shift
```

---

# Question 20 — Que surveillez-vous ?

Je séparerais le monitoring en quatre catégories.

### 1. Infrastructure

```text
CPU
Memory
Latency
HTTP Errors
Throughput
```

### 2. Data

```text
Missing values
Distribution
Schema
Data Drift
```

### 3. Model

```text
Precision
Recall
PR-AUC
Calibration
False Positives
False Negatives
```

### 4. Business

```text
Fraud loss
Customer rejection rate
Manual review rate
Conversion
Revenue impact
```

⭐ Cette distinction est excellente en entretien.

---

# PARTIE 8 — Question comportementale

Le recruteur demande :

> Vous avez travaillé pendant deux semaines sur un modèle et votre manager décide de ne pas le mettre en production. Comment réagissez-vous ?

### Mauvaise réponse

> Je serais déçu parce que j'ai travaillé beaucoup dessus.

### Réponse mature

> Je chercherais d'abord à comprendre les raisons de la décision. Si les contraintes métier, de latence, de maintenance ou de performance ne permettent pas de justifier le modèle, je considérerais que l'objectif est de résoudre le problème métier et non de défendre mon approche initiale. J'analyserais les retours et chercherais à réutiliser les enseignements du travail réalisé.

---

# PARTIE 9 — Question comportementale SentiLink

Le poste indique notamment :

> **Follow Through**
> **Deep Understanding**
> **Whatever It Takes**
> **Do Something Smart**

Le recruteur peut vous demander :

> **Tell me about a time you went deep into a problem.**

### Structure idéale

```text
Situation
   ↓
Problème
   ↓
Investigation
   ↓
Découverte
   ↓
Action
   ↓
Résultat
   ↓
Leçon
```

---

# Exemple de réponse

> Dans un projet de Machine Learning, j'ai constaté que les performances semblaient bonnes globalement, mais qu'un segment particulier avait beaucoup plus de faux négatifs. Plutôt que de modifier immédiatement le modèle, j'ai analysé les données par segment et étudié les distributions des features. J'ai découvert que certaines variables étaient calculées différemment pour ce groupe. Après correction du pipeline et ajout de features comportementales, les performances se sont améliorées. Cette expérience m'a appris à toujours analyser les données et les erreurs avant de modifier l'algorithme.

---

# PARTIE 10 — Votre checklist avant entretien

Vous devez pouvoir expliquer **sans notes** :

### Python

* List / Tuple / Set / Dict
* Generator
* Decorator
* `*args`, `**kwargs`
* OOP
* Complexité Big-O
* Pandas
* NumPy

### SQL

* JOIN
* GROUP BY
* HAVING
* CTE
* Window Functions
* `ROW_NUMBER`
* `LAG`
* `LEAD`
* `RANK`
* `EXPLAIN ANALYZE`

### ML

* Bias / Variance
* Overfitting
* Cross-validation
* Regularization
* Feature Engineering
* Hyperparameter tuning
* Calibration
* Data Leakage

### Fraude

* Class imbalance
* Precision
* Recall
* F1
* PR-AUC
* Threshold optimization
* Anomaly Detection
* Concept Drift
* Graph ML

### Production

* FastAPI
* Docker
* AWS
* Kafka
* Redis
* Feature Store
* MLflow
* Monitoring
* Canary Deployment
* Shadow Deployment

---

# 🎯 Étape suivante : véritable entretien blanc

Nous avons maintenant suffisamment étudié la théorie.

**Je vous propose de changer complètement de méthode.**

Je vais devenir le recruteur.

Je ne vous donnerai **plus immédiatement les réponses**.

Je vous poserai une question, vous répondrez comme en entretien, puis je corrigerai votre réponse selon quatre critères :

| Critère              | Évaluation |
| -------------------- | ---------- |
| Exactitude technique | /5         |
| Raisonnement         | /5         |
| Communication        | /5         |
| Niveau Senior        | /5         |
| **Total**            | **/20**    |

Et après chaque réponse, je vous donnerai :

* ce qui était correct ;
* ce qui manquait ;
* une **réponse modèle niveau SentiLink** ;
* les questions de relance que le recruteur pourrait poser.

### 🎤 Entretien blanc — Question 1

> **You are given a dataset containing 10 million financial transactions, but only 0.2% are labeled as fraudulent. How would you build a fraud detection model?**

**Répondez comme si vous étiez réellement devant le recruteur.**

Ne cherchez pas à donner une réponse parfaite : **je veux d'abord évaluer votre niveau actuel.**
