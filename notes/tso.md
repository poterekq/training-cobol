# TSO

## Rappels 

PDS = *library* = partitioned data set.

![[ispf_pds.png]]

Un cylindre contient 15 pistes ou tracks.

On peut restreindre le traitement à certaines lignes grâce aux labels. On peut restreindre le traitement à certaines colonnes grâce aux bounds.

## Introduction

**TSO** signifie *Time Sharing Option*. C'est le sous-système qui permet de partager des ressources entre utilisateurs. Plus précisément, c'est un interpréteur de lignes de commandes interagissant avec l'OS des grands systèmes IBM. Sa version actuelle correspond à TSO/E, soit TSO Extensions. Il peut être interfacé avec d'autres outils, à l'aide d'ISPF par exemple. Ces outils sont par exemple RACF, DFDSS, HSM, PDF, QMF, SMP... Ils sont présentés sur la figure suivante.

![[mainframe_software.png]]

**ISPF** signifie *Interactive System Productivity Facility*. C'est un gestionnaire de dialogue destiné à améliorer l'ergonomie de manipulation du système TSO. En somme, il permet la communication entre un écran géré par TSO, et un programme donné (ISPF/PDF, COBOL, etc.). ISPF fournit également des services pour l'envoi et la lecture d'écrans, la gestion de variables et de tables, le contrôle en cas d'erreur, etc. Sa structure en menus est présentée sur la figure suivante.

![[ispf_structure.gif]]

**ISPF/PDF** signifie *ISPF Program Development Facility*. C'est l'IDE mis à disposition dans les systèmes z/OS.

## Le menu principal

Pour se connecter à TSO, on utilise `LOGON API1`. Pour se déconnecter de TSO, on utilise `LOGOFF`.

Une fois arrivé dans TSO, on peut faire taper la touche `ENTREE` pour accéder au menu ISPF, qui correspond à l'un des outils fournis par TSO. 

![[ispf_menu_principal.png]]

Ce menu s'organise en plusieurs panneaux numérotés de 0 à 11. On accède aux autres panneaux avec `M`, pour *more*. La touche `F3` permet de revenir au panneau précédent. La touche `X` permet de quitter ISPF pour revenir à TSO. Pour revenir à ISPF, il suffit de taper la commande `ISPF`.

Le **menu principal** est aussi appelé *Primary Option Menu*. Plusieurs panneaux ou options sont disponibles. Attention, pour que les options soient sauvegardées, il faut entrer la commande `X` dans le menu principal pour tuer le processus ISPF, puis `LOGOFF` lorsqu'on est sur TSO. 

Il est conseillé d'arrêter la session TSO tous les soirs pour être certain que tout soit bien sauvegardé sur le système !

La commande `PFSHOW ON` permet d'afficher en bas de l'écran les touches de fonction disponibles. On peut les désactiver avec `PFSHOW OFF`.

La commande `REFRESH` permet de rafraîchir l'affichage.

Pour accéder automatiquement à une option sans avoir à naviguer les menus intermédiaires, on peut utiliser les commandes `=3.4` ou `START 3.4`, qui nous envoient à l'option 4 de l'option 3.

## Option 0 — Les paramètres de la session

L'**option 0** du menu principal permet de gérer les **paramètres de la session**.

![[ispf_option0.png]]

C'est important de noter qu'avec TSO, un certain nombre d'informations inutiles sont affichés dans le `STDOUT`. Pour cacher ces informations, on peut se rendre sous l'option `1. Log Data set defaults` du sous-menu `Log/List`, et fixer `Process option` à `2`. On peut ensuite assigner la valeur `H` au `Batch SYSOUT` pour éviter que les sorties partent sur une imprimante. Ces mêmes valeurs sont à utiliser pour les options dédiées aux jeux de données. 

![[loglist_logdata.png]]

L'option `4.JCL...` permet de définir un JCL par défaut à utiliser lorsqu'on n'a pas fourni de JCL spécifique pour l'exécution d'un job. On renseigne les paramètres suivants : `//api1a job class=h,msgclass=h,notify=&sysuid,time=(,1)`. L'attribut `time` permet d'accorder un maximum de 1 seconde de temps CPU à un job, temps au-delà duquel il est interrompu.

![[loglist_jcl.png]]

