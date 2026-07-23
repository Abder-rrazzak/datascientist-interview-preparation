Nous commençons maintenant le **MODULE 6**, qui est le plus important de toute la préparation.

Jusqu'à présent, nous avons étudié chaque composant séparément :

* Python
* SQL
* Machine Learning
* Détection de fraude
* AWS
* Docker
* FastAPI
* CI/CD

Maintenant, nous allons les assembler dans **un projet complet**, exactement comme vous le feriez chez une entreprise comme **SentiLink**.

---

# MODULE 6 – Projet complet : Construire un système de détection de fraude de A à Z

## Le scénario

Vous êtes Data Scientist chez SentiLink.

Une banque vous confie un fichier contenant **10 millions de transactions**.

Votre objectif est de développer un système capable de répondre à cette question :

> **Cette transaction est-elle frauduleuse ?**

---

# Étape 1 : Comprendre le problème métier

Avant d'écrire du code, il faut comprendre le besoin.

Questions à poser :

* Quel est le coût d'une fraude ?
* Quel est le coût d'un faux positif ?
* Combien de temps avons-nous pour répondre ?
* Quand une fraude est-elle confirmée ?
* Existe-t-il déjà des règles métier ?

---

## Pourquoi ces questions ?

Imaginez :

Une fraude coûte :

```text
50 $
```

Un faux positif coûte :

```text
2 $
```

Dans ce cas,

on acceptera davantage de faux positifs afin d'éviter les fraudes.

Le métier influence donc directement le modèle.

---

# Étape 2 : Chargement des données

```python
import pandas as pd

df = pd.read_csv("transactions.csv")
```

Première inspection

```python
df.head()

df.info()

df.describe()
```

Questions :

* Nombre de lignes ?
* Variables numériques ?
* Variables catégorielles ?
* Valeurs manquantes ?

---

# Étape 3 : Analyse Exploratoire (EDA)

Regarder la cible.

```python
df["is_fraud"].value_counts(normalize=True)
```

Résultat

| Classe | Pourcentage |
| ------ | ----------: |
| 0      |      99.7 % |
| 1      |       0.3 % |

Première conclusion :

Très fort déséquilibre.

---

# Étape 4 : Nettoyage

Supprimer :

* doublons
* valeurs impossibles
* colonnes inutiles

Traiter :

* valeurs manquantes
* formats de date
* catégories incohérentes

---

Exemple

```python
df.drop_duplicates(inplace=True)
```

---

# Étape 5 : Feature Engineering

C'est ici que le Data Scientist apporte le plus de valeur.

Créer :

```text
Heure de transaction
```

```python
df["hour"] = df["timestamp"].dt.hour
```

---

Créer

```text
Week-end ?
```

```python
df["weekend"] = df["timestamp"].dt.dayofweek >= 5
```

---

Créer

```text
Montant moyen du client
```

```python
df.groupby("customer_id")["amount"].transform("mean")
```

---

Créer

```text
Nombre de transactions 24h
```

---

Créer

```text
Premier appareil ?
```

---

Créer

```text
Distance entre deux transactions
```

Toutes ces variables sont beaucoup plus informatives que les données brutes.

---

# Étape 6 : Encodage

Variables catégorielles

↓

OneHotEncoder

ou

CatBoost Encoder

selon le modèle choisi.

---

# Étape 7 : Séparation Train/Test

```python
from sklearn.model_selection import train_test_split
```

Toujours faire le découpage **avant** tout suréchantillonnage (SMOTE) pour éviter les fuites de données.

---

# Étape 8 : Baseline

Commencer simple.

```python
LogisticRegression()
```

Pourquoi ?

Parce qu'il faut toujours avoir un point de comparaison.

---

# Étape 9 : Modèle avancé

Tester ensuite :

```text
Random Forest

↓

XGBoost

↓

LightGBM
```

Comparer leurs performances sur le même jeu de test.

---

# Étape 10 : Validation croisée

```python
cross_val_score()
```

Pourquoi ?

Pour obtenir une estimation plus robuste des performances et limiter le risque de surapprentissage.

---

# Étape 11 : Optimisation

Avec :

```text
Random Search
```

ou

```text
Optuna
```

Optimiser :

* profondeur (`max_depth`) ;
* nombre d'arbres (`n_estimators`) ;
* taux d'apprentissage (`learning_rate`) ;
* etc.

---

# Étape 12 : Évaluation

Calculer :

```text
Recall

Precision

F1

ROC-AUC

PR-AUC
```

Puis analyser la matrice de confusion.

---

Exemple

|        | Prédit 0 | Prédit 1 |
| ------ | -------: | -------: |
| Réel 0 |     9800 |      120 |
| Réel 1 |       20 |       60 |

