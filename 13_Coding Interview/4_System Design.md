Nous passons maintenant au **System Design**, une partie très importante, car le Data Scientist doit écrire du code de production et participer au cycle complet : acquisition → features → modèle → production → monitoring.

# MODULE 16 — System Design : Fraud Detection en temps réel

## 🎯 Question d'entretien

Le recruteur vous dit :

> **Design a real-time fraud detection system capable of processing 20,000 transactions per second with a latency below 100 ms.**

Vous avez 20 à 30 minutes pour répondre.

Le but n'est pas de réciter AWS.

Le recruteur veut voir votre capacité à **raisonner sur l'architecture**.

---

# 1. Commencez par clarifier les exigences

⚠️ Ne commencez pas immédiatement par :

> "Je vais utiliser Kafka, Redis et XGBoost."

Commencez par poser des questions.

### Questions fonctionnelles

Je demanderais :

> What exactly are we predicting?

Par exemple :

```text
fraud_probability
```

ou :

```text
approve / review / decline
```

Ensuite :

> What information is available at prediction time?

Puis :

> How quickly do we need to return a decision?

Ici :

```text
< 100 ms
```

---

### Questions non fonctionnelles

Je voudrais également connaître :

* 20 000 transactions/sec : moyenne ou pic ?
* disponibilité attendue ?
* tolérance aux pannes ?
* besoin de replay des événements ?
* combien de temps conserver les données ?
* le modèle doit-il être explicable ?
* quelle est la fréquence de mise à jour du modèle ?

---

# 2. Architecture globale

Une bonne architecture pourrait être :

```text
                         TRANSACTION
                              │
                              ▼
                       API Gateway
                              │
                              ▼
                       Load Balancer
                              │
                  ┌───────────┴───────────┐
                  ▼                       ▼
             Fraud API #1            Fraud API #2
                  │                       │
                  └───────────┬───────────┘
                              ▼
                        Feature Store
                              │
                           Redis
                              │
                              ▼
                         ML Model
                              │
                              ▼
                       Decision Engine
                              │
                    ┌─────────┴─────────┐
                    ▼                   ▼
                  PASS                REVIEW
                                        │
                                        ▼
                                  FRAUD ANALYST
```

Et en parallèle :

```text
Transaction
     │
     ▼
   Kafka
     │
     ├──────────► Flink
     │              │
     │              ▼
     │        Real-time Features
     │
     └──────────► Data Lake / S3
```

---

# 3. Pourquoi séparer le chemin temps réel du chemin analytique ?

C'est une question importante.

Nous avons deux besoins différents.

## Chemin critique

```text
Transaction
    ↓
Features
    ↓
Model
    ↓
Decision
```

Objectif :

> **moins de 100 ms**

---

## Chemin asynchrone

```text
Transaction
    ↓
Kafka
    ↓
Flink
    ↓
S3
    ↓
Analytics / Training
```

Ce chemin peut prendre davantage de temps.

Il ne doit pas bloquer la décision.

---

# 4. Pourquoi Kafka ?

Kafka sert principalement à transporter les événements.

```text
Transaction
      ↓
    Kafka
      ↓
 ┌────┼──────────┐
 ↓    ↓          ↓
Flink S3       Monitoring
```

Un avantage majeur est le **découplage**.

Le producteur de transactions n'a pas besoin de connaître tous les consommateurs.

---

# 5. Kafka et la latence

Attention à une subtilité.

Si le chemin critique est :

```text
API
 ↓
Kafka
 ↓
Consumer
 ↓
Model
```

cela peut compliquer l'objectif de moins de 100 ms.

Je pourrais donc avoir :

```text
                 ┌──────────────► Kafka
Transaction ─────┤
                 │
                 └──────────────► Fraud API
                                      ↓
                                   Model
```

Kafka sert alors principalement à l'**event streaming, à l'analytics et à l'alimentation des features**, tandis que la décision synchrone reste extrêmement courte.

⭐ Cette nuance montre que vous comprenez l'architecture plutôt que de simplement citer Kafka.

---

# 6. Pourquoi Redis ?

Supposons que nous ayons besoin de :

```text
transactions_last_10min
amount_last_1h
unique_devices_last_24h
```

Nous ne voulons pas interroger PostgreSQL à chaque transaction.

Cela pourrait devenir :

```text
Transaction
     ↓
PostgreSQL
     ↓
Query
     ↓
Aggregation
```

