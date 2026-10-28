# Telecom Churn Analytics

**Analyse du churn client dans le secteur des télécommunications avec la Business Intelligence et le Machine Learning**

## 1. Présentation du projet

Ce projet porte sur l'analyse du *Customer Churn*, c'est-à-dire le départ des clients d'une entreprise de télécommunications.

L'objectif est d'exploiter les données clients afin de comprendre les principaux facteurs associés au churn, de visualiser les indicateurs clés et d'étudier la possibilité de prédire les clients susceptibles de quitter l'entreprise.

Le projet est organisé autour de deux volets complémentaires :

- **Business Intelligence (BI)** : conception d'un Data Warehouse, développement des processus ETL, création d'un cube OLAP et réalisation d'un tableau de bord interactif.
- **Machine Learning (ML)** : préparation des données, entraînement de modèles de classification et comparaison de leurs performances pour la prédiction du churn.

## 2. Objectifs

Les principaux objectifs sont :

- Analyser le comportement des clients et leurs caractéristiques.
- Identifier les segments de clients présentant un nombre élevé de départs.
- Étudier l'influence du type de contrat, des services souscrits et de l'ancienneté.
- Centraliser les données dans un Data Warehouse organisé selon un schéma en étoile.
- Faciliter l'analyse multidimensionnelle grâce à un cube OLAP.
- Concevoir un tableau de bord permettant d'explorer les indicateurs de churn.
- Tester des modèles de Machine Learning pour prédire le départ des clients.
- Comparer les modèles à l'aide de métriques adaptées à la classification.

## 3. Jeu de données

Le projet utilise le jeu de données **Telco Customer Churn**, qui contient des informations sur les clients d'une entreprise de télécommunications.

Le jeu de données original comporte **7 043 clients et 21 variables**.

Les variables couvrent notamment :

- **Informations démographiques** : genre, âge supérieur à 65 ans, partenaire et personnes à charge.
- **Contrat et facturation** : type de contrat, mode de paiement, facturation électronique.
- **Services souscrits** : téléphonie, internet, sécurité en ligne et assistance technique.
- **Informations financières** : charges mensuelles et charges totales.
- **Ancienneté** : durée pendant laquelle le client est resté dans l'entreprise.
- **Churn** : indication précisant si le client a quitté l'entreprise.

La variable cible utilisée pour l'analyse du churn est `Churn`.

*Remarque : le jeu de données contient des informations clients utilisées à des fins pédagogiques et analytiques. Il ne représente pas nécessairement les données d'une entreprise réelle.*

## 4. Architecture du projet

Le projet suit deux chaînes de traitement complémentaires.

### 4.1. Architecture Business Intelligence

Les données sont préparées et chargées dans un Data Warehouse avant d'être exploitées dans un cube OLAP et un tableau de bord Power BI.

**Chaîne de traitement :**

Données sources → Staging → ETL avec SSIS → Data Warehouse → Cube OLAP avec SSAS → Power BI

### 4.2. Architecture Machine Learning

Les données sont préparées pour entraîner et évaluer des modèles de classification.

**Chaîne de traitement :**

Données sources → Prétraitement → Séparation des données → Entraînement des modèles → Évaluation → Comparaison des performances

## 5. Partie I — Business Intelligence

### 5.1. Technologies utilisées

- Microsoft SQL Server
- SQL Server Integration Services (SSIS)
- SQL Server Analysis Services (SSAS)
- Power BI Desktop
- SQL

### 5.2. Data Warehouse

Le Data Warehouse a été créé dans SQL Server sous le nom `DataWarehouseChurn`.

Une table de staging, `StagingChurn`, sert de zone intermédiaire pour préparer les données avant leur chargement dans les tables dimensionnelles et la table de faits.

Le modèle décisionnel repose sur un **schéma en étoile** comprenant les tables suivantes :

| Table | Description |
|---|---|
| `DimDemographics` | Informations démographiques des clients |
| `DimContract` | Informations relatives aux contrats |
| `DimServices` | Services souscrits par les clients |
| `DimTenureGroup` | Groupes d'ancienneté des clients |
| `FactChurn` | Données et indicateurs utilisés pour l'analyse du churn |

Cette organisation facilite les analyses multidimensionnelles et la production d'indicateurs décisionnels.

### 5.3. Processus ETL avec SSIS

Les processus ETL (*Extract, Transform, Load*) ont été développés avec SQL Server Integration Services.

Les packages créés sont :

1. `StagingChurn.dtsx`
2. `Load_DimDemographics.dtsx`
3. `Load_DimContract.dtsx`
4. `Load_DimServices.dtsx`
5. `Load_DimTenureGroup.dtsx`
6. `Load_FactChurn.dtsx`

