Nous abordons maintenant le **MODULE 21**, qui est extrêmement important pour SentiLink.

Dans l'offre d'emploi, les technologies mentionnées sont :

* **Python 3**
* **PostgreSQL**
* **AWS (EC2, S3, RDS, Redshift, etc.)**
* **APIs temps réel**

Le recruteur ne cherche **pas un ingénieur DevOps**, mais il s'attend à ce qu'un Data Scientist comprenne comment son modèle est déployé et fonctionne en production.

---

# MODULE 21 — AWS, Architecture de Déploiement et APIs (Niveau SentiLink)

# Objectifs

À la fin de ce module, vous devrez être capable de :

* comprendre une architecture AWS de Machine Learning ;
* expliquer le rôle de chaque service AWS ;
* déployer mentalement un modèle ML en production ;
* comprendre le fonctionnement d'une API REST ;
* expliquer comment un score de fraude est calculé en temps réel ;
* répondre aux questions d'entretien sur AWS.

---

# 1. Vision globale

Imaginez qu'une banque utilise SentiLink.

Lorsqu'un client demande un prêt :

```text
Client
   │
   ▼
Banque
   │
HTTP Request
   │
   ▼
SentiLink API
   │
   ▼
Feature Engineering
   │
   ▼
ML Model
   │
   ▼
Fraud Score
   │
   ▼
Réponse à la banque
```

Tout ce processus doit généralement prendre **quelques dizaines de millisecondes**.

---

# 2. Architecture AWS

Une architecture simplifiée pourrait être :

```text
                 Internet
                      │
                      ▼
              Load Balancer
                      │
        ┌─────────────┴─────────────┐
        ▼                           ▼
     EC2 Instance              EC2 Instance
        │                           │
        └─────────────┬─────────────┘
                      ▼
                 Model API
                      │
          ┌───────────┼────────────┐
          ▼           ▼            ▼
        RDS          Redis         S3
          │
          ▼
     PostgreSQL
```

---

# 3. Pourquoi plusieurs EC2 ?

Question classique.

Pourquoi ne pas avoir un seul serveur ?

Parce que :

* panne matérielle ;
* surcharge ;
* maintenance ;
* montée en charge.

Plusieurs instances permettent une meilleure disponibilité.

---

# 4. EC2

EC2 est un serveur virtuel.

On peut y installer :

* Python ;
* FastAPI ;
* Flask ;
* XGBoost ;
* PostgreSQL client ;
* Docker.

En pratique :

```text
Ubuntu

↓

Python

↓

Model API

↓

Prediction
```

---

# 5. S3

S3 est un stockage objet.

On y place :

```text
trained_model.pkl

features.csv

training_data.parquet

logs

backups
```

---

# 6. Pourquoi S3 ?

Question :

> Why store models in S3?

Réponse :

> S3 provides durable, scalable and cost-effective storage. Models, datasets and artifacts can be versioned and accessed by training or inference services.

---

# 7. RDS

RDS est une base de données managée.

Dans notre cas :

```text
PostgreSQL
```

Elle peut contenir :

```text
Customers

Transactions

Devices

Predictions

Logs
```

---

# 8. Pourquoi RDS plutôt qu'installer PostgreSQL sur EC2 ?

Réponse :

Parce que RDS gère automatiquement :

* sauvegardes ;
* mises à jour ;
* réplication ;
* restauration ;
* haute disponibilité.

---

# 9. Redshift

Question fréquente.

Quelle différence entre PostgreSQL et Redshift ?

### PostgreSQL

Optimisé pour :

```text
Transactions

OLTP

INSERT

UPDATE

DELETE
```

---

### Redshift

Optimisé pour :

```text
Analytics

OLAP

Huge datasets

Aggregation
```

Par exemple :

```sql
SELECT
country,
AVG(amount)
FROM transactions
GROUP BY country;
```

sur plusieurs milliards de lignes.

---

# 10. RDS vs Redshift

| RDS              | Redshift            |
| ---------------- | ------------------- |
| OLTP             | OLAP                |
| Transactions     | Analytics           |
| Temps réel       | Reporting           |
| INSERT fréquent  | Lecture massive     |
| PostgreSQL/MySQL | Entrepôt de données |

---

# 11. Où entraîner le modèle ?

En général :

```text
Raw Data

↓

S3

↓

Redshift

↓

Python

↓

Training

↓

Model

↓

S3
```

---

# 12. Où faire les prédictions ?

En production :

```text
API

↓

EC2

↓

Load model

↓

Predict

↓

Return score
```

