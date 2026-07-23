Nous arrivons maintenant à l'un des sujets les plus avancés et les plus intéressants de la Data Science appliquée à la fraude.

> **C'est un sujet qui revient très souvent chez SentiLink, Stripe, PayPal, Visa, Mastercard et les banques.**

Pourquoi ?

Parce qu'un fraudeur intelligent **change constamment sa stratégie**.

Un modèle supervisé ne peut détecter que les fraudes qu'il a déjà vues.

La vraie question est donc :

> **Comment détecter une fraude totalement nouvelle ?**

C'est là qu'intervient **l'Anomaly Detection**.

---

# MODULE 10 – Détection d'anomalies (Anomaly Detection)

## 1. Pourquoi un modèle supervisé ne suffit pas ?

Imaginons votre dataset.

| Transaction | Fraud |
| ----------- | ----: |
| A           |     0 |
| B           |     0 |
| C           |     0 |
| D           |     1 |

Le modèle apprend :

> "Voici à quoi ressemble une fraude."

Tout fonctionne.

---

Mais imaginons maintenant :

Un nouveau fraudeur invente une technique jamais observée.

Le dataset devient :

```text
Anciennes fraudes

↓

Nouvelles fraudes
```

Le modèle n'a jamais vu ces nouveaux comportements.

Il risque de les classer comme **légitimes**.

C'est ce qu'on appelle un **Concept Drift**.

---

# 2. Supervised vs Unsupervised

## Machine Learning supervisé

On possède les labels.

```text
Fraude

↓

1

Non fraude

↓

0
```

Objectif

↓

Prédire le label.

---

## Machine Learning non supervisé

Aucun label.

On cherche :

> **Ce qui paraît anormal.**

---

# Exemple

Transactions normales :

```text
20 €

35 €

18 €

42 €

28 €
```

Puis apparaît :

```text
25 000 €
```

Même sans label,

tout le monde comprend que cette transaction est inhabituelle.

---

# Définition

Une anomalie est une observation :

* rare ;
* différente ;
* inattendue.

---

# Types d'anomalies

## 1. Anomalie ponctuelle

Exemple

```text
20 €

25 €

18 €

30 €

40 000 €
```

Très facile à détecter.

---

## 2. Anomalie contextuelle

Exemple

```text
300 €
```

Normal.

Mais

```text
300 €

à 03h00 du matin

↓

Suspect.
```

Le contexte change tout.

---

## 3. Anomalie collective

Exemple

Une seule transaction :

↓

Normale.

Mais :

500 transactions identiques

↓

Même IP

↓

Même appareil

↓

Même minute

↓

Très suspect.

---

# Algorithme 1 – Isolation Forest

L'un des plus utilisés.

## Principe

Au lieu de chercher les données normales,

Isolation Forest cherche :

> **Les observations faciles à isoler.**

---

Imaginez :

1000 points

↓

999 regroupés

↓

1 très éloigné.

Cette observation sera isolée très rapidement.

↓

Probablement une anomalie.

---

# Fonctionnement

L'algorithme construit plusieurs arbres aléatoires.

Les anomalies sont isolées avec peu de divisions.

Les observations normales nécessitent davantage de divisions.

---

## Question d'entretien

Pourquoi Isolation Forest est-il efficace ?

### Réponse

Parce que les observations atypiques sont généralement plus faciles à isoler que les observations normales.

---

# Exemple Python

```python
from sklearn.ensemble import IsolationForest

model = IsolationForest(
    contamination=0.01,
    random_state=42
)

model.fit(X)
```

---

Prédiction

```python
prediction = model.predict(X)
```

Résultat

```text
1

↓

Normal

-1

↓

Anomalie
```

---

# Paramètre important

```python
contamination=0.01
```

Signifie :

On estime que

1 %

des données sont anormales.

---

# Algorithme 2 – Local Outlier Factor (LOF)

LOF compare chaque observation à ses voisins.

Exemple

Tous les points sont proches.

↓

Un point est très éloigné.

↓

LOF

↓

Anomalie.

---

Très efficace lorsque la densité locale varie selon les régions des données.

---

# Algorithme 3 – One-Class SVM

Principe

On entraîne uniquement sur les données normales.

Le modèle apprend :

> "Voici la frontière des comportements normaux."

Toute observation en dehors de cette frontière est considérée comme suspecte.

---

Très utile lorsque :

* les fraudes sont rares ;
* on ne possède quasiment aucun exemple de fraude.

---

# Algorithme 4 – Autoencoder

Très populaire en Deep Learning.

## Principe

Le réseau apprend à reconstruire les données normales.

Schéma

```text
Entrée

↓

Compression

↓

Décompression

↓

Reconstruction
```

---

Pour une donnée normale

↓

Erreur de reconstruction faible.

---

Pour une anomalie

↓

Erreur importante.

↓

Fraude potentielle.

---

# Pourquoi cela fonctionne ?

Parce que le réseau n'a appris que les comportements habituels.

