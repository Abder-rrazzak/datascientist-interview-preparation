Nous entrons maintenant dans **le cœur technique du poste : Machine Learning appliqué à la détection de fraude**.

# MODULE 19 — Machine Learning avancé pour la détection de fraude

## 🎯 Objectif du module

À la fin, vous devez pouvoir répondre clairement à des questions comme :

* Pourquoi choisir XGBoost plutôt qu'une régression logistique ?
* Pourquoi l'accuracy est-elle mauvaise pour la fraude ?
* Comment gérer un dataset très déséquilibré ?
* Comment choisir le seuil de décision ?
* Comment éviter le data leakage ?
* Comment valider un modèle dans le temps ?
* Comment interpréter une prédiction ?
* Pourquoi utiliser PR-AUC plutôt que ROC-AUC ?
* Comment détecter le drift ?
* Comment savoir si le modèle est réellement utile au business ?

---

# 1. Le problème de fraude

Imaginons :

```text
1 000 000 transactions
      ↓
995 000 non-fraudes
  5 000 fraudes
```

Donc :

```text
Fraude = 0,5 %
Non-fraude = 99,5 %
```

C'est un problème de **classification binaire fortement déséquilibré**.

On définit :

```text
y = 1 → fraude
y = 0 → non-fraude
```

Le modèle retourne :

```text
P(fraud | features)
```

Par exemple :

```text
0.02
0.15
0.87
0.94
```

---

# 2. Logistic Regression

C'est une excellente baseline.

Le modèle calcule :

[
P(y=1|X)=\sigma(w^TX+b)
]

où :

[
\sigma(z)=\frac{1}{1+e^{-z}}
]

En pratique :

```python
from sklearn.linear_model import LogisticRegression

model = LogisticRegression(
    class_weight="balanced",
    max_iter=1000
)

model.fit(X_train, y_train)
```

---

# 3. Pourquoi commencer par une baseline ?

Question :

> **Why would you start with Logistic Regression if you expect to use XGBoost?**

Réponse :

> **I would start with a simple baseline because it gives me a reference point for performance, training time, feature usefulness and calibration. A more complex model should provide a meaningful improvement over that baseline.**

C'est une excellente philosophie de Data Scientist.

Ne cherchez pas immédiatement le modèle le plus complexe.

---

# 4. Decision Tree

Un arbre prend des décisions successives :

```text
amount > 500 ?
       │
   ┌───┴───┐
  YES      NO
   │        │
device_new?
   │
 ...
```

Avantages :

* interprétable ;
* gère les interactions ;
* peu de preprocessing.

Inconvénient :

> un arbre unique peut facilement surapprendre.

---

# 5. Random Forest

Random Forest combine plusieurs arbres.

```text
             Dataset
                │
       ┌────────┼────────┐
       ▼        ▼        ▼
    Tree 1    Tree 2   Tree 3
       │        │        │
       └────────┼────────┘
                ▼
             Voting
                ↓
             Prediction
```

Avantages :

* robuste ;
* bonne baseline non linéaire ;
* capture les interactions.

Mais pour des données tabulaires structurées, **Gradient Boosting / XGBoost** est souvent un candidat particulièrement fort.

---

# 6. XGBoost

XGBoost construit les arbres séquentiellement.

```text
Tree 1
  ↓
Errors
  ↓
Tree 2
  ↓
Remaining errors
  ↓
Tree 3
  ↓
...
```

Chaque nouvel arbre cherche à améliorer les erreurs précédentes.

---

# 7. Pourquoi XGBoost pour la fraude ?

Question très probable :

> **Why would you choose XGBoost for fraud detection?**

Réponse :

> **Fraud detection often involves structured and heterogeneous tabular data with nonlinear relationships and interactions between features. Gradient-boosted trees such as XGBoost are strong candidates for this type of problem, while also providing useful feature importance and interpretability tools.**

Exemples d'interactions :

```text
new_device
+
high_amount
+
new_IP
+
unusual_location
```

Le risque peut augmenter fortement lorsque plusieurs signaux apparaissent ensemble.

---

# 8. XGBoost vs Logistic Regression

