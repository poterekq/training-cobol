# COBOL

## Divers

Attention, PROCLIB et JCLLIB c'est uniquement pour les procédures. Pour appeler un programme, on utilise une STEPLIB ou une JOBLIB.

## Éléments de base

Un programme COBOL contient :
- 4 divisions
	- Identification
	- Data
	- Procedure, pour exécuter les instructions du programme et qui se termine par un `STOP RUN.`.

### Exemple pratique

Les divisions, sections, paragraphes et structures commencent en colonne 8. Les ordres ou instructions commencent en colonne 12. Les commentaires commencent dans la colonne 7 et sont précédés par `*`. On met un point à la fin de chaque division, section, paragraphe, structure, ordre, déclaration... Attention, il y a aussi un point avant un paragraphe.

(1) Écrire un premier programme.

![[cobol_premier_programme.png]]

Un *load module* est un programme exécutable. Lorsqu'on compile le fichier COBOL, on produit un *load module*, stocké sous `API1.COBOL.LOAB`.

(2) Compiler le programme avec `COMPLKD`.

(3) Exécuter le programme. Pour cela, on crée un nouveau programme `PROGJ01`. Attention, il est très important de préciser le `TIME` dans les paramètres du JCL. Aussi, comme on appelle notre programme `PROG01`, il faut préciser où le chercher avec un `JOBLIB` ou un `STEPLIB`.

![[cobol_exec_prog01.png]]

### Structure d'un programme

Une phrase est une suite de chaînes qui caractères qui se termine par un point. 

Un paragraphe est un ensemble de phrases. Il commence par un nom suivi d'un point. Il se termine par le début d'un nouveau paragraphe.

Les divisions apparaissent dans un ordre défini : 
(1) *identification*. Elle est obligatoire et identifie le programme.
(2) *environment*. Elle est facultative. Elle déclare la configuration et les fichiers utilisés dans le programme.
(3) *data*. Elle est facultative. Elle comporte la description des données stockées dans les fichiers ou en mémoire.
(4) *procedure*. Elle est facultative. Elle comprend les instructions du programme.

### Format de référence

Les 6 premières colonnes contiennent les numéros de séquence.

La colonne 7 :
- `-`, la continuation d'un littéral qui dépasse de la ligne précédente.
- `*`, le commentaire.
- `D`, l'instruction sert uniquement au debugging, la ligne n'était pas compilée lors du passage en production.

Entre les colonnes 8 à 11 (marge A) commencent les indications de division, section, etc.

Entre les colonnes 12 et 72 (marge B) se trouvent les phrases ordres passés à COBOL.

### Schéma général d'un programme

On utilise `I-O-CONTROL.` pour les bandes magnétiques.

Dans la `DATA DIVISION.`, on retrouve les sections suivantes :
- La `LINKAGE SECTION.` appartient à un autre programme avec lequel on établit un lien.

### Variables et littéraux

Un littéral non numérique est encadré par des `'` ou `''`. Dans le cas où la chaîne de caractères comprend des apostrophes, il faut utiliser les `''`.

Un littéral numérique n'est pas encadré par des apostrophes ou *singles quotes*.

### Notations

## Identification et division environnement

## Data division

Un nombre niveau est utilisé pour identifier une information par rapport à une autre :
- Le niveau `01` est utilisé comme début de structure.
- Le niveau `77` est une zone qui n'appartient à aucune structure et qui sert juste à définir une information.

### Structure des données 

Tout ce qui est niveau de groupe est considéré comme du PICX. Ainsi, `LIEU-NAISS.` est un PIC X de 5.

### Exemple pratique

Tout niveau élémentaire ou de groupe doit se terminer par un point. Ici, on a créé deux zones ou variables `TOTO` et `Z1`, auxquelles on a assigné une valeur, et qu'on affiche sur la même ligne que le texte précédemment renseigné.

![[ex_cobol_data.png]]

NULL n'existe pas pour l'ordinateur. ALL remplit complètement la variable par le littéral qui lui est passé.

## Procedure division

### Les instructions

On distingue 9 catégories de divisions.

L'ordre `COMPUTE` fait appel à un sous-programme écrit par IBM pour réaliser le calcul. On l'utilise pour des calculs complexes avec des puissances, parenthèses, etc.

