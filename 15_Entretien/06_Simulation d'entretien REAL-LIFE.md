Nous allons maintenant entrer dans la phase que suivent les candidats des entreprises comme **SentiLink, Stripe, Meta, Airbnb, Block et Google**.

# MODULE 29 — Simulation d'entretien REAL-LIFE (Senior Data Scientist)

> **Règle importante**
>
> À partir de maintenant, je suis votre **interviewer**.
>
> Je ne vais plus enseigner.
>
> Je vais vous évaluer exactement comme le ferait un Senior Data Scientist.

Je noterai chaque réponse sur :

| Critère                   | Points |
| ------------------------- | ------ |
| Compréhension du problème | /10    |
| Raisonnement              | /10    |
| Communication             | /10    |
| Technique                 | /10    |
| Business Thinking         | /10    |

Score total : **/50**

Une note de **40+/50** correspond généralement à un candidat très solide.

---

# Introduction

Bonjour,

Merci d'avoir accepté cet entretien.

Je vais vous poser plusieurs questions techniques.

N'hésitez pas à réfléchir avant de répondre.

Si quelque chose n'est pas clair, vous pouvez demander des précisions.

Commençons.

---

# Question 1 (Python)

On vous donne la liste suivante :

```python
transactions = [
    ("Alice",120),
    ("Bob",80),
    ("Alice",300),
    ("John",150),
    ("Bob",40),
    ("Alice",200)
]
```

Écrivez une fonction Python qui retourne :

```python
{
"Alice":620,
"Bob":120,
"John":150
}
```

Je m'intéresse autant à votre raisonnement qu'à votre code.

---

# Ce que j'attends

Vous devriez expliquer :

* la structure de données choisie ;
* la complexité temporelle ;
* la complexité mémoire.

---

Lorsque vous aurez répondu, je jouerai le rôle du recruteur et je poserai immédiatement une question de relance, comme cela se fait dans un véritable entretien.

Nous poursuivrons ensuite avec des exercices de SQL, de Machine Learning, de System Design et des questions comportementales, puis je vous fournirai une évaluation détaillée à la fin de la simulation.

Parfait. Comme nous sommes dans une préparation complète, je vais jouer **les deux rôles** : celui du **candidat** (réponse idéale) puis celui du **recruteur** (analyse et questions de relance). C'est ainsi que vous apprendrez non seulement **quoi répondre**, mais aussi **pourquoi**.

---

# Entretien 1 — Python

## Question

Écrire une fonction qui calcule le montant total des transactions par client.

```python
transactions = [
    ("Alice",120),
    ("Bob",80),
    ("Alice",300),
    ("John",150),
    ("Bob",40),
    ("Alice",200)
]
```

Résultat attendu :

```python
{
    "Alice":620,
    "Bob":120,
    "John":150
}
```

---

# Réponse idéale

Avant d'écrire le code, un bon candidat explique son approche.

> "Je vais utiliser un dictionnaire (`dict`) car il permet d'associer chaque client à son total de transactions. Pour chaque transaction, je vérifierai si le client existe déjà dans le dictionnaire. Si oui, j'ajoute le montant ; sinon, j'initialise son total."

Puis il écrit :

```python
def total_transactions(transactions):
    totals = {}

    for customer, amount in transactions:
        totals[customer] = totals.get(customer, 0) + amount

    return totals
```

Résultat :

```python
{
    'Alice': 620,
    'Bob': 120,
    'John': 150
}
```

---

# Variante plus Pythonique

Un candidat expérimenté peut proposer :

```python
from collections import defaultdict

def total_transactions(transactions):
    totals = defaultdict(int)

    for customer, amount in transactions:
        totals[customer] += amount

    return dict(totals)
```

Cette version est élégante et évite d'utiliser `get()`.

---

# Analyse de complexité

Le recruteur demande souvent :

> **What is the time complexity?**

Réponse :

```
O(n)
```

Une seule traversée de la liste.

---

Puis :

> **Space complexity?**

Réponse :

```
O(k)
```

où `k` est le nombre de clients distincts.

---

# Ce que pense le recruteur

Un candidat junior répond souvent uniquement avec le code.

Un candidat intermédiaire ajoute la complexité.

Un candidat senior explique **le choix de la structure de données**, la complexité et les alternatives.

---

# Question de relance

Le recruteur dit :

> **What if the file contains one billion transactions?**

Réponse attendue :