---

# 13. Pourquoi ne pas utiliser Redshift pour le scoring ?

Parce que :

Le scoring doit répondre très rapidement.

Redshift est conçu pour des requêtes analytiques massives, pas pour servir des prédictions individuelles à très faible latence.

---

# 14. Redis

Redis est une base mémoire extrêmement rapide.

Exemple :

```text
device_id

↓

historical_features
```

Au lieu d'interroger PostgreSQL à chaque requête, certaines informations fréquemment utilisées peuvent être mises en cache.

---

# 15. Exemple

Sans cache :

```text
API

↓

SQL Query

↓

100 ms
```

Avec Redis :

```text
API

↓

Redis

↓

2 ms
```

---

# 16. API REST

Question presque certaine.

Une API reçoit :

```json
{
  "customer_id": 123,
  "amount": 900,
  "device_id": "ABC123"
}
```

Puis :

```text
↓

Model

↓

Score

↓

Response
```

---

# 17. Réponse JSON

Exemple :

```json
{
  "fraud_score": 0.87,
  "decision": "review"
}
```

---

# 18. FastAPI

Aujourd'hui, FastAPI est un excellent choix pour exposer un modèle Python.

Exemple minimal :

```python
from fastapi import FastAPI

app = FastAPI()

@app.post("/predict")
def predict(transaction):
    score = model.predict_proba(transaction)
    return {"score": score}
```

---

# 19. Pourquoi FastAPI ?

Question :

> Why FastAPI?

Réponse :

> FastAPI is fast, easy to use, supports automatic documentation, data validation and integrates well with Python machine learning services.

---

# 20. API Latency

Supposons :

```text
Feature extraction

20 ms

↓

Prediction

5 ms

↓

Serialization

3 ms

↓

Network

15 ms
```

Total :

```text
43 ms
```

Très bon résultat.

---

# 21. Le modèle est lent

Supposons :

```text
Prediction

250 ms
```

Question :

Que faire ?

Réponses possibles :

* simplifier le modèle ;
* optimiser les features ;
* réduire les accès à la base ;
* utiliser un cache ;
* profiler le code ;
* paralléliser certaines opérations si pertinent.

Il faut mesurer avant d'optimiser.

---

# 22. CloudWatch

CloudWatch sert à surveiller :

```text
CPU

Memory

Latency

Errors

Logs

Requests
```

---

# 23. Que surveiller ?

Le Data Scientist doit suivre :

```text
Prediction latency

Error rate

Fraud score distribution

Precision

Recall

Drift
```

---

# 24. IAM

IAM gère :

```text
Users

Roles

Permissions
```

Exemple :

```text
EC2

↓

Read

↓

S3

↓

YES
```

Mais :

```text
Delete Bucket

↓

NO
```

Principe fondamental :

**Least Privilege** : accorder uniquement les permissions nécessaires.

---

# 25. Auto Scaling

Supposons :

```text
100 requests/sec
```

Puis :

```text
5 000 requests/sec
```

AWS peut créer automatiquement de nouvelles instances EC2 si l'architecture est configurée pour cela.

---

# 26. Load Balancer

Le Load Balancer répartit les requêtes.

Sans lui :

```text
EC2

↓

100 %

des requêtes
```

Avec lui :

```text
EC2-1

33 %

EC2-2

33 %

EC2-3

34 %
```

---

# 27. Haute disponibilité

Si une machine tombe :

```text
EC2-2

↓

DOWN
```

Le Load Balancer continue de distribuer les requêtes vers les autres instances.

---

# 28. Docker

Docker permet d'emballer l'application avec toutes ses dépendances.

Contenu typique :

```text
Python

FastAPI

XGBoost

Requirements

Model
```

Le même conteneur peut être exécuté dans différents environnements.

---

# 29. Pourquoi Docker ?

Réponse d'entretien :

> Docker ensures a consistent runtime environment, making deployments more reliable and reducing "it works on my machine" issues.

---

# 30. Pipeline ML

Le cycle complet peut ressembler à ceci :

```text
Transactions

↓

S3

↓

Data Validation

↓

Feature Engineering

↓

Training

↓

Model Evaluation

↓

Model Registry

↓

Deployment

↓

Monitoring
```

---

# 31. CI/CD

Lorsqu'un nouveau modèle est validé :

```text
Git

↓

Tests

↓

Build

↓

Deploy

↓

Production
```

Le pipeline automatise les étapes de déploiement.

---

# 32. Versionnement

