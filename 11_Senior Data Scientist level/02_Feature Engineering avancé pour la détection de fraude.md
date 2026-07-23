Nous allons maintenant aborder **le sujet le plus important en détection de fraude**.

> **Si je devais choisir un seul domaine qui différencie un Data Scientist moyen d'un excellent Data Scientist en FinTech, ce serait le Feature Engineering.**

Chez **SentiLink**, **Stripe**, **PayPal**, **Capital One** ou **Visa**, les modèles ne sont souvent pas le principal facteur de performance.

La différence provient principalement des **features**.

> Il existe un adage très connu :

> **"Better data beats better algorithms."**

Un excellent Feature Engineering permet souvent d'obtenir un meilleur modèle avec une simple régression logistique qu'un modèle complexe entraîné sur de mauvaises variables.

---

# MODULE 9 – Feature Engineering avancé pour la détection de fraude

## Pourquoi est-ce si important ?

Imaginons que votre dataset contienne uniquement :

| transaction_id | amount | country | device | fraud |
| -------------- | -----: | ------- | ------ | ----: |
| 1              |   2500 | FR      | A1     |     0 |
| 2              |  15000 | RU      | B5     |     1 |

Ces variables seules sont peu informatives.

Un fraudeur peut facilement changer :

* le montant ;
* le pays ;
* le navigateur.

En revanche, son **comportement** est beaucoup plus difficile à masquer.

C'est précisément ce que le Feature Engineering cherche à capturer.

---

# 1. Features temporelles

Le temps est une source d'information très riche.

À partir d'un timestamp, on peut créer :

```text
timestamp
2026-07-23 03:12:18
```

↓

| Feature            |
| ------------------ |
| Heure              |
| Jour               |
| Jour de la semaine |
| Week-end           |
| Mois               |
| Vacances           |
| Nuit (oui/non)     |

---

### Exemple

```python
df["hour"] = df["timestamp"].dt.hour
df["weekday"] = df["timestamp"].dt.dayofweek
df["is_weekend"] = df["weekday"] >= 5
df["is_night"] = df["hour"].between(0, 5)
```

---

### Pourquoi ?

Une transaction à :

**03h15 du matin**

↓

est souvent plus risquée qu'à :

**15h30**

(selon le profil du client).

---

## Question d'entretien

Pourquoi créer la variable **is_night** ?

### Réponse

Parce qu'elle capture un comportement potentiellement inhabituel et permet au modèle de distinguer plus facilement les transactions à risque.

---

# 2. Temps depuis la dernière transaction

Variable extrêmement importante.

Exemple :

| Transaction | Heure |
| ----------- | ----- |
| 1           | 10:00 |
| 2           | 10:01 |

↓

Temps écoulé

↓

1 minute

---

Pourquoi ?

Une carte bancaire utilisée :

Paris

↓

30 secondes plus tard

↓

Tokyo

↓

est impossible physiquement.

---

Feature :

```text
time_since_last_transaction
```

---

# 3. Nombre de transactions récentes

Exemple :

Nombre de transactions

durant :

* 10 minutes
* 1 heure
* 24 heures

---

Exemple

```text
Transactions dernières 10 min

=

17
```

↓

Très suspect.

---

Feature

```text
transactions_last_10_minutes
```

---

# 4. Rolling Features

Très utilisées.

Exemple

Montant moyen

des

30 dernières transactions.

```text
rolling_mean_amount
```

---

Ou

Écart-type

↓

```text
rolling_std_amount
```

---

Pourquoi ?

Parce qu'un achat de :

```text
15 000 €
```

est très inhabituel

si le client dépense normalement :

```text
40 €
```

---

# 5. Historique du client

Créer :

```text
account_age_days
```

Ancienneté du compte.

---

Question

Pourquoi ?

Les nouveaux comptes présentent souvent un risque plus élevé de fraude.

---

Autres variables utiles :

* nombre total de transactions ;
* nombre de cartes utilisées ;
* nombre d'adresses enregistrées ;
* fréquence de changement d'appareil.

---

# 6. Features géographiques

Très importantes.

Exemple

Latitude

Longitude

↓

Distance.

---

Paris

↓

Tokyo

↓

10 minutes

↓

Impossible.

---

Créer :

```text
distance_from_last_transaction
```

---

On peut aussi créer :

```text
country_changed
```

↓

Le pays est-il différent de celui de la transaction précédente ?

---

# 7. Device Fingerprinting

Très utilisé chez SentiLink.

Variables possibles :

* nouvel appareil ;
* système d'exploitation ;
* navigateur ;
* résolution d'écran ;
* fuseau horaire ;
* langue du navigateur ;
* empreinte du navigateur (*browser fingerprint*).

---

Exemple

```text
new_device = True
```

↓

Augmentation du risque.

---

# 8. Adresse IP

Créer :

```text
new_ip
```

---

Ou :

```text
ip_country_changed
```

---

Ou :

```text
number_of_ips_last_week
```

---

Pourquoi ?

Les fraudeurs changent souvent d'adresse IP ou utilisent des VPN et des proxies.

---

# 9. Email

Variables possibles :

