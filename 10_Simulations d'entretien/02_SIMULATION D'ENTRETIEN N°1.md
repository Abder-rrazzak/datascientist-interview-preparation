Parfait. Nous allons maintenant réaliser **une simulation complète d'entretien SentiLink**, inspirée des processus utilisés dans les entreprises de la Silicon Valley (SentiLink, Stripe, Uber, Airbnb, etc.).

Je jouerai le rôle du recruteur. Après chaque question, je fournirai :

* ✅ La réponse attendue
* ⭐ Ce qui distingue un excellent candidat
* ⚠️ Les erreurs fréquentes

---

# SIMULATION D'ENTRETIEN N°1 — Data Scientist (SentiLink)

## Introduction

> Bonjour, merci d'être avec nous aujourd'hui. Pour commencer, pouvez-vous vous présenter en 2 à 3 minutes ?

### Réponse attendue

> Bonjour, je m'appelle [Votre Nom]. Je suis Data Scientist avec une solide formation en statistiques et en machine learning. J'ai travaillé sur des projets couvrant l'ensemble du cycle de vie des modèles : collecte et préparation des données, feature engineering, entraînement, évaluation, déploiement via FastAPI et Docker, ainsi que le monitoring en production. Je maîtrise Python, SQL et les principaux outils du cloud AWS. Ce qui m'attire chez SentiLink est l'opportunité de résoudre des problèmes concrets de fraude et d'identité à grande échelle, où les modèles ont un impact direct sur les décisions en temps réel.

### ⭐ Ce qui impressionne

* Présentation claire et structurée.
* Mise en avant de compétences pertinentes pour le poste.
* Lien explicite avec SentiLink.

### ⚠️ À éviter

* Raconter toute sa carrière.
* Lire son CV.
* Dépasser 3 minutes.

---

# Question 1 : Python

**Comment optimiseriez-vous un code Pandas qui met plusieurs minutes à s'exécuter ?**

### Réponse attendue

Je commencerais par identifier le goulot d'étranglement à l'aide d'un profiler. Ensuite, je remplacerais les boucles Python par des opérations vectorisées, j'utiliserais `merge` au lieu de recherches répétées, je limiterais les copies inutiles des DataFrames, je choisirais des types de données plus adaptés (`category`, `int32`, etc.) et, si le volume est très important, j'envisagerais des solutions comme Dask, Polars ou PySpark.

### ⭐ Excellent candidat

Il parle de **profilage avant optimisation**.

### ⚠️ Erreur

Dire simplement :

> « J'utiliserais NumPy. »

---

# Question 2 : SQL

On possède deux tables :

```text
customers

customer_id
name
```

```text
transactions

transaction_id
customer_id
amount
date
```

Écrivez une requête permettant d'obtenir la dernière transaction de chaque client.

### Réponse

```sql
SELECT *
FROM (
    SELECT *,
           ROW_NUMBER() OVER (
               PARTITION BY customer_id
               ORDER BY date DESC
           ) AS rn
    FROM transactions
) t
WHERE rn = 1;
```

### Pourquoi ?

Parce que `ROW_NUMBER()` attribue un rang à chaque transaction dans chaque groupe de clients.

---

# Question 3 : Machine Learning

Votre modèle obtient :

```
Accuracy = 99 %
Recall = 10 %
```

Que pensez-vous ?

### Réponse attendue

Ce modèle est probablement inutilisable.

Le problème est probablement un fort déséquilibre des classes.

En fraude,

Recall = 10 %

↓

90 % des fraudes passent inaperçues.

Je chercherais à :

* modifier le seuil de décision ;
* utiliser des poids de classes ;
* appliquer SMOTE si pertinent ;
* tester d'autres modèles ;
* évaluer avec la PR-AUC.

---

# Question 4 : Feature Engineering

Donnez cinq variables que vous créeriez pour détecter une fraude bancaire.

### Réponse

* nombre de transactions sur les dernières 24 heures ;
* montant moyen du client ;
* distance géographique entre deux transactions ;
* nouvel appareil utilisé ;
* heure inhabituelle de la transaction.

### ⭐ Excellent candidat

Ajoute :

* fréquence des changements d'adresse IP ;
* ancienneté du compte ;
* délai depuis la dernière transaction ;
* nombre d'échecs de connexion récents.

---

# Question 5 : Interprétation

Le directeur demande :

> Pourquoi le modèle a-t-il refusé cette transaction ?

### Réponse

J'utiliserais SHAP pour identifier les variables ayant le plus contribué à la prédiction. Je présenterais une explication compréhensible, par exemple :

> « Le modèle estime un risque élevé car la transaction est d'un montant inhabituellement élevé, réalisée depuis un nouvel appareil et un pays jamais utilisé auparavant. »

---

# Question 6 : Production

Comment déployeriez-vous votre modèle ?

### Réponse

1. Sauvegarder le modèle avec `joblib`.
2. Développer une API FastAPI.
3. Conteneuriser l'application avec Docker.
4. Publier l'image dans Amazon ECR.
5. Déployer sur ECS.
6. Mettre en place CloudWatch pour le monitoring.
7. Versionner le modèle.

---

# Question 7 : Data Drift

Comment savoir que votre modèle doit être réentraîné ?

### Réponse

