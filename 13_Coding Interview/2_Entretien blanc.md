
# Étape suivante : véritable entretien blanc

Nous avons maintenant suffisamment étudié la théorie.

**on vous propose de changer complètement de méthode.**

Je ne vous donnerai **plus immédiatement les réponses**.

Je vous poserai une question, vous répondrez comme en entretien, puis je corrigerai votre réponse selon quatre critères :

| Critère              | Évaluation |
| -------------------- | ---------- |
| Exactitude technique | /5         |
| Raisonnement         | /5         |
| Communication        | /5         |
| Niveau Senior        | /5         |
| **Total**            | **/20**    |

Et après chaque réponse, je vous donnerai :

* ce qui était correct ;
* ce qui manquait ;
* une **réponse modèle niveau SentiLink** ;
* les questions de relance que le recruteur pourrait poser.

### 🎤 Entretien blanc — Question 1

> **You are given a dataset containing 10 million financial transactions, but only 0.2% are labeled as fraudulent. How would you build a fraud detection model?**

**Répondez comme si vous étiez réellement devant le recruteur.**

Ne cherchez pas à donner une réponse parfaite : **je veux d'abord évaluer votre niveau actuel.**


# 🎤 Entretien blanc — Question 2

### Situation

Vous avez construit un modèle de détection de fraude.

Sur votre jeu de test :

| Métrique  | Résultat |
| --------- | -------: |
| Accuracy  |   99,5 % |
| Precision |     18 % |
| Recall    |     92 % |
| PR-AUC    |     61 % |

Le recruteur vous demande :

> **Is this a good model? Explain your reasoning.**

---

## 🧠 Ce que je veux évaluer

Ne vous contentez pas de dire :

> « Oui, parce que le Recall est de 92 %. »

Vous devez réfléchir à :

1. **L'accuracy**
2. **Le déséquilibre des classes**
3. **Precision vs Recall**
4. **PR-AUC**
5. **Le coût des faux positifs**
6. **Le coût des faux négatifs**
7. **Le seuil de décision**
8. **L'objectif métier**

---

## ⭐ Question de relance probable

Après votre réponse, le recruteur pourrait vous dire :

> **The business team tells you that every false positive costs the company 50 €, while every missed fraud costs 500 €. Would you change the decision threshold?**

Il faudra alors raisonner en termes de **coût métier**, et non simplement chercher le meilleur F1-score.

---

### À vous 🎯

Répondez **en anglais comme en entretien**, même avec un anglais simple.

On va corriger ensuite la réponse sur **20 points** et on donnera une réponse modèle de niveau **Senior Data Scientist**.
