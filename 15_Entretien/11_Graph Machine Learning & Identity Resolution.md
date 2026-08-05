Nous arrivons maintenant au **MODULE 34**, qui est probablement **LE module qui différencie un bon Data Scientist d'un excellent candidat chez SentiLink**.

> **Pourquoi ?**
>
> La majorité des entreprises construisent des modèles à partir de **tables**.
>
> SentiLink, comme certaines équipes de Stripe, Visa ou PayPal, exploite également les **relations entre les entités** (clients, appareils, emails, numéros de téléphone, adresses IP, comptes, etc.).
>
> C'est précisément ce domaine que l'on appelle **Identity Resolution** et **Graph Machine Learning**.

---

# MODULE 34 — Graph Machine Learning & Identity Resolution

## Objectifs

À la fin de ce module, vous serez capable de :

* comprendre ce qu'est un graphe ;
* modéliser des données d'identité sous forme de graphe ;
* créer des features issues d'un graphe ;
* expliquer pourquoi les graphes sont si efficaces pour détecter des réseaux de fraude.

---

# PARTIE 1 — Pourquoi un graphe ?

Imaginez les données suivantes :

| Compte | Téléphone | Email                             | Appareil |
| ------ | --------- | --------------------------------- | -------- |
| A      | 111       | [a@gmail.com](mailto:a@gmail.com) | Device1  |
| B      | 111       | [b@gmail.com](mailto:b@gmail.com) | Device2  |
| C      | 222       | [c@gmail.com](mailto:c@gmail.com) | Device1  |

Sous forme de tableau, il est difficile de voir les liens.

Sous forme de graphe :

```text
         Phone 111
          /      \
         /        \
   Account A    Account B
        |
     Device1
        |
   Account C
        |
     Phone 222
```

On voit immédiatement que :

* A et B partagent un téléphone ;
* A et C partagent un appareil.

Ces relations sont souvent invisibles avec une simple table SQL.

---

# PARTIE 2 — Les nœuds (Nodes)

Dans un graphe, chaque entité est un **nœud**.

Exemples :

* Customer
* Device
* Phone
* Email
* IP Address
* Credit Card
* Bank Account

---

# PARTIE 3 — Les arêtes (Edges)

Les arêtes représentent les relations.

Par exemple :

```text
Customer

↓

uses

↓

Device
```

ou

```text
Customer

↓

owns

↓

Phone
```

Chaque relation peut également avoir des propriétés :

* date ;
* fréquence ;
* confiance ;
* type de relation.

---

# PARTIE 4 — Pourquoi les graphes détectent-ils la fraude ?

Prenons un exemple.

Supposons :

```text
100 comptes

↓

1 téléphone
```

Ce comportement est très inhabituel.

Autre exemple :

```text
50 comptes

↓

1 appareil
```

Très suspect.

Le modèle peut apprendre que ces configurations sont souvent associées à de la fraude.

---

# PARTIE 5 — Connected Components

Une **composante connexe** est un groupe de nœuds reliés entre eux.

Exemple :

```text
Account A
     |
Phone X
     |
Account B
     |
Device Y
     |
Account C
```

Tous ces nœuds appartiennent à la même composante.

---

## Feature intéressante

```text
graph_cluster_size
```

Si un compte appartient à un très grand cluster, cela peut être un signal de risque.

---

# PARTIE 6 — Degree (Degré)

Le degré correspond au nombre de connexions d'un nœud.

Exemple :

```text
Device 1

↓

Account A

↓

Account B

↓

Account C
```

Le degré du **Device 1** est de **3**.

Feature :

```text
device_degree
```

Plus le degré est élevé, plus le comportement mérite d'être analysé.

---

# PARTIE 7 — Centralité

Certaines entités sont plus "centrales" que d'autres.

Mesures courantes :

* Degree Centrality
* Betweenness Centrality
* Closeness Centrality
* PageRank

En pratique, ces métriques peuvent aider à identifier :

* un appareil utilisé par de nombreux comptes ;
* un email partagé ;
* un téléphone pivot dans un réseau de fraude.

---

# PARTIE 8 — Graph Features

Exemples de variables très utiles :

```text
accounts_per_phone

accounts_per_device

accounts_per_email

accounts_per_ip

device_degree

phone_degree

cluster_size

shared_devices

shared_emails
```

Ces features sont souvent très discriminantes.

---

# PARTIE 9 — Identity Resolution

Question très fréquente.

> **What is Identity Resolution?**

Réponse :

L'objectif est de déterminer si plusieurs enregistrements correspondent à une même personne, malgré des informations incomplètes ou contradictoires.

Par exemple :

| Nom        | Téléphone | Email                                               |
| ---------- | --------- | --------------------------------------------------- |
| John Smith | 111       | [john@gmail.com](mailto:john@gmail.com)             |
| J. Smith   | 111       | [john.smith@gmail.com](mailto:john.smith@gmail.com) |

Ces deux enregistrements peuvent représenter la même personne.

---

# PARTIE 10 — Matching

Deux approches principales :

### Déterministe

Basée sur des règles :

* même téléphone ;
* même numéro d'identité ;
* même email.

Simple mais limitée.

---

### Probabiliste

On attribue un score de similarité en combinant plusieurs indices :

* nom ;
* adresse ;
* téléphone ;
* email ;
* date de naissance.

Cette approche est plus robuste face aux données imparfaites.

---

# PARTIE 11 — Similarité

Le recruteur peut demander :

> **How would you compare two names?**

Quelques techniques :

