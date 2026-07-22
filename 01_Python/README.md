
Module 1 – Python pour la Data Science et les entretiens
Objectifs

À la fin de ce module, vous serez capable de :

Maîtriser Python pour la Data Science.
Manipuler efficacement des données avec Pandas et NumPy.
Écrire un code propre, lisible et maintenable.
Comprendre les questions techniques fréquemment posées en entretien.
Réaliser les manipulations de données les plus courantes.
Être prêt pour les exercices de live coding.
Plan du Module
Chapitre 1 : Les fondamentaux de Python
1.1 Les types de données

Les principaux types sont :

# Entiers
x = 10

# Réels
y = 3.14

# Chaînes
nom = "Alice"

# Booléens
actif = True

# Valeur nulle
z = None
À retenir
Type	Exemple	Mutable
int	5	Non
float	5.6	Non
bool	True	Non
str	"Python"	Non
list	[1,2,3]	Oui
tuple	(1,2,3)	Non
set	{1,2,3}	Oui
dict	{"a":1}	Oui
Question d'entretien

Question :

Quelle est la différence entre une liste et un tuple ?

Réponse attendue

Une liste est mutable : on peut modifier son contenu après sa création.

Un tuple est immuable : son contenu ne peut pas être modifié.

Les tuples sont généralement plus rapides et peuvent être utilisés comme clés de dictionnaire lorsqu'ils contiennent uniquement des éléments immuables.

Exemple
liste = [1, 2, 3]
liste.append(4)

print(liste)

Résultat

[1, 2, 3, 4]
t = (1, 2, 3)

# Ceci provoque une erreur
t[0] = 10
Question piège

Pourquoi utiliser un tuple plutôt qu'une liste ?

Bonne réponse
données constantes
meilleures performances
moins de mémoire
peut servir de clé dans un dictionnaire
1.2 Variables et références

Beaucoup de candidats échouent ici.

a = [1,2,3]

b = a

b.append(4)

print(a)

Résultat

[1,2,3,4]

Pourquoi ?

Parce que :

a ------\
         \
          [1,2,3]
         /
b ------/

Les deux variables pointent vers le même objet.

Pour copier :

b = a.copy()

ou

import copy

b = copy.deepcopy(a)
Question d'entretien

Quelle différence entre :

a = b

et

a = b.copy()

Réponse :

Le premier partage la même référence mémoire. Le second crée une nouvelle liste indépendante (copie superficielle).

Chapitre 2 : Les structures de contrôle
Conditions
age = 20

if age >= 18:
    print("Majeur")
else:
    print("Mineur")
Boucles
for i in range(5):
    print(i)
while condition:
    ...
Compréhension de listes

Très demandée.

carres = [x*x for x in range(10)]

équivalent à

carres = []

for x in range(10):
    carres.append(x*x)
Question

Pourquoi utiliser les compréhensions ?

Réponse :

plus lisibles
souvent plus rapides
plus "Pythoniques"
Chapitre 3 : Fonctions
def aire(longueur, largeur):
    return longueur * largeur

Fonctions avec valeur par défaut

def saluer(nom="Invité"):
    print(f"Bonjour {nom}")

Arguments nommés

aire(
    longueur=10,
    largeur=5
)
*args et **kwargs
def somme(*args):
    return sum(args)
somme(1,2,3,4)
def afficher(**kwargs):
    print(kwargs)

Question classique

Quelle différence entre :

*args

**kwargs

Réponse

*args reçoit un nombre variable d'arguments positionnels sous forme de tuple.
**kwargs reçoit un nombre variable d'arguments nommés sous forme de dictionnaire.
Chapitre 4 : Programmation orientée objet

Classe

class Compte:

    def __init__(self, titulaire, solde):
        self.titulaire = titulaire
        self.solde = solde

    def depot(self, montant):
        self.solde += montant

Création

c = Compte("Alice",1000)

c.depot(500)

Question

Pourquoi utiliser la POO ?

Réponse

encapsulation
réutilisation
maintenance
extensibilité
Chapitre 5 : Gestion des exceptions
try:
    x = 10/0

except ZeroDivisionError:

    print("Division impossible")

Toujours éviter :

except:
    pass

car cela masque les erreurs.

Chapitre 6 : Les modules
import math

math.sqrt(25)

Créer son propre module

utils.py
def somme(a,b):
    return a+b

Puis

from utils import somme
Chapitre 7 : Les fichiers

Lire un fichier

with open("data.csv") as f:
    contenu = f.read()

Le mot-clé with garantit la fermeture du fichier, même en cas d'erreur.

Exercices
Exercice 1

Écrire une fonction qui retourne le maximum d'une liste sans utiliser max().

Exercice 2

Compter le nombre d'occurrences de chaque mot dans une phrase.

Exemple :

bonjour le monde bonjour python

Résultat attendu :

{
    "bonjour":2,
    "le":1,
    "monde":1,
    "python":1
}
Exercice 3

Écrire une fonction qui détermine si une chaîne est un palindrome.

Exemple :

radar

→ True

Exercice 4

Fusionner deux dictionnaires.

Exercice 5

Trier une liste de dictionnaires représentant des employés selon leur salaire.

Questions d'entretien les plus fréquentes
Quelle différence entre une liste et un tuple ?
Que signifie la mutabilité d'un objet ?
Quelle différence entre == et is ?
Qu'est-ce qu'une copie superficielle (shallow copy) et une copie profonde (deep copy) ?
Expliquez le fonctionnement du ramasse-miettes (garbage collector) en Python.
Pourquoi utiliser with lors de la manipulation de fichiers ?
À quoi servent les générateurs (yield) et quels avantages offrent-ils ?
Quelle différence entre append() et extend() ?
Comment fonctionne une compréhension de liste et quand l'utiliser ?
Pourquoi éviter except: sans préciser le type d'exception ?
Ce que les recruteurs attendent

Ils ne cherchent pas uniquement quelqu'un qui connaît la syntaxe, mais quelqu'un qui comprend le comportement de Python en profondeur. Les questions portent souvent sur la gestion de la mémoire, les références d'objets, les structures de données, les bonnes pratiques de codage et la capacité à écrire un code clair et robuste.

Prochaine étape

Avant d'aborder Pandas et NumPy, je recommande d'approfondir quelques notions Python qui reviennent très souvent en entretien et qui font la différence entre un candidat débutant et un candidat intermédiaire :

Fonctions lambda
map(), filter() et reduce()
Itérateurs et générateurs (yield)
Décorateurs
Gestionnaires de contexte (with)
Compréhensions (listes, ensembles, dictionnaires)
Complexité algorithmique (Big O) appliquée aux structures Python
collections (Counter, defaultdict, deque, namedtuple)
dataclasses
typing (annotations de types)
logging et organisation d'un projet Python

Ces sujets sont très fréquemment abordés dans les entretiens pour des postes de Data Scientist et Machine Learning Engineer, et ils seront particulièrement utiles avant de passer à Pandas, NumPy et au traitement de données.
