Nous entrons maintenant dans **l'un des domaines qui différencie un Data Scientist "Notebook" d'un Data Scientist Production**.

> Beaucoup de candidats savent entraîner un modèle.
>
> Peu savent expliquer **comment ce modèle arrive automatiquement en production**.

C'est précisément le rôle du **CI/CD** et du **MLOps**.

---

# MODULE 5 – Chapitre 4 : CI/CD et MLOps

## Objectifs

À la fin de ce chapitre, vous serez capable de :

* comprendre ce qu'est une pipeline CI/CD ;
* expliquer GitHub Actions ;
* automatiser les tests ;
* déployer automatiquement une API ;
* comprendre le cycle de vie complet d'un modèle.

---

# 1. Le problème

Imaginez :

Vous modifiez une seule ligne de code.

Comment être certain que :

* rien n'est cassé ?
* les tests passent ?
* l'API fonctionne encore ?
* le modèle est correctement chargé ?

Faire ces vérifications à la main est lent et source d'erreurs.

---

# 2. Qu'est-ce que le CI/CD ?

CI = **Continuous Integration**

CD = **Continuous Delivery** ou **Continuous Deployment**

L'idée est simple :

Chaque modification du code déclenche automatiquement une série d'étapes.

---

## Pipeline simplifiée

```text
Développeur
      │
      ▼
Git Push
      │
      ▼
GitHub
      │
      ▼
Tests automatiques
      │
      ▼
Construction Docker
      │
      ▼
Déploiement
      │
      ▼
Production
```

---

# 3. Continuous Integration (CI)

À chaque `git push` :

Le système exécute automatiquement :

* les tests unitaires ;
* les tests d'intégration ;
* les vérifications de qualité du code ;
* la construction du projet.

Si un test échoue :

❌ Le déploiement est bloqué.

---

# Exemple

Vous modifiez :

```python
predict()
```

GitHub Actions lance automatiquement :

```text
pytest
```

Résultat :

```
35 tests réussis
```

Le projet peut continuer.

---

# 4. Continuous Delivery

Une fois les tests validés :

Le système prépare automatiquement :

* l'image Docker ;
* les fichiers nécessaires ;
* le package de déploiement.

Mais le déploiement en production reste **manuel**.

---

# 5. Continuous Deployment

Ici,

aucune intervention humaine.

Après validation :

```text
Git Push

↓

Tests

↓

Docker

↓

AWS

↓

Production
```

Le nouveau modèle est directement disponible.

---

# Question d'entretien

Quelle différence entre Continuous Delivery et Continuous Deployment ?

### Réponse

* **Continuous Delivery** : le logiciel est prêt à être déployé, mais une validation humaine est nécessaire.
* **Continuous Deployment** : le déploiement est entièrement automatisé après le succès des tests.

---

# 6. GitHub Actions

GitHub Actions est l'outil CI/CD le plus courant.

À chaque push :

GitHub lit un fichier YAML.

Exemple :

```yaml
name: CI

on:
  push:

jobs:
  test:
    runs-on: ubuntu-latest

    steps:

      - uses: actions/checkout@v4

      - uses: actions/setup-python@v5

      - run: pip install -r requirements.txt

      - run: pytest
```

Vous n'avez pas besoin de connaître cette syntaxe par cœur, mais il faut comprendre son rôle.

---

# 7. Tests unitaires

Exemple :

```python
def predict(amount):

    return amount > 1000
```

Test :

```python
assert predict(1500) == True
assert predict(200) == False
```

Chaque modification est automatiquement vérifiée.

---

# 8. Qualité du code

Avant de déployer,

on vérifie également :

* formatage (`black`) ;
* qualité (`flake8`, `ruff`) ;
* sécurité (`bandit`) ;
* couverture de tests (`coverage`).

---

# 9. Construction Docker

Si tous les tests passent :

```text
Docker Build
```

est exécuté automatiquement.

Puis :

```text
fraud-api:1.3
```

est créé.

---

# 10. Publication dans Amazon ECR

Pipeline :

```text
Docker Build

↓

Amazon ECR

↓

Nouvelle image disponible
```

---

# 11. Déploiement automatique

Ensuite :

```text
Amazon ECS

↓

Nouvelle version

↓

API disponible
```

---

# 12. Rollback

Supposons :

Version 1.4

↓

Erreur critique.

Le système revient immédiatement à :

```text
Version 1.3
```

C'est pourquoi les versions sont conservées.

---

# 13. Versionner les modèles

Ne jamais remplacer :

```text
model.pkl
```

Préférez :

```text
model_v1.pkl

model_v2.pkl

model_v3.pkl
```

Encore mieux :

```
fraud_model_2026_07_23.pkl
```

Ou utiliser un registre de modèles (Model Registry).

