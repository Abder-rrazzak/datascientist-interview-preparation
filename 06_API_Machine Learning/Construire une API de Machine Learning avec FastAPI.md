Nous allons maintenant construire **une véritable API de Machine Learning**, comme cela se fait dans une entreprise comme **SentiLink**.

> **C'est probablement le chapitre qui impressionne le plus les recruteurs**, car il montre que vous savez aller au-delà de Jupyter Notebook et mettre un modèle à disposition d'autres applications.

---

# MODULE 5 – Chapitre 2 : Construire une API de Machine Learning avec FastAPI

## Objectifs

À la fin de ce chapitre, vous saurez :

* créer une API REST ;
* charger un modèle entraîné ;
* recevoir des données JSON ;
* renvoyer une prédiction ;
* gérer les erreurs ;
* préparer le déploiement.

---

# Pourquoi une API ?

Votre modèle est entraîné.

Mais comment une banque peut-elle l'utiliser ?

Elle ne va pas ouvrir votre notebook.

Elle va envoyer une requête HTTP.

Exemple :

```text
POST /predict
```

avec les données de la transaction.

L'API répond immédiatement avec un score de fraude.

---

# Architecture

```text
Banque

↓

POST /predict

↓

FastAPI

↓

Model.pkl

↓

Score

↓

JSON
```

---

# Exemple de requête

La banque envoie :

```json
{
    "amount": 12000,
    "country": "RU",
    "account_age": 2,
    "new_device": true
}
```

---

L'API répond :

```json
{
    "fraud_probability": 0.97,
    "decision": "reject"
}
```

---

# Pourquoi FastAPI ?

Il existe plusieurs frameworks :

* Flask
* Django
* FastAPI

Aujourd'hui, **FastAPI est souvent privilégié** pour les APIs de Machine Learning car il est :

* très rapide ;
* simple à utiliser ;
* asynchrone (`async`) ;
* doté d'une documentation automatique (Swagger/OpenAPI) ;
* basé sur les annotations de types Python.

---

# Structure d'un projet

```text
fraud_api/
│
├── app.py
├── model.pkl
├── schemas.py
├── requirements.txt
└── Dockerfile
```

---

# Charger le modèle

Le modèle est entraîné une seule fois.

On le sauvegarde avec `joblib`.

```python
import joblib

model = joblib.load("model.pkl")
```

⚠️ On charge le modèle **au démarrage de l'application**, pas à chaque requête, pour éviter une forte latence.

---

# Définir le schéma des données

Avec **Pydantic**, FastAPI valide automatiquement les entrées.

```python
from pydantic import BaseModel

class Transaction(BaseModel):
    amount: float
    country: str
    account_age: int
    new_device: bool
```

---

# Créer l'API

```python
from fastapi import FastAPI

app = FastAPI()
```

---

# Endpoint `/predict`

```python
@app.post("/predict")
def predict(transaction: Transaction):

    features = [
        transaction.amount,
        transaction.account_age,
        int(transaction.new_device)
    ]

    score = model.predict_proba([features])[0][1]

    return {
        "fraud_probability": score
    }
```

---

# Résultat

Requête

```json
{
    "amount":15000,
    "country":"RU",
    "account_age":1,
    "new_device":true
}
```

Réponse

```json
{
    "fraud_probability":0.984
}
```

---

# Ajouter une décision

Souvent, le métier préfère recevoir une décision directement.

```python
if score > 0.8:
    decision = "reject"
elif score > 0.3:
    decision = "review"
else:
    decision = "approve"
```

Réponse :

```json
{
    "fraud_probability":0.92,
    "decision":"reject"
}
```

---

# Validation automatique

Si la banque envoie :

```json
{
    "amount":"abc"
}
```

FastAPI renvoie automatiquement une erreur **422 Unprocessable Entity**.

Vous n'avez pas besoin d'écrire cette validation vous-même.

---

# Documentation automatique

FastAPI génère automatiquement une interface Swagger.

Une fois l'application lancée :

```text
http://localhost:8000/docs
```

Vous pouvez :

* tester les endpoints ;
* voir les schémas ;
* envoyer des requêtes directement depuis le navigateur.

C'est un énorme avantage en développement.

---

# Tester avec `curl`

```bash
curl -X POST http://localhost:8000/predict \
-H "Content-Type: application/json" \
-d '{
      "amount":12000,
      "country":"RU",
      "account_age":2,
      "new_device":true
}'
```

---

# Tester avec Postman

Le recruteur peut vous demander :

> "Comment testez-vous votre API ?"

Réponse :

* Swagger (`/docs`) ;
* Postman ;
* `curl` ;
* tests automatisés (pytest + TestClient).

