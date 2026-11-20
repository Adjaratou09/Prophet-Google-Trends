# Prévoir l'intérêt pour la « data science » avec Prophet

Prévision de l'intérêt de recherche Google pour le mot-clé **« data science »** aux États-Unis, à partir des données **Google Trends** (2004-2020), avec la bibliothèque **Prophet** de Meta (Facebook).


## Objectif

Prévoir l'intérêt mensuel pour « data science » sur **2 ans** (2018-2019), à partir des données de 2004 à 2017, et choisir le modèle Prophet le mieux adapté à la série.

## Données

- **Source :** [Google Trends](https://trends.google.com/trends/?geo=US), mot-clé « data science », États-Unis
- **Période :** janvier 2004 – juillet 2020 (199 mois ; août 2020, incomplet au moment de l'extraction, a été retiré)
- **Échelle :** 0 à 100 (100 = mois de plus forte popularité sur la période)

Les graphiques (série, prévisions, composantes, validation croisée) sont visibles directement dans le notebook.

## Démarche

1. **Exploration de la série**
   - trois phases : intérêt faible et stable (2004-2011), forte croissance (2012-2019), plafonnement en 2020 ;
   - saisonnalité annuelle marquée : pic à la rentrée universitaire (septembre), creux en été et en décembre ;
   - profil saisonnier par mois, décomposition additive et multiplicative, transformation logarithmique : l'amplitude saisonnière grandit avec le niveau de la série.
2. **Modélisation avec Prophet**, entraînement sur 2004-2017 et test sur 2018-2019 :
   - saisonnalité **additive** (réglage par défaut) ;
   - saisonnalité **multiplicative** ;
   - saisonnalité multiplicative **lissée** (4 termes de Fourier au lieu de 10), plus adaptée à des données mensuelles.
3. **Évaluation** sur le jeu de test : MAE, RMSE, MAPE, biais et couverture de l'intervalle d'incertitude.
4. **Validation croisée temporelle** : 17 plis, 5 ans d'entraînement initial, horizon de 1 an, décalage de 180 jours.

## Résultats

| Modèle | MAE test 2018-2019 | MAPE test | MAE validation croisée | MAPE validation croisée |
|---|---|---|---|---|
| Prophet additif | 5,29 | 6,54 % | 3,87 | 17,9 % |
| Prophet multiplicatif (Fourier 10) | 3,63 | 4,67 % | — | — |
| **Prophet multiplicatif (Fourier 4)** | **3,63** | **4,77 %** | **3,88** | **18,4 %** |


- Le modèle **multiplicatif** est nettement meilleur sur 2018-2019 (MAE de 3,63 contre 5,29) : il reproduit l'amplitude des pics et des creux, qui grandit avec le niveau de la série.
- En validation croisée (2009-2017), les deux modes sont équivalents : sur une période où la série est faible, l'amplitude saisonnière ne dépasse pas quelques points.
- Avec **4 termes de Fourier**, la saisonnalité devient lisse et interprétable (+20 % en septembre, −20 % en juin-juillet), pour une précision équivalente.


**Modèle retenu :** Prophet à saisonnalité multiplicative, 4 termes de Fourier.

## Limites et pistes

- **Intervalles d'incertitude trop étroits :** seulement 30 à 40 % des valeurs réelles tombent dans l'intervalle à 80 %. Prophet ne tient pas compte de l'incertitude sur la saisonnalité.
- **Tendance linéaire :** Prophet prolonge la dernière pente et ne peut pas anticiper le plafonnement de 2020.
- **Pistes :** croissance logistique plafonnée (`growth="logistic"`), prise en compte du Covid comme événement (`holidays`), comparaison avec un modèle SARIMA.

## Structure du dépôt

```
├── README.md
├── requirements.txt
├── 
│  data_science_google_trends.csv
── facebook_prophet.ipynb
```



## Outils

Python · pandas · NumPy · Matplotlib · statsmodels · Prophet

## Auteure

**Adjaratou Diarrassouba** 
