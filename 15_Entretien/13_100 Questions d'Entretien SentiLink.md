Nous arrivons maintenant au **MODULE 36**, qui est probablement **le plus utile pour réussir l'entretien**.

J'ai compilé les questions qui reviennent le plus souvent dans les entretiens de sociétés comme **SentiLink, Stripe, Block (Square), Visa, Mastercard, PayPal, Robinhood, Capital One, Affirm, Plaid, Ramp, Brex**.

L'objectif n'est pas seulement de connaître la réponse, mais **de répondre comme un Senior Data Scientist**.

---

# MODULE 36 — 100 Questions d'Entretien SentiLink (avec réponses)

---

# SECTION 1 — Python

## Q1. Pourquoi Python est-il si populaire en Data Science ?

**Réponse**

Python offre un excellent équilibre entre simplicité, richesse de l'écosystème (Pandas, NumPy, Scikit-learn, PyTorch, TensorFlow), rapidité de développement et intégration avec les systèmes de production.

---

## Q2. Différence entre une liste et un tuple ?

**Réponse**

* Liste : mutable.
* Tuple : immuable.

Les tuples sont adaptés aux données qui ne doivent pas être modifiées et peuvent servir de clés dans un dictionnaire.

---

## Q3. Pourquoi les dictionnaires sont-ils rapides ?

**Réponse**

Parce qu'ils reposent sur une table de hachage (hash table), offrant un accès en temps moyen O(1).

---

## Q4. Que fait `yield` ?

**Réponse**

Il transforme une fonction en générateur. Les valeurs sont produites à la demande, ce qui réduit fortement l'utilisation de la mémoire.

---

## Q5. Quand utiliser un générateur ?

**Réponse**

Lorsque je traite de très grands volumes de données ou des flux continus, afin d'éviter de tout charger en mémoire.

---

## Q6. Différence entre `deepcopy()` et `copy()` ?

**Réponse**

* `copy()` copie uniquement le premier niveau.
* `deepcopy()` copie récursivement tous les objets imbriqués.

---

## Q7. Qu'est-ce que le GIL ?

**Réponse**

Le Global Interpreter Lock est un mécanisme de CPython qui limite l'exécution simultanée du bytecode Python à un seul thread par processus. Pour les tâches CPU intensives, on privilégie souvent le multiprocessing.

---

## Q8. Qu'est-ce qu'un décorateur ?

**Réponse**

Une fonction qui ajoute un comportement à une autre fonction sans modifier son implémentation.

---

## Q9. Pourquoi utiliser des annotations de type ?

**Réponse**

Elles améliorent la lisibilité, facilitent la maintenance et permettent une vérification statique avec des outils comme `mypy`.

---

## Q10. `print()` ou `logging` ?

**Réponse**

En production, j'utilise `logging` pour gérer les niveaux de journalisation, les fichiers de logs et l'intégration avec les outils de monitoring.

---

# SECTION 2 — SQL

## Q11. Différence entre INNER JOIN et LEFT JOIN ?

**Réponse**

* INNER JOIN : uniquement les correspondances.
* LEFT JOIN : toutes les lignes de la table de gauche, avec des valeurs NULL si aucune correspondance n'existe.

---

## Q12. Qu'est-ce qu'une CTE ?

**Réponse**

Une Common Table Expression permet de structurer une requête complexe en créant une table temporaire nommée, améliorant la lisibilité.

---

## Q13. À quoi servent les Window Functions ?

**Réponse**

Elles permettent de calculer des agrégats (RANK, LAG, LEAD, SUM, AVG, etc.) sans perdre le détail des lignes.

---

## Q14. Différence entre `ROW_NUMBER()`, `RANK()` et `DENSE_RANK()` ?

**Réponse**

* `ROW_NUMBER()` attribue un numéro unique.
* `RANK()` laisse des écarts en cas d'égalité.
* `DENSE_RANK()` ne laisse pas d'écarts.

---

## Q15. Comment optimiser une requête SQL lente ?

**Réponse**

J'examine le plan d'exécution, j'ajoute des index si nécessaire, je limite les scans complets, je réduis les données traitées et j'évite les opérations inutiles.

---

# SECTION 3 — Machine Learning

## Q16. Quelle est la différence entre classification et régression ?

**Réponse**

* Classification : prédire une catégorie.
* Régression : prédire une valeur continue.

---

## Q17. Pourquoi séparer train, validation et test ?

**Réponse**

* Train : apprentissage.
* Validation : réglage des hyperparamètres.
* Test : évaluation finale sur des données jamais vues.

---

## Q18. Qu'est-ce que l'overfitting ?

**Réponse**

Le modèle apprend trop précisément les données d'entraînement et généralise mal sur de nouvelles données.

---

## Q19. Comment éviter l'overfitting ?

**Réponse**

Validation croisée, régularisation, réduction de la complexité, davantage de données, early stopping selon le modèle.

---

## Q20. Différence entre biais et variance ?

**Réponse**

* Biais élevé : modèle trop simple.
* Variance élevée : modèle trop complexe.

L'objectif est de trouver un bon compromis.

---

# SECTION 4 — Validation

## Q21. Pourquoi utiliser la Cross Validation ?

**Réponse**

Elle fournit une estimation plus robuste des performances en évaluant le modèle sur plusieurs partitions des données.

---

## Q22. Qu'est-ce que le Grid Search ?

**Réponse**

Une recherche exhaustive sur un ensemble de combinaisons d'hyperparamètres.

---

## Q23. Et le Random Search ?

**Réponse**

Il explore un sous-ensemble aléatoire des combinaisons. Il est souvent plus efficace lorsque l'espace de recherche est vaste.

---

