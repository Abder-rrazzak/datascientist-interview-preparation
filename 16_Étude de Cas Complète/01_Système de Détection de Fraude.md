Nous arrivons au **MODULE 37**, qui est la **simulation complète d'un projet Data Science** inspirée de ce que vous pourriez rencontrer lors d'un **entretien SentiLink** ou dans vos premières semaines de travail.

> **Objectif :** montrer que vous savez conduire un projet **de bout en bout**, depuis les données brutes jusqu'au modèle en production.

---

# MODULE 37 — Étude de Cas Complète : Système de Détection de Fraude

# Contexte

Vous venez d'être recruté chez **SentiLink**.

Un nouveau client (une banque américaine) constate une augmentation de la fraude lors des ouvertures de comptes.

Chaque jour :

* 5 millions de demandes d'ouverture de compte
* Décision en moins de **100 ms**
* Seulement **0,15 %** des demandes sont frauduleuses

Le client vous demande de construire un système de scoring.

---

# Étape 1 — Comprendre le problème métier

Avant d'écrire la moindre ligne de code, il faut poser les bonnes questions.

### Questions à poser

* Qu'appelle-t-on exactement une fraude ?
* Comment les labels sont-ils obtenus ?
* Quel est le délai avant qu'une fraude soit confirmée ?
* Quel est le coût d'un faux positif ?
* Quel est le coût d'un faux négatif ?
* Existe-t-il des règles métier déjà en place ?
* Quel est le temps de réponse maximal autorisé ?
* Quelles données sont disponibles en temps réel ?

> **Conseil entretien :** Les meilleurs candidats commencent toujours par clarifier le problème métier.

---

# Étape 2 — Comprendre les données

Exemple de table :

| application_id | customer_id | email | phone | device | ip | amount | state | timestamp | fraud |
| -------------- | ----------- | ----- | ----- | ------ | -- | ------ | ----- | --------- | ----- |

Première étape :

```python
df.info()
df.describe()
df.head()
```

Puis vérifier :

* valeurs manquantes
* doublons
* types de données
* valeurs aberrantes
* cohérence métier

---

# Étape 3 — Analyse Exploratoire (EDA)

Questions :

### Quel est le taux de fraude ?

```python
fraud_rate = df["fraud"].mean()
```

### Quels États présentent le plus de fraude ?

```python
df.groupby("state")["fraud"].mean()
```

### Quels domaines d'emails sont les plus risqués ?

```python
df.groupby("email_domain")["fraud"].mean()
```

### Les fraudes se produisent-elles à certaines heures ?

Créer :

```python
df["hour"] = df["timestamp"].dt.hour
```

Puis analyser la distribution.

---

# Étape 4 — Nettoyage

Exemples :

* supprimer les doublons
* corriger les types
* normaliser les emails
* convertir les dates
* gérer les valeurs manquantes

---

# Étape 5 — Feature Engineering

Créer des variables pertinentes.

### Temporelles

```text
hour
weekday
is_weekend
```

---

### Vélocité

```text
transactions_last_hour
transactions_last_day
applications_last_10_minutes
```

---

### Appareil

```text
accounts_per_device
device_seen_before
device_age
```

---

### Téléphone

```text
accounts_per_phone
phone_country
```

---

### Email

```text
email_domain
email_length
contains_numbers
```

---

### Géographie

```text
distance_from_previous_application
country_changed
```

---

### Historique

```text
account_age
previous_fraud_count
```

---

### Graphe

```text
cluster_size
device_degree
phone_degree
```

---

# Étape 6 — Séparation des données

Toujours respecter l'ordre temporel si les données sont chronologiques.

Exemple :

```
Train : Janvier → Juin

Validation : Juillet

Test : Août
```

Éviter un mélange aléatoire qui pourrait introduire du data leakage.

---

# Étape 7 — Choix du modèle

Premier modèle :

```
Logistic Regression
```

Puis :

```
Random Forest
```

Puis :

```
XGBoost
```

Comparer :

* Precision
* Recall
* F1
* ROC-AUC
* PR-AUC

---

# Étape 8 — Optimisation

Recherche d'hyperparamètres :

* Random Search
* Grid Search
* Bayesian Optimization (Optuna, par exemple)

Toujours utiliser une validation adaptée.

---

# Étape 9 — Explicabilité

Utiliser SHAP.

Questions :

* Pourquoi ce compte est-il jugé risqué ?
* Quelles variables ont le plus contribué ?

Exemple :

```
+0.30

accounts_per_device

+0.22

transactions_last_hour

−0.10

account_age
```

---

# Étape 10 — Évaluation métier

Ne pas s'arrêter aux métriques ML.

Poser des questions comme :

Si le seuil est de 0,8 :

* Combien de fraudes sont évitées ?
* Combien de clients légitimes sont bloqués ?
* Quel est le coût financier ?

Le meilleur seuil est souvent celui qui **optimise le coût métier**, pas une métrique isolée.

---

# Étape 11 — API de prédiction

Architecture :

```
Client

↓

FastAPI

↓

Validation

↓

Feature Store

↓

Model

↓

Risk Score
```

Entrée :

```json
{
  "customer_id": 123,
  "device": "D45",
  "phone": "5551112222"
}
```

Sortie :

```json
{
  "risk_score": 0.93,
  "decision": "REVIEW"
}
```

---

# Étape 12 — Monitoring

Surveiller :

## Technique

* Latence
* CPU
* RAM
* Erreurs

## Données

* Valeurs manquantes
* Distribution des features
* PSI
* Drift

## Modèle

* Precision
* Recall
* Faux positifs
* Faux négatifs