Les *function keys* sont accessibles et modifiables avec la commande `KEYS` ou grâce à un sous-menu de l'option 0. La touche `F9` donne accès à plusieurs sessions et permet de passer de l'une à l'autre. Pour `F12`, il est conseillé de remplacer `CANCEL` par `RETRIEVE` pour redonner à l'utilisateur la commande qu'il avait passée plus tôt.

![[ispf_keys.png]]

### Les touches de fonction

Les **touches de fonction** donnent accès à un certain nombre d'outils pour simplifier l'utilisation de TSO, ISPF et les sous-systèmes associés. Ces touches de fonction vont généralement de `F1` ou `PF1` à `F12` ou `PF12`. Avec le modificateur `ALT`, il est possible d'activer un mode étendu permettant d'utiliser jusqu'à 24 fonctions.

Il est conseillé de les configurer de la façon suivante :

| Key | Fonction | Description                                                |
| --- | -------- | ---------------------------------------------------------- |
| F1  | HELP     | Affiche l'aide.                                            |
| F2  | SPLIT    | Crée un nouvel écran suivant la position du curseur.       |
| F3  | END      | Met fin à l'opération actuelle et ferme le panneau actuel. |
| F7  | BACKWARD | Défile en arrière.                                         |
| F8  | FORWARD  | Défile en avant.                                           |
| F9  | SWAP     | Passe d'un écran logique à un autre.                       |
| F10 | ACTIONS  | Place le curseur dans la barre des menus.                  |
| F12 | RETRIEVE | Récupère la dernière commande primaire utilisée.           | 

Un léger supplément concernant `SPLIT` et `SWAP`... La commande `SWAP LIST` permet d'afficher l'ensemble des écrans logiques qui ont été créés. Ils sont au nombre de 8 au maximum. Pour accéder à un écran particulier, il suffit de renseigner son numéro dans la ligne de commande primaire, puis de taper `F9` pour réaliser un `SWAP`. Pour attribuer une fenêtre entière à chaque écran, il suffit de réaliser le partage en ayant placé le curseur au sommet à gauche. La commande `=X` utilisée sur un écran donné permet de le supprimer.

En plus des touches de fonction standards, d'autres sont disponibles directement via la fenêtre de visualisation du terminal de z/OS. Ce sont :
- **PA1**. Lorsque cette touche est pressée une première fois, elle rafraîchit l'écran. Une deuxième pression entraîne l'arrêt de l'exécution des processus dans la session TSO. Elle s'utilise lorsqu'une commande ne parvient pas à se terminer.
- **PA2**, qui permet de rafraîchir l'écran jusqu'à l'état datant de la dernière utilisation de la touche `ENTREE`.
- **ENT**, qui correspond à `ENTREE`.
- **CLR**, qui permet de nettoyer le terminal. Elle s'utilise uniquement sur CICS.

![[ispf_addkeys.png]]

Les deux options qui suivent sont la consultation et l'édition. On édite `EDIT` uniquement les fichiers dont on est prioritaire. Si les fichiers appartiennent à un autre utilisateur, il faut les manipuler en mode de consultation `VIEW`.

## Option 1 — La consultation

La **consultation** correspond à deux procédés :
- `BROWSE`, qui permet de voir un fichier sans aucune possibilité de le modifier.
- `VIEW`, qui octroie la possibilité de visualiser un fichier et de l'éditer. Il est seulement possible de sauvegarder vers un nouveau fichier, mais il est interdit d'en écraser un existant.

Si une opération a été réalisée en consultation, la commande `CANCEL` permet de l'annuler.

### Répétition d'action

Les **touches de répétition d'action** sont assignées pour l'éditeur uniquement. Ce sont :
- La touche `F5`, utilisée pour effectuer une recherche. Si le premier item découvert ne nous intéresse pas, on peut continuer à taper `F5` jusqu'à atteindre l'item d'intérêt.
- La touche `F6`, utilisée pour effectuer une modification. On peut taper `F6` pour réaliser plusieurs fois le changement.

### Vues sur listes ou sources

Lors de la consultation d'un document, les touches `F7`, `F8`, `F10` et `F11` sont utilisées pour **visualiser** le haut, le bas, la gauche et la droite d'une fenêtre respectivement, lorsque celle-ci est trop large pour être affichée. C'est l'équivalent des barres de défilement.

Par ailleurs, la commande `M` suivie de `F8` amène au bas du fichier. Avec la commande `M` et `F7`, c'est le sommet du fichier qui est ciblé. Le fonctionnement est similaire avec les touches `F10` et `F11`.

