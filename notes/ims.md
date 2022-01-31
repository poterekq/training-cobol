# IMS

Lorsqu'une information est modifiée dans un fichier, il y a une incohérence sur les autres fichiers. Il y avait un problème de redondance des données ? L'idée était de centraliser ces données pour ne devoir modifier qu'une fois.

Comme en VSAM, le chargement initial nécessite que les données soient triées dans une séquence particulière. Ensuite, pour toute opération, on peut par exemple faire une insertion sans suivre l'ordre initial du tri. La base de chargera toute seule de remanier la séquence hiérarchique.

Un PSB peut contenir un ou plusieurs PCB (Programe Control Block). Le PSB est l'équivalent d'un JCL, tandis que les PCB sont l'équivalent des PGM dans les STEP. Les PCB correspondent à des zones de communication avec des données récupérées dans l'équivalent de la LINKAGE. Au minimum dans un PSB, on précise : code fonction, zone d'IO et le PCB utilisé. Les PCB référencent plusieurs DBD qui contient des segments sur lesquels on souhaite travailler.

Avant de faire une opération de ISRT, REPL ou DLET, il faut obligatoirement faire avant cela l'une des opérations de GH (Get Hold) afin d'avoir le contrôle sur l'enregistrement. Avant de faire un GNP, on doit faire un GU (Get Unique). Le GNP va lire l'ensemble des enfants sur lesquels on pointe. Le GN va lire toute la base. Si on DLET la racine, tous les enfants sont détruits.

Un PCB1-CR = 'GB' indique la fin de la base, comme un code 10 en SEQ.

1/ LANCER LE DELDEF POUR CREER LE FICHIER VSAM           : DELDEF

Ici, on a défini la base IMS.

2/ DEFINIR  LA BASE (DBD)                                : DBD010A

La base contient 3 segments.

3/ GENERER  LE DBD                                       : DBDGEN

4/ DEFINIR  LE PSB DE LOAD                               : PSB010L

5/ GENERER  LE PSB DE LOAD                               : PSBGEN

6/ LANCER LE JCL DE CHARGEMENT                           : DFSDDLT0
7/ ECRIRE LES PROGRAMMES (N : NUM PROG, GG : NUM DU GPE) : TPNGG
8/ COMPILER LE PROGRAMME                                 : COMPILE
9/ EXECUTER LE PROGRAMME                                 : COMPILE

Segment : Un élément de la hiérarchie de la BD IMS. Un segment regroupe des données similaires sur le plan thématique. Le segment le plus haut de la hiérarchie est la racine, et c'est aussi le plus général. Plus on descend la hiérarchie, plus les segments sont porteurs d'informations détaillées. Tous les segments inférieurs sont enfants, petits-enfants... de la racine.

DL/I peut accéder aux segments de façon séquentielle ou directe. L'accès séquentiel se fait de haut en bas et de gauche à droite. L'accès direct nécessite de fournir une clé qui décrit le chemin depuis la racine vers le segment recherché. On a une clé pour chaque segment du chemin, ce qui permet de créer une clé concaténée.

Champ clé : aussi appelé champ séquence. Se trouve dans un segment et permet de récupérer une occurence du segment. Le champ clé trie les occurences d'un champ de façon ascendante. Une seule clé par segment.

Blocs de contrôle IMS :
- Database Descriptor (DBD). Il décrit la structure physique d'une base de données après que les segments aient été définis.
	- Le DBD est créé à l'aide de déclarations DBDGEN.

```r
# Identifie la BD, avec un nom et mode d'accès
DBD     NAME=DBM010,ACCESS=(HDAM,VSAM),RMNAME=(DFSHDC40,2,20)
# Identifie le fichier qui contient la DB
DATASET DD1=DBM010A,DEVICE=3380,SIZE=4096
# Définit le segment racine (PARENT=0) et ses deux champs
SEGM    NAME=P010A,PARENT=0,BYTES=60
FIELD   NAME=(P010AK,SEQ,U),START=1,BYTES=06
FIELD   NAME=P010ALIB,START=7,BYTES=44
# Définit un segment enfant de P010A et ses deux champs
SEGM    NAME=P010B,PARENT=P010A,BYTES=40
FIELD   NAME=(P010BSER,SEQ,U),START=1,BYTES=05
FIELD   NAME=P010BLIB,START=6,BYTES=25
# Définit un segment enfant de P010A et ses quatre champs
SEGM    NAME=P010C,PARENT=P010A,BYTES=30
FIELD   NAME=(P010CPRO,SEQ,U),START=1,BYTES=06
FIELD   NAME=P010CLIB,START=7,BYTES=14
FIELD   NAME=P010CSER,START=21,BYTES=5
FIELD   NAME=P010CCAT,START=26,BYTES=1
DBDGEN
FINISH
END
```