---

# 14. Versionner les données

Le code change.

Les données aussi.

En production, il faut savoir :

* avec quelles données un modèle a été entraîné ;
* quelle version du code a été utilisée ;
* quelles hyperparamètres ont été choisis.

---

# 15. Le rôle du Model Registry

Un registre de modèles conserve :

* la version ;
* les métriques ;
* la date d'entraînement ;
* les paramètres ;
* le statut (staging, production, archivé).

Exemple :

| Version | Recall | Statut     |
| ------- | ------ | ---------- |
| 1.2     | 91 %   | Archivé    |
| 1.3     | 94 %   | Production |
| 1.4     | 95 %   | Staging    |

---

# 16. Pipeline MLOps complète

```text
Collecte des données
          │
          ▼
Validation
          │
          ▼
Feature Engineering
          │
          ▼
Entraînement
          │
          ▼
Évaluation
          │
          ▼
Model Registry
          │
          ▼
Docker
          │
          ▼
GitHub Actions
          │
          ▼
Amazon ECR
          │
          ▼
Amazon ECS
          │
          ▼
API FastAPI
          │
          ▼
Monitoring
```

---

# Cas pratique SentiLink

Le recruteur demande :

> « Vous améliorez votre modèle. Comment le mettez-vous en production ? »

### Réponse idéale

1. Développer et tester localement.
2. Envoyer le code sur GitHub.
3. Lancer automatiquement les tests via GitHub Actions.
4. Construire une nouvelle image Docker.
5. Publier cette image dans Amazon ECR.
6. Déployer progressivement sur ECS (Canary ou Blue/Green).
7. Surveiller les performances et la latence.
8. Revenir à la version précédente si un problème est détecté.

---

# Questions d'entretien

### Pourquoi utiliser CI/CD ?

Pour automatiser les tests, réduire les erreurs humaines et accélérer les mises en production.

---

### Pourquoi tester automatiquement ?

Pour détecter rapidement les régressions avant qu'elles n'atteignent la production.

---

### Pourquoi versionner les modèles ?

Pour assurer la traçabilité, faciliter les comparaisons et permettre un retour rapide à une version stable.

---

### Pourquoi utiliser GitHub Actions ?

Parce qu'il automatise les étapes de validation, de construction et de déploiement directement depuis GitHub.

---

### Pourquoi conserver plusieurs versions d'un modèle ?

Pour comparer leurs performances et effectuer un rollback en cas de problème.

---

# Questions avancées

### Si un nouveau modèle est meilleur en ROC-AUC mais plus lent, que faites-vous ?

Je compare les performances globales en tenant compte des contraintes métier. En détection de fraude temps réel, un léger gain de précision ne justifie pas toujours une augmentation importante de la latence. Je mesure le compromis avant toute mise en production.

---

### Comment déployer un nouveau modèle sans risque ?

En utilisant un **Canary Deployment** ou un **Shadow Deployment**, afin de comparer le nouveau modèle au modèle actuel avant un déploiement complet.

---

# Ce que les recruteurs recherchent

Ils veulent savoir si vous comprenez qu'un modèle n'est pas seulement un fichier `.pkl`.

Un modèle en production implique :

* du code versionné ;
* des tests automatisés ;
* une API ;
* un conteneur Docker ;
* une infrastructure cloud ;
* un monitoring continu ;
* une stratégie de déploiement et de retour arrière.

---

# Conclusion du Jour 5

Vous maîtrisez désormais les bases de l'écosystème de production :

* AWS (S3, EC2, RDS, Redshift, IAM, CloudWatch, SageMaker) ;
* FastAPI pour exposer un modèle ;
* Docker pour garantir la reproductibilité ;
* CI/CD et GitHub Actions pour automatiser les déploiements ;
* les principes fondamentaux du MLOps.

Ces connaissances sont souvent suffisantes pour répondre aux questions d'architecture posées lors d'un entretien de Data Scientist orienté production.

---

# Prochaine étape : Jour 6 – Projet pratique complet

Le prochain module sera entièrement pratique. Nous construirons **un pipeline complet de bout en bout**, comme lors d'une mission réelle :

1. Lecture d'un jeu de données de fraude.
2. Analyse exploratoire (EDA).
3. Nettoyage des données.
4. Feature engineering.
5. Entraînement de plusieurs modèles.
6. Gestion du déséquilibre des classes.
7. Optimisation des hyperparamètres.
8. Interprétation avec SHAP.
9. Sauvegarde du modèle.
10. Création d'une API FastAPI.
11. Conteneurisation avec Docker.
12. Déploiement sur AWS.

À la fin de ce projet, vous aurez une vision complète du travail attendu d'un Data Scientist dans une entreprise comme **SentiLink**.