### Les modalités de défilement
Les **options de défilement** dans PDF sont renseignées par `Scroll`. Ce sont les suivantes :
- `CSR`, pour défiler selon la position actuelle curseur.
- `PAGE`, pour défiler d'une portion de page à une autre.
- `HALF`, pour défiler d'une moitié de page.
- `DATA`, pour défiler d'une page moins une ligne, ou moins une colonne.
- Un nombre, 6 par exemple, pour défiler de 6 lignes en 6 lignes. Cela vaut également pour les colonnes.

![[scroll_option.png]]

### Consulter des jeux de données

PDF permet de **visualiser** des jeux de données (1) partitionnées et (2) séquentielles.

![[visualiser_ds.png]]

La visualisation d'un jeu de données partitionnées (1) nécessite de renseigner les qualificateurs de la *library* dont on souhaite consulter les membres. Pour accéder à un membre en particulier, il est nécessaire de préciser son nom. Autrement, le répertoire et son contenu sont présentés.

![[edit_dataset_name.png]]

Pour le jeu de données séquentielles (2), il suffit de renseigner son nom et éventuellement le label du volume de stockage.

Pour accéder à un jeu de données dont le propriétaire est un autre utilisateur, il faut renseigner son nom entre apostrophes `'API10.SOURCE.TSO'` et penser à préciser le `USERID`, ici `API10`.

![[access_other_user_ds.png]]

## Option 2 — L'édition

L'**éditeur** est accessible grâce à l'option 2 du menu principal. Au moment de l'**édition d'un fichier**, les mêmes commandes que celles proposées à la consultation sont disponibles. Cette fois-ci cependant, il est possible d'écraser son contenu après modification.

De la même manière que pour la consultation, il faut renseigner le jeu de données à éditer.

TSO produit un certain nombre de métadonnées pour les fichiers lorsqu'ils sont accédés. La touche `F11` affiche davantage d'informations. À chaque sauvegarde, la métadonnée `VV MM` est mise à jour et représente la version actuelle du fichier. Cette fonctionnalité peut être utilisée pour faciliter le versionnage.

![[fichier_metadonnees.png]]

Lors de l'édition d'un membre, deux grands types de commandes sont disponibles :
1) Les **commandes primaires**, ou *primary commands*, qui s'utilisent dans l'en-tête du terminal et s'appliquent à tout le document.
2) Les **commandes de ligne**, qui s'appliquent à une ligne, à une plage de lignes, ou à un bloc de lignes.

![[types_commandes.png]]

Ces deux types de commandes peuvent être combinés.

### Les divers de l'édition

En accédant à un fichier avec `VIEW` ou `EDIT`, la combinaison `CTRL + END` efface tous les caractères d'une ligne, situés après le curseur.

On peut se placer sur une ligne et utiliser la commande `COLUMNS` ou `COLS` pour afficher les numéros de colonne. C'est purement visuel et permet de déterminer la position chaque caractère.

### Les commandes de ligne

Les **commandes de ligne** sont déclarées dans le panel gauche de l'éditeur, où sont renseignés les numéros de lignes. Ces commandes portent sur :
- Une **ligne seule**. La commande est représentée par un ou plusieurs caractères. C'est par exemple `I`.
- Une **plage de lignes**. La commande est représentée par un ou plusieurs caractères, suivis d'un nombre entier qui définit la portée de la plage. C'est par exemple `I3`, qui concerne une plage de 3 enregistrements, à partir de la ligne sur laquelle est écrite la commande.
- Un **bloc de lignes**. La commande est représentée par un ou plusieurs caractères, dont le dernier est répété une fois. La commande en question est déclarée à deux reprises, la première au début du bloc et la seconde à la fin de celui-ci. L'une des deux déclarations peut être suivie d'un nombre entier qui constitue alors un modificateur. C'est par exemple `RR...RR3`, qui permet de répéter un bloc 3 fois.

