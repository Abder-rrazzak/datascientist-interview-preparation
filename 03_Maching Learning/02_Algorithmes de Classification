Nous abordons maintenant **le cœur du Machine Learning**. C'est cette partie qui est la plus fréquemment évaluée lors des entretiens de Data Scientist.

> **Important :** Les recruteurs ne cherchent généralement pas à savoir si vous connaissez toutes les équations par cœur. Ils veulent surtout vérifier que vous savez :
>
> * choisir le bon algorithme ;
> * expliquer son fonctionnement ;
> * justifier pourquoi vous le choisissez plutôt qu'un autre ;
> * interpréter les résultats.

---

# MODULE 3 – Chapitre 10 : Les Algorithmes de Classification

# Les modèles les plus utilisés

Nous allons étudier les modèles suivants :

1. Logistic Regression
2. Decision Tree
3. Random Forest
4. Gradient Boosting
5. XGBoost
6. LightGBM
7. CatBoost
8. Support Vector Machine (SVM)
9. K-Nearest Neighbors (KNN)
10. Naive Bayes

Pour chacun, nous verrons :

* Principe
* Avantages
* Inconvénients
* Hyperparamètres
* Cas d'utilisation
* Questions d'entretien

---

# 1. Logistic Regression

## Malgré son nom...

Ce n'est **pas** un algorithme de régression.

C'est un modèle de **classification**.

---

## Principe

Le modèle calcule une probabilité :

```text
P(Fraude = 1)
```

Puis applique un seuil (souvent 0,5).

Exemple :

```text
0.92
```

↓

Fraude

---

```text
0.13
```

↓

Non fraude

---

## Fonctionnement

Le modèle combine les variables :

* montant
* pays
* device
* historique
* fréquence
* etc.

Puis applique une fonction sigmoïde :

```text
                1
σ(x) = ------------------
        1 + e^(-x)
```

Cette fonction transforme n'importe quelle valeur réelle en une probabilité comprise entre **0 et 1**.

---

## Exemple

| Montant | Pays | Fraude |
| ------: | ---- | ------ |
|      50 | FR   | 0      |
|      70 | FR   | 0      |
|   12000 | RU   | 1      |

Le modèle apprend que :

* un montant très élevé ;
* un pays inhabituel ;

augmentent la probabilité de fraude.

---

## Hyperparamètres importants

### C

Contrôle la régularisation.

Petit C

↓

plus de régularisation

↓

modèle plus simple

Grand C

↓

moins de régularisation

↓

modèle plus complexe

---

### penalty

```text
L1

L2
```

L2 est le choix par défaut dans la plupart des cas.

---

## Avantages

✅ très rapide

✅ facilement interprétable

✅ fournit directement des probabilités

✅ excellent modèle de référence (baseline)

---

## Inconvénients

❌ suppose une relation approximativement linéaire entre les variables explicatives et le log-odds

❌ peut être moins performant lorsque les frontières de décision sont très complexes

---

## Utilisation en fraude

Souvent utilisé comme premier modèle.

Ensuite :

Random Forest

ou

XGBoost

donnent fréquemment de meilleures performances.

---

## Question d'entretien

Pourquoi commencer par une Logistic Regression ?

### Réponse

Parce qu'elle est simple, rapide à entraîner, interprétable et constitue une excellente base de comparaison avant d'utiliser des modèles plus complexes.

---

# 2. Decision Tree

## Principe

L'arbre pose des questions successives.

Exemple :

```text
Montant > 1000 ?

         Oui
          │
     Pays = RU ?

      Oui      Non
      │          │
   Fraude     Non fraude
```

---

## Pourquoi est-il intéressant ?

Il est très facile à expliquer.

---

## Avantages

✅ interprétable

✅ gère naturellement les variables non linéaires

✅ peu de prétraitement

---

## Inconvénients

❌ surapprentissage (overfitting)

Un arbre trop profond finit par mémoriser les données.

---

## Hyperparamètres

### max_depth

Limite la profondeur.

---

### min_samples_leaf

Nombre minimal d'observations dans une feuille.

---

### min_samples_split

Nombre minimal d'observations pour effectuer une séparation.

---

## Question

Pourquoi limiter la profondeur ?

### Réponse

Pour éviter que l'arbre ne mémorise les données d'entraînement et améliorer sa capacité de généralisation.