| Critère                 | Logistic Regression | XGBoost       |
| ----------------------- | ------------------- | ------------- |
| Non-linéarité           | Faible              | Excellente    |
| Interactions            | Limitées            | Excellentes   |
| Interprétation          | Très bonne          | Bonne         |
| Tabular data            | Bonne               | Excellente    |
| Training                | Rapide              | Plus complexe |
| Baseline                | ⭐⭐⭐⭐⭐               | ⭐⭐⭐⭐          |
| Performance potentielle | Bonne               | Très bonne    |

Mais attention :

> **XGBoost n'est pas automatiquement meilleur.**

Il faut le démontrer expérimentalement.

---

# 9. Train / Validation / Test

Une erreur classique :

```text
Dataset
 ↓
train_test_split
```

sans réfléchir à la dimension temporelle.

Dans la fraude, le temps est crucial.

---

# 10. Pourquoi le split aléatoire peut être dangereux ?

Supposons :

```text
Janvier → fraude
Février → fraude
Mars → fraude
Avril → fraude
```

Si vous mélangez toutes les transactions :

```text
Train
Janvier + Mars + Avril
```

et :

```text
Test
Février
```

le modèle peut apprendre des patterns futurs.

Cela peut donner une performance artificiellement élevée.

---

# 11. Validation temporelle

Une approche plus réaliste :

```text
TRAIN
Jan ───── Mars

VALIDATION
Avril

TEST
Mai
```

Puis :

```text
Train → passé
Validation → futur proche
Test → futur encore plus récent
```

C'est beaucoup plus proche de la production.

---

# 12. Question d'entretien

> **Why would you prefer a temporal split for fraud detection?**

Réponse :

> **Because fraud patterns evolve over time. A temporal split better reflects the production scenario, where the model is trained on historical information and predicts future transactions. It also helps prevent temporal leakage.**

---

# 13. Cross-validation

Pour un problème classique :

```python
from sklearn.model_selection import StratifiedKFold
```

La stratification permet de conserver approximativement la proportion des classes dans les folds.

Mais pour les données temporelles :

```python
TimeSeriesSplit
```

peut être plus approprié.

---

# 14. Pourquoi pas une Cross-Validation aléatoire ?

Parce que :

```text
Fold 1
→ données futures
```

peuvent indirectement influencer :

```text
Fold 2
→ données passées
```

Cela ne correspond pas à la situation réelle.

---

# 15. Accuracy : le piège classique

Supposons :

```text
99,5 % non-fraude
0,5 % fraude
```

Un modèle prédit :

```text
TOUT = non-fraude
```

Il obtient :

```text
Accuracy = 99,5 %
```

Ça semble excellent.

Mais :

```text
Recall fraude = 0 %
```

Le modèle ne détecte aucune fraude.

Donc :

> **Accuracy peut être complètement trompeuse.**

---

# 16. Confusion Matrix

Pour une classification :

```text
                   Actual
                Fraud   Legit
Prediction
Fraud           TP      FP

Legit           FN      TN
```

### TP

Fraude correctement détectée.

### FP

Transaction légitime bloquée.

### FN

Fraude non détectée.

### TN

Transaction légitime correctement acceptée.

---

# 17. Precision

[
Precision=\frac{TP}{TP+FP}
]

Question :

> Parmi les transactions que mon modèle considère comme frauduleuses, combien le sont réellement ?

---

# 18. Recall

[
Recall=\frac{TP}{TP+FN}
]

Question :

> Parmi toutes les fraudes existantes, combien mon modèle détecte-t-il ?

---

# 19. Exemple

Supposons :

```text
TP = 900
FP = 100
FN = 100
```

Alors :

[
Precision=\frac{900}{900+100}=90%
]

et :

[
Recall=\frac{900}{900+100}=90%
]

---

# 20. F1 Score

[
F1=2\frac{Precision \times Recall}
{Precision+Recall}
]

Il fournit un compromis entre Precision et Recall.

Mais attention :

> **F1 n'est pas automatiquement la meilleure métrique business.**

---

# 21. Pourquoi PR-AUC ?

Pour les problèmes fortement déséquilibrés, la courbe Precision-Recall est souvent particulièrement informative.

Elle se concentre sur :

```text
Precision
+
Recall
```