---

Interprétation :

20 fraudes n'ont pas été détectées.

120 clients légitimes ont été bloqués.

C'est cette analyse qui permet de discuter avec le métier.

---

# Étape 13 : Interprétation

Utiliser SHAP.

Exemple

```text
Montant

+

Nouvel appareil

+

Adresse IP inhabituelle

↓

Score élevé
```

Cela permet d'expliquer pourquoi une transaction est jugée suspecte.

---

# Étape 14 : Sauvegarder le modèle

```python
import joblib

joblib.dump(model, "fraud_model.pkl")
```

---

# Étape 15 : Construire l'API

FastAPI

↓

Charge

```text
fraud_model.pkl
```

↓

Reçoit

JSON

↓

Retourne

Score

---

# Étape 16 : Docker

Construire

```text
Docker Image
```

Puis lancer le conteneur.

---

# Étape 17 : Déploiement AWS

Pipeline

```text
GitHub

↓

GitHub Actions

↓

Docker

↓

Amazon ECR

↓

Amazon ECS

↓

FastAPI

↓

Client
```

---

# Étape 18 : Monitoring

Surveiller :

* Recall ;
* Precision ;
* Latence ;
* Drift ;
* Disponibilité de l'API.

---

# Étape 19 : Réentraînement

Si :

Recall ↓

ou

Data Drift ↑

↓

Réentraîner le modèle.

---

# Le pipeline complet

```text
Collecte des données
          │
          ▼
Exploration (EDA)
          │
          ▼
Nettoyage
          │
          ▼
Feature Engineering
          │
          ▼
Train / Validation / Test
          │
          ▼
Baseline (Logistic Regression)
          │
          ▼
XGBoost / LightGBM
          │
          ▼
Évaluation
          │
          ▼
Optimisation
          │
          ▼
SHAP
          │
          ▼
Sauvegarde
          │
          ▼
FastAPI
          │
          ▼
Docker
          │
          ▼
AWS
          │
          ▼
Monitoring
          │
          ▼
Réentraînement
```

---

# Cas d'entretien SentiLink

Le recruteur vous demande :

> **"Décrivez votre approche pour construire un système de détection de fraude."**

### Réponse attendue (2 à 3 minutes)

> Je commence par comprendre le problème métier et les coûts associés aux erreurs. Ensuite, j'explore et nettoie les données, puis je réalise un feature engineering adapté à la fraude. Je construis une baseline avec une régression logistique avant de comparer des modèles comme XGBoost ou LightGBM. J'évalue les performances avec des métriques adaptées aux classes déséquilibrées, notamment le Recall, la Precision et la PR-AUC. J'interprète les prédictions avec SHAP, puis je déploie le modèle derrière une API FastAPI conteneurisée avec Docker. Enfin, je mets en place un monitoring pour suivre les performances, détecter le drift et déclencher un réentraînement lorsque cela devient nécessaire.

---

# Questions fréquentes

### Pourquoi commencer par une baseline ?

Pour disposer d'une référence simple et interprétable afin de mesurer l'apport des modèles plus complexes.

---

### Pourquoi SHAP est-il important ?

Parce qu'il aide à expliquer les décisions du modèle, ce qui est essentiel dans les domaines réglementés comme la finance.

---

### Pourquoi Docker ?

Pour garantir que le modèle fonctionne de manière identique en développement, en test et en production.

---

### Pourquoi FastAPI ?

Pour exposer le modèle via une API rapide, documentée et facilement intégrable.

---

### Pourquoi monitorer un modèle ?

Parce que les données et les comportements évoluent, ce qui peut dégrader progressivement les performances.

---

# Ce qu'un recruteur SentiLink attend réellement

Au-delà des outils, il cherche à vérifier que vous êtes capable de :

* raisonner de manière structurée ;
* comprendre les contraintes métier ;
* choisir des métriques adaptées ;
* expliquer vos décisions techniques ;
* concevoir un système robuste de bout en bout.

---

# Prochaine étape : Simulations d'entretien

Nous passerons à des **entretiens blancs** inspirés des processus des grandes entreprises (SentiLink, Stripe, Amazon, Uber).

Nous couvrirons :

1. Questions Python.
2. Questions SQL.
3. Questions Machine Learning.
4. Questions sur la fraude.
5. Questions AWS et architecture.
6. Études de cas.
7. Questions comportementales.
8. Questions de réflexion ("Pourquoi ?", "Et si... ?").

L'objectif sera de vous entraîner à répondre avec le niveau de précision et de clarté attendu lors d'un véritable entretien technique.