| Commande                         | Opération                                                                       |
| -------------------------------- | ------------------------------------------------------------------------------- |
| `I`, `In`                        | **Insère** 1 ligne, $n$ lignes.                                                 |
| `D`, `Dn`, `DD...DD`             | **Détruit** 1 ligne, $n$ lignes, un bloc de lignes.                             |
| `R`, `Rn`, `RR...RR`, `RR...RRn` | **Répète** 1 ligne, $n$ lignes, un bloc de lignes, un bloc de lignes $n$ fois.  |
| `M`, `Mn`, `MM...MM`             | **Déplace** 1 ligne, $n$ lignes, un bloc de lignes.                             |
| `C`, `Cn`, `CC...CC`             | **Copie** 1 ligne, $n$ lignes, un bloc de lignes.                               |
| `X`, `Xn`, `XX...XX`             | **Exclut** de l'affichage 1 ligne, $n$ lignes, un bloc de lignes.               |
| `S`, `Sn`                        | **Réintègre** à l'affichage 1 ligne, $n$ lignes.                                |
| `F`, `Fn`                        | **Réintègre** à l'affichage la première ligne exclue, les $n$ premières lignes. |
| `L`, `Ln`                        | **Réintègre** à l'affichage la dernière ligne exclue, les $n$ dernières lignes. |

L'exclusion de lignes avec la commande `X`n'est que temporaire et constitue une forme de formatage qui peut être annulée avec la commande primaire `RESET`. Les exclusions peuvent également être inversées avec la commande primaire `FLIP`. Attention, la commande `S` réintègre une ou plusieurs lignes compte-tenu de leur importance. Ce critère est défini par le degré d'indentation des enregistrements.

Les clauses `M` et `C`, utilisées pour le déplacement et la copie respectivement, peuvent être complétées par des commandes de ligne qui indiquent où réaliser l'opération.

| Commande             | Opération                                                                            |
| -------------------- | ------------------------------------------------------------------------------------ |
| `A`, `An`            | Déplace ou copie **après** cette ligne 1 fois, $n$ fois.                             |
| `B`, `Bn`            | Déplace ou copie **avant** cette ligne 1 fois, $n$ fois.                             |
| `O`, `On`, `OO...OO` | **Remplace** les vides de la ligne par les caractères de la source du `M` ou du `C`. | 

![[edit_move.png]]

Dans la capture d'écran ci-dessus, la ligne 1 est marquée par `M`. Elle sera déplacée après la ligne 7 et copiée 3 fois, car la destination est marquée par `A3`

L'opérateur d'*overlay* `O` permet de réaliser l'opération représentée sur les figures ci-dessous. On utilise la commande `M` ou `C`sur la ligne dont il faut reprendre le contenu, et `O` sur celle pour laquelle on souhaite superposer le contenu de l'origine.

| Avant *overlay*              | Après *overlay*             |
| ---------------------------- | --------------------------- |
| ![[edit_overlay_before.png]] | ![[edit_overlay_after.png]] |

Le contenu d'une ou plusieurs lignes peut également être **déplacé en colonnes**. Il existe pour cela des **opérateurs de décalage**, **avec ou sans protection**. Sans protection, ces opérateurs sont représentés par des parenthèses `( )` et n'empêchent pas de supprimer de la donnée par erreur lorsque l'enregistrement dépasse la plage des colonnes autorisées. Les opérateurs avec protection sont représentés par des chevrons `< >`. Ils empêchent de réaliser un décalage qui entraînerait la suppression de données, et entraînent l'affichage d'une erreur de ligne `==ERR##`.

| Commande                         | Opération                                                                                                              |
| -------------------------------- | ---------------------------------------------------------------------------------------------------------------------- |
| `(`, `(n`, `((...((`, `((...((n` | **Décale** une ligne vers la **gauche** de 1 colonne, $n$ colonnes, un bloc de 1 colonne, de $n$ colonnes.             |
| `)`, `)n`, `))...))`, `))...))n` | **Décale** une ligne vers la **droite** de 1 colonne, $n$ colonnes, un bloc de 1 colonne, de $n$ colonnes.             | 
| `<`, `<n`, `<<...<<`, `<<...<<n` | **Décale en sécurité** une ligne vers la **gauche** de 1 colonne, $n$ colonnes, un bloc de 1 colonne, de $n$ colonnes. |
| `>`, `>n`, `>>...>>`, `>>...>>n` | **Décale en sécurité** une ligne vers la **droite** de 1 colonne, $n$ colonnes, un bloc de 1 colonne, de $n$ colonnes. |

Des commandes de ligne sont également disponibles pour **modifier la casse des caractères**.