L'ordre MERGE fusionne deux fichiers triés. L'ordre RELEASE écrit dans le fichier de tri. L'ordre SORT trie. L'ordre RETURN permet de récupérer un enregistrement du fichier trié.

L'ordre ACCEPT récupère l'heure, le jour, la date et le jour de la semaine.

L'ordre INITIALIZE initialise une structure. Tout ce qui est texte est rempli par un blanc. Tout ce qui est numérique est rempli par 0.

L'ordre MOVE permet de copier une chose dans une autre.

L'ordre INSPECT permet de chercher quelque chose dans une zone pour en faire quelque chose.

Les ordres STRING et UNSTRING sont utilisés pour la concaténation ou la découpe de chaîne de caractères.

L'ordre ALTER est aujourd'hui interdit.

L'ordre EXIT est utilisé dans les paragraphes vides et pour éviter des erreurs de compilation.

L'ordre GO TO est aujourd'hui interdit.

L'ordre PERFORM va à un endroit faire quelque chose puis revient.

L'ordre STOP RUN arrête définitivement le programme. L'ordre GOBACK permet de retourner dans le programme parent lorsqu'on est dans un enfant.

L'ordre EVALUATE est l'équivalent d'un CASE.

L'ordre SEARCH est utilisé pour chercher quelque chose dans une table. L'ordre SET assigne une valeur à une zone d'une table.

L'ordre ACCEPT est utilisé pour chercher des informations, par défaut le SYSIN. L'ordre DISPLAY affiche quelque chose. L'ordre OPEN ouvre un fichier. READ, WRITE, REWRITE, DELETE pour supprimer un enregistrement. START pour se positionner à l'intérieur d'un fichier. On a toujours un OPEN et un CLOSE pour un fichier.

L'ordre CALL appelle un sous-programme. Les autres s'oublient.

### Les catégories d'instructions

Le délimiteur `.` met fin à l'instruction. Toutes les instructions peuvent avoir un END.

### Exemple pratique

Le système donne accès à un ordre `DATE` pour récupérer la date du système.

![[ex_cobol_levelnumber.png]]

Pourquoi être passé du niveau 1 au 05 ? On aurait très bien pu faire un niveau 02, mais on utilise le 05 au cas où, car si on doit ajouter des niveaux intermédiaires on doit aller modifier les `02` partout et adapter la hiérarchie.

Ici, le niveau de groupe 01 ne doit pas avoir de PIC. On assigne un PIC uniquement aux niveaux les plus bas.

> **Note :** Pour chercher les messages d'erreur, on fait un `find igy`.

On peut faire un MOVE dans la procédure pour assigner une nouvelle valeur à une variable.

MOVE CORRESPONDING ou MOVE CORR. Cet ordre formate un espace de données grâce à un autre, en faisant correspondre les noms des niveaux intermédiaires des deux espaces.

Quand des niveaux intermédiaires portent le même nom, on précise avec le mot-clé `OF` suivi du nom du niveau de groupe.

![[ex_cobol_of.png]]

L'ordre ACCEPT récupère de l'info depuis le SYSIN.

![[ex_cobol_accept1.png]]

![[ex_cobol_accept2.png]]

MOVE sur des variables de tailles différentes :
- Pour PIC X et PIC A, les caractères sont perdus à l'arrière.
- Pour PIC 9, les chiffres sont perdus à l'avant.

### ACCEPT

Par défaut, ACCEPT récupère l'information de SYSIN. On peut aussi ACCEPT une valeur depuis la CONSOLE avec FROM CONSOLE. Dans ce cas, un opérateur doit répondre à une question.

![[ex_cobol_fromconsole.png]]

On doit aller sous m.5, i (input queue), s sur le job qui est en attente d'input, puis entrer dans la command input : /04,ursula. Ici, il faut remplacer le 04 par ce qui se situe en ligne 6 avant le "Awaiting reply".

Pour créer un nombre aléatoire, on utilise souvent un ACCEPT TIME.

NO ADVANCING était utilisé pour les imprimantes, pour écrire sur la même ligne et simuler les caractères gras.

### Initialize

