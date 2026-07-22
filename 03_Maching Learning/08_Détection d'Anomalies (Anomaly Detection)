Nous abordons maintenant un sujet qui revient très souvent chez **SentiLink**, **Stripe**, **PayPal**, **Airbnb**, **Uber**, **Amazon** et dans toutes les entreprises qui luttent contre la fraude.

---

# MODULE 4 – Chapitre 6 : Détection d'Anomalies (Anomaly Detection)

> **Une fraude n'est pas toujours connue à l'avance.**

Dans la réalité, de nouveaux types de fraude apparaissent chaque semaine.

Le problème est donc :

> **Comment détecter une fraude qui n'existe pas encore dans les données d'entraînement ?**

C'est précisément le rôle de la **détection d'anomalies**.

---

# 1. Classification vs Détection d'anomalies

## Classification supervisée

Vous disposez des labels :

| Transaction | Fraude |
| ----------- | ------ |
| A           | 0      |
| B           | 0      |
| C           | 1      |
| D           | 0      |

Le modèle apprend :

```
X → y
```

Exemples :

* Logistic Regression
* Random Forest
* XGBoost
* LightGBM

---

## Détection d'anomalies

Vous ne connaissez pas les fraudes.

Vous ne disposez que des transactions normales.

Le modèle cherche :

> **Tout comportement inhabituel.**

---

## Exemple

Historique d'un client

```
120 €

140 €

90 €

110 €

130 €
```

Nouvelle transaction

```
18 000 €
```

Même sans label,

elle est probablement anormale.

---

# Question d'entretien

Quand utiliser l'Anomaly Detection ?

### Réponse

Lorsque les données sont peu ou pas étiquetées, lorsque de nouveaux types de fraude apparaissent régulièrement ou comme couche complémentaire à un modèle supervisé.

---

# 2. Les grandes approches

Les algorithmes les plus connus sont :

* Isolation Forest
* Local Outlier Factor (LOF)
* One-Class SVM
* Autoencoder
* DBSCAN (dans certains cas)
* Méthodes statistiques simples (Z-score, IQR)

---

# 3. Isolation Forest

C'est probablement l'algorithme le plus utilisé.

Pourquoi ?

Parce qu'il est :

* rapide ;
* robuste ;
* adapté aux grands jeux de données.

---

## Principe

Imaginez un arbre.

Chaque séparation isole progressivement les observations.

Les observations normales nécessitent beaucoup de séparations.

Les anomalies sont isolées très rapidement.

Exemple

```
Client normal

↓

20 séparations

↓

Isolé
```

---

```
Fraude

↓

3 séparations

↓

Isolée
```

Le modèle conclut :

Plus une observation est isolée rapidement,

plus elle est suspecte.

---

## Exemple Python

```python
from sklearn.ensemble import IsolationForest

model = IsolationForest(
    contamination=0.01,
    random_state=42
)

model.fit(X_train)

pred = model.predict(X_test)
```

Le paramètre `contamination` représente la proportion estimée d'anomalies.

---

## Avantages

* Très rapide.
* Peu de paramètres.
* Fonctionne bien sur de grands ensembles de données.

---

## Limites

* Ne tire pas parti des labels lorsqu'ils sont disponibles.
* Le choix de `contamination` influence les résultats.

---

# Question d'entretien

Pourquoi Isolation Forest est-elle adaptée à la fraude ?

### Réponse

Parce qu'elle identifie efficacement des comportements rares sans nécessiter un grand nombre d'exemples de fraude étiquetés.

---

# 4. Local Outlier Factor (LOF)

LOF compare chaque observation à ses voisins.

Une observation est considérée comme anormale si sa densité locale est nettement plus faible que celle de ses voisins.

---

## Exemple

```
● ● ● ● ●

● ● ● ●

●

● ● ● ●
```

Le point isolé est détecté comme anomalie.

---

## Avantages

Très performant lorsque les anomalies sont locales.

---

## Limites

* Peu adapté aux très grands jeux de données.
* Sensible au choix du nombre de voisins (`n_neighbors`).

---

# 5. One-Class SVM

Idée :

Apprendre uniquement les observations normales.

Le modèle construit une frontière qui englobe ces observations.

Toute observation située à l'extérieur est considérée comme anormale.

---

## Avantages

Fonctionne bien lorsque la frontière entre normal et anormal est complexe.

---

## Limites

* Très coûteux en calcul.
* Peu adapté aux dizaines de millions de transactions.

---

# Question

Pourquoi One-Class SVM est-il rarement utilisé à grande échelle ?

### Réponse

Parce que son coût de calcul et sa consommation mémoire augmentent rapidement avec la taille des données.

---

# 6. Autoencoders

Très utilisés avec le Deep Learning.

---

## Principe

Le réseau de neurones apprend à reconstruire une transaction normale.

```
Entrée

↓

Encodeur

↓

Représentation compressée

↓

Décodeur

↓

Reconstruction
```

---

Pour une transaction normale

Erreur de reconstruction :

```
Faible
```

---

Pour une fraude

Erreur :

```
Très élevée
```

La fraude est détectée.

