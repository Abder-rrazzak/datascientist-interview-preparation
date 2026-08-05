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


Nous allons terminer le **MODULE 36** avec les **50 questions restantes**. Ce sont les questions qui reviennent le plus souvent dans les entretiens **Senior Data Scientist / Machine Learning Engineer** chez SentiLink et d'autres entreprises FinTech.

---

# MODULE 36 (Suite) — Questions 51 à 100

---

# SECTION 10 — Arbres de décision et Gradient Boosting

## Q51. Pourquoi XGBoost est-il si populaire ?

**Réponse**

Parce qu'il combine :

* une excellente performance sur les données tabulaires ;
* une gestion efficace des valeurs manquantes ;
* une régularisation intégrée ;
* une parallélisation de l'entraînement.

---

## Q52. Différence entre Random Forest et XGBoost ?

**Réponse**

* **Random Forest** construit plusieurs arbres indépendants en parallèle (bagging).
* **XGBoost** construit les arbres de manière séquentielle, chaque nouvel arbre corrigeant les erreurs des précédents (boosting).

---

## Q53. Quand utiliser CatBoost ?

**Réponse**

Lorsque les données contiennent beaucoup de variables catégorielles. CatBoost les traite efficacement sans nécessiter un encodage manuel important.

---

## Q54. Que fait LightGBM ?

**Réponse**

Il accélère l'entraînement grâce à une stratégie de croissance des arbres optimisée et une consommation mémoire réduite.

---

## Q55. Comment éviter l'overfitting avec XGBoost ?

**Réponse**

En ajustant notamment :

* `max_depth`
* `learning_rate`
* `min_child_weight`
* `subsample`
* `colsample_bytree`
* `reg_alpha`
* `reg_lambda`
* l'early stopping.

---

# SECTION 11 — Statistiques

## Q56. Différence entre moyenne et médiane ?

**Réponse**

* La moyenne est sensible aux valeurs extrêmes.
* La médiane est plus robuste en présence d'outliers.

---

## Q57. Qu'est-ce qu'une distribution normale ?

**Réponse**

Une distribution symétrique en forme de cloche où la moyenne, la médiane et le mode coïncident.

---

## Q58. Qu'est-ce qu'un intervalle de confiance ?

**Réponse**

Une plage de valeurs dans laquelle on estime que le vrai paramètre se situe avec un certain niveau de confiance (par exemple 95 %).

---

## Q59. Qu'est-ce qu'une p-value ?

**Réponse**

La probabilité d'observer un résultat au moins aussi extrême que celui obtenu si l'hypothèse nulle est vraie.

---

## Q60. Corrélation signifie-t-elle causalité ?

**Réponse**

Non.

Deux variables peuvent être corrélées sans qu'il existe une relation de cause à effet.

---

# SECTION 12 — Probabilités

## Q61. Théorème de Bayes ?

**Réponse**

Il permet de mettre à jour une probabilité à partir de nouvelles observations.

En fraude, il est souvent utilisé pour raisonner sur la probabilité qu'une transaction soit frauduleuse compte tenu des informations disponibles.

---

## Q62. Qu'est-ce qu'une probabilité conditionnelle ?

**Réponse**

La probabilité qu'un événement se produise sachant qu'un autre événement est déjà réalisé.

---

## Q63. Qu'est-ce que l'indépendance ?

**Réponse**

Deux événements sont indépendants si la réalisation de l'un ne modifie pas la probabilité de l'autre.

---

# SECTION 13 — Deep Learning

## Q64. Quand utiliser un réseau de neurones ?

**Réponse**

Principalement lorsque les relations entre les variables sont très complexes ou que les données sont non tabulaires (images, texte, audio).

---

## Q65. Pourquoi les arbres restent-ils populaires en fraude ?

**Réponse**

Parce qu'ils sont performants sur les données tabulaires, rapides à entraîner et souvent plus faciles à expliquer.

---

## Q66. Qu'est-ce que le dropout ?

**Réponse**

Une technique de régularisation qui désactive aléatoirement certains neurones pendant l'entraînement afin de limiter le surapprentissage.

---

# SECTION 14 — Graph Machine Learning

## Q67. Pourquoi utiliser des graphes ?

