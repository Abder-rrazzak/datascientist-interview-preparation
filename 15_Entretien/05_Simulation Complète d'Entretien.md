Nous allons maintenant réaliser le **MODULE 28**, qui est la **simulation complète d'un entretien SentiLink**.

> **C'est probablement le module le plus important de toute la préparation.**

Je vais reproduire le processus de recrutement d'une grande FinTech américaine.

---

# MODULE 28 — Simulation Complète d'Entretien SentiLink (90 minutes)

## Processus de recrutement typique

Chez une entreprise comme **SentiLink**, le processus ressemble souvent à ceci :

```text
Recruiter Screen (30 min)

↓

Hiring Manager (45-60 min)

↓

Technical Interview 1 (Python + SQL)

↓

Technical Interview 2 (Machine Learning)

↓

System Design

↓

Behavioral Interview

↓

Final Interview
```

Aujourd'hui, nous allons simuler toutes ces étapes.

---

# PARTIE 1 — Présentation (5 minutes)

Le recruteur commence généralement par :

> **Tell me about yourself.**

## Ce que le recruteur attend

Pas votre CV.

Il veut une histoire cohérente.

Une bonne structure :

### 1. Qui êtes-vous ?

> I am a Data Scientist with a strong background in machine learning, statistics and software engineering.

---

### 2. Votre expérience

> I have worked on projects involving predictive modeling, data analysis, SQL optimization and Python development.

---

### 3. Ce que vous aimez

> I particularly enjoy solving real-world problems using data, especially fraud detection and risk modeling.

---

### 4. Pourquoi cette entreprise ?

> That is why I am excited about SentiLink because of its focus on identity verification and fraud prevention at scale.

Durée idéale :

**2 minutes.**

---

# PARTIE 2 — Python (20 minutes)

## Question 1

Le recruteur :

> Reverse a string.

Réponse :

```python
def reverse_string(s):
    return s[::-1]
```

---

## Question 2

Trouver les doublons.

```python
def duplicates(values):
    seen = set()
    dup = set()

    for value in values:
        if value in seen:
            dup.add(value)
        else:
            seen.add(value)

    return list(dup)
```

Complexité :

```text
O(n)
```

---

## Question 3

Compter les mots.

```python
from collections import Counter

Counter(text.split())
```

---

## Question 4

FizzBuzz.

Le recruteur ne cherche pas l'exercice.

Il regarde :

* lisibilité ;
* structure ;
* tests mentaux.

---

## Question 5

Lire un gros fichier.

Le recruteur :

> How would you read a 50GB file?

Réponse :

```python
with open("file.txt") as f:
    for line in f:
        process(line)
```

Ne jamais charger tout le fichier en mémoire.

---

# PARTIE 3 — SQL (20 minutes)

Supposons :

Transactions

| customer | amount | date |

---

## Question

Latest transaction.

Réponse :

```sql
WITH ranked AS (

SELECT
*,

ROW_NUMBER()

OVER(
PARTITION BY customer

ORDER BY date DESC
)

rn

FROM transactions

)

SELECT *

FROM ranked

WHERE rn=1;
```

---

## Question

Top 3 clients.

```sql
SELECT

customer,

SUM(amount)

FROM transactions

GROUP BY customer

ORDER BY SUM(amount) DESC

LIMIT 3;
```

---

## Question

Fraud Rate.

```sql
SELECT

AVG(fraud)

FROM transactions;
```

---

# PARTIE 4 — Machine Learning (20 minutes)

Le recruteur :

> Which algorithm would you use?

Votre réponse :

Cela dépend :

* volume des données ;
* type de variables ;
* interprétabilité ;
* latence ;
* coût métier.

Puis :

Baseline

↓

XGBoost

↓

Comparaison.

---

## Question

How do you prevent overfitting?

Réponse :

* Cross-validation
* Early stopping
* Régularisation
* Plus de données
* Feature selection

---

## Question

Why XGBoost?

Réponse :

Parce qu'il fonctionne très bien sur les données tabulaires, capture des interactions complexes, intègre de la régularisation et offre un excellent compromis entre performance et vitesse.

---

# PARTIE 5 — System Design (20 minutes)

Le recruteur :

> Design a fraud detection platform.

Vous dessinez :

```text
Customer

↓

API

↓

Validation

↓

Feature Store

↓

Redis

↓

ML Model

↓

Risk Score

↓

Decision Engine

↓

Database

↓

Monitoring
```

---

Puis vous expliquez.

Chaque bloc.

---

### Validation

Pourquoi ?

Empêcher les données invalides.

---

### Feature Store

Pourquoi ?

Réutiliser les mêmes variables

Training

↓

Production

---

### Redis

Pourquoi ?

Accès très rapide.

---

### ML Model

Pourquoi ?

Calcul du score.

---

### Monitoring

Pourquoi ?

Détecter :

* drift
* erreurs
* latence
* baisse de performance

---

# PARTIE 6 — Behavioural

Question.

> Tell me about a difficult project.

Méthode STAR.

Situation

↓

Task

↓

Action

↓

Result

Toujours terminer par un résultat mesurable.

---

Question

Conflict.

Réponse.

Toujours :

