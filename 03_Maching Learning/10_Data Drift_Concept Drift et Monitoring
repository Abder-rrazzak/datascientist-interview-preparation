Nous abordons maintenant **l'un des sujets les plus appréciés dans les entretiens de Data Scientist Senior**.

Beaucoup de candidats pensent que le travail s'arrête après le déploiement du modèle.

En réalité, **le déploiement n'est que le début**.

Chez des entreprises comme **SentiLink**, **Stripe**, **PayPal**, **Amazon**, **Google** ou **Netflix**, une grande partie du travail consiste à **surveiller les modèles en production**.

---

# MODULE 4 – Chapitre 8 : Data Drift, Concept Drift et Monitoring

## Objectifs

À la fin de ce chapitre, vous serez capable de :

* comprendre pourquoi un modèle se dégrade ;
* distinguer Data Drift et Concept Drift ;
* choisir les bonnes métriques de monitoring ;
* concevoir une stratégie de réentraînement ;
* répondre aux questions MLOps en entretien.

---

# 1. Pourquoi un modèle se dégrade-t-il ?

Imaginons :

Vous entraînez un modèle en janvier.

En janvier :

* fraude = 0,4 %
* pays à risque = A
* navigateur principal = Chrome

Six mois plus tard :

Les fraudeurs changent complètement de stratégie.

Le modèle continue d'utiliser les anciennes règles.

Les performances chutent.

Ce phénomène est appelé **Model Drift**.

---

# 2. Data Drift

Définition :

Les **données d'entrée changent**, mais la relation entre les variables et la cible reste globalement la même.

Exemple :

Entraînement :

| Variable | Valeur moyenne |
| -------- | -------------: |
| Montant  |          120 € |

Production :

| Variable | Valeur moyenne |
| -------- | -------------: |
| Montant  |          340 € |

Le modèle voit désormais des montants très différents.

---

## Autres exemples

Avant

```text
90 % Chrome
```

Aujourd'hui

```text
55 % Chrome

35 % Safari

10 % Edge
```

Ou encore :

Le pays dominant change.

Les heures d'activité évoluent.

Le type d'appareil change.

Toutes ces situations correspondent à du **Data Drift**.

---

# Question d'entretien

Qu'est-ce que le Data Drift ?

### Réponse

Le Data Drift correspond à une modification de la distribution des variables d'entrée entre l'entraînement et la production.

---

# 3. Concept Drift

Ici, les données peuvent rester similaires.

Ce qui change est **la relation entre les variables et la fraude**.

Exemple

Avant :

Un nouvel appareil

↓

Très risqué.

Aujourd'hui :

Les fraudeurs utilisent désormais des appareils déjà connus.

Le modèle continue pourtant à attribuer un risque élevé aux nouveaux appareils.

La règle apprise n'est plus valable.

---

## Exemple concret

2025

```text
Montant élevé

↓

Fraude
```

2026

Les fraudeurs effectuent :

* beaucoup de petites transactions.

Le comportement a changé.

Le modèle est dépassé.

---

# Différence entre Data Drift et Concept Drift

| Data Drift                                  | Concept Drift                                      |
| ------------------------------------------- | -------------------------------------------------- |
| Les variables changent                      | La relation entre les variables et la cible change |
| Le modèle reçoit de nouvelles distributions | Le comportement sous-jacent évolue                 |
| Souvent détectable rapidement               | Plus difficile à détecter                          |

---

# 4. Comment détecter un Data Drift ?

Une méthode simple consiste à comparer les distributions.

Exemple :

Montant moyen

Entraînement :

```text
110 €
```

Production :

```text
420 €
```

Écart important.

Il faut investiguer.

---

## Tests statistiques

Parmi les plus utilisés :

* Kolmogorov–Smirnov (KS Test)
* Chi² (variables catégorielles)
* Jensen-Shannon Distance
* Population Stability Index (PSI)

---

# 5. Population Stability Index (PSI)

Très fréquent en banque.

Interprétation classique :

| PSI        | Interprétation  |
| ---------- | --------------- |
| < 0.1      | Stable          |
| 0.1 – 0.25 | Drift modéré    |
| > 0.25     | Drift important |

> Les seuils de PSI sont des règles empiriques largement utilisées, mais peuvent varier selon les entreprises.

---

# Question

Pourquoi surveiller le PSI ?

### Réponse

Pour détecter automatiquement si la distribution d'une variable s'éloigne significativement de celle observée pendant l'entraînement.

---

# 6. Monitoring des performances

Il faut suivre régulièrement :

* Recall
* Precision
* F1-score
* ROC-AUC
* PR-AUC
* Latence
* Temps d'inférence
* Taux d'erreur
* Volume des requêtes

---

## Exemple

Semaine 1

Recall

```text
94 %
```

---

Semaine 6

Recall

```text
76 %
```

↓

Investigation nécessaire.

---

# 7. Monitoring des Features

Très important.

Exemple

Feature

```text
account_age
```

Entraînement

```text
300 jours
```

Production

```text
25 jours
```

↓

Drift.

Même si le modèle fonctionne encore,

cela mérite une analyse.

---

# 8. Monitoring des prédictions

Il faut également surveiller :

Distribution des scores.

Exemple

Avant

```text
Fraude moyenne

3 %
```

Aujourd'hui

```text
18 %
```

Deux possibilités :

* les fraudeurs sont plus actifs ;
* le modèle est dégradé.

Il faut analyser.

