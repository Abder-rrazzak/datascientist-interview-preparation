Nous allons maintenant passer au **MODULE 39**, qui est probablement **le plus difficile** de toute la préparation.

C'est le type d'entretien que Google, Meta, Stripe, Uber, Airbnb, OpenAI, Palantir et SentiLink utilisent pour distinguer les meilleurs candidats.

Ici, le recruteur **ne vous demande plus vos connaissances**.

Il vous donne un problème que vous n'avez jamais vu.

Il observe votre façon de réfléchir.

---

# MODULE 39 — Machine Learning Case Study (Live Problem Solving)

# Contexte

Vous entrez dans la salle.

Le recruteur dit :

> **Let's work on a real problem together.**

Puis :

> **Suppose you're joining SentiLink next Monday.**

> **Our fraud rate suddenly doubled last night.**

> **Walk me through your investigation.**

Il n'y a pas UNE bonne réponse.

Le recruteur cherche :

* votre logique
* votre méthode
* votre communication

---

# La méthode en 8 étapes

Je recommande toujours cette structure.

```text
1. Clarifier

↓

2. Définir le problème

↓

3. Identifier les hypothèses

↓

4. Collecter les données

↓

5. Analyser

↓

6. Tester

↓

7. Proposer une solution

↓

8. Vérifier les résultats
```

Si vous appliquez toujours cette structure, vous ne serez jamais perdu.

---

# Cas n°1

## Le taux de fraude double.

Le recruteur.

Que faites-vous ?

Mauvaise réponse.

> Je réentraîne le modèle.

Élimination.

---

Bonne réponse.

Je commence par vérifier :

* Est-ce un vrai problème ou une erreur de mesure ?
* Les labels sont-ils corrects ?
* Les tableaux de bord sont-ils à jour ?
* Les pipelines fonctionnent-ils ?

Ensuite :

Je vérifie :

* le déploiement récent
* les logs
* les erreurs
* les API

Puis :

Je regarde les données.

---

# Questions que je me pose

Le problème touche-t-il :

Tous les clients ?

Ou :

Un seul État ?

Un seul navigateur ?

Une seule banque ?

Une seule heure ?

Un seul produit ?

---

# Exemple

Supposons.

Fraude :

Hier :

```text
0.2 %
```

Aujourd'hui :

```text
0.4 %
```

Je segmente.

Par État.

Résultat.

```text
California

0.2 %

↓

2.3 %
```

Très intéressant.

---

Puis.

Par appareil.

```text
Android

↓

Normal

iPhone

↓

Normal

Windows

↓

Explosion
```

On progresse.

---

Puis.

Version navigateur.

```text
Chrome 145

↓

Explosion
```

Le problème est très ciblé.

---

# Cas n°2

Le modèle perd 15 % de Recall.

Le recruteur.

Pourquoi ?

Les causes possibles.

* Drift
* Nouveaux fraudeurs
* Pipeline cassé
* Features manquantes
* Mauvaise version du modèle
* Labels retardés
* Bug logiciel

Je ne saute jamais à une conclusion.

---

# Cas n°3

Le nombre de faux positifs explose.

Question.

Que faites-vous ?

Je vérifie.

Le seuil.

Est-il passé :

```text
0.80

↓

0.60
```

Cela peut expliquer le problème.

---

Autre possibilité.

Une feature est cassée.

Exemple.

Avant.

```text
transactions_last_hour

↓

15
```

Aujourd'hui.

```text
0
```

Toutes les lignes.

Le modèle réagit mal.

---

# Cas n°4

Le modèle fonctionne très bien en validation.

Mais très mal en production.

Pourquoi ?

Le recruteur adore cette question.

Les possibilités.

## Data Leakage

Très fréquent.

---

## Distribution différente

Training.

```text
2024
```

Production.

```text
2026
```

Les comportements ont changé.

---

## Features différentes

Training.

```python
age =
today
-
birth_date
```

Production.

```python
age =
registration_date
-
birth_date
```

Erreur.

---

# Cas n°5

Une nouvelle fraude apparaît.

Le modèle ne la détecte pas.

Que faites-vous ?

