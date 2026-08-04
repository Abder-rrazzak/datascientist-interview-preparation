Nous arrivons maintenant au **MODULE 22**, qui est probablement **le plus important de toute la préparation**.

C'est exactement le type d'exercice qu'un recruteur de **SentiLink**, **Stripe**, **Airbnb**, **Capital One**, **Affirm** ou **Block** peut vous demander.

L'objectif est de montrer que vous êtes capable de construire **un projet complet de Data Science**, et pas seulement d'entraîner un modèle.

---

# MODULE 22 — Projet complet de Data Science (de la donnée brute au modèle en production)

# Objectif

Nous allons construire un pipeline complet de Machine Learning pour la détection de fraude.

Le pipeline suivra les étapes suivantes :

```text
Business Problem
        ↓
Data Collection
        ↓
Data Validation
        ↓
Exploratory Data Analysis
        ↓
Feature Engineering
        ↓
Train / Validation / Test
        ↓
Baseline Model
        ↓
Model Selection
        ↓
Hyperparameter Tuning
        ↓
Evaluation
        ↓
Threshold Optimization
        ↓
Explainability
        ↓
Deployment
        ↓
Monitoring
```

C'est exactement le workflow attendu chez SentiLink.

---

# Étape 1 : Comprendre le problème métier

Le recruteur commence rarement par parler de Machine Learning.

Il demande d'abord :

> **What business problem are we trying to solve?**

Votre réponse :

> We want to identify fraudulent applications before they are approved while minimizing false positives that would negatively impact legitimate customers.

Le problème n'est pas de maximiser l'accuracy.

Le problème est de réduire le coût global de la fraude tout en limitant les frictions pour les bons clients.

---

# Étape 2 : Comprendre les données

Supposons que nous disposions de cette table.

| customer_id | amount | device_id | ip  | country | timestamp  | fraud |
| ----------- | ------ | --------- | --- | ------- | ---------- | ----- |
| 101         | 120    | A12       | IP1 | US      | 2026-01-01 | 0     |
| 102         | 950    | B91       | IP7 | US      | 2026-01-01 | 1     |
| 103         | 45     | A12       | IP1 | US      | 2026-01-02 | 0     |

Le label :

```text
fraud = 1

legitimate = 0
```

---

# Étape 3 : Data Validation

Première question :

Les données sont-elles fiables ?

On vérifie :

* valeurs manquantes ;
* doublons ;
* types incorrects ;
* dates invalides ;
* montants négatifs ;
* incohérences.

Exemple Python :

```python
df.info()

df.describe()

df.isnull().sum()

df.duplicated().sum()
```

---

# Question d'entretien

**Why do you validate data before modeling?**

Réponse :

> Because machine learning models are only as good as the data they learn from. Data validation helps detect missing values, inconsistencies, duplicates and unexpected distributions before they affect model performance.

---

# Étape 4 : Analyse exploratoire (EDA)

On cherche à répondre à des questions simples.

## Quel est le taux de fraude ?

```python
df["fraud"].mean()
```

Supposons :

```text
Fraud rate = 0.4%
```

Déjà une information essentielle.

---

## Distribution des montants

```python
df["amount"].hist()
```

Questions :

* les montants sont-ils asymétriques ?
* existe-t-il des valeurs extrêmes ?

---

## Fraude par pays

```python
df.groupby("country")["fraud"].mean()
```

On peut découvrir :

| Country | Fraud Rate |
| ------- | ---------- |
| US      | 0.3%       |
| UK      | 0.4%       |
| MX      | 1.8%       |

Attention :

Cela ne signifie pas que le pays cause la fraude.

---

# Étape 5 : Comprendre les données temporelles

Question importante.

Les fraudes augmentent-elles :

* la nuit ?
* le week-end ?
* pendant certaines périodes ?

On crée :

```python
df["hour"] = df.timestamp.dt.hour
```

Puis :

```python
df.groupby("hour")["fraud"].mean()
```

---

# Étape 6 : Feature Engineering

C'est ici que le Data Scientist apporte le plus de valeur.

À partir de :

```text
amount
```

on construit :

```text
amount_ratio
```

À partir de :

```text
timestamp
```

on construit :

```text
hour

weekday

weekend
```

À partir de :

```text
device_id
```

on construit :

```text
device_age

device_customer_count

device_transaction_count
```

À partir de :

```text
IP
```

on construit :

```text
shared_ip

vpn

proxy

tor
```

---

# Étape 7 : Séparation des données

Ne jamais entraîner sur toutes les données.

Exemple :

```text
Train

70%
```

```text
Validation

15%
```

```text
Test

15%
```

Dans un contexte de fraude, privilégiez un découpage **temporel** lorsque cela est pertinent.

---

# Question d'entretien

**Why do we need a validation set?**

Réponse :

> The validation set is used to tune hyperparameters and compare models without touching the final test set, ensuring an unbiased estimate of performance.

---

# Étape 8 : Construire une baseline

Toujours commencer simple.

```python
LogisticRegression()
```

Mesurer :

* Precision ;
* Recall ;
* PR-AUC ;
* ROC-AUC.

