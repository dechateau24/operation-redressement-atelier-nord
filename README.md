# Opération Redressement · Atelier Nord

Projet du module Predictive Analytics & Data Intelligence (M2 Data IA for FB, RNCP39591 bloc BC04), pairagogie du 13/11/2026.
Une agence data est missionnée par une marque de mode fictive en déclin. Toutes les données sont fictives.

[![Ouvrir dans Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/VOTRE_PSEUDO/operation-redressement-atelier-nord/blob/main/notebooks/Operation_Redressement_notebook.ipynb)

## La question du client

« Quels clients sont en train de partir, et comment les retenir sans détruire nos marges ? »

Atelier Nord : 180 magasins, un site et une appli, CA de 47,2 M€ en 2025 (−14 % depuis 2023), part du CA en promotion passée de 32 % à 37 %.
Dilemme : option A (baisser les prix pour reconquérir les jeunes) ou option B (monter en gamme, moins de promos, plus de services).

## Contenu du dépôt

```
notebooks/   notebook Colab complet (étapes 0 à 10, cellules exécutées)
presentation/ deck final de 8 slides
data/        les 5 fichiers CSV (optionnel, voir plus bas)
README.md    cette documentation
```

## Paramètres du client

| Paramètre | Valeur |
|---|---|
| Date d'observation | 31 mars 2026 |
| Départ (Y = 1) | aucun achat dans les 120 jours suivants |
| Budget de campagne | 3 500 € pour 5 000 clients |
| Coût d'envoi | 0,50 € par client, plus le coût de l'offre |
| Valeur d'un client retenu | 140 € de marge |
| Segments | 18-29, 30-44, 45+ |

## Données

Cinq fichiers : `clients`, `activite` (achats et retours), `digital` (appli, satisfaction), `campagne_test` (4 000 clients, 3 offres et un groupe contrôle), `mensuel` (CA 2021-2026 par âge).

## Méthode

1. **Nettoyage** : 60 doublons supprimés, 12 âges impossibles remplacés par la médiane, 39 orthographes de villes ramenées à 13, 112 clients sans fiche digitale, environ 3 % de valeurs manquantes sur genre, canal et club.
2. **Diagnostic** : les 18-29 ans perdent 37,6 % de CA entre 2023 et 2025 et expliquent environ 90 % du recul. Les 45+ sont stables. La baisse dure deux ans sans rebond, elle est structurelle.
3. **Cible** : population de 5 000 clients actifs au 31/03/2026, taux de départ de 31,3 %.
4. **Signaux** : le départ est 2,4 fois plus fréquent chez les clients dont l'activité ralentit (46 % contre 19 %).
5. **Variables** : 4 colonnes écartées sur 22 (voir ci-dessous).
6. **Modèles** : régression logistique, arbre de décision, forêt aléatoire. Retenu : régression logistique, AUC test 0,867, validation croisée 0,863. Seuil de 0,30, choisi en minimisant le coût des erreurs (rappel 78 %, précision 62 %).
7. **Offres** : la meilleure offre par segment est le programme de reprise pour les 18-29 et la livraison offerte pour les 30-44 et 45+.
8. **Ciblage** : plan sous budget de 3 500 €.

## Variables écartées

| Colonne | Raison |
|---|---|
| bon_reactivation_juillet2026 | Fuite de données : envoyée après le 31/03 aux clients déjà partis, elle contient la réponse (AUC > 0,93) |
| grossesse_declaree | Donnée de santé, catégorie particulière du RGPD, très faible valeur prédictive |
| couleur_preferee | Aucun lien logique avec le départ |
| code_postal | Proxy social possible, redondant avec la ville |

`genre` est conservé dans le modèle pour contrôler l'équité : l'AUC est identique avec et sans (0,867), il n'apporte aucune information.

## Résultats des 5 défis

| Défi | Résultat |
|---|---|
| 1. Une offre par segment | Profit net de 4 886 € contre 1 879 € pour une offre unique, à budget égal (1 007 clients ciblés, 59,8 sauvés) |
| 2. Équité du modèle | AUC de 0,864 (18-29), 0,874 (30-44), 0,837 (45+), 0,878 (femmes), 0,846 (hommes). Les 45+ sont les moins bien prédits |
| 3. Hypothèses | Point mort à 58 € par client retenu (91 € pour l'offre unique). Avec un effet divisé par 2 : +699 € (segmenté) contre −811 € (offre unique) |
| 4. Dépendance | La variable la plus lourde est `visites_appli_90j`. Sans elle, AUC de 0,751, et 38 % des partants captés dans le top 20 % au lieu de 49,9 % |
| 5. Prix ou déception | Départ de 45,7 % chez les moins satisfaits contre 17,9 % chez les plus satisfaits. La promotion ajoute environ 7 points, la note basse environ 17 |

## Recommandation

Option B, ciblée : services plutôt que baisse de prix générale. Programme de reprise pour les 18-29, livraison offerte pour les 30-44, test mesuré avant extension.

## Limites

- « Client sauvé » repose sur une réaction à 30 jours dans la campagne test, pas sur un achat à 120 jours.
- La date de fin des 90 jours de `visites_appli_90j` n'est pas documentée dans le fichier : à vérifier avec le client.
- Peu de clients par cellule dans la campagne test, donc les uplifts sont des estimations.
- Les 45+ ne reçoivent aucune offre dans le plan : leur suivi reste à prévoir.
- Les liens entre note de satisfaction, promotion et départ sont des corrélations.

## Éthique et conformité

Aucune donnée de santé dans le modèle, pas de décision fondée sur le code postal, équité contrôlée par âge et genre, possibilité de se désinscrire à chaque envoi, relecture humaine avant toute campagne.

## Reproduire

1. Ouvrir le notebook dans Google Colab.
2. Choisir `MARQUE = "atelier_nord"` (étape 0) et déposer les 5 CSV quand la fenêtre s'ouvre.
3. Exécuter les cellules dans l'ordre. Les décisions sont dans les cellules « À VOUS » : `EXCLURE` (étape 5), `SEUIL` (étape 6) et les cellules des défis (étape 10).

Les CSV viennent de l'école : ne les publiez dans `data/` que si l'intervenant l'autorise.

## Équipe

[Prénoms et rôles]
