Nous commençons maintenant le **MODULE 5**, consacré à **AWS, aux APIs et au déploiement**.

> En entretien, il est peu probable qu'on vous demande d'être un ingénieur DevOps, mais on attend de vous que vous sachiez **comment un modèle de Machine Learning est mis en production**.

---

# MODULE 5 — AWS pour le Data Scientist (Niveau Entretien)

## Objectifs

À la fin de ce module, vous serez capable de :

* comprendre les principaux services AWS ;
* expliquer une architecture ML sur AWS ;
* déployer un modèle via une API ;
* répondre aux questions Cloud les plus fréquentes.

---

# Architecture globale

Une architecture typique chez SentiLink, par exemple, peut ressembler à ceci :

```text
                Client / Banque
                      │
                  API Gateway
                      │
                 Load Balancer
                      │
          +-----------+-----------+
          |                       |
      FastAPI #1             FastAPI #2
          │                       │
          +-----------+-----------+
                      │
                Modèle XGBoost
                      │
        +-------------+--------------+
        │                            │
       Redis                    PostgreSQL (RDS)
        │                            │
        +-------------+--------------+
                      │
                     S3
```

---

# Les services AWS à connaître

Il existe des dizaines de services AWS.

Pour un Data Scientist, une dizaine suffisent largement.

---

# 1. Amazon S3

Le service le plus important.

S3 = **Simple Storage Service**

C'est un stockage d'objets.

On y stocke :

* datasets
* modèles ML
* fichiers CSV
* images
* logs
* sauvegardes

---

## Exemple

```text
s3://fraud-data/

├── train.csv

├── test.csv

├── model.pkl

└── logs/
```

---

## Pourquoi S3 ?

Parce qu'il est :

* très fiable ;
* peu coûteux ;
* hautement scalable.

---

## Question d'entretien

Pourquoi utiliser S3 ?

### Réponse

Pour stocker de manière durable les données, les modèles et les artefacts de Machine Learning.

---

# 2. EC2

EC2 = **Elastic Compute Cloud**

Ce sont des machines virtuelles.

On peut y installer :

* Python
* PostgreSQL
* FastAPI
* Docker
* un modèle XGBoost

---

Exemple

```text
EC2

Ubuntu

↓

Python

↓

FastAPI

↓

model.pkl
```

---

Le serveur répond ensuite aux requêtes.

---

# Question

Pourquoi utiliser EC2 ?

### Réponse

Pour exécuter des applications ou des modèles de Machine Learning sur une machine virtuelle configurable.

---

# 3. RDS

RDS = **Relational Database Service**

AWS gère automatiquement :

* PostgreSQL
* MySQL
* MariaDB
* SQL Server

---

Exemple

```text
Fraud API

↓

RDS PostgreSQL
```

---

Le Data Scientist n'a pas besoin d'installer PostgreSQL lui-même.

---

# Pourquoi RDS ?

* sauvegardes automatiques ;
* réplication ;
* haute disponibilité ;
* maintenance simplifiée.

---

# Question

Pourquoi utiliser RDS plutôt qu'installer PostgreSQL sur EC2 ?

### Réponse

Parce que RDS automatise les sauvegardes, les mises à jour, la réplication et la haute disponibilité, ce qui réduit la charge opérationnelle.

---

# 4. Redshift

Redshift est un entrepôt de données (Data Warehouse).

Très utilisé pour :

* l'analytique ;
* les tableaux de bord ;
* les requêtes SQL sur de très gros volumes.

---

Exemple

```text
5 milliards de transactions
```

↓

Redshift

↓

SQL

↓

Power BI

---

Redshift n'est généralement **pas** utilisé pour le scoring temps réel, mais pour l'analyse historique.

---

# Question

Différence entre PostgreSQL et Redshift ?

### Réponse

| PostgreSQL                             | Redshift                   |
| -------------------------------------- | -------------------------- |
| Base transactionnelle (OLTP)           | Entrepôt de données (OLAP) |
| Applications en temps réel             | Analyses massives          |
| Milliers de lignes à quelques millions | Milliards de lignes        |

---

# 5. Lambda

Lambda exécute du code **sans serveur**.

Vous fournissez uniquement une fonction.

AWS se charge du reste.

---

Exemple

```python
def handler(event, context):

    return "Hello"
```

---

Utilisations possibles

Lorsqu'un fichier arrive dans S3 :

↓

Lambda

↓

Prétraitement automatique

---

# Avantages

* pas de serveur à gérer ;
* paiement à l'utilisation ;
* montée en charge automatique.