❌ Trop lent et difficile à scaler.

À la place :

```text
                Redis
                  │
       ┌──────────┼───────────┐
       ▼          ▼           ▼
10min count    1h amount   24h devices
```

Redis fournit des accès très rapides en mémoire.

---

# 7. Pourquoi PostgreSQL ?

PostgreSQL peut être utilisé pour :

* données transactionnelles ;
* données structurées ;
* métadonnées ;
* informations clients ;
* configuration.

Mais je ne l'utiliserais pas comme moteur principal pour calculer toutes les features temps réel à 20 000 transactions/seconde.

---

# 8. Pourquoi S3 ?

S3 devient le **Data Lake**.

On peut y stocker :

```text
raw transactions
historical data
training datasets
predictions
model artifacts
logs
```

Par exemple :

```text
s3://fraud-data/
    ├── raw/
    ├── processed/
    ├── features/
    ├── training/
    └── models/
```

Cela permet de conserver l'historique nécessaire au Machine Learning.

---

# 9. Pourquoi Redshift ?

Redshift est davantage adapté à l'analytique à grande échelle.

Par exemple :

> Quelle est la fraude par pays et par mois ?

```sql
SELECT
    country,
    DATE_TRUNC('month', transaction_date) AS month,
    COUNT(*) AS transactions,
    SUM(CASE WHEN is_fraud = 1 THEN 1 ELSE 0 END) AS frauds
FROM transactions
GROUP BY country, DATE_TRUNC('month', transaction_date);
```

Ce type de requête n'a pas besoin d'être exécuté dans le chemin critique de scoring.

---

# 10. Le modèle

Pour des données tabulaires, une baseline pourrait être :

```text
Logistic Regression
```

Puis :

```text
XGBoost
```

ou :

```text
LightGBM
```

Le modèle reçoit :

```text
amount
customer_age
transactions_last_10min
average_amount_30d
new_device
device_customer_count
ip_customer_count
country_changed
...
```

et retourne :

```text
fraud_probability = 0.93
```

---

# 11. Decision Engine

⚠️ Le modèle ne devrait pas forcément décider directement.

Exemple :

```text
fraud_probability = 0.93
```

Le système peut appliquer des règles :

```text
score < 0.30
       ↓
     PASS
```

```text
0.30 <= score < 0.80
       ↓
    REVIEW
```

```text
score >= 0.80
       ↓
    DECLINE
```

Mais ces seuils doivent être déterminés à partir des **coûts métier et des objectifs opérationnels**, pas choisis arbitrairement.

---

# 12. Pourquoi séparer modèle et décision ?

Parce que :

```text
Model
=
probabilité
```

alors que :

```text
Decision Engine
=
probabilité + règles métier + contraintes
```

Cette séparation facilite :

* les changements de politique ;
* les tests ;
* l'explicabilité ;
* le monitoring.

---

# 13. Comment gérer 20 000 transactions/sec ?

C'est une question essentielle.

Une seule instance ne suffit pas.

Nous allons utiliser plusieurs instances :

```text
                 Load Balancer
                       │
          ┌────────────┼────────────┐
          ▼            ▼            ▼
       API #1        API #2       API #3
          │            │            │
          └────────────┼────────────┘
                       ▼
                  Model Service
```

On peut augmenter horizontalement le nombre d'instances.

C'est le principe du :

> **Horizontal Scaling**

---

# 14. Vertical vs Horizontal Scaling

### Vertical

Augmenter la puissance d'une machine :

```text
4 CPU
 ↓
16 CPU
```

### Horizontal

Ajouter des machines :

```text
1 instance
 ↓
10 instances
 ↓
100 instances
```

Pour une architecture distribuée à fort trafic, le scaling horizontal est généralement préférable.

---

# 15. Calcul approximatif

20 000 transactions/sec.

Si une instance traite :

```text
500 transactions/sec
```

il faut théoriquement :

```text
20 000 / 500
=
40 instances
```

Mais je ne déploierais pas exactement 40 instances.

Je prévoirais une marge pour :

* pics de trafic ;
* pannes ;
* maintenance ;
* autoscaling.

Par exemple :

```text
40 nécessaires
+
30 % marge
≈ 52 instances
```

Ce n'est qu'un exemple : le vrai nombre doit être déterminé par des **benchmarks de charge**.

---

# 16. La latence