Je travaille avec les analystes fraude.

Je collecte.

Les nouveaux cas.

Je crée.

De nouvelles features.

Je réentraîne.

Je surveille.

---

# Cas n°6

Votre modèle est excellent.

Mais :

150 ms.

Le SLA.

100 ms.

Que faites-vous ?

Je mesure.

Où est le temps ?

Exemple.

```text
API

20 ms

Feature Store

60 ms

Model

15 ms

Network

55 ms
```

Le modèle n'est pas forcément responsable.

---

# Cas n°7

Votre modèle est trop lent.

Que pouvez-vous faire ?

* réduire le nombre de features ;
* utiliser Redis ;
* simplifier le modèle ;
* mettre en cache les features ;
* optimiser le code ;
* paralléliser certaines opérations.

---

# Cas n°8

Le client dit.

Je veux.

Moins de faux positifs.

Que faites-vous ?

Je demande.

Quel coût acceptez-vous ?

Car.

Réduire les faux positifs.

↓

Augmente parfois.

Les faux négatifs.

Toujours parler du compromis métier.

---

# Cas n°9

Les données arrivent en retard.

Le pipeline échoue.

Que faites-vous ?

Je mets en place.

* alertes ;
* contrôles de qualité ;
* mécanismes de reprise (retry) ;
* traitement des données en retard si le métier le permet.

---

# Cas n°10

Le recruteur.

Dessinez votre architecture.

Je dessine.

```text
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

Rules Engine

↓

Decision

↓

Logs

↓

Monitoring
```

Toujours simple.

---

# Les erreurs que le recruteur attend

Le recruteur crée volontairement des pièges.

Exemple.

> Notre Accuracy est excellente.

Réponse.

Je demande.

Quel est :

Le Recall ?

La Precision ?

Le taux de fraude ?

L'Accuracy seule ne suffit pas.

---

Autre piège.

Le recruteur.

Pourquoi ne pas utiliser un Deep Learning ?

Réponse.

Je réponds.

Pour des données tabulaires.

XGBoost est souvent :

* plus rapide ;
* plus explicable ;
* plus performant.

---

# Cas SentiLink réel (simulation)

Le recruteur.

Vous avez.

100 millions.

D'applications.

0.1 %

Fraude.

Que faites-vous ?

Je réponds.

1. Comprendre le coût métier.
2. Explorer les données.
3. Construire des features comportementales.
4. Construire des features de graphe.
5. Gérer le déséquilibre des classes.
6. Tester plusieurs modèles.
7. Choisir les métriques adaptées (PR-AUC, Precision, Recall).
8. Déployer progressivement.
9. Surveiller le drift.
10. Réentraîner selon les performances.

---

# La question la plus difficile

Le recruteur.

> **Suppose you only have one week.**

> **What would you do first?**

Une excellente réponse.

> La première étape serait de comprendre le problème métier et d'analyser les données existantes. Avant de modifier le modèle, je vérifierais la qualité des données, les performances actuelles et les principales sources d'erreur. Ensuite, je me concentrerais sur le Feature Engineering, car c'est souvent là que se trouvent les gains les plus importants en détection de fraude. Une fois les nouvelles features évaluées, je comparerais plusieurs modèles, puis je préparerais un déploiement progressif avec monitoring.

---

# Les qualités recherchées

À la fin de ce type d'entretien, le recruteur ne retient pas celui qui connaît le plus d'algorithmes.

Il retient celui qui :

* structure son raisonnement ;
* pose les bonnes questions ;
* prend en compte les contraintes métier ;
* valide ses hypothèses avec les données ;
* pense au déploiement et au suivi du modèle.

Ces compétences sont celles qui distinguent un **Data Scientist capable de produire un impact réel** d'un candidat qui maîtrise uniquement la théorie.

### Étape suivante

Après ce module, je vous proposerais une **simulation complète d'entretien SentiLink de 90 minutes**, reproduisant le format réel : questions de Python, SQL, Machine Learning, étude de cas, System Design et entretien comportemental, avec les réponses attendues et les critères d'évaluation. C'est la meilleure façon de vérifier que vous êtes réellement prêt.
