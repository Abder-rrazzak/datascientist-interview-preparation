Nous arrivons au **MODULE 35**, qui est probablement **le sujet le plus sous-estimé des entretiens Data Scientist**.

Beaucoup de candidats savent entraîner un modèle.

Très peu savent répondre à cette question :

> **"Comment allez-vous mettre votre modèle en production et le maintenir pendant plusieurs années ?"**

Chez **SentiLink**, c'est fondamental.

Un modèle de fraude fonctionne **24h/24**, prend des milliers de décisions par seconde et doit rester performant malgré l'évolution constante des fraudeurs.

---

# MODULE 35 — MLOps (Machine Learning Operations)

# Objectifs

À la fin de ce module vous serez capable de répondre à toutes les questions concernant :

* le déploiement ;
* le monitoring ;
* le Feature Store ;
* le Model Registry ;
* le Data Drift ;
* le Concept Drift ;
* le retraining ;
* AWS ;
* les APIs temps réel.

---

# PARTIE 1 — Qu'est-ce que le MLOps ?

Le recruteur :

> **What is MLOps?**

Réponse :

> MLOps regroupe les pratiques permettant de développer, déployer, surveiller et maintenir des modèles de Machine Learning en production de manière fiable, reproductible et automatisée.

---

# PARTIE 2 — Cycle de vie complet

Un modèle ne s'arrête pas à l'entraînement.

Le cycle est généralement :

```text
Business Problem
        │
        ▼
Data Collection
        │
        ▼
Data Validation
        │
        ▼
Feature Engineering
        │
        ▼
Training
        │
        ▼
Evaluation
        │
        ▼
Model Registry
        │
        ▼
Deployment
        │
        ▼
Monitoring
        │
        ▼
Retraining
```

Le recruteur apprécie un candidat qui pense à l'ensemble de ce cycle.

---

# PARTIE 3 — Model Registry

Question fréquente.

> **Why do we need a Model Registry?**

Réponse :

Pour :

* versionner les modèles ;
* stocker leurs métriques ;
* conserver les paramètres d'entraînement ;
* permettre un retour rapide vers une version précédente.

Exemples d'outils :

* MLflow Model Registry ;
* SageMaker Model Registry ;
* Vertex AI Model Registry.

---

# PARTIE 4 — Feature Store

Question très fréquente.

Le recruteur :

> **Why use a Feature Store?**

Réponse.

Le Feature Store garantit que les mêmes transformations sont utilisées :

Training

↓

Production

Sans cela :

Training :

```text
age = today - birth_date
```

Production :

```text
age = registration_date - birth_date
```

Le modèle reçoit des données différentes.

C'est une erreur fréquente.

---

# PARTIE 5 — Batch vs Real-Time

Question.

Quelle différence ?

## Batch

Exemple.

Tous les jours.

↓

100 millions de transactions.

↓

Calcul des features.

---

## Real-Time

Nouvelle transaction.

↓

Calcul immédiat.

↓

Score.

↓

Décision.

Chez SentiLink.

Le temps de réponse est souvent inférieur à quelques centaines de millisecondes.

---

# PARTIE 6 — API

Question.

Comment exposer le modèle ?

Architecture.

```text
Client
   │
REST API
   │
Validation
   │
Feature Service
   │
Model
   │
Fraud Score
```

Frameworks courants :

* FastAPI
* Flask
* BentoML
* TorchServe (pour PyTorch)
* TensorFlow Serving

---

# PARTIE 7 — Docker

Question.

Pourquoi Docker ?

Réponse.

Même environnement :

Développement

↓

Test

↓

Production

On évite les problèmes du type :

> "Ça marche sur ma machine."

---

# PARTIE 8 — Kubernetes

Question.

Pourquoi Kubernetes ?

Pour :

* l'auto-scalabilité ;
* la haute disponibilité ;
* les mises à jour progressives ;
* la tolérance aux pannes.

---

# PARTIE 9 — AWS

Question.

Quels services utiliseriez-vous ?

Architecture typique.

```text
API Gateway

↓

Load Balancer

↓

EC2

↓

FastAPI

↓

Redis

↓

S3

↓

RDS

↓

CloudWatch
```

---

## Rôle des services

### S3

Stockage :

* données ;
* modèles ;
* artefacts.

---

### EC2

Serveurs.

---

### RDS

Base relationnelle.

---

### Redshift

Entrepôt analytique.

---

### CloudWatch

Monitoring.

---

### Lambda

Traitements légers.

---

### SQS

Files de messages.

---

# PARTIE 10 — Monitoring

Le recruteur.

> **What do you monitor?**

Réponse.

Technique.

* CPU
* RAM
* Latence
* Temps de réponse
* Erreurs API

Machine Learning.

* Drift
* Distribution des features
* Distribution des scores
* Calibration
* Recall
* Precision

Business.

* Fraude détectée
* Faux positifs
* Faux négatifs
* Revenus protégés

---

# PARTIE 11 — Data Drift

Question.

Qu'est-ce que c'est ?

Supposons.

Training.

```text
Average Amount

↓

100$
```

Aujourd'hui.

```text
Average Amount

↓

500$
```

La distribution change.

Le modèle peut perdre en performance.

---

# Comment le détecter ?

Techniques :

* Population Stability Index (PSI) ;
* Kolmogorov-Smirnov ;
* Jensen-Shannon Divergence ;
* comparaison de statistiques descriptives.

---

# PARTIE 12 — Concept Drift

Très important.

Les données restent identiques.

