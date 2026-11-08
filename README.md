# 🏥 Prédiction de la réadmission hospitalière

Projet final du module **ISD2** : construire un modèle qui repère, dès la sortie d'un patient diabétique, ceux qui risquent d'être réadmis à l'hôpital **dans les 30 jours**.

**[Consulter le notebook et ses résultats](projet_final.ipynb)**

![Python](https://img.shields.io/badge/Python-3.x-blue.svg)
![scikit-learn](https://img.shields.io/badge/scikit--learn-Random%20Forest-orange.svg)
![Jupyter](https://img.shields.io/badge/Notebook-Jupyter-F37626.svg)

---

## 🎯 Problématique

Une réadmission rapide est un indicateur clé de la qualité des soins. Aux États-Unis, le programme **HRRP** pénalise les hôpitaux dont le taux de réadmission est anormalement élevé. Chez les patients diabétiques, elle signale souvent une instabilité glycémique mal gérée pendant le premier séjour.

**Objectif :** identifier les patients à haut risque pour mieux allouer les ressources et améliorer le suivi après la sortie.

## 📊 Données

- **Source :** [Diabetes 130-US Hospitals (1999-2008), UCI](https://archive.ics.uci.edu/ml/datasets/diabetes+130-us+hospitals+for+years+1999-2008)
- **Volume :** 101 766 séjours, 130 hôpitaux américains, 50 variables (démographie, diagnostics ICD-9, médication)
- **Cible :** `readmitted`, transformée en binaire (1 = réadmis en moins de 30 jours, 0 sinon)
- **Référence scientifique :** Strack et al. (2014), AUC-ROC d'environ 0,65 sur ce jeu de données

## 🔬 Méthode

1. **Audit qualité :** suppression des colonnes avec plus de 40 % de valeurs manquantes (le poids en manque 96,9 %).
2. **Nettoyage :**
   - décodage des codes numériques via `IDS_mapping.csv` ;
   - retrait des patients décédés ou en soins palliatifs (non réadmissibles) ;
   - **déduplication : seule la première visite de chaque patient est conservée**, pour éviter la fuite d'information (*data leakage*). Le taux de réadmission passe alors de 11,2 % à **9,0 %**.
3. **Analyse exploratoire :** le risque augmente avec l'âge (surtout après 70 ans) et avec la durée du séjour initial.
4. **Feature engineering :** regroupement des codes de diagnostic ICD-9 en **8 catégories cliniques** (circulatoire, digestif, diabète…).
5. **Modélisation :** Random Forest comparé à une régression logistique, avec `class_weight='balanced'` pour compenser le fort déséquilibre des classes (9 % / 91 %) et un découpage train/test **stratifié**.
6. **Validation :** courbe ROC, matrice de confusion, comparaison train/test (sur-apprentissage), validation croisée à 5 plis.

## 📈 Résultats

| Indicateur | Valeur |
|---|---|
| AUC-ROC (test) | **0,63** |
| AUC-ROC (validation croisée, 5 plis) | **0,63** (stable) |
| Réadmissions réelles détectées | environ **35 %** |
| Référence de la littérature | environ 0,65 |

**Variables les plus influentes :** nombre de séjours hospitaliers dans l'année précédente, durée du séjour actuel, âge.

### Limites

- Un AUC de 0,63 reste modeste : la réadmission dépend de facteurs biologiques et sociaux absents des données.
- Léger sur-apprentissage entre l'entraînement et le test.
- Le modèle est une base d'aide à la décision, pas un outil de diagnostic.

### Pistes d'amélioration

- Rééquilibrage des classes avec **SMOTE**
- Regroupement des diagnostics avec les catégories **CCS** (18 catégories)
- Modèles de **Gradient Boosting** (XGBoost)

## 📁 Contenu du dépôt

```
├── projet_final.ipynb     # Notebook complet : analyse, modèle, résultats
├── diabetic_data.csv      # Jeu de données brut (UCI)
├── IDS_mapping.csv        # Correspondance des codes (admission, sortie…)
└── README.md
```

## 🚀 Lancer le notebook

```bash
pip install pandas numpy matplotlib seaborn scikit-learn jupyter
jupyter notebook projet_final.ipynb
```

## 🛠 Outils

`pandas` · `numpy` · `matplotlib` · `seaborn` · `scikit-learn`

## 👥 Équipe

- **Manishva VIDJEACOUMAR**
- **Edam DAMMAK**
- **Louaï ZERDOUMI**