L'initialisation ne concerne pas les données sans nom, ou pour lesquelles une valeur est déjà assignée. On peut initialiser un niveau intermédiaire ou le niveau de groupe.

### STOP

### PERFORM

PERFORM permet d'exécuter un élément dans le programme, identifié comme étant une section ou un paragraphe. Si on appelle une section, tous ses paragraphes sont exécutés.

(1) Créer un programme COBOL avec un paragraphe fin qui fait un DISPLAY de 'Fin du programme'.

![[ex_cobol_perform.png]]

(2) Demander à répéter le paragraphe 1 3 fois.
![[ex_cobol_times.png]]

(3) Créer un paragraphe ARRET.

> **Rappel :** Un paragraphe se termine toujours par un point !
> **Note :** S'il n'y a pas d'arrêt STOP RUN, le programme continue en séquence.

![[ex_cobol_stoprun.png]]

(4) Utiliser le paramètre THROUGH du PERFORM.

![[ex_cobol_perfthrough.png]]

> **Note :** Il ne peut pas y avoir de doublon dans le nom des structures, variables, etc.

(5) Utiliser la notation PERFORM...END-PERFORM. C'est un PERFORM qui contient des instructions. Un point à l'intérieur du bloc arrête le PERFORM. Sans point, on utilise le END-PERFORM.

Demander à réaliser ce bloc 2 fois.

![[ex_cob_perfend.png]]

Le paramètre UNTIL permet de conditionner le PERFORM.

![[ex_cob_peruntil.png]]

Préférer un `UNTIL CPT > 12` à un `UNTIL CPT = 13`. On ne travaille plus sur du "faire tant que", mais sur du "jusqu'à ce que".

### EXIT

Il est interdit d'avoir un paragraphe vide. Pour palier à cela, on donne `EXIT.` à un paragraphe vide pour éviter les erreurs de compilations. On s'en sert principalement avec les PERFORM THROUGH, où l'on peut demander d'aller jusqu'à un paragraphe qui marque la fin des instructions et qui est vide.

Le EXIT PROGRAM. provoque un retour au programme appelant ou un STOP RUN.

### EVALUATE

EVALUATE permet de tester quelque chose. On peut rajouter d'autres conditions avec le mot ALSO.

Si on évalue avec plusieurs ALSO, il faut respecter le même format avec les WHEN. Si on a 2 ALSO dans l'EVALUATE, il faut alors 2 ALSO dans les WHEN. On a le WHEN OTHER pour tout autre cas, et sur lequel on n'a pas à mettre de ALSO.

### GO TO

### CONTINUE et NEXT SENTENCE

### Les instructions arithmétiques

Quand on crée un PIC numérique signé avec par exemple `S999`, le dernier caractère correspond à une lettre ou un signe en hexadécimal :
- C : Signe positif.
- D : Signe négatif.
- F : Non signé.

E -> +5
} -> +0

Donc E5 ça fait 55, et 6} ça fait 60.

![[ex_cob_nbhex.png]]

Une zone numérique non initialisée donne un ABEND 0C7, qui signifie que le calcul n'a pas été fait sur un champ numérique. Il faut toujours initialiser une zone, que ce soit dans la DATA DIVISION avec VALUE, ou dans la PROCURE DIVISION avec des MOVE TO.

![[exo_cob_arithm.png]]

Si Z3 n'est pas numérique d'abord, mais qu'on fait un GIVING Z3, alors Z3 sera numérique. Si on fait un ADD 7 TO Z4 et que Z4 n'est pas numérique, ça fera un ABEND. Le GIVING écrase la zone à laquelle il s'applique, et lui donne le résultat de l'opération.

Quand on fait un test sur une zone, on évalue d'abord toujours son type (NUMERIC) puis la valeur (> 50 par exemple).

### NOMS CONDITIONS

Un nom condition est une valeur testée dans une zone de niveau 88.

## Représentation interne des données

### Clause USAGE

La clause USAGE décrit comment représenter l'information. Par défaut, USAGE DISPLAY.

Pour IBM, un mot correspond à 4 octets. 2 octets correspondent à un demi-mot. 8 octets correspondent à un double mot.

Pas de PIC lorsqu'on définit une zone INDEX.

### Clause SYNCHRONIZED

## Traitements sur les fichiers

