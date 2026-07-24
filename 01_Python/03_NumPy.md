Nous entrons maintenant dans l'une des parties **les plus importantes** pour un Data Scientist. Chez **SentiLink**, **Stripe**, **PayPal**, **Amazon**, **Google**, etc., NumPy est la base de presque tout le Machine Learning. Pandas, Scikit-Learn, TensorFlow et PyTorch s'appuient sur des concepts issus de NumPy.

---

# MODULE 1 – Chapitre 18 : NumPy (Niveau Entretien)

# Objectifs

À la fin de ce chapitre, vous serez capable de :

* Comprendre pourquoi NumPy est beaucoup plus rapide que les listes Python.
* Manipuler des tableaux multidimensionnels.
* Effectuer des opérations vectorisées.
* Comprendre le broadcasting.
* Réaliser des calculs matriciels.
* Répondre aux questions d'entretien les plus fréquentes.

---

# 1. Qu'est-ce que NumPy ?

NumPy (**Numerical Python**) est une bibliothèque destinée au calcul scientifique.

Son objet principal est le **ndarray** (*N-dimensional array*).

Contrairement aux listes Python, un `ndarray` :

* stocke des données de même type,
* occupe moins de mémoire,
* est optimisé en langage C,
* permet des calculs vectorisés très rapides.

---

# 2. Pourquoi NumPy est-il beaucoup plus rapide ?

Prenons un exemple.

Avec une liste Python :

```python
a = [1,2,3,4]

b = []

for x in a:
    b.append(x*2)
```

Avec NumPy :

```python
import numpy as np

a = np.array([1,2,3,4])

b = a * 2
```

Une seule instruction.

Aucune boucle Python.

NumPy effectue les calculs directement en mémoire grâce à du code compilé.

---

## Question d'entretien

Pourquoi NumPy est-il plus rapide que Python ?

### Réponse attendue

Parce que :

* les données sont stockées dans une zone mémoire continue ;
* tous les éléments ont le même type ;
* les opérations sont implémentées en C ;
* les calculs sont vectorisés, évitant les boucles Python.

---

# 3. Création d'un tableau

```python
import numpy as np

a = np.array([1,2,3,4])
```

Tableau 2D

```python
A = np.array([
    [1,2],
    [3,4]
])
```

Tableau 3D

```python
cube = np.array([
    [
        [1,2],
        [3,4]
    ]
])
```

---

# 4. Les propriétés importantes

```python
A.shape
```

Retourne

```python
(2,2)
```

---

```python
A.ndim
```

Retourne

```python
2
```

---

```python
A.size
```

Retourne

```python
4
```

---

```python
A.dtype
```

Retourne

```python
int64
```

---

### Question

Quelle différence entre

```python
shape

size

ndim
```

Réponse :

* **shape** → dimensions du tableau
* **size** → nombre total d'éléments
* **ndim** → nombre de dimensions

---

# 5. Création automatique

Très fréquent.

```python
np.zeros((3,4))
```

Produit

```
0 0 0 0

0 0 0 0

0 0 0 0
```

---

```python
np.ones((2,3))
```

---

```python
np.eye(3)
```

Matrice identité

---

```python
np.arange(10)
```

Produit

```
0 1 2 3 4 5 6 7 8 9
```

---

```python
np.linspace(0,1,5)
```

Produit

```
0

0.25

0.50

0.75

1
```

---

## Question

Différence entre

```python
arange

linspace
```

Réponse

**arange**

→ utilise un pas.

**linspace**

→ utilise un nombre fixe de valeurs.

---

# 6. Indexation

```python
A = np.array([10,20,30,40])

A[0]
```

Résultat

```
10
```

---

```python
A[-1]
```

Résultat

```
40
```

---

# Tableau 2D

```python
A = np.array([
    [1,2],
    [3,4]
])
```

```python
A[1,0]
```

Résultat

```
3
```

---

# 7. Slicing

```python
A[1:4]
```

---

```python
A[:,1]
```

Toutes les lignes

Deuxième colonne

---

```python
A[0,:]
```

Première ligne

---

## Question

Comment sélectionner uniquement la deuxième colonne ?

Réponse

```python
A[:,1]
```

---

# 8. Vectorisation

Sujet incontournable.

Sans NumPy

```python
for i in range(len(A)):
    A[i] *= 2
```

Avec NumPy

```python
A = A * 2
```

Beaucoup plus rapide.

---

# 9. Les opérations

Addition

```python
A+B
```

---

Multiplication

```python
A*B
```

Attention :

Ici il s'agit d'une multiplication **élément par élément**.

---

Pour la multiplication matricielle

```python
A @ B
```

ou

```python
np.dot(A,B)
```

---

## Question

Quelle différence entre

```python
A*B
```

et

```python
A@B
```

