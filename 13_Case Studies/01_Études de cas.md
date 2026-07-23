Nous arrivons maintenant au **dernier module**. C'est celui qui fait réellement la différence entre un **bon candidat** et un **candidat recruté**.

En tant que mentor, je peux vous dire une chose :

> **80 % des candidats échouent non pas parce qu'ils ne connaissent pas le Machine Learning, mais parce qu'ils ne savent pas raisonner.**

Chez **SentiLink**, les recruteurs ne cherchent pas uniquement quelqu'un qui connaît `XGBoost`.

Ils cherchent quelqu'un qui sait :

* analyser un problème métier ;
* poser les bonnes questions ;
* proposer une solution réaliste ;
* justifier chaque décision.

C'est exactement ce que nous allons apprendre.

---

# MODULE 13 — Études de cas (Case Studies)

Les études de cas représentent souvent **50 à 70 % de l'entretien technique**.

Le recruteur ne cherche pas LA bonne réponse.

Il cherche votre manière de réfléchir.

---

# Cas n°1 — Explosion des faux positifs

Le recruteur dit :

> Depuis hier, notre système bloque énormément de clients légitimes.
>
> Que faites-vous ?

La plupart des candidats répondent immédiatement :

> "Je vais réentraîner le modèle."

❌ Mauvaise réponse.

---

## Réponse Senior

Je commence par comprendre le problème.

### Étape 1

Vérifier

Les données ont-elles changé ?

Questions :

* nouvelle source ?
* bug ?
* colonnes manquantes ?
* changement de format ?

---

### Étape 2

Comparer

Avant

↓

Après

Les distributions.

Par exemple :

Montant moyen.

Adresse IP.

Pays.

---

### Étape 3

Regarder

La matrice de confusion.

Le problème vient-il :

* des faux positifs ?
* des faux négatifs ?

---

### Étape 4

Le seuil a-t-il changé ?

Par exemple

Avant

```text
0.70
```

Aujourd'hui

```text
0.45
```

Le problème est peut-être simplement le seuil.

---

### Étape 5

Le modèle

est-il le problème ?

Ou

les features ?

Ou

les données ?

---

## Ce que cherche le recruteur

Il veut voir que vous **diagnostiquez avant d'agir**.

---

# Cas n°2 — Nouveau type de fraude

Le recruteur dit

Nous avons découvert

une fraude

jamais observée.

Que faites-vous ?

---

## Réponse

Je ne commence pas

par entraîner un modèle.

Je commence par :

* comprendre la fraude ;
* parler aux analystes fraude ;
* analyser les transactions ;
* identifier les nouvelles caractéristiques.

Ensuite

je crée

de nouvelles features.

Puis

je collecte

de nouveaux labels.

Enfin

je réentraîne.

---

# Cas n°3 — Recall en baisse

Le Recall passe

de

95 %

à

72 %.

---

Question

Que faites-vous ?

---

Réponse

Je regarde :

* Data Drift
* Concept Drift
* qualité des données
* nouvelles règles métier
* changement de comportement des fraudeurs
* stabilité des features

---

Seulement ensuite

je décide

s'il faut

réentraîner.

---

# Cas n°4 — Modèle lent

Le modèle répond

en

2 secondes.

L'objectif

↓

100 ms.

---

Que faites-vous ?

---

Réponse Senior

Je mesure d'abord où est passé le temps :

* lecture des données ;
* calcul des features ;
* inférence ;
* réseau.

Puis j'optimise.

Par exemple :

* cache Redis ;
* pré-calcul des features ;
* modèle plus léger ;
* parallélisation ;
* optimisation des requêtes SQL.

---

# Cas n°5 — Données manquantes

Depuis ce matin

30 %

des IP

sont nulles.

---

Que faites-vous ?

---

Réponse

Je cherche

pourquoi.

Le problème

vient-il

* du fournisseur ?
* d'un bug ?
* d'un changement d'API ?

Ensuite

je décide

comment traiter

les valeurs manquantes.

---

# Cas n°6 — Deux modèles

Modèle A

Recall

95 %

Precision

70 %

---

Modèle B

Recall

90 %

Precision

92 %

Lequel choisissez-vous ?

---

Réponse

Cela dépend

du coût métier.

Si manquer

une fraude

coûte

très cher

↓

Modèle A.

Si bloquer

des clients

est très coûteux

↓

Modèle B.

---

Le recruteur adore cette réponse.

Pourquoi ?

Parce qu'il comprend

que

Machine Learning

=

Business.

---

# Cas n°7 — Vous avez 1 milliard de transactions

Question

Que faites-vous ?

---

Réponse