| Commande                 | Opération                                                                         |
| ------------------------ | --------------------------------------------------------------------------------- |
| `UC`, `UCn`, `UCC...UCC` | Met les caractères en **majuscules** pour 1 ligne, $n$ lignes, un bloc de lignes. |
| `LC`, `LCn`, `LCC...LCC` | Met les caractères en **minuscules** pour 1 ligne, $n$ lignes, un bloc de lignes. | 

Enfin, il existe une commande pour **assigner un label à une ligne** ; celle-ci doit commencer par un `.` et être suivie d'un ou plusieurs caractères alphabétiques. C'est par exemple `.A`. Ces labels peuvent être effacés un par un, ou dans leur totalité avec `RES LABEL`.

![[label_ligne.png]]

Les première et dernière lignes d'un fichier possèdent des **labels implicites**, à savoir `.ZFIRST` et `.ZLAST` respectivement, abrégés par `.ZF` et `.ZL`. Le label `.ZCSR` correspond au curseur.

### Les commandes primaires

Les **commandes primaires** sont renseignées dans l'en-tête du terminal, et plus précisément dans l'espace qui leur est dédié à droite de l'indicateur `Command ===>`.

Par ailleurs, on peut chaîner plusieurs commandes primaires en les séparant par un `;`.

L'ensemble des commandes primaires étudiées sont renseignées dans le tableau ci-dessous.

| Commande  | Alternative(s)               | Opération                                                                                |
| --------- | ---------------------------- | ---------------------------------------------------------------------------------------- |
| `FIND`    | `F`                          | **Recherche** une donnée dans le fichier.                                                |
| `CHANGE`  | `CHG`, `CHA`, `C`            | **Remplace** une donnée `d1` par une donnée `d2`.                                        |
| `EXCLUDE` | `EXCLUDED`, `EXC`, `EX`, `X` | **Exclut** de la vitalisation les lignes qui possèdent la donnée `d`.                    |
| `COPY`    |                              | **Copie** le contenu d'un autre membre dans le fichier actuel. La source est conservée.  |
| `MOVE`    |                              | **Déplace** le contenu d'un autre membre dans le fichier actuel. La source est détruite. |
| `CREATE`  |                              | **Crée et remplit** un fichier.                                                          |
| `REPLACE` |                              | **Remplace** un fichier existant.                                                        |
| `DELETE`  | `DEL`                        | **Supprime** des lignes d'un fichier.                                                    |
| `LOCATE`  | `LOC`, `L`                   | **Positionne** une ligne donnée au sommet de la vue.                                     |
| `SAVE`    |                              | **Sauvegarde** le travail.                                                               |
| `CANCEL`  | `CAN`                        | **Abandonne** le travail.                                                                |
| `EDIT`    |                              | **Édite** un membre du PDS.                                                              |
| `SUBMIT`  | `SUB`                        | **Soumet** un job.                                                                       |
| `PROFILE` | `PROF`, `PRO`, `PR`          | **Affichage** les caractéristiques du profil d'édition.                                                                                         |

Les commandes de recherche `FIND`, de remplacement `CHANGE` et d'exclusion `EXCLUDE` possèdent un certain nombre de **paramètres** en commun, à savoir :

| Paramètre                                            | Fonction                                                                          |
| ---------------------------------------------------- | --------------------------------------------------------------------------------- |
| `string`                                             | La chaîne à évaluer, qu'elle soit une source ou une destination.                  |
| `.labela`, `.labelb`                                 | Les labels entre lesquelles l'opération est réalisée.                             |
| `NEXT` par défaut, ou `ALL`, `FIRST`, `LAST`, `PREV` | La chaîne sur laquelle démarrer l'opération, par rapport au curseur ou à la page. |
| `CHARS` par défaut, ou `PREFIX`, `SUFFIX`, `WORD`    | La position de la chaîne dans le contexte précisé par le paramètre.               |
| `X` ou `NX`                                          | L'opération s'effectue sur les lignes exclues, non exclues.                       |
| `col1`, `*col2`                                      | La colonne à laquelle démarrer l'opération, arrêter l'opération (optionnelle).    |