Réponse

`*`

→ multiplication élément par élément.

`@`

→ multiplication matricielle.

C'est une question très fréquente.

---

# 10. Broadcasting

Sujet adoré en entretien.

```python
A = np.array([
    [1,2],
    [3,4]
])

A + 10
```

Résultat

```
11 12

13 14
```

Le nombre 10 est automatiquement "diffusé" (broadcast) sur tout le tableau.

---

Autre exemple

```python
A = np.array([
    [1,2,3],
    [4,5,6]
])

v = np.array([10,20,30])

A+v
```

Résultat

```
11 22 33

14 25 36
```

---

## Question

Qu'est-ce que le broadcasting ?

### Réponse attendue

Le broadcasting est le mécanisme par lequel NumPy applique automatiquement des opérations entre des tableaux de dimensions compatibles sans recopier explicitement les données.

---

# 11. Fonctions statistiques

Très utilisées.

```python
A.mean()
```

---

```python
A.std()
```

---

```python
A.max()
```

---

```python
A.min()
```

---

```python
A.sum()
```

---

```python
A.var()
```

---

Par axe

```python
A.mean(axis=0)
```

Moyenne des colonnes.

---

```python
A.mean(axis=1)
```

Moyenne des lignes.

---

## Question

Quelle différence entre

```
axis=0

axis=1
```

Réponse

* `axis=0` : les calculs se font colonne par colonne.
* `axis=1` : les calculs se font ligne par ligne.

Exemple :

```python
A = np.array([
    [1,2,3],
    [4,5,6]
])
```

`A.sum(axis=0)` donne :

```text
[5,7,9]
```

`A.sum(axis=1)` donne :

```text
[6,15]
```

---

# 12. Masques booléens

Extrêmement utilisés.

```python
A = np.array([1,5,8,10])

A>5
```

Résultat

```
False

False

True

True
```

Puis

```python
A[A>5]
```

Résultat

```
8

10
```

---

En Data Science, cette technique est très utilisée pour filtrer des observations.

---

# 13. Reshape

```python
A = np.arange(12)

A.reshape(3,4)
```

Résultat

```
0 1 2 3

4 5 6 7

8 9 10 11
```

---

## Question

Pourquoi utiliser `reshape()` ?

Réponse

Pour modifier la forme d'un tableau sans changer ses données, par exemple avant de fournir des données à un modèle de Machine Learning.

---

# 14. Flatten

Transformer une matrice en vecteur.

```python
A.flatten()
```

---

# 15. Transposée

```python
A.T
```

Inverse lignes et colonnes.

---

# Questions d'entretien

## Question 1

Pourquoi NumPy est-il beaucoup plus rapide que Python ?

---

## Question 2

Différence entre une liste Python et un ndarray ?

---

## Question 3

Qu'est-ce que le broadcasting ?

---

## Question 4

Différence entre

```python
*
```

et

```python
@
```

---

## Question 5

Comment sélectionner la troisième colonne d'une matrice ?

Réponse

```python
A[:,2]
```

---

## Question 6

Pourquoi utiliser des opérations vectorisées ?

Réponse

Les opérations vectorisées éliminent les boucles Python, exploitent les implémentations optimisées en C et améliorent fortement les performances.

---

# Exercice d'entretien

On vous donne une matrice représentant les montants des transactions de trois clients sur quatre jours :

```python
transactions = np.array([
    [120, 80, 95, 150],
    [45, 300, 110, 90],
    [200, 180, 210, 220]
])
```

**Questions :**

1. Calculez le montant total des transactions pour chaque client.
2. Calculez le montant moyen par jour.
3. Sélectionnez toutes les transactions supérieures à 150.
4. Multipliez toutes les transactions par 1,05 pour simuler une augmentation de 5 %.
5. Normalisez les données (soustrayez la moyenne globale puis divisez par l'écart-type).

---

# Ce que les recruteurs attendent

Ils veulent vérifier que vous savez utiliser NumPy comme un outil de calcul efficace, et non comme une simple alternative aux listes Python. Vous devez être capable d'expliquer vos choix, d'éviter les boucles inutiles et de manipuler des tableaux de façon idiomatique.

## Prochaine étape : **Pandas (niveau expert)**

Le prochain chapitre sera consacré à **Pandas**, probablement la bibliothèque la plus utilisée dans les entretiens de Data Scientist. Nous couvrirons :

* `DataFrame` et `Series`
* lecture de fichiers (CSV, Excel, SQL)
* sélection, filtrage et indexation
* nettoyage des données
* gestion des valeurs manquantes
* `groupby`
* `merge` et `join`
* `pivot_table`
* agrégations
* optimisation des performances
* plus de **80 questions d'entretien** avec réponses détaillées et études de cas inspirées de jeux de données réels.
