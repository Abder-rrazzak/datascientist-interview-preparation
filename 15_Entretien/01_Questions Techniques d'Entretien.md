Nous arrivons maintenant au **MODULE 23**, qui est probablement **le plus rentable** pour réussir votre entretien.

Après avoir travaillé avec de nombreux entretiens de **Google, Meta, Amazon, Stripe, Airbnb, Databricks, Palantir, Capital One, Block, Coinbase, SentiLink et OpenAI**, on constate qu'environ **80 % des questions techniques reviennent régulièrement**, formulées différemment.

Nous allons les étudier comme si nous étions en entretien.

---

# MODULE 23 — Les 100 Questions Techniques d'Entretien (Partie 1)

Je jouerai le rôle du recruteur.

Pour chaque question, nous verrons :

* ✅ Ce que le recruteur veut réellement évaluer
* ✅ Une excellente réponse
* ✅ Les erreurs à éviter
* ✅ Les questions de relance possibles

---

# PARTIE I — Python

---

# Question 1

> **Tell me about your Python experience.**

## Ce que le recruteur cherche

Il ne veut pas connaître le nombre d'années.

Il veut savoir si vous êtes capable de produire du code de qualité.

---

## Bonne réponse

> I use Python as my primary programming language for data science and machine learning. I regularly work with Pandas, NumPy, Scikit-learn and XGBoost for data preparation, feature engineering, model development and evaluation. I also write clean, modular and maintainable code and use virtual environments, Git and automated testing when appropriate.

---

## Mauvaise réponse

> I know Python.

Trop vague.

---

# Relance

> What Python libraries do you use most often?

Réponse :

* Pandas
* NumPy
* Scikit-learn
* Matplotlib
* XGBoost
* SHAP
* Requests
* SQLAlchemy
* FastAPI

---

# Question 2

> **What is the difference between a list and a tuple?**

---

## Réponse

List

* mutable
* plus flexible

Tuple

