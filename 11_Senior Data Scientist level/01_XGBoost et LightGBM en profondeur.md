À partir de maintenant, nous allons aborder le niveau **Senior Data Scientist**, c'est-à-dire les sujets qui permettent réellement de se distinguer lors d'un entretien chez **SentiLink**, **Stripe**, **Ramp**, **Affirm**, **Block (Square)**, **Capital One**, etc.

---

# MODULE 8 – XGBoost et LightGBM en profondeur

> **Pourquoi ce module est-il important ?**

Dans les entreprises FinTech, **80 à 90 % des modèles de Machine Learning sur données tabulaires** utilisent encore des algorithmes de Gradient Boosting (XGBoost, LightGBM ou CatBoost), car ils offrent souvent de meilleures performances que les réseaux de neurones sur ce type de données.

---

# Chapitre 1 – Pourquoi XGBoost est-il si performant ?

Imaginons que nous souhaitions prédire si une transaction est frauduleuse.

### Premier arbre

Le premier arbre apprend une première règle simple.

```text
Montant > 5000 € ?

        Oui → Fraude
        Non → Légitime
```

Ce modèle est très imparfait.

---

### Deuxième arbre

Le deuxième arbre ne repart pas de zéro.

Il se concentre uniquement sur les erreurs du premier.

Par exemple :

```text
Nouvel appareil ?

        Oui → Fraude
        Non → Légitime
```

---

### Troisième arbre

Le troisième corrige les erreurs restantes.

```text
Adresse IP inhabituelle ?

        Oui → Fraude
```

---

À la fin :

```text
Prédiction finale

=
Arbre 1
+
Arbre 2
+
Arbre 3
+
...
+
Arbre N
```

Chaque arbre améliore progressivement la prédiction.

---

# Pourquoi cela fonctionne-t-il si bien ?

Chaque nouvel arbre apprend les **résidus**, c'est-à-dire les erreurs des arbres précédents.

Contrairement à Random Forest :

* Random Forest construit tous les arbres indépendamment.
* XGBoost construit les arbres **séquentiellement**.

C'est cette approche qui lui permet d'obtenir d'excellentes performances.

---

# Question d'entretien

**Quelle est la différence entre Random Forest et XGBoost ?**

### Réponse idéale

Random Forest construit plusieurs arbres indépendants en parallèle puis moyenne leurs prédictions afin de réduire la variance.

XGBoost construit les arbres de manière séquentielle. Chaque nouvel arbre corrige les erreurs des précédents, ce qui réduit progressivement le biais tout en utilisant des techniques de régularisation pour limiter le surapprentissage.

---

# Les hyperparamètres importants

Les recruteurs aiment souvent demander :

> **Quels hyperparamètres ajusteriez-vous dans XGBoost ?**

Les principaux sont :

| Hyperparamètre     | Rôle                                                    |
| ------------------ | ------------------------------------------------------- |
| `n_estimators`     | Nombre d'arbres                                         |
| `max_depth`        | Profondeur maximale des arbres                          |
| `learning_rate`    | Importance de chaque nouvel arbre                       |
| `subsample`        | Pourcentage de lignes utilisé par arbre                 |
| `colsample_bytree` | Pourcentage de variables utilisé                        |
| `min_child_weight` | Taille minimale d'un nœud                               |
| `gamma`            | Gain minimal nécessaire pour créer une nouvelle branche |
| `reg_alpha`        | Régularisation L1                                       |
| `reg_lambda`       | Régularisation L2                                       |

---

# 1. `learning_rate`

Le plus connu.

Exemple :

```text
learning_rate = 1
```

Chaque arbre corrige totalement les erreurs.

C'est souvent trop agressif.

---

Exemple :

```text
learning_rate = 0.05
```

Chaque arbre apporte une petite correction.

Le modèle apprend plus lentement mais généralise souvent mieux.

---

Question fréquente

**Pourquoi diminuer le learning rate ?**

Réponse :

Pour rendre l'apprentissage plus progressif et réduire le risque de surapprentissage.

---

# 2. `n_estimators`

Nombre d'arbres.

Exemple :

```text
50 arbres
```

↓

Apprentissage rapide

---

```text
1000 arbres
```

↓

Apprentissage plus riche mais plus coûteux.

---

Très souvent,

on augmente le nombre d'arbres lorsque l'on diminue le `learning_rate`.

---

# 3. `max_depth`

Contrôle la complexité des arbres.

Petit :

```text
max_depth = 3
```

↓

Modèle simple

---

Grand :

```text
max_depth = 20
```

↓

Risque d'overfitting.

---

Question

Pourquoi limiter la profondeur ?

Réponse :

Pour améliorer la capacité de généralisation et éviter que les arbres n'apprennent le bruit présent dans les données.

---

# 4. `subsample`

À chaque arbre,