**Réponse**

Pour capturer les relations entre les entités (clients, appareils, emails, téléphones, IP), ce qui est particulièrement utile pour détecter des réseaux de fraude.

---

## Q68. Qu'est-ce qu'un nœud ?

**Réponse**

Une entité du graphe (par exemple un client, un appareil ou un numéro de téléphone).

---

## Q69. Qu'est-ce qu'une arête ?

**Réponse**

Une relation entre deux nœuds (par exemple « utilise », « possède », « partage »).

---

## Q70. Qu'est-ce que le degré d'un nœud ?

**Réponse**

Le nombre de connexions qu'il possède.

---

# SECTION 15 — PostgreSQL

## Q71. Pourquoi PostgreSQL ?

**Réponse**

Parce qu'il est robuste, open source, conforme aux standards SQL et très performant pour les applications transactionnelles.

---

## Q72. Qu'est-ce qu'un index ?

**Réponse**

Une structure de données qui accélère les recherches au prix d'un coût supplémentaire lors des insertions et mises à jour.

---

## Q73. Pourquoi éviter `SELECT *` ?

**Réponse**

Parce qu'il récupère des colonnes inutiles, augmente le trafic réseau et peut ralentir les requêtes.

---

## Q74. Comment analyser une requête lente ?

**Réponse**

Avec `EXPLAIN` ou `EXPLAIN ANALYZE` pour comprendre le plan d'exécution.

---

# SECTION 16 — Conception de systèmes

## Q75. Votre modèle doit répondre en moins de 100 ms. Que faites-vous ?

**Réponse**

Je privilégie :

* des features calculables rapidement ;
* un modèle optimisé ;
* du cache si nécessaire ;
* une API légère ;
* une infrastructure adaptée.

---

## Q76. Comment gérer 10 000 requêtes par seconde ?

**Réponse**

Avec un équilibrage de charge, plusieurs instances, une mise à l'échelle horizontale, du cache et un monitoring continu.

---

## Q77. Pourquoi utiliser Redis ?

**Réponse**

Pour stocker des données fréquemment consultées ou des features temporaires avec une latence très faible.

---

# SECTION 17 — Cas pratiques

## Q78. Un client réalise 50 transactions en 10 minutes. Que faites-vous ?

**Réponse**

Je calcule des features de vélocité, j'analyse l'historique du client, les appareils, les IP, puis j'évalue le risque avec le modèle.

---

## Q79. Même appareil, 30 comptes différents.

**Réponse**

C'est un signal fort mais pas une preuve. Je complète l'analyse avec d'autres informations (IP, téléphone, email, géographie, historique).

---

## Q80. Même téléphone utilisé par plusieurs comptes.

**Réponse**

Je mesure le nombre de comptes associés, leur historique et j'intègre cette information comme feature.

---

# SECTION 18 — Explicabilité

## Q81. Comment expliquer une prédiction ?

**Réponse**

À l'aide de SHAP, de l'importance des variables et d'une analyse des principales features ayant influencé la décision.

---

## Q82. Pourquoi l'explicabilité est-elle importante ?

**Réponse**

Pour faciliter les audits, gagner la confiance des utilisateurs et respecter les exigences réglementaires.

---

# SECTION 19 — Questions comportementales

## Q83. Parlez-moi d'un projet difficile.

**Réponse (structure STAR)**

* Situation
* Tâche
* Actions
* Résultat

Le recruteur attend une histoire concrète et structurée.

---

## Q84. Comment gérez-vous un désaccord technique ?

**Réponse**

Je privilégie les données, les expérimentations et les échanges constructifs plutôt que les opinions.

---

## Q85. Que faites-vous lorsqu'un modèle ne fonctionne pas ?

**Réponse**

Je vérifie les données, les features, les hypothèses, les métriques et je procède par itérations.

---

## Q86. Pourquoi souhaitez-vous rejoindre SentiLink ?

**Réponse**

Parce que l'entreprise traite des problèmes réels de fraude à grande échelle, combine recherche et production, et offre un fort impact métier.

---

## Q87. Préférez-vous la recherche ou la production ?

**Réponse**