---

# Question

Quand utiliser Lambda ?

### Réponse

Pour des traitements courts et événementiels, comme le déclenchement automatique d'un prétraitement lorsqu'un fichier est déposé dans S3.

---

# 6. SageMaker

Le service Machine Learning d'AWS.

Il permet :

* entraîner un modèle ;
* effectuer du tuning ;
* déployer une API ;
* monitorer les modèles.

---

Pipeline

```text
Dataset

↓

Training

↓

Model

↓

Endpoint

↓

Prédiction
```

---

# Question

Pourquoi utiliser SageMaker ?

### Réponse

Parce qu'il fournit un environnement intégré pour entraîner, déployer et superviser des modèles de Machine Learning à grande échelle.

---

# 7. IAM

IAM = **Identity and Access Management**

Gestion des permissions.

Exemple

Le Data Scientist :

✅ Lire S3

❌ Supprimer les bases de données

---

Toujours appliquer le **principe du moindre privilège** (*least privilege*).

---

# 8. CloudWatch

Le système de monitoring AWS.

Il permet de surveiller :

* CPU ;
* mémoire (avec configuration adaptée) ;
* erreurs ;
* latence ;
* logs ;
* nombre de requêtes.

---

Exemple

```text
API

↓

CloudWatch

↓

Alerte

↓

Email
```

---

# Question

Pourquoi utiliser CloudWatch ?

### Réponse

Pour surveiller les performances des applications et recevoir des alertes en cas de problème.

---

# 9. ECR

ECR = **Elastic Container Registry**

Il stocke les images Docker.

Exemple

```text
Docker Image

↓

ECR

↓

EC2

↓

Application
```

---

# 10. ECS

ECS = **Elastic Container Service**

Permet d'exécuter des conteneurs Docker.

---

Architecture

```text
Docker

↓

ECR

↓

ECS

↓

API
```

---

# Architecture complète

```text
Utilisateur

↓

API Gateway

↓

Load Balancer

↓

FastAPI (Docker)

↓

XGBoost

↓

Redis

↓

RDS PostgreSQL

↓

S3
```

---

# Questions d'entretien

### Pourquoi stocker un modèle sur S3 ?

Parce que S3 est durable, scalable et permet de partager facilement les artefacts entre les différentes étapes du pipeline.

---

### Pourquoi utiliser EC2 ?

Pour héberger une application, une API ou un modèle nécessitant un contrôle complet de l'environnement.

---

### Pourquoi Redshift ?

Pour analyser efficacement de très grands volumes de données historiques.

---

### Pourquoi Lambda ?

Pour automatiser des traitements déclenchés par des événements, sans gérer d'infrastructure serveur.

---

### Pourquoi SageMaker ?

Pour simplifier l'entraînement, le déploiement et la gestion des modèles de Machine Learning sur AWS.

---

# Cas pratique SentiLink

Le recruteur demande :

> "Décrivez une architecture AWS pour servir un modèle de détection de fraude."

### Réponse idéale

1. Les données historiques sont stockées dans **S3**.
2. Les modèles sont entraînés (sur EC2 ou SageMaker selon les besoins).
3. Le modèle est sauvegardé dans **S3**.
4. Une API **FastAPI** est déployée sur **EC2** ou dans des conteneurs **ECS**.
5. Les features sont récupérées depuis **RDS PostgreSQL** ou un Feature Store.
6. **Redis** est utilisé pour réduire la latence des accès fréquents.
7. **CloudWatch** surveille les performances, les logs et la disponibilité de l'API.
8. Les accès aux ressources AWS sont sécurisés avec **IAM**.

---

# À retenir pour l'entretien

Un Data Scientist n'est pas censé maîtriser tous les détails d'AWS, mais il doit être capable :

* d'expliquer le rôle des principaux services ;
* de comprendre le parcours d'une requête de prédiction ;
* de justifier les choix d'architecture en fonction des besoins métier (latence, scalabilité, sécurité, coût).

---

# Prochain chapitre

Nous passerons à un sujet **très pratique** :

# **Construire une API de Machine Learning avec FastAPI**

Vous apprendrez :

* à créer une API REST en Python ;
* à charger un modèle (`joblib` ou XGBoost) ;
* à recevoir une requête JSON ;
* à renvoyer un score de fraude ;
* à tester l'API avec `curl` et Postman ;
* à expliquer ce fonctionnement clairement en entretien.

C'est l'une des compétences les plus valorisées pour un Data Scientist orienté production.