on ne prend qu'une partie des observations.

Exemple :

```text
subsample = 0.8
```

↓

80 % des lignes.

Cela ajoute de l'aléatoire et améliore souvent la robustesse.

---

# 5. `colsample_bytree`

Même principe,

mais sur les variables.

Exemple :

```text
0.7
```

↓

Chaque arbre ne voit que 70 % des variables.

---

Pourquoi ?

Pour réduire la corrélation entre les arbres et limiter le surapprentissage.

---

# Régularisation

XGBoost intègre des mécanismes de régularisation, ce qui est l'une de ses grandes forces.

Les deux principaux paramètres sont :

* `reg_alpha` (L1) : favorise des modèles plus parcimonieux.
* `reg_lambda` (L2) : réduit les poids trop élevés et stabilise le modèle.

---

# Comment optimiser XGBoost ?

Une erreur fréquente consiste à tester toutes les combinaisons possibles (`GridSearchCV`), ce qui devient rapidement très coûteux.

En pratique, on privilégie souvent :

* `RandomizedSearchCV` pour explorer rapidement un espace de paramètres.
* **Optuna**, qui est aujourd'hui l'un des outils les plus utilisés pour optimiser efficacement les hyperparamètres.

---

# Quelles métriques optimiser ?

En détection de fraude, on évite généralement d'optimiser l'accuracy.

On privilégie :

* Recall
* Precision
* F1-score
* PR-AUC
* ROC-AUC (avec prudence)
* ou une métrique métier (coût financier, gain attendu).

---

# Le choix du seuil de décision

Par défaut :

```text
Probabilité > 0.5
```

→ Fraude.

Mais ce seuil est rarement optimal.

Exemple :

| Seuil | Recall | Precision |
| ----: | -----: | --------: |
|  0.50 |   0.81 |      0.92 |
|  0.30 |   0.93 |      0.78 |
|  0.15 |   0.98 |      0.55 |

Le choix dépend du coût des erreurs.

---

# Calibration des probabilités

Un score de 0,90 ne signifie pas forcément :

> "90 % de chances que ce soit une fraude."

Pour obtenir des probabilités plus fiables, on peut utiliser :

* Platt Scaling
* Isotonic Regression

Ces techniques sont utiles lorsque les probabilités servent à prendre des décisions métier.

---

# Feature Importance

XGBoost fournit plusieurs mesures d'importance :

* Gain
* Weight
* Cover

En pratique, ces mesures sont utiles pour une première analyse, mais elles ne remplacent pas des méthodes plus robustes comme **SHAP**, qui permettent d'expliquer chaque prédiction individuellement.

---

# Questions d'entretien

### Pourquoi XGBoost est-il souvent meilleur que Random Forest ?

Parce qu'il apprend de manière séquentielle en corrigeant les erreurs des arbres précédents et intègre des mécanismes de régularisation qui améliorent souvent les performances sur les données tabulaires.

---

### Pourquoi réduire le learning rate ?

Pour rendre l'apprentissage plus progressif, améliorer la généralisation et limiter le risque de surapprentissage.

---

### Pourquoi ne pas construire des arbres très profonds ?

Parce qu'ils mémorisent facilement le bruit des données et généralisent moins bien sur de nouvelles observations.

---

### Pourquoi utiliser Optuna ?

Parce qu'il explore intelligemment l'espace des hyperparamètres et nécessite généralement moins d'essais qu'une recherche exhaustive.

---

# Ce qu'un excellent candidat ajoute

Au-delà des performances du modèle, il explique qu'en production il surveille également :

* la stabilité des performances dans le temps ;
* la dérive des données (Data Drift) ;
* la dérive du concept (Concept Drift) ;
* la calibration des probabilités ;
* la latence de prédiction ;
* l'impact métier des décisions.

---

# Ce qui est souvent attendu chez SentiLink

Les recruteurs apprécient les candidats capables de raisonner ainsi :

> « Je ne choisis pas un modèle uniquement parce qu'il obtient la meilleure métrique. Je prends aussi en compte la latence, l'interprétabilité, la facilité de maintenance, le coût de calcul et les exigences métier. »

C'est ce type de réponse qui montre une compréhension de la **mise en production** d'un modèle, et pas seulement de son entraînement.

---

## Prochain chapitre

Nous passerons à un sujet encore plus spécifique à la fraude :

# **Feature Engineering avancé pour la détection de fraude**

Nous verrons comment les grandes FinTech construisent des variables très discriminantes, notamment :

* les variables temporelles ;
* les agrégations glissantes (*rolling features*) ;
* les graphes de relations entre utilisateurs ;
* les variables comportementales ;
* les signaux issus des appareils, des adresses IP et des réseaux.

C'est souvent le domaine qui apporte le plus de gain de performance sur les modèles de fraude.
