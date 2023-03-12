# TP - Outil de saisie grâce aux expressions

## Introduction

L'idée de ce TP est d'illustrer comment utiliser les **expressions** dans QGIS
pour créer un **outil de saisie** complet, à partir d'une donnée au format tabulaire,
avec les étapes suivantes :

* **Import** d'une donnée "tableur" dans un GeoPackage et affichage dans QGIS
* Création et correction de données via des **expressions**
* Mettre les données en **relation**
* Créer un **formulaire de saisie** avancé
* Visualiser les données problématiques ou importantes dans la **table attributaire**

On privilégie dans cet exemple l'utilisation d'**expressions** dans QGIS
pour la majorité des traitements :
pas d'utilisation de requêtes SQL en base de données ou d'algorithmes complexes.

## L'exemple choisi

* On fait des **observations d'oiseaux** sur le terrain :
  le **Moqueur Gorge Blanche** à la Martinique
* La saisie est historiquement faite **dans un tableur** (MS Excel ou LibreOffice Calc)
* Une **observation** est :
    * un **point** avec une `longitude` et une `latitude`,
    * faite à une **date**,
    * par des **observateurs**
    * on doit respecter une certaine **nomenclature** pour certains champs,
    * on souhaite connaître la **commune** sur laquelle est faite l'observation

![Moqueur Gorge Blanche](media/moqueur_gorge_blanche.png)
