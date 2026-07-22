Nous arrivons maintenant au **chapitre le plus proche du travail quotidien d'un Data Scientist.

Jusqu'à présent, nous avons construit des modèles.

Mais dans une entreprise de détection de fraude, **un modèle performant qui répond en 5 secondes est considéré comme inutilisable**.

Pourquoi ?

Parce qu'une transaction par carte bancaire est généralement acceptée ou refusée en **quelques dizaines de millisecondes**.

Le véritable défi n'est donc pas seulement d'obtenir un bon modèle, mais de construire **un système de Machine Learning temps réel**.

---

# MODULE 4 – Chapitre 7 : Détection de fraude en temps réel (Real-Time Fraud Detection)

## Objectifs

À la fin de ce chapitre, vous saurez :

* concevoir une architecture temps réel ;
* comprendre le chemin complet d'une transaction ;
* utiliser un Feature Store ;
* déployer un modèle derrière une API ;
* monitorer un modèle en production ;
* répondre aux questions d'architecture en entretien.

---

# 1. Le pipeline complet

Lorsqu'un utilisateur effectue un paiement, voici ce qui se passe.

```text
Client
   │
   ▼
API Gateway
   │
   ▼
Fraud Scoring API
   │
   ├────────► Feature Store
   │
   ├────────► Modèle ML
   │
   ▼
Score de fraude
   │
   ▼
Décision
(Accepter / Rejeter / Revue manuelle)
```

Tout ce processus doit souvent être exécuté en moins de **100 ms**, parfois **50 ms**.

---

# Étape 1 : Réception de la transaction

Exemple

```json
{
  "customer_id": 10025,
  "amount": 980,
  "country": "US",
  "device_id": "A125",
  "merchant": "Amazon",
  "timestamp": "2026-07-22T10:02:12"
}
```

Le système reçoit ces informations via une API REST ou gRPC.

---

# Étape 2 : Récupération des features

Le modèle n'utilise pas uniquement les données reçues.

Il va également récupérer :

* nombre de transactions des dernières 24 heures ;
* montant moyen ;
* ancienneté du compte ;
* nombre d'appareils connus ;
* adresse IP habituelle ;
* fréquence des changements de pays ;
* score de risque historique.

Ces informations proviennent du **Feature Store**.

---

# 2. Qu'est-ce qu'un Feature Store ?

Le Feature Store est une base spécialisée qui centralise les variables utilisées par les modèles.

Exemple

| customer_id | avg_amount_30d | nb_tx_24h | account_age | risk_score |
| ----------- | -------------: | --------: | ----------: | ---------: |
| 10025       |          112 € |         8 |   520 jours |       0.04 |

Au lieu de recalculer ces variables à chaque transaction, elles sont déjà prêtes.

---

## Pourquoi est-ce important ?

Sans Feature Store :

Chaque requête devrait recalculer toutes les statistiques.

Cela prendrait plusieurs secondes.

Avec un Feature Store :

Les variables sont disponibles immédiatement.

---

# Question d'entretien

Pourquoi utiliser un Feature Store ?

### Réponse

Pour garantir que les mêmes features sont utilisées pendant l'entraînement et en production, tout en réduisant le temps de calcul lors des prédictions.

---

# 3. Le modèle de scoring

Le modèle reçoit les features.

Exemple

```text
Montant = 980

Moyenne historique = 120

Nouvel appareil = Oui

Pays inhabituel = Oui

Compte récent = Non
```

Le modèle calcule :

```text
P(Fraude) = 0.96
```

---

# 4. La prise de décision

Le score seul ne suffit pas.

Exemple

```text
Score < 0.20

↓

Accepter
```

---

```text
0.20 ≤ Score < 0.80

↓

Revue manuelle
```

---

```text
Score ≥ 0.80

↓

Refuser
```

Cette logique est souvent configurable par les équipes métier.

---

# 5. Pourquoi ne pas utiliser uniquement le Machine Learning ?

Parce que certaines règles sont évidentes.

Exemple

```text
Carte signalée volée
```

↓

Refus immédiat.

Aucun modèle n'est nécessaire.

---

En pratique :

```text
Règles métier

+

Machine Learning

+

Détection d'anomalies
```

---

# 6. Architecture typique

```text
Client

↓

API Gateway

↓

Load Balancer

↓

Fraud API

↓

Redis

↓

Feature Store

↓

XGBoost

↓

PostgreSQL

↓

Logs
```

---

# Rôle de chaque composant

## API Gateway

Reçoit les requêtes.

---

## Load Balancer

Répartit les requêtes entre plusieurs serveurs.

---

## Redis

Cache les features les plus utilisées.

Exemple

```text
customer_id=10025
```

déjà présent en mémoire.

Réponse :

quelques millisecondes.

---

## PostgreSQL

Historique.

---

## XGBoost

Calcul du score.

---

# Question d'entretien

Pourquoi utiliser Redis ?

### Réponse

Pour réduire la latence en stockant en mémoire les données fréquemment utilisées.

---

# 7. Temps de réponse

Objectif typique

```text
<100 ms
```

Répartition possible :

API

```text
10 ms
```

