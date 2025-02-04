# Data analysis : Children and Autism

### *AVANTURIER Aurélie, PUECH Lilian*

## Introduction

Aux États-Unis, vous payez tous vos soins à plein pots. Cela concerne également
la prise en charge des enfants atteints d'autisme, peu importe la méthode
d'accompagnement choisie. Différentes méthodes existent mais elles ne sont pas
toutes efficaces de la même manière, selon l'âge, le sexe, l'ethnicité (car oui,
aux États-Unis d'Amérique, cela compte) ou encore l'entourage dans
lequel l'enfant grandit (élevé.e par ses parents, un seul parent, des tuteurs,
oncle, tante, etc).

Ce serait utile si nous pouvions obtenir un arbre de classification supervisée
à partir d'un ensemble de données recueillies spéciquement dans ce besoin de
rechercher la méthode la plus efficace... Et c'est ce que nous avons !

## Lecture et analyse descriptive des données

On peut importer les doonées en R en téléchargeant le fichier ARFF depuis la
base de données en ligne et la charger via la commande ``X<-read.arff("z.arff")``.

Lorsqu'on observe les doonées via ``summary(X)``, on s'aperçoit qu'il y a beaucoup
de disparité parmi les individus, qu'il y a "de tout". Pour analyser descriptivement
les données, on peut utiliser les commandes suivantes:

1. ``summary(X)``: donne en particulier le 1er quartile, 3ème quartile, min, max,
moyenne et médiane de chaque variable numérique et pour les variables nominales,
le taux de chaque valeur (combien d'européens, combien d'asiatiques, combien de noirs,
combien de blancs européens, etc, pour la variable 'ethnicity' par exemple).
2. ``var(X)``: donne les covariances entre chaque variable.

## Classification non supervisée

L'objectif de cette partie est de déterminer le nombre de clusters idéal pour
ce jeu de données.

```r
coudekmeans <- function(data,min,max) {
    if (min >= max) {
        res <- kmeans(data,max)
        return(1.0 - res$betweenss / res$totss)
    }
    else {
        res <- kmeans(data,max)
        return(c(coudekmeans(data,min,max-1), 1.0 - res$betweenss / res$totss))
    }
}
```

```r
mck <- function(data,min,max,n) {
  result <- list()
  for (i in seq(1,n)) {
    result <- append(result, list(c(coudekmeans(data,min,max))))
  }
  averages <- c()
  for (k in seq(1,max-min+1)) {
    average <- 0.0
    for (i in seq(1,n)) {
      average <- average + result[[i]][k] / n
    }
    averages <- c(averages, average)
  }
  return(append(result, list(averages)))
}
```

```r
mckplot <- function(res,min,max,n) {
  plot(x=seq(min,max), kmeanres[[1]], type="p", col="gray", main="Fonction du coude de KMeans", xlab="k", ylab="loss")
  for(i in seq(2,n)) {
    lines(x=seq(min,max), kmeanres[[i]], col="gray", type="p")
  }
  lines(x=seq(min,max), kmeanres[[n+1]], col="green", type="l")
}
```
