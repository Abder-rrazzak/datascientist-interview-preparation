Nous arrivons au **module le plus important de toute la préparation**.

> **Si vous réussissez ce module, vous serez capable de discuter avec un Data Scientist sur des problématiques réelles de fraude.**

C'est exactement ce qui fait la différence entre un candidat qui connaît le Machine Learning et un candidat qui comprend **le métier**.

---

# MODULE 4 — Détection de Fraude (Niveau Expert)

## Objectifs

À la fin de ce module, vous serez capable de :

* comprendre les différents types de fraude ;
* construire des variables (features) pertinentes ;
* gérer les jeux de données très déséquilibrés ;
* choisir les bonnes métriques ;
* concevoir un pipeline de détection en temps réel ;
* répondre aux questions métier lors d'un entretien.

---

# Plan du module

1. Comprendre la fraude
2. Types de fraude
3. Le cycle de vie d'une fraude
4. Feature Engineering
5. Déséquilibre des classes
6. Détection d'anomalies
7. Détection en temps réel
8. Data Drift & Concept Drift
9. Monitoring des modèles
10. Étude de cas SentiLink

---

# Chapitre 1 — Comprendre la fraude

Le fraudeur cherche à contourner le système de contrôle.

Le Data Scientist cherche à construire un modèle capable de reconnaître ces comportements.

Une transaction contient souvent :

| Variable             | Exemple                                 |
| -------------------- | --------------------------------------- |
| Montant              | 1200 €                                  |
| Pays                 | US                                      |
| Adresse IP           | 104.xxx.xxx.xxx                         |
| Device ID            | ABC123                                  |
| Email                | [john@gmail.com](mailto:john@gmail.com) |
| Heure                | 02:30                                   |
| Navigateur           | Chrome                                  |
| Ancienneté du compte | 3 jours                                 |

À partir de ces informations, le modèle doit estimer :

[
P(\text{Fraude})
]

---

# Question d'entretien

Quel est le rôle d'un Data Scientist dans la fraude ?

### Réponse

Développer des modèles capables de détecter les transactions suspectes tout en limitant les faux positifs, afin de protéger l'entreprise sans dégrader l'expérience des clients.

---

# Chapitre 2 — Les différents types de fraude

Chez SentiLink, plusieurs formes de fraude peuvent être rencontrées.

---

## 1. Application Fraud

Très fréquente.

Le fraudeur crée une demande avec de fausses informations.

Exemple :

* faux revenu ;
* faux numéro de téléphone ;
* faux justificatifs.

Objectif :

Obtenir un prêt ou une carte bancaire.

---

## 2. Identity Theft

Le fraudeur utilise l'identité d'une autre personne.

Exemple :

* nom réel ;
* numéro de sécurité sociale volé ;
* date de naissance réelle.

Le système doit détecter cette usurpation.

---

## 3. Synthetic Identity Fraud

L'une des fraudes les plus difficiles.

Le fraudeur mélange :

* de vraies informations ;
* de fausses informations.

Exemple :

* vrai numéro de sécurité sociale ;
* faux nom ;
* fausse adresse.

Cette identité n'appartient à personne, mais paraît crédible.

---

## 4. Account Takeover (ATO)

Le compte existe déjà.

Le fraudeur en prend le contrôle.

Exemple :

* mot de passe compromis ;
* phishing ;
* vol de session.

---

## 5. Payment Fraud

Exemple :

* carte bancaire volée ;
* paiement frauduleux ;
* chargeback.

---

## Question d'entretien

Quelle différence entre Identity Theft et Synthetic Identity ?

### Réponse

* **Identity Theft** : utilisation de l'identité réelle d'une autre personne.
* **Synthetic Identity** : création d'une identité fictive à partir d'informations réelles et inventées.

---

# Chapitre 3 — Le cycle de vie d'une fraude

Le fraudeur :

```text
Création du compte
        ↓
Ajout d'informations
        ↓
Premières transactions
        ↓
Augmentation progressive des montants
        ↓
Fraude
        ↓
Disparition
```

Le rôle du modèle est d'intervenir **avant** la fraude.

---

# Chapitre 4 — Feature Engineering

C'est la partie la plus importante.

Un bon modèle dépend souvent davantage de la qualité des variables que du choix de l'algorithme.

---

## Les meilleures variables sont rarement présentes dans les données brutes.

Exemple

Mauvaise variable :

```text
Montant
```

Bonne variable :

```text
Montant moyen des 30 derniers jours
```

Encore mieux :

```text
Montant actuel / montant moyen historique
```

---

# Exemple

Client

Historique

100 €

120 €

130 €

Transaction actuelle

3000 €

Le ratio :

```text
3000 / 116 ≈ 25,9
```

Cette variable est bien plus informative que le montant seul.

---

# Variables temporelles

Très utilisées.

Exemples

Nombre de transactions :

* dernière heure ;
* dernières 24 heures ;
* 7 derniers jours.

---

Temps depuis :

* dernière connexion ;
* dernière transaction ;
* changement de mot de passe.

---

# Variables géographiques

Exemple

Hier :