fd fic1 recording f. -> File definition, fic1, enregistrements fixes.
01 efic1 pic x(80). -> La longueur de l'enregistrement est de 80.
77 ws-fic1 pix xx. -> Toujours 2 octets pour un file status.

Le file status permet de gérer les erreurs, ce qui doit être réalisé après chaque opération sur un fichier (open, read, close...).

Pour éviter d'arrêter le programme avec un code CC0, on fait un MOVE 16 TO RETURN-CODE (mot réservé par COBOL) pour renvoyer une erreur.

![[ex_cob_filestatus.png]]

PERFORM UNTIL WS-FIC1 = '10', on traite le fichier jusqu'au code 10 qui représente la fin de fichier.

Pour utiliser les champs d'un fichier, il faut définir une structure dans la FILE SECTION. Attention, ici le fichier d'entrée fait 80 colonnes. On crée un champ vide à la fin de taille 9 pour avoir ce vide.

![[ex_cob_fields.png]]

![[ex_cob_display_fields.png]]

La structure développée plus haut peut être utilisée dans un autre programme. On crée un move sur les niveaux intermédiaires, et on les place dans un membre avec cre copyass, et un bloc mm sur les lignes en question.

![[ex_cob_copyass.png]]

On peut ensuite l'importer avec COPY COPYASS dans le niveau de groupe.

![[ex_cob_copyass2.png]]

Pour que le compilateur retrouve le COPYASS, il faut bien avoir renseigné la ligne COBOL.SYSLIB dans COMPLKD. Ici on fait une concaténation.

![[ex_cob_copyass_complkd.png]]

### Clause FILE STATUS

Code retour VSAM :
Code fonction VSAM pour l'open, le close, le write, le delete, etc.
Code feedback de VSAM :

Les codes VSAM ne sont pas réellement nécessaires, on peut se contenter de ceux vus précédemment.

### Description de l'enregistrement

Pour les VSAM, on utilise juste FD FICHIER1. Pour les fichiers séquentiels, on utilise FD FICHIER1 RECORDING F/V (fixe ou variable).

La variable associée au FILE STATUS (WS-FIC1 dans notre exemple) est à mettre en PIC XX de préférence, pour avoir des codes retour plus complets.

### Exemple récapitulatif

Il est recommandé de faire un OPEN par fichier, et pas un OPEN de bloc pour l'ensemble des fichiers utilisés. De cette façon là, chaque fichier aura son return code pour en vérifier l'intégrité.

### OPEN

OPEN INPUT : lire le fichier
OPEN OUTPUT : écrire le fichier
OPEN I-O : lire et écrire le fichier
OPEN EXTEND : mode append

### READ

Un READ PREVIOUS permet de relire l'enregistrement que l'on vient de lire. Il faut faire un deuxième READ PREVIOUS pour lire le précédent.

La lecture permet de lire le fichier. READ fichier.

### WRITE

L'écriture permet d'écrire un enregistrement dans le fichier. WRITE enregistrement.

### Autre

Il est recommandé de fermer d'abord le fichier OUTPUT en premier, puis les fichiers INPUT en dernier ! Aussi, il est recommandé de tester le file status à chaque écriture, notamment pour vérifier qu'on n'essaie pas d'écrire dans un fichier plein.

## Editions

Les PICTURE d'édition peuvent servir à récupérer le résultat d'un calcul, mais ne peuvent pas être utilisée pour réaliser un calcul.

L'astérisque sert à faire de la protection numérique. On l'utilise pour ne pas pouvoir falsifier les chiffres. Lorsqu'on met un astérisque, on ne peut plus ajouter de nouveau chiffre devant pour augmenter par exemple la valeur d'un chèque. L'astérisque se met sur tous les caractères qui ne sont pas renseignés.

Si on fait '+++', on considère le + comme un signe flottant. Si j'ai un 10, alors je récupère + 10. Il donnera un - 10 si le chiffre est négatif.

![[ex_cob_pic_edition.png]]

Ici, on a des S999. Pour voir le + ou le -, il faut +++9. Autant de + que de chiffres dans le nombre.

## Fichiers à accès direct

Ce sont les VSAM et BDAM. On travaille principalement sur du KSDS et RRDS.

