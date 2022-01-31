# VSAM

## Divers

Pour se reconnecter à TSO :
- Lancer l'utilitaire de connexion.
- Entre le `LOGON API1`.
- Entrer le mot de passe et ajouter un `S` devant l'option `- Reconnect` située en bas.
- Taper `ENTREE`.

`ABEND 0 C4` : Calcul sur un champ non numérique
`ABEND 0 C7` : Travail réalisé sur un fichier dont le buffer n'a pas encore été ouvert, ou qui a déjà été fermé.

On peut utiliser l'utilitaire `IEBUPDATE` pour mettre à jour ou créer des fichiers séquentiels. Quand on lui passe `PARM=NEW`, il ne contrôle pas si le fichier existe ou non dans le PS fourni en `SYSUT1`. 

![[ex_iebupdate.png]]

Pour reprendre toutes les librairies dans un volume, on utilise `API1.SOURCE.**`.

Utiliser :
- `API1.SOURCE.JCL(ADRDDUMP)` pour créer un backup des libraires contenues dans un volume.
- `API1.SOURCE.JCL(ADRDREST)` pour restaurer des librairies. Il faut supprimer les fichiers avant de pouvoir restaurer.

Il est recommandé de compresser les librairies avant de réaliser un backup, cela permettant de ne pas faire une sauvegarde des fantômes.

Jusqu'à présent, nous n'avons fait que des QSAM (fichiers non paritionnés) et des PDS.

Pas besoin de faire un `DELETE` du VSAM lorsqu'on utilise le paramètre `REUSE` sur le cluster, l'AIX, le BIX...

Quand on écrit du JCL, on ne peut pas dépasser la colonne 72 !

## Introduction

Le VSAM, ou *Virtual Storage Access Method*, est une nouveauté ajoutée par IBM utilisée pour manipuler des fichiers sur disque et destinée à simplifier les modes de gestion. Tous les fichiers VSAM sont dans le catalogue avec l'information complète sur la définition des données de chacun de leur fichier. Ils donnent aussi accès à des utilitaires de backup.

Dans VSAM on définit des CI. Un CI contient des blocs physiques. On a une zone de Free Space FSPC. Les valeurs de contrôle CTRL permettent d'identifier les blocs qui les précèdent. Un CA Control Area correspond à un ensemble de CI et d'espaces libres. Les données sont écrites de la gauche vers la droite. Les données de contrôle sont écrites de la gauche vers la droite. Entre les deux, on retrouve le FSPC qui indique l'espace encore disponible dans le CI.

![[vsam_ci_ca.png]]

Quand un CI ou un CA est plein, il est coupé en deux.

### Control Interval CI

Un *spanned* est un enregistrement qui a été coupé en plusieurs morceaux. Un *record spanned* occupe des CI entiers.

###

Le CTRL contient des informations sur :
- Le CIDF, ou *Control Interval Definition Field*. 4 octets pour trouver l'espace libre, 2 octets pour en déterminer la longueur. Si un CIDF pointe vers un CI qui contient uniquement des 0, cela marque la fin de fichier.
- Le RDF, ou *Record Definition Field*. 3 octets pour la gestion des enregistrements logiques. On a un RDF par enregistrement. Il donne des indications sur les *spanned*, les espaces vides, la longueur des enregistrements ou segments, le nombre d'enregistrements consécutifs...

![[vsam_rdf_cidf.png]]

Le *free space* se trouve entre les données (enregistrements logiques) et les informations de contrôle (CTRL, donc CIDF et RDF).

### CI et RBA

RBA correspond à l'adresse relative de l'octet, du CI ou de l'enregistrement sur lequel on écrit. On utilise cette adresse pour retrouver un CI dans le fichier.

Concernant la taille d'un CI... On peut aller d'un demi k jusqu'à 8ko (pas de 0.5ko), ou bien de 2ko jusqu'à 32ko (pas de 2ko). Déjà 28ko c'est trop gros, cela prend plus d'une demi-piste. En général, on travaille sur des 4ko ou des 8ko.

### CA

La CA est une unité d'allocation qui va d'1 piste à 1 cylindre. C'est VSMA qui décide quoi prendre. Tous les CA contiennent le même nombre de CI.

### Organisation KSDS

Le KSDS, ou *Key Sequenced Data Set*, est un fichier avec des clés. Il contient deux parties principales :
- Les données.
- L'index.
Physiquement, on a deux fichiers, l'un pour les données et l'autre pour l'index. Les données peuvent être de longueur variable. Les clés sont de longueur invariable.

