# MODULE 1 – Chapitre 19 : Pandas (Niveau Expert)

# Objectifs

À la fin de ce chapitre, vous serez capable de :

* Manipuler des DataFrames efficacement.
* Nettoyer des jeux de données complexes.
* Réaliser des analyses exploratoires (EDA).
* Fusionner plusieurs sources de données.
* Préparer les données pour le Machine Learning.
* Répondre aux questions d'entretien les plus fréquentes.

---

# 1. Qu'est-ce que Pandas ?

Pandas est une bibliothèque Python dédiée à la manipulation et à l'analyse de données tabulaires.

Les deux structures principales sont :

* **Series** : une colonne.
* **DataFrame** : un tableau (lignes × colonnes).

Exemple :

```python
import pandas as pd

df = pd.DataFrame({
    "nom": ["Alice", "Bob", "Charlie"],
    "age": [25, 30, 35]
})
```

Résultat :

| nom     | age |
| ------- | --: |
| Alice   |  25 |
| Bob     |  30 |
| Charlie |  35 |

---

# 2. Lire des données

CSV

```python
df = pd.read_csv("transactions.csv")
```

Excel

```python
df = pd.read_excel("transactions.xlsx")
```

SQL

```python
import sqlalchemy

engine = sqlalchemy.create_engine(...)

df = pd.read_sql(query, engine)
```

JSON

```python
df = pd.read_json("data.json")
```

---

## Question d'entretien

Comment charger un fichier CSV de plusieurs Go ?

### Réponse

Utiliser la lecture par morceaux (`chunksize`) :

```python
for chunk in pd.read_csv("transactions.csv", chunksize=100000):
    ...
```

Cette approche est essentielle lorsque le fichier ne tient pas en mémoire.

---

# 3. Les premières commandes

Toujours commencer par explorer les données.

```python
df.head()
```

Premières lignes.

---

```python
df.tail()
```

Dernières lignes.

---

```python
df.info()
```

Structure du DataFrame.

---

```python
df.describe()
```

Statistiques descriptives.

---

```python
df.columns
```

Liste des colonnes.

---

```python
df.shape
```

Nombre de lignes et de colonnes.

---

## Question

Quelle différence entre :

```python
shape
```

et

```python
info()
```

### Réponse

* `shape` retourne uniquement le nombre de lignes et de colonnes.
* `info()` fournit également les types des colonnes, le nombre de valeurs non nulles et l'utilisation mémoire.

---

# 4. Sélection de colonnes

Une colonne

```python
df["age"]
```

Plusieurs colonnes

```python
df[["nom","age"]]
```

---

# 5. loc et iloc

Question très fréquente.

## loc

Basé sur les **étiquettes**.

```python
df.loc[5]
```

---

## iloc

Basé sur les **positions**.

```python
df.iloc[5]
```

---

### Exemple

```python
df.loc[:,["nom","age"]]
```

Toutes les lignes

Deux colonnes

---

```python
df.iloc[0:5,0:2]
```

Les cinq premières lignes

Les deux premières colonnes

---

## Question d'entretien

Quelle différence entre `loc` et `iloc` ?

### Réponse

* `loc` utilise les noms d'index et de colonnes.
* `iloc` utilise les positions numériques.

---

# 6. Filtrage

Exemple

```python
df[df["age"] > 30]
```

Deux conditions

```python
df[
    (df["age"] > 30) &
    (df["ville"]=="Paris")
]
```

OU

```python
|
```

---

# 7. Trier

```python
df.sort_values("age")
```

Décroissant

```python
df.sort_values("age", ascending=False)
```

---

# 8. Colonnes calculées

```python
df["salaire_annuel"] = df["salaire"] * 12
```

---

# 9. Valeurs manquantes

Sujet incontournable.

Détection

```python
df.isna()
```

Compter

```python
df.isna().sum()
```

Suppression

```python
df.dropna()
```

Remplacement

```python
df.fillna(0)
```

Remplacer par la moyenne

```python
df["age"].fillna(
    df["age"].mean()
)
```

---

## Question

Quand supprimer des lignes ?

Quand remplacer les valeurs manquantes ?

### Réponse

* Supprimer si peu de lignes sont concernées et qu'elles ne sont pas critiques.
* Remplacer (imputation) lorsque la perte de données serait importante ou lorsqu'une stratégie statistique est plus adaptée.

---

# 10. Doublons

Détection

```python
df.duplicated()
```

Suppression

```python
df.drop_duplicates()
```

---

# 11. groupby()

Le roi de Pandas.

Exemple

```python
df.groupby("ville")["salaire"].mean()
```

Résultat

| Ville | Salaire moyen |
| ----- | ------------: |
| Paris |          3500 |
| Lyon  |          2900 |

---

Plusieurs agrégations

```python
df.groupby("ville").agg({
    "salaire":["mean","max","min"],
    "age":"mean"
})
```

---

## Question

Pourquoi utiliser `groupby()` ?

Réponse

Pour regrouper des observations selon une ou plusieurs colonnes et calculer des statistiques agrégées.

---

# 12. merge()

Très demandé.

Clients

| id | nom   |
| -- | ----- |
| 1  | Alice |
| 2  | Bob   |

Transactions

| id | montant |
| -- | ------: |
| 1  |     100 |
| 2  |     200 |

