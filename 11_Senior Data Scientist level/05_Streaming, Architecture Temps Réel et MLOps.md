Nous arrivons au **niveau Expert+**, qui correspond aux connaissances attendues des Data Scientists travaillant sur des systèmes de décision **temps réel**.

> **Pourquoi ce sujet est-il fondamental chez SentiLink ?**

Parce que lorsqu'un client fait une demande de prêt ou ouvre un compte bancaire, la réponse doit être fournie en **quelques centaines de millisecondes**.

Le modèle ne peut pas prendre 10 secondes.

---

# MODULE 12 — Streaming, Architecture Temps Réel et MLOps

## Partie 1 – Le problème

Imaginez un utilisateur qui soumet une demande de crédit.

```text
Nom : John Smith
SSN : XXX-XX-1234
Adresse : ...
```

La banque appelle immédiatement l'API de SentiLink.

Le temps disponible est généralement inférieur à **300 ms**.

Dans ce délai, il faut :

* récupérer les données ;
* construire les features ;
* interroger le modèle ;
* retourner un score de risque.

---

# Pourquoi le batch ne convient pas ?

En mode batch :

```text
Transactions
        │
        ▼
Traitement de nuit
        │
        ▼
Modèle
```

Ce mode est parfait pour les rapports ou les analyses.

Mais pas pour une décision instantanée.

---

# Streaming

En streaming :

```text
Nouvelle transaction
        │
        ▼
Kafka
        │
        ▼
Feature Service
        │
        ▼
Modèle
        │
        ▼
Réponse
```

Chaque événement est traité dès son arrivée.

---

# Apache Kafka

Kafka est une plateforme de diffusion d'événements (*event streaming*).

Imaginez une file d'attente très rapide.

```text
Transaction 1
Transaction 2
Transaction 3
Transaction 4
...
```

Les événements sont publiés dans un **Topic**.

---

## Les composants

### Producer

Envoie les messages.

Exemple :

L'application bancaire.

---

### Topic

Canal de communication.

Exemple :

```text
transactions
```

---

### Consumer

Lit les messages.

Exemple :

Le service de scoring.

---

# Pourquoi Kafka ?

Parce qu'il est :

* distribué ;
* tolérant aux pannes ;
* très rapide ;
* capable de gérer des millions d'événements.

---

# Exemple d'architecture

```text
Client
   │
   ▼
API Gateway
   │
   ▼
Kafka
   │
   ▼
Fraud Scoring Service
   │
   ▼
Feature Store
   │
   ▼
XGBoost
   │
   ▼
Réponse
```

---

# Apache Flink

Kafka transporte les événements.

Flink les traite en continu.

Exemple :

Compter le nombre de transactions d'un utilisateur dans les 10 dernières minutes.

Avec un système classique, il faudrait interroger la base de données.

Avec Flink, cette information est maintenue en mémoire.

---

# Fenêtres temporelles (Windows)

Exemple :

```text
Dernières 10 minutes
```

ou

```text
Dernière heure
```

Flink calcule automatiquement :

* le nombre de transactions ;
* le montant moyen ;
* le montant maximal.

Ces informations deviennent immédiatement disponibles pour le modèle.

---

# Pourquoi est-ce important ?

Le modèle peut recevoir des variables telles que :

```text
transactions_last_10_minutes = 17
average_amount_last_hour = 125 €
```

Sans avoir à recalculer ces statistiques à chaque requête.

---

# Feature Store

L'un des composants les plus importants en production.

## Problème

Pendant l'entraînement :

```python
average_amount = mean(last_30_days)
```

En production :

Un développeur calcule :

```python
average_amount = mean(last_7_days)
```

Le modèle reçoit une feature différente.

Les performances chutent.

---

## Solution

Un Feature Store.

Il garantit que :

* les mêmes calculs sont utilisés pour l'entraînement ;
* les mêmes calculs sont utilisés en production.

On évite ainsi le **Training-Serving Skew**.

---

# Qu'est-ce que le Training-Serving Skew ?

C'est l'écart entre la manière dont les features sont calculées pendant l'entraînement et la manière dont elles sont calculées lors des prédictions en production.

C'est une cause fréquente de baisse de performance.

---

# MLflow

MLflow est un outil de gestion du cycle de vie des modèles.

Il permet notamment de :

* enregistrer les expériences ;
* suivre les hyperparamètres ;
* comparer les métriques ;
* stocker les modèles.

---

# Exemple