qui sont directement liés à la détection de la classe positive rare.

---

# 22. ROC-AUC vs PR-AUC

### ROC-AUC

Mesure la capacité du modèle à classer les positifs devant les négatifs sur différents seuils.

### PR-AUC

Met davantage l'accent sur la qualité de la détection de la classe positive.

Pour la fraude :

```text
Fraud = rare
```

Donc :

> **PR-AUC peut être plus informative que ROC-AUC.**

---

# 23. Question d'entretien

> **Would you optimize for Precision or Recall?**

⚠️ Il ne faut pas répondre automatiquement :

> Recall.

La réponse dépend du coût business.

---

# 24. Exemple business

Supposons :

### False Negative

Une fraude de :

```text
1 000 €
```

coûte potentiellement :

```text
1 000 €
```

### False Positive

Une transaction légitime bloquée entraîne :

* frustration ;
* perte de conversion ;
* support client ;
* potentiellement perte d'un client.

Donc nous avons deux coûts différents.

---

# 25. Fonction de coût

On peut conceptualiser :

[
Cost =
C_{FN}\times FN+
C_{FP}\times FP
]

Exemple :

```text
Cost(FN) = 1 000 €
Cost(FP) = 20 €
```

Le choix du seuil doit tenir compte de ces coûts.

---

# 26. Threshold

Le modèle produit :

```text
fraud_probability = 0.73
```

Mais il faut transformer cela en décision.

Par exemple :

```text
threshold = 0.5
```

Donc :

```text
0.73 > 0.5
→ fraud
```

Mais pourquoi 0.5 ?

Il n'y a aucune raison universelle.

---

# 27. Optimisation du seuil

On peut tester :

```text
0.10
0.20
0.30
0.40
0.50
0.60
0.70
0.80
0.90
```

et calculer :

```text
Precision
Recall
FP
FN
Cost
```

pour chaque seuil.

---

# 28. Exemple

| Threshold | Precision | Recall |
| --------: | --------: | -----: |
|      0,20 |      30 % |   95 % |
|      0,40 |      55 % |   88 % |
|      0,60 |      72 % |   78 % |
|      0,80 |      90 % |   55 % |

Le seuil optimal dépend du business.

---

# 29. Trois niveaux de décision

Pour SentiLink, une stratégie intéressante peut être :

```text
Probability
     │
     ▼
┌─────────────────────┐
│ < 0,30              │
│ PASS                │
└─────────────────────┘

┌─────────────────────┐
│ 0,30 – 0,80         │
│ REVIEW              │
└─────────────────────┘

┌─────────────────────┐
│ > 0,80              │
│ DECLINE             │
└─────────────────────┘
```

Ce n'est qu'un exemple.

Les vrais seuils doivent être déterminés par les données et les objectifs métier.

---

# 30. Calibration

Voici un concept très intéressant pour un entretien avancé.

Si le modèle dit :

```text
Probability = 0.80
```

on aimerait que parmi toutes les transactions auxquelles le modèle attribue environ 0,80, environ 80 % soient effectivement frauduleuses.

C'est la **calibration**.

---

# 31. Pourquoi la calibration est importante ?

Parce qu'un système de décision peut utiliser :

```text
fraud_probability
```

pour :

* accepter ;
* refuser ;
* demander une vérification ;
* envoyer en manual review.

Si les probabilités sont mal calibrées, les seuils deviennent moins fiables.

---

# 32. Comment calibrer ?

Avec par exemple :

```python
from sklearn.calibration import CalibratedClassifierCV
```

Méthodes courantes :

```text
Platt scaling
Isotonic regression
```

---

# 33. Class Imbalance

Supposons :

```text
99,5 % legitimate
0,5 % fraud
```

Le modèle peut être dominé par la classe majoritaire.

Solutions possibles :

```text
class weights
oversampling
undersampling
SMOTE
threshold optimization
anomaly detection
```

---

# 34. Class Weight

Avec XGBoost, on peut par exemple utiliser un poids de classe adapté au déséquilibre.

Avec Logistic Regression :

```python
LogisticRegression(
    class_weight="balanced"
)
```

Cela donne davantage d'importance aux exemples frauduleux.

---

# 35. SMOTE

