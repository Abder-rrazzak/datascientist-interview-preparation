# MODULE 3 — Machine Learning (Niveau Expert)

# Chapitre 1 — Qu'est-ce que le Machine Learning ?

Le Machine Learning consiste à apprendre automatiquement une relation entre des variables d'entrée (**features**) et une variable cible (**target**) à partir de données.

Exemple chez SentiLink :

Entrée :

| Montant | Pays | Ancienneté | Device | Nb transactions |
| ------- | ---- | ---------- | ------ | --------------- |
| 120     | US   | 3 ans      | iPhone | 5               |

Sortie :

```text
Fraude = 0
```

---

Autre exemple

| Montant | Pays | Ancienneté | Device          | Nb transactions |
| ------- | ---- | ---------- | --------------- | --------------- |
| 12000   | RU   | 2 jours    | Nouvel appareil | 1               |

Sortie

```text
Fraude = 1
```

Le modèle apprend à distinguer automatiquement les transactions frauduleuses.

---

# Les grandes familles

## 1. Apprentissage supervisé

Les données sont étiquetées.

Exemple :

```text
Fraude = Oui
```

ou

```text
Fraude = Non
```

C'est le cas le plus courant chez SentiLink.

---

## 2. Apprentissage non supervisé

Aucune étiquette.

Le modèle cherche des groupes ou des anomalies.

Exemple :

Détection d'un nouveau type de fraude.

---

## 3. Reinforcement Learning

Rare dans la détection de fraude.

Le modèle apprend grâce à un système de récompense.

---

# Question d'entretien

Quelle différence entre apprentissage supervisé et non supervisé ?

### Réponse attendue

Le supervisé utilise des données étiquetées (avec une cible connue), tandis que le non supervisé travaille sans cible et cherche des structures ou des regroupements dans les données.

---

# Chapitre 2 — Le pipeline Machine Learning

Un excellent Data Scientist pense toujours en termes de pipeline.

```text
Collecte des données
        ↓
Nettoyage
        ↓
Analyse exploratoire (EDA)
        ↓
Feature Engineering
        ↓
Train / Validation / Test
        ↓
Choix du modèle
        ↓
Entraînement
        ↓
Évaluation
        ↓
Optimisation
        ↓
Déploiement
        ↓
Monitoring
```

---

# Question

Quelles sont les étapes d'un projet ML ?

### Réponse

Le recruteur attend que vous puissiez décrire l'ensemble du cycle de vie, depuis la collecte des données jusqu'au suivi du modèle en production.

---

# Chapitre 3 — Classification vs Régression

Très fréquent.

## Classification

La sortie est une catégorie.

Exemples :

```text
Fraude

Oui / Non
```

```text
Spam

Oui / Non
```

```text
Maladie

Positive / Négative
```

---

## Régression

La sortie est une valeur numérique.

Exemples

Prix d'une maison

```text
320000 €
```

Prévision du chiffre d'affaires

```text
1 250 000 €
```

---

# Question

La détection de fraude est-elle une classification ou une régression ?

Réponse

Une **classification binaire**.

---

# Chapitre 4 — Préparation des données

En entreprise, cette étape représente souvent plus de 70 % du temps.

Les principales tâches sont :

* suppression des doublons ;
* gestion des valeurs manquantes ;
* correction des types de données ;
* suppression des valeurs aberrantes (selon le contexte) ;
* création de nouvelles variables.

---

# Exemple

Avant

| montant |
| ------: |
|     120 |
|     130 |
|   12500 |

Le montant de **12500** n'est pas forcément une erreur. En fraude, il peut être très informatif.

Le rôle du Data Scientist est de comprendre le contexte métier avant de supprimer des observations.

---

# Chapitre 5 — Encodage des variables

Les modèles ne comprennent pas directement les chaînes de caractères.

Exemple

```text
Pays

France

USA

Canada
```

---

## One-Hot Encoding

```text
France

1 0 0
```

```text
USA

0 1 0
```

```text
Canada

0 0 1
```

Avec Scikit-Learn :

```python
from sklearn.preprocessing import OneHotEncoder

encoder = OneHotEncoder(handle_unknown="ignore")
```

---

## Label Encoding

```text
France = 0

USA = 1

Canada = 2
```

À utiliser principalement lorsque les catégories ont un ordre naturel ou pour certaines variables cibles. Pour les variables explicatives nominales, le One-Hot Encoding est souvent préférable.

---

# Question

Quand utiliser One-Hot Encoding ?

### Réponse

