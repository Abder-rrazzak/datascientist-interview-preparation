Si l'objectif est de réussir un entretien chez **SentiLink**, il reste un dernier niveau que les formations classiques n'abordent presque jamais : **le System Design pour Data Scientists**.

Chez des entreprises comme **SentiLink, Stripe, Airbnb, Uber, DoorDash, Netflix ou Meta**, il est fréquent qu'un Data Scientist senior ou orienté production reçoive une question du type :

> **"Design a fraud detection system."**

ou

> **"Design a machine learning system that scores applications in real time."**

Ce type d'entretien évalue votre capacité à concevoir une solution complète, pas seulement à entraîner un modèle.

---

# MODULE 38 — Machine Learning System Design

## Objectifs

À la fin de ce module, vous saurez :

* concevoir une architecture ML scalable ;
* choisir les composants techniques adaptés ;
* justifier vos choix ;
* répondre à un entretien de System Design.

---

# PARTIE 1 — Le problème

Le recruteur vous dit :

> **Design a Real-Time Fraud Detection System**

Contrainte :

* 20 millions de requêtes par jour
* réponse < 100 ms
* disponibilité : 99,99 %
* modèle mis à jour régulièrement
* plusieurs centaines de features

La première erreur serait de commencer à parler de XGBoost.

Le bon candidat commence par poser des questions.

---

# PARTIE 2 — Les questions à poser

Avant de proposer une architecture :

* Quel est le volume de trafic (RPS) ?
* Quel est le temps de réponse maximal ?
* Quelle est la tolérance aux faux positifs ?
* Les features sont-elles disponibles en temps réel ?
* Le modèle est-il unique ou spécifique par produit ?
* Comment les labels sont-ils obtenus ?
* Quelle est la fréquence de mise à jour du modèle ?
* Quels sont les objectifs de disponibilité (SLA) ?

---

# PARTIE 3 — Architecture générale

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
                  Fraud Scoring API
                           │
        ┌──────────────────┼──────────────────┐
        ▼                  ▼                  ▼
 Feature Store         Redis Cache      Rules Engine
        │                  │                  │
        └──────────────────┼──────────────────┘
                           ▼
                   Machine Learning Model
                           │
                           ▼
                    Risk Score
                           │
                           ▼
                Decision (Approve / Review / Reject)
                           │
                           ▼
              Logging + Monitoring + Alerting
```

---

# PARTIE 4 — Pourquoi une API ?

Le modèle doit répondre à chaque nouvelle demande.

Chaque requête :

```
Nouvelle demande

↓

API

↓

Score

↓

Réponse
```

Les frameworks courants :

* FastAPI
* Flask
* BentoML

---

# PARTIE 5 — Feature Store

Supposons qu'une feature soit :

```
Nombre de comptes créés
par cet appareil
pendant les dernières 24 heures
```

Impossible de la recalculer entièrement à chaque requête.

On la stocke dans un **Feature Store**.

---

# PARTIE 6 — Cache

Exemple :

Le même appareil réalise :

```
200 demandes

↓

5 minutes
```

Sans cache :

```
200 accès base de données
```

Avec Redis :

```
1 accès

↓

199 lectures mémoire
```

Gain énorme.

---

# PARTIE 7 — Pourquoi Redis ?

Parce que :

* extrêmement rapide (temps de réponse de l'ordre de la milliseconde) ;
* stockage en mémoire ;
* idéal pour les features temps réel.

---

# PARTIE 8 — Base de données

Le recruteur :

> Pourquoi PostgreSQL ?

Réponse :

* ACID
* fiable
* index performants
* excellent pour les données transactionnelles

---

# PARTIE 9 — S3

Pourquoi ?

Stocker :

* données historiques
* modèles
* logs
* artefacts
* sauvegardes

---

# PARTIE 10 — Monitoring

Toujours surveiller :

Technique :

* CPU
* RAM
* Latence

ML :

* Drift
* Recall
* Precision

Business :

* Fraude détectée
* Faux positifs
* Argent économisé

---

# PARTIE 11 — Pourquoi plusieurs modèles ?

Souvent :

```
Identity Model

