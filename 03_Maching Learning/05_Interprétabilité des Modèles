Nous arrivons à un chapitre **essentiel** pour un poste de datascientist.

Dans la fintech, la banque, l'assurance et la détection de fraude, il ne suffit pas de construire un modèle performant. Il faut aussi être capable d'expliquer **pourquoi** le modèle prend une décision.

Imaginez qu'une banque demande :

> **"Pourquoi avez-vous refusé ce prêt ?"**

ou

> **"Pourquoi cette transaction a-t-elle été considérée comme frauduleuse ?"**

Répondre simplement :

> **"Parce que XGBoost l'a décidé."**

est inacceptable.

C'est pourquoi les recruteurs accordent une grande importance à **l'interprétabilité**.

---

# MODULE 3 – Chapitre 13 : Interprétabilité des Modèles

## Objectifs

À la fin de ce chapitre, vous serez capable de :

* expliquer les décisions d'un modèle ;
* interpréter les variables importantes ;
* utiliser SHAP ;
* comprendre les limites de la Feature Importance ;
* répondre aux questions d'entretien.

---

# Pourquoi interpréter un modèle ?

Deux modèles :

| Modèle | Accuracy |
| ------ | -------: |
| A      |     95 % |
| B      |     95 % |

Lequel choisir ?

Impossible de répondre.

Il faut savoir :

* quelles variables influencent les décisions ;
* si le modèle est cohérent avec la réalité métier ;
* s'il présente des biais ;
* s'il respecte les exigences réglementaires.

---

# Modèles interprétables

Quelques modèles sont naturellement explicables.

## Logistic Regression

Très simple.

Chaque variable possède un coefficient.

Exemple

| Variable         | Coefficient |
| ---------------- | ----------: |
| Montant          |        +2.4 |
| Ancienneté       |        -1.3 |
| Nombre de cartes |        +0.8 |

Interprétation :

* un montant élevé augmente le risque ;
* une ancienneté importante diminue le risque.

---

## Decision Tree

Très simple également.

Exemple

```text
Montant > 5000 ?

Oui
↓

Pays = RU ?

Oui
↓

Fraude
```

Chaque décision est visible.

---

# Question d'entretien

Pourquoi la Logistic Regression est-elle interprétable ?

### Réponse

Parce que chaque coefficient mesure l'effet d'une variable sur le log-odds de la classe positive. En exponentiant le coefficient, on obtient un **odds ratio**, plus facile à interpréter.

---

# Les modèles complexes

Random Forest

↓

500 arbres

---

XGBoost

↓

1000 arbres

---

Impossible à expliquer directement.

Il faut utiliser des outils spécialisés.

---

# 1. Feature Importance

Disponible pour :

* Random Forest
* XGBoost
* LightGBM
* CatBoost

Exemple

| Variable   | Importance |
| ---------- | ---------: |
| Montant    |       38 % |
| Pays       |       25 % |
| Device     |       20 % |
| Adresse IP |       10 % |
| Âge        |        7 % |

Le montant est la variable la plus influente au niveau global.

---

## Limites

La Feature Importance :

* donne une importance **globale** ;
* n'explique pas une prédiction individuelle ;
* peut être biaisée lorsque des variables sont très corrélées.

---

# Question

Que signifie une Feature Importance élevée ?

### Réponse

Cela indique que la variable contribue fortement aux décisions du modèle dans son ensemble, mais cela ne signifie pas nécessairement qu'elle est la cause d'une prédiction particulière.

---

# 2. Permutation Importance

Principe

Supposons

```text
Montant
```

On mélange aléatoirement cette colonne.

Si les performances chutent fortement :

↓

Cette variable est importante.

Si rien ne change :

↓

Elle apporte peu d'information.

---

## Avantages

* indépendante de l'algorithme ;
* facile à comprendre ;
* mesure directement l'impact sur les performances.

---

## Question

Pourquoi utiliser la Permutation Importance ?

### Réponse

Parce qu'elle évalue l'importance d'une variable en observant la dégradation des performances lorsqu'on détruit son information.

---

# 3. SHAP

Le standard actuel.

Très demandé en entretien.

SHAP signifie :

**SHapley Additive exPlanations**

Inspiré de la théorie des jeux.

---

## Principe

Chaque variable reçoit une contribution.

Exemple

Transaction

Montant :

```text
+0.45
```

Pays :

```text
+0.31
```

Nouvel appareil :

```text
+0.20
```

Historique :

```text
-0.10
```

Somme :

```text
Probabilité de fraude = 86 %
```

Chaque variable "pousse" la prédiction vers la fraude ou vers la non-fraude.

---

# Pourquoi SHAP est-il si populaire ?

Parce qu'il explique :

* chaque observation individuellement ;
* le modèle dans son ensemble.

Il fournit à la fois une interprétation **locale** et **globale**.

---

# Exemple

Transaction

Montant :

```text
15000 €
```

SHAP :

```text
+0.62
```

---

Pays :

```text
RU
```

SHAP :

```text
+0.28
```

---

Compte créé :

```text
Aujourd'hui
```

SHAP :

```text
+0.45
```

---

Le modèle considère cette transaction comme très risquée.

---

# Question

Pourquoi SHAP est-il largement utilisé ?

### Réponse

Parce qu'il permet d'expliquer de manière cohérente les prédictions de modèles complexes, à la fois globalement et individuellement.

---

# 4. Partial Dependence Plot (PDP)

Question plus avancée.

Principe

Faire varier une variable

et observer

l'évolution moyenne de la prédiction.

Exemple

Montant

↓

100

↓

1000

↓

10000

On observe comment le risque évolue en moyenne lorsque seul le montant change.

---

# 5. ICE Plot

