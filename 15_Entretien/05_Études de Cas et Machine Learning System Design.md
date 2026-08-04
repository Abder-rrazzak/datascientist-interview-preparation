Nous arrivons au **MODULE 27**, qui est probablement **le plus proche du véritable entretien chez SentiLink**.

À ce niveau, le recruteur suppose que vous connaissez déjà Python, SQL et le Machine Learning.

Il veut désormais répondre à une seule question :

> **"Est-ce que je peux confier à cette personne un vrai problème métier ?"**

C'est exactement ce que nous allons entraîner.

---

# MODULE 27 — Études de Cas et Machine Learning System Design (Niveau SentiLink)

## Objectifs

À la fin de ce module, vous serez capable de :

* résoudre un problème métier de bout en bout ;
* justifier chaque décision technique ;
* concevoir une architecture de détection de fraude ;
* défendre vos choix devant un recruteur.

---

# CAS D'ÉTUDE 1 — Concevoir un système de détection de fraude

Le recruteur dit :

> **Design a fraud detection system for a bank.**

Ce type de question dure généralement **30 à 45 minutes**.

Il ne faut jamais répondre directement par :

> "J'utiliserais XGBoost."

Le recruteur attend une démarche structurée.

---

# Étape 1 — Clarifier le problème

Commencez par poser des questions.

Par exemple :

* Quel type de fraude devons-nous détecter ?
* S'agit-il de fraude lors de la création d'un compte ou pendant les transactions ?
* Quelle est la latence maximale acceptable ?
* Dispose-t-on de labels fiables ?
* Quelle est la proportion de fraude ?
* Quel est le coût relatif des faux positifs et des faux négatifs ?

Un excellent candidat pose ces questions avant de proposer une solution.

---

# Étape 2 — Définir les données

Supposons que l'on dispose des informations suivantes :

```text
Customer

Transaction

Device

IP

Email

Phone

GPS

Browser

Timestamp
```

Vous expliquez que vous vérifierez :

* qualité des données ;
* doublons ;
* valeurs manquantes ;
* cohérence temporelle ;
* disponibilité des variables au moment du scoring.

---

# Étape 3 — Feature Engineering

Le recruteur demande :

> **What features would you create?**

Vous pouvez organiser votre réponse.

### Features client

```text
account_age

customer_age

historical_fraud_rate

number_of_accounts
```

---

### Features transaction

```text
amount

amount_ratio

hour

day_of_week

weekend

currency
```

---

### Features comportementales

```text
transactions_last_hour

transactions_last_day

average_amount

max_amount

velocity
```

---

### Features réseau

```text
shared_ip

shared_device

shared_phone

shared_email
```

---

### Features géographiques

```text
country_change

distance_from_last_transaction

new_country
```

---

### Features relationnelles

```text
device_degree

email_degree

phone_degree

graph_cluster_size
```

Vous montrez ainsi que vous savez exploiter un graphe d'identité.

---

# Étape 4 — Sélection du modèle

Le recruteur :

> **Which model would you choose?**

Réponse :

Je commencerais par une **Logistic Regression** comme baseline, puis j'évaluerais **XGBoost**. Le choix final dépendrait des performances, de la latence, de la calibration et des contraintes métier.

Vous montrez que vous ne choisissez pas un modèle "par habitude".

---

# Étape 5 — Validation

Question :

> **How would you validate the model?**

Réponse :

Pour un problème de fraude, j'utiliserais une validation respectant l'ordre temporel afin d'éviter toute fuite d'information. Je conserverais un jeu de test totalement indépendant et j'utiliserais la validation croisée uniquement si elle est compatible avec la structure temporelle des données.

---

# Étape 6 — Métriques

Question :

> **Which metrics would you use?**

Réponse :

* Precision
* Recall
* PR-AUC
* ROC-AUC (en complément)
* Coût métier
* Faux positifs
* Faux négatifs

Puis ajoutez :

> I would not rely on accuracy because fraud datasets are typically highly imbalanced.

