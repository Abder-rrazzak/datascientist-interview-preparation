Nous allons avancer vers un **niveau encore plus proche d'un entretien réel**.

# MODULE 15 — Feature Engineering avancé pour la fraude

Le modèle n'est pas nécessairement la partie la plus difficile.

Le vrai défi est souvent :

> **Comment transformer des événements bruts en signaux permettant de distinguer un comportement normal d'un comportement frauduleux ?**

---

# 1. Situation d'entretien

Le recruteur vous donne seulement :

```text
customer_id
timestamp
amount
device_id
ip_address
email
phone
country
```

Puis il demande :

> **What features would you create to detect fraud?**

Il attend une réponse structurée.

---

# 2. Features temporelles

À partir de :

```text
timestamp
```

on peut créer :

```text
hour
day_of_week
day_of_month
month
is_weekend
is_night
```

Exemple :

```python
df["hour"] = df["timestamp"].dt.hour

df["day_of_week"] = df["timestamp"].dt.dayofweek

df["is_weekend"] = (
    df["day_of_week"] >= 5
).astype(int)
```

Mais attention :

Une simple heure n'est pas forcément une excellente feature.

Une feature plus intéressante serait :

```text
transactions_last_10_minutes
```

---

# 3. Velocity Features

Très importantes en fraude.

Une **velocity feature** mesure la fréquence d'activité pendant une période donnée.

Exemples :

```text
transactions_last_5min
transactions_last_10min
transactions_last_1h
transactions_last_24h
```

Mais également :

```text
amount_last_10min
amount_last_1h
unique_devices_last_24h
unique_ips_last_24h
```

---

## Exemple

Utilisateur normal :

```text
2 transactions / heure
```

Utilisateur suspect :

```text
27 transactions / 5 minutes
```

Même si chaque transaction semble normale individuellement, le comportement global est très différent.

---

# 4. Features comportementales

On veut comparer la transaction actuelle au comportement historique du client.

Par exemple :

```text
average_amount_30d
median_amount_30d
max_amount_30d
transaction_count_30d
```

Puis :

```text
amount / average_amount_30d
```

---

## Exemple

Historique :

```text
20 €
30 €
25 €
40 €
```

Moyenne :

```text
28,75 €
```

Nouvelle transaction :

```text
500 €
```

Ratio :

```text
500 / 28,75 ≈ 17,4
```

Le modèle reçoit alors :

```text
amount_ratio = 17.4
```

C'est beaucoup plus informatif que `amount = 500` seul.

---

# 5. Recency Features

Une autre catégorie très importante :

> **Depuis combien de temps quelque chose s'est-il produit ?**

Exemples :

```text
time_since_last_transaction
time_since_last_login
time_since_device_seen
time_since_last_password_change
```

Exemple :

```text
time_since_last_transaction = 4 seconds
```

et

```text
amount = 2 000 €
```

peut constituer un signal intéressant.

---

# 6. Device Features

Supposons :

```text
device_id = DEV123
```

On peut calculer :

```text
device_customer_count
device_transaction_count
device_fraud_rate
device_first_seen
device_age
```

Exemple :

```text
device_customer_count = 150
```

Un même appareil utilisé par 150 comptes peut être extrêmement intéressant à analyser.

⚠️ Mais ce n'est pas automatiquement une fraude.

Un appareil peut être partagé dans :

* une entreprise ;
* un cybercafé ;
* une famille ;
* une infrastructure de test.

Il faut donc combiner plusieurs signaux.

---

# 7. IP Features

Même logique.

```text
ip_customer_count
ip_transaction_count
ip_country_count
ip_fraud_rate
```

Exemple :

```text
IP 192.xxx.xxx.xxx

→ 350 comptes
→ 7 pays
→ 25 comptes frauduleux
```

C'est un signal beaucoup plus puissant que :

```text
country = US
```

---

# 8. Email Features

On peut créer :

```text
email_domain
email_domain_customer_count
email_domain_fraud_rate
email_age
```

Et également détecter certains patterns :

```text
firstname.lastname@gmail.com
```

versus

```text
random12345@temporary-domain.com
```

Attention toutefois à ne pas créer de règles naïves qui introduisent des biais.

---

# 9. Phone Features

Exemples :

```text
phone_customer_count
phone_transaction_count
phone_fraud_rate
phone_age
```

Et surtout :

```text
phone_country != customer_country
```

peut constituer un signal supplémentaire.

---

# 10. Geographic Features

On peut comparer :

```text
billing_country
shipping_country
ip_country
phone_country
```

Exemple :

```text
IP       → France
Phone    → Maroc
Address  → États-Unis
Card     → Royaume-Uni
```

Ce n'est pas nécessairement frauduleux.

Mais cela peut constituer un **signal de risque**.

---

# 11. Interaction Features

Une erreur fréquente est de créer uniquement des variables individuelles.

Parfois, la combinaison est beaucoup plus informative.

Par exemple :

```text
new_device = 1
```

est relativement faible.

Mais :

```text
new_device = 1
AND
new_ip = 1
AND
amount_ratio = 15
AND
transactions_last_10min = 8
```

est beaucoup plus intéressant.

Un modèle comme XGBoost est particulièrement efficace pour apprendre ce genre d'interactions non linéaires.

---

# 12. Features relationnelles

Nous revenons ici au **Graph ML**.

On peut calculer :

```text
device_customer_count
ip_customer_count
phone_customer_count
email_customer_count
```

Mais également :

```text
shared_device_count
shared_ip_count
connected_component_size
graph_degree
community_size
```

---

# 13. Une feature particulièrement intéressante

Imaginez :

```text
customer A
    |
    +---- Device 1
    |
    +---- IP 1
```

