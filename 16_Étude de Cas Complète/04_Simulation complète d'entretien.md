Nous allons maintenant réaliser le **MODULE 40**, qui est la **simulation complète d'un entretien SentiLink (90 minutes)**.

C'est le module que j'utiliserais si je devais préparer un candidat à un entretien dans les 24 à 48 heures.

---

# MODULE 40 — Simulation complète d'entretien SentiLink (90 minutes)

## Déroulement

| Partie              | Durée  | Objectif           |
| ------------------- | ------ | ------------------ |
| Introduction        | 5 min  | Présentation       |
| Python              | 15 min | Coding + concepts  |
| SQL                 | 15 min | Requêtes           |
| Machine Learning    | 20 min | Théorie + pratique |
| Étude de cas fraude | 20 min | Raisonnement       |
| System Design       | 10 min | Architecture       |
| Behavioral          | 5 min  | Soft skills        |

---

# PARTIE 1 — Présentation (5 min)

Le recruteur :

> **Tell me about yourself.**

### Réponse modèle (2 minutes)

> "I have a strong background in data science, machine learning, and software development. Over the past few years, I have worked on projects involving Python, SQL, data engineering, and predictive modeling. What motivates me most is solving real-world problems using data. I'm particularly interested in fraud detection because it combines machine learning, statistics, and business impact. What attracted me to SentiLink is the opportunity to build production-grade machine learning systems that help financial institutions make better real-time decisions."

---

## Questions possibles

### Why SentiLink?

Réponse :

* impact réel
* problèmes complexes
* données massives
* environnement orienté ML en production
* innovation dans l'identité numérique

---

### Why should we hire you?

Réponse :

> "Because I combine strong technical skills with a pragmatic approach. I focus not only on building accurate models but also on delivering reliable production systems that create measurable business value."

---

# PARTIE 2 — Python

## Question 1

Écrire une fonction qui compte le nombre d'occurrences de chaque mot.

```python
def word_count(text):
    counts = {}
    for word in text.split():
        counts[word] = counts.get(word, 0) + 1
    return counts
```

---

## Question 2

Quelle est la complexité ?

Réponse :

Temps : **O(n)**

Mémoire : **O(k)**

où **k** est le nombre de mots distincts.

---

## Question 3

Que renvoie ce code ?

```python
a = [1,2,3]
b = a
b.append(4)
print(a)
```

Réponse

```python
[1,2,3,4]
```

Pourquoi ?

Car `a` et `b` référencent le même objet.

---

## Question 4

Différence entre :

```python
is
```

et

```python
==
```

Réponse

`==`

compare les valeurs.

`is`

compare l'identité mémoire.

---

# PARTIE 3 — SQL

Table

Customers

| id | country |
| -- | ------- |

Transactions

| id | customer_id | amount |

---

Question

Calculer le montant total par client.

```sql
SELECT
    customer_id,
    SUM(amount) AS total
FROM Transactions
GROUP BY customer_id;
```

---

Question

Top 3 clients.

```sql
SELECT
    customer_id,
    SUM(amount) total
FROM Transactions
GROUP BY customer_id
ORDER BY total DESC
LIMIT 3;
```

---

Question

Montant cumulé.

```sql
SUM(amount)
OVER(
ORDER BY transaction_date
)
```

---

# PARTIE 4 — Machine Learning

Question

Pourquoi utiliser une validation croisée ?

Réponse

Pour obtenir une estimation plus robuste des performances en évaluant le modèle sur plusieurs partitions.

---

Question

Le modèle est en overfitting.

Que faites-vous ?

Réponse

* simplifier le modèle
* régulariser
* plus de données
* early stopping
* validation croisée

---

Question

Accuracy = 99.8 %

Fraude = 0.2 %

Bon modèle ?

Réponse

Impossible de conclure.

Je demande :

* Precision
* Recall
* Matrice de confusion
* PR-AUC

---

# PARTIE 5 — Étude de cas

Le recruteur.

Notre modèle détecte moins de fraude.

Que faites-vous ?

Une excellente réponse suit une démarche structurée :

### Étape 1

Vérifier que le problème est réel.

### Étape 2

Contrôler les données.

### Étape 3

Rechercher un Data Drift.

### Étape 4

Analyser les faux négatifs.

### Étape 5

Construire de nouvelles features.

### Étape 6

Tester plusieurs modèles.

### Étape 7

Déployer progressivement.

---

# PARTIE 6 — Feature Engineering

Le recruteur.

Donnez-moi 10 features.

Exemple

