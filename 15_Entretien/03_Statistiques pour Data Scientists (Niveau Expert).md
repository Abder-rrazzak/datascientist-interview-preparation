Nous abordons maintenant le **MODULE 25**, qui est l'un des plus importants dans les entretiens de Data Scientist.

Contrairement à ce que beaucoup pensent, **les recruteurs évaluent davantage votre capacité à raisonner avec les statistiques qu'à réciter des formules**. Ils veulent savoir si vous comprenez **pourquoi** utiliser un test statistique, **quand** il est adapté et **comment** interpréter les résultats.

---

# MODULE 25 — Statistiques pour Data Scientists (Niveau Expert)

## Objectifs

À la fin de ce module, vous devrez être capable de :

* maîtriser les concepts fondamentaux de statistique ;
* choisir le bon test statistique selon le contexte ;
* interpréter correctement une p-value ;
* comprendre les notions de biais, variance et incertitude ;
* répondre aux questions d'entretien les plus fréquentes.

---

# Partie 1 — Pourquoi les statistiques sont-elles importantes ?

Le Machine Learning repose sur les statistiques.

Par exemple, lorsque vous entraînez un modèle, vous vous posez des questions comme :

* Les données sont-elles représentatives ?
* Les différences observées sont-elles réelles ou dues au hasard ?
* Mon modèle généralise-t-il correctement ?
* La nouvelle version est-elle réellement meilleure ?

Les statistiques permettent de répondre à ces questions.

---

# Partie 2 — Population vs Échantillon

## Population

Ensemble complet des observations.

Exemple :

Tous les clients d'une banque.

---

## Échantillon

Sous-ensemble de la population.

Exemple :

100 000 clients sélectionnés pour entraîner un modèle.

---

### Question d'entretien

> **Why do we use samples instead of entire populations?**

Bonne réponse :

> Because collecting data from the entire population is often impossible or too expensive. A representative sample allows us to estimate population characteristics efficiently.

---

# Partie 3 — Variables

### Variables numériques

Exemples :

* âge ;
* revenu ;
* montant d'une transaction.

---

### Variables catégorielles

Exemples :

* pays ;
* type de carte bancaire ;
* navigateur.

---

# Partie 4 — Moyenne, Médiane et Mode

Supposons :

```
10, 12, 14, 15, 500
```

### Moyenne

```
110.2
```

Fortement influencée par la valeur extrême.

### Médiane

```
14
```

Plus robuste.

### Mode

Valeur la plus fréquente.

---

### Question

> **When would you prefer the median over the mean?**

Réponse :

> When the data contains outliers or is highly skewed, because the median is more robust than the mean.

---

# Partie 5 — Variance et Écart-type

La variance mesure la dispersion des données.

L'écart-type est la racine carrée de la variance.

Plus l'écart-type est élevé, plus les données sont dispersées.

---

### Exemple

Deux clients :

```
A : 98, 100, 102
```

```
B : 10, 100, 190
```

Même moyenne.

Variances très différentes.

---

# Partie 6 — Distribution Normale

La célèbre courbe en cloche.

Propriétés approximatives :

* 68 % des valeurs dans ±1 écart-type ;
* 95 % dans ±2 écarts-types ;
* 99,7 % dans ±3 écarts-types.

---

### Question

> **Do all datasets follow a normal distribution?**

Réponse :

> No. Many real-world datasets, especially financial or fraud-related data, are highly skewed and do not follow a normal distribution.

---

# Partie 7 — Probabilité

La probabilité mesure la chance qu'un événement se produise.

Exemple :

Lancer une pièce équilibrée :

```
P(Pile) = 0.5
```

---

# Partie 8 — Probabilité conditionnelle

Très importante en fraude.

Exemple :

Quelle est la probabilité qu'une transaction soit frauduleuse sachant qu'elle provient d'un nouvel appareil ?

Notation :

```
P(Fraud | NewDevice)
```

---

# Partie 9 — Théorème de Bayes

Question parfois posée.

Formule :

[
P(A|B)=\frac{P(B|A)\times P(A)}{P(B)}
]

En pratique, Bayes permet de mettre à jour une probabilité lorsqu'une nouvelle information est disponible.

---

# Partie 10 — Corrélation

La corrélation mesure la relation entre deux variables.

Valeurs possibles :

```
-1
```

corrélation négative parfaite

```
0
```

aucune corrélation linéaire

```
+1
```

corrélation positive parfaite

---

### Attention

**Corrélation ≠ causalité**

Deux variables peuvent être corrélées sans qu'il existe un lien de cause à effet.

---

# Partie 11 — Hypothèses

Tout test statistique commence par deux hypothèses.

### H0

Hypothèse nulle.

Exemple :

Le nouveau modèle n'est pas meilleur.

### H1

Hypothèse alternative.

Le nouveau modèle est meilleur.

---

# Partie 12 — p-value

La question la plus fréquente.

> **What is a p-value?**

Bonne réponse :

> The p-value is the probability of observing results at least as extreme as those obtained if the null hypothesis were true.

Une petite p-value indique que les données observées seraient peu probables sous l'hypothèse nulle.

---

### Erreur classique

❌

"La p-value est la probabilité que H0 soit vraie."

C'est faux.

---

# Partie 13 — Niveau de Signification

Souvent :

```
α = 0.05
```

Si :

```
p < 0.05
```

On rejette H0.

Cela ne signifie pas que H1 est forcément vraie, mais que les données sont incompatibles avec H0 au seuil choisi.

---

# Partie 14 — Erreurs de Type I et II

### Type I

Faux positif.

