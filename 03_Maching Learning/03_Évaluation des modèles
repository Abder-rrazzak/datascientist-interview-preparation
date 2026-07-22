Nous entrons maintenant dans la partie qui fait souvent la différence entre un candidat **junior** et un candidat **confirmé**.

Lors des entretiens chez **SentiLink**, **Stripe**, **Meta**, **Amazon** ou **Google**, il est très fréquent que le recruteur présente un modèle qui obtient **99 % d'accuracy** et demande :

> **"Est-ce un bon modèle ?"**

La bonne réponse est souvent... **non**.

Pourquoi ? Parce que l'accuracy peut être très trompeuse, surtout en **détection de fraude**, où les transactions frauduleuses représentent souvent moins de **1 %** des données.

---

# MODULE 3 — Chapitre 11 : Évaluation des modèles

## Objectifs

À la fin de ce chapitre, vous serez capable de :

* choisir la bonne métrique selon le problème ;
* interpréter correctement les résultats ;
* expliquer pourquoi l'accuracy est souvent insuffisante ;
* comparer plusieurs modèles ;
* répondre aux questions classiques d'entretien.

---

# 1. La matrice de confusion

Considérons un modèle de détection de fraude.

Le modèle prédit :

```
Fraude
```

ou

```
Non fraude
```

Les résultats peuvent être résumés dans une **matrice de confusion**.

| Réalité    | Prédit Non fraude | Prédit Fraude |
| ---------- | ----------------: | ------------: |
| Non fraude |                TN |            FP |
| Fraude     |                FN |            TP |

---

## Les quatre cas

### True Positive (TP)

Le modèle prédit :

```
Fraude
```

et la transaction est réellement frauduleuse.

→ Bonne décision.

---

### True Negative (TN)

Le modèle prédit :

```
Non fraude
```

et ce n'est effectivement pas une fraude.

→ Bonne décision.

---

### False Positive (FP)

Le modèle annonce une fraude alors que la transaction est légitime.

Conséquence :

* carte bancaire bloquée ;
* client mécontent ;
* perte potentielle de revenus.

---

### False Negative (FN)

Le modèle laisse passer une vraie fraude.

Conséquence :

* perte financière ;
* risque réglementaire ;
* impact sur la réputation.

---

## Question d'entretien

Lequel est le plus grave :

False Positive

ou

False Negative ?

### Réponse

Cela dépend du contexte métier.

Dans la détection de fraude bancaire, les **False Negatives** sont souvent plus coûteux, car une fraude non détectée entraîne une perte financière.

Cependant, un excès de **False Positives** peut également être très problématique : blocage de clients légitimes, baisse de satisfaction et augmentation des coûts de traitement.

Un bon Data Scientist explique toujours ce compromis au lieu de répondre de manière absolue.

---

# 2. Accuracy

Formule

[
Accuracy=\frac{TP+TN}{TP+TN+FP+FN}
]

---

Exemple

100 000 transactions.

99 000 normales.

1 000 fraudes.

Un modèle prédit :

```
Tout est normal.
```

Accuracy :

```
99 %
```

Pourtant :

Le modèle ne détecte **aucune fraude**.

Il est donc inutilisable.

---

## Question

Pourquoi l'accuracy est-elle mauvaise en fraude ?

### Réponse

Parce que les classes sont très déséquilibrées.

---

# 3. Precision

Formule

[
Precision=\frac{TP}{TP+FP}
]

Question :

Parmi les transactions prédites comme frauduleuses,

combien le sont réellement ?

---

Exemple

100 alertes.

80 sont de vraies fraudes.

Precision :

```
80 %
```

---

Utilité

Réduire les faux positifs.

---

# 4. Recall

Formule

[
Recall=\frac{TP}{TP+FN}
]

Question :

Parmi toutes les fraudes,

combien avons-nous détectées ?

---

Exemple

100 vraies fraudes.

Le modèle en détecte 92.

Recall :

```
92 %
```

---

Utilité

Réduire les faux négatifs.

---

# Precision vs Recall

Très fréquente en entretien.

Imaginez un filet de pêche.

Petit filet :

↓

Très précis.

Mais

↓

Beaucoup de poissons échappent.

Grand filet :

↓

Capture presque tous les poissons.

Mais

↓

Ramasse aussi beaucoup d'algues.

C'est exactement le compromis entre **Precision** et **Recall**.

---

# Question

En fraude, faut-il privilégier Precision ou Recall ?

### Réponse

Il n'existe pas de réponse universelle.

* Si le coût d'une fraude est très élevé, on privilégiera souvent un meilleur **Recall**.
* Si le coût des faux positifs est élevé (blocage abusif de clients), il faudra améliorer la **Precision**.

La décision dépend toujours du coût métier.

---

# 5. F1-score

Le F1-score combine Precision et Recall.

[
F1 = 2 \times \frac{Precision \times Recall}
{Precision + Recall}
]

---

Pourquoi ?

Parce qu'un modèle :

Precision :

```
99 %
```

Recall :

```
10 %
```

n'est pas satisfaisant.

Le F1-score pénalise ce déséquilibre.

---

# 6. ROC Curve

La courbe ROC montre le compromis entre :