Pour un KSDS : SELECT... ORGANIZATION IS INDEXED ACCESS MODE IS ...

Pour l'access mode :
- dynamic : on fait du séquentiel ou du random.
- random : on accède via une primary key. Dans ce cas, on précise RECORD KEY IS. Une clé de KSDS est toujours du PIC X. On peut aussi préciser un AIX avec ALTERNATE RECORD KEY IS... Il faut préciser ce qu'on fait des doublons ! Si on utilise un AIX, il faut préciser les cartes DD du KSDS et du PATH !
- sequential : on lit le fichier de façon séquentiel.

> Note : Pour regarder la clé alternative utilisée pour un AIX, on fait un PRINT sur le PATH.

![[ex_cob_ksds_seq.png]]

Dans l'exemple ci-dessus, il faut penser à enlever RECORDING F sur l'ordre FD, puisqu'on traite un VSAM. On doit préciser la clé, même si on est en mode séquentiel pour un KSDS...

En mode random, on va récupérer le numéro d'assuré, puis boucler dessus jusqu'à ce qu'on rencontre le numéro '000000' qui indique un enregistrement non renseigné. Dans le JCL de lancement du programme, on peut préciser les clés qu'on souhaite utiliser pour accéder au KSDS, cela via l'ordre SYSIN. Elles sont fournies au programme COBOL grâce au verbe ACCEPT, suivi de la clé soit ACCEPT ASS-NUMERO.

![[ex_cob_ksds_random_sysin.png]]

On va faire la même chose avec l'AIX... Pour cela, il faut préciser l'ALTERNATE KEY du fichier. Il faut préciser à COBOL qu'on accède au fichier via ASS-NOM et non pas ASS-NUMERO. Pour cela, on utilise le verbe START, avec START FIC1 KEY > ASS-NOM, et un LOW-VALUE (0 binaire) dans ASS-NOM. On cherche ici dans le fichier le premier enregistrement qui a une clé alternative supérieure à LOW-VALUE. On est obligé de faire un START sur ASS-NOM pour que COBOL se positionne sur cette clé plutôt que sur ASS-NUMERO puisqu'on utilise un AIX.

> Note : Quand on utilise l'AIX, il faut créer une carte DD spéciale dans le JCL, qui reprend le nom de la carte du KSDS, suivie d'un 1.

![[ex_cob_ksds_aix_jcl.png]]

Pour le mode d'organisation RANDOM, on n'utilise pas de START. On fait faire un READ FIC1 KEY ASS-NOM à la place.

Pour le RRDS avec lecture sur clé et non pas séquentielle :

![[ex_cob_rrds_key.png]]

Attention, petite erreur, car on doit utiliser une RELATIVE KEY car on est sur un RRDS !

![[ex_cob_rrds_relativekey.png]]

Pour renvoyer un return code à l'aide d'une zone définie par le système, on peut utiliser une linkage section.

![[ex_cob_rc_linkage.png]]

![[ex_cob_rc_linkage2.png]]

## Analyse de la compilation

## Perfectionnement COBOL - Description des données

### Littéraux hex et DCBS

### EVALUATE et REDEFINES

![[ex_evaluate.png]]

Si la valeur en C1 vaut 1, alors un lit le champ comme code-1, et ainsi de suite jusqu'à 3

```cobol
EVALUATE TRUE
  WHEN code-enr = 1
    DISPLAY code-1
  WHEN
  ...
END-EVALUATE
```

MOVE NOM(1:5) TO CODE-POSTAL
Ici, la partie entre parenthèses correspond à un *reference modifier*. Le premier nombre correspond à la position et le deuxième à la longueur.

## Les tables

Ce sont des tableaux dans le programme, pas des BDD.

![[ex_table.png]]

On a une table qui contient 12 occurrences de la même structure, à savoir le nom du mois, suivi du nombre de jours.

On peut redéfinir un ensemble d'enregistrements en un tableau :

![[ex_table_redefines.png]]

Un autre exemple :

![[ex_table_redefine2.png]]

> Note : Le REDEFINE se trouve toujours au même niveau que la donnée qui est redéfinie, et directement après !