Le recruteur demande :

> How would you keep latency below 100 ms?

Je décomposerais la latence :

```text
Network
   +
Feature retrieval
   +
Model inference
   +
Decision logic
   +
Network response
```

Exemple :

| Étape         |    Budget |
| ------------- | --------: |
| Network       |     15 ms |
| Feature Store |     20 ms |
| Model         |      5 ms |
| Decision      |      5 ms |
| Réponse       |     15 ms |
| **Total**     | **60 ms** |

Il reste donc une marge.

---

# 17. Une erreur fréquente

Supposons :

```text
Model inference = 5 ms
```

Le candidat dit :

> "Le modèle est rapide."

Mais :

```text
Database query = 150 ms
```

Le système entier est lent.

Le **model latency** n'est donc pas la même chose que la **end-to-end latency**.

⭐ Très important.

---

# 18. Comment réduire la latence ?

Je chercherais à :

### Pré-calculer les features

Au lieu de :

```text
Calculate → Query → Aggregate
```

faire :

```text
Streaming
   ↓
Precompute
   ↓
Redis
```

---

### Cacher les données fréquemment utilisées

```text
Redis
```

---

### Réduire les appels réseau

Éviter :

```text
API
 ↓
Service A
 ↓
Service B
 ↓
Database
 ↓
Service C
```

si toutes ces étapes sont synchrones.

---

### Utiliser un modèle léger

Si :

```text
XGBoost = 5 ms
```

mais :

```text
Deep Neural Network = 80 ms
```

le modèle complexe n'est pas forcément le meilleur choix.

---

# 19. Résilience

Le recruteur peut demander :

> **What happens if Redis goes down?**

Il faut prévoir un comportement dégradé.

Par exemple :

```text
Redis unavailable
       ↓
Fallback
       ↓
Default features / backup store
       ↓
Rules-based decision
```

ou éventuellement :

```text
Temporary manual review
```

selon les exigences du produit.

Il faut surtout **éviter que toute l'activité soit indisponible simplement parce qu'un composant tombe en panne**.

---

# 20. Monitoring

Je surveillerais quatre niveaux.

### Infrastructure

```text
CPU
Memory
Throughput
Latency
Error rate
```

### Data

```text
Missing values
Schema changes
Feature distributions
Data drift
```

### Model

```text
Precision
Recall
PR-AUC
False positives
False negatives
Calibration
```

### Business

```text
Fraud losses
Approval rate
Decline rate
Manual review rate
Customer conversion
```

---

# 21. Un problème très important : Label Delay

Le recruteur peut poser :

> **How do you monitor model performance if fraud labels arrive weeks later?**

Excellent problème.

Lorsque la transaction arrive :

```text
Day 0
Transaction
```

Mais nous savons seulement :

```text
Day 20
Confirmed fraud
```

On ne peut donc pas calculer immédiatement le Recall réel.

Il faut distinguer :

```text
Real-time monitoring
```

et :

```text
Delayed performance monitoring
```

---

# 22. Monitoring immédiat

On peut surveiller :

* score distribution ;
* feature distribution ;
* taux de refus ;
* taux de review ;
* latence ;
* erreurs ;
* volume.

---

# 23. Monitoring différé

Lorsque les labels deviennent disponibles :

```text
Actual fraud
       ↓
Compare
       ↓
Prediction
       ↓
Precision
Recall
PR-AUC
```

---

# 24. Data Drift

Supposons que :

### Avant

```text
average_amount = 100 €
```

### Maintenant

```text
average_amount = 350 €
```

La distribution a changé.

Il faut investiguer.

Mais attention :

> **Data Drift ≠ forcément Model Failure.**

Un changement de distribution peut être parfaitement légitime.

---

# 25. Concept Drift

Plus dangereux.

Le comportement de la fraude change.

Exemple :

```text
2025
Fraude → nouveau device
```

En 2026 :

```text
Fraude → device ancien compromis
```

La relation :

```text
Feature → Fraud
```

a changé.

C'est du :

> **Concept Drift**

---

# 26. MLOps

Le pipeline d'entraînement pourrait être :

```text
S3
 ↓
Data Validation
 ↓
Feature Engineering
 ↓
Train
 ↓
Evaluation
 ↓
MLflow
 ↓
Model Registry
 ↓
Approval
 ↓
Deployment
```

---

# 27. Déploiement progressif

Je ne mettrais pas directement :