Les principales opérations comprennent :

- Le chargement des données sources dans la table de staging.
- La préparation et la déduplication des données dimensionnelles.
- La création de groupes d'ancienneté : 0–12 mois, 13–24 mois, 25–48 mois et 49 mois ou plus.
- La conversion de la variable `Churn` en indicateur numérique `Churn_Int` : 1 pour Yes et 0 pour No.
- L'utilisation de transformations Lookup pour récupérer les clés des dimensions.
- Le chargement des données dans la table de faits.

### 5.4. Cube OLAP avec SSAS

Un cube OLAP nommé `ChurnAnalyticsCube` a été conçu avec SQL Server Analysis Services.

La configuration comprend :

- Une source de données connectée au Data Warehouse.
- Une Data Source View (DSV).
- Les dimensions nécessaires à l'analyse.
- Les mesures permettant d'étudier les clients et leurs caractéristiques.

Les indicateurs exploités comprennent notamment le nombre total de clients, le churn, l'ancienneté, les charges mensuelles et les charges totales.

Le cube a été déployé et traité afin de permettre l'exploration des données dans un environnement multidimensionnel.

### 5.5. Tableau de bord Power BI

Power BI est connecté au cube SSAS en utilisant une connexion Live Connection.

Le tableau de bord permet de consulter les principaux indicateurs et de comparer les caractéristiques des clients ayant quitté l'entreprise.

Les visualisations comprennent notamment :

- Le nombre total de clients.
- Le nombre de clients ayant quitté l'entreprise.
- Le taux global de churn.
- Les départs par type de contrat.
- Les départs par service Internet.
- Les départs par groupe d'ancienneté.
- Les départs selon la présence d'un partenaire.
- Les indicateurs liés aux charges mensuelles.

### 5.6. Principaux constats de l'analyse BI

Les résultats obtenus dans le tableau de bord permettent notamment d'observer :

- Un taux global de churn d'environ **26,54 %**.
- Une concentration importante des départs parmi les clients ayant un contrat mensuel (*Month-to-month*).
- Un nombre élevé de départs parmi les clients ayant une ancienneté comprise entre 0 et 12 mois.
- Un nombre important de départs chez les clients utilisant le service Internet Fiber Optic.

Ces constats décrivent les tendances observées dans les données. Les nombres de départs par segment ne doivent pas être confondus avec les taux de churn de ces segments, qui nécessitent de rapporter les départs au nombre total de clients concernés.

## 6. Partie II — Machine Learning

### 6.1. Objectif

La partie Machine Learning vise à construire des modèles capables d'estimer la probabilité qu'un client quitte l'entreprise.

Il s'agit d'un problème de **classification binaire** :

- `0` : le client ne quitte pas l'entreprise.
- `1` : le client quitte l'entreprise.

### 6.2. Technologies utilisées

- Python
- Jupyter Notebook
- Pandas et NumPy
- Scikit-learn
- XGBoost
- Matplotlib et Seaborn

### 6.3. Préparation des données

Le pipeline de Machine Learning comprend les étapes suivantes :

- Chargement et exploration des données.
- Vérification des valeurs manquantes et des types de variables.
- Préparation des variables explicatives et de la variable cible.
- Encodage des variables catégorielles et traitement des variables numériques.
- Séparation des données en ensembles d'entraînement et de test en préservant la distribution de la variable cible.
- Préparation du pipeline de prétraitement et de modélisation.

Le prétraitement doit être ajusté sur les données d'entraînement afin de limiter les risques de fuite de données (*data leakage*).

### 6.4. Modèles étudiés

Deux modèles de classification sont étudiés :

**Random Forest**

Random Forest est un algorithme d'ensemble qui combine plusieurs arbres de décision. Il permet de modéliser des relations non linéaires et de mesurer l'importance des variables.

**XGBoost**

XGBoost est un algorithme de gradient boosting qui construit progressivement un ensemble d'arbres afin d'améliorer les prédictions.

### 6.5. Évaluation des modèles

Les modèles sont évalués à l'aide de plusieurs métriques :

| Métrique | Objectif |
|---|---|
| Accuracy | Proportion totale de prédictions correctes |
| Precision | Proportion de vrais churners parmi les clients prédits comme churners |
| Recall | Proportion de churners réellement détectés |
| F1-score | Équilibre entre précision et rappel |
| F2-score | Métrique donnant davantage de poids au rappel |
| ROC-AUC | Capacité à distinguer les deux classes sur différents seuils |
| Average Precision | Résumé de la courbe précision-rappel |

Le choix du seuil de classification influence le compromis entre les clients détectés et les fausses alertes. Un seuil plus faible peut permettre de détecter davantage de churners, mais peut aussi augmenter le nombre de faux positifs.