---

Lecture Feature Store

```text
20 ms
```

---

Inférence XGBoost

```text
15 ms
```

---

Décision

```text
5 ms
```

---

Total

```text
50 ms
```

---

# 8. Que se passe-t-il si le modèle est indisponible ?

Très bonne question d'entretien.

Le système doit continuer à fonctionner.

Solutions :

* règles métier de secours ;
* dernier modèle stable ;
* mode dégradé ;
* cache des prédictions récentes.

---

# Réponse attendue

Un système critique ne doit jamais dépendre d'un unique composant.

---

# 9. Monitoring

Après le déploiement,

le travail ne s'arrête pas.

Il faut surveiller :

* Recall
* Precision
* Latence
* Nombre de requêtes
* Taux d'erreur
* Distribution des variables
* Data Drift
* Concept Drift

---

# Exemple

Hier

Recall

```text
94 %
```

Aujourd'hui

```text
72 %
```

↓

Alerte.

---

# 10. Logging

Chaque décision est enregistrée.

Exemple

```text
Transaction

↓

Features

↓

Score

↓

Version du modèle

↓

Décision
```

Pourquoi ?

Pour pouvoir expliquer une décision plusieurs mois plus tard.

---

# 11. Déploiement

Les entreprises ne remplacent pas brutalement un modèle.

---

## Blue/Green Deployment

```text
Ancien modèle

↓

Nouveau modèle

↓

Bascule complète
```

---

## Canary Deployment

Seulement :

```text
5 %
```

des utilisateurs utilisent le nouveau modèle.

Si tout fonctionne,

on augmente progressivement.

---

## Shadow Deployment

Très populaire.

Le nouveau modèle calcule ses prédictions,

mais elles ne sont **pas utilisées**.

On compare :

Ancien modèle

↓

Nouveau modèle

sans risque.

---

# Question

Pourquoi utiliser un Shadow Deployment ?

### Réponse

Pour comparer les performances d'un nouveau modèle en conditions réelles sans impacter les décisions prises pour les utilisateurs.

---

# Cas pratique SentiLink

Une banque vous demande :

> Nous devons répondre en moins de 80 ms.

Que faites-vous ?

---

## Réponse idéale

* Utiliser un modèle rapide (par exemple XGBoost ou LightGBM).
* Pré-calculer les features dans un Feature Store.
* Mettre en cache les données fréquemment utilisées avec Redis.
* Déployer plusieurs instances de l'API derrière un Load Balancer.
* Éviter les traitements coûteux pendant la requête.
* Monitorer en continu la latence et le taux d'erreur.

---

# Questions d'entretien

### Pourquoi utiliser un Feature Store ?

Pour réutiliser des variables cohérentes entre l'entraînement et la production et réduire les temps de calcul.

---

### Pourquoi Redis ?

Pour diminuer la latence grâce à un accès mémoire très rapide.

---

### Pourquoi monitorer un modèle ?

Parce que les données évoluent dans le temps et que les performances peuvent se dégrader.

---

### Que faire si le modèle tombe en panne ?

Basculer vers un mode de secours : règles métier, dernier modèle stable ou autre mécanisme de repli.

---

### Quelle différence entre Batch et Temps Réel ?

| Batch                                            | Temps réel                              |
| ------------------------------------------------ | --------------------------------------- |
| Traitement périodique (minutes, heures ou jours) | Traitement à chaque événement           |
| Calcul hors ligne                                | Calcul immédiatement lors de la requête |
| Exemple : rapport quotidien                      | Exemple : autorisation d'un paiement    |

---

# Cas d'entretien (niveau Senior)

Le recruteur vous demande :

> « Votre modèle atteint 99 % de Recall, mais son temps d'inférence est de 450 ms. Que faites-vous ? »

### Réponse attendue

Je chercherais d'abord à comprendre où se situe la latence (calcul des features, réseau, inférence, base de données).

Ensuite, plusieurs pistes sont possibles :

* optimiser ou simplifier le modèle si nécessaire ;
* pré-calculer les features coûteuses ;
* utiliser un cache (Redis) ;
* optimiser l'infrastructure (parallélisme, autoscaling) ;
* éventuellement accepter un modèle légèrement moins performant si le gain de latence améliore les performances globales du système.

En production, **un modèle très précis mais trop lent peut être moins utile qu'un modèle légèrement moins performant mais capable de répondre dans les délais imposés par le métier**.

---

# Ce qui nous attend ensuite

Le prochain chapitre traite d'un sujet qui distingue les Data Scientists expérimentés :

## Data Drift, Concept Drift et Monitoring des modèles

Nous verrons :

* pourquoi un modèle performant aujourd'hui peut devenir mauvais dans six mois ;
* comment détecter automatiquement une dérive des données ;
* quand déclencher un réentraînement ;
* les métriques de surveillance utilisées en production ;
* les outils et bonnes pratiques de MLOps pour maintenir un système de détection de fraude fiable dans le temps.

C'est un thème très fréquent dans les entretiens de niveau intermédiaire et senior, car il montre que vous comprenez le **cycle de vie complet** d'un modèle de Machine Learning.
