# DEFT2013 Tâche 2 : 

MHEDHBI Khalil - MABULU Marius

## Description de la tâche

Le projet s'inscrit dans le cadre de la compétition DEFT 2013 et porte sur la classification thématique de documents. Plus précisément, la tâche consiste à assigner automatiquement une catégorie (Entrée, Plat principal ou Dessert) à une recette de cuisine à partir de son titre, de la liste de ses ingrédients, de ses instructions et d'autres données.

1 ou 2 exemples de documents (avec leur identifiant)

doc_id                                         recette_80405.xml  
titre                Poitrine de veau aux épices et aux abricots  
type                                              Plat principal  
difficulte                                           Très facile  
cout                                                       Moyen  
ingredients    - 1,5 kg de poitrine de veau avec les os - 1 g...  
recette        Dans une cocotte en fonte, faites chauffer l'h...

doc_id                                         recette_72303.xml  
titre          Salade d'été au saumon fumé, feta, pommes et c...  
type                                                      Entrée  
difficulte                                           Très facile  
cout                                                  Bon marché  
ingredients    - 1 concombre - 2 carottes - 2 pommes Golden o...  
recette        Eplucher le concombre et le détailler en laniè...

## Statistiques corpus

Nombre de document de train/test

Nombre de documents total :  
Train : 12 473 recettes.  
Test : 1 388 recettes.

Répartition des étiquettes dans chacun des sous-ensemble

Répartition des étiquettes (Train) :  
Plat principal : 46,5 % (5 802 documents)  
Dessert : 30,2 % (3 762 documents).  
Entrée : 23,3 % (2 909 documents) 

## Méthodes proposées

### Run1: Baseline aléatoire

Description de la méthode:

- descripteurs utilisés  
Aucun. 

- classifieur utilisé  
Prédiction aléatoire. On attribue au hasard l'une des trois catégories (Entrée, Plat, Dessert) à chaque recette.

### Run2: Baseline Classe Majoritaire

Description de la méthode :

- descripteurs utilisés  
Aucun.

- classifieur utilisé  
On génère un tableau rempli uniquement avec la classe ("Plat principal") pour toutes les prédictions.

### Run3: METH A Naive Bayes + TF-IDF

Description de la méthode :

- descripteurs utilisés  
Vectorisation TF-IDF standard sur le texte concaténé (titre, recette et ingrédients).

- classifieur utilisé  
Multinomial Naive Bayes.

### Run4: METH B Régression Logistique et TF-IDF + Bi-grammes (pour aller plus loin)

Description de la méthode :

- descripteurs utilisés  
Vectorisation TF-IDF avec n-grammes (1,2), d'un tokenizer avec lemmatisation, et filtrage des fréquences (max_df=0.95, min_df=5).

- classifieur utilisé  
Régression Logistique avec le paramètre class_weight='balanced'.

### Run 5 : METH C Modèle Enrichi 

Description de la méthode : 

- descripteurs utilisés  
Union de la matrice TF-IDF et de caractéristiques numériques calculées (nombre d'ingrédients, longueur de la recette, ratio). Les données numériques sont transformées par logarithme et standardisées. 

- classifieur utilisé  
Régression Logistique avec class_weight='balanced' et une régularisation renforcée (C=0.5).

## Résultats

| Run          | Macro-f1 Score |
| ------------ | --------:|
| Baseline 1   |  33,7 % |
| Baseline 2   |   21,1 |
| METH A       |  78,5 |
| METH B       |  87,5 |
| METH C       |  86,4 |

### Réflexion critique

Pistes d'analyse:

* La tâche est-elle bien définie ?  

Oui, la tâche est clairement définie. L'objectif est de réaliser une classification automatique de textes (recettes de cuisine) en trois catégories : Entrée, Plat principal ou Dessert. 

* Une recette peut-elle appartenir à plusieurs catégories ?  

D'après la structure du projet, non. Chaque recette est associée à une seule étiquette parmi les trois possibles. Il s'agit d'un problème de classification multiclasse où les catégories sont mutuellement exclusives. Même si dans la réalité certains plats pourraient être servis en entrée ou en plat, le modèle doit choisir la catégorie unique définie dans le jeu de données.

* Les classes sont-elles naturellement séparables ?  

Les classes ne sont pas toutes facilement séparables. Si la catégorie "Dessert" se distingue très bien grâce à un vocabulaire spécifique (sucre, chocolat), il existe une forte confusion entre les "Entrées" et les "Plats principaux". Ces deux classes partagent beaucoup de mots, ce qui rend la frontière floue pour un modèle basé uniquement sur les mots. Même pour un humain, la séparation n'est pas forcément intuitive.

* La macro-F1 est-elle la meilleure métrique ?  

La Macro-F1 est une métrique très utile ici car elle donne la même importance à chaque classe, peu importe son nombre d'exemples. Comme le jeu de données est déséquilibré (beaucoup plus de plats que d'entrées), l'Accuracy peut être trompeuse en cachant les mauvaises performances sur les petites classes. La Macro-F1 permet donc de vérifier que le modèle est réellement performant sur les trois catégories, et pas seulement sur la plus nombreuse.

* Vos modèles généralisent-ils réellement ?  

Les modèles montrent une capacité de généralisation correcte car les scores sur le jeu de test restent élevés (environ 87-88 % d'Accuracy). L'utilisation de techniques comme le filtrage des mots rares (min_df=5) et la lemmatisation aide à éviter que le modèle n'apprenne les données par cœur. Cependant, la difficulté du modèle à distinguer certaines entrées des plats montre qu'il atteint une limite de généralisation sur les cas les plus ambigus.