Nous arrivons maintenant à un chapitre qui est **très fréquemment abordé lors des entretiens de Data Scientist**.

Un recruteur peut vous demander :

> **"Vous avez entraîné un Random Forest. Comment choisissez-vous ses hyperparamètres ?"**

ou

> **"Pourquoi avez-vous utilisé RandomizedSearchCV plutôt que GridSearchCV ?"**

ou encore

> **"Comment éviter le surapprentissage pendant l'optimisation des hyperparamètres ?"**

Si vous savez répondre à ces questions, vous vous démarquerez de nombreux candidats.

---

# MODULE 3 – Chapitre 12 : Optimisation des Hyperparamètres

## Objectifs

À la fin de ce chapitre, vous serez capable de :

* distinguer paramètres et hyperparamètres ;
* choisir une stratégie de recherche adaptée ;
* optimiser les principaux modèles de classification ;
* éviter les pièges courants ;
* expliquer vos choix en entretien.

---

# 1. Paramètres vs Hyperparamètres

## Paramètres

Ce sont les valeurs **apprises automatiquement** pendant l'entraînement.

Exemple avec une régression logistique :

[
y = w_1x_1 + w_2x_2 + b
]

Les coefficients **w₁**, **w₂** et **b** sont des paramètres.

Le modèle les apprend à partir des données.

---

## Hyperparamètres

Ils sont définis **avant l'entraînement**.

Exemple :

```python
RandomForestClassifier(
    n_estimators=300,
    max_depth=8
)
```

Ici :

* `n_estimators`
* `max_depth`

sont des hyperparamètres.

---

## Question d'entretien

Quelle est la différence entre un paramètre et un hyperparamètre ?

### Réponse

Les paramètres sont appris automatiquement pendant l'entraînement, tandis que les hyperparamètres sont choisis avant l'entraînement et contrôlent le comportement de l'algorithme.

---

# 2. Pourquoi optimiser les hyperparamètres ?

Deux modèles identiques peuvent produire des performances très différentes selon leurs hyperparamètres.

Exemple :

```python
RandomForest(max_depth=2)
```

Accuracy :

```text
82 %
```

---

```python
RandomForest(max_depth=10)
```

Accuracy :

```text
94 %
```

---

L'optimisation consiste à trouver la meilleure combinaison.

---

# 3. Grid Search

Principe :

Tester **toutes** les combinaisons.

Exemple

```python
param_grid = {
    "max_depth": [3, 5, 8],
    "n_estimators": [100, 300, 500]
}
```

Nombre de combinaisons :

```text
3 × 3 = 9
```

Toutes seront évaluées.

---

Avec Scikit-Learn

```python
from sklearn.model_selection import GridSearchCV

grid = GridSearchCV(
    estimator=model,
    param_grid=param_grid,
    cv=5,
    scoring="f1"
)

grid.fit(X_train, y_train)
```

---

## Avantages

* Recherche exhaustive.
* Garantit de tester toutes les combinaisons proposées.

---

## Inconvénients

Très coûteux si l'espace de recherche est grand.

---

# Question

Quand utiliser GridSearchCV ?

### Réponse

Lorsque le nombre de combinaisons est raisonnable et que l'on souhaite une recherche exhaustive.

---

# 4. Randomized Search

Même idée.

Mais :

Au lieu de tester toutes les combinaisons,

on en choisit un nombre limité au hasard.

Exemple

1000 combinaisons possibles.

On en teste seulement :

```text
50
```

---

Scikit-Learn

```python
from sklearn.model_selection import RandomizedSearchCV

search = RandomizedSearchCV(
    estimator=model,
    param_distributions=params,
    n_iter=50,
    cv=5,
    scoring="f1",
    random_state=42
)
```

---

## Pourquoi est-ce souvent meilleur ?

Parce que :

* beaucoup plus rapide ;
* permet d'explorer des espaces de recherche beaucoup plus grands.

---

## Question

Pourquoi RandomizedSearchCV est-il souvent préféré ?

### Réponse

Parce qu'il trouve souvent une très bonne solution avec un coût de calcul bien inférieur à celui d'une recherche exhaustive.

---

# 5. Validation croisée pendant le tuning

Ne jamais optimiser un modèle sur le jeu de test.

La bonne approche :

```text
Train
     ↓
Validation croisée
     ↓
Choix des hyperparamètres
     ↓
Évaluation finale sur Test
```

Le jeu de test ne doit être utilisé qu'à la fin.

---

# 6. Hyperparamètres importants

## Logistic Regression

```python
C
```

Force de la régularisation.

---

```python
penalty
```

L1

ou

L2

---

# Random Forest

Les principaux hyperparamètres :

```python
n_estimators
```

Nombre d'arbres.

---

```python
max_depth
```

Profondeur maximale.

---

```python
min_samples_split
```

Nombre minimal d'observations pour effectuer une séparation.

---

```python
min_samples_leaf
```

Nombre minimal d'observations dans une feuille.

---

```python
max_features
```

Nombre de variables candidates à chaque séparation.

---

# XGBoost

Les plus importants :

```python
learning_rate
```

Plus il est faible,

plus il faut généralement augmenter :

```python
n_estimators
```

---

```python
max_depth
```

Contrôle la complexité.

---

```python
subsample
```

Fraction des observations utilisée pour construire chaque arbre.

---

```python
colsample_bytree
```

Fraction des variables utilisée pour chaque arbre.

---

```python
min_child_weight
```

Contrôle la création des feuilles et aide à limiter le surapprentissage.

---

# LightGBM

Les principaux :

* `num_leaves`
* `max_depth`
* `learning_rate`
* `feature_fraction`
* `bagging_fraction`
* `min_data_in_leaf`