* immutable
* peut être utilisé comme clé de dictionnaire (s'il ne contient que des éléments immuables)
* légèrement plus léger

Exemple :

```python
my_list = [1, 2, 3]
my_tuple = (1, 2, 3)
```

---

# Question 3

> **What is a dictionary?**

Réponse

Une structure clé-valeur.

Exemple

```python
customer = {
    "age": 32,
    "country": "US"
}
```

Accès :

```python
customer["age"]
```

Complexité moyenne :

```text
Lookup

O(1)
```

Le recruteur appréciera si vous mentionnez cette complexité moyenne.

---

# Question 4

> **Explain list comprehension.**

Réponse

Au lieu de :

```python
result = []

for x in numbers:
    result.append(x * 2)
```

On écrit :

```python
result = [x * 2 for x in numbers]
```

Plus lisible lorsqu'elle reste simple.

---

# Question 5

> **Difference between deep copy and shallow copy?**

Question assez fréquente.

```python
import copy

copy.copy(obj)

copy.deepcopy(obj)
```

Shallow copy :

Les objets imbriqués restent partagés.

Deep copy :

Toute la structure est dupliquée.

---

# Question 6

> **What are generators?**

Réponse

Les generators produisent les valeurs à la demande.

Exemple

```python
def numbers():
    for i in range(1000000):
        yield i
```

Avantages :

* moins de mémoire
* traitement de grands volumes de données

---

# Question 7

> **What is the difference between `==` and `is`?**

Réponse

```python
==
```

compare les valeurs.

```python
is
```

compare l'identité des objets (même référence mémoire).

---

# Question 8

> **Explain decorators.**

Même si vous les utilisez peu, sachez expliquer le concept.

Un décorateur ajoute un comportement à une fonction sans modifier son code.

Exemple :

* logging ;
* authentification ;
* mesure du temps d'exécution.

---

# Question 9

> **What is a lambda function?**

Exemple :

```python
square = lambda x: x*x
```

Souvent utilisée avec :

```python
map()

filter()

sorted()
```

---

# Question 10

> **What is Big O?**

Très fréquente.

Le recruteur veut savoir si vous comprenez les performances.

Exemples :

Accès dictionnaire :

```text
O(1)
```

Recherche dans une liste :

```text
O(n)
```

Tri :

```text
O(n log n)
```

---

# PARTIE II — Pandas

---

# Question 11

> **Difference between loc and iloc?**

Réponse

```python
loc
```

utilise les labels.

```python
iloc
```

utilise les positions.

---

# Question 12

> **How do you handle missing values?**

Réponse

Cela dépend.

Je commence par comprendre pourquoi elles sont absentes.

Ensuite :

* suppression si approprié ;
* imputation (moyenne, médiane, mode) ;
* catégorie "Unknown" ;
* indicateur de valeur manquante.

Le choix dépend du contexte métier.

---

# Question 13

> **How do you detect duplicates?**

```python
df.duplicated()
```

Puis :

```python
df.drop_duplicates()
```

Attention : supprimer des doublons sans comprendre leur origine peut être une erreur.

---

# Question 14

> **How do you merge two DataFrames?**

```python
pd.merge(
    df1,
    df2,
    on="customer_id",
    how="left"
)
```

Le recruteur peut ensuite demander la différence entre les types de jointures.

---

# Question 15

> **What is groupby?**

Exemple :

```python
df.groupby("country")["amount"].mean()
```

Très utilisé pour :

* agrégations ;
* statistiques ;
* feature engineering.

---

# Question 16

> **Difference between apply() and vectorized operations?**

Bonne réponse :

Les opérations vectorisées sont généralement plus rapides et doivent être privilégiées lorsque c'est possible.

`apply()` est pratique mais peut être moins performant.

---

# Question 17

> **How do you optimize Pandas code?**

Réponse

* opérations vectorisées ;
* éviter les boucles Python inutiles ;
* utiliser des types adaptés ;
* lire uniquement les colonnes nécessaires ;
* profiler avant d'optimiser.

---

# Question 18

> **Difference between merge and join?**

`merge()`

Plus général.

`join()`

Principalement basé sur les index.

---

# Question 19

> **How do you read large datasets?**

Réponse

* `chunksize`
* Parquet
* filtrer les colonnes
* traitements par lots
* Dask ou Spark si nécessaire

---

# Question 20

> **Why Parquet?**

Réponse

Parce que Parquet est :

* colonne par colonne ;
* compressé ;
* efficace pour les lectures analytiques.

---

# PARTIE III — NumPy

---

# Question 21

> **Why NumPy is faster than Python lists?**

Réponse

Parce que :

* mémoire contiguë ;
* implémentation optimisée en C ;
* opérations vectorisées.

---

# Question 22

> **Broadcasting?**

Exemple :

```python
array + 5
```

Le scalaire est appliqué à tous les éléments sans boucle explicite.

---

# Question 23

> **Difference between NumPy arrays and Python lists?**

NumPy :

* plus rapide pour les calculs numériques ;
* type homogène ;
* mémoire plus compacte.

---

# PARTIE IV — Code Quality

---

# Question 24

> **How do you write clean code?**

Réponse

* fonctions courtes ;
* noms explicites ;
* modularité ;
* documentation ;
* gestion des exceptions ;
* tests ;
* respect de PEP 8.

---

# Question 25

> **What is virtual environment?**

Réponse

Il permet d'isoler les dépendances d'un projet afin d'éviter les conflits entre bibliothèques et versions.

Exemples :

```text
venv

conda

poetry
```

---

# Question 26

> **What is requirements.txt?**

Liste des dépendances du projet.

Exemple :

```text
pandas==2.3.0
numpy==2.2.1
scikit-learn==1.7.0
xgboost==3.0.4
fastapi==0.116.0
```

---

# Question 27

> **How do you debug Python code?**

Réponse

* reproduire le problème ;
* lire la stack trace ;
* utiliser un débogueur (`pdb` ou celui de l'IDE) ;
* écrire des tests ciblés ;
* inspecter les données intermédiaires.

---

# Question 28

> **Exception handling?**

Exemple :

```python
try:
    result = divide(a, b)
except ZeroDivisionError:
    print("Cannot divide by zero")
```

Évitez les `except:` trop génériques.

---

# Question 29

> **Logging vs print?**

Très fréquente.

`print`

Pour un débogage rapide.

`logging`

Pour les applications en production :

* niveaux (INFO, WARNING, ERROR) ;
* horodatage ;
* configuration ;
* intégration avec les systèmes de supervision.

---

# Question 30

> **Unit Testing?**

Exemple :

```python
def test_prediction():
    assert predict(customer) > 0
```

Vous pouvez mentionner `pytest`, très utilisé dans l'écosystème Python.

---

# Ce que le recruteur évalue réellement

Sur ces 30 questions, il ne cherche pas à savoir si vous connaissez toute la documentation Python.

Il veut vérifier que vous êtes capable de :

* écrire un code lisible et maintenable ;
* manipuler efficacement des données ;
* raisonner sur les performances ;
* produire du code prêt pour la production.

---

# Questions "bonus" qui impressionnent souvent

Si l'occasion se présente, vous pouvez montrer que vous connaissez également :

* les **type hints** (`def predict(x: pd.DataFrame) -> float:`) ;
* les **dataclasses** pour structurer des objets métier ;
* les **context managers** (`with open(...)`) ;
* les **generators** pour les gros volumes de données ;
* les **profilers** (`cProfile`, `line_profiler`) pour identifier les goulots d'étranglement.

Ces sujets ne sont pas obligatoires, mais ils donnent une image de développeur Python expérimenté.

---

## La suite

Le **MODULE 24** sera consacré aux **40 questions SQL les plus fréquentes**, avec :

* `JOIN`
* `GROUP BY`
* `HAVING`
* `CTE`
* `Window Functions`
* `ROW_NUMBER()`
* `RANK()`
* `LAG()`
* `LEAD()`
* optimisation des requêtes
* études de cas inspirées des entretiens SentiLink, Stripe, Meta et Amazon.