et :

```text
customer B
    |
    +---- Device 1
    |
    +---- IP 2
```

On peut créer :

```text
device_shared_with_other_customers = 1
```

Puis aller plus loin :

```text
device_fraud_rate
```

---

# 14. Attention au Data Leakage

C'est ici que le recruteur peut vous pousser très loin.

Supposons qu'une transaction ait lieu :

```text
10 janvier
```

et qu'on sache seulement :

```text
20 janvier → transaction confirmée frauduleuse
```

Vous ne pouvez pas utiliser cette information pour construire une feature disponible le 10 janvier.

---

## Mauvais

```text
device_fraud_rate
```

calculé sur **toute l'histoire**, y compris les événements postérieurs.

---

## Correct

Calculer uniquement avec les informations disponibles **avant la transaction**.

C'est ce qu'on appelle :

> **Point-in-time correctness**

---

# 15. Question d'entretien

### Recruteur :

> **How would you prevent data leakage when creating fraud features?**

### Réponse excellente :

> I would make sure that every feature is computed using only information that was available at prediction time. For historical aggregates, I would use time-based windows and point-in-time joins rather than aggregating over the entire dataset. I would also use a temporal train-validation-test split to make sure future information cannot leak into the training data.

---

# 16. Très bonne question de suivi

Le recruteur demande :

> **Why is a random train-test split potentially dangerous for fraud detection?**

### Réponse :

Parce que les données de fraude sont temporelles.

Un split aléatoire peut placer :

```text
transaction du 5 janvier
```

dans le test et :

```text
transaction très similaire du 7 janvier
```

dans le train.

Cela peut donner une estimation artificiellement optimiste des performances.

Je privilégierais donc :

```text
Past
  ↓
Training

Recent past
  ↓
Validation

Future
  ↓
Test
```

---

# 17. Feature Engineering : réponse complète en entretien

Si le recruteur vous demande :

> **How would you approach feature engineering for fraud detection?**

Vous pouvez répondre :

> I would organize features into several groups. First, temporal and velocity features such as transaction counts and amounts over different time windows. Second, customer behavioral features comparing the current transaction with the customer's historical behavior. Third, entity-level features around devices, IP addresses, emails and phone numbers. Fourth, geographic and relational features capturing inconsistencies and shared entities. Finally, I would carefully validate that all features are point-in-time correct to avoid data leakage.

C'est une **très bonne réponse de niveau Senior**.

---

# 18. Exercice Python

Vous disposez de :

```python
df = pd.DataFrame({
    "customer_id": [1, 1, 1, 2, 2],
    "timestamp": pd.to_datetime([
        "2026-01-01 10:00",
        "2026-01-01 10:05",
        "2026-01-01 10:10",
        "2026-01-01 11:00",
        "2026-01-01 11:05"
    ]),
    "amount": [100, 150, 500, 50, 70]
})
```

Le recruteur vous demande :

> **Create a feature representing the customer's previous transaction amount.**

Il attend que vous connaissiez `shift()`.

```python
df = df.sort_values(
    ["customer_id", "timestamp"]
)

df["previous_amount"] = (
    df.groupby("customer_id")["amount"]
      .shift(1)
)
```

Résultat conceptuel :

| customer | amount | previous_amount |
| -------: | -----: | --------------: |
|        1 |    100 |             NaN |
|        1 |    150 |             100 |
|        1 |    500 |             150 |
|        2 |     50 |             NaN |
|        2 |     70 |              50 |

---

# 19. Question suivante

Le recruteur demande maintenant :

> **Can you create a feature representing the customer's average transaction amount before the current transaction?**

Attention :

```python
df.groupby("customer_id")["amount"].transform("mean")
```

❌ serait incorrect.

Pourquoi ?

Parce qu'il utilise potentiellement **la transaction actuelle et les transactions futures**.

Il faut créer une moyenne historique qui respecte le temps.

Une possibilité :

```python
df = df.sort_values(
    ["customer_id", "timestamp"]
)

df["previous_avg_amount"] = (
    df.groupby("customer_id")["amount"]
      .transform(
          lambda x: x.shift(1).expanding().mean()
      )
)
```

Ainsi, pour chaque transaction, la moyenne est calculée uniquement à partir des transactions précédentes.

---

# 20. Ce que le recruteur veut entendre

Il ne cherche pas seulement :

```python
shift()
```

Il cherche à voir si vous comprenez :

> **"Je ne dois jamais utiliser une information qui n'était pas disponible au moment de la décision."**

C'est l'un des principes les plus importants d'un système ML de fraude.

---

# 🎯 Niveau suivant : System Design

Nous allons maintenant passer à une partie encore plus exigeante :

> **Design a real-time fraud detection system for 20,000 transactions per second with a latency requirement below 100 ms.**

Vous devrez concevoir l'architecture complète :

```text
Client
   ↓
API Gateway
   ↓
Load Balancer
   ↓
Fraud Service
   ↓
Feature Store
   ↓
Model
   ↓
Decision Engine
   ↓
Response
```

Puis expliquer :

* où placer **Kafka** ;
* pourquoi utiliser **Redis** ;
* où mettre **S3** ;
* comment utiliser **PostgreSQL / Redshift** ;
* comment entraîner le modèle ;
* comment gérer **MLflow** ;
* comment monitorer le système ;
* comment gérer **Data Drift** ;
* comment garantir une latence < 100 ms ;
* et surtout **comment rendre l'architecture scalable à 20 000 transactions/seconde**.

C'est le type de question qui peut faire passer votre entretien d'un niveau **"connaissances ML"** à **"Data Scientist capable de construire un système réel"**.
