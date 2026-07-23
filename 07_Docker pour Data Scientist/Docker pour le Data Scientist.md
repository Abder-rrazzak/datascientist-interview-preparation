Nous arrivons à un sujet **très fréquemment abordé en entretien**.

> Il est peu probable que l'on vous demande d'écrire un Dockerfile de mémoire, mais il est très probable qu'on vous demande :

> **"Comment déployeriez-vous votre API de Machine Learning ?"**

La réponse passe presque toujours par **Docker**.

---

# MODULE 5 – Chapitre 3 : Docker pour le Data Scientist

# Pourquoi Docker ?

Avant Docker, un problème revenait constamment :

Le développeur disait :

> **"Chez moi ça fonctionne."**

Mais en production...

> **"Ça ne fonctionne plus."**

Pourquoi ?

Parce que les environnements étaient différents.

Par exemple :

Votre ordinateur :

```
Python 3.12

scikit-learn 1.7

NumPy 2.0
```

Serveur :

```
Python 3.10

scikit-learn 1.4

NumPy 1.24
```

Le modèle peut alors ne plus fonctionner.

Docker résout ce problème.

---

# Qu'est-ce que Docker ?

Docker permet d'emballer :

* votre code ;
* Python ;
* les librairies ;
* votre modèle ML ;
* la configuration.

dans un **conteneur**.

Le conteneur fonctionnera exactement de la même manière partout.

---

# Une analogie

Imaginez un conteneur maritime.

Peu importe le camion ou le bateau.

Le contenu reste identique.

Docker fonctionne selon le même principe.

---

# Architecture

```
Votre ordinateur

↓

Docker Image

↓

Docker Container

↓

Même résultat partout
```

---

# Image vs Conteneur

Question très fréquente.

## Image

Une image est un modèle.

Elle est immuable.

Exemple :

```
fraud-api:1.0
```

---

## Conteneur

Le conteneur est une instance en cours d'exécution.

```
Image

↓

Container
```

On peut lancer plusieurs conteneurs à partir de la même image.

---

# Analogie

Image

↓

Recette de cuisine

Conteneur

↓

Le gâteau.

---

# Dockerfile

Le Dockerfile explique à Docker comment construire l'image.

Exemple

```dockerfile
FROM python:3.11

WORKDIR /app

COPY . .

RUN pip install -r requirements.txt

CMD ["uvicorn","app:app","--host","0.0.0.0","--port","8000"]
```

---

# Explication ligne par ligne

## FROM

```dockerfile
FROM python:3.11
```

Image de base.

---

## WORKDIR

```
WORKDIR /app
```

Répertoire de travail.

---

## COPY

```
COPY . .
```

Copie le projet.

---

## RUN

```
RUN pip install -r requirements.txt
```

Installation des dépendances.

---

## CMD

```
CMD [...]
```

Commande exécutée au démarrage.

---

# Construire l'image

```
docker build -t fraud-api .
```

Docker lit le Dockerfile.

↓

Construit une image.

---

# Lancer le conteneur

```
docker run -p 8000:8000 fraud-api
```

Votre API devient accessible sur :

```
localhost:8000
```

---

# Vérifier les conteneurs

```
docker ps
```

Affiche les conteneurs actifs.

---

# Arrêter un conteneur

```
docker stop container_id
```

---

# Supprimer un conteneur

```
docker rm container_id
```

---

# Voir les logs

```
docker logs container_id
```

Très utilisé en production.

---

# Entrer dans le conteneur

```
docker exec -it container_id bash
```

Permet de diagnostiquer un problème.

---

# Volumes Docker

Problème :

Si le conteneur est supprimé,

les fichiers disparaissent.

Les volumes permettent de conserver les données.

---

# Variables d'environnement

Ne jamais écrire ceci :

```python
password="mypassword"
```

À la place :

```
DATABASE_URL
```

Puis :

```python
import os

url = os.getenv("DATABASE_URL")
```

---

# Pourquoi ?

Parce que :

* plus sécurisé ;
* plus flexible ;
* compatible avec AWS.

---

# Docker Compose

Imaginez :

Vous avez :

* FastAPI
* PostgreSQL
* Redis

Trois services.

Docker Compose les démarre ensemble.

Architecture

```
FastAPI

↓

Redis

↓

PostgreSQL
```