Je ne chargerais pas toutes les données en mémoire. Je traiterais les transactions **en flux (streaming)**, par exemple ligne par ligne à partir d'un fichier ou d'une source comme Kafka. Le dictionnaire des totaux reste en mémoire, mais les transactions sont consommées progressivement.

Exemple :

```python
totals = {}

with open("transactions.csv") as file:
    for line in file:
        customer, amount = parse(line)
        totals[customer] = totals.get(customer, 0) + amount
```

---

# Nouvelle relance

Le recruteur poursuit :

> **What if there are 500 million customers?**

C'est une excellente question.

Le dictionnaire lui-même devient trop volumineux.

Réponse :

Je réfléchirais à une solution distribuée :

* Apache Spark ;
* Hadoop/MapReduce ;
* Agrégation dans PostgreSQL ou Redshift ;
* Partitionnement des données ;
* Traitement par lots (batch processing).

Je pourrais aussi agréger localement avant de fusionner les résultats.

---

# Niveau Senior

Le recruteur appréciera si vous ajoutez :

> "Le choix dépend de l'architecture existante. Si les données sont déjà dans un entrepôt de données (Redshift, PostgreSQL), il est souvent plus efficace d'effectuer l'agrégation directement en SQL plutôt que de rapatrier les données en Python."

Cette remarque montre une vision **système**, pas uniquement algorithmique.

---

# Deuxième exercice Python

## Question

Trouver le client ayant dépensé le plus.

Exemple :

```python
transactions = [
    ("Alice",120),
    ("Bob",80),
    ("Alice",300),
    ("John",150),
    ("Bob",40),
    ("Alice",200)
]
```

Résultat attendu :

```python
("Alice", 620)
```

---

# Réponse

```python
def top_customer(transactions):
    totals = {}

    for customer, amount in transactions:
        totals[customer] = totals.get(customer, 0) + amount

    return max(totals.items(), key=lambda x: x[1])
```

---

# Question de relance

> **Can you solve it without calling `max()`?**

Oui.

```python
def top_customer(transactions):
    totals = {}

    for customer, amount in transactions:
        totals[customer] = totals.get(customer, 0) + amount

    best_customer = None
    best_total = float("-inf")

    for customer, total in totals.items():
        if total > best_total:
            best_total = total
            best_customer = customer

    return best_customer, best_total
```

---

# Troisième exercice

## Question

Compter les occurrences de chaque mot.

Entrée :

```text
fraud fraud bank risk bank fraud machine learning
```

Sortie :

```python
{
    "fraud": 3,
    "bank": 2,
    "risk": 1,
    "machine": 1,
    "learning": 1
}
```

---

# Réponse simple

```python
from collections import Counter

Counter(text.split())
```

---

# Réponse sans bibliothèque

```python
def count_words(text):
    counts = {}

    for word in text.split():
        counts[word] = counts.get(word, 0) + 1

    return counts
```

---

# Ce que le recruteur cherche

Pour un exercice aussi simple, il ne cherche pas à tester votre mémoire.

Il veut observer :

* votre manière de raisonner ;
* votre capacité à écrire un code propre ;
* votre maîtrise de la complexité ;
* votre communication.

Un candidat qui explique calmement sa démarche avant de coder obtient souvent une meilleure évaluation qu'un candidat qui écrit immédiatement du code sans justification.

---

# Évaluation de ce premier entretien Python

| Critère              | Attendu |
| -------------------- | ------: |
| Code correct         |   ⭐⭐⭐⭐⭐ |
| Lisibilité           |   ⭐⭐⭐⭐⭐ |
| Complexité expliquée |   ⭐⭐⭐⭐⭐ |
| Cas limites évoqués  |   ⭐⭐⭐⭐☆ |
| Communication        |   ⭐⭐⭐⭐⭐ |

**Score estimé : 48–50 / 50**

Ce niveau correspond à ce qu'un recruteur attend d'un très bon candidat pour des exercices Python de difficulté facile à intermédiaire.

---

# La suite

Lors d'un véritable entretien SentiLink, après cette première partie Python, le recruteur enchaîne généralement sur **SQL**, souvent considéré comme l'étape la plus discriminante.

Dans le **Module 30**, nous reproduirons un **coding interview SQL complet** avec des exercices de niveau Stripe, Airbnb et SentiLink, accompagnés des réponses détaillées, des pièges classiques et des questions de relance posées par les recruteurs.