---

## Utilisation

Très utile lorsque :

* beaucoup de variables ;
* données complexes ;
* comportements évolutifs.

---

# Question d'entretien

Pourquoi un Autoencoder peut-il détecter une fraude ?

### Réponse

Parce qu'il apprend les caractéristiques des comportements normaux. Une transaction inhabituelle est généralement mal reconstruite et produit une erreur élevée.

---

# 7. Z-score

Approche statistique simple.

Supposons :

Montant moyen

```
100 €
```

Écart-type

```
20 €
```

Nouvelle transaction

```
500 €
```

Le Z-score est très élevé.

Cette transaction est donc atypique.

---

## Limites

Suppose une distribution proche de la normale, ce qui est rarement le cas pour les montants de transactions.

---

# 8. Quand utiliser chaque méthode ?

| Méthode          | Labels requis | Grand volume |             Temps réel            | Cas d'usage                    |
| ---------------- | :-----------: | :----------: | :-------------------------------: | ------------------------------ |
| Isolation Forest |       ❌       |       ✅      |                 ✅                 | Détection d'anomalies générale |
| LOF              |       ❌       |     Moyen    |                Non                | Anomalies locales              |
| One-Class SVM    |       ❌       |       ❌      |                Non                | Petits jeux de données         |
| Autoencoder      |       ❌       |       ✅      | Oui (avec infrastructure adaptée) | Données complexes              |
| XGBoost          |       ✅       |       ✅      |                 ✅                 | Fraudes connues                |

---

# 9. Supervised + Unsupervised

En production,

les meilleures entreprises ne choisissent pas une seule approche.

Elles combinent plusieurs modèles.

Exemple

```
Transaction

↓

Règles métier

↓

Isolation Forest

↓

XGBoost

↓

Score final
```

Chaque composant apporte une information complémentaire.

---

# Cas pratique SentiLink

Nouvelle fraude.

Aucun exemple disponible.

Question

Que faites-vous ?

---

### Réponse attendue

Dans un premier temps :

* utiliser un système de détection d'anomalies (par exemple Isolation Forest) ;
* mettre en place des règles métier temporaires ;
* collecter les nouveaux cas confirmés ;
* entraîner ensuite un modèle supervisé lorsque suffisamment de labels sont disponibles.

---

# 10. Limites de la détection d'anomalies

Toutes les anomalies ne sont pas des fraudes.

Exemple :

Un client effectue un achat exceptionnel pendant ses vacances.

Le comportement est inhabituel,

mais parfaitement légitime.

C'est pourquoi la détection d'anomalies génère souvent davantage de faux positifs qu'un modèle supervisé.

---

# Questions d'entretien

### Quelle différence entre un modèle supervisé et un modèle de détection d'anomalies ?

Un modèle supervisé apprend à partir de données étiquetées, tandis qu'un modèle de détection d'anomalies recherche des comportements inhabituels sans avoir nécessairement besoin de labels.

---

### Pourquoi utiliser Isolation Forest plutôt que LOF sur 100 millions de transactions ?

Parce qu'Isolation Forest est beaucoup plus scalable et mieux adaptée aux très grands volumes de données.

---

### Les anomalies sont-elles toujours des fraudes ?

Non. Une anomalie est simplement un comportement rare. Une investigation supplémentaire est souvent nécessaire pour déterminer s'il s'agit réellement d'une fraude.

---

### Pourquoi combiner XGBoost et Isolation Forest ?

Parce que :

* XGBoost détecte efficacement les fraudes déjà connues ;
* Isolation Forest peut identifier des comportements nouveaux ou inattendus.

Leur combinaison améliore la couverture globale du système.

---

# Cas d'entretien (niveau SentiLink)

Le recruteur vous dit :

> « Nous venons d'observer un nouveau type de fraude. Nous n'avons que cinq exemples confirmés. Comment réagiriez-vous ? »

### Réponse idéale

1. Ne pas entraîner immédiatement un modèle supervisé, car cinq exemples sont insuffisants.
2. Mettre en place des règles métier temporaires pour limiter le risque.
3. Déployer un système de détection d'anomalies afin d'identifier des comportements similaires.
4. Collecter progressivement des données étiquetées grâce aux équipes de fraude.
5. Dès qu'un volume suffisant de cas est disponible, entraîner un modèle supervisé et le comparer aux approches existantes.
6. Surveiller les performances et ajuster régulièrement le système au fur et à mesure de l'évolution des comportements.

---

# Prochain chapitre : Détection de fraude en temps réel

C'est le sujet central des entreprises comme **SentiLink**.

Nous verrons :

* comment construire un pipeline capable de répondre en **moins de 100 ms** ;
* les architectures événementielles avec **Kafka**, **Kinesis** et les files de messages ;
* le **feature store** ;
* les API de scoring en temps réel ;
* la mise en cache des features ;
* les contraintes de latence et de disponibilité ;
* les stratégies de déploiement des modèles (Blue/Green, Canary, Shadow Deployment).

Ce chapitre correspond très directement aux systèmes utilisés en production dans les grandes plateformes de détection de fraude.
