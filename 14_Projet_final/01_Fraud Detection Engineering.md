Nous allons maintenant aborder **le module le plus proche du travail quotidien chez SentiLink**.

Jusqu'à présent, nous avons appris les algorithmes. Maintenant, nous allons apprendre **comment un Data Scientist conçoit réellement un système de détection de fraude en production**.

---

# MODULE 20 — Fraud Detection Engineering (Niveau SentiLink)

## Objectifs

À la fin de ce module, vous serez capable de :

* Comprendre le cycle de vie complet d'un système de détection de fraude.
* Concevoir des features de haute qualité.
* Éviter les erreurs classiques (data leakage, mauvaise validation, mauvais labels).
* Construire un pipeline de scoring temps réel.
* Expliquer vos choix techniques lors d'un entretien.

---

# 1. Comment fonctionne un système de fraude ?

Lorsqu'un utilisateur effectue une transaction :

```text
Utilisateur
      │
      ▼
API
      │
      ▼
Validation des données
      │
      ▼
Feature Engineering
      │
      ▼
Machine Learning Model
      │
      ▼
Fraud Score (0 → 1)
      │
      ▼
Business Rules
      │
      ▼
Decision
```

Le modèle ne décide pas directement.

Il fournit un **score de risque**.

---

# 2. Exemple réel

Une demande de prêt arrive.

Informations reçues :

```text
Nom
Adresse
Date de naissance
Téléphone
Email
Montant demandé
Adresse IP
Device ID
Navigateur
Heure
```

Le modèle ne travaille pas directement sur ces champs.

Il construit des **features**.

---

# 3. Qu'est-ce qu'une feature ?

Une feature est une variable calculée à partir des données brutes.

Par exemple :

Données :

```text
Amount = 800 $
```

Feature :

```text
amount_above_customer_average
```

ou

```text
amount_ratio
```

Les features sont souvent plus informatives que les données brutes.

---

# 4. Types de features

On distingue généralement plusieurs familles :

### Features comportementales

```text
transactions_last_hour
transactions_last_day
average_amount
max_amount
```

---

### Features temporelles

```text
hour_of_day
day_of_week
weekend
time_since_previous_transaction
```

---

### Features géographiques

```text
country
city
distance_from_last_location
country_changed
```

---

### Features réseau

```text
shared_ip_count
shared_device_count
shared_email_count
```

---

### Features historiques

```text
customer_age_days
account_age
historical_fraud_rate
historical_chargebacks
```

---

# 5. Exemple

Supposons :

```text
Customer

Average amount = 40$

Current transaction = 900$
```

Créer uniquement :

```text
amount = 900
```

n'est pas très informatif.

Créer :

```text
amount_ratio = 22.5
```

est beaucoup plus puissant.

---

# 6. Pourquoi ?

Parce que :

Pour un client qui dépense habituellement :

```text
900$
```

une transaction de 900$ est normale.

Pour un client qui dépense :

```text
20$
```

elle est très suspecte.

Le contexte est essentiel.

---

# 7. Velocity Features

Les fraudeurs agissent souvent rapidement.

Exemples :

```text
transactions_last_5_minutes

transactions_last_30_minutes

transactions_last_hour

transactions_last_day
```

---

# 8. Exemple

Un client réalise :

```text
10 transactions

en 2 minutes
```

Feature :

```text
transactions_last_2_minutes = 10
```

Très bon indicateur.

---

# 9. Device Features

Le Device ID est extrêmement important.

Exemples :

```text
is_new_device

device_age

device_customer_count

device_transaction_count

device_fraud_rate
```

---

# 10. Pourquoi ?

Supposons :

```text
Device X
```

utilisé par :

```text
150 clients
```

C'est anormal.

Feature :

```text
unique_customers_per_device = 150
```

Très forte valeur prédictive.

---

# 11. IP Features

Même logique.

Exemples :

```text
unique_accounts_per_ip

fraud_rate_per_ip

new_ip

vpn_detected

proxy_detected

tor_detected
```

---

# 12. Email Features

Exemples :

```text
email_domain

gmail

hotmail

temporary_email

email_length

contains_digits

domain_age
```

Les adresses e-mail jetables peuvent être un signal de risque.

---

# 13. Téléphone

Features possibles :

```text
phone_country

phone_age

carrier

voip

number_of_accounts
```

---

