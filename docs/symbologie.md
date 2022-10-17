# La symbologie vecteur

Les expressions peuvent être utilisées dans QGIS pour **rendre plus dynamique**
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

![Symbole gradué par densité](./media/symbologie_graduee_expression.png)


## La symbologie par ensemble de règles

Ce type de symbologie est très utile si vous souhaitez contrôler quels symboles
sont rendus en fonction des entités, ou d'autres caractéristiques (échelles)

Par exemple, on peut choisir de rendre les polygones des communes comme

* des **points** à petite échelle : `$scale >= 500000`
* des **polygones** à grande échelle : `$scale < 500000`

Pour cela, il faut ouvrir les **propriétés de la couche**, dans l'onglet `Symbologie`
et choisir le mode `Ensemble de règles

![](./media/symbologie_ensemble_regles.png)`

## Contrôle les symboles à l'aide d'une expression

Quasiment toutes les propriétés de la symbologie (et des étiquettes)
peuvent être **contrôlées à l'aide d'expressions**.

Pour cela, on doit activer le Epsilon ![](./media/mIconExpression.png)
à la place du symbole suivant ![](./media/mIconDataDefine.png)

Il est ainsi possible de choisir

* la **taille** d'un symbole,
* la **couleur** d'une bordure,
* l'**échelle** d'affichage,
* le fait que la donnée soit affichée ou non,
* modifier la symbologie ou les étiquettes des objets sélectionnés :
  ```sql
  CASE
    WHEN is_selected() THEN 'blue'
    ELSE 'red'
  END
  ```
* la rotation des symboles à partir d'un champ
  (Ex: angle de prise de vue d'une photographie)

Un exemple amusant :

![Rotation par expression](./media/rotation_par_expression.gif)


!!! danger
    Attention à ne pas utiliser les expressions alors que des moyens
    plus simples et plus performants existent.
    Par exemple pour faire varier une couleur sur 5 classes: utiliser
    la symbologie catégorisée.

## Créer des géométries dynamiques

Voir l'exemple sur la [Calculatrice](./calculatrice.md) pour la création
de géométries à partir de champs de la couches

Sinon, on peut aussi créer **dynamiquement des géométries**, avec le **générateur de géométries** par exemple :

* Ouvrir le fichier [des Hydrants de Fort-de-France (@ contributeurs OSM)](./media/hydrant_fort_de_france.geojson)
* Ouvrir la symbologie
* Ajouter un niveau de symbole
* Utiliser le mode "Générateur de géométrie"
* Créer un **tampon de 500 mètres** autour de points via :
  ```sql
  buffer($geometry, 1000)
  ```

Configuration :

![Générateur de géométrie (configuration)](./media/generateur_geometrie_expression.png)

Le résultat :

![Générateur de géométrie (résultat)](./media/generateur_geometrie_resultat.png)

!!! tip
    Dans l'expression, on pourrait très bien faire varier la distance tampon
    en fonction de la valeur d'un champ ou d'un calcul

On peut aussi créer des lignes entre objets, avec la fonction `make_line`.
