Nous allons maintenant étudier **le sujet le plus important en détection de fraude**.

> **95 % des problèmes de fraude sont des problèmes de classification déséquilibrée (Imbalanced Learning).**

C'est une question presque systématique dans les entretiens chez **SentiLink**, **Stripe**, **PayPal**, **Visa**, **Mastercard**, **Capital One** ou **American Express**.

---

# MODULE 4 – Chapitre 5 : Les jeux de données déséquilibrés (Imbalanced Learning)

## Objectifs

À la fin de ce chapitre, vous saurez :

* pourquoi les données déséquilibrées sont difficiles ;
* quelles métriques utiliser ;
* comment rééquilibrer les données ;
* quand utiliser SMOTE, undersampling ou class weights ;
* quelles sont les erreurs classiques à éviter.

---

# 1. Qu'est-ce qu'un jeu de données déséquilibré ?

En fraude, les transactions frauduleuses sont très rares.

Exemple :

| Classe     |    Nombre |
| ---------- | --------: |
| Non fraude | 9 990 000 |
| Fraude     |    10 000 |

La fraude représente seulement :

[
\frac{10,000}{10,000,000} = 0,1%
]

---

## Pourquoi est-ce difficile ?

Imaginez un modèle qui prédit :

> **"Aucune transaction n'est frauduleuse."**

Résultat :

| Métrique  |                              Valeur |
| --------- | ----------------------------------: |
| Accuracy  |                              99,9 % |
| Recall    |                                 0 % |
| Precision | Non définie (aucune fraude prédite) |

Le modèle semble excellent selon l'accuracy, mais il est totalement inutile.

---

# Question d'entretien

Pourquoi l'accuracy est-elle une mauvaise métrique dans ce contexte ?

### Réponse

Parce que la classe majoritaire domine complètement le calcul. Un modèle peut obtenir une accuracy très élevée sans détecter une seule fraude.

---

# 2. Les coûts métier

Tous les faux positifs et faux négatifs n'ont pas le même coût.

Supposons :