# 14. Address Features

```text
shipping_matches_billing

distance_shipping_billing

address_reused
```

---

# 15. Identity Graph

C'est l'un des grands atouts de SentiLink.

Imaginez le graphe suivant :

```text
Customer A
      │
      ▼
Device X
      ▲
      │
Customer B
      ▲
      │
Customer C
```

Le même appareil est partagé.

C'est un signal important.

---

# 16. Autre exemple

```text
Customer A

Email 1

Phone 1

Device X
```

Puis :

```text
Customer B

Email 2

Phone 2

Device X
```

Le Device ID crée un lien entre deux identités.

---

# 17. Feature Graph

On peut construire :

```text
device_degree

email_degree

phone_degree

ip_degree
```

Ces variables décrivent la connectivité dans le graphe.

---

# 18. Question d'entretien

> **Why is graph information useful in fraud detection?**

Bonne réponse :

> Fraudsters often reuse devices, phone numbers, email addresses or IPs across multiple identities. Graph-based features help capture these hidden relationships and identify suspicious clusters that would not be visible from a single transaction alone.

---

# 19. Data Quality

Un excellent modèle sur de mauvaises données reste un mauvais système.

Toujours vérifier :

```text
Missing values

Duplicates

Invalid timestamps

Impossible ages

Negative amounts

Outliers

Corrupted records
```

---

# 20. Gestion des valeurs manquantes

Supposons :

```text
Income

NULL
```

Que faire ?

Plusieurs options :

* supprimer ;
* imputer ;
* créer une catégorie "Unknown" ;
* ajouter une feature :

```text
income_missing = 1
```

Le choix dépend du contexte métier.

---

# 21. Feature Scaling

Question fréquente.

Avec :

```text
Logistic Regression
```

le scaling est généralement utile.

Avec :

```text
XGBoost
```

il est souvent beaucoup moins nécessaire, car les arbres ne dépendent pas des distances entre variables comme les modèles linéaires ou certains algorithmes basés sur la distance.

---

# 22. Encodage des variables catégorielles

Exemple :

```text
Country

France

USA

Canada
```

On peut utiliser :

```text
One-Hot Encoding
```

ou d'autres techniques selon le modèle et la cardinalité.

---

# 23. Variables à forte cardinalité

Exemple :

```text
ZIP Code

Email

Customer ID
```

Le One-Hot peut devenir inefficace.

On peut envisager :

* Target Encoding (avec précautions contre le leakage) ;
* Frequency Encoding ;
* Hashing ;
* CatBoost Encoding (selon le modèle utilisé).

---

# 24. Data Leakage dans les features

Supposons :

Feature :

```text
chargeback_received
```

Le chargeback arrive :

```text
3 semaines

après la transaction.
```

Cette feature est excellente...

mais impossible à utiliser au moment du scoring.

Donc :

❌ leakage.

---

# 25. Point-in-Time Correctness

Question très probable.

Chaque feature doit répondre à :

> **Was this information available at prediction time?**

Si :

NON

↓

La feature est invalide.

---

# 26. Label Definition

Question très importante.

Qu'est-ce qu'une fraude ?

Ce n'est pas toujours évident.

Exemple :

```text
chargeback

identity theft

manual investigation

confirmed fraud
```

Le label dépend de la définition métier.

---

# 27. Pourquoi les labels sont-ils critiques ?

Un modèle apprend uniquement :

```text
Features

↓

Labels
```

Si les labels sont incorrects :

↓

Le modèle apprendra des erreurs.

---

# 28. Label Delay

Dans la fraude :

La fraude est souvent confirmée plusieurs jours ou semaines plus tard.

Exemple :

```text
Transaction

↓

Chargeback

↓

30 jours
```

Il faut donc tenir compte du délai lors de la constitution des jeux d'entraînement.

---

# 29. Concept Drift

Le fraudeur évolue.

Les patterns changent.

Exemple :

```text
2024

↓

Fraude A
```

Puis :

```text
2025

↓

Nouvelle technique
```

Le modèle peut devenir moins performant.

---

# 30. Types de drift

### Data Drift

Les variables changent.

Exemple :

```text
Average amount
```

augmente progressivement.

---

### Concept Drift

La relation entre les variables et la fraude change.

C'est plus difficile à détecter.

---

# 31. Monitoring

Après le déploiement, il faut surveiller :