J'apprécie la recherche, mais je trouve particulièrement motivant de transformer des idées en solutions qui apportent une valeur concrète aux utilisateurs.

---

# SECTION 20 — Questions avancées

## Q88. Comment choisir un seuil de décision ?

**Réponse**

En fonction des coûts métier associés aux faux positifs et aux faux négatifs, et non uniquement en maximisant une métrique statistique.

---

## Q89. Pourquoi surveiller les faux positifs ?

**Réponse**

Parce qu'ils peuvent dégrader l'expérience utilisateur et entraîner des coûts opérationnels importants.

---

## Q90. Pourquoi surveiller les faux négatifs ?

**Réponse**

Parce qu'ils correspondent à des fraudes non détectées, avec un impact financier direct.

---

## Q91. Que feriez-vous le premier jour chez SentiLink ?

**Réponse**

Je chercherais à comprendre les données, les objectifs métier, les modèles existants, les métriques suivies et le pipeline de production avant de proposer des améliorations.

---

## Q92. Quel est votre plus grand atout ?

**Réponse**

Adaptez votre réponse à votre profil, mais mettez en avant votre capacité à résoudre des problèmes complexes, apprendre rapidement et collaborer efficacement.

---

## Q93. Quel est votre point à améliorer ?

**Réponse**

Choisissez un point réel mais non critique, et expliquez les actions mises en place pour progresser.

---

## Q94. Comment priorisez-vous plusieurs projets ?

**Réponse**

En évaluant leur impact métier, leur urgence, les risques et les dépendances techniques.

---

## Q95. Comment travaillez-vous avec les équipes produit ?

**Réponse**

Je commence par comprendre les besoins métier, je traduis ces besoins en objectifs mesurables, puis je valide régulièrement les résultats avec les parties prenantes.

---

## Q96. Que faites-vous si vos données sont de mauvaise qualité ?

**Réponse**

Je réalise un audit des données, je mets en place des contrôles qualité, je documente les problèmes et je collabore avec les équipes responsables des sources de données.

---

## Q97. Quelle est votre approche pour apprendre une nouvelle technologie ?

**Réponse**

Je commence par les concepts fondamentaux, puis je réalise un projet pratique et enfin j'étudie la documentation officielle et les bonnes pratiques.

---

## Q98. Comment mesurez-vous le succès d'un modèle ?

**Réponse**

À travers :

* les métriques ML ;
* les indicateurs métier ;
* la stabilité dans le temps ;
* l'impact opérationnel.

---

## Q99. Qu'est-ce qui distingue un bon Data Scientist d'un excellent Data Scientist ?

**Réponse**

Un excellent Data Scientist ne se limite pas à construire un bon modèle. Il comprend le métier, produit un code maintenable, collabore avec les autres équipes, déploie des solutions robustes et mesure leur impact réel.

---

## Q100. Pourquoi devrions-nous vous recruter ?

**Réponse**

Une réponse convaincante pourrait être :

> *Je combine des compétences solides en Python, SQL, Machine Learning et ingénierie des données avec une approche orientée production. J'aime comprendre les problèmes métier en profondeur, concevoir des solutions robustes et les améliorer de manière continue grâce aux données. Ce qui m'attire particulièrement chez SentiLink, c'est la possibilité de travailler sur des problématiques complexes de détection de fraude ayant un impact direct pour les clients.*

---

# Félicitations

À ce stade, vous avez couvert :

* ✅ Python avancé
* ✅ SQL avancé
* ✅ Machine Learning
* ✅ Statistiques et probabilités
* ✅ Feature Engineering
* ✅ Détection de fraude
* ✅ Détection d'anomalies
* ✅ Graph Machine Learning
* ✅ AWS
* ✅ MLOps
* ✅ Architecture de production
* ✅ 100 questions d'entretien

Vous disposez désormais des connaissances techniques attendues pour un entretien de **Data Scientist chez SentiLink**.

Le **Module 37** sera une **simulation complète d'un projet SentiLink**, de la collecte des données jusqu'au déploiement d'un système de détection de fraude en production, avec du code Python, SQL, AWS et des décisions d'architecture comme dans un véritable environnement professionnel. C'est le module qui vous préparera le plus efficacement aux études de cas techniques.

