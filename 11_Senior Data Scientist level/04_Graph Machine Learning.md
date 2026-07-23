Nous arrivons maintenant au niveau **Expert**.

Le sujet suivant est probablement **le plus important chez SentiLink**.

En réalité, si vous regardez les publications techniques de SentiLink, Stripe, Visa, Mastercard ou Feedzai, vous constaterez qu'ils parlent énormément de **Graph Machine Learning**.

Pourquoi ?

Parce que **la fraude est rarement un acte isolé**.

Les fraudeurs travaillent souvent en **réseaux**.

---

# MODULE 11 — Graph Machine Learning (Graph ML)

## Pourquoi utiliser un graphe ?

Imaginons un modèle classique.

Vous avez :

| Customer | Device | IP  | Email   |
| -------- | ------ | --- | ------- |
| Alice    | A      | IP1 | gmail   |
| Bob      | B      | IP2 | hotmail |

Le modèle voit uniquement chaque ligne.

---

Mais imaginons maintenant :

```text
          Device A
          /      \
         /        \
     Alice ------ Bob
        |
        |
      IP 12
        |
        |
      Charlie
```

Tout change.

Pourquoi ?

Parce que :

Alice

↓

Bob

↓

Charlie

sont liés.

---

Le Machine Learning classique ne voit pas cette relation.

Le Graph ML

↓

la voit immédiatement.

---

# Définition

Un graphe est composé de :

## Nœuds (Nodes)

Exemple

* utilisateur
* téléphone
* email
* carte bancaire
* appareil
* adresse IP

---

## Arêtes (Edges)

Les liens.

Par exemple

```text
Utilisateur

↓

utilise

↓

Téléphone
```

ou

```text
Utilisateur

↓

possède

↓

Carte bancaire
```

---

Chez SentiLink,

un seul utilisateur peut être connecté à :

* plusieurs emails
* plusieurs appareils
* plusieurs cartes
* plusieurs IP

---

# Pourquoi les graphes détectent-ils mieux la fraude ?

Imaginons :

```text
Utilisateur A

↓

Téléphone 1

↓

Utilisateur B

↓

Carte 3

↓

Utilisateur C

↓

Adresse IP
```

Tous ces comptes semblent différents.

Mais

↓

ils sont connectés.

---

Le graphe révèle le réseau.

---

# Exemple réel

Une banque possède :

```text
10 millions

de comptes
```

Les fraudeurs créent :

```text
1000 faux comptes
```

Tous utilisent :

* les mêmes téléphones
* les mêmes appareils
* les mêmes IP

Le Graph ML détecte rapidement ces regroupements.

---

# Les métriques importantes

## Degree

Nombre de connexions.

Exemple

```text
Utilisateur

↓

Email

↓

Device

↓

Phone

↓

IP

↓

Carte
```

Degree

=

5

---

Question

Pourquoi ?

Un appareil connecté à :

500 comptes

↓

Très suspect.

---

# Centrality

Mesure

l'importance

d'un nœud.

Un téléphone utilisé par

100 fraudeurs

↓

Centralité élevée.

---

# Connected Components

Imaginez

```text
A

↓

B

↓

C

↓

D
```

Tous sont reliés.

↓

Même groupe.

---

Très utile

pour détecter

des réseaux criminels.

---

# Community Detection

L'objectif

↓

Trouver automatiquement

des groupes.

Algorithmes

* Louvain
* Leiden

---

Question

Pourquoi ?

Les fraudeurs

forment souvent

des communautés.

---

# PageRank

Oui,

celui de Google.

On peut l'utiliser

pour trouver

les nœuds

les plus influents.

---

# Shortest Path

Exemple

```text
Fraudeur

↓

Téléphone

↓

Utilisateur

↓

IP

↓

Autre utilisateur
```

Distance

=

4

---

Plus la distance est faible,

plus la suspicion augmente.

---

# Node Embeddings

Le problème

↓

Les graphes

ne peuvent pas

être donnés directement

à XGBoost.

Il faut

les transformer

en vecteurs.

---

Node2Vec

↓

Apprend

un vecteur

pour chaque nœud.

---

Exemple

```text
Utilisateur A

↓

[0.14
0.75
0.31
...]
```

---

Ensuite

↓

XGBoost

utilise

ce vecteur

comme feature.

---

# Graph Neural Networks (GNN)

Encore plus puissant.

Les plus connus

* GraphSAGE
* GAT
* GCN

---

Principe

Chaque nœud

apprend

à partir

de ses voisins.

---

Exemple

```text
Utilisateur

↓

Téléphone

↓

Fraudeur
```

Le modèle

comprend

que