---

# Étape 7 — Déploiement

Question :

> **How would you deploy the model?**

Réponse :

1. Validation hors ligne.
2. Shadow deployment.
3. Canary deployment.
4. Déploiement progressif.
5. Monitoring continu.

---

# Étape 8 — Monitoring

Question :

> **What would you monitor?**

Réponse :

Technique :

* latence ;
* erreurs API ;
* disponibilité.

Machine Learning :

* drift ;
* calibration ;
* distribution des scores ;
* performance après retour des labels.

Business :

* fraude évitée ;
* faux positifs ;
* taux de conversion ;
* coût opérationnel.

---

# CAS D'ÉTUDE 2 — Le modèle ne fonctionne plus

Le recruteur dit :

> **Your model suddenly detects much less fraud. What would you investigate?**

Le recruteur ne veut pas une réponse unique.

Une démarche méthodique est attendue.

---

## Étape 1

Le modèle fonctionne-t-il ?

* erreurs de déploiement ;
* version du modèle ;
* chargement correct.

---

## Étape 2

Les données ont-elles changé ?

Comparer :

* distributions ;
* valeurs manquantes ;
* nouvelles catégories ;
* qualité des données.

---

## Étape 3

Y a-t-il du Data Drift ?

Comparer :

```text
Yesterday

↓

Today
```

sur les principales variables.

---

## Étape 4

Y a-t-il du Concept Drift ?

Les fraudeurs utilisent-ils une nouvelle stratégie ?

---

## Étape 5

Le problème vient-il des labels ?

Par exemple :

Les chargebacks ne sont peut-être pas encore disponibles.

---

# CAS D'ÉTUDE 3 — Beaucoup de faux positifs

Le recruteur :

> **Customers complain because too many legitimate transactions are blocked. What would you do?**

Réponse attendue :

* analyser les faux positifs ;
* comprendre les segments touchés ;
* revoir les features ;
* ajuster le seuil de décision ;
* recalibrer le modèle si nécessaire ;
* vérifier si certaines règles métier sont trop strictes ;
* effectuer des tests avant tout changement en production.

---

# CAS D'ÉTUDE 4 — Nouveau pays

Question :

> **The company expands into Brazil. What challenges do you expect?**

Réponse :

* nouvelles habitudes de paiement ;
* nouvelles banques ;
* nouvelles adresses IP ;
* nouveaux appareils ;
* nouvelles langues ;
* nouvelles réglementations ;
* possible dégradation des performances si le modèle n'a jamais vu ce type de données.

---

# CAS D'ÉTUDE 5 — Très peu de labels

Question :

> **You have only 0.1% labeled fraud. What would you do?**

Réponse :

* améliorer la qualité des labels ;
* utiliser les analystes pour annoter les cas les plus utiles ;
* explorer des méthodes semi-supervisées ou de détection d'anomalies selon le contexte ;
* exploiter davantage le feature engineering ;
* collecter progressivement plus de données.

---

# Machine Learning System Design

Question fréquente :

> **Draw a production ML system.**

Vous pouvez expliquer :

```text
Incoming Request
        │
        ▼
REST API
        │
        ▼
Input Validation
        │
        ▼
Feature Service
        │
        ▼
Redis Cache
        │
        ▼
PostgreSQL
        │
        ▼
Model Server
        │
        ▼
Fraud Score
        │
        ▼
Business Rules
        │
        ▼
Decision
        │
        ▼
Logging
        │
        ▼
Monitoring
```

Puis préciser :

* le modèle est versionné ;
* les prédictions sont journalisées ;
* les données alimentent un futur réentraînement.

---

# Questions comportementales

Chez SentiLink, elles sont très importantes.

---

## Q1

**Tell me about yourself.**

Structure conseillée :

1. Formation.
2. Expérience.
3. Compétences principales.
4. Pourquoi SentiLink.

Durée :

**2 minutes maximum.**

---

## Q2