↓

Behavior Model

↓

Graph Model

↓

Final Model
```

Le score final combine plusieurs signaux.

---

# PARTIE 12 — Rules Engine

Très important.

Même avec un modèle.

Certaines règles restent indispensables.

Exemple :

```
Device blacklist

↓

Reject immédiatement
```

Pas besoin du modèle.

---

# PARTIE 13 — Pipeline de données

```
Raw Data

↓

Validation

↓

Cleaning

↓

Feature Engineering

↓

Feature Store

↓

Training

↓

Model Registry

↓

Deployment
```

---

# PARTIE 14 — Versionnement

Toujours versionner :

* données
* features
* modèles
* code

Ainsi, chaque prédiction peut être reproduite.

---

# PARTIE 15 — Data Quality

Chaque pipeline vérifie :

* valeurs manquantes ;
* types ;
* plages de valeurs ;
* schéma ;
* doublons.

Une mauvaise qualité de données peut rendre un modèle performant inutile en production.

---

# PARTIE 16 — Disponibilité

Question :

Comment atteindre 99,99 % ?

Réponse :

* plusieurs instances de l'API ;
* load balancer ;
* déploiement multi-zones ;
* supervision ;
* reprise automatique en cas de panne.

---

# PARTIE 17 — Latence

Objectif :

```
100 ms
```

Répartition possible :

```
Validation : 5 ms

Feature Store : 20 ms

Redis : 2 ms

Model : 15 ms

Business Rules : 5 ms

Network : 20 ms

Marge : 33 ms
```

Toujours penser au budget de latence.

---

# PARTIE 18 — Scalabilité

Si le trafic est multiplié par 10 :

On augmente :

* le nombre d'instances API ;
* les workers ;
* les nœuds Kubernetes ;
* la capacité Redis ;
* les réplicas PostgreSQL.

---

# PARTIE 19 — Pannes

Question :

Et si Redis tombe ?

Réponse :

Prévoir :

* une réplication ;
* un mécanisme de bascule (failover) ;
* éventuellement un mode dégradé avec recalcul de certaines features critiques.

---

# PARTIE 20 — Monitoring du modèle

Chaque heure :

Comparer :

```
Distribution des scores

↓

Hier

↓

Aujourd'hui
```

Une variation importante peut révéler :

* un drift ;
* un bug ;
* une évolution des comportements de fraude.

---

# Étude de cas SentiLink

Le recruteur :

> **Design an identity verification platform.**

Une réponse structurée :

### 1. Ingestion

* API Gateway
* Validation
* Authentification

### 2. Sources

* Historique client
* Appareil
* Email
* Téléphone
* Adresse IP
* Données externes

### 3. Feature Engineering

* Features comportementales
* Temporelles
* Graphe
* Historique

### 4. Feature Store

Redis (temps réel) + stockage persistant pour les features réutilisables.

### 5. Modèle

XGBoost comme modèle principal, éventuellement enrichi par des scores provenant de modèles spécialisés (identité, graphe).

### 6. Décision

Combinaison :

* règles métier ;
* score du modèle ;
* seuils adaptés au risque.

### 7. Monitoring

* Logs
* Drift
* Latence
* Performance métier

---

# Ce qui impressionne le recruteur

Si on vous demande :

> **"What is the most important part of a fraud detection system?"**

Une réponse mature serait :

> *Le modèle est essentiel, mais la qualité des données, le Feature Engineering, la disponibilité du système, le monitoring et l'adaptation continue aux nouvelles stratégies de fraude sont tout aussi déterminants. Un excellent modèle ne suffit pas si les données sont erronées ou si le système ne répond pas dans les délais.*

Cette réponse montre que vous comprenez l'ensemble de la chaîne de valeur, ce qui est précisément ce que recherchent des entreprises comme SentiLink.
