Nous arrivons à la phase la plus importante de toute la préparation.

> À partir de maintenant, je vais jouer le rôle d'un **Senior Data Scientist** ou d'un **Hiring Manager chez SentiLink**.

L'objectif n'est plus d'apprendre des concepts, mais de **savoir répondre comme un candidat expérimenté**.

---

# Module final — Simulations d'entretien

Un entretien chez SentiLink comporte généralement plusieurs étapes :

1. Screening RH
2. Entretien technique (Python, SQL, Machine Learning)
3. Étude de cas
4. System Design (pour les profils plus expérimentés)
5. Entretien comportemental (Behavioral Interview)
6. Questions du candidat

---

# PARTIE 1 — Questions Python

## Question 1

**Expliquez la différence entre une liste et un tuple.**

### Réponse idéale

Une liste est mutable, ce qui signifie que l'on peut modifier son contenu après sa création. Un tuple est immuable. Les tuples sont souvent utilisés pour représenter des données qui ne doivent pas être modifiées et peuvent être légèrement plus performants et utilisables comme clés de dictionnaire lorsqu'ils contiennent uniquement des éléments immuables.

---

## Question 2

Quelle est la différence entre `deep copy` et `shallow copy` ?

### Réponse

Une **shallow copy** copie uniquement le premier niveau de l'objet. Les objets imbriqués restent partagés.

Une **deep copy** crée une copie indépendante de tous les objets imbriqués.

Exemple :

```python
import copy

a = [[1,2],[3,4]]

b = copy.copy(a)
c = copy.deepcopy(a)
```

---

## Question 3

Expliquez les générateurs (`yield`).

### Réponse

Les générateurs produisent les éléments à la demande au lieu de les stocker tous en mémoire.

Ils sont particulièrement utiles pour traiter de très grands jeux de données.

---

## Question 4

Pourquoi Pandas est-il plus rapide qu'une boucle Python ?

### Réponse

Parce qu'il s'appuie largement sur NumPy et des opérations vectorisées implémentées en C, évitant ainsi les boucles Python interprétées.

---

# PARTIE 2 — Questions SQL

## Question

Différence entre WHERE et HAVING ?

### Réponse

* `WHERE` filtre les lignes avant l'agrégation.
* `HAVING` filtre les groupes après l'agrégation.

---

## Question

Expliquez une Window Function.

### Réponse

Une Window Function calcule une valeur sur un ensemble de lignes liées sans réduire le nombre de lignes retournées.

Exemple :

```sql
ROW_NUMBER() OVER (
PARTITION BY customer_id
ORDER BY transaction_date DESC
)
```

---

## Question

Comment optimiser une requête SQL lente ?

### Réponse

Je commencerais par :

* analyser le plan d'exécution (`EXPLAIN ANALYZE`) ;
* vérifier les index ;
* limiter les colonnes sélectionnées ;
* filtrer les données le plus tôt possible ;
* éviter les sous-requêtes inutiles ;
* vérifier les jointures.

---

# PARTIE 3 — Machine Learning

## Question

Pourquoi ne pas utiliser uniquement l'accuracy ?

### Réponse

Parce qu'en présence d'un fort déséquilibre des classes, un modèle peut obtenir une accuracy élevée tout en ne détectant aucune fraude.

---

## Question

Expliquez le biais et la variance.

### Réponse

* **Biais élevé** : le modèle est trop simple et sous-apprend (*underfitting*).
* **Variance élevée** : le modèle apprend trop précisément les données d'entraînement et généralise mal (*overfitting*).

---

## Question

Pourquoi XGBoost est-il souvent performant ?

### Réponse

Parce qu'il construit séquentiellement des arbres qui corrigent les erreurs des précédents, tout en intégrant une régularisation efficace et une bonne gestion des valeurs manquantes.

---

# PARTIE 4 — Détection de fraude

## Question

Pourquoi les fraudes sont-elles difficiles à détecter ?

### Réponse

Parce qu'elles sont rares, évoluent rapidement et que les fraudeurs adaptent continuellement leurs stratégies.

---

## Question

Pourquoi utiliser le Recall ?

### Réponse

Parce qu'il mesure la proportion de fraudes réellement détectées. En fraude, manquer une fraude peut être très coûteux.

---

## Question

Comment détecter un nouveau type de fraude ?

### Réponse

Je combinerais des règles métier, des méthodes de détection d'anomalies (par exemple Isolation Forest) et une collecte progressive de nouveaux labels afin d'entraîner ensuite un modèle supervisé.

---

# PARTIE 5 — AWS

## Question

Pourquoi utiliser S3 ?

### Réponse

Pour stocker durablement les données, les modèles et les artefacts de Machine Learning.

---

## Question

Différence entre EC2 et Lambda ?

### Réponse

* **EC2** : machine virtuelle adaptée aux applications persistantes.
* **Lambda** : exécution de fonctions courtes déclenchées par des événements, sans gestion de serveur.

---

## Question

Pourquoi Redis ?

### Réponse

Pour réduire la latence grâce à un accès mémoire très rapide aux données fréquemment utilisées.

---

# PARTIE 6 — System Design

## Question

Concevez une API capable de traiter 10 000 requêtes par seconde.