---

# CatBoost

Les plus utilisés :

* `depth`
* `learning_rate`
* `iterations`
* `l2_leaf_reg`

---

# 7. Early Stopping

Très fréquent avec XGBoost et LightGBM.

Principe :

Le modèle s'améliore.

Puis :

```text
Validation

0.81

0.84

0.86

0.861

0.861

0.860

0.859
```

On arrête automatiquement l'entraînement lorsque la métrique de validation ne s'améliore plus pendant un certain nombre d'itérations.

---

Pourquoi ?

Pour éviter le surapprentissage et réduire le temps d'entraînement.

---

# Question

Pourquoi utiliser Early Stopping ?

### Réponse

Pour arrêter l'entraînement avant que le modèle ne commence à mémoriser les données d'entraînement et pour économiser des ressources de calcul.

---

# 8. Choisir la bonne métrique

En fraude :

Évitez :

```text
Accuracy
```

Préférez :

* Precision
* Recall
* F1-score
* ROC-AUC
* **PR-AUC (Precision-Recall AUC)**

La métrique utilisée pour le tuning doit être cohérente avec l'objectif métier.

---

# 9. Les erreurs fréquentes

## Erreur 1

Tester les hyperparamètres sur le jeu de test.

❌ Incorrect.

---

## Erreur 2

Optimiser uniquement l'Accuracy.

❌ Très risqué sur des données déséquilibrées.

---

## Erreur 3

Chercher une précision excessive.

Exemple :

```python
max_depth
```

de 1 à 100.

Souvent inutile.

Définissez un espace de recherche raisonnable.

---

## Erreur 4

Ne pas fixer `random_state` pendant les expériences.

Cela rend les résultats difficiles à reproduire.

---

# Cas pratique SentiLink

Vous entraînez un modèle XGBoost.

Résultats :

Train :

```text
99.9 %
```

Validation :

```text
87 %
```

Question

Que faites-vous ?

### Réponse attendue

Le modèle semble souffrir de surapprentissage.

Actions possibles :

* réduire `max_depth` ;
* diminuer la complexité globale ;
* augmenter la régularisation ;
* diminuer le `learning_rate` et ajuster `n_estimators` ;
* utiliser `subsample` et `colsample_bytree` ;
* activer `early_stopping` ;
* vérifier l'absence de data leakage.

---

# Exemple complet

```python
from sklearn.model_selection import RandomizedSearchCV
from xgboost import XGBClassifier

params = {
    "max_depth": [3, 5, 7],
    "learning_rate": [0.01, 0.05, 0.1],
    "n_estimators": [100, 300, 500],
    "subsample": [0.7, 0.9, 1.0]
}

model = XGBClassifier(
    eval_metric="logloss",
    random_state=42
)

search = RandomizedSearchCV(
    estimator=model,
    param_distributions=params,
    n_iter=20,
    cv=5,
    scoring="average_precision",  # adapté aux classes déséquilibrées
    random_state=42,
    n_jobs=-1
)

search.fit(X_train, y_train)

print(search.best_params_)
```

---

# Questions d'entretien

### Quelle différence entre GridSearchCV et RandomizedSearchCV ?

* **GridSearchCV** teste toutes les combinaisons.
* **RandomizedSearchCV** en teste un sous-ensemble choisi aléatoirement.

---

### Pourquoi utiliser une validation croisée pendant le tuning ?

Pour obtenir une estimation plus robuste des performances et limiter le risque de choisir des hyperparamètres adaptés à un seul découpage des données.

---

### Pourquoi utiliser Early Stopping ?

Pour arrêter l'entraînement lorsque les performances sur les données de validation cessent de progresser.

---

### Quels sont les hyperparamètres les plus importants de Random Forest ?

* `n_estimators`
* `max_depth`
* `min_samples_split`
* `min_samples_leaf`
* `max_features`

---

### Quels sont les hyperparamètres essentiels de XGBoost ?

* `learning_rate`
* `n_estimators`
* `max_depth`
* `subsample`
* `colsample_bytree`
* `min_child_weight`

---

# Cas d'entretien complet

**Question :**

Vous devez entraîner un modèle de détection de fraude sur **30 millions de transactions**, avec **0,15 % de fraudes**.

Comment procédez-vous ?

### Réponse attendue

1. Construire une **baseline** (par exemple une régression logistique).
2. Tester un modèle de type **Gradient Boosting** (XGBoost, LightGBM ou CatBoost).
3. Définir un espace de recherche réaliste pour les hyperparamètres.
4. Utiliser **RandomizedSearchCV** avec une **validation croisée stratifiée**.
5. Optimiser une métrique adaptée aux classes déséquilibrées (par exemple **PR-AUC**, **Recall** ou **F1-score**, selon l'objectif métier).
6. Utiliser **Early Stopping** lorsque c'est possible.
7. Évaluer le modèle final sur un jeu de test indépendant.
8. Analyser les erreurs (False Positives et False Negatives) avant le déploiement.

---

# Prochain chapitre

Nous aborderons un sujet devenu incontournable dans les entretiens modernes de Data Science :

## Interprétabilité des modèles

Vous apprendrez à répondre à des questions telles que :

* Pourquoi le modèle a-t-il classé cette transaction comme frauduleuse ?
* Quelles variables influencent le plus les prédictions ?
* Comment expliquer un modèle complexe à une équipe métier ou à un régulateur ?
* Comment utiliser **Feature Importance**, **Permutation Importance**, **Partial Dependence Plots** et **SHAP** ?

Cette partie est particulièrement importante dans les secteurs de la **finance**, de la **banque** et de la **détection de fraude**, où les décisions automatisées doivent être justifiables.
