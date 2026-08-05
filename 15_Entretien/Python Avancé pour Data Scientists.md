Nous entrons maintenant dans le **MODULE 31**, qui correspond au niveau **Senior Data Scientist / Machine Learning Engineer**.

> **C'est le niveau de Python attendu chez SentiLink.**

La bonne nouvelle est que **SentiLink ne cherche pas des spécialistes de LeetCode**.

Ils recherchent quelqu'un qui sait :

* écrire du code propre ;
* manipuler efficacement les données ;
* raisonner sur la complexité ;
* produire un code prêt pour la production.

---

# MODULE 31 — Python Avancé pour Data Scientists

# Objectifs

À la fin de ce module, vous serez capable de répondre à pratiquement toutes les questions Python posées en entretien Data Science.

Nous allons couvrir :

* structures de données ;
* complexité algorithmique ;
* générateurs ;
* itérateurs ;
* décorateurs ;
* compréhensions ;
* gestion mémoire ;
* programmation orientée objet ;
* concurrence ;
* questions d'entretien.

---

# PARTIE 1 — Pourquoi Python ?

Question très fréquente.

> **Why is Python the dominant language for Data Science?**

Réponse :

> Python combines simplicity, readability, a rich ecosystem (NumPy, Pandas, Scikit-learn, PyTorch, TensorFlow), excellent community support, and seamless integration with production systems.

---

# PARTIE 2 — List vs Tuple

Question classique.

## List

```python
a = [1,2,3]
```

Mutable.

---

## Tuple

```python
a = (1,2,3)
```

Immutable.

---

### Entretien

> **When would you use a tuple instead of a list?**

Réponse :

> I use tuples for fixed collections of values that should not change, such as coordinates or database keys. Their immutability also makes them usable as dictionary keys.

---

# PARTIE 3 — Dictionary

Question extrêmement fréquente.

Pourquoi les dictionnaires sont-ils si rapides ?

Réponse :

Parce qu'ils utilisent une **table de hachage (hash table)**.

Complexité moyenne :

| Opération   | Complexité |
| ----------- | ---------- |
| Recherche   | O(1)       |
| Insertion   | O(1)       |
| Suppression | O(1)       |

Le recruteur peut demander :

> **Is dictionary lookup always O(1)?**

Réponse :

> On average, yes. In the worst case, collisions can degrade performance, but Python's hash table implementation is highly optimized.

---

# PARTIE 4 — Sets

Question.

Pourquoi utiliser un set ?

Exemple.

```python
emails = set()
```

Avantages :

* recherche rapide ;
* suppression des doublons ;
* appartenance en O(1) en moyenne.

---

# Question

Trouver les doublons.

```python
def duplicates(values):
    seen = set()
    dup = set()

    for v in values:
        if v in seen:
            dup.add(v)
        else:
            seen.add(v)

    return dup
```

Complexité :

O(n)

---

# PARTIE 5 — List Comprehension

Question très fréquente.

Code classique.

```python
squares = []

for x in range(10):
    squares.append(x*x)
```

Version Python.

```python
squares = [x*x for x in range(10)]
```

---

# Avec condition

```python
even = [x for x in range(20) if x%2==0]
```

---

# Entretien

> **When should you avoid list comprehensions?**

Réponse :

Lorsque la logique devient trop complexe et nuit à la lisibilité.

---

# PARTIE 6 — Generator

Question très fréquente.

```python
def generate():
    for i in range(1000000000):
        yield i
```

Pourquoi utiliser `yield` ?

Parce que les valeurs sont produites à la demande, sans charger toute la séquence en mémoire.

---

# Entretien

> **Difference between return and yield?**

| return                  | yield                     |
| ----------------------- | ------------------------- |
| termine la fonction     | suspend la fonction       |
| retourne une seule fois | produit plusieurs valeurs |

---

# PARTIE 7 — Iterator

Question classique.

Un itérateur implémente :

```python
__iter__()
```

et

```python
__next__()
```

Tous les générateurs sont des itérateurs, mais tous les itérateurs ne sont pas des générateurs.

---

# PARTIE 8 — Lambda

```python
numbers = [4,2,8]

sorted(numbers, key=lambda x:x)
```

Question.

Quand utiliser lambda ?

Réponse.

Pour des fonctions simples et courtes. Si la logique devient complexe, une fonction nommée est plus lisible.

---

# PARTIE 9 — map()

```python
list(map(str,[1,2,3]))
```

↓

```python
["1","2","3"]
```

---

# filter()

```python
list(filter(lambda x:x>10,numbers))
```

---

# PARTIE 10 — zip()

Très fréquent.

```python
names=["Alice","Bob"]

ages=[20,25]

list(zip(names,ages))
```

↓

```python
[
("Alice",20),

("Bob",25)
]
```

---

# PARTIE 11 — enumerate()

```python
for index,value in enumerate(items):
```

Très utilisé.

---

# PARTIE 12 — Décorateurs

Question avancée.

Exemple.

```python
def log(func):

    def wrapper(*args, **kwargs):
        print("Calling function")
        return func(*args, **kwargs)

    return wrapper
```

Puis :

```python
@log
def train():
    print("Training model")
```

Question.

Pourquoi les décorateurs ?

Réponse :

Ils permettent d'ajouter un comportement (journalisation, contrôle d'accès, mesure du temps, etc.) sans modifier le code de la fonction.

---

# PARTIE 13 — Exceptions

Question.

Pourquoi utiliser :

```python
try:
```

Réponse.

Gérer proprement les erreurs inattendues.

Exemple.