Très proche du PDP.

Différence :

* PDP : moyenne sur toutes les observations ;
* ICE : une courbe par observation.

L'ICE permet de voir si l'effet d'une variable est homogène ou varie selon les individus.

---

# 6. LIME

Autre méthode d'explication locale.

Elle construit un modèle simple autour d'une observation donnée.

Aujourd'hui, SHAP est généralement préféré pour les modèles tabulaires, mais connaître LIME est un plus.

---

# Cas pratique SentiLink

Transaction

| Variable      | Valeur  |
| ------------- | ------- |
| Montant       | 12000   |
| Pays          | RU      |
| Device        | Nouveau |
| Âge du compte | 2 jours |

Le modèle prédit

```text
Fraude = 97 %
```

Le responsable demande :

> Pourquoi ?

Réponse

Avec SHAP :

| Variable        | Contribution |
| --------------- | -----------: |
| Montant         |        +0.50 |
| Pays            |        +0.20 |
| Compte récent   |        +0.18 |
| Nouvel appareil |        +0.09 |

Conclusion :

La décision est principalement expliquée par le montant élevé, le pays, l'ancienneté très faible du compte et l'utilisation d'un nouvel appareil.

---

# Attention : Corrélation ≠ Causalité

Question fréquente.

Supposons :

Les utilisateurs d'iPhone présentent moins de fraudes.

Cela ne signifie pas que :

```text
iPhone
```

réduit la fraude.

Il peut exister des variables cachées (niveau de revenu, ancienneté, type de clientèle, etc.).

Un modèle prédictif apprend des associations, pas des relations causales.

---

# Bonnes pratiques

Toujours vérifier :

* les biais ;
* les variables sensibles ;
* les corrélations fortes ;
* les risques réglementaires ;
* la stabilité des explications dans le temps.

---

# Questions d'entretien

### Pourquoi interpréter un modèle ?

Pour comprendre ses décisions, détecter d'éventuels biais, renforcer la confiance des utilisateurs et satisfaire les exigences réglementaires.

---

### Quelle différence entre Feature Importance et SHAP ?

* **Feature Importance** : importance globale des variables.
* **SHAP** : explication globale **et** individuelle de chaque prédiction.

---

### Pourquoi utiliser SHAP ?

Parce qu'il fournit des explications cohérentes pour des modèles complexes tout en permettant d'analyser une décision spécifique.

---

### Pourquoi la Feature Importance peut-elle être trompeuse ?

Parce qu'elle peut être influencée par des variables corrélées et ne permet pas d'expliquer une prédiction individuelle.

---

### Quelle différence entre PDP et ICE ?

* **PDP** montre l'effet moyen d'une variable.
* **ICE** montre cet effet pour chaque observation individuellement.

---

# Exercice type entretien

Vous entraînez un modèle **XGBoost** qui atteint :

* ROC-AUC : 0,98
* Recall : 94 %
* Precision : 82 %

Le directeur des risques demande :

> **Pourquoi cette transaction a-t-elle été refusée ?**

Comment répondez-vous ?

### Réponse attendue

1. Utiliser **SHAP** pour expliquer la prédiction individuelle.
2. Identifier les variables ayant le plus contribué à la décision (par exemple : montant élevé, appareil inconnu, compte récent, adresse IP inhabituelle).
3. Présenter ces contributions de manière compréhensible pour un public non technique.
4. Vérifier que la décision est cohérente avec les règles métier et qu'elle ne repose pas sur une variable problématique.

---

# Récapitulatif des méthodes d'interprétation

| Méthode                            | Globale | Locale |         Modèle spécifique ?         | Cas d'usage                             |
| ---------------------------------- | :-----: | :----: | :---------------------------------: | --------------------------------------- |
| Coefficients (Logistic Regression) |    ✅    |    ✅   |                 Oui                 | Modèles linéaires                       |
| Feature Importance                 |    ✅    |    ❌   |        Principalement arbres        | Vue d'ensemble                          |
| Permutation Importance             |    ✅    |    ❌   |                 Non                 | Comparaison des variables               |
| SHAP                               |    ✅    |    ✅   | Non (avec implémentations adaptées) | Standard en production                  |
| PDP                                |    ✅    |    ❌   |                 Non                 | Comprendre l'effet moyen d'une variable |
| ICE                                |    ❌    |    ✅   |                 Non                 | Variabilité entre observations          |
| LIME                               |    ❌    |    ✅   |                 Non                 | Explications locales                    |

---

# Fin du Module 3

Vous maîtrisez désormais :

* les principaux algorithmes de classification ;
* l'évaluation des modèles ;
* l'optimisation des hyperparamètres ;
* l'interprétation des prédictions.

Ces compétences couvrent l'essentiel des questions de Machine Learning posées en entretien.

---

# Prochain module : Détection de fraude (le plus spécifique à SentiLink)

C'est le module le plus directement lié au poste. Nous y aborderons :

* les différents types de fraude (application fraud, identity fraud, account takeover, synthetic identity, etc.) ;
* le **feature engineering** spécifique à la fraude ;
* les jeux de données extrêmement déséquilibrés ;
* les techniques de rééchantillonnage (SMOTE, undersampling, pondération des classes) ;
* la détection d'anomalies ;
* la détection en temps réel (streaming) ;
* les graphes de fraude (fraud rings) ;
* les dérives de données (*data drift* et *concept drift*) ;
* les pipelines de production utilisés dans les entreprises comme **SentiLink**, **Stripe** ou **PayPal**.

> **À mon avis, c'est le module qui apportera le plus de valeur pour réussir l'entretien, car il relie directement le Machine Learning aux problématiques métier que l'entreprise cherche à résoudre.
