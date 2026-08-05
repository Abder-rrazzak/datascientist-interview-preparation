Nous arrivons maintenant au **MODULE 32**, qui est probablement **le module le plus important pour réussir un entretien chez SentiLink**.

> **Pourquoi ?**
>
> Parce que chez SentiLink, le Machine Learning n'est pas ce qui fait gagner.
>
> **Le Feature Engineering est la véritable compétence différenciante.**

En réalité, dans la plupart des compétitions Kaggle et des systèmes industriels de détection de fraude :

* **20 %** des gains proviennent du choix du modèle.
* **80 %** proviennent de la qualité des données et des features.

---

# MODULE 32 — Feature Engineering Avancé pour la Détection de Fraude

# Objectifs

À la fin de ce module, vous serez capable de :

* concevoir des variables discriminantes pour la fraude ;
* expliquer leur intérêt métier ;
* éviter les erreurs classiques (data leakage, redondance, variables instables) ;
* répondre aux questions d'entretien sur le feature engineering.

---

# PARTIE 1 — Qu'est-ce qu'une Feature ?

Une **feature** est une variable utilisée par le modèle pour faire une prédiction.

Exemple :

```text
Age = 35
```

est une feature.

Mais :

```text
Nombre de transactions réalisées dans les 24 dernières heures
```

est souvent une **bien meilleure feature**, car elle apporte davantage d'information sur le comportement.

---

# PARTIE 2 — Les catégories de features

En détection de fraude, on distingue généralement :

| Type             | Exemple                          |
| ---------------- | -------------------------------- |
| Démographiques   | âge, pays                        |
| Temporelles      | heure, jour                      |
| Comportementales | fréquence des transactions       |
| Financières      | montant, moyenne, variance       |
| Réseau           | adresse IP, appareil             |
| Géographiques    | distance entre deux transactions |
| Relationnelles   | appareils ou emails partagés     |
| Historiques      | ancienneté du compte             |

Un recruteur appréciera une réponse structurée par catégories.

---

# PARTIE 3 — Features temporelles

À partir d'une date :

```text
2026-08-05 22:43:10
```

on peut extraire :

* heure ;
* jour de la semaine ;
* week-end ou non ;
* mois ;
* trimestre ;
* jour férié (selon le pays).

Exemple Python :

```python
df["hour"] = df["timestamp"].dt.hour
df["weekday"] = df["timestamp"].dt.dayofweek
df["is_weekend"] = df["weekday"] >= 5
```

---

# Pourquoi ?

Les comportements frauduleux sont souvent concentrés à certaines heures ou périodes.

---

# PARTIE 4 — Features de fréquence

Exemple :

Nombre de transactions :

* dernière heure ;
* dernières 24 heures ;
* 7 derniers jours.

En SQL :

```sql
COUNT(*)
OVER(
PARTITION BY customer_id
ORDER BY transaction_time
RANGE BETWEEN INTERVAL '24 hours' PRECEDING
AND CURRENT ROW
)
```

---

# Pourquoi ?

Un fraudeur effectue souvent de nombreuses opérations en peu de temps.

---

# PARTIE 5 — Velocity Features

Très utilisées chez :

* Stripe ;
* Visa ;
* Mastercard ;
* PayPal ;
* SentiLink.

Exemples :

```text
transactions_last_5_minutes

transactions_last_hour

devices_last_day

cards_last_week
```

Ces variables mesurent la **vitesse d'activité**.

---

# PARTIE 6 — Features statistiques

Calculer pour chaque client :

* moyenne ;
* médiane ;
* minimum ;
* maximum ;
* variance ;
* écart-type.

Exemple :

```text
current_amount

/

average_amount_last_month
```

Si ce ratio vaut :

```text
20
```

la transaction est potentiellement anormale.

---

# PARTIE 7 — Rolling Statistics

Exemple :

Montants :

```text
100

120

90

5000
```

La moyenne des trois premières transactions est proche de 103.

Une transaction à 5000 € représente une rupture nette.

---

# PARTIE 8 — Features géographiques

Exemples :

Distance entre :

* la transaction actuelle ;
* la précédente.

Supposons :

```text
Paris

↓

Tokyo

↓

20 minutes
```

Très suspect.

Feature :

```text
distance_from_previous
```

---

Autre feature :

```text
country_changed
```

Valeur :

```text
True
```

---

# PARTIE 9 — Device Features

Variables utiles :

* nouvel appareil ;
* nombre d'appareils utilisés ;
* appareil partagé par plusieurs comptes ;
* système d'exploitation ;
* navigateur.

Exemple :

```text
device_seen_before
```

Booléen.

---

# PARTIE 10 — IP Features

Variables :

* nouvelle IP ;
* pays de l'IP ;
* nombre de comptes utilisant la même IP ;
* score de réputation de l'IP (si disponible).

---

# PARTIE 11 — Email Features

Exemples :

Longueur de l'adresse :

```text
len(email)
```

Nombre de chiffres :

```text
john123456789@gmail.com
```

Domaine :

```text
gmail.com
```

Âge du domaine (si cette information est disponible).

---

# PARTIE 12 — Téléphone

Variables :

* indicatif pays ;
* longueur ;
* nombre de comptes associés ;
* première utilisation.

---

# PARTIE 13 — Identity Resolution

Chez SentiLink, cette partie est essentielle.

Supposons :

Même :