Toujours conserver :

```text
Model v1

Model v2

Model v3
```

Ainsi, un retour arrière est possible si un problème survient.

---

# 33. Question fréquente

> How would you deploy a new model safely?

Bonne réponse :

1. validation hors ligne ;
2. shadow deployment ;
3. canary deployment ;
4. surveillance des métriques ;
5. déploiement progressif.

---

# 34. Monitoring du modèle

Il ne suffit pas de surveiller les serveurs.

Il faut aussi surveiller :

```text
Prediction distribution

Feature drift

Label delay

False positives

False negatives
```

---

# 35. Exemple de drift

Hier :

```text
Average fraud score = 0.12
```

Aujourd'hui :

```text
Average fraud score = 0.55
```

Cela peut indiquer :

* une évolution du comportement des fraudeurs ;
* un changement dans les données ;
* un bug.

---

# 36. Réentraînement

Le modèle n'est pas entraîné une seule fois.

Cycle :

```text
New Data

↓

Training

↓

Validation

↓

Deploy

↓

Monitoring

↓

Retraining
```

---

# 37. Architecture complète (vue simplifiée)

```text
                    Client
                       │
                       ▼
                API Gateway
                       │
                       ▼
               Load Balancer
                       │
          ┌────────────┴────────────┐
          ▼                         ▼
      FastAPI                    FastAPI
      (EC2)                      (EC2)
          │                         │
          └────────────┬────────────┘
                       ▼
                 Feature Service
             ┌─────────┼─────────┐
             ▼         ▼         ▼
          Redis       RDS        S3
             │                    │
             └─────────┬──────────┘
                       ▼
                  ML Model
                       │
                       ▼
                 Fraud Score
                       │
                       ▼
                   Response
```

---

# 38. Questions d'entretien AWS

### Q1

**What is EC2?**

> EC2 is a virtual server used to run applications such as machine learning APIs or training jobs.

---

### Q2

**What is S3 used for?**

> S3 stores datasets, trained models, logs, backups and other artifacts.

---

### Q3

**RDS vs Redshift?**

> RDS is designed for transactional workloads, while Redshift is a data warehouse optimized for large-scale analytics.

---

### Q4

**Why Redis?**

> Redis provides extremely fast in-memory access, making it useful for caching frequently accessed features or metadata and reducing API latency.

---

### Q5

**Why Load Balancer?**

> It distributes traffic across multiple instances, improving scalability and availability.

---

### Q6

**How do you monitor a deployed model?**

> I would monitor infrastructure metrics, latency, prediction distributions, data quality, feature drift, calibration and business KPIs such as false-positive and false-negative rates.

---

### Q7

**What happens if an EC2 instance crashes?**

> If the service is behind a load balancer with multiple healthy instances, traffic is redirected automatically. Auto Scaling can also replace failed instances.

---

### Q8

**How would you reduce API latency?**

> I would profile the pipeline first, then optimize feature retrieval, use caching where appropriate, simplify expensive computations if needed, and ensure efficient model serving.

---

# 39. Cas pratique

Le recruteur vous demande :

> **Design a real-time fraud detection system.**

Vous pouvez répondre avec cette architecture :

```text
Incoming Transaction
        │
        ▼
REST API
        │
        ▼
Input Validation
        │
        ▼
Feature Retrieval
        │
        ▼
Redis Cache
        │
        ▼
PostgreSQL (si nécessaire)
        │
        ▼
Feature Engineering
        │
        ▼
XGBoost Model
        │
        ▼
Fraud Score
        │
        ▼
Business Rules
        │
        ▼
Accept / Review / Reject
        │
        ▼
Logging & Monitoring
```

Puis conclure :

> *"The system should be scalable, fault tolerant, monitored continuously, and designed to avoid data leakage while meeting strict latency requirements."*

Cette réponse montre que vous comprenez non seulement le Machine Learning, mais aussi son intégration dans une architecture de production, ce qui correspond exactement au profil recherché par SentiLink.

---

# 🚀 Prochain module : MODULE 22 — Pipeline complet de Data Science (de l'acquisition des données au déploiement)

Nous réaliserons un **projet complet de niveau entretien** en reproduisant le travail d'un Data Scientist chez SentiLink :

* ingestion des données ;
* nettoyage et validation ;
* feature engineering avancé ;
* entraînement d'un modèle XGBoost ;
* optimisation des hyperparamètres ;
* interprétation avec SHAP ;
* déploiement sous forme d'API ;
* monitoring et amélioration continue.
