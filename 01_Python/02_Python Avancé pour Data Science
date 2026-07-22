# MODULE 1 – Python Avancé pour Data Science

# Chapitre 8 – Les fonctions Lambda

## Définition

Une **fonction lambda** est une fonction anonyme, généralement utilisée lorsqu'une fonction est simple et ne sera utilisée qu'une seule fois.

Syntaxe :

```python
lambda arguments: expression
```

Exemple :

```python
carre = lambda x: x ** 2

print(carre(5))
```

Résultat :

```text
25
```

Équivalent avec `def` :

```python
def carre(x):
    return x ** 2
```

### Quand utiliser une lambda ?

* Pour les fonctions courtes.
* Avec `map()`, `filter()`, `sorted()`.
* Pour éviter de définir une fonction nommée inutilement.

### Quand ne pas utiliser une lambda ?

* Lorsque la logique est complexe.
* Lorsqu'il y a plusieurs lignes.
* Si cela nuit à la lisibilité.

---

## Question d'entretien

**Question :** Quelle est la différence entre une fonction `lambda` et une fonction définie avec `def` ?

### Réponse attendue

Une fonction `lambda` est une fonction anonyme limitée à une seule expression. Une fonction définie avec `def` peut contenir plusieurs instructions, une documentation (`docstring`) et est plus adaptée aux traitements complexes.

---

# Chapitre 9 – map(), filter() et reduce()

Ces trois fonctions sont très fréquentes en entretien.

## map()

Applique une fonction à tous les éléments d'un itérable.

Exemple :

```python
nombres = [1, 2, 3, 4]

resultat = list(map(lambda x: x * 2, nombres))

print(resultat)
```

Résultat :

```text
[2, 4, 6, 8]
```

Équivalent avec une compréhension :

```python
resultat = [x * 2 for x in nombres]
```

### Bonnes pratiques

Aujourd'hui, en Python, les compréhensions sont souvent préférées pour leur lisibilité.

---

## filter()

Conserve uniquement les éléments qui satisfont une condition.

```python
nombres = [1,2,3,4,5,6]

pairs = list(filter(lambda x: x % 2 == 0, nombres))

print(pairs)
```

Résultat :

```text
[2,4,6]
```

---

## reduce()

Disponible dans `functools`.

```python
from functools import reduce

nombres = [1,2,3,4]

somme = reduce(lambda x,y: x+y, nombres)

print(somme)
```

Résultat

```text
10
```

---

## Question d'entretien

Pourquoi `reduce()` est-il moins utilisé aujourd'hui ?

### Réponse

Parce qu'il est souvent moins lisible qu'une boucle explicite ou que des fonctions intégrées comme `sum()`, `min()`, `max()`.

---

# Chapitre 10 – Les compréhensions

## List Comprehension

```python
carres = [x*x for x in range(10)]
```

Avec condition :

```python
pairs = [x for x in range(20) if x % 2 == 0]
```

---

## Dictionary Comprehension

```python
carres = {x: x*x for x in range(5)}
```

Résultat

```python
{
0:0,
1:1,
2:4,
3:9,
4:16
}
```

---

## Set Comprehension

```python
uniques = {x for x in [1,2,2,3,3]}
```

Résultat

```python
{1,2,3}
```

---

## Question

Pourquoi utiliser les compréhensions ?

Réponse :

* plus rapides
* plus lisibles
* plus Pythoniques
* moins de code

---

# Chapitre 11 – Itérateurs

## Définition

Un itérateur produit des éléments un par un.

Exemple :

```python
liste = [10,20,30]

it = iter(liste)

print(next(it))
print(next(it))
print(next(it))
```

Résultat

```text
10
20
30
```

---

## Pourquoi est-ce important ?

Parce que Python parcourt pratiquement tout via des itérateurs.

---

# Chapitre 12 – Les générateurs

Sujet extrêmement fréquent.

Un générateur utilise `yield`.

Exemple :

```python
def compteur():

    for i in range(5):

        yield i
```

Utilisation

```python
for x in compteur():

    print(x)
```

---

### Différence avec return

`return`

→ termine la fonction

`yield`

→ suspend la fonction

---

### Pourquoi utiliser un générateur ?

Pour économiser énormément de mémoire.

Exemple :

```python
def lire_fichier():

    with open("big.csv") as f:

        for ligne in f:

            yield ligne
```

Au lieu de charger un fichier de plusieurs Go en mémoire, on lit une ligne à la fois.

---

## Question d'entretien

Quelle différence entre une liste et un générateur ?

