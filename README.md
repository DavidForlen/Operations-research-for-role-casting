# Attribution de rôles assistée par recherche opérationnelle

Le Excel dont il est fait mention dans ce document est situé dans le répertoire `src` de ce dépôt.

## Introduction

### Contraintes de l'attribution de rôles
Ce document explique la démarche suivie pour attribuer les rôles d'une pièce à des comédiens, en respectant des contraintes suivantes :
- chaque rôle doit être attribué à un unique comédien
- un comédien ne peut avoir deux rôles présents dans une même scène
- chaque comédien doit avoir au moins un rôle

### Ce qu'il faut optimiser

Le critère choisi pour mesurer la valeur/score d'une attribution de rôle est la somme pour chaque comédien des points de préférence de tous les rôles attribués à ce comédien.

Ce critère doit être minimisé : le cas parfait étant que chaque comédien ait uniquement le rôle qu'il a mis en premier (avec un score de 1).

L'attribution ne tient pas compte des taille des rôles : certains comédiens peuvent donc se retrouver avec très peu de texte.

### Remarques
Pour des raisons de simplification, certains petites rôles (ici, les élèves, le prof et les choeurs de gardiens) ont été fusionnés en un seul et attribués manuellement en dernier pour équilibrer la charge de travail entre les comédiens.


## 1. Récolte des préférences des comédiens

J'ai récolté avec un Google Form les préférences de chaque comédien pour chaque rôle, en leur demandant d'attribuer pour chaque rôle un score de préférence (de 1 à 6, 1 pour le rôle qu'ils préfèrent puis par ordre de préférence décroissante). J'ai donné la possibilité de donner le score de 1000 à un rôle pour signifier qu'ils ne le veulent absolument pas.

N.B : 1000 a été choisi autant comme nombre exécivement grand que car il est l'objet d'une private joke au sein des clubs où je participe.


J'ai ensuite compilé les réponses dans un tableur Excel (Google Form le fait déjà très bien, il n'y a qu'à copier coller les résultats qu'il donne).

## 2. Détermination des incompatibilités entre les rôles par une matrice d'adjacence

Deux rôles sont considérés comme incompatibles s'ils apparaissent dans la même scène au moins une fois.

La feuille Excel "Nb scènes par rôles" est chargée de générer la matrice d'adjacence des rôles en fonction d'un tableau indiquant pour chaque scène les rôles qui sont présents.

### 3. Modélisation des contraintes

La feuille Excel "Distribution" reprend les comédiens et les met en face d'une attribution de rôle binaire pour chaque rôle : on attribut un rôle à un comédien en mettant un "1" dans la colonne du rôle.

- La colonne "Rôles attribués" est chargée de concaténer tous les rôles attribués pour chaque comédien, pour rendre lisible l'attribution.
- La colonne "CONTRAINTE : les rôles d'un comédien ne doivent pas apparaître dans une même scène" indique le nombre d'incompatibilités entre les rôles attribués pour chacun des acteurs. Chaque case devrait idéalement valoir "0".
- La colonne "Points du rôle choisi" indique pour chaque comédien la somme des points de préférence de tous les rôles attribués à ce comédien. La somme de cette colonne est le critère à minimiser durant l'attribution de rôle.
- La colonne "CONTRAINTE : SOMME DES CHOIX" indique le nombre de rôles attribués pour chaque comédien.
- La ligne "CONTRAINTE : SOMME ROLES" indique pour chaque rôle, combien de comédiens l'ont.

Le tableau indiquant les préférences des comédiens pour les rôles est repris (avec un copié collé manuel) à gauche du tableau d'attribution, pour rendre les calculs aussi linéaires que possible.

#### A noter :
Il est dès lors possible d'attribuer manuellement les rôles des comédiens en vérifiant que les contraintes ne sautent pas (notamment la contrainte d'incompatibilité de rôle).

### 4. Utilisation d'un solveur (non linéaire) pour l'attribution automatique des rôles

Excel dispose d'un solveur permettant de proposer une attribution optimale selon nos contraintes et critère à optimiser. Pour l'activer : https://support.office.com/fr-fr/article/charger-le-compl%C3%A9ment-solver-dans-excel-612926fc-d53b-46b4-872c-e24772f078ca.

 La plus grosse contrainte réside dans la limitation du solveur Excel : il n'accepte d'avoir que 200 cellules à déterminer au maximum dans la version gratuite du solveur. Ce nombre représente pour nous la multiplication du nombre de comédiens par le nombre de rôles.

 Cela impose d'initialiser manuellement l'attribution de rôles manuellement pour diminuer le nombre de rôles à attribuer par le solveur (c'est faisable pour les rôles principaux, que l'on va attribuer aux comédiens qu'on juge les plus aptes à les tenir).

 Pour une raison qui m'échappe, la contrainte d'incompatibilités des rôles rend le problème non linéaire, ce qui nous empêche d'utiliser des solveurs linéaires très efficients (et rapides).

#### Lancer le solveur :

1. Dans l'onglet "Données" de Excel, cliquer sur "Solveur".
2. Si un modèle est déjà chargé, parfait, lancez le avec "Résoudre".
3. Sinon (ou s'il y a un problème avec le modèle préchargé), cliquez sur "Charger/enregistrer", puis rentrez "$C$27:$C$37" dans la plage à sélectionner. Cliquez sur "Charger" et lancez le modèle.

Comme le solveur n'est pas linéaire, la résolution peut être très longue (compter entre 10 minutes et 1 heure selon votre processeur et carte graphique).

### 5. Utiliser ce tableur pour d'autres attributions

Dans la mesure du possible, conservez les colonnes et lignes existantes pour ne pas déranger de formule.
Lorsque vous insérez des colonnes/lignes, faites le toujours au milieu de colonnes/lignes existantes (jamais en début ou fin de tableau, pour que les formules tiennent compte de ce que vous ajouter).

#### Si vous devez ajouter des rôles :
Prenons l'exemple de l'insertion de rôle après le rôle "prof" et avant "élèves parties 1".

- insérez une colonne dans
  - "Nb scènes par rôle" (en AO) par exemple, mettez y le nom du rôle
  - "Distribution" (en AR **et** CI) par exemple, mettez y le nom du rôle
- insérez une ligne dans "Distribution" (en ligne 57 par exemple).
- Pensez à indiquer les préférences de vos comédiens pour ce nouveau rôle, dans la colonne que vous avez inséré dans "Distribution", tout à droite du tableau.

#### Si vous devez ajouter des comédiens :

Prenons l'exemple de l'insertion de comédien entre "David" et "Benjamin" par exemple.

- insérez une ligne dans :
  - "Distribution" (en ligne 11 par exemple), mettez le nom de votre comédien dans la colonne "Comédien".
- Pensez à ajouter les préfences de ce comédien dans la ligne que vous venez d'insérer (tout à droite du tableau).


#### Problèmes communs :

- Si la colonne "CONTRAINTE : les rôles d'un comédien ne doivent pas apparaître dans une même scène" contient un "#VALEUR!" à la suite d'une modification, vérifiez que les attributions binaires des rôles sont toutes remplies (avec un "0" ou un "1").