Il reconstruit mal ce qu'il n'a jamais vu.

---

# Quand utiliser chaque méthode ?

| Algorithme       | Cas d'utilisation                                   |
| ---------------- | --------------------------------------------------- |
| Isolation Forest | Excellent choix général pour les données tabulaires |
| LOF              | Anomalies locales                                   |
| One-Class SVM    | Très peu d'anomalies connues                        |
| Autoencoder      | Grand volume de données complexes                   |

---

# Cas pratique SentiLink

Le recruteur dit :

> "Une nouvelle fraude apparaît aujourd'hui. Aucun exemple étiqueté n'existe encore."

Que faites-vous ?

### Réponse idéale

Je combine plusieurs approches :

1. Utiliser des règles métier pour détecter rapidement des comportements inhabituels.
2. Déployer un modèle de détection d'anomalies (par exemple Isolation Forest) afin d'identifier les transactions atypiques.
3. Faire examiner les cas les plus suspects par les analystes fraude.
4. Une fois les nouveaux cas confirmés, les intégrer au jeu d'entraînement pour réentraîner le modèle supervisé.

---

# Pourquoi combiner les méthodes ?

Dans la pratique :

```text
Règles métier

+

Machine Learning supervisé

+

Anomaly Detection

=

Système robuste
```

Aucune méthode ne suffit seule.

---

# Concept Drift

Très important.

Les fraudeurs changent.

Le modèle devient progressivement moins performant.

Exemple

2025

↓

Fraudes

↓

Cartes volées.

2026

↓

Fraudes

↓

Deepfakes.

Le modèle doit évoluer.

---

# Data Drift vs Concept Drift

## Data Drift

Les variables changent.

Exemple

Le montant moyen des transactions augmente.

---

## Concept Drift

La relation entre les variables et la fraude change.

C'est plus difficile à détecter.

---

# Comment détecter un Drift ?

Surveiller :

* Recall
* Precision
* Distribution des variables
* Distribution des scores
* Taux de fraude
* Temps de réponse

---

# Réentraînement

Question fréquente :

> Quand réentraîner un modèle ?

Réponse

Lorsque :

* les performances diminuent ;
* un drift est détecté ;
* suffisamment de nouvelles données labellisées sont disponibles.

---

# Questions d'entretien

### Pourquoi utiliser Isolation Forest ?

Parce qu'il détecte efficacement les observations atypiques sans nécessiter de labels.

---

### Pourquoi un modèle supervisé ne détecte-t-il pas toujours les nouvelles fraudes ?

Parce qu'il apprend uniquement à partir des exemples observés pendant l'entraînement.

---

### Pourquoi les Autoencoders sont-ils utilisés ?

Parce qu'ils apprennent le comportement normal et signalent les observations qu'ils reconstruisent mal.

---

### Quelle différence entre Data Drift et Concept Drift ?

* Data Drift : la distribution des variables évolue.
* Concept Drift : la relation entre les variables et la cible change.

---

# Question très difficile

Le recruteur demande :

> "Votre modèle détecte bien les anciennes fraudes mais manque toutes les nouvelles. Que faites-vous ?"

### Réponse de niveau Senior

Je commencerais par vérifier si un Concept Drift est apparu. J'analyserais les faux négatifs pour identifier de nouveaux schémas de fraude. En parallèle, je mettrais en place un système de détection d'anomalies pour détecter les comportements inédits et je travaillerais avec les équipes fraude afin d'obtenir rapidement des labels. Enfin, je réentraînerais le modèle avec ces nouveaux exemples et j'ajusterais les règles métier si nécessaire.

---

# Ce qu'un recruteur SentiLink apprécie

Un candidat qui comprend que :

* les fraudeurs s'adaptent ;
* les données évoluent ;
* un modèle n'est jamais "terminé" ;
* la supervision humaine reste indispensable ;
* le système doit être capable d'apprendre en continu.

---

# Les connaissances supplémentaires qui vous démarqueront

Si vous souhaitez atteindre un niveau comparable à celui d'un **Senior Data Scientist** dans une FinTech, les prochains sujets à maîtriser sont :

1. **Graph Machine Learning**

   * Détection de réseaux de fraude.
   * Algorithmes comme Node2Vec, GraphSAGE et GNN.
   * Analyse de communautés frauduleuses.

2. **Streaming et décision temps réel**

   * Apache Kafka.
   * Apache Flink.
   * Traitement d'événements avec une latence de quelques millisecondes.

3. **Feature Store**

   * Pourquoi il est indispensable en production.
   * Partage des variables entre entraînement et prédiction.
   * Éviter les incohérences (*training-serving skew*).

4. **Explicabilité avancée**

   * SHAP global et local.
   * LIME.
   * Explications adaptées aux exigences réglementaires.

Ces sujets sont fréquemment abordés dans les entretiens les plus exigeants pour les postes de Data Scientist spécialisés en fraude, identité numérique et gestion des risques.