Pour se déplacer dans les tableaux, les compteurs utilisés peuvent être en COMP (binaire) et permettant ainsi d'aller plus vite, car c'est un déplacement dans la mémoire. Pour les calculs réalisés sur des flottants, on spécifie le type COMP-3.

### Valorisation d'une table

Lors de la compilation, on dispose d'une compilation :
- SSRANGE : Le compilateur vérifie à chaque fois que l'indice utilisé lors de l'appel d'un tableau est compris dans la plage d'indices valables.
- NOSSRANGE : Le compilateur ne vérifie pas. Risque de 0C4 (adresse invalide) ou que le programme récupère de l'information dans une zone mémoire autre que celle à laquelle on souhaite accéder.

Pour mettre une option : 

![[option-compilation-cbl.png]]

Pour avoir trois boucles imbriquées :

![[cob-perform-after.png]]

On fait la première boucle de 1 à 31 avec J et I à 1. Ensuite, on refait la boucle de 1 à 31 avec J = 2 et I = 1, et ainsi de suite. Cette formule s'utilise uniquement lorsque ne réalise pas des opérations sur les niveaux intermédiaires.

### Tables de longueur variable

Avec un OCCURS, on dit quelles seront les longueurs minimale et maximale. On utilise un littéral ou une variable pour préciser la longueur avec DEPENDING. Pour réserver la table, le compilateur va d'emblée employer la valeur maximale, ce qui au final ne sert à rien de préciser la valeur du DEPENDING... Pas très utile.

### Tables indexées

On précise qu'une table est indexée par un nom d'index. Le compilateur définit automatiquement les index, ce n'est pas à l'opérateur de les préciser dans le working-storage. 

Pour démarrer une recherche dans une table indexée, on utilise un SET MyIndex TO 1 par exemple. On a ensuite l'instruction SEARCH utilisée sur la table indexée.

La recherche indexée est plus rapide, car c'est du binaire. En revanche, on peut faire la même chose avec un PERFORM VARYING lorsque fixe nos indices en COMP. Le seul avantage des tableaux indexés est la recherche dichotomique SEARCH ALL (binary search), qui nécessite cependant que la table soit triée !

## Sous-programmes externes

![[proc-div-ext.png]]

Préciser tous les paramètres dans la PROCEDURE DIVISION.

On assigne à ADRESSE-DATE l'adresse de DATEPROG. Si la valeur est LOW-VALUE (zéro binaire), cela signifie qu'on n'a pas renseigné ce paramètre optionnel DATEPROG. On peut tester cela par une comparaison avec NULL (`IF ADRESSE-DATE = NULL`). ADRESSE-DATE est défini plus haut comme un pointeur avec `77 ADRESSE-DATE POINTER.`.

Un sous-programme revient dans un programme avec une instruction GO BACK, ou un EXIT PROGRAM. On n'utilise pas de STOP RUN !

Le EXIT PROGRAM fait un GO BACK pour un sous-programme et un STOP RUN pour un programme.

![[ex_cob_goback.png]]

CALL 'NomProgramme' USING parm1 parm2 ... parmn

Dans le programme, l'option CBL DYNAM est indispensable lorsqu'on fait un CALL sur un nom littéral de sous-programme.

![[cbl_dynam.png]]

![[cbl_dynam_call_litteral.png]]

Les CALL statiques se font lorsque le sous-programme est attaché au programme dans le load module lorsqu'on crée l'exécutable. On a 1 load. Avec un CALL dynamique, le sous-programme est cherché dans le load module et arrive en mémoire seulement une fois que le programme réalise le CALL. On a 2 loads.

CALL BY CONTENT MyVar    : MyVar n'est pas modifiable par le sous-programme.
          BY REFERENCE MyVar : MyVar est modifiable par le sous-programme.

## Manipulation de chaînes

## Algorithmique

> **Important :** Pour les IF et compagnie, si on fait une opération booléenne, on peut ne pas la faire sur le champ d'un fichier. Il faut absolument créer une variable dans le working-storage !

WRITE ECLIENTF FROM ECLIENT1, nous évite de faire des MOVE !

Pour retirer les doublons lors d'un tri :
![[jcl_doublons.png]]
SORT pour trier directement depuis SPDF.

## Debugging

Utiliser le mode debugging lors du développement. Au passage en préproduction, il faut enlever le mode de debugging.