- Programe Specification Block (PSB). Une BD n'a qu'une seule structure physique décrite par le DBD, mais peut avoir différentes vues, définies par le PSB.
	- Le PSB consiste en un ou plusieurs PCB (Program Communication Block).
	- Le PSB est créé à l'aide de déclarations PSGEN.

```r
# Décrit la BD, son nom, les options de traitement et la longueur de clé concaténée
# KEYLEN : On a 2 niveaux et pour chacun la clé a une taille de 6, donc KEYLEN = 12
PCB    TYPE=DB,DBDNAME=DBM010,PROCOPT=L,KEYLEN=12
# SENSEG : Segment Level Sensitivity. Définit les segments auxquels le programme a accès.
SENSEG NAME=P010A,PARENT=0
SENSEG NAME=P010B,PARENT=P010A
SENSEG NAME=P010C,PARENT=P010A
# La macro PSBGEN donne un nom à la sortie du module, et précise le langage de 
# l'application qui l'utilisera, ici COBOL
PSBGEN PSBNAME=PSB010L,LANG=COBOL
END
```

- Access Control Block (ACB). On l'utilise pour une application qui combine un DBD et un PSB sous une forme exécutable.
	- L'ACB est créé par ACBGEN.

Pour utiliser IMS et DL/I en COBOL, 3 déclarations sont indispensables :
- Une ENTRY, pour donner le contrôle au programme COBOL depuis DL/I.

```cobol
            ENTRY 'DLITCBL' USING pcb-name1
                                 [pcb-name2]
```

- Un GOBACK, pour donner le contrôle à DL/I depuis le programme COBOL.

```cobol
            GOBACK
```

- Un CALL pour réaliser une requête auprès des services DL/I sur une BD IMS.

```cobol
            CALL 'CBLTDLI' USING DLI Function Code
                                 PCB Mask
                                 Segment I/O Area
                                [Segment Search Arguments]
```

Lors d'un appel DL/I, on dispose de plusieurs codes fonction, utilisés pour réaliser des opérations sur une BD IMS et passés en première position au CALL.

```cobol
        01 DLI-FUNCTIONS.
           05 DLI-GU        PIC X(4)    VALUE 'GU  '.
           05 DLI-GHU       PIC X(4)    VALUE 'GHU '.
           05 DLI-GN        PIC X(4)    VALUE 'GN  '.
           05 DLI-GHN       PIC X(4)    VALUE 'GHN '.
           05 DLI-GNP       PIC X(4)    VALUE 'GNP '.
           05 DLI-GHNP      PIC X(4)    VALUE 'GHNP'.
           05 DLI-ISRT      PIC X(4)    VALUE 'ISRT'.
           05 DLI-DLET      PIC X(4)    VALUE 'DLET'.
           05 DLI-REPL      PIC X(4)    VALUE 'REPL'.
           05 DLI-CHKP      PIC X(4)    VALUE 'CHKP'.
           05 DLI-XRST      PIC X(4)    VALUE 'XRST'.
           05 DLI-PCB       PIC X(4)    VALUE 'PCB '.
```

Les codes fonction sont :
- GU, *Get Unique*. Utilisé pour l'accès direct, il cherche une occurrence donnée d'un segment sur la base de valeurs de champs, fournis dans les arguments de recherche attributaire.
- GN, *Get Next*. Similaire au `READ NEXT` en COBOL, il lit les segments de façon séquentielle de haut en bas et de gauche à droite. Il peut utiliser des arguments de recherche.
- GNP, *Get Next within Parent*. Il lit de façon séquentielle les occurrences de segments dépendants d'un parent donné.
- GHU, *Get Hold Unique*. Il précise que le segment sera mis à jour après l'avoir récupéré par accès direct.
- GHN, *Get Hold Next*. Il précise que le segment sera mis à jour après l'avoir récupéré par accès séquentiel.
- Pour la mise à jour des segments lorsqu'on travaille en GHU ou GHN :
	- ISRT, *Insert*. Il ajoute un segment à la base de données.
	- DLET, *Delete*. Il supprime un segment à la base de données.
	- REPL, *Replace*. Il modifie un segment de la base de données.
