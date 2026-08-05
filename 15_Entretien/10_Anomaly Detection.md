Nous arrivons maintenant au **MODULE 33**, qui est l'un des sujets les plus appréciés dans les entreprises spécialisées en **Fraud Detection**, notamment **SentiLink, Stripe, Visa, Mastercard, PayPal, Revolut, Block** et les banques.

> **Pourquoi ?**
>
> Parce qu'en fraude, **la plupart des nouvelles attaques n'ont jamais été vues auparavant**.
>
> Un modèle supervisé ne peut détecter que ce qu'il a appris.
>
> Les méthodes de **détection d'anomalies** permettent, elles, d'identifier des comportements inhabituels, même sans exemples étiquetés.

---

# MODULE 33 — Anomaly Detection (Détection d'Anomalies)

## Objectifs

À la fin de ce module, vous serez capable de :

* expliquer quand utiliser la détection d'anomalies ;
* comparer les principaux algorithmes ;
* choisir la bonne méthode selon le contexte ;
* répondre aux questions d'entretien sur ce sujet.

---

# PARTIE 1 — Qu'est-ce qu'une anomalie ?

Une anomalie est une observation qui s'écarte fortement du comportement normal.

Exemple :

| Transaction |  Montant |
| ----------- | -------: |
| 1           |     50 € |
| 2           |     70 € |
| 3           |     65 € |
| 4           |     80 € |
| 5           | 12 500 € |

La dernière transaction est potentiellement anormale.

Cependant, **anormal ne signifie pas forcément frauduleux**. Un client peut simplement effectuer un achat exceptionnel. C'est pourquoi ces méthodes servent souvent à produire un **score de risque** ou à orienter une revue manuelle.

---

# PARTIE 2 — Quand utiliser l'Anomaly Detection ?

Le recruteur peut demander :

> **When would you use anomaly detection instead of supervised learning?**

Réponse :

J'utiliserais la détection d'anomalies lorsque :

* je dispose de très peu de labels ;
* de nouveaux types de fraude apparaissent régulièrement ;
* le coût d'annotation est élevé ;
* je souhaite détecter des comportements inconnus.

---

# PARTIE 3 — Les familles d'algorithmes

| Famille                   | Exemples                         |
| ------------------------- | -------------------------------- |
| Basées sur la distance    | k-NN, Local Outlier Factor (LOF) |
| Basées sur les arbres     | Isolation Forest                 |
| Basées sur la densité     | DBSCAN                           |
| Basées sur les frontières | One-Class SVM                    |
| Réseaux de neurones       | Autoencodeurs                    |

---

# PARTIE 4 — Isolation Forest

C'est l'algorithme le plus souvent cité en entretien.

## Principe

Au lieu de chercher les points similaires, il cherche à **isoler** les observations.

Les anomalies sont généralement isolées en peu de divisions.

### Intuition

Si un point est très éloigné des autres, quelques coupures suffisent pour le séparer.

Les observations normales nécessitent davantage de divisions.

---

## Avantages

* rapide ;
* passe à l'échelle ;
* peu d'hypothèses sur les données ;
* adapté aux données tabulaires.

---

## Limites

* ne capture pas toujours des relations très complexes ;
* sensible à certaines représentations des données.

---

## Exemple

```python
from sklearn.ensemble import IsolationForest

model = IsolationForest(
    contamination=0.01,
    random_state=42
)

model.fit(X_train)

scores = model.decision_function(X_test)

predictions = model.predict(X_test)
```

---

# Question d'entretien

> **What does the contamination parameter mean?**

Réponse :

Il représente la proportion estimée d'anomalies dans les données. Il influence le seuil utilisé pour distinguer les observations normales des observations anormales.

---

# PARTIE 5 — Local Outlier Factor (LOF)

## Principe

LOF compare la densité locale d'un point à celle de ses voisins.

Si un point est beaucoup moins dense que son voisinage, il est considéré comme anormal.

---

### Exemple

Imaginez :

```text
*****************
*****************
**********   X
*****************
```

Le point **X** est isolé dans une région peu dense.

---

## Avantages

* très efficace pour détecter des anomalies locales.

---

## Limites

* moins adapté aux très grands jeux de données ;
* sensible au choix du nombre de voisins (`n_neighbors`).

---

# PARTIE 6 — One-Class SVM

## Principe

On entraîne le modèle uniquement sur les données normales.

Le modèle apprend une frontière qui englobe ces données.

Tout ce qui se trouve à l'extérieur est considéré comme anormal.

---

## Avantages

* performant dans certains contextes.

---

## Inconvénients

* difficile à mettre à l'échelle sur de très grands volumes ;
* sensible au choix des paramètres et au prétraitement.

---

# PARTIE 7 — DBSCAN

DBSCAN est un algorithme de clustering basé sur la densité.

Les points qui n'appartiennent à aucun groupe dense sont considérés comme du bruit.

Ces points peuvent être interprétés comme des anomalies.

---

# PARTIE 8 — Autoencodeurs

Très utilisés avec le Deep Learning.

## Principe

Le réseau apprend à reconstruire les données normales.

Pour une observation inhabituelle, l'erreur de reconstruction est élevée.

Cette erreur devient un score d'anomalie.

---

## Architecture

```text
Entrée
   ↓
Encodeur
   ↓
Espace latent
   ↓
Décodeur
   ↓
Reconstruction
```