```python
pd.merge(
    clients,
    transactions,
    on="id"
)
```

---

Types

```python
how="inner"
```

```python
how="left"
```

```python
how="right"
```

```python
how="outer"
```

---

## Question

Différence entre INNER JOIN et LEFT JOIN ?

### Réponse

* **INNER JOIN** : uniquement les lignes présentes dans les deux tables.
* **LEFT JOIN** : toutes les lignes de la table de gauche, avec les correspondances de la table de droite si elles existent.

---

# 13. concat()

Concatène plusieurs DataFrames.

```python
pd.concat([df1,df2])
```

---

## Différence

merge

↓

comme un JOIN SQL

concat

↓

comme un UNION

---

# 14. pivot_table()

Très utilisée.

```python
pd.pivot_table(
    df,
    values="montant",
    index="ville",
    aggfunc="mean"
)
```

---

# 15. apply()

```python
df["age2"] = df["age"].apply(
    lambda x:x+10
)
```

---

Attention :

Pour les opérations simples, privilégier la vectorisation.

```python
df["age2"] = df["age"] + 10
```

Plus rapide.

---

# 16. Dates

Conversion

```python
df["date"] = pd.to_datetime(
    df["date"]
)
```

Extraction

```python
df["annee"] = df["date"].dt.year
```

```python
df["mois"] = df["date"].dt.month
```

```python
df["jour"] = df["date"].dt.day
```

---

# 17. Export

CSV

```python
df.to_csv(
    "result.csv",
    index=False
)
```

Excel

```python
df.to_excel(
    "result.xlsx",
    index=False
)
```

---

# Cas pratique : Détection de fraude

Imaginez un DataFrame contenant les colonnes suivantes :

| transaction_id | client_id | montant | pays | device  | fraude |
| -------------- | --------- | ------: | ---- | ------- | ------ |
| 1              | 101       |    2500 | FR   | iPhone  | 1      |
| 2              | 101       |      40 | FR   | iPhone  | 0      |
| 3              | 102       |   15000 | RU   | Android | 1      |
| 4              | 103       |      35 | FR   | Windows | 0      |

Questions possibles :

### Quel est le montant moyen des transactions frauduleuses ?

```python
df[df["fraude"] == 1]["montant"].mean()
```

---

### Combien de transactions par pays ?

```python
df.groupby("pays").size()
```

---

### Quel est le montant maximal par client ?

```python
df.groupby("client_id")["montant"].max()
```

---

### Quels clients ont réalisé plus de 5 transactions ?

```python
df.groupby("client_id").size().loc[lambda s: s > 5]
```

---

# Les 20 questions d'entretien les plus fréquentes

## Niveau débutant

1. Différence entre `Series` et `DataFrame`.
2. Différence entre `loc` et `iloc`.
3. Que fait `head()` ?
4. À quoi sert `describe()` ?
5. Comment lire un CSV ?

## Niveau intermédiaire

6. Différence entre `merge()` et `concat()`.
7. Différence entre `apply()` et une opération vectorisée.
8. Comment supprimer les doublons ?
9. Comment gérer les valeurs manquantes ?
10. À quoi sert `groupby()` ?

## Niveau avancé

11. Comment optimiser un DataFrame de 50 millions de lignes ?
12. Pourquoi éviter `apply()` lorsque c'est possible ?
13. Comment lire un fichier de plusieurs Go ?
14. Quelle différence entre `merge()` et un `JOIN` SQL ?
15. Quand utiliser `pivot_table()` plutôt que `groupby()` ?
16. Comment réduire l'utilisation mémoire d'un DataFrame ?
17. Quelle différence entre `copy()` et une simple affectation ?
18. Pourquoi convertir une colonne `object` en `category` ?
19. Comment créer des variables temporelles (année, mois, jour) à partir d'une date ?
20. Quelles étapes effectuez-vous avant d'entraîner un modèle sur un nouveau jeu de données ?

---

# Exercice de niveau entretien (inspiré de SentiLink)

Vous recevez trois fichiers :

* `customers.csv`
* `transactions.csv`
* `devices.csv`

**Objectif :** construire un DataFrame d'entraînement pour un modèle de détection de fraude.

Décrivez les étapes que vous suivriez.

### Réponse attendue

1. Charger les trois fichiers avec `read_csv()`.
2. Vérifier les types de données (`info()`).
3. Détecter et traiter les valeurs manquantes.
4. Supprimer ou consolider les doublons.
5. Fusionner les données avec `merge()` sur les clés appropriées (`client_id`, `device_id`, etc.).
6. Créer des variables dérivées (nombre de transactions par client, montant moyen, ancienneté du compte, fréquence des changements de périphérique, etc.).
7. Vérifier la qualité des données et les distributions.
8. Préparer le jeu de données pour l'entraînement (encodage, séparation des variables explicatives et de la cible).

---

## Fin du Module 1

À ce stade, vous maîtrisez les bases solides de Python nécessaires pour un entretien de Data Scientist.

Le **Module 2** sera consacré à **SQL pour Data Scientists**. Nous irons au-delà de la syntaxe : optimisation des requêtes, Window Functions, CTE récursives, plans d'exécution (`EXPLAIN`), indexation et cas pratiques inspirés d'entretiens techniques. C'est l'un des modules les plus importants après Python et Machine Learning.