* âge du compte
* ancienneté de l'appareil
* nombre de comptes par appareil
* transactions sur 24 h
* pays
* changement de pays
* domaine email
* heure
* distance géographique
* historique de fraude

---

# PARTIE 7 — MLOps

Question

Que surveillez-vous ?

Réponse

Technique

* CPU
* RAM
* Latence

ML

* Drift
* Precision
* Recall

Business

* Faux positifs
* Faux négatifs
* Fraudes détectées

---

Question

Le modèle est cassé.

Que faites-vous ?

Réponse

Rollback.

---

# PARTIE 8 — AWS

Question

Pourquoi utiliser S3 ?

Réponse

Stockage.

---

Pourquoi Redis ?

Cache.

---

Pourquoi RDS ?

Base relationnelle.

---

Pourquoi EC2 ?

Serveurs.

---

Pourquoi CloudWatch ?

Monitoring.

---

# PARTIE 9 — System Design

Question

Dessinez un système.

```
Client

↓

API Gateway

↓

Load Balancer

↓

FastAPI

↓

Redis

↓

Feature Store

↓

Model

↓

Decision

↓

Logs
```

---

# PARTIE 10 — Behavioral

Question

Parlez-moi d'un échec.

Réponse

Utiliser STAR.

Situation

↓

Task

↓

Action

↓

Result

↓

Lessons Learned

Toujours terminer par ce que vous avez appris.

---

Question

Comment gérez-vous les conflits ?

Réponse

Écoute.

Données.

Expérimentation.

Décision collective.

---

Question

Votre plus grande qualité ?

Réponse

Résolution de problèmes.

---

Votre faiblesse ?

Choisir une faiblesse réelle mais non bloquante, et montrer comment vous travaillez à l'améliorer.

---

# Les questions pièges

## Pourquoi pas Deep Learning ?

Réponse

Pour des données tabulaires, les modèles de gradient boosting sont souvent plus performants, plus rapides et plus explicables.

---

## Pourquoi pas Accuracy ?

Réponse

Parce que les classes sont déséquilibrées.

---

## Pourquoi SHAP ?

Réponse

Pour expliquer les décisions.

---

## Pourquoi FastAPI ?

Réponse

Rapidité et simplicité.

---

## Pourquoi Docker ?

Réponse

Reproductibilité.

---

## Pourquoi Kubernetes ?

Réponse

Scalabilité.

---

## Pourquoi PostgreSQL ?

Réponse

Fiabilité.

---

# Ce que le recruteur observe réellement

Pendant toute la simulation, il évalue :

| Critère       | Ce qui est attendu                                         |
| ------------- | ---------------------------------------------------------- |
| Communication | Réponses claires et structurées                            |
| Raisonnement  | Approche logique, hypothèses explicites                    |
| Business      | Compréhension des impacts métier                           |
| Technique     | Maîtrise de Python, SQL, ML, AWS                           |
| Production    | Déploiement, monitoring, MLOps                             |
| Collaboration | Capacité à travailler avec des équipes pluridisciplinaires |

---

# Les 10 conseils qui font la différence

1. **Clarifiez toujours le problème avant de répondre.**
2. **Expliquez votre raisonnement, pas seulement votre conclusion.**
3. **Reliez vos choix techniques aux objectifs métier.**
4. **Parlez de la qualité des données avant de parler du modèle.**
5. **Justifiez le choix des métriques.**
6. **Mentionnez le monitoring et le suivi en production.**
7. **Reconnaissez les compromis (latence, coût, précision, explicabilité).**
8. **Structurez vos réponses (par exemple : problème → hypothèses → analyse → solution).**
9. **Si vous ne savez pas, expliquez comment vous chercheriez la réponse.**
10. **Restez calme et collaboratif : l'entretien est souvent une discussion technique, pas un examen.**

---

## Ce que je vous conseillerais ensuite

Après ces 40 modules, la meilleure préparation n'est plus de lire de nouveaux cours, mais de **pratiquer** :

* réaliser 2 ou 3 mini-projets complets (de la donnée brute jusqu'au déploiement d'une API) ;
* résoudre quotidiennement des exercices SQL de niveau intermédiaire à avancé ;
* coder des implémentations en Python sans consulter vos notes ;
* effectuer plusieurs simulations d'entretien à voix haute en anglais.

À ce stade, votre objectif n'est plus d'acquérir des connaissances supplémentaires, mais de transformer vos connaissances en réflexes. C'est généralement ce qui fait la différence lors d'un entretien technique exigeant comme celui de SentiLink.