Une seule commande :

```
docker compose up
```

---

# Docker dans AWS

Le pipeline est généralement :

```
Code

↓

Docker Image

↓

Amazon ECR

↓

Amazon ECS

↓

API
```

---

# Pourquoi Docker est indispensable ?

Parce que :

* tous les développeurs utilisent le même environnement ;
* les déploiements sont reproductibles ;
* le passage en production est simplifié ;
* le rollback est facile.

---

# Cas pratique

Vous développez votre modèle.

Vous l'envoyez à un collègue.

Sans Docker :

Le modèle ne fonctionne pas.

Avec Docker :

Même environnement.

Même résultat.

---

# Questions d'entretien

## Qu'est-ce qu'une image Docker ?

### Réponse

Une image est un modèle contenant le code, les dépendances et la configuration nécessaires pour créer un conteneur.

---

## Qu'est-ce qu'un conteneur ?

Une instance en cours d'exécution d'une image Docker.

---

## Pourquoi utiliser Docker ?

Pour garantir que l'application fonctionne de manière identique sur tous les environnements, du développement à la production.

---

## Pourquoi Docker est-il populaire en Machine Learning ?

Parce qu'il permet d'encapsuler le modèle, les dépendances Python et l'API dans un environnement reproductible.

---

## Pourquoi utiliser Docker avec AWS ?

Parce que des services comme ECS, EKS ou AWS Batch exécutent directement des conteneurs Docker.

---

# Cas d'entretien SentiLink

Le recruteur dit :

> « Votre API fonctionne sur votre ordinateur mais pas sur notre serveur. Que faites-vous ? »

### Réponse idéale

Je m'assure que l'application est conteneurisée avec Docker afin que les mêmes versions de Python, des bibliothèques et de la configuration soient utilisées partout. Ensuite, j'analyse les logs, je vérifie les variables d'environnement, les dépendances, les accès aux services externes et la configuration réseau pour identifier la cause du problème.

---

# Questions avancées

### Pourquoi ne pas installer directement Python sur EC2 ?

Parce que Docker :

* isole les applications ;
* simplifie les mises à jour ;
* facilite les retours en arrière ;
* évite les conflits de dépendances.

---

### Pourquoi utiliser Docker plutôt qu'une machine virtuelle ?

| Docker                         | Machine virtuelle                       |
| ------------------------------ | --------------------------------------- |
| Léger                          | Plus lourd                              |
| Démarrage en quelques secondes | Démarrage plus lent                     |
| Partage le noyau de l'OS hôte  | Chaque VM possède son propre OS         |
| Consomme moins de ressources   | Consomme davantage de CPU et de mémoire |

---

### Peut-on exécuter plusieurs conteneurs sur une même machine ?

Oui. C'est même l'un des principaux avantages de Docker. Chaque conteneur reste isolé des autres tout en partageant le noyau du système d'exploitation.

---

# Ce que les recruteurs apprécient

Un bon candidat ne dit pas seulement :

> « J'utilise Docker. »

Il explique aussi :

* pourquoi il est utilisé ;
* comment il garantit la reproductibilité ;
* comment il s'intègre dans un pipeline de déploiement ;
* comment il facilite les mises à jour et les retours en arrière.

---

# Vue d'ensemble d'un pipeline moderne

```text
GitHub
   │
   ▼
CI/CD (GitHub Actions)
   │
   ▼
Construction de l'image Docker
   │
   ▼
Amazon ECR
   │
   ▼
Amazon ECS
   │
   ▼
FastAPI
   │
   ▼
Modèle XGBoost
   │
   ▼
Prédiction
```

Cette architecture est très proche de ce que l'on retrouve dans de nombreuses entreprises SaaS et FinTech.

---

# Prochain chapitre : CI/CD pour le Machine Learning (MLOps)

Nous verrons :

* ce qu'est une pipeline **CI/CD** ;
* comment automatiser les tests et le déploiement ;
* le rôle de **GitHub Actions** ;
* comment déployer automatiquement une nouvelle version d'un modèle ;
* les bonnes pratiques de versionnement des modèles et des données.

C'est un sujet de plus en plus abordé en entretien, car il montre que vous comprenez le **cycle de vie complet** d'une application de Machine Learning, de l'écriture du code jusqu'à sa mise en production.