Réponse attendue :

| Liste                    | Générateur                        |
| ------------------------ | --------------------------------- |
| Charge tous les éléments | Produit les éléments à la demande |
| Plus rapide à réutiliser | Très économe en mémoire           |
| Réutilisable             | Consommé au fur et à mesure       |

---

# Chapitre 13 – Décorateurs

Très demandé.

Un décorateur modifie le comportement d'une fonction sans modifier son code.

Exemple :

```python
def log(func):

    def wrapper():

        print("Début")

        func()

        print("Fin")

    return wrapper
```

Puis

```python
@log
def bonjour():

    print("Bonjour")
```

Résultat

```text
Début

Bonjour

Fin
```

---

## Pourquoi les utiliser ?

Très utilisés pour :

* Logging
* Authentification
* Mesure du temps
* Cache
* Validation

---

## Question

Que fait le symbole `@` ?

Réponse

Il applique automatiquement un décorateur à une fonction ou une classe.

---

# Chapitre 14 – Gestionnaires de contexte

Le mot-clé :

```python
with
```

est omniprésent.

Exemple

```python
with open("data.csv") as f:

    contenu = f.read()
```

Pourquoi ?

Le fichier sera fermé automatiquement même si une exception est levée.

---

## Question

Pourquoi éviter :

```python
f = open(...)
```

Réponse

Parce qu'on risque d'oublier `close()`.

---

# Chapitre 15 – Collections

Module extrêmement utile.

## Counter

```python
from collections import Counter

texte = ["A","B","A","C","A"]

Counter(texte)
```

Résultat

```python
Counter({
'A':3,
'B':1,
'C':1
})
```

---

## defaultdict

Évite les erreurs de clé inexistante.

```python
from collections import defaultdict

d = defaultdict(int)

d["A"] += 1
```

---

## deque

Très performant pour les files.

```python
from collections import deque

q = deque()

q.append(1)

q.appendleft(0)
```

---

# Chapitre 16 – Dataclasses

Très utilisées en Python moderne.

Avant

```python
class Client:

    def __init__(self,id,nom):

        self.id=id

        self.nom=nom
```

Aujourd'hui

```python
from dataclasses import dataclass

@dataclass
class Client:

    id:int

    nom:str
```

Beaucoup plus simple.

---

# Chapitre 17 – Typing

Très apprécié.

```python
def somme(a:int,b:int)->int:

    return a+b
```

Ce n'est pas obligatoire, mais cela améliore la lisibilité, l'autocomplétion des IDE et les outils d'analyse statique.

---

# Questions d'entretien (Python avancé)

1. Quelle différence entre un itérateur et un générateur ?
2. Quand utiliser `yield` ?
3. Pourquoi les compréhensions sont-elles souvent préférées à `map()` ou `filter()` ?
4. Quelle est la différence entre `append()` et `extend()` ?
5. Qu'est-ce qu'un décorateur ?
6. Pourquoi utiliser `with open()` ?
7. Quelle différence entre `Counter` et un dictionnaire classique ?
8. Quels avantages offrent les `dataclasses` ?
9. Pourquoi utiliser des annotations de type (`typing`) ?
10. Donnez un cas concret où un générateur est préférable à une liste.

---

# Mini exercice d'entretien

Vous disposez d'un fichier de **20 Go** contenant des transactions bancaires. Vous devez compter le nombre de lignes correspondant à des transactions frauduleuses sans dépasser la mémoire disponible.

**Question :** Quelle approche adopteriez-vous ?

**Réponse attendue :**

* Lire le fichier ligne par ligne avec un générateur (`yield`) ou directement via un itérateur.
* Utiliser `with open(...)` pour garantir la fermeture du fichier.
* Traiter chaque ligne au fur et à mesure, sans charger tout le fichier en mémoire.
* Incrémenter un compteur lorsqu'une transaction répond aux critères de fraude.
* Cette approche réduit fortement l'utilisation de la mémoire et permet de traiter des fichiers très volumineux.

---

# Ce qui nous attend ensuite

Le prochain chapitre portera sur **NumPy**, la base du calcul scientifique en Python. Nous verrons en profondeur :

* les `ndarray` ;
* la vectorisation ;
* le broadcasting ;
* les opérations matricielles ;
* les performances par rapport aux listes Python ;
* les questions d'entretien les plus fréquentes ;
* des exercices pratiques inspirés de cas de Data Science.

C'est une étape essentielle avant d'aborder **Pandas** et les pipelines de Machine Learning.