---

# 3. Random Forest

L'un des modèles les plus populaires.

## Principe

Au lieu d'un arbre :

```text
Arbre 1

Arbre 2

Arbre 3

...

Arbre 500
```

Tous votent.

Décision finale :

Majorité.

---

## Pourquoi est-il performant ?

Chaque arbre voit :

* un sous-ensemble d'observations (bootstrap) ;
* un sous-ensemble de variables.

Cela réduit la variance et améliore la robustesse.

---

## Avantages

✅ robuste

✅ très bonnes performances

✅ peu sensible aux valeurs atypiques

✅ gère les interactions complexes

---

## Inconvénients

❌ moins interprétable

❌ plus lent

---

## Hyperparamètres

### n_estimators

Nombre d'arbres.

---

### max_depth

Profondeur maximale.

---

### max_features

Nombre de variables testées à chaque séparation.

---

## Question

Pourquoi Random Forest est-il généralement meilleur qu'un arbre unique ?

### Réponse

Parce qu'il réduit la variance grâce à l'agrégation des prédictions de nombreux arbres entraînés sur des échantillons et des variables différents.

---

# 4. Gradient Boosting

Contrairement à Random Forest :

Les arbres sont construits **séquentiellement**.

Chaque nouvel arbre cherche à corriger les erreurs du précédent.

```text
Arbre 1

↓

corrige

↓

Arbre 2

↓

corrige

↓

Arbre 3
```

---

## Avantages

Très bonnes performances.

---

## Inconvénients

Plus lent à entraîner.

---

# 5. XGBoost

Probablement le modèle le plus célèbre.

Très utilisé :

* Kaggle
* Finance
* Assurance
* Détection de fraude

---

## Pourquoi ?

Parce qu'il est :

* rapide ;
* performant ;
* robuste ;
* capable de gérer des données tabulaires de grande taille.

---

## Hyperparamètres

### learning_rate

Plus petit

↓

apprentissage plus lent

↓

meilleure généralisation (souvent).

---

### max_depth

Profondeur des arbres.

---

### n_estimators

Nombre d'arbres.

---

### subsample

Fraction des observations utilisée pour chaque arbre.

---

## Question

Pourquoi XGBoost est-il très populaire ?

### Réponse

Parce qu'il offre un excellent compromis entre performance, flexibilité et vitesse sur les données tabulaires.

---

# 6. LightGBM

Développé par Microsoft.

Très proche de XGBoost.

---

## Différences

LightGBM est souvent :

* plus rapide sur de très grands jeux de données ;
* plus économe en mémoire.

---

## Utilisation

Très fréquent en production.

---

# 7. CatBoost

Développé par Yandex.

Très performant sur les données comportant de nombreuses variables catégorielles.

Il réduit le besoin d'encodage manuel dans de nombreux cas.

---

# 8. Support Vector Machine (SVM)

## Principe

Le SVM cherche l'hyperplan qui sépare au mieux les classes en maximisant la marge.

Grâce aux **kernels**, il peut modéliser des frontières non linéaires.

---

## Avantages

* Très performant sur des jeux de données de taille modérée.
* Efficace lorsque les classes sont bien séparées.

---

## Limites

* Peu adapté aux très grands jeux de données.
* Sensible au choix des hyperparamètres (`C`, `gamma`) et au changement d'échelle des variables.

---

# 9. K-Nearest Neighbors (KNN)

## Principe

Pour prédire la classe d'un nouvel individu, on regarde les **K voisins** les plus proches.

---

## Hyperparamètre principal

```text
K
```

Nombre de voisins.

---

## Avantages

Très simple.

---

## Inconvénients

* Lent lors de la prédiction sur de gros volumes.
* Sensible à l'échelle des variables.
* Performances qui se dégradent avec de nombreuses dimensions.

---

# 10. Naive Bayes

Basé sur le théorème de Bayes avec une hypothèse d'indépendance conditionnelle entre les variables.

---

## Utilisation

Très utilisé pour :

* classification de texte ;
* filtrage de spam ;
* analyse de sentiments.

---

## Avantages

* Très rapide.
* Fonctionne bien avec peu de données dans certains contextes.

---

## Limites

L'hypothèse d'indépendance est souvent simplificatrice.

---