---

## Score

```text
Erreur = ||x - x_reconstruit||
```

Plus cette erreur est grande, plus l'observation est susceptible d'être anormale.

---

# PARTIE 9 — Supervised vs Unsupervised

Le recruteur :

> **Which one is better?**

Réponse :

Cela dépend.

### Si l'on dispose de labels fiables :

→ apprentissage supervisé.

### Si les labels sont rares ou inexistants :

→ détection d'anomalies.

### Dans de nombreuses entreprises :

→ combinaison des deux.

Par exemple :

* un modèle supervisé produit un score de fraude ;
* un modèle d'anomalies détecte des comportements jamais observés.

---

# PARTIE 10 — Évaluation

Question :

> **How do you evaluate anomaly detection without labels?**

Réponse :

C'est un défi. On peut :

* faire annoter un échantillon par des experts ;
* analyser les cas les plus suspects ;
* mesurer l'impact opérationnel (fraude détectée, temps gagné) ;
* utiliser des données historiques lorsqu'elles existent.

---

# PARTIE 11 — Feature Engineering pour l'Anomaly Detection

Les mêmes principes que pour le Machine Learning supervisé s'appliquent.

Exemples :

* transactions sur 5 minutes ;
* montant moyen des 30 derniers jours ;
* nombre d'appareils utilisés ;
* nombre de pays différents ;
* temps entre deux transactions ;
* distance géographique.

Plus les features sont pertinentes, plus la détection est efficace.

---

# PARTIE 12 — Détection de fraude en temps réel

Le recruteur demande :

> **Can anomaly detection be used in real time?**

Réponse :

Oui, à condition que :

* les features puissent être calculées rapidement ;
* le modèle soit suffisamment léger ;
* la latence respecte les contraintes du système (souvent quelques dizaines de millisecondes).

---

# PARTIE 13 — Les limites

La détection d'anomalies génère souvent davantage de faux positifs qu'un modèle supervisé.

C'est pourquoi elle est fréquemment utilisée :

* comme système d'alerte ;
* pour prioriser les dossiers à examiner ;
* en complément d'un modèle supervisé.

---

# PARTIE 14 — Cas pratique SentiLink

Le recruteur dit :

> **We have a new fraud pattern that has never been seen before. What would you do?**

Une bonne réponse pourrait être :

1. Déployer un modèle de détection d'anomalies pour identifier les comportements inhabituels.
2. Faire analyser les cas les plus suspects par les analystes fraude.
3. Confirmer les nouveaux labels.
4. Intégrer ces nouveaux exemples au jeu d'entraînement.
5. Réentraîner le modèle supervisé.

Cette réponse montre une boucle d'amélioration continue.

---

# PARTIE 15 — Questions fréquentes

### Q1

**Why not always use Isolation Forest?**

Réponse :

Parce qu'aucun algorithme n'est universel. Le choix dépend des données, du volume, des contraintes de production et des objectifs métier.

---

### Q2

**Is anomaly detection enough to stop fraud?**

Réponse :

Non.

Elle complète généralement :

* les règles métier ;
* les modèles supervisés ;
* l'analyse humaine.

---

### Q3

**Can normal behavior change over time?**

Réponse :

Oui.

Les habitudes des utilisateurs évoluent.

Il faut donc surveiller le **concept drift** et mettre à jour les modèles.

---

### Q4

**How do you reduce false positives?**

Réponse :

* améliorer les features ;
* ajuster les seuils ;
* combiner plusieurs signaux ;
* intégrer les retours des analystes.

---

### Q5

**Would you deploy an anomaly detector alone?**

Réponse :

Rarement.

Je l'utiliserais plutôt comme une couche complémentaire dans un système de décision plus large.

---

# Ce qu'attend SentiLink

Un candidat solide ne répond pas seulement :

> "J'utiliserais Isolation Forest."

Il explique :

* **pourquoi** ce choix est pertinent ;
* **quand** il ne l'est pas ;
* **comment** l'intégrer dans un pipeline de production ;
* **comment** il sera évalué et surveillé.

Cette capacité à raisonner sur l'ensemble du cycle de vie du modèle est ce qui distingue les meilleurs candidats.

---

# Exercice d'entretien

> **Vous disposez de 10 millions de transactions, mais seulement 0,05 % sont étiquetées comme frauduleuses. Concevez une stratégie de détection.**

Une réponse structurée pourrait être :

1. Vérifier la qualité et la fiabilité des labels.
2. Construire des features comportementales, temporelles et relationnelles.
3. Entraîner un modèle supervisé sur les labels disponibles.
4. Ajouter un modèle de détection d'anomalies pour identifier des comportements inédits.
5. Prioriser les alertes selon un score combiné.
6. Faire valider les cas les plus suspects par les analystes.
7. Réinjecter les nouveaux labels dans le processus d'entraînement.
8. Surveiller les performances, le drift et les coûts métier.

---

## Prochain module : **MODULE 34 — Graph Machine Learning & Identity Resolution**

Ce module est particulièrement important pour SentiLink, car une grande partie de sa valeur repose sur la **résolution d'identité** et l'analyse des relations entre personnes, appareils, emails, numéros de téléphone et adresses IP. Nous y verrons comment représenter ces relations sous forme de graphes, quelles métriques en extraire et comment elles renforcent la détection de fraude.