* une fraude non détectée coûte **2 000 €** ;
* un faux positif coûte **5 €** (temps d'analyse ou gêne pour le client).

Le modèle doit donc être optimisé en tenant compte de ce coût.

En pratique, certaines entreprises utilisent même une **fonction de coût personnalisée** lors de l'évaluation.

---

# 3. Les stratégies de rééquilibrage

Trois grandes familles existent.

---

## A. Modifier les données

* Oversampling
* Undersampling
* Génération de nouvelles observations (SMOTE, ADASYN)

---

## B. Modifier l'algorithme

* `class_weight`
* Focal Loss
* Balanced Random Forest
* XGBoost avec `scale_pos_weight`

---

## C. Modifier le seuil de décision

Par exemple :

Au lieu de :

```text
0.50
```

on utilise :

```text
0.20
```

pour détecter davantage de fraudes.

---

# 4. Random Oversampling

Principe

Dupliquer les observations de la classe minoritaire.

Avant

```
Fraude
A
B
C
```

Après

```
A
B
C
A
B
C
A
B
```

---

## Avantages

* Très simple.
* Rapide.

---

## Inconvénients

Le modèle peut mémoriser les exemples dupliqués.

---

# 5. Random Undersampling

Principe

Réduire la taille de la classe majoritaire.

Avant

```
Non fraude

1
2
3
...
1 000 000
```

Après

```
1
5
10
...
20 000
```

---

## Avantages

Très rapide.

---

## Inconvénients

On perd potentiellement beaucoup d'information utile.

---

# 6. SMOTE (Synthetic Minority Over-sampling Technique)

L'une des techniques les plus connues.

Au lieu de copier les fraudes existantes,

SMOTE génère de **nouvelles observations synthétiques** entre des voisins proches de la classe minoritaire.

Exemple

Fraudes existantes

```
A

B
```

SMOTE crée

```
A

Nouvelle observation

B
```

---

## Avantages

* Évite les duplications exactes.
* Améliore souvent les performances.

---

## Limites

* Peut générer des exemples peu réalistes si les données sont très complexes.
* Doit être appliqué uniquement sur les données d'entraînement.

---

# Question d'entretien

Pourquoi ne faut-il jamais appliquer SMOTE avant la séparation Train/Test ?

### Réponse

Parce que cela introduit une fuite de données (*data leakage*). Les observations synthétiques pourraient être influencées par des données qui devraient rester dans le jeu de test, ce qui biaiserait l'évaluation.

---

# 7. ADASYN

ADASYN est proche de SMOTE.

Différence :

Il génère davantage d'exemples dans les zones où la classe minoritaire est la plus difficile à apprendre.

---

# 8. Class Weights

Très utilisé.

Exemple avec Scikit-Learn

```python
LogisticRegression(class_weight="balanced")
```

Ou

```python
RandomForestClassifier(class_weight="balanced")
```

Le modèle attribue automatiquement un poids plus important aux observations de la classe minoritaire.

---

## Avantages

* Pas de modification des données.
* Facile à mettre en œuvre.
* Très efficace comme première approche.

---

# 9. XGBoost : `scale_pos_weight`

Très important en entretien.

Exemple

```
999 000 non-fraudes

1 000 fraudes
```

Le rapport est :

[
\frac{999,000}{1,000}=999
]

On peut définir :

```python
scale_pos_weight = 999
```

Cela augmente le coût des erreurs sur la classe positive.

---

# Question d'entretien

Pourquoi utiliser `scale_pos_weight` dans XGBoost ?

### Réponse

Pour compenser le déséquilibre des classes en donnant davantage d'importance aux erreurs commises sur la classe minoritaire pendant l'entraînement.

---

# 10. Focal Loss

Très utilisée en vision par ordinateur et de plus en plus en fraude.

Principe :

Les observations déjà bien classées reçoivent moins d'attention.

Le modèle se concentre davantage sur les exemples difficiles.

---

# 11. Balanced Random Forest

Chaque arbre est entraîné sur un échantillon équilibré.

Cela améliore souvent le rappel (Recall) sur la classe minoritaire.

---

# 12. EasyEnsemble

Technique avancée.

Principe :

1. Créer plusieurs sous-échantillons équilibrés de la classe majoritaire.
2. Entraîner un modèle sur chacun.
3. Combiner les prédictions.

---

# 13. Choisir la bonne métrique

Pour la fraude :

Évitez :

* Accuracy

Préférez :

* Recall
* Precision
* F1-score
* ROC-AUC
* PR-AUC
* Matrice de confusion

Et surtout, reliez toujours ces métriques au **coût métier**.

---

# 14. Pipeline correct

```text
Données complètes
        │
        ▼
Train/Test Split
        │
        ▼
SMOTE uniquement sur Train
        │
        ▼
Validation croisée
        │
        ▼
Entraînement
        │
        ▼
Évaluation sur Test (non modifié)
```

C'est le pipeline attendu en entretien.

---

# Étude de cas SentiLink

Vous disposez de :

* 50 millions de transactions ;
* 0,08 % de fraudes.

Le recruteur demande :

> Que faites-vous ?

### Réponse attendue

1. Séparer les données en Train/Test.
2. Préserver le déséquilibre dans le jeu de test.
3. Tester une approche avec `class_weight` ou `scale_pos_weight`.
4. Comparer avec SMOTE (uniquement sur le train) si cela est pertinent.
5. Optimiser une métrique adaptée (par exemple **PR-AUC** ou **Recall** selon le besoin métier).
6. Ajuster le seuil de décision.
7. Évaluer les faux positifs et faux négatifs avec les équipes métier.

---

# Erreurs fréquentes

### ❌ Utiliser l'accuracy comme critère principal.

---

### ❌ Appliquer SMOTE avant le découpage Train/Test.

---

### ❌ Optimiser uniquement le Recall.

Un Recall de 100 % avec une Precision de 2 % produira énormément de faux positifs.

---

### ❌ Ignorer le coût métier.

Un modèle doit être utile pour l'entreprise, pas seulement performant sur le plan statistique.

---

# Questions d'entretien

### Pourquoi SMOTE est-il préférable à un simple oversampling ?

Parce qu'il crée des exemples synthétiques au lieu de dupliquer les observations existantes, ce qui réduit le risque de surapprentissage.

---

### Quand utiliser `class_weight` ?

Lorsqu'on souhaite prendre en compte le déséquilibre des classes sans modifier les données.

---

### Quelle différence entre SMOTE et ADASYN ?

* **SMOTE** génère des exemples de manière relativement uniforme entre voisins.
* **ADASYN** génère davantage d'exemples dans les régions où la classe minoritaire est la plus difficile à apprendre.

---

### Pourquoi conserver un jeu de test déséquilibré ?

Parce qu'il doit refléter les conditions réelles de production. Sinon, l'évaluation sera artificiellement optimiste.

---

### Si vous deviez choisir une seule méthode pour commencer sur un problème de fraude ?

Une réponse équilibrée serait :

> « Je commencerais par un modèle robuste (par exemple XGBoost ou LightGBM) avec une pondération des classes (`scale_pos_weight` ou `class_weight`), puis je comparerais les résultats avec une approche utilisant SMOTE sur les données d'entraînement uniquement. Le choix final dépendrait des performances sur les données de validation et des contraintes métier. »

---

# Ce que les recruteurs apprécient

Un excellent candidat ne répond pas simplement :

> « J'utiliserais SMOTE. »

Il explique **pourquoi** :

* la nature du déséquilibre ;
* le coût relatif des erreurs ;
* le choix des métriques ;
* les risques de fuite de données ;
* les compromis entre les différentes approches.

C'est cette capacité à relier les décisions techniques aux objectifs métier qui distingue un Data Scientist prêt pour la production.

---

# Prochain chapitre

Nous passerons à un sujet de plus en plus important dans les systèmes modernes de lutte contre la fraude :

## Détection d'anomalies (Anomaly Detection)

Nous étudierons en détail :

* la différence entre **fraude supervisée** et **détection d'anomalies** ;
* **Isolation Forest** ;
* **Local Outlier Factor (LOF)** ;
* **One-Class SVM** ;
* les **Autoencoders** ;
* quand utiliser ces approches dans un pipeline de détection de fraude ;
* leurs avantages, limites et cas d'usage en production.