Je ne charge jamais

1 milliard

de lignes

dans Pandas.

Je pense immédiatement :

* Spark
* Parquet
* S3
* partitionnement
* traitement distribué

---

# Cas n°8 — Les données sont déséquilibrées

99.9 %

des transactions

↓

Normales.

---

Que faites-vous ?

---

Réponse

Je pense immédiatement :

* Recall
* Precision
* PR-AUC
* Class Weight
* SMOTE (avec prudence)
* XGBoost
* seuil optimal

---

# Cas n°9 — Le PDG pose une question

Pourquoi

ce client

a été refusé ?

---

Réponse

Je montre

SHAP.

Je traduis

ensuite

l'explication

dans un langage métier.

---

Jamais

je ne réponds

> "Parce que XGBoost l'a dit."

---

# Cas n°10 — Vous êtes seul Data Scientist

Comment organisez-vous

le projet ?

---

Réponse

1. Comprendre le métier.

2. Définir la cible.

3. Explorer les données.

4. Nettoyer.

5. Feature Engineering.

6. Baseline.

7. Modèles.

8. Validation.

9. Déploiement.

10. Monitoring.

---

# Question très difficile

Le recruteur dit

Notre modèle

obtient

99 %

de Recall

mais

la banque

perd

des millions.

Pourquoi ?

---

Réponse Senior

Parce que le Recall n'est qu'une partie de l'équation. Si la Precision est faible, le modèle peut bloquer un grand nombre de transactions légitimes, générant des coûts opérationnels, une mauvaise expérience client et une perte de revenus. J'analyserais donc les coûts associés aux faux positifs et aux faux négatifs afin de déterminer le seuil de décision optimal.

---

# Une autre question difficile

Pourquoi

XGBoost

ne suffit-il pas ?

---

Réponse

Parce qu'un système

de fraude

n'est jamais

uniquement

un modèle.

Il comprend :

* règles métier
* Feature Store
* Monitoring
* Drift Detection
* APIs
* Kafka
* Graph ML
* Analystes fraude

Le modèle

n'est

qu'un composant.

---

# Les cinq qualités recherchées chez SentiLink

À la fin d'un entretien, un recruteur se pose souvent cinq questions :

### 1. Peut-il coder ?

Python, SQL, Git, bonnes pratiques.

---

### 2. Comprend-il le Machine Learning ?

Choix des modèles, validation, métriques, interprétation.

---

### 3. Comprend-il la fraude ?

Comportements, signaux faibles, compromis métier.

---

### 4. Peut-il mettre un modèle en production ?

API, Docker, AWS, monitoring, MLOps.

---

### 5. Sait-il raisonner ?

C'est le critère le plus important.

---

# Ce qui distingue les meilleurs candidats

Ils ne répondent pas uniquement :

> "J'utiliserais XGBoost."

Ils expliquent :

* pourquoi ;
* dans quelles conditions ;
* quelles alternatives existent ;
* quels compromis ils acceptent.

---

# Votre niveau après cette formation

Si vous maîtrisez les modules que nous avons parcourus, vous serez préparé sur les principaux domaines suivants :

| Domaine                 | Niveau attendu         |
| ----------------------- | ---------------------- |
| Python                  | Avancé                 |
| SQL                     | Avancé                 |
| Pandas                  | Expert                 |
| Machine Learning        | Avancé                 |
| XGBoost / LightGBM      | Avancé                 |
| Détection de fraude     | Expert                 |
| Feature Engineering     | Expert                 |
| Graph ML                | Intermédiaire à avancé |
| AWS                     | Intermédiaire          |
| FastAPI                 | Intermédiaire          |
| Docker                  | Intermédiaire          |
| Kafka / Streaming       | Intermédiaire          |
| MLOps                   | Intermédiaire à avancé |
| Communication technique | Avancé                 |

## Dernier conseil

Le meilleur moyen de transformer ces connaissances en réussite est de les pratiquer.

Je vous recommande de réaliser **un projet complet de détection de fraude**, comprenant :

* ingestion des données ;
* exploration et nettoyage ;
* feature engineering avancé ;
* entraînement de plusieurs modèles (Logistic Regression, XGBoost, LightGBM) ;
* interprétation avec SHAP ;
* exposition du modèle via une API FastAPI ;
* conteneurisation avec Docker ;
* déploiement sur AWS ;
* monitoring des performances et détection du drift.

Si vous êtes capable de présenter un tel projet en expliquant vos choix techniques et métier, vous serez en mesure de démontrer des compétences très proches de celles attendues pour un poste de Data Scientist en FinTech comme celui proposé par SentiLink.