```text
Precision

Recall

PR-AUC

Fraud Rate

Latency

Prediction Distribution

Feature Distribution

Data Drift
```

Le travail du Data Scientist ne s'arrête pas après la mise en production.

---

# 32. Exemple de Monitoring

Hier :

```text
Fraud Score Mean

0.12
```

Aujourd'hui :

```text
0.38
```

Deux possibilités :

* les fraudeurs ont changé de comportement ;
* un problème est apparu dans les données.

Il faut enquêter.

---

# 33. Shadow Deployment

Question fréquente.

On déploie le nouveau modèle :

```text
Production

↓

Predictions

↓

Non utilisées
```

On compare :

Ancien modèle

vs

Nouveau modèle

sans impacter les clients.

---

# 34. Canary Deployment

On envoie :

```text
5 %

des transactions
```

au nouveau modèle.

Si tout fonctionne :

```text
10 %

↓

25 %

↓

50 %

↓

100 %
```

---

# 35. Pourquoi ?

Pour limiter le risque.

Si le nouveau modèle est défectueux :

↓

Seuls quelques utilisateurs sont impactés.

---

# 36. Feedback Loop

Après chaque décision :

```text
Prediction

↓

Manual Review

↓

Chargeback

↓

Confirmed Fraud

↓

Retraining Dataset
```

Le système s'améliore progressivement.

---

# 37. Active Learning

Les analystes ne peuvent pas examiner toutes les transactions.

On sélectionne celles qui apporteront le plus d'information au modèle, par exemple les cas les plus incertains.

---

# 38. Human in the Loop

Le modèle ne remplace pas les analystes.

Il les aide à prioriser.

Par exemple :

```text
Fraud Score = 0.48

↓

Manual Review
```

---

# 39. Architecture SentiLink (simplifiée)

```text
Partner API
      │
      ▼
Feature Store
      │
      ▼
ML Model
      │
      ▼
Risk Score
      │
      ▼
Decision Engine
      │
      ▼
Partner
```

Le moteur de décision peut combiner :

* score ML ;
* règles métier ;
* politiques spécifiques au client.

---

# 40. Question d'entretien

> **If you could improve only one thing in a fraud detection system, what would you choose?**

Une réponse mature :

> **I would first focus on improving data quality and feature engineering. In fraud detection, better features and reliable labels often produce larger gains than simply switching to a more sophisticated algorithm.**

Cette réponse est parfaitement alignée avec la philosophie de SentiLink.

---

# 41. Étude de cas complète

Imaginons :

### Données disponibles

```text
Transactions

Customers

Devices

IP

Emails
```

### Étape 1

Construire les features :

```text
transactions_last_hour

device_age

new_device

amount_ratio

shared_device

shared_ip

country_change

time_since_last_transaction
```

### Étape 2

Validation temporelle.

### Étape 3

Entraîner un modèle XGBoost.

### Étape 4

Évaluer avec :

* PR-AUC ;
* Precision ;
* Recall ;
* coût métier.

### Étape 5

Choisir un seuil optimal.

### Étape 6

Déployer progressivement (Shadow → Canary → Production).

### Étape 7

Surveiller :

* dérive des données ;
* performance ;
* latence ;
* stabilité des features.

---

# Ce qu'un recruteur SentiLink attend de vous

À la fin de ce module, vous devez être capable de montrer que vous ne savez pas seulement **entraîner un modèle**, mais que vous comprenez **tout le cycle de vie d'un système de détection de fraude** :

* acquisition et qualité des données ;
* feature engineering orienté métier ;
* validation temporelle ;
* gestion du déséquilibre des classes ;
* prévention du data leakage ;
* interprétabilité des prédictions ;
* déploiement progressif ;
* surveillance en production ;
* amélioration continue grâce aux retours des analystes.

---

## 📌 Prochaine étape : MODULE 21 — AWS, architecture de déploiement et APIs

Nous étudierons en détail :

* l'architecture AWS utilisée par des entreprises comme SentiLink (EC2, S3, RDS, Redshift, Lambda, IAM, CloudWatch) ;
* la construction d'un pipeline de Machine Learning en production ;
* le déploiement d'un modèle sous forme d'API REST ;
* le monitoring, la journalisation et la haute disponibilité ;
* les questions d'entretien AWS les plus fréquentes avec leurs réponses détaillées.
