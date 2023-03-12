# TP - Modification et nettoyage des données

## Introduction

Nous allons utiliser des premières expressions pour :

* convertir du texte en date
* remplir les géométries à partir des coordonnées
* nettoyer les valeurs de certains champs (entier)

## Modifier la géométrie via une expression

On va définir les points via **la longitude et latitude** et une expression :

* On passe la couche `observations` couche en `🖊 Édition`
* On utilise la **calculatrice de champs** ![icône calculatrice](media/icone_calculatrice_de_champ.png) et on modifie la géométrie
  via l'expression

??? note "Créer une géométrie de type Point depuis une longitude et une latitude"

    On peut le faire simplement avec `make_point`

    ```sql
    make_point("longitude", "latitude")
    ```

Dans l'interface de la calculatrice :

* Bien cocher `Mise à jour d'un champ existant`.
* Choisir dans la liste `<geometry>` pour modifier la géométrie.
* Utiliser la bonne expression dans le bloc texte.
* Valider.

 ![image](media/geometrie_make_point.png)

## On visualise alors les points sur la carte

Les géométries sont bien créées et correspondent aux coordonnées.

On **sauvegarde** les données via la disquette ![icône](media/mActionFileSave.png)
de la **barre de numérisation** !

![image](media/carte_donnees_apres_import_avec_geometrie.png)


## On transforme un champ texte en Date

Exemple du champ `date_observation` de type texte, qui contient par exemple
un texte `26/06/2013`.

Ce champ a été détecté par QGIS comme une **chaîne de caractères** (type `QString`). On ne peut donc pas réaliser des **calculs** ou des **tris** par date.

Pour régler ce souci, on peut utiliser les expressions :

* On crée un **nouveau champ** `date_obs` de type `Date` via la
  **calculatrice de champ** ![icône calculatrice](media/icone_calculatrice_de_champ.png) avec :

??? note "Convertir du texte en date"

    ```sql
    -- to_date attend du texte en 1er paramètre
    -- et le format actuel en 2ème paramètre
    to_date("date_observation", 'dd/MM/yyyy')
    ```

* On obtient bien un **nouveau champ** `date_obs` :
  ![width:800](media/exemple_champ_date_obs.png)
* On **supprime** le champ `date_observation`
* On **enregistre** l'édition via la disquette

On pourra donc maintenant faire des **calculs de date**, par exemple
l'âge en jours de l'observation, via :

??? note "Calculer l'âge d'une date en jours"
    ```sql
    -- renverra par exemple 128
    to_int(
      day(age(now(), "date_obs"))
    )
    ```

* Ou tester si la date donnée est bien **inférieure ou égale** à la date du jour

??? note "Tester si une date n'est pas dans le futur"
    ```sql
    -- Renverra Vrai ou Faux (booléen)
    "date_obs" <= now()
    ```
    qui renverra `Vrai` ou `Faux`


## On nettoie les valeurs des champs avec des entiers

Les valeurs **non entières** `NAN`, `NSP`, `Inconnu`, `RAS` ne doivent pas
apparaître dans les champs qui attendent des **entiers**.

On voit dans l'image suivante un extrait de la table attributaire montrant ces valeurs :

![image](media/nettoyer_donnees_non_entieres.png)

On peut appliquer une expression avec la **calculatrice de champ** sur
tous les champs qui doivent contenir des entiers:
`nid_nb_oeuf`, `nid_nb_poussin`, `nid_nb_immature`, `nid_nb_adulte`


??? note "Soit en remplaçant 'NSP' par `NULL`"
    Si on veut seulement remplacer `NSP` par du vide `NULL` :

    * on utilise `trim` pour supprimer les espaces avant et après le texte,
    * on utilise `replace` qui attend 3 paramètre: le texte source, la valeur à rechercher,
      la valeur à remplacer,
    * on utilise `Nullif` qui met `NULL` si la valeur dans le 1er paramètre vaut celle
      du 2ème paramètre.
    ```sql
    -- On remplace 'NSP' par NULL
    -- nullif permet de mettre NULL si la valeur vaut le 2ème paramètre
    Nullif(
        -- replace remplace une valeur par une autre
        replace(
            -- trim permet de supprimer les espaces avant/après
            trim("nid_nb_oeuf"),
            'NSP',
            ''
        ),
        ''
    )
    ```
C'est possible, mais il faut connaître à l'avance toutes les valeurs à nettoyer.

Si on aime les **expressions régulières**, on peut rendre un peu plus générique le chercher/remplacer :

??? note "Soit avec une expression régulière (plus complexe)"
    Pour remplacer tout ce qui n'est pas un entier en valeur `NULL` :

    * Utilisation de `CASE ... WHEN ... THEN ... END` pour faire une condition
    * Utilisation de `trim` pour nettoyer les espaces
    * Utilisation de `regexp_match` pour remplacer par expression régulière
    * Conversion en entier via `to_int`

    ```sql
    -- Une condition avec une expression régulière pour ne conserver que les entiers
    CASE
        WHEN regexp_match(trim("nid_nb_oeuf"), '^\\d+$')
            THEN to_int(trim("nid_nb_oeuf"))
        ELSE NULL
    END
    ```
On doit ouvrir la **calculatrice de champ** et modifier les valeurs pour chacun des
quatre champs `nid_nb_oeuf`, `nid_nb_poussin`, `nid_nb_immature`, `nid_nb_adulte`.

![width:700](media/nettoyer_donnees_non_entieres_boulier.png)

Après le premier passage sur le champ `nid_nb_oeuf` on peut constater que la donnée
a bien été nettoyée :

![width:350](media/nettoyer_donnees_non_entieres_ok.png)

!!! danger "Ne pas oublier d'enregistrer la couche"

    Il faut bien penser à enregistrer les modifications récentes via
    la disquette ![icône](media/mActionFileSave.png)
    puis quitter le mode 🖊 **Édition**


## Modifier le type des quatre champs nettoyés

On doit maintenant utiliser le **Gestionnaire de bases de données** pour changer le type
des champs `nid_nb_xxxx` de **Texte** à **Entier**

On crée d'abord une **connexion** vers le GeoPackage :

* menu `Couche / Gestionnaire des sources de données`
* Onglet `GeoPackage`, bouton `Nouveau`
* Aller chercher le fichier GeoPackage `suivi_donnees_moqueur` et valider.

Ensuite, on ouvre le **Gestionnaire de bases de données** :

* Dans le panneau de gauche, on double-clique sur `GeoPackage`
* Puis sur le nom de la connexion `suivi_donnees_moqueur` qu'on vient de créer
* Sur la table `observations`, on ouvre le menu **Table** > **Modifier une table**,
  et on choisit le type `SMALLINT` ou `INTEGER` pour les **4 champs**
* On enregistre

![width:700](media/modifier_type_en_entier.png)

Ensuite, on doit **recharger la couche** dans QGIS:

* soit on enregistre le projet, on le ferme et on le ré-ouvre
* soit on utilise un clic-droit sur la couche des observations et `Changer la source de données`
  puis on va chercher le fichier `GeoPackage` et la table des observations