# Comparatif des modèles

| Modèle              | Interprétable |       Rapide       | Données tabulaires |     Probabilités    | Très utilisé en fraude |
| ------------------- | :-----------: | :----------------: | :----------------: | :-----------------: | :--------------------: |
| Logistic Regression |       ✅       |          ✅         |          ✅         |          ✅          |            ✅           |
| Decision Tree       |       ✅       |          ✅         |          ✅         |          ✅          |   Oui, comme baseline  |
| Random Forest       |     Moyen     |        Moyen       |          ✅         |          ✅          |            ✅           |
| Gradient Boosting   |     Faible    |        Moyen       |          ✅         |          ✅          |            ✅           |
| XGBoost             |     Faible    |        Moyen       |         ⭐⭐⭐        |          ✅          |           ⭐⭐⭐          |
| LightGBM            |     Faible    |         ⭐⭐⭐        |         ⭐⭐⭐        |          ✅          |           ⭐⭐⭐          |
| CatBoost            |     Faible    |         ⭐⭐         |         ⭐⭐⭐        |          ✅          |           ⭐⭐           |
| SVM                 |     Faible    |   Non (gros jeux)  |        Moyen       | Selon configuration |          Rare          |
| KNN                 |     Moyen     | Lent en prédiction |       Faible       |   Non directement   |          Rare          |
| Naive Bayes         |     Moyen     |         ⭐⭐⭐        |        Moyen       |          ✅          |          Rare          |

---

# Questions d'entretien

### Pourquoi utiliser une Logistic Regression comme modèle de départ ?

Parce qu'elle est rapide, simple à interpréter et fournit une excellente référence.

---

### Pourquoi Random Forest réduit-il l'overfitting ?

Parce qu'il agrège les prédictions de nombreux arbres entraînés sur des échantillons et des variables différents.

---

### Quelle différence entre Random Forest et Gradient Boosting ?

* **Random Forest** : les arbres sont entraînés indépendamment (en parallèle).
* **Gradient Boosting** : les arbres sont construits séquentiellement pour corriger les erreurs des précédents.

---

### Pourquoi XGBoost est-il si populaire ?

Parce qu'il offre généralement d'excellentes performances sur les données tabulaires tout en restant efficace sur de grands volumes.

---

### Quel modèle choisir pour un problème de détection de fraude ?

Une réponse solide en entretien serait :

1. Construire une **Logistic Regression** comme baseline.
2. Tester ensuite **Random Forest**.
3. Comparer avec **XGBoost**, **LightGBM** ou **CatBoost**.
4. Choisir le modèle offrant le meilleur compromis entre performances, temps d'inférence, interprétabilité et contraintes de production.

---

# Cas pratique SentiLink

Vous devez construire un modèle de détection de fraude sur **20 millions de transactions** comprenant :

* 150 variables numériques ;
* 40 variables catégorielles ;
* seulement **0,3 %** de transactions frauduleuses.

**Question :** Quel modèle testeriez-vous en priorité et pourquoi ?

### Réponse attendue

* Commencer par une **Logistic Regression** pour établir une baseline.
* Construire ensuite un modèle de type **Gradient Boosting** (par exemple **XGBoost**, **LightGBM** ou **CatBoost**) qui est particulièrement performant sur des données tabulaires complexes.
* Gérer explicitement le **déséquilibre des classes** (pondération des classes, rééchantillonnage ou autres techniques adaptées).
* Évaluer les modèles avec des métriques adaptées à la fraude (par exemple **Precision**, **Recall**, **F1-score**, **ROC-AUC** et surtout **Precision-Recall AUC** lorsque la classe positive est très rare).
* Vérifier les temps d'entraînement et d'inférence avant le déploiement.

---

# Suite du programme

Le prochain chapitre portera sur un sujet décisif pour la détection de fraude :

* **Overfitting et Underfitting** ;
* **Bias-Variance Tradeoff** ;
* **Régularisation (L1/L2)** ;
* **Hyperparameter Tuning** (`GridSearchCV`, `RandomizedSearchCV`, optimisation bayésienne) ;
* **Calibration des probabilités** ;
* **Interprétabilité des modèles** avec **Feature Importance**, **Permutation Importance** et **SHAP**, des sujets très fréquemment abordés dans les entretiens de Data Scientist senior.