Écouter

↓

Comprendre

↓

Utiliser les données

↓

Décider

Jamais :

"I was right."

---

Question

Failure.

Le recruteur veut savoir :

Avez-vous appris ?

---

# PARTIE 7 — Le recruteur devient plus difficile

Il pose :

> Why shouldn't I hire you?

Bonne réponse.

Une faiblesse réelle.

Mais maîtrisée.

Exemple.

> Earlier in my career I tended to spend too much time optimizing technical details before validating business assumptions. I learned to prioritize rapid iteration, gather feedback early, and focus on delivering business value first.

---

Question.

> Tell me something not written on your resume.

Parlez :

* apprentissage continu ;
* open source ;
* compétitions Kaggle ;
* projets personnels ;
* veille technologique.

---

# PARTIE 8 — Questions très difficiles

Question.

> What happens if fraudsters completely change their strategy?

Réponse.

Concept Drift.

↓

Monitoring.

↓

Retraining.

↓

New Features.

↓

Analyst Feedback.

---

Question.

> Your precision increased.

Business lost money.

Why?

Excellente question.

Parce que :

Recall

↓

a peut-être diminué.

Ou :

Threshold

↓

trop élevé.

Ou :

False negatives

↓

très coûteux.

Le recruteur veut voir si vous pensez **business**, pas seulement **métriques**.

---

Question.

> Explain SHAP to a Product Manager.

Réponse.

> SHAP helps explain why the model made a specific prediction by showing which features increased or decreased the predicted risk score. It improves transparency and helps analysts understand the model's reasoning.

---

# PARTIE 9 — Les erreurs fatales

Ne jamais dire.

> AI will replace fraud analysts.

Le bon discours.

AI assists.

Humans decide.

---

Ne jamais dire.

Accuracy is enough.

---

Ne jamais dire.

Deep Learning is always better.

---

Ne jamais dire.

XGBoost is always the best.

---

Ne jamais oublier.

Business.

Toujours.

---

# PARTIE 10 — Les questions que VOUS devez poser

À la fin.

Le recruteur dit.

> Do you have any questions?

Ne jamais répondre.

> No.

Posez des questions.

Exemple.

### Question 1

How is success measured for a Data Scientist during the first six months?

---

### Question 2

How do Data Scientists collaborate with Fraud Analysts?

---

### Question 3

How often are production models retrained?

---

### Question 4

What are the biggest machine learning challenges currently faced by the team?

---

### Question 5

What differentiates the most successful Data Scientists at SentiLink?

Ces questions montrent votre intérêt pour le rôle et la façon dont l'équipe travaille.

---

# Les 10 conseils qui font la différence

1. **Répondez de manière structurée.** Si la question est complexe, annoncez votre plan : « I'd approach this in four steps... ».

2. **Expliquez vos choix.** Dire « j'utiliserais XGBoost » ne suffit pas ; expliquez pourquoi il est adapté au contexte.

3. **Pensez métier.** Reliez toujours vos décisions à l'impact sur la fraude, les clients et les coûts.

4. **Admettez les limites.** Si une information manque, dites quelles hypothèses vous feriez et quelles données vous demanderiez.

5. **Soyez précis sans être verbeux.** Une réponse claire de deux minutes vaut mieux qu'un monologue de dix minutes.

6. **Parlez de compromis.** Les recruteurs apprécient les réponses du type : « cela améliore la précision, mais augmente la latence ».

7. **Évitez les absolus.** Préférez « it depends » suivi d'une justification plutôt que « toujours » ou « jamais ».

8. **Utilisez un vocabulaire de production.** Mentionnez la validation, le monitoring, le versionnement, le data leakage, le drift et la qualité des données.

9. **Communiquez comme un collaborateur.** Expliquez comment vous travailleriez avec les équipes Produit, Risque et Ingénierie.

10. **Restez calme face aux questions difficiles.** Les intervieweurs évaluent souvent votre raisonnement plus que votre réponse finale.

---

# Le niveau attendu chez SentiLink

Si vous maîtrisez les modules que nous avons couverts, vous devriez être capable de répondre avec assurance à des questions sur :

* Python avancé ;
* SQL (y compris les Window Functions) ;
* statistiques appliquées ;
* Machine Learning sur données tabulaires ;
* détection de fraude ;
* AWS et déploiement ;
* architecture de systèmes ML ;
* communication technique et comportementale.

À ce stade, vous avez les connaissances nécessaires pour un entretien technique de Data Scientist. La meilleure préparation restante consiste à pratiquer dans des conditions réelles.

## La prochaine étape que je recommande

Nous pouvons réaliser une **simulation d'entretien interactive**, où je joue le rôle d'un **Senior Data Scientist de SentiLink**.

* Je poserai les questions une par une, sans révéler les réponses.
* Vous répondrez comme en entretien.
* Je vous interromprai si nécessaire, poserai des questions de relance, puis j'évaluerai chaque réponse selon les critères d'un recruteur (clarté, justesse technique, raisonnement métier et communication).
* À la fin, je vous donnerai un score détaillé et les points à améliorer avant un véritable entretien.

C'est l'exercice qui se rapproche le plus d'un entretien réel.