Vous entraînez :

```text
XGBoost v1
Recall = 91 %
```

Puis :

```text
XGBoost v2
Recall = 94 %
```

MLflow conserve l'historique et facilite les comparaisons.

---

# Model Registry

Une entreprise ne déploie pas directement un modèle entraîné.

Les versions sont gérées.

Par exemple :

```text
Version 1 → Production
Version 2 → Validation
Version 3 → Développement
```

Chaque modèle passe par des étapes de validation avant d'être promu.

---

# Déploiement

Le plus simple :

```text
Docker
        │
        ▼
FastAPI
```

Mais en entreprise :

```text
Docker
        │
        ▼
Kubernetes ou ECS
```

avec plusieurs instances.

---

# A/B Testing

On veut comparer deux modèles.

```text
90 % des utilisateurs
        │
        ▼
Modèle A
```

```text
10 % des utilisateurs
        │
        ▼
Modèle B
```

On compare :

* Recall ;
* Precision ;
* temps de réponse ;
* impact métier.

---

# Canary Deployment

Déploiement progressif.

Exemple :

```text
1 %
```

↓

```text
5 %
```

↓

```text
20 %
```

↓

```text
100 %
```

Si un problème est détecté, on revient rapidement à la version précédente.

---

# Shadow Deployment

Très utilisé en FinTech.

Le nouveau modèle reçoit les mêmes requêtes que le modèle en production, mais ses prédictions ne sont **pas utilisées** pour prendre les décisions.

On compare les résultats avant tout déploiement.

---

# Monitoring

Que faut-il surveiller ?

### Infrastructure

* Temps de réponse
* CPU
* Mémoire
* Erreurs HTTP

### Données

* Valeurs manquantes
* Distribution des variables
* Data Drift

### Modèle

* Recall
* Precision
* Faux positifs
* Faux négatifs
* Calibration

---

# Pipeline complet

```text
Nouvelle transaction
        │
        ▼
Kafka
        │
        ▼
Flink
        │
        ▼
Feature Store
        │
        ▼
FastAPI
        │
        ▼
XGBoost
        │
        ▼
Décision
        │
        ▼
Logs
        │
        ▼
MLflow
        │
        ▼
Monitoring
        │
        ▼
Réentraînement
```

---

# Questions d'entretien

## Pourquoi utiliser Kafka ?

Parce qu'il permet de traiter des événements en continu avec une forte capacité de montée en charge et une faible latence.

---

## Pourquoi utiliser Flink ?

Parce qu'il calcule des agrégations temps réel (fenêtres glissantes, compteurs, moyennes) sans devoir interroger une base de données à chaque prédiction.

---

## Pourquoi un Feature Store ?

Pour garantir la cohérence des features entre l'entraînement et la production et éviter le *Training-Serving Skew*.

---

## Pourquoi MLflow ?

Pour suivre les expériences, gérer les versions des modèles et faciliter leur déploiement.

---

## Différence entre A/B Testing et Canary Deployment

**A/B Testing**

Objectif : comparer deux modèles pour déterminer lequel obtient les meilleurs résultats.

**Canary Deployment**

Objectif : déployer progressivement un nouveau modèle afin de limiter les risques.

---

## Pourquoi utiliser un Shadow Deployment ?

Pour observer le comportement d'un nouveau modèle sur le trafic réel sans impacter les décisions prises pour les utilisateurs.

---

# Étude de cas SentiLink

**Question :**

> Vous avez développé un nouveau modèle avec un Recall de 96 %, contre 93 % pour le modèle actuel. Le déployez-vous immédiatement ?

### Réponse de niveau Senior

Non. Une amélioration du Recall ne suffit pas. Je vérifierais également la Precision, la PR-AUC, la calibration, la latence, la stabilité sur différentes populations et l'impact métier (coût des faux positifs et des faux négatifs). Je commencerais ensuite par un Shadow Deployment, puis un Canary Deployment avant un déploiement complet si les résultats restent satisfaisants.

---

# Ce qui distingue un Senior Data Scientist

Un candidat expérimenté ne se contente pas de dire :

> « Mon modèle est meilleur. »

Il explique :

* comment il le valide ;
* comment il le déploie sans risque ;
* comment il le surveille ;
* comment il le fait évoluer.

C'est exactement cette vision **end-to-end**, couvrant la donnée, le modèle, l'infrastructure et les contraintes métier, qui est recherchée dans les équipes Data Science de FinTech comme SentiLink.