* âge de l'adresse email ;
* domaine (`gmail.com`, `yahoo.com`, domaine jetable) ;
* longueur de l'adresse ;
* présence de chiffres ;
* similarité avec le nom du client.

---

Exemple

```text
john198746583@gmail.com
```

↓

Peut être plus suspect qu'une adresse utilisée depuis plusieurs années, selon le contexte.

---

# 10. Téléphone

Créer :

```text
new_phone
```

Ou :

```text
phone_country
```

---

# 11. Adresse postale

Variables :

* changement d'adresse ;
* distance avec l'adresse précédente ;
* boîte postale ;
* adresse incomplète.

---

# 12. Features comportementales

Ce sont souvent les plus puissantes.

Exemple

Temps passé sur la page.

↓

```text
3 secondes
```

↓

Très suspect.

---

Autres exemples

* vitesse de frappe ;
* vitesse de déplacement de la souris ;
* mouvements de la souris ;
* pression exercée sur les touches (si disponible) ;
* temps entre deux clics.

---

Ces signaux sont difficiles à imiter.

---

# 13. Agrégations

Exemple

```text
Nombre de cartes

utilisées

par cette IP
```

---

Ou

```text
Nombre de comptes

utilisant

cet appareil
```

---

Ces variables permettent de détecter des comportements anormaux.

---

# 14. Features Graphes

Très utilisées chez SentiLink.

Imaginez :

```text
Utilisateur A

↓

Adresse

↓

Utilisateur B

↓

Téléphone

↓

Utilisateur C
```

Tous ces utilisateurs partagent des informations.

On peut construire un graphe reliant :

* utilisateurs ;
* emails ;
* téléphones ;
* appareils ;
* adresses IP.

---

Exemple

```text
Graph Degree

=

15
```

↓

Très inhabituel.

---

Autres variables :

* nombre de voisins ;
* centralité ;
* composante connexe.

Ces approches sont particulièrement efficaces pour détecter les réseaux organisés de fraude.

---

# 15. Encodage des catégories

Pour des variables à forte cardinalité (par exemple `merchant_id` ou `device_id`), un One-Hot Encoding peut devenir inefficace.

Selon le modèle, on peut utiliser :

* Target Encoding (avec précautions pour éviter les fuites de données) ;
* CatBoost Encoder ;
* Embeddings (dans certains modèles profonds).

---

# 16. Sélection des features

Ajouter des variables est utile, mais il faut aussi éviter d'en conserver des inutiles.

On peut utiliser :

* SHAP ;
* Permutation Importance ;
* des méthodes de sélection récursive ;
* l'expertise métier.

---

# Cas pratique SentiLink

Le recruteur demande :

> **Vous disposez uniquement de ces colonnes :**

```text
Amount

Timestamp

Country

Device_ID

Customer_ID
```

> **Quelles nouvelles variables créeriez-vous ?**

### Réponse idéale

Je créerais notamment :

* heure et jour de la semaine ;
* indicateur de nuit ;
* temps depuis la dernière transaction ;
* nombre de transactions sur différentes fenêtres temporelles (10 min, 1 h, 24 h) ;
* montant moyen et écart-type des transactions récentes ;
* ancienneté du compte ;
* indicateur de nouvel appareil ;
* changement de pays ;
* distance géographique entre deux transactions (si les coordonnées sont disponibles) ;
* fréquence d'utilisation de l'appareil par différents comptes.

---

# Questions d'entretien

### Pourquoi le Feature Engineering est-il si important ?

Parce qu'il transforme des données brutes en variables qui rendent les comportements frauduleux beaucoup plus visibles pour le modèle.

---

### Quelle est la meilleure feature en fraude ?

Il n'existe pas de réponse universelle. Les variables comportementales, temporelles et relationnelles sont souvent parmi les plus discriminantes, mais leur efficacité dépend du contexte métier.

---

### Pourquoi les Rolling Features sont-elles utiles ?

Parce qu'elles permettent de comparer une transaction au comportement récent du client plutôt qu'à une moyenne globale.

---

### Pourquoi les graphes sont-ils efficaces ?

Parce qu'ils révèlent des réseaux de fraude en mettant en évidence les relations entre utilisateurs, appareils, adresses IP, téléphones ou emails.

---

# Ce qu'un excellent candidat répond

> « Je ne crée pas des variables uniquement à partir des colonnes disponibles. Je cherche surtout à représenter le comportement du client dans le temps, ses interactions avec d'autres entités et les anomalies par rapport à son historique. »

Cette manière de raisonner est exactement celle que recherchent les équipes spécialisées dans la détection de fraude.

---

# Prochain chapitre

Nous allons maintenant étudier un sujet très avancé et directement lié aux problématiques de SentiLink :

# **Détection d'anomalies (Anomaly Detection) et nouvelles fraudes**

Nous verrons notamment :

* pourquoi les modèles supervisés ne suffisent pas toujours ;
* Isolation Forest ;
* Local Outlier Factor (LOF) ;
* One-Class SVM ;
* Autoencoders ;
* comment détecter des **fraudes inédites** pour lesquelles aucun exemple étiqueté n'existe encore.

C'est un thème très apprécié lors des entretiens de Data Science orientés fraude et cybersécurité.