La comparaison finale doit être réalisée à partir de résultats effectivement obtenus lors de l'exécution du notebook.

## 7. Structure du dépôt GitHub

L'organisation recommandée du dépôt est la suivante :

```text
telecom-churn-analytics/
│
├── README.md
│
├── bi-project/
│   ├── sql/
│   ├── ssis/
│   ├── ssas/
│   ├── powerbi/
│   └── README.md
│
├── ml-project/
│   ├── telecom-churn-prediction.ipynb
│   ├── requirements.txt
│   └── README.md
│
└── screenshots/
    ├── powerbi-dashboard.png
    ├── ssis-execution.png
    └── ssas-cube.png
```

Cette structure est indicative. Les dossiers doivent contenir les fichiers réellement disponibles dans le projet. Les captures d'écran sont facultatives, mais utiles pour présenter les résultats.

## 8. Installation et utilisation

### 8.1. Partie Business Intelligence

Pour reproduire la partie BI, il est nécessaire de disposer d'un environnement Microsoft SQL Server compatible avec les composants utilisés.

Étapes générales :

1. Récupérer les fichiers SQL et les projets SSIS et SSAS.
2. Adapter les chaînes de connexion à l'environnement local.
3. Vérifier les chemins des fichiers sources et les noms des bases de données.
4. Créer ou restaurer le Data Warehouse.
5. Exécuter les packages SSIS dans l'ordre prévu.
6. Déployer et traiter le cube SSAS.
7. Ouvrir le rapport Power BI et adapter la connexion au cube si nécessaire.

Les versions des outils, les extensions installées et la configuration du serveur peuvent nécessiter des ajustements.

### 8.2. Partie Machine Learning

Un environnement Python est nécessaire pour exécuter le notebook.

Après avoir récupéré le dépôt, créer un environnement virtuel et installer les dépendances disponibles dans `ml-project/requirements.txt`.

Exemple sous Windows :

```bash
python -m venv .venv
.venv\Scripts\activate
pip install -r ml-project/requirements.txt
```

Ouvrir ensuite le notebook `telecom-churn-prediction.ipynb` dans Jupyter Notebook ou dans Visual Studio Code.

Placer le fichier CSV du dataset dans le chemin attendu par le notebook, ou modifier le chemin de chargement. Exécuter les cellules dans l'ordre et vérifier les résultats produits.

Les dépendances et les chemins peuvent devoir être adaptés selon l'environnement utilisé.

## 9. Limites et améliorations possibles

Le projet constitue une base d'analyse décisionnelle et de prédiction du churn. Plusieurs améliorations sont envisageables :

- Automatiser l'actualisation des données et des indicateurs.
- Ajouter des contrôles de qualité aux processus ETL.
- Améliorer les visualisations et les analyses par segment.
- Comparer davantage de modèles et de stratégies de validation.
- Examiner l'importance des variables et interpréter les prédictions.
- Exporter les probabilités de churn calculées par le modèle vers une table SQL.
- Intégrer les prédictions dans Power BI pour comparer les résultats réels et les risques estimés.
- Mettre en place un suivi des performances du modèle après son déploiement.

L'intégration des prédictions ML dans le tableau de bord BI représente une évolution possible du projet ; elle nécessite un flux de données supplémentaire et n'est pas considérée comme réalisée tant que cette intégration n'a pas été développée et vérifiée.

## 10. Compétences mobilisées

- Analyse et préparation des données.
- SQL et modélisation dimensionnelle.
- Conception d'un Data Warehouse en schéma en étoile.
- Développement de processus ETL avec SSIS.
- Modélisation multidimensionnelle avec SSAS.
- Création de tableaux de bord avec Power BI.
- Prétraitement des données avec Python.
- Classification supervisée avec Random Forest et XGBoost.
- Évaluation et comparaison de modèles de Machine Learning.
- Organisation et documentation d'un projet avec GitHub.

## 11. Conclusion

Ce projet met en pratique deux approches complémentaires de l'analyse des données : la Business Intelligence pour comprendre et visualiser les tendances historiques, et le Machine Learning pour étudier la prédiction du départ des clients.

L'association de ces approches constitue une base pour développer, à terme, une solution intégrée d'aide à la décision permettant de mieux comprendre le churn et d'identifier les clients nécessitant une attention particulière.

## 12. Avertissement

Les fichiers BI peuvent dépendre de configurations locales, notamment les chemins d'accès, les chaînes de connexion, les noms de serveurs et les versions des outils. Une adaptation peut être nécessaire avant toute reproduction dans un autre environnement.

Les résultats du notebook Machine Learning doivent être vérifiés après exécution dans l'environnement cible avant d'être utilisés comme résultats définitifs.