---

# 9. Quand réentraîner un modèle ?

Il n'existe pas de règle universelle.

Plusieurs stratégies existent.

---

## A. Calendrier fixe

Tous les mois.

Tous les trimestres.

Simple,

mais parfois inutile.

---

## B. Déclenchement automatique

Le modèle est réentraîné si :

* Recall ↓
* PSI ↑
* Data Drift détecté
* Concept Drift suspecté

C'est souvent la meilleure approche.

---

## C. Réentraînement continu

Certaines entreprises mettent à jour leurs modèles très fréquemment.

Cela demande cependant des validations rigoureuses avant mise en production.

---

# Question

Quand faut-il réentraîner un modèle ?

### Réponse

Lorsque les performances se dégradent de manière significative ou qu'une dérive importante des données ou du comportement est détectée.

---

# 10. Data Leakage

Sujet incontournable.

Définition

Le modèle apprend des informations qu'il ne devrait pas connaître au moment de la prédiction.

---

## Exemple

Vous utilisez :

```text
chargeback
```

pour prédire une fraude.

Mais un chargeback survient plusieurs jours après la transaction.

Cette variable n'est donc pas disponible lors du scoring en temps réel.

Le modèle paraît excellent pendant l'entraînement,

mais il sera inutilisable en production.

---

# Question d'entretien

Qu'est-ce que le Data Leakage ?

### Réponse

Le Data Leakage survient lorsqu'une information indisponible au moment de la prédiction est utilisée pendant l'entraînement, ce qui conduit à une estimation artificiellement optimiste des performances.

---

# 11. Monitoring complet

Un système de production surveille simultanément :

```text
Latence

↓

Drift

↓

Recall

↓

Precision

↓

Volume

↓

Erreurs API

↓

Disponibilité

↓

Logs
```

Tout est instrumenté.

---

# Cas pratique SentiLink

Le modèle obtient :

Mois 1

Recall

```text
94 %
```

---

Mois 4

Recall

```text
72 %
```

PSI

```text
0.41
```

Question

Que faites-vous ?

---

## Réponse idéale

1. Vérifier quelles variables présentent un drift important.
2. Déterminer s'il s'agit d'un Data Drift ou d'un Concept Drift.
3. Contrôler la qualité des données et l'absence de problème technique.
4. Réentraîner le modèle avec des données récentes si nécessaire.
5. Valider le nouveau modèle sur un jeu de test indépendant.
6. Déployer progressivement (Canary ou Shadow Deployment).
7. Continuer le monitoring après le déploiement.

---

# Questions d'entretien

### Quelle différence entre Data Drift et Concept Drift ?

* **Data Drift** : la distribution des variables change.
* **Concept Drift** : la relation entre les variables et la cible change.

---

### Pourquoi surveiller les features ?

Parce qu'une évolution importante de leur distribution peut entraîner une baisse des performances du modèle.

---

### Pourquoi monitorer les prédictions ?

Pour détecter rapidement un changement de comportement du modèle ou une évolution du contexte métier.

---

### Pourquoi un modèle performant aujourd'hui peut-il devenir mauvais ?

Parce que les comportements des utilisateurs, les fraudeurs, les produits et les données évoluent avec le temps.

---

### Comment éviter le Data Leakage ?

* N'utiliser que les informations réellement disponibles au moment de la prédiction.
* Reproduire le pipeline de production lors de l'entraînement.
* Être particulièrement vigilant avec les variables créées après l'événement à prédire.

---

# Cas d'entretien (niveau Senior)

**Question :**

> « Votre modèle est toujours à 98 % de ROC-AUC, mais le nombre de plaintes des clients augmente fortement. Que faites-vous ? »

### Réponse attendue

Je ne me limite pas à une seule métrique.

Je vérifie notamment :

* la matrice de confusion ;
* l'évolution des faux positifs ;
* la Precision et le Recall ;
* la distribution des scores ;
* les segments de clientèle affectés ;
* la présence d'un Data Drift ou d'un Concept Drift ;
* les changements récents dans les règles métier.

Il est possible que le modèle conserve une bonne ROC-AUC globale tout en générant davantage de faux positifs pour certains segments, ce qui dégrade l'expérience client.

---

# Architecture MLOps simplifiée

```text
Collecte des données
          │
          ▼
Validation qualité des données
          │
          ▼
Feature Store
          │
          ▼
Entraînement du modèle
          │
          ▼
Validation
          │
          ▼
Registry de modèles
          │
          ▼
Déploiement (Canary / Shadow)
          │
          ▼
API de prédiction
          │
          ▼
Monitoring (drift, performances, latence)
          │
          ▼
Alerte ou réentraînement
```

---

# Ce qui vient ensuite

Le prochain chapitre sera une **étude de cas complète inspirée du fonctionnement d'une entreprise comme SentiLink**.

Vous réaliserez le travail d'un Data Scientist de bout en bout :

* réception de données brutes ;
* exploration et nettoyage ;
* feature engineering ;
* création du pipeline ;
* entraînement de plusieurs modèles ;
* gestion du déséquilibre des classes ;
* optimisation des hyperparamètres ;
* interprétation avec SHAP ;
* déploiement sur AWS ;
* API de scoring en temps réel ;
* monitoring et gestion du drift.

C'est exactement le type de raisonnement que les recruteurs attendent lorsqu'ils demandent :

> **« Décrivez comment vous construiriez un système complet de détection de fraude en production. »**
