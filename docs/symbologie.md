# La symbologie vecteur

Les expressions peuvent être utilisées dans QGIS pour rendre plus dynamique
la symbologie des couches vecteurs:

* calcul via une expression pour **classer les entités** au lieu d'utiliser un champ.
* filtrer quelles entités doivent être rendues par tel symbologie via des filtres
* contrôler toutes les **propriétés** via une expressions (échelle, couleur, taille, etc.)
* **créer des géométries** pour l'affichage (tampons, lignes entre objets)

!!! tip
    Vous pouvez consulter le chapitre (Symbologie vectorielle)[https://docs.3liz.org/formation-qgis/symbologie-vecteur/)
    de notre formation QGIS pour un rappel sur les règles de symbologie dans QGIS.

## La symbologie graduée et catégorisée

Dans l'onglet symbologie, au lieu d'appeler un champ de la couche à représenter,
il est tout à fait possible d'**utiliser une expression**.

Quelques exemples :

* Faire une classification sur une **densité de popuplation** sans avoir besoin
  d'avoir un champ (virtuel ou non): `"POPULATION" / ($area / 1000000)`
* Faire une classification sur l'ensemble des valeurs possibles prises par 2 champs :
  `concat("CATEGORIE", ' - ', "IMPORTANCE")`
* Faire une symbologie graduée en fonction de la compacité des géométries

## La symbologie par ensemble de règles

Ce type de symbologie est très utile si vous souhaitez contrôler quels symboles
sont rendus en fonction des entités, ou d'autres caractéristiques (échelles)

Par exemple, on peut choisir de rendre les polygones des communes comme

* des points à petite échelle
* des polygones à grande échelle

Pour cela, il faut ouvrir les **propriétés de la couche**, dans l'onglet `Symbologie`
et choisir le mode `Ensemble de règles

![](./media/symbologie_ensemble_regles.png)`

## Contrôle les symboles à l'aide d'une expression

Quasiment toutes les propriétés de la symbologie (et des étiquettes)
peuvent être **contrôlées à l'aide d'expressions**.

Il est ainsi possible de choisir la **taille** d'un symbole, la **couleur** d'une bordure,
l'**échelle** d'affichage, le fait que la donnée soit affichée ou non.

!!! danger
    Attention à ne pas utiliser les expressions alors que des moyens
    plus simples et plus performants existent.
    Par exemple pour faire varier une couleur sur 5 classes: utiliser
    la symbologie catégorisée.

## Créer des géométries dynamiques

* Voir l'exemple sur la [Calculatrice](./calculatrice.md) pour la création
  de géométries à partir de champs de la couches
* Sinon, on peut aussi créer dynamiquement des géométries, par exemple
  un tampon de 1km autour de points:

```sql
buffer($geometry, 1000)
```