![[debugging.png]]

Pour enlever le mode debugging, on commente juste la ligne SOURCE-COMPUTER qui contient le WITH DEBUGGING MODE. De cette façon, toutes les lignes du programme qui commencent par D en colonne 7 sont ignorées.

## Manipulation des chaînes

L'instruction INSPECT :
- LEADING pour traiter le côté gauche de la chaîne.
- TALLYING pour traiter le côté droit de la chaîne.

Le CONVERTING de INSPECT réalise un mapping entre chaque élément de deux chaînes de comparaison.

Le STRING permet de réaliser de la concaténation de variables.

## Note JCL

Quand on exécute un JCL, pour éviter les boucles, on définit le TIME dans la carte JOB, et il faut aussi penser à mettre un OUTLIM=80000 par exemple sur la SYSOUT ! Quand JES arrive à 80000 lignes dans le SYSOUT, il renvoie un ABEND S722.

## Les fonctions intrinsèques

ANNUITY : Utilisé pour les crédits, nous donne le tableau d'amortissement.

Pour les fonctions intrinsèques avec les dates en 1600, on se sert d'une date d'origine, d'un nombre de jours, et on fait simplement la somme pour déterminer par exemple quelle sera la date dans 90 jours.

![[dates.png]]

On peut aussi faire un : `COMPUTE DATE-R = FUNCTION DATE-OF-INTEGER(NBJ)`

Pour déterminer le nombre de jours entre ma naissance et aujourd'hui :

![[determiner_nbj.png]]

Une méthode intéressante est de créer un niveau 01 qui contient tous mes compteurs. Ensuite, il suffit de faire un INITIALIZE de ce niveau 01, ce qui va permettre d'initialiser tous les niveaux atomiques contenus dans le groupe.

Par défaut, le ACCEPT récupère la SYSIN. Dans le working-storage, on définit une variable, par exemple CONTENT. Dans la procedure division, on fait ensuite un ACCEPT CONTENT, qui va récupérer la SYSIN. On peut créer des niveaux intermédiaires pour dire quelle est la structure du SYSIN reçu par ACCEPT.

On utilise `EXIT` pour un paragraphe vide.

Pour compresser la valeur 87 en un octet, on peut utiliser la technique suivante :

![[compresser-valeur.png]]

Le premier octet appelé z3 va contenir 87, et le deuxième sans nom contient 0C.

Aujourd'hui, on peut remplacer cette technique en définissant une zone en hexadécimal.

![[compresser-hex.png]]

Pour un UNSTRING, on peut combiner plusieurs délimiteurs, tel que :
```
UNSTRING Zone DELIMITED BY '?' ',' '-'
  INTO Z1 DELIMITER IN Dl1 COUNT IN Cpt1
       Z2 DELIMITER IN Dl2 COUNT IN Cpt2
	   ...
END-UNSTRING
```

Quand on crée un fichier pour l'impression, il faut penser à utiliser le nombre de colonnes spécifié dans le FD du fichier, puis lui ajouter 1. Cette valeur est passée au JCL lors de la création du fichier d'impression. Si on avait un FD contenant une ligne de PIC X(80), il faut alors dans le JCL créer un fichier dont le LRECL vaut 81.

Erreur B37 sur la bibliothèque quand elle est pleine ! Il faut penser à compresser la bibliothèque ! En 3.4, on affiche API1.SOURCE.COBOL, puis on met simplement un Z à côté du PDS pour le compresser.

Lorsqu'on utilise les COPY, il faut préciser au JCL où trouver les fichiers à copier. On les renseigne via la procédure COBOL.SYSLIB. Pour rappel, on peut concaténer plusieurs bibliothèques dans le JCL.

![[renseigner_copy.png]]

COPY CMVT SUPPRESS : Le SUPPRESS permet d'éliminer le contenu de COPY du compte rendu de compilation.

```
COPY CMVT REPLACING ==05== BY ==02==
COPY CMVT REPLACING ==10== BY ==04==
```

Les COPY ont une structure qu'on ne doit pas changer. On peut cependant modifier leur structure temporairement via COBOL à l'aide du REPLACE. Dans l'exemple ci-dessus, on modifie les niveaux 05 par des 02, et les 10 par des 04.