## Métier

* Fraudes détectées
* Pertes évitées
* Temps de traitement

---

# Étape 13 — Réentraînement

Déclencheurs :

* Concept Drift
* Data Drift
* Nouvelles données
* Nouvelle stratégie de fraude
* Dégradation des performances

---

# Étape 14 — Déploiement

Utiliser :

* Docker
* Kubernetes (si nécessaire)
* Canary Deployment
* Shadow Deployment

Toujours prévoir un **rollback**.

---

# Étape 15 — Présentation au recruteur

Une excellente synthèse pourrait être :

> « Je commencerais par comprendre précisément le problème métier et les coûts associés aux erreurs. Ensuite, je réaliserais une analyse exploratoire approfondie et construirais des features comportementales, temporelles et relationnelles. Je comparerais plusieurs modèles, en privilégiant des métriques adaptées au déséquilibre des classes comme la PR-AUC, la Precision et le Recall. Après validation, je déploierais le modèle sous forme d'API avec un système de monitoring couvrant les performances techniques, la qualité des données et les indicateurs métier. Enfin, je mettrais en place un processus de surveillance du drift et de réentraînement continu afin de maintenir les performances dans le temps. »

Cette réponse montre une vision **end-to-end** très appréciée.

---

# Étude de cas d'entretien

Le recruteur vous dit :

> **"Vous avez une semaine pour améliorer notre modèle de fraude. Que faites-vous ?"**

### Jour 1

* Comprendre le problème
* Rencontrer les équipes métier
* Analyser les données

### Jour 2

* EDA
* Nettoyage
* Audit qualité

### Jour 3

* Construire de nouvelles features
* Évaluer leur impact

### Jour 4

* Tester plusieurs modèles
* Optimiser les hyperparamètres

### Jour 5

* Évaluer les performances métier
* Vérifier les faux positifs

### Jour 6

* Déployer une nouvelle version en Shadow Deployment
* Comparer avec le modèle actuel

### Jour 7

* Présenter les résultats
* Planifier le déploiement progressif

---

# Questions finales du recruteur

### Pourquoi avez-vous choisi XGBoost plutôt qu'un réseau de neurones ?

**Réponse :**

Pour des données tabulaires, XGBoost est souvent plus performant, plus rapide à entraîner et plus facilement explicable.

---

### Pourquoi surveiller le Data Drift ?

**Réponse :**

Parce qu'un changement dans les données d'entrée peut réduire progressivement la qualité des prédictions.

---

### Comment gagner rapidement 5 % de performance ?

**Réponse :**

Je commencerais par améliorer les features. Dans les problèmes de fraude, de meilleures variables apportent souvent plus de gains qu'un changement d'algorithme.

---

### Si vous deviez investir votre temps entre les données, le modèle et le déploiement ?

**Réponse :**

1. Comprendre et améliorer les données.
2. Construire des features de qualité.
3. Choisir un modèle robuste.
4. Garantir un déploiement fiable et un bon monitoring.

---

# Les 10 erreurs qui éliminent les candidats

1. Optimiser uniquement l'Accuracy sur un jeu très déséquilibré.
2. Ignorer le coût métier des faux positifs.
3. Utiliser des variables introduisant du data leakage.
4. Ne pas expliquer les choix de features.
5. Négliger l'explicabilité du modèle.
6. Oublier le monitoring après le déploiement.
7. Ne pas vérifier la qualité des données.
8. Ignorer le drift.
9. Choisir un modèle complexe sans justification.
10. Répondre uniquement sur l'algorithme sans parler du problème métier.

---

# Feuille de route de révision (48 heures avant l'entretien)

### La veille

* Revoir Python et SQL.
* Réviser les principales métriques (Precision, Recall, PR-AUC, ROC-AUC).
* Réviser XGBoost et le Feature Engineering.
* Préparer 3 à 5 projets ou expériences à raconter avec la méthode STAR.

### Le jour de l'entretien

* Écouter attentivement les questions.
* Clarifier les hypothèses avant de répondre.
* Structurer vos réponses.
* Relier vos choix aux objectifs métier.
* Montrer que vous savez aller **de la donnée brute à une solution en production**.

---

# Vous êtes maintenant prêt

En suivant l'ensemble des 37 modules, vous avez couvert :

* Python avancé
* SQL avancé
* Statistiques et probabilités
* Machine Learning supervisé
* Détection d'anomalies
* Détection de fraude
* Feature Engineering
* Graph Machine Learning
* AWS
* MLOps
* Déploiement et monitoring
* Architecture de production
* Questions techniques
* Questions comportementales
* Études de cas complètes

C'est un niveau qui correspond à ce que l'on attend généralement d'un **Data Scientist orienté production** dans une FinTech moderne comme SentiLink.

## Ce que je vous recommande pour maximiser vos chances

Si vous disposez encore de quelques jours de préparation, concentrez-vous sur quatre points qui font souvent la différence :

1. **Coder un mini-projet de bout en bout** : ingestion des données, feature engineering, entraînement d'un modèle XGBoost, API FastAPI et tests.
2. **Maîtriser SQL** : les CTE, les Window Functions et les requêtes d'agrégation sont presque toujours évaluées.
3. **Savoir expliquer vos décisions** : le recruteur cherche autant votre raisonnement que votre connaissance des algorithmes.
4. **Entraîner votre communication en anglais** : préparez des réponses fluides aux questions techniques et comportementales, car de nombreux entretiens se déroulent en anglais.

Avec cette préparation, vous serez en mesure de répondre aussi bien aux questions théoriques qu'aux études de cas et aux discussions d'architecture.