---

# Gérer les erreurs

Exemple :

```python
from fastapi import HTTPException

if transaction.amount < 0:
    raise HTTPException(
        status_code=400,
        detail="Amount must be positive"
    )
```

Les erreurs sont renvoyées au format JSON, ce qui facilite leur traitement côté client.

---

# Logging

Chaque appel peut être journalisé.

Exemple :

```text
Timestamp

Customer ID

Score

Decision

Model Version
```

Ces informations sont précieuses pour :

* l'audit ;
* le débogage ;
* l'explication des décisions.

---

# Versionner les modèles

Ne remplacez jamais directement un modèle.

Exemple :

```text
model_v1.pkl

model_v2.pkl

model_v3.pkl
```

L'API peut charger une version précise et permettre un retour arrière si nécessaire.

---

# Sécuriser l'API

En production, on ajoute généralement :

* authentification (API Key, OAuth2, JWT) ;
* HTTPS ;
* limitation du nombre de requêtes (*rate limiting*) ;
* journalisation des accès ;
* contrôle des permissions.

---

# Déploiement avec Uvicorn

En local :

```bash
uvicorn app:app --reload
```

En production :

```bash
uvicorn app:app --host 0.0.0.0 --port 8000
```

Souvent derrière un reverse proxy (Nginx) ou un load balancer.

---

# Questions d'entretien

### Pourquoi FastAPI est-il souvent préféré à Flask ?

FastAPI offre de meilleures performances, une validation automatique des données avec Pydantic, une documentation OpenAPI intégrée et un excellent support des fonctionnalités asynchrones.

---

### Pourquoi charger le modèle une seule fois ?

Parce que le chargement est coûteux. Le faire à chaque requête augmenterait fortement la latence.

---

### Pourquoi utiliser Pydantic ?

Pour valider automatiquement les données d'entrée, détecter les erreurs de type et générer la documentation de l'API.

---

### Pourquoi versionner les modèles ?

Pour pouvoir comparer plusieurs versions, revenir rapidement à une version stable et assurer la traçabilité des décisions.

---

### Pourquoi journaliser les requêtes ?

Pour faciliter le débogage, les audits, le monitoring et l'analyse des performances du modèle.

---

# Cas pratique SentiLink

Le recruteur vous dit :

> « Nous recevons environ 500 requêtes par seconde. Votre API commence à ralentir. Que faites-vous ? »

### Réponse idéale

1. Mesurer où se situe le goulot d'étranglement (profilage).
2. Vérifier si la latence provient du calcul des features, de la base de données ou du modèle.
3. Mettre en cache les données fréquemment utilisées (Redis).
4. Déployer plusieurs instances de l'API derrière un Load Balancer.
5. Optimiser le modèle si nécessaire.
6. Mettre en place de l'autoscaling pour absorber les pics de charge.

---

# Questions avancées

### Pourquoi ne pas exposer directement le modèle ?

Parce qu'une API fournit :

* une validation des données ;
* une authentification ;
* une journalisation ;
* une gestion des erreurs ;
* une abstraction entre le client et le modèle.

---

### Que se passe-t-il si l'API est indisponible ?

Un système robuste prévoit des mécanismes de secours :

* plusieurs instances de l'API ;
* un load balancer ;
* des contrôles de santé (*health checks*) ;
* éventuellement un mode dégradé basé sur des règles métier.

---

# Mini architecture de production

```text
Client
   │
HTTPS
   │
API Gateway
   │
Load Balancer
   │
FastAPI (xN instances)
   │
Redis (cache)
   │
Feature Store / PostgreSQL
   │
Modèle XGBoost
   │
CloudWatch (logs et métriques)
```

---

## Ce que les recruteurs aiment entendre

Ne vous contentez pas de dire :

> « Je crée une API avec FastAPI. »

Expliquez aussi :

* comment vous validez les données ;
* comment vous gérez les erreurs ;
* comment vous réduisez la latence ;
* comment vous sécurisez l'API ;
* comment vous versionnez les modèles ;
* comment vous surveillez l'application en production.

---

# Prochain chapitre

Nous passerons à **Docker**, une compétence très souvent demandée.

Vous apprendrez :

* pourquoi Docker est devenu indispensable ;
* comment conteneuriser une API FastAPI ;
* ce qu'est une image et un conteneur ;
* les commandes essentielles (`build`, `run`, `exec`, `logs`) ;
* comment Docker facilite le déploiement sur AWS (ECS, EKS, Kubernetes).

Même si le poste est celui de **Data Scientist**, une bonne compréhension de Docker est aujourd'hui un véritable avantage lors des entretiens techniques.