* distance de Levenshtein ;
* Jaro-Winkler ;
* Soundex ;
* embeddings de texte.

Le choix dépend de la langue et du contexte.

---

# PARTIE 12 — Graph Database

Question :

> **Why use Neo4j instead of PostgreSQL?**

Réponse :

Les bases orientées graphe sont optimisées pour les parcours de relations complexes.

Par exemple :

"Trouver tous les comptes reliés à moins de trois relations d'un appareil suspect."

Cette requête est naturelle dans Neo4j, mais peut devenir coûteuse avec de nombreuses jointures SQL.

---

# PARTIE 13 — Graph Neural Networks (GNN)

Sujet avancé.

Les GNN apprennent directement sur la structure du graphe.

Exemples :

* GraphSAGE ;
* Graph Attention Networks (GAT) ;
* GCN (Graph Convolutional Networks).

Ils permettent de combiner :

* les caractéristiques d'un nœud ;
* les informations de ses voisins.

---

# PARTIE 14 — Exemple de pipeline

```text
Raw Data
     │
     ▼
Identity Resolution
     │
     ▼
Graph Construction
     │
     ▼
Graph Features
     │
     ▼
Machine Learning Model
     │
     ▼
Fraud Score
```

---

# PARTIE 15 — Cas pratique

Le recruteur dit :

> **Three different accounts use the same phone number and two devices. What would you investigate?**

Une bonne réponse :

1. Vérifier si ce comportement est attendu (par exemple, une famille).
2. Examiner l'historique des comptes.
3. Vérifier les IP utilisées.
4. Étudier les dates de création des comptes.
5. Analyser les montants et la fréquence des transactions.
6. Calculer des features de graphe (degré, taille du cluster, nombre de comptes liés).

---

# PARTIE 16 — Questions fréquentes

### Q1

**Why are graphs useful for fraud detection?**

Réponse :

Parce qu'ils révèlent des relations cachées entre les entités, difficiles à détecter avec des données tabulaires.

---

### Q2

**Can two legitimate users share the same device?**

Oui.

Exemple :

* une famille ;
* un ordinateur partagé ;
* un cybercafé.

C'est pourquoi une seule feature ne suffit jamais pour conclure à une fraude.

---

### Q3

**Would you use graph features alone?**

Non.

Je les combinerais avec :

* des features comportementales ;
* des features temporelles ;
* des variables financières.

---

### Q4

**How do you update the graph?**

Selon les contraintes du système :

* en temps réel pour les nouvelles transactions critiques ;
* ou par lots (batch) à intervalles réguliers.

---

### Q5

**How do graphs improve machine learning?**

Ils apportent un contexte relationnel qui n'existe pas dans les données tabulaires, ce qui améliore souvent la détection de réseaux organisés de fraude.

---

# Cas d'entretien SentiLink

Le recruteur vous demande :

> **Design an identity graph for fraud detection.**

Une réponse structurée pourrait être :

### Nœuds

* Customer
* Device
* Email
* Phone
* IP Address
* Bank Account

### Relations

* uses
* owns
* logs_in_from
* transfers_to
* shares_with

### Features

* accounts_per_phone
* accounts_per_device
* cluster_size
* device_degree
* email_degree
* ip_degree
* number_of_shared_attributes

### Modèle

Ces features alimentent ensuite un modèle supervisé (par exemple XGBoost) ou un système hybride combinant règles métier, graphes et Machine Learning.

---

# Ce qui impressionne un recruteur

Un excellent candidat ajoute :

> « Les graphes ne remplacent pas les modèles tabulaires. Ils enrichissent les données en capturant les relations entre entités. Je privilégierais donc une approche hybride : des features de graphe calculées en amont, puis intégrées au modèle de fraude avec les autres variables comportementales et temporelles. »

Cette réponse montre une vision pragmatique et orientée production.

---

## Exercice pratique

Imaginons les données suivantes :

| Account | Phone | Device | Email                           |
| ------- | ----- | ------ | ------------------------------- |
| A       | 111   | D1     | [a@mail.com](mailto:a@mail.com) |
| B       | 111   | D2     | [b@mail.com](mailto:b@mail.com) |
| C       | 222   | D1     | [c@mail.com](mailto:c@mail.com) |
| D       | 333   | D3     | [d@mail.com](mailto:d@mail.com) |

À partir de ce tableau, quelles features de graphe construiriez-vous ?

Une bonne réponse pourrait inclure :

* `accounts_per_phone`
* `accounts_per_device`
* `phone_degree`
* `device_degree`
* `cluster_size`
* `shared_phone`
* `shared_device`
* `distance_to_known_fraud` (si des comptes frauduleux connus existent)
* `number_of_connected_accounts`

---

# Ce qu'il faut retenir

Les systèmes modernes de lutte contre la fraude reposent rarement sur un seul modèle. Ils combinent généralement :

* des règles métier ;
* des modèles supervisés ;
* des techniques de détection d'anomalies ;
* des graphes d'identité ;
* des analyses réalisées par des experts fraude.

Comprendre comment ces composants interagissent est précisément ce qui distingue un candidat capable de concevoir des solutions de production.

---

## La prochaine étape

Le **Module 35** sera consacré au **MLOps**, avec un focus sur :

* le versionnement des modèles ;
* le Feature Store ;
* le déploiement continu (CI/CD) ;
* le monitoring (latence, drift, qualité des données) ;
* les stratégies de réentraînement ;
* les architectures AWS typiques utilisées en production.

C'est souvent la dernière grande étape technique avant les entretiens de niveau Senior ou Staff.
