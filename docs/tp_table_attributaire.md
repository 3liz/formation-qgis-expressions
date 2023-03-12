# TP - Mise en forme de la table attributaire

Il est possible avec les expressions de fixer des **règles d'affichage**
des **cellules** et des **lignes** de la table attributaire.

On peut par exemple vérifier que certaines valeurs sont bien saisies.

??? note "Montrer en rouge les observations sans espèce support"

    Pour penser à retourner sur le terrain et ajouter l'information
    ```sql
    "id_espece" IS NULL
    ```

??? note "Montrer en grisé les observations sans individus observés"

    Il faut faire la somme des valeurs de tous les champs d'effectif
    ```sql
    -- Vrai si la somme des champs d'effectif vaut zéro
    -- Coalesce permet de mettre 0 lorsque le champ est vide
    Coalesce("nid_nb_oeuf", 0") + Coalesce("nid_nb_poussin", 0)
    + Coalesce("nid_nb_immature", 0) + Coalesce("nid_nb_adulte", 0) = 0
    ```

??? note "Montrer en vert les observations de l'année"

    Pour voir d'un seul coup les observations récentes
    ```sql
    -- observations récentes
    -- c'est-à-dire celle dont l'âge est de moins de 100 jours
    year(now()) = year("date_obs")
    ```

![table attributaire](./media/table_attributaire_mise_forme_conditionnelle.png)
