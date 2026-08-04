Nous arrivons maintenant au **MODULE 26**, qui est probablement **le module le plus important pour un entretien chez SentiLink**.

Pourquoi ?

Parce que la majorité des modèles de fraude en production ne sont **pas** des réseaux de neurones.

Ils utilisent principalement :

* **XGBoost**
* **LightGBM**
* **CatBoost**
* **Random Forest**
* parfois une **Logistic Regression** comme baseline.

Dans beaucoup d'entreprises FinTech, **XGBoost reste la référence** grâce à son excellent compromis entre performance, vitesse et interprétabilité.

---

# MODULE 26 — Machine Learning Avancé (Niveau SentiLink)

# Objectifs

À la fin de ce module, vous serez capable de :

* choisir le bon algorithme ;
* expliquer XGBoost en détail ;
* éviter le data leakage ;
* interpréter un modèle avec SHAP ;
* optimiser les hyperparamètres ;
* répondre aux questions techniques les plus difficiles.

---

# PARTIE 1 — Comment choisir un algorithme ?

Question d'entretien :

> **How do you choose a machine learning model?**

Une réponse structurée est souvent appréciée :

1. Comprendre le problème métier.
2. Identifier le type de variable cible.
3. Évaluer la taille et la qualité des données.
4. Construire une baseline simple.
5. Tester plusieurs modèles.
6. Comparer les performances et les contraintes (temps de calcul, interprétabilité, latence).
7. Choisir le modèle qui répond le mieux aux besoins métier.

Le meilleur modèle n'est pas toujours celui qui obtient le meilleur score.

---

# PARTIE 2 — Pourquoi commencer par une baseline ?

Question fréquente.

> **Why build a baseline model?**

Réponse :

> A baseline provides a reference point. It helps determine whether a more complex model actually delivers meaningful improvements.

Exemple :

```text
Logistic Regression

↓

PR-AUC = 0.72
```

Puis :

```text
XGBoost

↓

PR-AUC = 0.84
```

Vous pouvez quantifier le gain.

---

# PARTIE 3 — Pourquoi XGBoost est-il si populaire ?

Question très fréquente.

Le recruteur attend une réponse plus riche que "parce qu'il est performant".

Bonne réponse :

> XGBoost generally performs very well on structured tabular data. It captures non-linear relationships and feature interactions, includes regularization to reduce overfitting, handles missing values internally in many cases, and is efficient for large datasets.

---

# PARTIE 4 — Comment fonctionne XGBoost ?

XGBoost ne construit pas un seul arbre.

Il construit une **suite d'arbres**.

Chaque nouvel arbre cherche à corriger les erreurs des arbres précédents.

Illustration :

```text
Tree 1

↓

Errors

↓

Tree 2

↓

Remaining Errors

↓

Tree 3

↓

...
```

C'est le principe du **Gradient Boosting**.

---

# PARTIE 5 — Gradient Boosting

Question classique.

> **What is Gradient Boosting?**

Réponse :

> Gradient Boosting builds models sequentially. Each new model focuses on correcting the residual errors made by the previous ensemble, gradually improving overall performance.

---

# PARTIE 6 — Random Forest vs XGBoost

Très fréquente.

| Random Forest                      | XGBoost                    |
| ---------------------------------- | -------------------------- |
| Arbres indépendants                | Arbres séquentiels         |
| Bagging                            | Boosting                   |
| Rapide à entraîner                 | Plus long à entraîner      |
| Robuste                            | Souvent plus performant    |
| Moins sensible aux hyperparamètres | Plus sensible aux réglages |

---

# Question

> **When would you use Random Forest instead of XGBoost?**

Réponse :

> When I need a strong baseline quickly, want a model that is simpler to tune, or when training time is an important constraint.

---

# PARTIE 7 — Hyperparamètres XGBoost

Les principaux sont :

```text
max_depth
```

Contrôle la profondeur des arbres.

---

```text
learning_rate
```

Taille des mises à jour.

Petit :

↓

apprentissage plus lent mais souvent plus stable.

---

```text
n_estimators
```

Nombre d'arbres.

---

```text
subsample
```

Fraction des observations utilisées pour chaque arbre.

---

```text
colsample_bytree
```