* téléphone ;
* email ;
* appareil.

Mais :

5 identités différentes.

Très suspect.

On crée :

```text
accounts_per_phone

accounts_per_device

accounts_per_email
```

---

# PARTIE 14 — Features de graphe

Imaginez un graphe :

```text
Compte A

↓

Téléphone

↓

Compte B

↓

Appareil

↓

Compte C
```

Variables possibles :

* degré du nœud ;
* taille de la composante ;
* nombre de voisins ;
* centralité.

Ces features sont très puissantes pour détecter les réseaux de fraude.

---

# PARTIE 15 — Encodage des variables catégorielles

Question fréquente :

> **How do you encode categorical variables?**

Réponse :

Selon le contexte :

* One-Hot Encoding pour les faibles cardinalités.
* Target Encoding avec précautions (validation adaptée pour éviter le data leakage).
* Frequency Encoding.
* Embeddings pour certains modèles de Deep Learning.

---

# PARTIE 16 — Data Leakage

Question incontournable.

Exemple :

On utilise :

```text
chargeback_received
```

pour prédire la fraude.

Erreur.

Le chargeback est connu **après** la transaction.

Le modèle "voit le futur".

---

# Définition

Le **data leakage** survient lorsqu'une information indisponible au moment de la prédiction est utilisée pendant l'entraînement.

C'est l'une des erreurs les plus graves en Machine Learning.

---

# PARTIE 17 — Feature Selection

Le recruteur :

> **How do you select features?**

Réponse :

Je combine plusieurs approches :

* connaissance métier ;
* analyse de corrélation ;
* importance des variables (par exemple via les modèles d'arbres ou SHAP) ;
* suppression des variables redondantes ;
* validation empirique sur un jeu de test indépendant.

---

# PARTIE 18 — SHAP

Très populaire.

Question.

> **Why use SHAP?**

Réponse.

SHAP permet d'expliquer :

* pourquoi une prédiction est élevée ;
* quelles variables ont augmenté le score ;
* quelles variables l'ont diminué.

Très utile :

* conformité ;
* audit ;
* confiance.

---

# PARTIE 19 — Pipeline de Features

Architecture classique :

```text
Raw Data

↓

Cleaning

↓

Aggregation

↓

Feature Engineering

↓

Feature Store

↓

Training

↓

Production
```

---

Pourquoi un **Feature Store** ?

Pour garantir que les mêmes transformations sont utilisées :

* pendant l'entraînement ;
* pendant l'inférence.

Cela évite les incohérences entre les environnements.

---

# PARTIE 20 — Questions d'entretien

### Q1

**What's the best feature for fraud detection?**

Réponse :

Il n'existe pas de feature universellement meilleure. Les variables comportementales, temporelles et relationnelles sont souvent très performantes, mais leur efficacité dépend du contexte métier et des données disponibles.

---

### Q2

**How do you know if a feature is useful?**

Réponse :

Je mesure son impact sur les performances du modèle, j'analyse son importance, sa stabilité dans le temps et sa disponibilité en production.

---

### Q3

**Would you create hundreds of features?**

Réponse :

Oui si elles apportent de l'information, mais je surveille la redondance, le coût de calcul et le risque de surapprentissage.

---

### Q4

**Can too many features hurt performance?**

Réponse :

Oui.

Elles peuvent :

* augmenter le bruit ;
* ralentir l'entraînement ;
* compliquer l'interprétation ;
* accroître le risque d'overfitting.

---

### Q5

**How do you avoid leakage?**

Réponse :

Je m'assure que chaque feature est calculée uniquement à partir d'informations disponibles **avant** le moment où la prédiction est effectuée.

---

# Cas pratique SentiLink

Le recruteur vous demande :

> **You have customer_id, device_id, email, phone_number, IP, transaction_amount, transaction_time. Design the best fraud features you can think of.**

Une réponse structurée pourrait être :

### Identité

* account_age
* previous_fraud_count
* total_transactions

### Appareil

* device_seen_before
* accounts_per_device
* device_age

### Téléphone

* accounts_per_phone
* phone_country
* phone_seen_before

### Email

* accounts_per_email
* email_domain
* email_length

### IP

* ip_country
* accounts_per_ip
* ip_changed

### Temporel

* hour_of_day
* day_of_week
* transactions_last_hour
* transactions_last_day

### Montants

* average_amount_30d
* max_amount_30d
* amount_to_average_ratio

### Géographie

* distance_from_previous_transaction
* country_changed

### Réseau

* device_degree
* phone_degree
* email_degree
* graph_cluster_size

---

# Ce qu'un excellent candidat ajoute

Il conclut en disant :

> « Je privilégierais des features stables, calculables en temps réel, explicables et disponibles au moment de la prédiction. Je validerais ensuite leur contribution via des expérimentations, tout en surveillant leur coût de calcul et leur robustesse face à l'évolution des comportements frauduleux. »

Cette conclusion montre une vision **production**, ce qui est très apprécié par les recruteurs.

---

## La suite

Le **Module 33** sera consacré à la **détection d'anomalies (Anomaly Detection)**, un sujet particulièrement pertinent lorsque les labels de fraude sont rares ou incomplets. Nous y couvrirons notamment Isolation Forest, Local Outlier Factor, One-Class SVM, les autoencodeurs, leurs avantages, leurs limites et leurs cas d'utilisation en production.