Pour les variables catégorielles sans ordre naturel (pays, navigateur, type d'appareil, etc.).

---

# Chapitre 6 — Normalisation

Deux notions importantes.

## Standardisation

Formule

```text
(x - moyenne) / écart-type
```

La moyenne devient proche de 0 et l'écart-type proche de 1.

---

## Min-Max Scaling

Transformation

```text
0 → 1
```

---

# Question

Quels algorithmes nécessitent généralement une mise à l'échelle des variables ?

### Réponse

Oui :

* Régression logistique
* SVM
* KNN
* Réseaux de neurones

Moins sensibles :

* Random Forest
* XGBoost
* LightGBM
* Decision Tree

---

# Chapitre 7 — Train / Validation / Test

Très important.

Un schéma courant :

```text
70 %

Train
```

```text
15 %

Validation
```

```text
15 %

Test
```

Le rôle de chaque ensemble :

* **Train** : apprentissage des paramètres du modèle.
* **Validation** : choix des hyperparamètres et comparaison des modèles.
* **Test** : estimation finale des performances sur des données jamais vues.

---

# Question

Pourquoi ne pas entraîner et tester sur le même jeu de données ?

### Réponse

Parce que le modèle pourrait mémoriser les données d'entraînement et donner une estimation artificiellement optimiste de ses performances. Il faut évaluer sa capacité de généralisation.

---

# Chapitre 8 — Validation croisée (Cross-Validation)

Au lieu de réaliser un seul découpage, on effectue plusieurs découpages.

Exemple :

```text
Fold 1

Train Test
```

```text
Fold 2

Train Test
```

...

Puis on moyenne les résultats.

En classification, on utilise souvent une **Stratified K-Fold** afin de conserver les proportions des classes dans chaque pli.

---

# Pourquoi ?

* Évaluation plus robuste.
* Moins dépendante d'un seul découpage.
* Meilleure estimation de la généralisation.

---

# Avec Scikit-Learn

```python
from sklearn.model_selection import cross_val_score

scores = cross_val_score(model, X, y, cv=5)
```

---

# Question

Pourquoi utiliser la validation croisée ?

### Réponse

Pour obtenir une estimation plus fiable des performances du modèle et réduire la variance liée au choix d'un unique jeu de validation.

---

# Chapitre 9 — Data Leakage

L'un des pièges les plus fréquents en entretien.

## Mauvaise pratique

Calculer une transformation (par exemple une moyenne, une normalisation ou un encodage) sur l'ensemble des données **avant** de séparer les jeux d'entraînement et de test.

Le modèle "voit" alors indirectement des informations provenant du jeu de test.

---

## Bonne pratique

1. Séparer les données (`train_test_split`).
2. Ajuster (`fit`) les transformations uniquement sur le jeu d'entraînement.
3. Appliquer (`transform`) ces transformations au jeu de validation et de test.

L'utilisation d'un `Pipeline` Scikit-Learn est la meilleure façon d'éviter ce problème.

---

# Questions d'entretien

1. Quelle différence entre apprentissage supervisé et non supervisé ?
2. Pourquoi la détection de fraude est-elle une classification ?
3. Quelles sont les étapes d'un pipeline Machine Learning ?
4. Pourquoi faut-il encoder les variables catégorielles ?
5. Quelle différence entre One-Hot Encoding et Label Encoding ?
6. Quels modèles nécessitent une normalisation ?
7. Pourquoi séparer les données en train, validation et test ?
8. À quoi sert la validation croisée ?
9. Qu'est-ce que le **data leakage** ?
10. Pourquoi utiliser un `Pipeline` Scikit-Learn ?

---

# Cas pratique (inspiré de SentiLink)

Vous recevez un jeu de données contenant :

| customer_age | country | amount | device          | fraud |
| ------------ | ------- | -----: | --------------- | ----- |
| 25           | US      |    120 | iPhone          | 0     |
| 42           | FR      |     85 | Android         | 0     |
| 31           | RU      |  12000 | Nouvel appareil | 1     |

**Question :** Décrivez les étapes de préparation avant d'entraîner un modèle.

### Réponse attendue

1. Vérifier les types de données.
2. Détecter et traiter les valeurs manquantes.
3. Supprimer ou analyser les doublons.
4. Étudier les distributions et les valeurs atypiques.
5. Encoder les variables catégorielles (`country`, `device`).
6. Créer des variables pertinentes (ancienneté du compte, fréquence des transactions, etc.).
7. Séparer les données en jeux d'entraînement, validation et test.
8. Construire un `Pipeline` incluant les transformations et le modèle.
9. Entraîner le modèle.
10. Évaluer les performances sur les données de validation puis de test.

---

## Ce que nous verrons dans la suite

La prochaine partie abordera les **algorithmes de classification** en détail :

* Régression Logistique ;
* Arbres de décision ;
* Random Forest ;
* Gradient Boosting ;
* XGBoost ;
* LightGBM ;
* CatBoost.

Pour chacun, nous verrons :

* le fonctionnement intuitif et mathématique ;
* les avantages et limites ;
* les hyperparamètres importants ;
* les cas d'utilisation ;
* les questions d'entretien les plus fréquentes ;
* et les raisons pour lesquelles ces modèles sont largement utilisés en **détection de fraude**.