```python
try:
    value = int(text)
except ValueError:
    print("Invalid input")
```

---

# PARTIE 14 — Context Manager

Question.

Pourquoi utiliser :

```python
with open(...)
```

Parce que le fichier est automatiquement fermé, même si une exception se produit.

---

# PARTIE 15 — Mutable Default Arguments

Question très fréquente.

Erreur.

```python
def add(item, values=[]):
    values.append(item)
    return values
```

Le même objet liste est réutilisé entre les appels.

Bonne pratique :

```python
def add(item, values=None):
    if values is None:
        values = []
    values.append(item)
    return values
```

---

# PARTIE 16 — Copie

Question.

Différence entre :

```python
copy.copy()
```

et

```python
copy.deepcopy()
```

* **Shallow copy** : copie le conteneur, mais les objets imbriqués restent partagés.
* **Deep copy** : copie récursivement tous les objets.

---

# PARTIE 17 — Concurrence

Question.

> **Difference between threading and multiprocessing?**

Réponse :

* `threading` partage la mémoire entre les threads et convient surtout aux tâches d'entrée/sortie (I/O).
* `multiprocessing` crée plusieurs processus, chacun avec sa propre mémoire, ce qui permet de tirer parti de plusieurs cœurs CPU pour les tâches gourmandes en calcul.

---

# PARTIE 18 — GIL

Question classique.

> **What is the Global Interpreter Lock (GIL)?**

Réponse :

Le GIL est un mécanisme de CPython qui permet à un seul thread d'exécuter du bytecode Python à la fois. Il simplifie la gestion de la mémoire mais limite le parallélisme des tâches CPU-bound dans un même processus.

---

# PARTIE 19 — Programmation Orientée Objet

Question.

Pourquoi utiliser une classe ?

Exemple.

```python
class FraudModel:

    def __init__(self, model):
        self.model = model

    def predict(self, X):
        return self.model.predict(X)
```

Réponse :

Pour regrouper les données et les comportements associés, améliorer la réutilisabilité et faciliter la maintenance.

---

# PARTIE 20 — Dataclasses

Question.

Pourquoi utiliser :

```python
from dataclasses import dataclass
```

Réponse :

Pour créer facilement des classes destinées principalement à stocker des données, avec génération automatique de méthodes comme `__init__`, `__repr__` et `__eq__`.

---

# PARTIE 21 — Logging

Le recruteur :

> **Would you use print() in production?**

Réponse :

Non.

J'utiliserais le module `logging`, qui permet différents niveaux (`DEBUG`, `INFO`, `WARNING`, `ERROR`, `CRITICAL`) et une meilleure intégration avec les outils de supervision.

---

# PARTIE 22 — Typage

Bonne pratique.

```python
def predict(
    features: list[float]
) -> float:
    ...
```

Les annotations de type améliorent la lisibilité et permettent des vérifications statiques avec des outils comme `mypy`.

---

# PARTIE 23 — Questions fréquentes

### Q1

**Why are dictionaries fast?**

Grâce aux tables de hachage.

---

### Q2

**Difference between list and tuple?**

Mutable vs immuable.

---

### Q3

**Why use generators?**

Pour économiser la mémoire lors du traitement de grandes quantités de données.

---

### Q4

**Difference between multiprocessing and multithreading?**

CPU-bound vs I/O-bound (en première approximation sous CPython).

---

### Q5

**Difference between shallow copy and deep copy?**

Le premier partage les objets imbriqués, le second les duplique.

---

### Q6

**What is a decorator?**

Une fonction qui prend une autre fonction et lui ajoute un comportement sans modifier son code.

---

# Exercice SentiLink

Le recruteur demande :

> **Read a 100 GB CSV and compute the average transaction amount without loading the whole file into memory.**

Réponse :

```python
def average_amount(file_path):
    total = 0.0
    count = 0

    with open(file_path) as f:
        next(f)  # ignorer l'en-tête

        for line in f:
            amount = float(line.strip().split(",")[1])
            total += amount
            count += 1

    return total / count if count else 0.0
```

Puis vous ajoutez :

> Dans un contexte réel, j'utiliserais souvent `pandas.read_csv(..., chunksize=...)`, `pyarrow`, ou un moteur distribué comme Spark si le volume de données dépasse les capacités d'une seule machine.

Cette précision montre que vous savez adapter votre solution à l'échelle du problème.

---

# Ce qui différencie un candidat Senior

Un recruteur ne cherche pas quelqu'un qui connaît toutes les subtilités du langage.

Il cherche quelqu'un qui :

* écrit un code lisible et maintenable ;
* choisit les bonnes structures de données ;
* raisonne sur la complexité ;
* pense à la mémoire et aux performances ;
* sait produire un code fiable pour la production.

---

# Les prochains modules (niveau Staff Data Scientist)

Nous avons désormais couvert l'essentiel des compétences techniques. Les modules suivants permettront d'atteindre un niveau encore plus avancé :

* **Module 32** : Feature Engineering avancé pour la détection de fraude.
* **Module 33** : Détection d'anomalies et Machine Learning non supervisé.
* **Module 34** : Graph Machine Learning et résolution d'identité.
* **Module 35** : MLOps complet (CI/CD, versionnement, monitoring, drift, retraining).
* **Module 36** : 100 questions d'entretien SentiLink avec réponses détaillées.
* **Module 37** : Étude de cas complète de bout en bout (de la donnée brute au modèle en production).

Ces derniers modules correspondent à un niveau que l'on retrouve chez les meilleurs candidats en Data Science et Machine Learning Engineering.
