Nous allons maintenant réaliser une **étude de cas complète**, très proche de ce que vous pourriez rencontrer lors d'un entretien.

> **Objectif :** construire un système de détection de fraude de bout en bout, comme le ferait un Data Scientist en production.

---

# MODULE 4 – Chapitre 9 : Étude de cas complète – Construction d'un système de détection de fraude

## Contexte

Vous venez d'intégrer **SentiLink**.

Une banque partenaire vous fournit un historique de **15 millions de demandes de crédit**.

Votre mission est de développer un modèle capable de détecter les demandes frauduleuses **en temps réel**.

### Les données disponibles

| Colonne               | Description                    |
| --------------------- | ------------------------------ |
| application_id        | Identifiant de la demande      |
| customer_id           | Identifiant du client          |
| application_date      | Date de la demande             |
| amount                | Montant demandé                |
| income                | Revenu déclaré                 |
| age                   | Âge                            |
| employment_status     | Situation professionnelle      |
| email                 | Adresse e-mail                 |
| phone                 | Téléphone                      |
| ip_address            | Adresse IP                     |
| device_id             | Appareil utilisé               |
| country               | Pays                           |
| browser               | Navigateur                     |
| previous_applications | Nombre de demandes précédentes |
| approved              | Demande acceptée ou non        |
| is_fraud              | Variable cible                 |

---

# Étape 1 – Comprendre le problème métier

Avant d'écrire une seule ligne de code, il faut poser les bonnes questions.

### Questions à poser au métier

* Comment une fraude est-elle définie ?
* Comment les labels `is_fraud` sont-ils obtenus ?
* Combien de temps faut-il pour confirmer une fraude ?
* Quel est le coût moyen d'une fraude ?
* Quel est le coût d'un faux positif ?
* Quel est le temps de réponse maximal acceptable (SLA) ?
* Existe-t-il déjà des règles métier en production ?

> **En entretien**, commencer par ces questions montre que vous ne vous limitez pas à l'aspect technique.

---

# Étape 2 – Exploration des données (EDA)

## Vérifier les dimensions

```python
df.shape
df.info()
df.describe()
```

Questions à se poser :

* Combien de lignes ?
* Combien de colonnes ?
* Types de données ?
* Valeurs manquantes ?
* Variables catégorielles ?

---

## Distribution de la cible

```python
df["is_fraud"].value_counts(normalize=True)
```

Exemple :

| Classe     | Pourcentage |
| ---------- | ----------: |
| Non fraude |      99,6 % |
| Fraude     |       0,4 % |

➡️ Première conclusion : problème de **classification fortement déséquilibrée**.

---

## Valeurs manquantes

Exemple :

| Variable | % manquant |
| -------- | ---------: |
| income   |        8 % |
| phone    |        2 % |
| browser  |        0 % |

Décision :

* supprimer ?
* imputer ?
* créer une variable "information manquante" ?

---

# Étape 3 – Nettoyage des données

Exemples :

* suppression des doublons ;
* correction des formats de date ;
* normalisation des pays ;
* validation des adresses e-mail ;
* détection des valeurs aberrantes.

Exemple :

```python
df.drop_duplicates()
```

---

# Étape 4 – Feature Engineering

C'est ici que se crée une grande partie de la valeur.

### Variables dérivées

À partir de `application_date` :

* heure ;
* jour de la semaine ;
* week-end ou non.

---

À partir de `amount` :

* montant moyen historique ;
* ratio montant / revenu ;
* écart au montant moyen.

---

À partir de `device_id` :

* nouvel appareil ?
* nombre de comptes utilisant cet appareil.

---

À partir de `ip_address` :

* pays de l'IP ;
* VPN ou proxy détecté ;
* nombre de comptes utilisant cette IP.

---

À partir de `email` :

* domaine (`gmail.com`, `hotmail.com`, etc.) ;
* domaine jetable ?
* ancienneté estimée si disponible.

---

# Étape 5 – Encodage des variables

Variables numériques :

Aucun traitement particulier (hors normalisation si nécessaire).

Variables catégorielles :

* One-Hot Encoding ;
* Target Encoding (avec précautions contre le data leakage) ;
* CatBoost Encoding (selon le modèle).

---

# Étape 6 – Découpage Train / Validation / Test

Par exemple :

* 70 % entraînement ;
* 15 % validation ;
* 15 % test.

Si les données sont temporelles, on respecte l'ordre chronologique pour éviter les fuites de données.

---

# Étape 7 – Gérer le déséquilibre

Première approche :

* `class_weight` ;
* `scale_pos_weight` (XGBoost).

Ensuite comparer avec :

* SMOTE (sur le train uniquement) ;
* undersampling ;
* Balanced Random Forest.

---

# Étape 8 – Choisir un modèle de base

Toujours commencer par un modèle simple.

Exemple :

* Logistic Regression.

Pourquoi ?

Elle sert de **baseline**.

Ensuite tester :

* Random Forest ;
* XGBoost ;
* LightGBM ;
* CatBoost.

---