La chaîne à évaluer `string` peut être ou non placée entre apostrophes `'` ; c'est cependant préférable de le faire pour éviter tout problème avec des espaces par exemple. Lorsque la chaîne contient elle-même une apostrophe, il faut alors la placer entre *double quotes* `''`, par exemple `''L'AVION''`. De plus, la chaîne peut être précédée d'un **indicateur de contexte** :
- La lettre `X` tel que `X'...'`, lorsque la chaîne est **hexadécimale**.
- La lettre `T` tel que `T'...'`, lorsque la chaîne est du **texte**.
- La lettre `P` tel que `P'...'`, lorsqu'on souhaite utiliser un analogue aux expressions régulières, et donc des **caractères spéciaux**.

Il existe des **caractères spéciaux** à disposition, combinables si besoin, pour simuler partiellement les **expressions régulières**. Ce sont :
- Le caractère `=` tel que `P'='`, pour un caractère quelconque.
- Le caractère `¬` tel que `P'¬'`, pour un caractère non blanc.
- Le caractère `.` tel que `P'.'`, pour un caractère non affichable.
- Le caractère `#` tel que `P'#'`, pour un caractère numérique.
- Le caractère `-` tel que `P'-'`, pour un caractère non numérique.
- Le caractère `@` tel que `P'@'`, pour un caractère alphabétique.
- Le caractère `<` tel que `P'<'`, pour un caractère en minuscule.
- Le caractère `>` tel que `P'>'`, pour un caractère en majuscule.
- Le caractère `$` tel que `P'='`, pour un caractère non alphanumérique.

#### Rechercher une donnée

La commande `FIND` localise une ou plusieurs d'occurrences d'une chaîne de recherche dans le fichier.

![[syntax_find.png]]

#### Remplacer une donnée

La commande `CHANGE` remplacer une chaîne par une autre dans le fichier.

![[syntax_change.png]]

#### Exclure de l'affichage

La commande `EXCLUDE` exclut de l'affichage l'ensemble des lignes qui possèdent la chaîne recherchée. L'exclusion peut être inversée avec `FLIP`, ou annulée avec `RESET`.

![[syntax_exclude.png]]

#### Transférer des lignes entre fichiers

Les commandes primaires utilisées pour **transférer** des lignes entre fichiers doivent être combinées à des commandes de ligne.

Les commandes primaires `COPY` et `MOVE` sont utilisées pour alimenter le fichier courant (cible) à partir du contenu d'une source. Elles sont combinables avec les commandes de ligne `A` et `B` pour marquer la position où placer les éléments.

La commande `COPY` permet d'alimenter le fichier courant tout en conservant la source.

![[syntax_copy.png]]

Dans l'exemple suivant, les lignes 2 à 5 du fichier `TEST01` sont copiées après la ligne marquée par `A` dans le fichier `TEST03`.

![[copy_content.png]] 

Un autre exemple consisterait à utiliser la commande `COPY TEST03 AFTER .ZLAST` pour copier le contenu du membre `TEST03` après la fin du fichier actuellement édité. On peut préciser quelles lignes on souhaite copier vers la destination, avec `COPY TEST03 AFTER .ZLAST 1 5` pour les 5 premières lignes.

La commande `MOVE` permet d'alimenter le fichier courant tout en détruisant la source.

![[syntax_move.png]]

Les commandes primaires `CREATE` et `REPLACE` sont utilisées pour alimenter un fichier cible à partir du contenu du fichier courant (source). Elles sont combinables avec les commandes de ligne `C` et `M` pour marquer la position des éléments à placer dans la cible.

La commande `CREATE` crée un nouveau fichier et le remplit.

![[syntax_create.png]]

Dans l'exemple ci-dessous, un fichier `TEST03` est créé à partir d'un bloc de lignes marqué par la commande `CC...CC` et issu du membre `TEST01`.

![[create_file.png]]

La commande `REPLACE` écrase le contenu d'un fichier existant.

![[syntax_replace.png]]

#### Détruire des lignes

La commande `DELETE` supprime l'ensemble des lignes affichées ou non, éventuellement sur une plage délimitée par des labels. Cette commande prend obligatoirement le paramètre `ALL`.

![[syntax_delete.png]]

#### Positionner l'affichage

La commande `LOCATE` permet d'afficher, au sommet de l'écran, une ligne répondant à un critère donné, généralement un label, une erreur ou une commande en attente.

Deux syntaxes sont disponibles pour cette commande.

La première est générique :

![[syntax_locate_gen.png]]

La seconde est spécifique :

![[syntax_locate_spe.png]]

Par exemple, la commande primaire `LOCATE .LA` cherche la ligne labellisée par `.LA`, tandis que la commande `LOCATE 100` affiche la ligne portant le n°100. L'affichage d'une erreur peut quant à lui se faire avec `LOCATE ERR`.