cet utilisateur

est probablement

suspect.

---

# Pourquoi GraphSAGE ?

Parce qu'il généralise

à

de nouveaux nœuds.

Très utile

chez SentiLink.

---

# Cas pratique

Le recruteur dit

Nous avons

```text
50 millions

d'utilisateurs
```

Chaque utilisateur

possède

* téléphone
* email
* IP
* appareil

Comment détecter

les réseaux

de fraude ?

---

Réponse Senior

Je construirais un graphe où les utilisateurs, appareils, emails, téléphones et adresses IP sont représentés comme des nœuds reliés par des arêtes. J'analyserais ensuite ce graphe à l'aide de mesures comme le degré, la centralité et les composantes connexes pour identifier des structures inhabituelles. Enfin, j'utiliserais des embeddings de graphe ou un modèle de Graph Neural Network afin d'intégrer ces informations relationnelles dans un système de détection de fraude.

---

# Feature Engineering Graph

Très utilisé.

Créer

```text
device_degree
```

Nombre

de comptes

par appareil.

---

Créer

```text
phone_degree
```

---

Créer

```text
email_degree
```

---

Créer

```text
shared_ip
```

Nombre

de comptes

ayant

la même IP.

---

Créer

```text
community_size
```

---

Créer

```text
pagerank
```

---

Toutes ces variables

deviennent

des features

pour XGBoost.

---

# Pipeline Graph ML

```text
Transactions
        │
        ▼
Construction du graphe
        │
        ▼
Calcul des métriques
        │
        ▼
Embeddings Node2Vec / GraphSAGE
        │
        ▼
Création de nouvelles features
        │
        ▼
XGBoost
        │
        ▼
Prédiction
```

---

# Questions d'entretien

## Pourquoi utiliser un graphe ?

Parce que les fraudes sont souvent organisées en réseaux. Un graphe permet de représenter explicitement les relations entre les entités et de détecter des schémas qu'un modèle tabulaire ne peut pas voir.

---

## Pourquoi Node2Vec ?

Parce qu'il transforme chaque nœud du graphe en un vecteur numérique qui capture sa position et son contexte relationnel, ce qui permet ensuite de l'utiliser dans des modèles classiques.

---

## Pourquoi GraphSAGE ?

Parce qu'il peut produire des représentations pour de nouveaux nœuds qui n'étaient pas présents lors de l'entraînement, ce qui est essentiel dans un système où de nouveaux utilisateurs apparaissent en permanence.

---

## Pourquoi les GNN sont-ils puissants ?

Parce qu'ils exploitent non seulement les caractéristiques d'un nœud, mais aussi celles de ses voisins et de la structure globale du réseau.

---

# Ce qu'un excellent candidat ajoute

Un candidat de niveau senior précise également les défis pratiques :

* **Scalabilité** : un graphe de plusieurs dizaines de millions de nœuds nécessite des architectures distribuées.
* **Mise à jour** : le graphe évolue en continu avec les nouvelles transactions.
* **Latence** : certaines métriques doivent être calculées rapidement pour permettre une décision en temps réel.
* **Intégration** : les features issues du graphe sont souvent combinées avec des features tabulaires dans un modèle comme XGBoost.

---

# Étude de cas

**Question :**

> Vous observez qu'un même appareil est utilisé par 250 comptes différents, mais les transactions semblent normales. Que faites-vous ?

**Réponse attendue :**

Je ne conclurais pas immédiatement à une fraude. Je vérifierais le contexte : il peut s'agir d'un cybercafé, d'une entreprise ou d'un appareil partagé. J'analyserais également les autres liens (emails, IP, téléphones), la taille de la communauté, les historiques des comptes et les comportements observés. Le graphe fournit un signal fort, mais il doit être interprété avec les autres informations disponibles.

Cette réponse montre une qualité essentielle recherchée chez un Data Scientist : **ne pas tirer de conclusions hâtives à partir d'un seul indicateur**, mais raisonner en combinant plusieurs sources d'information.

---

### La suite de la préparation

Il ne reste plus que deux grands modules pour atteindre un niveau d'entretien très avancé :

1. **Streaming et décision en temps réel**

   * Kafka
   * Flink
   * Architecture de scoring en moins de 100 ms
   * Event-driven architecture

2. **MLOps avancé**

   * MLflow
   * Feature Store
   * Model Registry
   * A/B Testing
   * Canary Deployment
   * Shadow Deployment
   * Monitoring et réentraînement automatique

Ces deux thèmes sont très fréquemment abordés pour les postes de Data Scientist et Machine Learning Engineer dans les FinTech et les grandes entreprises spécialisées dans la détection de fraude.