Fraction des variables utilisées pour construire un arbre.

---

```text
reg_alpha
reg_lambda
```

Régularisation L1 et L2.

---

# Question

> **How would you tune XGBoost?**

Bonne réponse :

> I would start with a reasonable baseline configuration, use cross-validation, optimize a small set of influential hyperparameters such as max_depth, learning_rate and n_estimators, then evaluate using metrics aligned with the business objective.

---

# PARTIE 8 — Feature Importance

Question très fréquente.

XGBoost peut fournir une importance des variables.

Exemple :

| Feature       | Importance |
| ------------- | ---------- |
| amount_ratio  | 0.31       |
| device_age    | 0.20       |
| shared_device | 0.18       |
| ip_risk       | 0.12       |

Attention :

L'importance dépend de la méthode utilisée (gain, cover, fréquence, permutation, etc.).

---

# PARTIE 9 — SHAP

Question presque certaine.

> **What is SHAP?**

Bonne réponse :

> SHAP explains individual predictions by estimating how much each feature contributes to moving the prediction away from a baseline value.

Exemple :

Transaction :

```text
Fraud Score = 0.91
```

Explication :

```text
+ New Device

+ High Amount Ratio

+ Suspicious IP

- Long Customer History
```

SHAP ne dit pas qu'une variable "cause" la fraude ; il explique sa contribution à la prédiction du modèle.

---

# PARTIE 10 — Calibration

Question avancée.

Un modèle prédit :

```text
0.90
```

Cela signifie-t-il que 90 % des transactions similaires sont frauduleuses ?

Pas nécessairement.

La calibration mesure si les probabilités prédites correspondent aux fréquences observées.

---

# Question

> **Why is calibration important?**

Réponse :

> Decision thresholds and business rules often rely on predicted probabilities. Poorly calibrated probabilities can lead to suboptimal decisions even if ranking performance is good.

---

# PARTIE 11 — Data Leakage

Question très importante.

Exemple :

Vous utilisez :

```text
chargeback
```

comme variable d'entrée.

Mais le chargeback est connu :

```text
30 jours

après
```

la transaction.

Le modèle "voit le futur".

↓

Data Leakage.

---

# Question

> **How do you prevent data leakage?**

Réponse :

> I make sure every feature is available at prediction time, use time-aware validation when appropriate, separate preprocessing between training and evaluation, and carefully review how labels and features are generated.

---

# PARTIE 12 — Feature Selection

Question fréquente.

Pourquoi supprimer certaines variables ?

Parce que :

* elles sont redondantes ;
* elles ajoutent du bruit ;
* elles augmentent le temps de calcul ;
* elles favorisent le surapprentissage.

---

# PARTIE 13 — Cross Validation

Exemple :

```text
5 folds
```

Le modèle est entraîné 5 fois.

Chaque fold devient le jeu de validation une fois.

Avantage :

Une estimation plus robuste de la performance.

---

# Attention

Pour la fraude ou les séries temporelles, une validation aléatoire peut être inadaptée. Une validation respectant l'ordre temporel est souvent préférable pour éviter les fuites d'information.

---

# PARTIE 14 — Early Stopping

Question fréquente.

Pendant l'entraînement :

```text
Validation Loss
```

cesse de s'améliorer.

On arrête.

Cela évite souvent le surapprentissage.

---

# PARTIE 15 — Pipelines

Question très fréquente.

Pourquoi utiliser un Pipeline Scikit-learn ?

Exemple :

```text
Imputation

↓

Encoding

↓

Scaling

↓

Model
```

Avantages :

* reproductibilité ;
* réduction du risque de fuite de données ;
* simplification de la validation.

---

# PARTIE 16 — Imbalanced Learning

Question incontournable.

Supposons :

```text
Fraud

0.5%
```

Le modèle prédit :

```text
Non Fraud

100%
```

Accuracy :

```text
99.5%
```

Mais le modèle est inutile.

---

# Solutions

* pondération des classes (`class_weight`) ;
* rééchantillonnage (sur-échantillonnage, sous-échantillonnage) ;
* SMOTE (avec prudence selon le contexte) ;
* ajustement du seuil de décision ;
* métriques adaptées (Precision, Recall, PR-AUC).