```text
Model v2
   ↓
100 % trafic
```

Je préférerais :

```text
Model v2
   ↓
Shadow
   ↓
Canary 1 %
   ↓
Canary 5 %
   ↓
Canary 25 %
   ↓
100 %
```

Avec monitoring entre chaque étape.

---

# 28. Architecture finale

Voici la réponse que vous devriez être capable de dessiner au tableau :

```text
                         ┌─────────────────────┐
                         │      Clients        │
                         └──────────┬──────────┘
                                    │
                                    ▼
                            ┌───────────────┐
                            │  API Gateway  │
                            └───────┬───────┘
                                    │
                                    ▼
                            ┌───────────────┐
                            │ Load Balancer │
                            └───────┬───────┘
                                    │
                       ┌────────────┴────────────┐
                       ▼                         ▼
                ┌────────────┐            ┌────────────┐
                │ Fraud API  │            │ Fraud API  │
                │     #1     │            │     #2     │
                └─────┬──────┘            └─────┬──────┘
                      │                         │
                      └────────────┬────────────┘
                                   ▼
                           ┌───────────────┐
                           │ Feature Store │
                           └───────┬───────┘
                                   │
                                   ▼
                                Redis
                                   │
                                   ▼
                            ┌────────────┐
                            │ ML Model   │
                            │  XGBoost   │
                            └─────┬──────┘
                                  │
                                  ▼
                         ┌─────────────────┐
                         │ Decision Engine │
                         └───────┬─────────┘
                                 │
                      ┌──────────┼──────────┐
                      ▼          ▼          ▼
                    PASS       REVIEW     DECLINE


          ASYNCHRONOUS PIPELINE
          
 Transaction ──────► Kafka
                       │
                       ▼
                     Flink
                       │
               ┌───────┴────────┐
               ▼                ▼
             Redis             S3
                                │
                                ▼
                         Training Pipeline
                                │
                                ▼
                             MLflow
                                │
                                ▼
                         Model Registry
                                │
                                ▼
                         Deployment
```

---

# 🎤 Réponse orale complète

Si le recruteur vous demande directement :

> **"Can you design the system?"**

Vous pourriez répondre :

> **"I would separate the real-time scoring path from the asynchronous data and training pipelines. The transaction would enter through an API Gateway and Load Balancer and be handled by horizontally scalable fraud-scoring services. For low-latency feature retrieval, I would use a Feature Store backed by an in-memory system such as Redis. The model would generate a fraud probability, and a separate decision layer would combine that score with business rules.**
>
> **In parallel, transaction events would be published to Kafka and processed by a streaming engine such as Flink to maintain real-time aggregates and feed the data lake. Historical data would be stored in S3 for analytics and model training. I would use MLflow and a Model Registry to manage experiments and model versions.**
>
> **For the 100-millisecond latency requirement, I would profile the entire end-to-end path rather than focusing only on model inference. I would minimize synchronous network calls, precompute features where possible, use caching, and horizontally scale the scoring service. Finally, I would monitor infrastructure, data quality, model performance, drift and business metrics, and deploy new models progressively using shadow and canary deployments."**

Cette réponse est déjà **très solide pour un entretien Data Scientist orienté production**.

---

# 🔥 Question piège du recruteur

Maintenant, il peut vous pousser :

> **"Why don't you simply put everything into AWS Lambda?"**

Ne répondez surtout pas :

> "Lambda est moins performant."

La bonne approche est de discuter de :

* durée d'exécution ;
* cold starts ;
* concurrence ;
* latence ;
* contrôle de l'environnement ;
* dépendances ML ;
* coût ;
* scalabilité ;
* nature du trafic.

Vous devez être capable de comparer :

```text
Lambda
vs
ECS
vs
EKS
vs
EC2
```

---

# 🎯 Prochaine étape — AWS pour l'entretien

Nous allons maintenant faire **MODULE 17 — AWS spécialement orienté Data Scientist**, et non un cours AWS général.

Nous allons couvrir précisément :

```text
S3
EC2
ECS
EKS
Lambda
RDS
Redshift
ElastiCache / Redis
SageMaker
IAM
CloudWatch
VPC
API Gateway
SQS / SNS
```

avec pour chacun :

**Pourquoi ? → Quand l'utiliser ? → Quand ne pas l'utiliser ? → Question d'entretien → Réponse idéale → Cas SentiLink.**