#### Sauvegarder ou abandonner un travail

La commande `SAVE` permet de sauvegarder un travail, sans avoir à quitter le panneau comme on le ferait autrement avec `F3`.

![[syntax_save.png]]

La commande `CANCEL` permet d'abandonner un travail sans le sauvegarder.

![[syntax_cancel.png]]

#### Éditer un membre

La commande `EDIT` permet d'éditer un membre du même PDS que l'actuel.

![[syntax_edit.png]]

#### Soumettre un job

La commande `SUBMIT` permet de soumettre un job JCL à JES. Sans préciser de paramètre, l'ensemble du fichier est soumis. Il est aussi possible de soumettre un job contenu dans un bloc de lignes délimité par des labels. Dans un cas comme dans l'autre, la carte `JOB` doit être présente dans au moins une des lignes soumises.

![[syntax_submit.png]]

#### Le profil d'édition

S pour SELECT. S TEST07. Ouvre le fichier. S'il n'existe pas et qu'on sauvegarde, un nouveau fichier est créé. Sinon, rien n'est créé.


Je peux taper la commande HEX ON pour afficher le contenu du fichier en format hexadécimal. HEX OFF pour revenir à l'état précédent.

La commande RESET ou RES permet d'effacer les messages d'erreur, les aides apportées par COLUMNS, le surlignage des éléments recherchés, etc. En somme, il enlève tous les éléments de formatage.

La commande DISPLAY permet de mettre en évidence des caractères non affichables.

### Les commandes primaires

La commande FLIP permet d'inverser les éléments affichés et exclus.

**Faire un tableau avec les commandes**

Le profil de l'éditeur TSO permet d'afficher le profil de l'utilisateur ainsi que les paramètres qui lui sont associés. Il est accessible via la commande `PROFILE`.

NUM ON STD 
NUM ON COBOL est utilisé pour activer une numérotation adaptée au COBOL.

UNNUM enlève la numérotation

Avec l'option RECOVERY ON, on a accès à la commande UNDO, qui permet d'annuler une modification réalisée entre deux sauvegardes. Un fichier de recovery est créé par TSO pour conserver les modifications en mémoire si nécessaire. C'est ce fichier qui est invoqué par UNDO.

Avec l'option TABS ON $, on peut créer une ligne de tabulation avec la commande de ligne TAB. Sur cette ligne, on crée ensuite un formatage de tabulation avec des `*`. Quand on édite ensuite une ligne classique, en séparant différents éléments de celle-ci avec des `\$`, alors ils sont directement organisés selon le modèle de tabulation après avoir cliqué sur ENTREE.

Pour les tabulations, les `*` correspondent aux frontières de zones.

PROFILE LOCK signifie que toutes les modifications apportées au profil sont perdues en sortant de ISPF/PDF. Avec PROFILE UNLOCK, on conserve ces modifications.

HILITE permet de sélectionner le type de coloration syntaxique à utiliser selon les besoins.

L'instruction CUT met dans le presse-papier une ou plusieurs lignes, marquées par une commande de ligne C. L'instruction PASTE colle le contenu du presse-papier au niveau de la ligne marquée par une commande de ligne A ou B. On peut créer différents presse-papiers en spécifiant un nom après la commande, avec `CUT AA` par exemple. Lors du collage, on pointe simplement vers le presse-papier à utiliser. CUT DISPLAY permet d'afficher les presse-papiers disponibles.

Avec CUT AA APPEND, on peut ajouter une ou plusieurs lignes au presse-papier AA !

Tant qu'on ne quitte pas sa session TSO, le presse-papier est conservé.

Les limite de l'écran logique peuvent être contrôlées avec la commande BOUNDARY. La commande `BOUNDARY 25 40` permet de définir un espace situé entre les colonnes 25 et 40, qui fera lui seul l'objet des commandes primaires et de ligne appelées par l'utilisateur. Cela vaut pour les déplacements avec les parenthèses et les chevrons, les tries, les copies, les recherches, etc. Attention, les limites doivent être retirées avec la commande `BOUNDARY` sans paramètres, et cela, avant de sortir du programme, car elles sont conservées autrement.

On peut combiner des limites (colonnes) et des labels (lignes) pour définir une fenêtre dans laquelle réaliser les traitements.

#### Versionnage