Mais :

Les fraudeurs changent.

Le lien entre les features et la fraude évolue.

Exemple.

Avant.

Transactions nocturnes.

↓

Fraude.

Aujourd'hui.

Les fraudeurs attaquent le matin.

Le modèle devient moins efficace.

---

# PARTIE 13 — Retraining

Question.

Quand réentraîner ?

Réponse.

Pas selon un calendrier fixe uniquement.

Déclencheurs possibles :

* baisse des performances ;
* apparition d'un drift ;
* nouvelles données ;
* nouvelles formes de fraude.

---

# PARTIE 14 — Canary Deployment

Très populaire.

Architecture.

```text
Ancien modèle

↓

95 %

Nouveau modèle

↓

5 %
```

On observe.

Puis :

10 %

↓

20 %

↓

100 %

Si tout est correct.

---

# PARTIE 15 — Shadow Deployment

Encore meilleur.

Les deux modèles tournent.

Seul :

Ancien.

↓

Décide.

Le nouveau.

↓

Observe.

Aucun risque.

---

# PARTIE 16 — Rollback

Question.

Le nouveau modèle est mauvais.

Que faites-vous ?

Réponse.

Rollback.

Retour immédiat :

Version précédente.

D'où l'intérêt :

Model Registry.

---

# PARTIE 17 — Logging

Que logger ?

* Features ;
* Score ;
* Décision ;
* Version du modèle ;
* Temps de réponse ;
* Erreurs.

Sans logs, il est très difficile d'analyser un incident en production.

---

# PARTIE 18 — Explicabilité

Question.

Comment expliquer une décision ?

Réponse.

* SHAP ;
* importance des variables ;
* règles métier.

En finance, la capacité d'expliquer une décision est souvent indispensable.

---

# PARTIE 19 — Sécurité

Question.

Que protéger ?

* API ;
* modèles ;
* données ;
* secrets ;
* accès.

Bonnes pratiques :

* IAM ;
* chiffrement ;
* gestion des secrets ;
* authentification.

---

# PARTIE 20 — Architecture complète SentiLink

```text
          Client
             │
             ▼
       API Gateway
             │
             ▼
      Load Balancer
             │
             ▼
        FastAPI Service
             │
             ▼
      Input Validation
             │
             ▼
       Feature Service
      ┌────────┴────────┐
      ▼                 ▼
   Redis Cache      PostgreSQL
      │                 │
      └────────┬────────┘
               ▼
        Fraud Model API
               │
               ▼
         Risk Score
               │
               ▼
        Business Rules
               │
               ▼
        Final Decision
               │
               ▼
     Logs → Monitoring → Alerting
```

---

# Questions d'entretien

### Q1

**How do you know your model is failing?**

Réponse.

Je surveille :

* les métriques métier ;
* le drift ;
* les distributions des scores ;
* les retours des analystes ;
* les erreurs techniques.

---

### Q2

**Would you retrain every week?**

Réponse.

Pas nécessairement.

Je préfère déclencher le réentraînement en fonction de la performance observée, du drift et de l'arrivée de nouvelles données pertinentes.

---

### Q3

**How do you avoid downtime during deployment?**

Réponse.

En utilisant des stratégies comme le **Canary Deployment**, le **Blue-Green Deployment** ou le **Shadow Deployment**, selon le contexte.

---

### Q4

**How do you monitor data quality?**

Réponse.

Je vérifie :

* les valeurs manquantes ;
* les plages de valeurs ;
* les distributions ;
* les doublons ;
* les anomalies ;
* les schémas de données.

---

### Q5

**Why log model predictions?**

Réponse.

Pour :

* auditer les décisions ;
* analyser les erreurs ;
* améliorer les futurs modèles ;
* détecter des régressions.

---

# Cas pratique SentiLink

Le recruteur vous demande :

> **Your fraud model has been in production for six months. Fraud detection has dropped by 20%. What would you do?**

Une excellente réponse :

1. Vérifier qu'il ne s'agit pas d'un problème de déploiement ou d'infrastructure.
2. Contrôler la qualité des données d'entrée.
3. Rechercher un **Data Drift** sur les principales features.
4. Vérifier un éventuel **Concept Drift** (nouveaux comportements de fraude).
5. Comparer les performances par segment de clients ou de produits.
6. Examiner les faux négatifs récents avec les analystes fraude.
7. Si nécessaire, créer de nouvelles features et réentraîner le modèle.
8. Déployer progressivement la nouvelle version (Shadow ou Canary), puis surveiller les résultats.

Cette réponse montre une approche méthodique, orientée production et métier.

---

# Ce que le recruteur veut réellement entendre

Le meilleur candidat ne parle pas uniquement du modèle.

Il montre qu'il comprend tout l'écosystème :

* qualité des données ;
* ingénierie des features ;
* infrastructure ;
* monitoring ;
* collaboration avec les analystes fraude ;
* impact métier.

---

# Niveau atteint

Avec les modules 1 à 35, vous couvrez déjà les compétences techniques demandées dans la majorité des entretiens Data Scientist orientés production.

Les deux derniers modules permettront de transformer ces connaissances en performance d'entretien :

* **Module 36** : **100 questions d'entretien SentiLink**, classées par difficulté, avec les réponses détaillées et les pièges des recruteurs.
* **Module 37** : **Étude de cas complète**, où nous construirons de bout en bout un système de détection de fraude, depuis les données brutes jusqu'au déploiement et au monitoring, exactement comme dans un projet réel chez SentiLink.