### Réponse

Je proposerais une architecture avec :

* API Gateway ;
* Load Balancer ;
* plusieurs instances FastAPI ;
* Redis pour le cache ;
* un Feature Store ;
* un modèle optimisé ;
* autoscaling ;
* CloudWatch pour le monitoring.

---

## Question

Que faites-vous si le modèle tombe en panne ?

### Réponse

Je prévois un mécanisme de secours :

* règles métier ;
* modèle précédent ;
* plusieurs instances ;
* surveillance automatique et redémarrage.

---

# PARTIE 7 — Questions comportementales

## Question

Parlez-moi de vous.

### Réponse (structure)

* Formation.
* Expérience.
* Projet principal.
* Ce qui vous motive.
* Pourquoi SentiLink.

---

## Question

Pourquoi souhaitez-vous rejoindre SentiLink ?

### Réponse

Je suis particulièrement attiré par les problématiques de Machine Learning appliquées à la fraude et à l'identité numérique. Ce qui m'intéresse chez SentiLink, c'est la possibilité de travailler sur des modèles ayant un impact direct à grande échelle, dans un environnement où la recherche, les données et la mise en production sont étroitement liées.

---

## Question

Parlez d'un échec.

Structure :

* Situation.
* Problème.
* Action.
* Résultat.
* Ce que vous avez appris.

(Utilisez la méthode **STAR** : Situation, Task, Action, Result.)

---

## Question

Parlez d'un conflit.

Même structure STAR.

L'objectif est de montrer votre capacité à communiquer, collaborer et résoudre les problèmes.

---

# PARTIE 8 — Questions pièges

## Question

Pourquoi n'utilisez-vous pas toujours un réseau de neurones ?

### Réponse

Parce qu'ils ne sont pas toujours le meilleur choix. Pour des données tabulaires, des modèles comme XGBoost ou LightGBM offrent souvent un excellent compromis entre performances, rapidité et interprétabilité.

---

## Question

Que faites-vous si votre Recall baisse soudainement ?

### Réponse

Je vérifie les données, recherche un Data Drift ou un Concept Drift, contrôle la qualité du pipeline, puis j'envisage un réentraînement si nécessaire.

---

## Question

Comment expliquer un modèle à un responsable métier ?

### Réponse

J'évite le jargon technique. J'explique les principaux facteurs qui influencent les décisions du modèle à l'aide d'outils comme SHAP et d'exemples concrets.

---

# Étude de cas (type SentiLink)

### Énoncé

Une banque constate une augmentation soudaine des fraudes.

Comment réagissez-vous ?

### Réponse idéale

1. Vérifier la qualité des données.
2. Identifier les segments concernés.
3. Comparer les distributions avec les données d'entraînement.
4. Analyser les faux négatifs.
5. Vérifier les règles métier.
6. Détecter un éventuel Data Drift ou Concept Drift.
7. Déployer des règles temporaires si nécessaire.
8. Réentraîner le modèle avec les nouveaux cas confirmés.
9. Déployer progressivement la nouvelle version.
10. Renforcer le monitoring.

---

# Questions que vous pouvez poser au recruteur

À la fin de l'entretien, poser des questions pertinentes montre votre intérêt pour le poste.

Exemples :

* Comment les équipes Data Science et Engineering collaborent-elles ?
* Quelle est la fréquence de réentraînement des modèles ?
* Quels outils utilisez-vous pour le monitoring et le MLOps ?
* Comment évaluez-vous le succès d'un Data Scientist après six mois ?
* Quels sont les principaux défis techniques auxquels l'équipe est confrontée aujourd'hui ?

---

# Les erreurs les plus fréquentes des candidats

❌ Dire : « J'ai utilisé XGBoost parce que c'est le meilleur. »

✅ Dire : « J'ai comparé plusieurs modèles et retenu celui qui offrait le meilleur compromis entre performances, interprétabilité et contraintes de production. »

---

❌ Parler uniquement d'accuracy.

✅ Discuter de Recall, Precision, PR-AUC et du coût métier des erreurs.

---

❌ Décrire seulement le modèle.

✅ Expliquer le pipeline complet : données, entraînement, déploiement, monitoring et amélioration continue.

---

# Ce qui fera la différence

Les recruteurs de SentiLink recherchent généralement des candidats capables de :

* comprendre les enjeux métier de la fraude ;
* manipuler efficacement Python et SQL ;
* construire des modèles robustes ;
* raisonner sur les compromis (performance, latence, coût, interprétabilité) ;
* expliquer leurs choix techniques de manière claire ;
* comprendre le cycle de vie complet d'un modèle, de la donnée jusqu'à la production.

---

## Recommandation finale

Pour maximiser vos chances, je vous conseille de faire encore trois exercices avant l'entretien :

1. **Un entretien Python/SQL chronométré** (45 minutes).
2. **Une étude de cas complète de détection de fraude** où vous expliquez votre raisonnement à voix haute.
3. **Une simulation d'entretien SentiLink de 90 minutes**, avec des questions techniques, des questions de conception de système et des questions comportementales, comme dans un véritable processus de recrutement.

Cette dernière simulation est probablement l'exercice le plus proche de ce que vous vivrez le jour de l'entretien.