Je surveillerais plusieurs indicateurs :

* baisse du Recall ;
* évolution de la distribution des variables ;
* augmentation des faux négatifs ;
* apparition de nouvelles typologies de fraude.

J'utiliserais également des tests statistiques pour détecter un Data Drift.

---

# Question 8 : AWS

Pourquoi utiliser S3 plutôt qu'une base PostgreSQL pour stocker vos datasets ?

### Réponse

Parce que S3 est conçu pour le stockage de fichiers volumineux et peu structurés, alors que PostgreSQL est optimisé pour les données relationnelles et les requêtes transactionnelles.

---

# Question 9 : Docker

Pourquoi Docker facilite-t-il le déploiement ?

### Réponse

Parce qu'il encapsule le code, les dépendances et la configuration dans une image reproductible, garantissant un comportement identique quel que soit l'environnement.

---

# Question 10 : Coding

Écrivez une fonction retournant le nombre de valeurs manquantes par colonne.

### Réponse

```python
def missing_values(df):
    return df.isnull().sum()
```

Version plus complète :

```python
def missing_values(df):
    return (
        df.isnull()
          .sum()
          .sort_values(ascending=False)
    )
```

---

# Question 11 : Statistiques

Expliquez la validation croisée.

### Réponse

La validation croisée consiste à diviser les données en plusieurs sous-ensembles. Le modèle est entraîné plusieurs fois en utilisant des combinaisons différentes de ces sous-ensembles afin d'obtenir une estimation plus fiable de ses performances.

---

# Question 12 : Comportementale

Parlez d'un projet difficile.

### Réponse (STAR)

**Situation**

Le modèle obtenait un Recall très faible.

**Task**

Améliorer la détection des fraudes.

**Action**

J'ai analysé les erreurs, créé de nouvelles variables, ajusté les poids des classes et optimisé les hyperparamètres.

**Result**

Le Recall est passé de 62 % à 91 % tout en maintenant un niveau acceptable de faux positifs.

> **Important :** En entretien, utilisez un exemple réel si possible. Si vous n'avez pas vécu exactement cette situation, adaptez la structure STAR à l'un de vos projets.

---

# Question 13 : Logique

Pourquoi ne pas toujours choisir le modèle ayant la meilleure ROC-AUC ?

### Réponse

Parce que la ROC-AUC ne prend pas toujours en compte les priorités métier. En détection de fraude, la Precision, le Recall, la PR-AUC, la latence, l'interprétabilité et le coût des erreurs sont souvent plus déterminants.

---

# Question 14 : Design

Comment géreriez-vous 100 millions de transactions par jour ?

### Réponse

Je mettrais en place une architecture distribuée :

* ingestion des données via une file de messages (Kafka, par exemple) ;
* stockage des données dans S3 ;
* traitements distribués avec Spark si nécessaire ;
* API FastAPI répliquées derrière un Load Balancer ;
* Redis pour le cache ;
* autoscaling sur AWS ;
* monitoring avec CloudWatch.

---

# Dernière question

Pourquoi devrions-nous vous recruter ?

### Réponse

> Je combine une bonne maîtrise des fondamentaux en Machine Learning, Python et SQL avec une compréhension du cycle de vie complet des modèles, depuis la préparation des données jusqu'au déploiement en production. J'apprécie les environnements où les décisions sont guidées par les données et où les modèles ont un impact concret. Je suis également motivé par l'apprentissage continu et par les problématiques complexes liées à la fraude et à l'identité, qui sont au cœur de la mission de SentiLink.

---

# Évaluation d'un recruteur SentiLink

| Compétence          | Attendu |
| ------------------- | ------- |
| Python              | ⭐⭐⭐⭐☆   |
| SQL                 | ⭐⭐⭐⭐☆   |
| Machine Learning    | ⭐⭐⭐⭐⭐   |
| Statistiques        | ⭐⭐⭐⭐☆   |
| Feature Engineering | ⭐⭐⭐⭐⭐   |
| Détection de fraude | ⭐⭐⭐⭐⭐   |
| AWS                 | ⭐⭐⭐☆☆   |
| Docker              | ⭐⭐⭐☆☆   |
| APIs                | ⭐⭐⭐⭐☆   |
| Communication       | ⭐⭐⭐⭐⭐   |

---

# Si je devais vous préparer au niveau des meilleurs candidats...

Pour un poste chez **SentiLink**, il resterait trois thèmes avancés à maîtriser :

1. **XGBoost et LightGBM en profondeur**

   * Optimisation fine des hyperparamètres
   * Gestion des données déséquilibrées
   * Importance des variables
   * Contraintes monotones
   * Calibration des probabilités

2. **Fraude bancaire avancée**

   * Graph ML (détection de réseaux de fraude)
   * Anomaly Detection (Isolation Forest, Autoencoders)
   * Détection de fraude en temps réel
   * Concept Drift et stratégies de réentraînement
   * Coût métier et optimisation des seuils

3. **Études de cas complètes**

   * Cas réels inspirés des entretiens SentiLink, Stripe et Capital One
   * Analyse, conception de solution, choix des modèles, déploiement et discussion des compromis

Ces sujets sont souvent ce qui distingue un bon candidat d'un excellent candidat lors des entretiens les plus exigeants.