L'information free space... Quand on crée un fichier, on a un paramètre free space qui permet de renseigner l'espace libre laissé dans les CI et dans le CA. Valable pour le KSDS pour avoir les informations triées selon l'ordre des clés.

### Enregistrements spanned

Les spanned dépassent la longueur du CI et sont morcellés par CI. Un CI qui contient un spanned ne peut pas avoir d'autre information que ce spanned. Aucun record ne peut franchir l'espace du CA. Si le record a une clé, cette clé doit se situer dans le premier morceau.

### Organisation ESDS

Pas de free space, composé de données uniquement, à longueur fixe ou variable.

Mode séquentiel (ordre d'entrée).

Un ESDS peut occuper un ou plusieurs volumes de même type, donc sur plusieurs 3390, mais pas sur un 3370 et un 3390.

Accès direct possible avec l'adresse RBA. Suppression d'enregistrement interdite, mais on peut par exemple remplacer un enregistrement par 0 octets pour simuler la suppression.

AIX : alternate index. Index sur une autre clé, situé à l'extérieur du fichier.

### Organisation RRDS

Le RRDS est un fichier à accès direct, via le numéro de l'enregistrement (RRN). On peut lire le record 1 (RRN=1), créer le record 25 sans avoir créé les 24 précédents, etc. C'est un fichier de taille fixe. Si on veut l'agrandir, on doit copier son contenu dans un fichier plus grand (record de longueur fixe).

Le RRDS autorise aussi l'accès séquentiel en plus de l'accès direct.

> Le fichier RRDS permet de lire en une seule IO. Avec le KSDS, il faut au moins 2 IO pour lire la clé puis l'enregistrement pour trouver la donnée...

### Résumé

### Exercice 

2. Cluster
3. CI
4. CIDF, 4 octets. Le free space est contenu dans le CIDF.
5. RDF, 3 octets
6. de la gauche vers la droite... de la droite vers la gauche
7. spanned, 3 octets
8. CA, 1 cyl
9. CI, CA, la taille du fichier
10. RBA (relative byte address)

## Définition des clusters

Le cluster contient l'ensemble des informations d'un fichier.

### Syntaxe de la SYSIN

On utilise ICAMS. On utilise généralement un séparateur blanc, mais on peut éventuellement utiliser une virgule si on le souhaite. Pas besoin de préciser le volume VOL, c'est SMS qui s'en charge.

Le caractère de continuation se met toujours après les commentaires.

On n'écrit jamais en colonne 1, c'est comme quand on donne les instructions d'un SORT par exemple.

### AMS

AMS a toujours un SYSPRINT et un SYSIN. Derrière le SYSIN se trouvent les commandes, qu'elles soient en instream ou contenues dans un fichier. Le programme utilisé pour appeler AMS correspond à IDCAMS.

Un file est un fichier qui n'est pas un VSAM. 

Un dataset est un fichier qui est un VSAM, une allocation dynamique faite par l'AMS. Le dataset s'adresse directement à un DSNAME.

Les codes retournés par l'AMS :
- 0.
- 4.
- 8. Cela arrive par exemple quand un fichier n'existe pas. Le programme continue quand même.
- 12. Malgré l'erreur, le programme continue.
- 16.

Pour définir un fichier qui n'est pas VSAM, on utilise `DEFINE NONVSAM` ou `DEF NVSAM`. Il faut obligatoirement préciser le nom. Les types d'unité et volumes sont précisés quand on peut cataloguer le non VSAM dans le catalogue VSAM, chose qui est rare...

#### Alias

Les alias sont utilisés pour donner un nom différent à un catalogue (1 ou plusieurs qualifieurs) ou à un fichier non VSAM (nom qualifié).

Le RELATE correspond au nom d'origine pour lequel on donne un alias.

### Generation Data Group

Une GDG se situe dans un cluster VSAM mais concerne des fichiers non VSAM. Le GDG contient un groupe de fichiers séquentiels QSAM, simplement qu'on se sert de la définition VSAM pour stocker la définition du groupe. On l'utilise pour gérer un certain nombre de versions de fichiers, par exemple, pour un fichier mensuel, on peut dire qu'on en dispose de 13 copies pour avoir une année complète.

Un nom de fichier avec gestion de GDG possède un qualifieur final sous la forme de `NOM.G0001V01` par exemple.

On utilise la commande `DEF GFG` pour définir un GDG. On donne un nom, on définit une limite d'éléments à conserver. 

**Voir scratch et noscratch**

### Exemple pratique (1)

On crée le fichier VS01, qui va être utilisé pour créer des fichiers VSAM GDG. VS01 est un fichier JCL utilisé pour créer les GDG dans le catalogue.

![[ex_gdg.png]]

On a un GDG avec une limite de 3, ce qui signifie qu'on veut conserver jusqu'à 3 fichiers.

Avec empty on vide la pile. Avec no empty on ne vide pas la pile.

Pour supprimer un GDG, il faut commencer par supprimer tous les fichiers qui portent le nom GDG correspondant, avant de pouvoir supprimer dans le VSAM l'information sur le GDG.

On va ensuite écrire un `IEBGENER` qui va écrire des fichiers `GDG1` à `GDG4` jusqu'à atteindre la limite de 3 puis la dépasser. L'objectif est de voir ce qu'il va se passer après une limite de 3, compte-tenu de `(NO)EMPTY` et `(NO)SCRATCH`. Le fichier correspondant est `IEBGNGDG`.

![[ex_gdg_iebgener_v1.png]]

Dans la procédure `CREATE`, le `+1` après le `&NUM` permet de créer la version suivante du fichier.

Le GDG1 correspond au fichier VSAM. Les GDG1.G0004V00 et ainsi de suite sont les fichiers GDG, autrement dit les versions. 

En résultat, on a :

![[ex_gdg_ibegener_res.png]]

On avait :
- GDG1 EMPTY SCRATCH. Quand la limite est dépassée, EMPTY vide toutes les versions préexistantes. SCRATCH signifie que le DS est supprimé physiquement du système.
- GDG2 EMPTY NOSCRATCH. Quand la limite est dépassée, NOSCRATCH retire le DS du catalogue mais ne le supprime pas physiquement du système.
- GDG3 NOEMPTY SCRATCH. Quand la limite est dépassée, NOEMPTY élimine la version la plus ancienne.
- GDG4 NOEMPTY NOSCRATCH.

Créer VS02 et y copier le VS01. Remplacer les DEF par un DELETE, avec le nom du fichier GDG seulement. Cela permet de supprimer les GDG qui ont été créés plus tôt.

![[ex_gdg_del.png]]

### Les clusters VSAM

Un cluster VSAM est une entité VSAM. Un cluster contient 1 ou 2 composantes :
- les données,
- les index s'il s'agit d'un KSDS.

Le nom du composant correspond au nom du cluster en plus de l'index, ou de l'AIX, etc.

Le PATH est un chemin qui pointe sur l'AIX du fichier.

Dans le DSN, on utilise soit :
- SDJ.FICHIER.CLIENT,
- SDJ.FICHIER.CLIENT.AIX.PATH

### Définition d'un cluster

### Valorisation du paramètre CISZ

La taille du CI est définie par le paramètre CISZ.

### Paramètres obligatoires

Pour définir un cluster, il faut préciser :
- Le nom
- Les allocations d'espace désirées (TRK/CYL, quantité primaire, quantité secondaire).

Si un fichier existe déjà sur le système avec les mêmes caractéristiques que celles du fichier qu'on souhaite créer, on peut spécifier le nom et le modèle de fichier correspondant.

### Define cluster, data, index

KEYS(longueur offset) et non pas KEYS(longueur position). L'offset correspond au déplacement nécessaire depuis la première colonne pour trouver la clé. Si la clé se trouve en colonne 1, alors l'offset vaut 0.

RECORDSIZE(taille_moy taille_max). Pour une longueur fixe avec un LRECL de 80, on aurait RECORDSIZE(80 80).

Quand on crée un fichier RRDS, il est impossible de spécifier une taille secondaire, car il possède une taille fixe.

### Exemple pratique (2)

KSDS

On crée un fichier KSDSCRE.

![[ex_cre_ksds_corr1.png]]

Ici, c'est normal qu'on ait une erreur lors de la première soumission du JCL, car on delete un fichier qui n'existe pas. En revanche, l'étape de définition qui suite fonctionne. Quand on relance le JCL, il n'y a plus d'erreur, car il nous supprime le KSDS qu'on avait créé plus tôt. Toujours bien penser à préciser le FSPC !

Pour charger le KSDS, il faut lui donner des enregistrements triés. Pour cela, on va créer un fichier KSDSLOAD. On réalise un tri sur le COB.ASSURES et la sortie triée se trouve dans un fichier temporaire. Ensuite, on passe le fichier temporaire trié comme entrée de la fonction REPRO de l'outil IDCAMS, qui permet de le charger dans le KSDS.

INFILE : input file
ods : output data set

On reproduit le INFILE dans le ODS avec l'utilitaire REPRO.

![[ex_ksds_repro_corr1.png]]

Pour afficher le contenu du KSDS, aller en 3.2, spécifier le nom du KSDS dans le DSNAME (TEST.KSDS) et la commande de ligne V. Entrée, entrée. On arrive dans l'environnement d'exécution et on renseigne les lignes suivantes avant de lancer un exec.

![[ex_print_ksds.png]]

La clé correspond au numéro client, les 6 premiers caractères de l'enregistrement. Pour rappel, on avait défini KEYS(6 0) lors de la création KSDS.

Créer un jcl KSDSPRT. On va se servir de REPRO pour imprimer le contenu du KSDS dans le SYSOUT.

![[ex_print_ksds_repro.png]]

On retrouve alors le contenu du KSDS dans le morceau FIC du job dans la sortie SDSF.

![[ex_print_ksds_fic.png]]

On peut afficher les informations du KSDS dans DSLIST (3.4) avec la commande de ligne `i`. On peut supprimer le KSDS avec `d` depuis la VTOC. Cela nous donne ensuite la possibilité de réaliser les mêmes opérations qu'en passant par l'option 3.2 V.

![[ex_info_kdsd.png]]

### Les paramètres de KSDS

Il y a déjà les 4 bytes réservés au CIDF. À cela s'ajoute les 3 octets pour un record. Le nombre de records $\times$ la taille + les informations de contrôle, cela doit être inférieur ou égal à la taille du CI.

### Définition d'un ESDS

Un ESDS est de type NIXD.

### Exemple pratique (3)

Créer un fichier ESDSCRE. Y copier le contenu de KSDSCRE. On le modifie ensuite pour l'adapter à un fichier ESDS. Pas de clé ni de free space.

![[ex_esds_corr1.png]]

Comme avant, le CC sera d'abord de 8, puis de 0 après l'avoir relancé du fait du DELETE.

On va maintenant charger le ESDS en créant un JCL ESDSLOAD et en y copiant KSDSLOAD. Changer tous les KSDS par ESDS. On le charge dans l'ordre, il n'y a pas de clé, donc pas besoin du tri.

![[ex_esds_load.png]]

On va maintenant faire le print, avec ESDSPRINT et une copie de KSDSPRT.

![[ex_esds_print.png]]

### Définition d'un RRDS

Un ESDS est de type NUMBERED. On précise le nombre d'enregistrements à écrire dedans, pas de cylindre ou autre préciser.

### Exemple pratique (4)

Créer un fichier RRDSCRE, y copier ESDSCRE. Changer les ESDS par RRDS.

![[ex_rrds.png]]

On passe maintenant au RRDSLOAD.

![[ex_rrds_load.png]]

Pareil sur le RRDSPRINT.

![[ex_rrds_print.png]]

### Fichier temporaire

Les fichiers temporaires n'existent pas en VSAM.

### Les listes

### LISTCAT

HURBA : High used RBA, HARBA : High allocated RBA.

### REPRO

REPRO est utilisé pour le chargement d'un fichier instream ou préexistant, vers un VSAM.

Pour un KSDS, on peut préciser la plage de clés à charger. On peut préciser une plage de RBA. On peut aussi préciser une plage de valeurs d'enregistrements pour les RRDS. On peut dire aussi si on souhaite REPLACE ou NOREPLACE les records similaires. Avec REUSE, on précise si on veut conserver les records préexistants.

Pour le KSDS, il faut fournir un fichier trié dans l'ordre des clés.

### Paramètres REUSE

Si REUSE, le fichier est vidé de son contenu, HURBA est mis à 0.
Si NOREUSE, il y a fusion de file1 et file2 dans file2, à condition qu'il n'y ait pas de doublons.

### Eclatement d'un fichier

Avec REPRO, on peut utiliser les paramètres SKIP et COUNT pour éclater des fichiers.

### Commandes modales

### Exécution conditionnelle

Après un DELETE, on voit très souvent SET MAXCC=0 pour ne pas faire apparaître le fait que le DELETE n'a pas marché, lorsque le fichier n'existe pas.

### Clé secondaire

On distingue les clés primaires et secondaires, utilisées pour accéder à des enregistrements.

Quand on réalise une lecture sur une clé secondaire avec doublon, un message indique qu'il s'agit d'un doublon.

Clé secondaire modifiable par accès via la clé primaire.

Un AIX est un KSDS qui contient comme clé primaire la clé secondaire du cluster de base, et comme donnée la clé primaire d'un KSDS, ou le RBA d'un ESDS. Le RBA fait 4 octets. La clé primaire dépend du fichier. L'AIX a ainsi une partie DATA et une partie INDEX.

Lors de la définition de l'AIX, le paramètre RELATE donne le nom du cluster de base auquel renvoie l'AIX.

Le RECORDSIZE va dépendre du nombre de doublons qu'on admet. Si on admet 5 doublons, et qu'on dispose de 20 enregistrements, alors le sous-paramètre de taille moyenne vaut 20 + longueur de la clé. Le sous-paramètre de taille maximale vaut (20 + longueur de clé) + (nombre de doublons admis * longueur de clé).

![[ex_aix_recsz.png]]

Attention, si on crée un AIX, on est obligé de définir un PATH et d'utiliser un BIX. Ces utilitaires permettent d'associer les clés primaires aux clés secondaires renseignées dans l'AIX, et font donc le lien entre le cluster et l'AIX. BIX écrit dans l'INDEX et DATA de l'AIX.

### Exemple pratique

Créer KSDSAIX. Dans l'exemple du dessous, c'est normal d'avoir d'abord un CC8 si l'AIX n'existe pas.

En faisait un DELETE sur l'AIX, on supprime les DATA et INDEX de l'AIX. Si on fait un DELETE sur le KSDS, l'AIX, l'INDEX et le DATA sont supprimés.

![[ex_aix_ksds.png]]

### Define PATH

Le PATH correspond au chemin utilisé pour accéder aux donnés. On lui fournit un nom (celui de l'AIX) et un PATHENTRY qui correspond au chemin en lui-même.

### Le BLDINDEX

On construit l'index avec le nom du cluster comme jeu d'entrée, et le nom de l'aix ou du path comme jeu de sortie.

### Exemple pratique

Créer un JCL KSDSPATH

Le BLDINDEX lit le KSDS, lit les clés secondaires et les met dans l'AIX.

![[ex_ksds_path_bix.png]]

Au cas où, c'est normal qu'on ait un spam de CC 8... Il essaie d'abord de supprimer le PATH mais il n'y arrive pas, car il n'existe pas. Ensuite, on crée le BIX sans souci. Quand on relance, il supprime bien le PATH, mais il ne peut pas réécrire un BIX, d'où une nouvelle erreur.

Le plus simple pour éviter les erreurs ici est de mettre le BIX dans le KSDSAIX.

Attention, si on supprime l'AIX, le PATH est également supprimé. Si on supprime le KSDS, tous ces éléments sont supprimés.

Le mieux reste de tout mettre dans le KSDSAIX.

![[ex_ksds_aix_full.png]]

AIX met la table en place. PATH crée le lien entre le KSDS et l'AIX. BIX remplit l'AIX avec clé alternative et clés primaires correspondantes.

### Créer un index alterné

Lors de la définition de l'AIX, on peut préciser le paramètre UPGRADE pour mettre à jour l'AIX si des changements sont opérés dans le KSDS.

Par défaut, les doublons sont autorisés.

### UPGRADE SET

## Utilitaires IDCAMS

Dans le PRINT, quand il est question des FROMADDRESS et TOADDRESS, l'adresse correspond à du RBA.

La commande EXPORT permet de sauvegarder un fichier pour l'importer après. REWIND/NOREWIND/UNLOAD sont utilisés pour travailler sur des bandes magnétiques (rembobiner la bande, écrire directement sur la bande, enlever la bande de l'unité).

En règle générale, on n'exporte pas les AIX. On les refait après les avoir DELETE.

EXAMINE est utilisé quand on a une erreur pour vérifier si de l'information a été écrite dans un KSDS ou ses fichiers associés.

VERIFY est utilisé quand un programme est passé sans faire la fermeture du fichier. Cet outil permet de vérifier les informations de fin de fichier et de retirer correctement le verrou du fichier, en cas d'ABEND notamment.