Les commandes VERSION (**01**.04) et LEVEL (01.**04**) sont utilisées pour le versionnage d'un document.

#### Tri éditeur

On peut trier l'ensemble des lignes d'un fichier, seulement celles d'un label à un autre, seulement celles qui sont exclues ou non, et seulement compte-tenu de certaines colonnes... On utilise les paramètres A et D pour *ascending* et *descending* respectivement. `SORT A 3` permet de faire un tri ascendant compte-tenu des caractères en colonne n°3.

## Les utilitaires (option 3)

L'option 3 donne accès à un certain nombre d'utilitaires ou outils, utilisés notamment pour la manipulation de catalogues, jeux de données et fichiers.

### Les bibliothèques

Data Set Information, commande I pour les PDS :
- Un cylindre possède 15 pistes sur un disque 3390. Une piste fait 56k.
- Block size : c'est la taille du bloc lue au moment d'une I/O. Il fait une taille de 27920, soit environ demi-piste moins 80 pour tenir compte du gap. 

![[dataset_info.png]]

De retour au panneau principal de l'option 3, si on demande les opérations B, D, R, E ou V, il faut préciser le nom du membre sous ISPF Library.

L'option Enhanced Membre List permet d'afficher une zone de texte dans le gestionnaire de fichiers, zone dans laquelle on peut entrer des commandes. Dans l'exemple ci-dessous, on renomme TEST01 en REST01.

![[enhanced_member_list.png]]

Organization PO : Data set name type PDS, soit library
PS : SDS

### Les fichiers

L'utilitaire de jeu de données est accessible avec l'option 3.2.

Pour créer un nouveau fichier, on utilise la commande. Il faut donner un nom au jeu de données sous Data Set Name, par exemple test.lib qui crée la bibliothèque test. Lors de la création du fichier, il faut préciser la manière dont il est stocké. Pour créer un fichier séquentiel, on l'appelle test.seq. Il possède 0 blocks de directory.

**Essayer de créer deux jeux, un SDS et un PDS, API1.TEST.LIB et API1.TEST.SEQ**

600 de gap
Record length 80
Si block size vaut 3200, alors on peut mettre 40 enregistrements dedans

Lorsqu'un jeu de données est alloué, il existe uniquement dans le catalogue. Il faut lui donner un contenu avant de pouvoir l'utiliser à proprement parler.

On va en mode `EDIT`, puis `S TEST01`. Si le mode EDIT ne fonctionne pas à cause du mode RECOVERY, on fait un CANCEL.

Pour créer un fichier séquentiel, on fait la chose suivante :

![[creer_sds.png]]

On assigne la valeur 0 à directory blocks, trks à space units, et on assigne une taille de bloc, ici 27920. Aussi, il est impératif d'enlever le data set name type pour un séquentiel ! On le renseigne uniquement pour un jeu partitionné.

On peut utiliser la commande primaire LOCATE dans le répertoire d'une *library* pour chercher un membre.

On doit modifier SPACE UNITS, QUANTITE PRIMAIRE et SECONDAIRE, DIRECTORY BLOCKS (1 bloc contient à peu près 7 membres).

### Déplacer, copier...

La manipulation des membres et jeux de données est accessible via l'option 3.3.

Quand on copie le contenu de plusieurs PDS vers un SDS, les enregistrements sont mis les un à la suite des autres et ne sont pas séparés en plusieurs membres comme pour un PDS.

L'utilitaire Move/Copy est constitué de deux panneaux, le premier pour l'origine et le second pour la destination après avoir tapé sur `ENTREE`.

Pour copier tous les membres, on donne l'asterisk `test.lib(*)`.

### Consultation de la VTOC

La VTOC peut être consultée grâce à l'option 3.4. Privilégier l'option 3.4 pour réaliser les opérations, car elle autoriser toutes les commandes vues précédemment !

## SDSF

SDSF est un outil de consultation des fichiers qui dépendent de JES, notamment les *spools* d'entrée et de sortie.

PRE permet de chercher les processus dont le nom possède un préfixe donné, renseigné avec un paramètre dans la commande. Pour retirer le filtre, on utilise la commande PRE sans aucun paramètre supplémentaire. On peut également réaliser un tri sur les colonnes.

Pour retirer des éléments de la queue de sortie, on peut utiliser la commande de ligne P ou PURGE.

### Le log

Le log affiche l'ensemble des activités du système.