SMOTE crée des exemples synthétiques de la classe minoritaire.

Mais attention :

⚠️ **Ne jamais appliquer SMOTE avant le split train/test.**

Mauvais :

```text
Dataset
 ↓
SMOTE
 ↓
Train/Test Split
```

Cela peut provoquer du leakage.

Correct :

```text
Dataset
 ↓
Train/Test Split
 ↓
SMOTE uniquement sur Train
 ↓
Training
```

---

# 36. Pipeline sklearn

La bonne pratique :

```python
from imblearn.pipeline import Pipeline
from imblearn.over_sampling import SMOTE
from sklearn.preprocessing import StandardScaler
from sklearn.linear_model import LogisticRegression

pipeline = Pipeline([
    ("scaler", StandardScaler()),
    ("smote", SMOTE()),
    ("model", LogisticRegression())
])
```

Le preprocessing est ainsi effectué correctement dans chaque fold lors de la validation.

---

# 37. Data Leakage

C'est une question **très probable chez SentiLink**.

Le leakage signifie que le modèle apprend une information qu'il ne devrait pas connaître au moment de la prédiction.

---

## Exemple

Supposons une feature :

```text
customer_fraud_rate
```

Vous la calculez à partir de toute l'historique :

```text
Janvier → transaction
Février → fraude
Mars → fraude
```

Si vous prédisez Janvier en utilisant des informations de Février et Mars :

❌ leakage.

---

# 38. Exemple encore plus simple

Vous voulez prédire :

```text
fraud
```

et vous utilisez comme feature :

```text
fraud_investigation_result
```

C'est directement lié au label.

Donc :

❌ impossible en production.

---

# 39. Comment éviter le leakage ?

Toujours poser la question :

> **Would this feature have been available at the exact moment of prediction?**

C'est une excellente phrase à mémoriser.

---

# 40. Feature Engineering

C'est probablement l'un des aspects les plus importants de ce poste.

Le modèle n'est pas nécessairement le principal avantage compétitif.

La qualité des features peut être déterminante.

---

## Feature 1 — Transaction frequency

```text
transactions_last_10min
```

---

## Feature 2 — Amount velocity

```text
amount_last_1h
```

---

## Feature 3 — Device novelty

```text
is_new_device
```

---

## Feature 4 — Device sharing

```text
unique_customers_per_device
```

---

## Feature 5 — IP sharing

```text
unique_customers_per_ip
```

---

## Feature 6 — Geographic inconsistency

```text
country_changed
```

---

## Feature 7 — Amount anomaly

```text
current_amount / historical_average_amount
```

---

# 41. Exemple de feature engineering

Supposons :

```text
historical_avg = 100 €
current_amount = 800 €
```

Alors :

[
ratio=\frac{800}{100}=8
]

Feature :

```text
amount_to_avg_ratio = 8
```

Cette feature peut être beaucoup plus informative que :

```text
amount = 800
```

---

# 42. Feature Interaction

Supposons :

```text
new_device = 1
```

seul :

```text
fraud probability = 2 %
```

Et :

```text
high_amount = 1
```

seul :

```text
fraud probability = 3 %
```

Mais :

```text
new_device = 1
+
high_amount = 1
```

peut produire :

```text
fraud probability = 25 %
```

Cela illustre une **interaction entre variables**.

Les modèles de boosting sont particulièrement intéressants pour capturer ce type de relations non linéaires.

---

# 43. Feature Importance

Avec XGBoost, vous pouvez examiner :

```text
feature_importances_
```

Mais attention :

> Feature importance ≠ causalité.

Si :

```text
device_customer_count
```

est important, cela signifie que cette feature contribue aux prédictions du modèle.

Cela ne signifie pas qu'elle **cause** la fraude.

---

# 44. SHAP

SHAP est particulièrement intéressant pour expliquer les prédictions.

Supposons :

```text
Prediction
fraud_probability = 0.91
```

SHAP peut nous aider à comprendre :

```text
+0.32 → new_device
+0.24 → unusual_IP
+0.18 → high_amount
-0.05 → long_customer_history
```

On peut alors expliquer pourquoi le score est élevé.

---

# 45. Question d'entretien

> **How would you explain a fraud prediction to a risk analyst?**