La baseline servira de référence.

---

# Étape 9 : Construire plusieurs modèles

Par exemple :

```text
Logistic Regression

↓

Random Forest

↓

XGBoost

↓

LightGBM (si disponible)
```

Comparer les performances et les contraintes (latence, interprétabilité, maintenance).

---

# Étape 10 : Hyperparameter Tuning

Pour XGBoost, on peut ajuster :

* max_depth ;
* learning_rate ;
* n_estimators ;
* subsample ;
* colsample_bytree ;
* reg_alpha ;
* reg_lambda.

Utiliser :

```python
GridSearchCV
```

ou

```python
RandomizedSearchCV
```

ou des outils comme **Optuna** pour une recherche plus efficace.

---

# Étape 11 : Évaluation

On calcule :

```python
precision_score()

recall_score()

f1_score()

average_precision_score()
```

La **PR-AUC** est souvent une métrique particulièrement utile lorsque la fraude est très rare.

---

# Étape 12 : Analyse des erreurs

Question essentielle.

Les erreurs sont :

```text
False Positives
```

Pourquoi ?

```text
False Negatives
```

Pourquoi ?

On ne se contente pas d'un score.

On inspecte les cas mal classés.

---

# Étape 13 : Optimisation du seuil

Le modèle retourne :

```text
0.83
```

Le seuil n'est pas forcément :

```text
0.50
```

On choisit le seuil selon :

* coût des faux positifs ;
* coût des faux négatifs ;
* capacité des analystes à traiter les revues manuelles ;
* objectifs métier.

---

# Étape 14 : Explainability

Avec SHAP :

```python
explainer = shap.TreeExplainer(model)
```

Pour une transaction :

```text
Score = 0.92
```

On peut expliquer :

```text
+ new_device

+ unusual_IP

+ amount_ratio

- customer_age
```

Les explications servent à aider les analystes à comprendre les prédictions du modèle.

---

# Étape 15 : Sauvegarder le modèle

```python
import joblib

joblib.dump(model, "fraud_model.pkl")
```

Le modèle peut ensuite être chargé par un service d'inférence.

---

# Étape 16 : Déploiement

Le pipeline devient :

```text
API

↓

Load Model

↓

Receive JSON

↓

Compute Features

↓

Predict

↓

Return JSON
```

---

# Exemple

Entrée :

```json
{
    "amount":900,
    "country":"US",
    "device":"A123"
}
```

Sortie :

```json
{
    "fraud_score":0.91,
    "decision":"review"
}
```

---

# Étape 17 : Monitoring

Après le déploiement, suivre :

Technique :

* latence ;
* erreurs API ;
* disponibilité.

Machine Learning :

* distribution des scores ;
* drift ;
* calibration ;
* performance après obtention des labels.

Business :

* fraude évitée ;
* taux de faux positifs ;
* taux de conversion ;
* coût total.

---

# Étape 18 : Réentraînement

Le modèle est régulièrement mis à jour :

```text
Nouvelles données

↓

Validation

↓

Réentraînement

↓

Déploiement progressif

↓

Surveillance
```

---

# Cas pratique d'entretien

Le recruteur vous dit :

> **Describe how you would build a fraud detection model from scratch.**

Une réponse structurée pourrait être :

> First, I would clearly define the business objective and the target label. Then I would validate and explore the data to understand its quality and characteristics. Next, I would engineer meaningful features, especially behavioral and temporal ones, split the data using an appropriate validation strategy, and build a simple baseline model before testing more advanced models such as XGBoost. I would evaluate the models using metrics suitable for imbalanced data, optimize the decision threshold, analyze errors, explain predictions with SHAP, deploy the selected model behind an API, and continuously monitor performance, drift, and business impact.

Cette réponse couvre **l'ensemble du cycle de vie** d'un modèle de Machine Learning en production.

---

# Les erreurs qui éliminent beaucoup de candidats

Évitez de dire :

* « Je choisis directement le modèle le plus complexe. »
* « L'accuracy est la meilleure métrique. »
* « Je fais un split aléatoire sans réfléchir au temps. »
* « Le modèle prend toutes les décisions seul. »
* « Une fois le modèle déployé, le projet est terminé. »
* « SHAP explique pourquoi un client est fraudeur. »

Préférez :

* partir d'une baseline ;
* choisir des métriques adaptées ;
* éviter le data leakage ;
* expliquer les compromis métier ;
* insister sur le monitoring et l'amélioration continue.

---

# Ce qu'un recruteur SentiLink attend réellement

Au-delà des algorithmes, il veut voir que vous êtes capable de raisonner comme un **Data Scientist en production** :

* vous comprenez le problème métier avant de coder ;
* vous privilégiez la qualité des données et des features ;
* vous savez construire une validation réaliste ;
* vous interprétez vos résultats au lieu de vous limiter à un score ;
* vous pensez au déploiement, à la surveillance et à l'évolution du modèle.

Cette vision « de bout en bout » est ce qui distingue un candidat qui connaît le Machine Learning d'un candidat capable de créer de la valeur dans une entreprise comme SentiLink.