# Étape 9 – Évaluation

Calculer :

* Recall ;
* Precision ;
* F1-score ;
* ROC-AUC ;
* PR-AUC ;
* matrice de confusion.

Exemple :

| Modèle              | Recall | Precision | PR-AUC |
| ------------------- | -----: | --------: | -----: |
| Logistic Regression |   82 % |      64 % |   0,59 |
| Random Forest       |   90 % |      74 % |   0,71 |
| XGBoost             |   94 % |      82 % |   0,83 |

➡️ XGBoost est retenu.

---

# Étape 10 – Optimisation

Optimiser :

* `max_depth`
* `learning_rate`
* `n_estimators`
* `subsample`
* `colsample_bytree`

Avec :

* Random Search ;
* Grid Search ;
* Optuna (souvent privilégié pour son efficacité).

---

# Étape 11 – Choix du seuil

Le seuil par défaut est souvent 0,5.

Mais en fraude, ce n'est pas forcément optimal.

Exemple :

| Seuil | Recall | Precision |
| ----: | -----: | --------: |
|  0,50 |   88 % |      90 % |
|  0,30 |   94 % |      82 % |
|  0,20 |   97 % |      71 % |

Le choix dépend du compromis acceptable entre faux positifs et faux négatifs.

---

# Étape 12 – Interprétation

Utiliser SHAP.

Exemple :

| Variable                | Contribution |
| ----------------------- | -----------: |
| Ratio montant/revenu    |        +0,42 |
| Nouvel appareil         |        +0,21 |
| Adresse IP inhabituelle |        +0,18 |
| Compte récent           |        +0,15 |

Ces explications permettent aux analystes fraude de comprendre la décision.

---

# Étape 13 – Déploiement

Architecture simplifiée :

```text
Client
   │
API Gateway
   │
Fraud API (FastAPI)
   │
Feature Store
   │
XGBoost
   │
Réponse (score + décision)
```

Le modèle est sérialisé (`joblib`, `pickle` ou format natif XGBoost) puis chargé par l'API.

---

# Étape 14 – Monitoring

Surveiller :

* Recall ;
* Precision ;
* Latence ;
* Drift ;
* Taux d'erreur API ;
* Temps de réponse ;
* Volume de requêtes.

Déclencher une alerte si les performances chutent.

---

# Étape 15 – Amélioration continue

Chaque nouvelle fraude confirmée est ajoutée au jeu d'entraînement.

Le modèle est réentraîné périodiquement ou lors d'une dérive significative.

---

# Questions d'entretien

### Pourquoi commencer par une Logistic Regression ?

Parce qu'elle fournit une baseline simple, rapide et interprétable qui sert de référence avant d'utiliser des modèles plus complexes.

---

### Pourquoi tester plusieurs modèles ?

Parce qu'il n'existe pas de modèle universellement meilleur. Le choix dépend des données, des contraintes métier, de la latence et de l'interprétabilité.

---

### Pourquoi optimiser le seuil de décision ?

Parce que le seuil influence directement le compromis entre Recall et Precision. En fraude, le meilleur seuil est souvent différent de 0,5.

---

### Pourquoi conserver un jeu de test indépendant ?

Pour obtenir une estimation fiable des performances sur des données jamais vues pendant le développement.

---

# Cas d'entretien (type SentiLink)

**Question :**

> « Décrivez comment vous construiriez un système complet de détection de fraude. »

### Réponse idéale (synthèse)

1. Comprendre le besoin métier et les coûts associés aux erreurs.
2. Explorer et nettoyer les données.
3. Réaliser un feature engineering orienté fraude.
4. Découper les données sans fuite d'information.
5. Gérer le déséquilibre des classes.
6. Construire une baseline puis comparer plusieurs modèles.
7. Optimiser les hyperparamètres et le seuil de décision.
8. Évaluer avec des métriques adaptées (Recall, Precision, PR-AUC).
9. Interpréter les prédictions avec SHAP.
10. Déployer le modèle derrière une API rapide avec un Feature Store.
11. Monitorer les performances, la latence et le drift.
12. Réentraîner le modèle lorsque les données évoluent.

---

# Ce qui nous attend

Nous avons désormais terminé la partie **Fraude**.

Le prochain module sera consacré à :

# **AWS, Architecture Cloud, APIs et Déploiement**

Nous y verrons notamment :

* les services AWS essentiels pour un Data Scientist (**S3, EC2, RDS, Redshift, Lambda, SageMaker, ECR, ECS, CloudWatch, IAM**) ;
* comment déployer un modèle de Machine Learning sur AWS ;
* la création d'une API avec **FastAPI** ;
* la conteneurisation avec **Docker** ;
* les architectures scalables et tolérantes aux pannes ;
* les questions d'entretien les plus fréquentes sur le cloud.

Ce module est particulièrement important, car leur stack mentionne explicitement **AWS (EC2, S3, RDS, Redshift, etc.)**, et les recruteurs attendent qu'un Data Scientist comprenne non seulement les modèles, mais aussi leur mise en production dans un environnement cloud.