Réponse :

> **I would provide the model score along with the main contributing features, for example an unusually high transaction amount, a new device, and an unusual IP pattern. I would use SHAP or another model explanation technique while clearly distinguishing model explanation from causal reasoning.**

Très bonne réponse.

---

# 46. Attention à l'interprétation de SHAP

Il ne faut pas dire :

> "SHAP tells us why the customer committed fraud."

❌

SHAP explique plutôt :

> **Why the model produced this prediction given the observed features.**

---

# 47. Hyperparameter Tuning

XGBoost possède beaucoup de paramètres.

Les plus importants à connaître :

```text
n_estimators
max_depth
learning_rate
subsample
colsample_bytree
min_child_weight
reg_alpha
reg_lambda
```

---

# 48. `max_depth`

Contrôle la profondeur des arbres.

```text
max_depth élevé
→ modèle complexe
→ risque de overfitting
```

```text
max_depth faible
→ modèle plus simple
→ risque d'underfitting
```

---

# 49. `learning_rate`

Petit learning rate :

```text
0.01
```

nécessite généralement davantage d'arbres.

Grand :

```text
0.3
```

apprend plus rapidement mais peut être moins stable.

---

# 50. `n_estimators`

Nombre d'arbres.

Une stratégie fréquente :

```text
learning_rate ↓
n_estimators ↑
```

avec early stopping.

---

# 51. Early Stopping

Exemple conceptuel :

```text
Training
  ↓
Round 1
  ↓
Round 2
  ↓
...
  ↓
Validation performance stops improving
  ↓
STOP
```

Cela permet de limiter le surapprentissage et de déterminer un nombre d'arbres raisonnable.

---

# 52. Overfitting

Exemple :

```text
Train PR-AUC = 0.99
Validation PR-AUC = 0.72
```

⚠️ Le modèle surapprend.

Un modèle plus sain pourrait être :

```text
Train = 0.85
Validation = 0.82
```

La deuxième situation est potentiellement préférable.

---

# 53. Question d'entretien

> **Your training performance is excellent but validation performance is poor. What do you do?**

Réponse structurée :

> I would first check for data leakage and whether the train-validation split reflects the production setting. Then I would investigate model complexity, feature leakage, duplicated entities, and distribution differences. I could reduce model complexity, increase regularization, improve the validation strategy, or collect more representative data.

⭐ Très bonne réponse.

---

# 54. Modèle vs données

Une idée importante pour SentiLink :

> **Better data and better features can be more valuable than a more sophisticated model.**

Par exemple :

```text
XGBoost + weak features
```

peut être inférieur à :

```text
Logistic Regression + excellent features
```

---

# 55. Une question très importante pour SentiLink

> **How would you improve a fraud model without changing the algorithm?**

Excellente réponse :

```text
1. Improve labels
2. Add better data sources
3. Improve feature engineering
4. Reduce leakage
5. Improve temporal validation
6. Handle missing data
7. Improve threshold selection
8. Improve calibration
9. Monitor drift
10. Improve data quality
```

C'est très proche de la philosophie exprimée dans l'offre :

> **"integration of new data sources and inventive feature engineering."**

---

# 56. Le cycle ML complet

Vous devez avoir cette architecture mentale :

```text
Business Problem
      ↓
Data Acquisition
      ↓
Data Quality
      ↓
Label Definition
      ↓
Feature Engineering
      ↓
Train / Validation / Test
      ↓
Baseline
      ↓
Model Training
      ↓
Evaluation
      ↓
Threshold Optimization
      ↓
Calibration
      ↓
Interpretability
      ↓
Deployment
      ↓
Monitoring
      ↓
Retraining
```

---

# 57. Question ultime

Le recruteur vous demande :

> **Your model has a PR-AUC of 0.85. Is that a good model?**

❌ Ne répondez pas :

> Yes.

La bonne réponse :

> **It depends on the baseline, the data distribution, the validation methodology and the business objective. I would compare it against a simple baseline and previous production models, then evaluate precision, recall, calibration, false-positive costs and business impact at the operating threshold.**

🔥 Très bonne réponse de Data Scientist.

---

# 58. Cas pratique complet SentiLink