* True Positive Rate (Recall)
* False Positive Rate

Plus la courbe est proche du coin supérieur gauche,

meilleur est le modèle.

---

# 7. AUC

AUC = Area Under Curve.

Valeurs :

```
0.5
```

↓

Hasard.

---

```
0.8
```

↓

Bon modèle.

---

```
0.95
```

↓

Excellent modèle.

---

# Question

Pourquoi utiliser ROC-AUC ?

### Réponse

Parce qu'il évalue la capacité du modèle à distinguer les deux classes pour tous les seuils de décision.

---

# 8. Precision-Recall Curve

En fraude,

cette courbe est souvent **plus informative que la courbe ROC**.

Pourquoi ?

Parce que la classe positive est très rare.

C'est une question très appréciée en entretien.

---

# Réponse

Lorsque les classes sont fortement déséquilibrées, la courbe Precision-Recall met davantage en évidence les performances sur la classe minoritaire.

---

# 9. Choisir le seuil

Par défaut :

```
0.5
```

Mais rien n'oblige à utiliser ce seuil.

Exemple

```
0.2
```

↓

Recall ↑

Precision ↓

---

```
0.8
```

↓

Precision ↑

Recall ↓

---

Question

Pourquoi changer le seuil ?

Réponse

Pour adapter le comportement du modèle au coût métier des erreurs.

---

# 10. Validation croisée

Supposons

Accuracy

```
98 %
```

Mais

uniquement sur un seul jeu de test.

Le recruteur demandera :

> Êtes-vous certain que le modèle est robuste ?

La bonne réponse :

Utiliser une validation croisée (par exemple **Stratified K-Fold** en classification) pour vérifier que les performances sont stables sur plusieurs découpages.

---

# 11. Overfitting

Très fréquent.

Train

```
99 %
```

Test

```
72 %
```

Le modèle mémorise.

Il généralise mal.

---

# Solutions

* réduire la complexité du modèle ;
* ajouter de la régularisation ;
* obtenir davantage de données si possible ;
* effectuer une validation croisée ;
* arrêter l'entraînement plus tôt (selon l'algorithme) ;
* ajuster les hyperparamètres.

---

# 12. Underfitting

Train

```
60 %
```

Test

```
58 %
```

Le modèle est trop simple.

---

Solutions

* modèle plus complexe ;
* nouvelles variables (feature engineering) ;
* réduire une régularisation excessive.

---

# Question

Différence entre Overfitting et Underfitting ?

### Réponse

* **Overfitting** : le modèle apprend trop bien les données d'entraînement mais généralise mal.
* **Underfitting** : le modèle est trop simple et ne capture pas suffisamment les relations présentes dans les données.

---

# Cas pratique SentiLink

Dataset :

10 millions de transactions.

Fraude :

```
0.2 %
```

Modèle A

Accuracy

```
99.8 %
```

Recall

```
5 %
```

Precision

```
90 %
```

---

Modèle B

Accuracy

```
98 %
```

Recall

```
91 %
```

Precision

```
74 %
```

Question

Lequel choisir ?

---

### Réponse attendue

Dans un contexte de détection de fraude, le **modèle B** est souvent préférable :

* il détecte une très grande partie des fraudes (**Recall = 91 %**) ;
* son Accuracy plus faible est peu significative étant donné le fort déséquilibre des classes ;
* il génère davantage de faux positifs que le modèle A, mais ce compromis peut être acceptable si le coût d'une fraude non détectée est élevé.

En pratique, le choix final dépendrait également :

* du coût financier d'une fraude ;
* du coût opérationnel lié aux faux positifs ;
* de la capacité des équipes de revue manuelle à traiter les alertes.

---

# Questions d'entretien

### Pourquoi l'Accuracy est-elle trompeuse ?

Parce qu'elle peut rester très élevée même lorsqu'un modèle ne détecte presque aucune fraude.

---

### Quand utiliser le F1-score ?

Lorsque l'on souhaite équilibrer Precision et Recall, notamment sur des jeux de données déséquilibrés.

---

### Pourquoi utiliser la Precision-Recall Curve plutôt que la ROC Curve ?

Parce que la Precision-Recall Curve est généralement plus informative lorsque la classe positive est très rare.

---

### Que représente l'AUC ?

La capacité globale du modèle à distinguer les classes, indépendamment d'un seuil de décision particulier.

---

### Comment détecter un Overfitting ?

En comparant les performances sur les données d'entraînement et de test : un écart important est un signal d'alerte.

---

# Ce qui vient ensuite

Le prochain chapitre est particulièrement apprécié dans les entretiens de Data Scientist :

## Optimisation des hyperparamètres

Nous verrons en détail :

* `GridSearchCV` ;
* `RandomizedSearchCV` ;
* validation croisée stratifiée ;
* optimisation bayésienne (Optuna, Hyperopt) ;
* Early Stopping ;
* réglage des principaux hyperparamètres de **Random Forest**, **XGBoost**, **LightGBM** et **CatBoost** ;
* bonnes pratiques pour éviter le surapprentissage pendant le tuning.

C'est une compétence très recherchée, car elle montre que vous savez transformer un modèle "correct" en un modèle réellement performant et prêt pour la production.