---

# PARTIE 17 — Cost-sensitive Learning

Toutes les erreurs n'ont pas le même coût.

Exemple :

Un faux négatif peut coûter :

```text
20 000 $
```

Un faux positif :

```text
10 $
```

Le modèle doit être optimisé en fonction du coût métier, pas uniquement d'une métrique statistique.

---

# PARTIE 18 — Ensemble Learning

Question classique.

Pourquoi combiner plusieurs modèles ?

Parce qu'ils peuvent faire des erreurs différentes.

Techniques :

* Bagging ;
* Boosting ;
* Stacking.

---

# PARTIE 19 — Les erreurs qui éliminent les candidats

❌ Dire :

> XGBoost est toujours meilleur.

Non.

Le meilleur modèle dépend :

* des données ;
* du temps disponible ;
* de l'interprétabilité requise ;
* des contraintes de production.

---

❌ Dire :

> Je choisis le modèle avec la meilleure accuracy.

Faux sur un problème de fraude fortement déséquilibré.

---

❌ Ignorer le coût métier.

Un bon Data Scientist pense :

```text
Business

↓

Model
```

et non l'inverse.

---

# Questions avancées de niveau SentiLink

### Q1

**Why is XGBoost so effective on tabular data?**

Réponse :

> Because it captures non-linear relationships, models feature interactions, includes regularization, handles heterogeneous features well, and often achieves excellent performance on structured datasets.

---

### Q2

**How would you explain a prediction to a fraud analyst?**

Réponse :

> I would use SHAP values or another explanation technique to show which features contributed most to the prediction, while making it clear that these explain the model's reasoning rather than proving causality.

---

### Q3

**How would you improve an existing fraud model?**

Réponse :

> I would first analyze errors, review data quality, improve feature engineering, evaluate label quality, and only then consider changing algorithms or tuning hyperparameters.

---

### Q4

**What is the biggest source of performance improvement in fraud detection?**

Réponse :

> In many real-world systems, the largest improvements come from better data quality, richer features and more reliable labels rather than from switching to a more complex algorithm.

---

### Q5

**Suppose XGBoost performs much better than Logistic Regression. Should you always deploy it?**

Réponse :

> Not necessarily. I would also compare inference latency, resource usage, explainability requirements, calibration, operational complexity and overall business impact before making a deployment decision.

---

# Étude de cas complète

Le recruteur vous dit :

> **Design the best fraud detection model.**

Une réponse solide :

1. Définir clairement le problème métier et le label.
2. Valider et explorer les données.
3. Construire des features comportementales, temporelles et relationnelles.
4. Établir une baseline (Logistic Regression).
5. Tester des modèles plus avancés (Random Forest, XGBoost).
6. Effectuer une validation adaptée au contexte temporel.
7. Optimiser les hyperparamètres.
8. Évaluer avec des métriques adaptées (PR-AUC, Precision, Recall, coût métier).
9. Choisir un seuil de décision.
10. Interpréter les prédictions (SHAP).
11. Déployer progressivement.
12. Surveiller les performances, la dérive et les indicateurs métier.

Cette réponse montre une vision **complète**, qui est précisément ce que recherchent des entreprises comme SentiLink.

---

# Ce qu'un excellent candidat retient

Un recruteur expérimenté ne cherche pas la personne qui connaît le plus d'algorithmes.

Il recherche celle qui sait :

* transformer un problème métier en problème de Machine Learning ;
* construire des données de qualité ;
* choisir un modèle adapté au contexte ;
* expliquer et surveiller le modèle en production ;
* collaborer avec des équipes Produit, Risque et Ingénierie.

C'est cette capacité de raisonnement de bout en bout qui distingue un Data Scientist expérimenté d'un candidat qui connaît uniquement les bibliothèques Python.

### Prochaine étape : **MODULE 27 — Études de cas complètes et simulations d'entretien SentiLink**

Nous jouerons de véritables entretiens techniques avec :

* des études de cas de fraude en temps réel ;
* des exercices de SQL et Python en direct ;
* des questions de conception de systèmes ("Machine Learning System Design") ;
* des questions comportementales et de communication ;
* des retours détaillés sur les réponses attendues par un recruteur.