On peut aussi REDEFINES des éléments contenus dans un COPY, directement dans le COBOL.

Dans le JCL, pour les lib :
- On met le JOBLIB directement après la carte JOB.
- On met le STEPLIB directement après la carte EXEC.

JCLLIB : Lieu de résidence des procédures
SYSLIB : Lieu de résidence des COPY.

Le mode COMP-5 permet de réaliser des calculs en mode compilatoire TRUNC(BIN). Il faut préférer son utiliser à celle de COMP.

Pour un KSDS, lors du SELECT ASSIGN, on donne la clé primaire avec RECORD KEY, et la clé secondaire avec ALTERNATE KEY. En COBOL, dans le FD du fichier, on doit toujours définir la clé en PIC X !

Pour un KSDS en mode séquentiel ou dynamique, on peut utiliser un START dans la PROCEDURE DIVISION. Cela permet de commencer à lire le fichier KSDS en mode séquentiel à partir de la clé spécifiée après le START.

Services LE : sous-programmes développés par IBM. Voir dans CEE.SCEESAMP, mais ils sont disponibles directement grâce à la phase de compilation qui les intègre.

Exemple pour appeler CEEDAYS

![[ex_ceedays.png]]

Avec CEEDATE, on peut fournir un format de date avec le mois en clair :

![[ex_ceedate.png]]

Le `Z` indique que tous les caractères inutiles sont concaténés.

Pour avoir le jour en clair, on utilise : `Wwwwwwwwz`. Avec `W` en majuscule, la première lettre du jour est en majuscule. On peut l'avoir également en minuscule avec `w`. La notation `ZD` donne le numéro de jour, le `Z` indiquant qu'on élimine le premier caractère pour les chiffres inférieurs à 10. Attention, `z` minuscule si fin de mot, `Z` majuscule si début de mot.

Modules pour faire de l'acquisition et libération dynamique de mémoire :
- CEEGTST. On définit l'ID de l'espace heap ainsi que sa taille. Il nous renvoie une adresse ADDRSS de type PIC S9(9) BINARY, POINTER ou PROCEDURE POINTER (équivalents). Pour y accéder, on utilise un PROCEDURE DIVISION USING ma_zone. Dans la LINKAGE SECTION, ma_zone contient deux niveaux est définie par exemple avec une PIC X(100000) qui correspond à la taille demandée pour la heap size. Dans la PROCEDURE DIVISION, on a après le CALL de CEEGTST, un IF FC(1:8) = LOW-VALUE SET ADDRESS OF ma_zone TO ADDRSS.
- CEEFRST qui libère un espace, avec deux paramètres ADDRSS et FC. Le paramètre ADDRSS correspond à un pointeur, l'adresse d'un espace mémoire alloué.

Un programme batch contient 2 parties, une pour la logique de traitement et l'autre pour l'accès aux données. On va prendre le programme de quittance et le séparer en deux. Une partie logique et une autre qui gère les accès (on parle d'un accesseur). L'accesseur contient les SELECT, OPEN, READ, etc. La communication entre les deux parties se fait par une zone de communication.

Dans le programme principal, CALL au SP avec un PARM (fonction, nom de fichier, i-o, cr). Le code fonction correspond à l'opération à réaliser, par exemple 01 pour une lecture. Le nom de fichier correspond au fichier sur lequel on souhaite travailler (ouvrir, lire, etc.). Dans le sous-programme, on retrouve PARM dans la LINKAGE, puis un USING dans la PROCEDURE DIVISION. On fait ensuite un EVALUATE du code fonction pour déterminer quoi faire.

![[ex_ppsp1.png]]

![[ex_ppsp2.png]]

En plus de la WORKING-STORAGE SECTION, il existe aussi une LOCAL-STORAGE SECTION. A chaque GOBACK, les valeurs des zones sont réinitialisées.

![[ex-localstoragesection.png]]

Pour les tests sur fichiers vides, mettre simplement une carte DD DUMMY dans le JCL pour le fichier correspondant.

Pour les spécifications, si elles nous semblent incomplètes (par exemple, pas précisé quoi faire lorsqu'il y a une division par zéro), poser la question au spécificateur.