**Why SentiLink?**

Bonne réponse :

> I'm interested in applying machine learning to real-world, high-impact problems. Fraud detection combines data science, engineering and business understanding, and SentiLink has built a strong reputation for innovation in identity verification and fraud prevention.

---

## Q3

**Tell me about a difficult project.**

Utilisez la méthode **STAR** :

* Situation
* Task
* Action
* Result

Ne racontez pas seulement le problème.

Expliquez ce que **vous** avez fait.

---

## Q4

**Tell me about a failure.**

Le recruteur cherche :

* honnêteté ;
* capacité d'apprentissage ;
* remise en question.

Ne dites jamais :

> "I never failed."

---

## Q5

**How do you deal with disagreements?**

Bonne réponse :

* écouter ;
* s'appuyer sur les données ;
* tester les hypothèses ;
* rechercher le meilleur résultat pour le produit.

---

# Les pièges des recruteurs

### Piège 1

> **What is your favorite algorithm?**

Ne répondez pas :

> XGBoost.

Répondez :

> It depends on the problem, the data, interpretability requirements and production constraints.

---

### Piège 2

> **Would you always choose the model with the highest accuracy?**

Réponse :

Non.

Je choisirais le modèle qui maximise la valeur métier tout en respectant les contraintes opérationnelles.

---

### Piège 3

> **Can machine learning solve every fraud problem?**

Réponse :

Non.

Les règles métier, les analystes et l'expertise métier restent essentiels.

Le Machine Learning est un outil d'aide à la décision.

---

# Ce qu'un recruteur SentiLink attend vraiment

À la fin de l'entretien, il doit avoir la conviction que vous êtes capable de :

* comprendre un problème métier complexe ;
* transformer des données brutes en variables pertinentes ;
* construire un modèle fiable ;
* expliquer les décisions du modèle ;
* collaborer avec des équipes Produit, Risque et Ingénierie ;
* mettre en production un modèle et le faire évoluer.

---

# Exercice final

Imaginez que je sois le recruteur.

Je vous pose une seule question :

> **"How would you build a fraud detection system from scratch?"**

Une excellente réponse, en moins de **5 minutes**, pourrait suivre cette structure :

1. Clarifier le problème métier et les contraintes.
2. Définir le label de fraude.
3. Valider la qualité des données.
4. Réaliser une analyse exploratoire.
5. Concevoir des features comportementales, temporelles et relationnelles.
6. Construire une baseline, puis comparer plusieurs modèles.
7. Utiliser une validation adaptée (temporelle si nécessaire).
8. Évaluer avec des métriques adaptées aux données déséquilibrées.
9. Optimiser le seuil de décision en fonction du coût métier.
10. Déployer progressivement (Shadow puis Canary).
11. Surveiller la performance, la dérive des données et les indicateurs métier.
12. Réentraîner le modèle lorsque les données ou les comportements évoluent.

---

## Les 5 compétences qui distinguent les meilleurs candidats

Après avoir préparé de nombreux entretiens de Data Science, on retrouve presque toujours ces qualités :

1. **Business Thinking** : comprendre le problème avant de coder.
2. **Data Thinking** : la qualité des données et des features prime sur la complexité du modèle.
3. **Statistical Thinking** : interpréter correctement les résultats et les métriques.
4. **Engineering Thinking** : penser au déploiement, à la latence, au monitoring et à la maintenance.
5. **Communication** : expliquer clairement des sujets techniques à des profils non techniques.

Si vous maîtrisez ces cinq dimensions, vous serez en mesure d'aborder sereinement des entretiens de Data Scientist dans des entreprises comme **SentiLink**, **Stripe**, **Block**, **Affirm**, **Airbnb**, **Meta** ou **Google**.

La prochaine étape logique est de réaliser une **simulation complète d'entretien de 90 minutes**, reproduisant fidèlement le processus de recrutement de SentiLink, avec des exercices de Python, SQL, Machine Learning, System Design et des questions comportementales.