- Autres fonctions :
	- CHKP, *Checkpoint*.Option de récupération IMS.
	- XRST, *Restart*. Option de redémarrage de IMS.
	- PCB.

Le bloc de communication du programme, ou PCB (*Program Communication Block*) est le second paramètre utilisé dans le `CALL`. Il est déclaré en `LINKAGE SECTION`.

```cobol
       01 PCB-NAME.
	  *   Nom de la base de données traitée.
          05 DBD-NAME         PIC X(8).
	  *   Niveau de segment traité, jusqu'à 15.
          05 SEG-LEVEL        PIC XX.
	  *   Code statut de l'opération DL/I.
	  *   Espace : Succès. 
	  *   GB : Fin de fichier. 
	  *   GE : Segment demandé non trouvé.
	  *   GA : Est monté d'un niveau avant de trouver le prochain segment. Non qualifié.
	  *   GK : Est passé à un type de segment différent tout en restant au même niveau. 
      *        Non qualifié.
          05 STATUS-CODE      PIC XX.
	  *   Options de traitement.
          05 PROC-OPTIONS     PIC X(4).
	  *   Zone réservée à IMS.
          05 RESERVED-DLI     PIC S9(5).
	  *   Nom du segment traité.
          05 SEG-NAME         PIC X(8).
	  *   Longueur de la clé concaténée pour le segment de plus bas niveau traité.
          05 LENGTH-FB-KEY    PIC S9(5).
	  *   Nombre de segments auxquels le programme est sensible.
          05 NUMB-SENS-SEGS   PIC S9(5).
	  *   Plus grande longueur de la clé concaténée possible pour la vue actuelle.
          05 KEY-FB-AREA      PIC X(n).
```

Le SSA, ou *Segment Search Arguments*, est un paramètre optionnel du `CALL`. On l'utiliser pour travailler sur des occurrences de segments répondant à un ou plusieurs critères. On distingue :
- Les SSA non qualifiés. Ils décrivent le nom du segment à traiter lors du `CALL`.

```cobol
       01 UNQUALIFIED-SSA.
	  *   Nom du segment traité.
          05  SEGMENT-NAME  PIC X(8).
          05  FILLER        PIC X     VALUE SPACE.
```

- Les SSA qualifiés. Ils donnent des critères supplémentaires pour l'extraction des occurrences de segments. On peut réaliser plusieurs qualifications à l'aide des opérateurs booléens `&` et `|`.

```cobol
      *Un SSA à qualification unique. 
	   01 QUALIFIED-SSA.
	  *   Nom du segment traité.
          05  SEGMENT-NAME  PIC X(8).
          05  FILLER        PIC X(01)     VALUE '('.
	  *   Champ sur lequel effectuer la recherche.
          05  FIELD-NAME    PIC X(8).
	  *   Opérateur (EQ, NE, GT, GE, LT, LE)
          05  REL-OPR       PIC X(2).
	  *   Valeur recherchée
          05  SEARCH-VALUE  PIC X(n).
          05  FILLER        PIC X(n+1) VALUE ')'.

	  *Un SSA à qualification multiple.
	   01 QUALIFIED-SSA.
          05  SEGMENT-NAME  PIC X(8).
          05  FILLER        PIC X(01)     VALUE '('.
          05  FIELD-NAME1   PIC X(8).
          05  REL-OPR       PIC X(2).
          05  SEARCH-VALUE1 PIC X(m).
          05  MUL-QUAL      PIC X      VALUE '&'.
          05  FIELD-NAME2   PIC X(8).
          05  REL-OPR       PIC X(2).
          05  SEARCH-VALUE2 PIC X(n).
          05  FILLER        PIC X(n+1) VALUE ')'.
```