## Q24. Qu'est-ce que l'Early Stopping ?

**Réponse**

Arrêter l'entraînement lorsque les performances sur les données de validation cessent de s'améliorer afin de limiter le surapprentissage.

---

## Q25. Quelle métrique utiliser pour une fraude ?

**Réponse**

Cela dépend du coût métier. Les métriques comme Precision, Recall, F1-score, PR-AUC et ROC-AUC sont importantes, mais en fraude, le Recall et la Precision sont souvent plus pertinents que l'Accuracy.

---

# SECTION 5 — Détection de fraude

## Q26. Pourquoi l'Accuracy est-elle trompeuse ?

**Réponse**

Parce que la fraude est rare. Un modèle qui prédit toujours "non fraude" peut avoir une Accuracy très élevée tout en étant inutile.

---

## Q27. Qu'est-ce que le class imbalance ?

**Réponse**

Une forte différence entre le nombre d'exemples des classes. C'est très courant en fraude.

---

## Q28. Comment gérer un jeu de données déséquilibré ?

**Réponse**

Plusieurs approches :

* pondération des classes (`class_weight`) ;
* sur-échantillonnage (SMOTE, par exemple) ;
* sous-échantillonnage ;
* ajustement du seuil de décision ;
* choix de métriques adaptées.

---

## Q29. Qu'est-ce que le data leakage ?

**Réponse**

Utiliser une information indisponible au moment de la prédiction. Cela conduit à des performances artificiellement élevées.

---

## Q30. Quelle est la meilleure feature de fraude ?

**Réponse**

Il n'existe pas de meilleure feature universelle. Les variables comportementales, temporelles et relationnelles sont souvent parmi les plus performantes.

---

# SECTION 6 — Feature Engineering

## Q31. Pourquoi créer des features ?

**Réponse**

Pour transformer les données brutes en informations plus pertinentes pour le modèle.

---

## Q32. Donnez un exemple de feature comportementale.

**Réponse**

Le nombre de transactions effectuées dans les dernières 24 heures.

---

## Q33. Donnez un exemple de feature de graphe.

**Réponse**

Le nombre de comptes partageant le même appareil (`accounts_per_device`).

---

## Q34. Pourquoi utiliser SHAP ?

**Réponse**

Pour expliquer les prédictions d'un modèle et comprendre la contribution de chaque feature.

---

## Q35. Comment sélectionner les features ?

**Réponse**

Je combine expertise métier, analyses statistiques, importance des variables et validation expérimentale.

---

# SECTION 7 — AWS

## Q36. Pourquoi utiliser S3 ?

**Réponse**

Pour stocker les données, les modèles, les artefacts d'entraînement et les sauvegardes.

---

## Q37. Pourquoi utiliser EC2 ?

**Réponse**

Pour exécuter les services applicatifs ou les traitements nécessitant des machines virtuelles.

---

## Q38. À quoi sert RDS ?

**Réponse**

À héberger une base de données relationnelle gérée.

---

## Q39. À quoi sert CloudWatch ?

**Réponse**

À surveiller les métriques, les logs et les alertes des applications et de l'infrastructure.

---

## Q40. Pourquoi API Gateway ?

**Réponse**

Pour exposer et sécuriser les APIs, gérer le routage, les quotas et l'authentification.

---

# SECTION 8 — MLOps

## Q41. Qu'est-ce qu'un Feature Store ?

**Réponse**

Un système qui centralise les features et garantit leur cohérence entre l'entraînement et la production.

---

## Q42. Pourquoi versionner les modèles ?

**Réponse**

Pour assurer la traçabilité, comparer les performances et revenir rapidement à une version précédente si nécessaire.

---

## Q43. Qu'est-ce que le Data Drift ?

**Réponse**

Une évolution de la distribution des données d'entrée.

---

## Q44. Qu'est-ce que le Concept Drift ?

**Réponse**

Une évolution de la relation entre les variables d'entrée et la cible.

---

## Q45. Que surveiller en production ?

**Réponse**

La qualité des données, la latence, les erreurs, les performances du modèle, le drift et les indicateurs métier.

---

# SECTION 9 — Architecture

## Q46. Comment déployer un modèle ?

**Réponse**

Je l'encapsule dans une API (par exemple FastAPI), je le conteneurise avec Docker, puis je le déploie sur une infrastructure adaptée (EC2, Kubernetes, etc.) avec un système de monitoring.

---

## Q47. Batch ou temps réel ?

**Réponse**

Le choix dépend des contraintes métier. La fraude sur les paiements exige souvent une décision en temps réel.

---

## Q48. Pourquoi Docker ?

**Réponse**

Pour garantir un environnement reproductible entre le développement, les tests et la production.

---

## Q49. Pourquoi Kubernetes ?

**Réponse**

Pour l'orchestration, l'auto-scalabilité, la haute disponibilité et les mises à jour sans interruption.

---

## Q50. Pourquoi FastAPI ?

**Réponse**

Parce qu'il est performant, simple à utiliser, génère automatiquement une documentation OpenAPI et s'intègre bien avec Python.

---

# Ce que nous verrons ensuite

Nous poursuivrons avec les **50 questions restantes**, qui porteront notamment sur :

* XGBoost, LightGBM et CatBoost ;
* Random Forest et Gradient Boosting ;
* Réseaux de neurones ;
* Graph Machine Learning ;
* PostgreSQL avancé ;
* Optimisation des performances ;
* Statistiques ;
* Probabilités ;
* Études de cas de fraude ;
* Questions comportementales ("Behavioral Interview").

Ces questions correspondent au niveau attendu pour un **Data Scientist chez SentiLink** et sont souvent celles qui font la différence entre un bon candidat et un candidat retenu.
