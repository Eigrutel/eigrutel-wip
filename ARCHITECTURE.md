# Architecture — WIP

## Vue d’ensemble

WIP est une application web autonome contenue dans un unique fichier `wip.html`.

Aucun framework, serveur ou système de compilation n’est nécessaire pour l’exécuter. Le fichier HTML contient directement :

- la structure de l’interface ;
- les styles CSS ;
- les traductions français / anglais ;
- la logique JavaScript ;
- le calculateur de travail.

Cette architecture permet de télécharger le fichier et de l’utiliser localement dans un navigateur moderne.

## Organisation générale

```text
wip.html
├── en-tête et métadonnées
├── styles CSS
├── structure HTML de l’interface
│   ├── en-tête du projet
│   ├── menu principal
│   ├── panneau des états
│   ├── tableau des pages
│   ├── calculateur de travail
│   ├── fiche page
│   └── mini aide
└── JavaScript
    ├── métadonnées de l’application
    ├── états par défaut
    ├── données du projet
    ├── traductions FR / EN
    ├── persistance locale
    ├── rendu des états
    ├── rendu des pages
    ├── fiche page
    ├── import / export JSON
    ├── calculateur
    ├── reset et restauration
    └── événements
```

## Métadonnées de l’application

Les informations de version sont regroupées dans `APP_META`.

Version stable :

- nom : `WIP`
- version : `1.0.0`
- date : `10-08-2026`

## État principal

L’état courant du projet est conservé dans l’objet `data`.

Il contient notamment :

- `title` : titre du projet ;
- `pageCount` : nombre total de pages ;
- `deadline` : échéance du projet ;
- `activeState` : état actuellement sélectionné ;
- `states` : liste des états ;
- `pages` : données des pages ;
- `hideDone` : masquage des pages terminées ;
- `editMode` : mode de modification des états ;
- `sortByState` : tri par avancement ;
- `lang` : langue de l’interface.

## États

Chaque état contient notamment :

- `id` : identifiant stable ;
- `label` : intitulé ;
- `color` : couleur.

Les états fournis par défaut sont définis dans `defaultStates`.

### États protégés

Deux identifiants jouent un rôle structurel :

- `non-commence` : état de repli utilisé notamment lorsqu’un état est supprimé ;
- `termine` : état final utilisé pour les calculs de progression et de travail restant.

L’état `termine` est automatiquement conservé et replacé en dernière position.

Les états intermédiaires peuvent être :

- renommés ;
- recolorés ;
- ajoutés ;
- supprimés ;
- déplacés vers le haut ou vers le bas.

Lorsqu’un état est supprimé, les pages qui l’utilisaient reviennent à `non-commence`.

## Pages

Une page contient notamment :

- `number` : numéro de page ;
- `state` : identifiant de l’état ;
- `note` : texte libre ;
- `date` : date éventuelle ;
- `panels` : nombre de cases.

Les pages sont conservées lors d’un changement du nombre total de pages lorsque leur numéro existe encore.

## Fiche page

La fiche d’une page permet de modifier :

- la date ;
- le nombre de cases ;
- les précisions / notes.

Les URL présentes dans la note sont détectées et proposées comme liens cliquables.

## Progression

La progression générale repose exclusivement sur l’état d’identifiant `termine`.

Le pourcentage est calculé selon :

```text
pages terminées / nombre total de pages
```

Cette logique ne dépend donc pas de l’ordre visuel des autres états.

## Calculateur de travail

Le calculateur dispose de deux modes.

### Pages

Le travail restant correspond à :

```text
nombre total de pages − pages à l’état termine
```

### Cases

Le travail restant correspond à la somme des valeurs `panels` des pages qui ne sont pas à l’état `termine`.

Le calculateur prend également en compte :

- la date courante ;
- la deadline ;
- les jours de semaine sélectionnés comme jours travaillés.

Il détermine ensuite le nombre de jours de travail disponibles et la quantité moyenne à produire par jour travaillé.

## Langues

Les textes d’interface français et anglais sont centralisés dans `I18N`.

Les intitulés des états fournis par défaut disposent également d’une correspondance bilingue dans `DEFAULT_STATE_LABELS`.

Les états créés ou renommés par l’utilisateur ne sont pas traduits automatiquement.

La langue choisie est enregistrée avec les données du projet.

## Stockage local

WIP utilise `localStorage`.

Clé principale de la version 1.0.0 :

```text
eigrutel_wip_v1
```

Le programme peut également lire plusieurs anciennes clés de développement afin de préserver les données créées avant la version publique.

Le calculateur dispose de sa propre clé de préférences :

```text
eigrutel_wip_planner_v1
```

## Import / export JSON

L’export JSON sérialise l’objet principal `data`.

Le fichier téléchargé utilise la forme :

```text
wip_nom-du-projet.json
```

L’import vérifie notamment la présence des tableaux `states` et `pages`.

Après import, la structure des états protégés est vérifiée afin de garantir la présence de `non-commence` et `termine`.

## Remises à zéro

Trois niveaux sont disponibles :

### Pages à zéro

Remet toutes les pages à `non-commence` et efface pour chaque page :

- note ;
- date ;
- nombre de cases.

### Rétablir les états par défaut

Restaure les états et couleurs d’origine tout en conservant les données compatibles du projet.

### Tout remettre à zéro

Recrée un projet vierge et supprime les données locales correspondantes.

## Affichage

Le tableau utilise une grille CSS responsive.

Chaque vignette représente une page et affiche :

- son numéro ;
- sa couleur d’état ;
- un indicateur de note ;
- le nombre de cases lorsqu’il est renseigné.

Sur petit écran, la mise en page passe en colonne et adapte la largeur des vignettes et commandes.

## Impression

Une feuille de style `@media print` simplifie l’interface pour l’impression :

- boutons masqués ;
- panneau latéral retiré ;
- fond blanc ;
- tableau conservé.

## Dépendances

Aucune bibliothèque JavaScript externe n’est requise.

WIP est conçu pour rester exploitable comme fichier HTML autonome.

## Fichiers du dépôt

- `wip.html` — application ;
- `index.html` — page de présentation GitHub Pages ;
- `README.md` — présentation et utilisation ;
- `NOTICE.md` — auteur, licences et marques ;
- `LICENSE.md` — répartition des licences ;
- `CHANGELOG.md` — historique des versions ;
- `ARCHITECTURE.md` — présent document ;
- `docs/` — captures et documentation éventuelle ;
- `favicon/favwip.png` — favicon.
