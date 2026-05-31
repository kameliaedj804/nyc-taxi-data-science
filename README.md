# Analyse et Modélisation des Trajets des Taxis Jaunes de New York

**Projet de fin de session – L3 DANT – Introduction à la Data Science**

## Contexte général du projet : 

Ce projet analyse les données de courses des taxis jaunes de New York City ssues du programme TPEP (Taxicab & Livery Passenger Enhancement Program).
L'objectif est de mener un pipeline complet de Data Science : exploration, nettoyage, feature engineering, apprentissage supervisé et non supervisé.


## Structure du projet

```
nyc-taxi-data-science/
├── notebook/
│   └── projet.ipynb              # Notebook principal
├── data/
│   └── yellowtripdata_100k.csv   # Echantillon de 100 000 courses
├── requirements.txt
└── README.md
```

## Installation et exécution

### 1. Cloner le dépôt

```bash
git clone https://github.com/<votre-username>/nyc-taxi-data-science.git
cd nyc-taxi-data-science
```

### 2. Installer les dépendances

```bash
pip install -r requirements.txt
```

### 3. Lancer le notebook

```bash
jupyter notebook notebook/projet.ipynb
```

Exécuter les cellules de haut en bas avec Kernel -> Restart & Run All.

## Dépendances

```
pandas
numpy
scikit-learn
matplotlib
seaborn
xgboost
jupyter
```

## Pipeline du projet

### Etape 1 : Exploration et Nettoyage (EDA)

- Chargement de 99 999 courses brutes
- Identification des valeurs aberrantes : distances nulles, montants négatifs, coordonnées GPS hors NYC, RatecodeID invalides, vitesses impossibles
- Environs 98 000 trajets valides conservés après nettoyage
- Visualisations : distribution des distances, courses par heure, modes de paiement, heatmap de corrélation, scatter distance/montant, distribution des passagers

### Etape 2 : Feature Engineering

Variables obligatoires :
- duree_course : durée en minutes entre pickup et dropoff
- heure_journee, jour_semaine : composantes temporelles
- est_weekend : indicateur binaire week-end
- est_heure_pointe : indicateur rush hour

Variables bonus :
- vitesse_moyenne : distance / durée (miles/minute)
- est_trajet_aeroport : trajets JFK/Newark (RatecodeID 2 ou 3)
- heure_sin, heure_cos : encodage cyclique de l'heure
- distance_haversine : distance réelle a vol d'oiseau (formule de Haversine)
- indice_detour : rapport trip_distance / distance_haversine

### Etape 3 : Apprentissage supervisé

Objectif : prédire le montant total de la course (total_amount)

| Modèle                        | RMSE   | MAE    | R²     |
|-------------------------------|--------|--------|--------|
| Régression Linéaire (baseline)| 2.90 $ | 1.44 $ | 0.9393 |
| Random Forest                 | 2.70 $ | 1.45 $ | 0.9443 |
| XGBoost                       | 2.54 $ | 1.37 $ | 0.9480 |

La variable la plus influente est trip_distance (90% d'importance).

### Etape 4 : Apprentissage non supervisé

Objectif : segmenter les trajets en profils types

- Méthode du coude : identifie k=4 comme nombre optimal
- Score de silhouette : confirme k=4 comme pic local (0.5502)
- DBSCAN : utilisé comme alternative pour identifier les courses atypiques

4 profils identifiés :

| Cluster | Profil                 | Distance moy. | Durée moy. | Montant moy. |
|---------|------------------------|---------------|------------|--------------|
| 0       | Court trajet nocturne  | 1.54 miles    | 9 min      | 10.70 $      |
| 1       | Navetteur du soir      | 5.12 miles    | 23 min     | 24.53 $      |
| 2       | Court trajet matinal   | 1.61 miles    | 11 min     | 11.39 $      |
| 3       | Trajet aeroport        | 13.67 miles   | 37 min     | 52.65 $      |

---

## Résultats clés

- Le prix d'un taxi a NYC est principalement déterminé par la distance et la durée
- XGBoost est le modèle le plus performant (RMSE = 2.54 $)
- Les trajets aeroport sont les plus rentables pour les chauffeurs (~52 $)
- L'echantillon présente un biais vers les jours de semaine (est_weekend ~ 1%)

---

## Auteur

Kamelia Edjekouane - L3 DANT