On rejette H0 alors qu'elle est vraie.

---

### Type II

Faux négatif.

On ne rejette pas H0 alors qu'elle est fausse.

---

### En fraude

Un faux négatif peut laisser passer une fraude.

Un faux positif peut bloquer un client légitime.

Le coût métier de ces erreurs est différent.

---

# Partie 15 — Intervalle de Confiance

Question fréquente.

Un intervalle de confiance à 95 % fournit une plage de valeurs plausibles pour un paramètre inconnu, selon la méthode utilisée.

Il ne signifie pas que "la probabilité que le vrai paramètre soit dans cet intervalle est de 95 %".

---

# Partie 16 — Tests Statistiques

## t-test

Comparer les moyennes de deux groupes.

Exemple :

Ancien modèle

vs

Nouveau modèle.

---

## Chi-square

Comparer des variables catégorielles.

Exemple :

Pays

et

Fraude.

---

## ANOVA

Comparer les moyennes de plusieurs groupes.

---

## Mann-Whitney

Alternative non paramétrique au t-test lorsque les hypothèses du t-test ne sont pas satisfaites.

---

# Partie 17 — A/B Testing

Question très fréquente.

Supposons :

Version A

↓

Ancien modèle.

Version B

↓

Nouveau modèle.

On compare :

* taux de fraude détectée ;
* faux positifs ;
* temps de réponse ;
* taux de conversion.

---

### Question

> **What metrics would you compare in an A/B test for fraud detection?**

Bonne réponse :

> I would compare fraud detection rate, false-positive rate, false-negative rate, precision, recall, customer impact and business cost.

---

# Partie 18 — Biais et Variance

## High Bias

Le modèle est trop simple.

Sous-apprentissage.

---

## High Variance

Le modèle mémorise les données d'entraînement.

Surapprentissage.

---

### Illustration

```
High Bias

↓

Poor Train

Poor Test
```

```
High Variance

↓

Excellent Train

Poor Test
```

---

# Partie 19 — Overfitting

Question très fréquente.

Comment le détecter ?

Le modèle obtient :

```
Train Accuracy = 99 %
```

```
Test Accuracy = 78 %
```

Le modèle généralise mal.

---

### Solutions

* plus de données ;
* régularisation ;
* validation croisée adaptée ;
* réduction de la complexité ;
* early stopping.

---

# Partie 20 — Underfitting

Le modèle est mauvais partout.

```
Train = 65 %
```

```
Test = 63 %
```

Le modèle est trop simple ou les features sont insuffisantes.

---

# Partie 21 — Central Limit Theorem (CLT)

Question classique.

Le théorème central limite dit que, sous certaines conditions, la distribution de la moyenne d'échantillons indépendants tend vers une loi normale lorsque la taille de l'échantillon augmente.

Cela explique pourquoi de nombreux intervalles de confiance et tests reposent sur une approximation normale.

---

# Partie 22 — Bootstrap

Technique de rééchantillonnage.

Très utile lorsque l'on ne souhaite pas faire d'hypothèses fortes sur la distribution ou pour estimer l'incertitude d'une statistique.

---

# Partie 23 — Questions d'entretien

### Q1

**What is the difference between correlation and causation?**

Réponse :

> Correlation measures an association between variables, while causation means that one variable directly influences another.

---

### Q2

**Why is the median robust to outliers?**

Réponse :

> Because it depends only on the ordering of values, not on their magnitude.

---

### Q3

**Explain overfitting.**

Réponse :

> Overfitting occurs when a model learns noise and specific patterns from the training data instead of general relationships, leading to poor performance on unseen data.

---

### Q4

**What is the purpose of cross-validation?**

Réponse :

> Cross-validation provides a more reliable estimate of a model's generalization performance by evaluating it across multiple data splits.

---

### Q5

**When would you use a non-parametric test?**

Réponse :

> When the assumptions required by parametric tests, such as normality, are not satisfied or when the data are ordinal.

---

# Cas pratique SentiLink

Le recruteur vous demande :

> **Your new fraud model improves Precision from 0.84 to 0.86. Is it necessarily better?**

Réponse attendue :

> Not necessarily. I would also compare recall, PR-AUC, false-positive rate, false-negative rate, calibration, latency, and most importantly the business impact. A small increase in precision could hide a decrease in recall or a higher operational cost.

Cette réponse montre que vous raisonnez en **Data Scientist**, pas seulement en utilisateur de métriques.

---

# Les erreurs à éviter

* Confondre corrélation et causalité.
* Dire que la p-value est la probabilité que H0 soit vraie.
* Évaluer un modèle uniquement avec l'accuracy sur un problème déséquilibré.
* Choisir un test statistique sans vérifier ses hypothèses.
* Ignorer le contexte métier dans l'interprétation des résultats.

---

# Ce qu'un recruteur évalue

À travers ces questions, il cherche à vérifier que vous êtes capable de :

* interpréter correctement des résultats statistiques ;
* choisir des méthodes adaptées aux données ;
* comprendre les compromis entre modèles ;
* justifier vos décisions avec un raisonnement rigoureux.

---

## 🚀 Prochain module : MODULE 26 — Machine Learning avancé et questions d'entretien expertes

Nous couvrirons notamment :

* XGBoost, LightGBM et CatBoost en profondeur ;
* calibration des probabilités ;
* feature importance et SHAP ;
* pipelines de `scikit-learn` ;
* sélection de variables ;
* détection du data leakage ;
* optimisation des hyperparamètres ;
* **50 questions d'entretien avancées** inspirées de SentiLink, Google, Meta et Stripe.