```text
Paris
```

Aujourd'hui :

```text
Tokyo
```

En deux heures.

Très suspect.

Variables possibles :

* distance entre deux transactions ;
* changement de pays ;
* changement d'adresse IP ;
* vitesse de déplacement estimée (*impossible travel*).

---

# Variables liées au Device

Exemple

Même utilisateur

↓

10 appareils différents

↓

Risque élevé.

Variables utiles :

* nombre d'appareils utilisés ;
* nouvel appareil ;
* navigateur inconnu ;
* système d'exploitation inhabituel.

---

# Variables comportementales

Très utilisées.

Exemple

Temps de remplissage d'un formulaire.

Humain :

```text
45 secondes
```

Bot :

```text
2 secondes
```

---

Autres variables

* vitesse de frappe ;
* mouvements de souris ;
* copier-coller massif ;
* ordre des clics.

---

# Variables réseau

Très importantes.

Exemple

Adresse IP

↓

Proxy ?

VPN ?

Tor ?

Datacenter ?

Pays à risque ?

---

# Variables relationnelles (Graph Features)

Très avancées.

Imaginez

```text
Email A
     │
     ├──── Device X
     │
Email B
     │
     ├──── Device X
     │
Email C
```

Le même appareil est partagé par plusieurs comptes.

Très suspect.

On construit alors des variables comme :

* nombre de comptes par appareil ;
* nombre d'adresses e-mail par téléphone ;
* nombre de cartes bancaires par IP.

Ces **features de graphe** sont très utilisées en fraude moderne.

---

# Questions d'entretien

### Pourquoi le Feature Engineering est-il si important ?

Parce qu'il permet de transformer des données brutes en variables beaucoup plus informatives pour le modèle.

---

### Donnez des exemples de variables pertinentes en fraude.

Exemples :

* montant moyen des 30 derniers jours ;
* nombre de transactions en 1 heure ;
* nouvel appareil ;
* âge du compte ;
* distance géographique entre deux transactions ;
* nombre de comptes utilisant le même appareil ;
* fréquence des changements d'adresse IP.

---

# Cas pratique SentiLink

Vous recevez :

| customer_id | amount | country | device | timestamp |
| ----------- | -----: | ------- | ------ | --------- |
| 101         |    100 | US      | A1     | 08:00     |
| 101         |    120 | US      | A1     | 09:00     |
| 101         |   9000 | RU      | B7     | 09:05     |

Le recruteur demande :

> Quelles nouvelles variables créeriez-vous ?

### Réponse attendue

Variables possibles :

* montant moyen historique ;
* ratio entre le montant actuel et le montant moyen ;
* nombre de transactions sur la dernière heure ;
* changement de pays ;
* nouvel appareil (`device`) ;
* temps écoulé depuis la transaction précédente ;
* distance géographique estimée entre les deux pays ;
* fréquence d'utilisation du nouvel appareil par d'autres comptes.

---

# Exercice de réflexion (niveau SentiLink)

Vous disposez des colonnes suivantes :

* `customer_id`
* `device_id`
* `ip_address`
* `email`
* `phone`
* `transaction_amount`
* `merchant_id`
* `timestamp`
* `country`
* `is_fraud`

**Question :**

Citez au moins **10 features dérivées** que vous construiriez pour améliorer un modèle de détection de fraude.

### Exemple de bonnes réponses

1. Nombre de transactions du client sur les 24 dernières heures.
2. Montant moyen des transactions des 30 derniers jours.
3. Ratio `montant actuel / montant moyen`.
4. Âge du compte au moment de la transaction.
5. Nombre d'appareils utilisés par le client.
6. Nombre de comptes utilisant le même `device_id`.
7. Nombre de comptes partageant la même adresse IP.
8. Temps écoulé depuis la transaction précédente.
9. Distance géographique entre les deux dernières transactions.
10. Nombre de pays différents utilisés au cours des 7 derniers jours.
11. Nombre de commerçants (`merchant_id`) différents visités récemment.
12. Indicateur de première utilisation d'un appareil ou d'une adresse IP.

---

# Ce que les recruteurs attendent

Ils ne cherchent pas uniquement quelqu'un qui connaît **XGBoost**.

Ils veulent surtout voir si vous savez **imaginer des variables intelligentes**, car en détection de fraude, le **feature engineering** apporte souvent davantage de gains que le simple changement d'algorithme.

---

# Dans la prochaine partie

Nous aborderons un autre sujet fondamental :

## Les jeux de données déséquilibrés (*Imbalanced Learning*)

Nous verrons :

* pourquoi 99,9 % d'accuracy peut être un mauvais résultat ;
* `class_weight` ;
* **SMOTE**, **ADASYN**, **undersampling**, **oversampling** ;
* **Balanced Random Forest** ;
* **EasyEnsemble** ;
* **Focal Loss** ;
* les métriques adaptées aux classes rares ;
* les bonnes pratiques spécifiques aux modèles de détection de fraude en production.

C'est l'un des thèmes les plus fréquemment abordés lors des entretiens de Data Scientist spécialisés en fraude.