Imaginez :

```text
10 millions transactions
Fraude = 0,3 %
```

Vous construisez :

```text
Model A = Logistic Regression
Model B = Random Forest
Model C = XGBoost
```

Résultats :

| Model         |   PR-AUC |   Recall | Precision |
| ------------- | -------: | -------: | --------: |
| Logistic      |     0,42 |     71 % |      11 % |
| Random Forest |     0,61 |     78 % |      18 % |
| XGBoost       | **0,74** | **84 %** |  **27 %** |

Vous choisissez XGBoost.

Mais le travail n'est **pas terminé**.

Il faut encore vérifier :

```text
Calibration
Threshold
False positives
Latency
Data drift
Feature stability
Business impact
```

---

# 59. Question : XGBoost est meilleur. On le déploie ?

Réponse :

> **Not immediately. I would first verify that the improvement is statistically and operationally meaningful, validate the model on a realistic temporal holdout, check calibration and latency, perform error analysis, and evaluate its business impact. I would then deploy it progressively, for example in shadow mode or through a canary deployment.**

Très bonne réponse.

---

# 60. Les 15 questions ML à savoir répondre

### Q1

**Why XGBoost?**

→ Excellent pour données tabulaires, interactions et non-linéarités.

### Q2

**Why not accuracy?**

→ Classe fraude rare.

### Q3

**Precision vs Recall?**

→ Dépend du coût FP/FN.

### Q4

**Why PR-AUC?**

→ Plus informative pour une classe positive rare.

### Q5

**Why temporal split?**

→ Évite leakage temporel et reflète production.

### Q6

**What is data leakage?**

→ Information indisponible au moment de la prédiction.

### Q7

**How to handle imbalance?**

→ Class weights, sampling, thresholding, etc.

### Q8

**What is calibration?**

→ Correspondance entre probabilités prédites et fréquences observées.

### Q9

**What is SHAP?**

→ Explication des contributions des features à une prédiction.

### Q10

**What is overfitting?**

→ Excellent sur train, mauvais sur données non vues.

### Q11

**How to reduce overfitting?**

→ Regularization, simplification, plus de données, validation correcte, early stopping.

### Q12

**Why feature engineering?**

→ Capturer les signaux métier utiles à la détection.

### Q13

**How do you choose threshold?**

→ Coûts métier + Precision/Recall + contraintes opérationnelles.

### Q14

**How do you monitor the model?**

→ Performance, drift, calibration, data quality et business KPIs.

### Q15

**How do you improve the model without changing algorithm?**

→ Données, labels, features, validation, leakage, seuils, monitoring.

---

# 🧪 Exercice d'entretien — À vous de répondre

Imaginez maintenant que je suis le recruteur de SentiLink.

### Question 1

> **Fraud represents only 0.2% of all transactions. Your model achieves 99.8% accuracy. Would you consider this a good model? Why or why not?**

### Question 2

> **You have two models:**
>
> **Model A:** Precision = 90%, Recall = 40%
>
> **Model B:** Precision = 40%, Recall = 90%
>
> **Which one would you deploy?**

### Question 3

> **Your model performs extremely well on random train/test split but poorly when evaluated on the following month's data. What could be happening?**

### Question 4

> **A fraud analyst asks you: "Why did the model flag this transaction?" How would you answer?**

### Question 5

> **You have a very strong XGBoost model but it takes 300 ms to produce a prediction, while the API requirement is below 100 ms. What would you do?**

---

## 🎯 Ce que je veux que vous pratiquiez

Répondez aux **5 questions en anglais**, comme si vous étiez devant le recruteur.

Ne cherchez pas à produire des réponses parfaites : **je corrigerai ensuite chaque réponse**, en distinguant :

* contenu technique ;
* anglais professionnel ;
* raisonnement Data Scientist ;
* vocabulaire à utiliser ;
* erreurs à éviter ;
* **note /20 pour chaque réponse**.

Ensuite nous passerons au **MODULE 20 — Fraud Detection approfondie**, où nous construirons réellement un système de détection de fraude avec **dataset → feature engineering → déséquilibre → XGBoost → PR-AUC → seuil → SHAP → monitoring**, comme dans un véritable projet SentiLink.
