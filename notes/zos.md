# Présentation de z/OS

## Miscellaneous

Une **piste** contient 40000 caractères.

## Introduction

### Définition d'un système d'exploitation

Un **système d'exploitation** (OS, *operating system*) est constitué par un ensemble de programmes dont l'objectif est de faciliter l'exploitation et l'utilisation des ordinateurs en prenant en charge des fonctions, telles que :
- La gestion des entrées et sorties vers les unités périphériques.
- Le partage des ressources (processeurs, mémoire, unités périphériques) entre les utilisateurs.
Il comprend également l'ensemble des programmes nécessaires à l'utilisation opérationnelle de l'ordinateur.

IBM propose des machines commerciales ou grands systèmes, appelés *mainframes* en anglais, sur lesquelles l'un de leur OS est installé, généralement **z/OS**. Ces machines peuvent être adaptées aux besoins des utilisateurs, par l'ajout de ressources supplémentaires (mémoire, stockage, processeurs, etc.) sans même avoir besoin de les arrêter. Aussi, les nouvelles versions de z/OS sont rétro-compatibles, ce qui permet à l'ensemble des programmes écrits sur une version antérieure de continuer à fonctionner.

### Principes des systèmes d'exploitation

Le rôle d'un système d'exploitation est de fournir un certain nombre de services aux programmes utilisateurs. Les programmes utilisateurs peuvent communiquer avec le système d'exploitation par l'intermédiaire des **appels système**, contenus dans des procédures correspondant aux **appels utilisateur**.

Le principe de fonctionnement est le suivant :
- Le programme utilisateur appelle la procédure en lui passant les paramètres de l'appel système.
- Le système d'exploitation prend le contrôle, évalue la validité des paramètres, exécute le traitement demandé et rend finalement le contrôle à la procédure, en lui fournissant les résultats du traitement.
- La procédure rend enfin le contrôle au programme utilisateur, en lui fournissant à son tour les résultats du traitement.

**SVC instruction** : SuperVisor Call instruction. Lorsqu'un programme émet une SVC, celui-ci est temporairement interrompu et une requête est soumise au système d'exploitation pour accéder à ses ressources et exécuter un traitement.

Le **code d'un programme** est stocké dans la mémoire à un moment donné. Les instructions sont chargées dans le processeur les unes à la suite des autres, à l'aide de pointeurs. Les caches permettent de stocker les instructions suivantes en mémoire, pour accélérer l'exécution du programme. Le cache est logé à côté du processeur pour accélérer la transmission d'informations de l'un à l'autre.

Un **processus** correspond à un programme qui s'exécute, accompagné des informations nécessaires à son exécution (données, mémoire, etc.). Les processus sont référencés dans la **table des processus**. Ils peuvent par ailleurs communiquer entre eux. Aussi, lorsqu'un processus en appelle un autre, on parle de **processus père et fils**. Le père est le processus appelant, le fils est le processus appelé. 

En COBOL, les processus ne tournent pas de façon concurrente. Si un sous-programme (fils) est exécuté, le programme principal (père) est mis en sommeil pour la durée de l'exécution.

**Registres** : Ce sont des morceaux de mémoire auxquels accède le processeur et qui contiennent des informations temporaires, des adresses à partir desquelles le système trouve les données, instructions, etc. nécessaires à l'exécution d'un programme.

Les fichiers sont des ensembles de données mis à disposition des programmes, et stockés sur un support, comme un disque ou une bande. Une fonction du système d'exploitation est de permettre aux programmes utilisateurs de gérer les fichiers indépendamment du matériel utilisé. Aussi, les appels système permettent de créer, supprimer, ouvrir, lire, écrire et fermer les fichiers.

À noter que les **bandes magnétiques** sont toujours utilisées du fait de leur faible coût et fiabilité.

Une **base de données** correspond à un ensemble de fichiers gérés par un système de gestion de bases de données (SGBD).

Pendant qu'un fichier est utilisé, on dispose :
- D'un **bloc de contrôle**. Il permet de connaître notre position dans un fichier en lecture ou en écriture.
- D'un ***buffer***. Il permet de stocker en mémoire une quantité fixe d'informations à lire ou à écrire dans un fichier.

La **communication avec le système d'exploitation** se fait à partir d'un terminal, au moyen d'un **interpréteur de commandes**. Ce dernier met à disposition de l'utilisateur un certain nombre de fonctions, qui permettent de lancer des opérations, de les surveiller et d'intervenir sur leur exécution, de manipuler des fichiers, d'obtenir des informations sur le système, etc.

### Les couches

Un système d'exploitation est structuré en **couches**. Chaque couche gère une fonction spécifique et s'appuie sur celle qui lui est immédiatement inférieure dans la hiérarchie. Un exemple de typologie en couches est le suivant :
- Couche n°0, allocation du processeur et multiprogrammation.
- Couche n°1, la gestion de la mémoire. La mémoire est nécessaire à l'exécution des programmes.
- Couche n°2, la communication opérateur-processus. Elle permet à l'utilisateur de spécifier des paramètres.
- Couche n°3, gestion des entrées et sorties.
- Couche n°4, les programmes utilisateurs.
- Couche n°5, les opérateurs qui utilisent les applications.

On peut ajouter d'autres couches, comme celle de la télécommunication.

Le **système z/OS** possède les couches suivantes, qui correspondent à des produits proposés par IBM :
- Software :
	- **DB2** : un système de gestion de bases de données.
	- **CICS** : *Customer Information Control System*. Il permet de réaliser des opérations transactionnelles. C'est un petit morceau de code qui s'exécute pour traiter une information donnée. Par exemple, créer un utilisateur "Dupont".
	- **JES** : *Job Entry Subsystem*, il accepte un job, récupère l'ensemble des ressources nécessaires à la réalisation d'une tâche, l'exécute, et gère ensuite les sorties compte-tenu des informations fournies par un fichier JCL.
	- **TSO** : *Time Sharing Option*, qui permet à plusieurs utilisateurs et systèmes de travailler simultanément avec les ressource d'un même système.
	- *Job Management*, qui s'occupe de prioriser les tâches à réaliser.
	- *Task Management*, qui s'occupe des tâches à exécuter dans le système.
	- *Data Management*, qui s'occupe des données.
- Hardware :
	- Instructions.
	- Micro-code.
	- z/OS.

Les couches soft (*software*) correspondent à la partie logicielle. Les couches hard (*hardware*) correspondent à la partie machine.

### La mémoire

Sur z/OS, il existe historiquement différents **types d'adressage**, avec le 24 bits, le 31 bits et aujourd'hui le 64 bits. Ils sont tous supportés par les grands systèmes actuels.

Ces adresses permettent de stocker temporairement du code, des instructions et de la donnée. Elles peuvent être accédées et partagées par plusieurs CPU. La mémoire constitue ainsi l'interface entre le CPU et les systèmes d'I/O.

Les deux grands types de **mémoire** :
- **ROM** : Mémoire en lecture seule, *read-only memory*.
- **RAM** : Mémoire en lecture et en écriture, *random access memory*.

### Notion de registres

Historiquement, les machines IBM disposaient de 16 **registres**, d'une taille comprise entre 4 octets (pour les anciennes versions) et 8 octets (aujourd'hui). Ce sont des emplacements mémoire dans lesquels est stockée l'information nécessaire à l'exécution des processus par le processeur. Plus précisément, ce sont des adresses situées au début de l'espace mémoire, qui sont donc accessibles très rapidement par le processeur.

Aujourd'hui, on dispose d'un nombre plus important de registres, allant jusqu'à une bonne soixantaine.

### Le système d'adressage

Les **adresses** disposent de 24 ou 31 bits. Dans un système 32 bits, le dernier bit renseigne sur le type d'adresse, 31 ou 24.

A partir d'un adresse, on peut accéder à un certain nombre de bits ou d'octects dans la mémoire ou dans un registre donné. Une adresse permet d'accéder aux 4096 caractères qui suivent dans cet espace mémoire ou registre. Pour passer aux caractères suivants, on a besoin d'une nouvelle adresse pour accéder à un nouveau registre.

### Les interruptions

Les **interruptions** sont des instructions gérées directement par la machine. On parle d'interruption lorsqu'on demande par exemple à un système de déclencher une lecture. Quand le système est prêt à nous fournir l'information liée à la lecture, il déclenche une interruption.

Lors d'une interruption :
- z/OS ou MVS prend le contrôle.
- Le PSW ou *Program Status Word*, qui renseigne sur l'état général du système, est alors appelé. Il analyse l'interruption, évalue si le système est capable de recevoir le type d'interruption reçu, et détermine si le système est en mode `READY`, `WAIT` ou `PROCESSING`.
- En fonction de l'état du système et du type d'interruption, PSW peut alors passer le contrôle à la routine de traitement. Le cas échant, le processus First Level Interrupt Handler et d'abord appelé et copie l'environnement du programme interrompu en mémoire.

Plus précisément, le PSW contient l'adresse de l'instruction exécutée pour un processeur. On distingue différents **types d'interruptions** : I/O, SVC (appels au système ou noyau), machine, programme, externes (réseau, périphériques, etc.), contrôleurs sur l'ordinateur (restart, etc.).

### La multiprogrammation

Pour un espace temporel continu de $t_0$ à $t_n$, imaginons différents niveaux, à savoir celui du CPU et ceux des différents programmes en cours d'exécution. Dans l'exemple proposé page 16, le programme 1 a besoin d'une information mise à disposition par le programme 2. Le programme 1 lance donc un SVC et une interruption. Le programme 2 reprend le contrôle, puis s'arrête après avoir appelé le programme 3, et ainsi de suite.

Ainsi, le temps CPU peut être partagé par plusieurs processus. On peut réaliser des traitements en parallèle seulement si l'on dispose de plusieurs processus. Autrement, les processus sont exécutés individuellement les uns après les autres.

### Intérêt de MVS et z/OS

L'acronyme **MVS** (*Multiple Virtual Storage*) correspond à l'ancien nom de **z/OS**. C'est un système de base pour des environnements de plus en plus complexes, avec l'utilisation de terminaux, la manipulation de bases de données, l'exécution de *batchs*...

Les ***batchs*** correspondent à des traitements par lot utilisés pour traiter de gros volumes de données. Les **transactions** sont utilisées pour traiter des données simples, atomiques.

### Historique de l'architecture

IBM introduit son premier *mainframe* en 1964 avec System/360, ou S/360. Les premiers grands systèmes disposant de MVS sont introduits avec System/370 dans les années 1970. Les systèmes XA sont apparus dans les années 1980, suivis des systèmes ESA dans les années 1990. Enfin, z/OS est apparu dans les années 2000.

### Les techniques utilisées

On ne peut pas être super-utilisateur d'un système z/OS, ce qui rend le système **inviolable**. Une machine IBM Z permet d'identifier le matériel défectueux et d'utiliser du matériel temporaire en remplacement. En attendant, un message est envoyé directement à IBM pour résoudre le problème. Cela permet ainsi l'**anticipation d'accidents**.

Les **méthodes d'accès** définissent la technique utilisée pour stocker et récupérer des données. Ce sont notamment les méthodes :
- **VSAM** : Accès virtuel aux disques.
- **VTAM** : Accès virtuel aux utilitaires de communication.

Les **sous-systèmes complémentaires** :
- **JES2** et **JES3**. JES3 permet d'automatiser des chaînes de production, en plus des autres capacités fournies par JES2. Dans les banques et assurances, on utilise généralement JES2.
- **TSO**, le temps partagé entre tous les utilisateurs d'une machine ou d'un système. Chacun dispose d'une partie des ressources CPU, mémoire, etc.
- **DB/DC**, *data base* et *data communication*. DB2 est utilisée comme base de données. Le CICS est utilisé pour la communication, pour la gestion des terminaux et transactions.
- **IMS/TM** (*Terminal Manager*).

Le **transactionnel** au niveau IBM correspond à CICS ou à IMS/TM.

### Ressources du système

Au niveau du **CPU**, un utilisateur est autorisé seulement à un certain taux et temps d'utilisation. En multiprogrammation, la priorité des utilisateurs est également considérée.

Quand on souhaite exécuter un programme, au niveau mémoire réelle, nous n'avons jamais l'espace nécessaire à l'exécution du code. On va définir un **fichier de pagination** dans lequel sont stockées des **pages**. Chaque page contient 4096 caractères et contient le **code dormant** à exécuter plus tard. Au moment de l'exécution, on cherche uniquement les pages dont on a besoin. Le fichier de pagination permet d'établir le lien entre la **mémoire réelle** et la **mémoire virtuelle**.

La **mémoire réelle** correspond à celle qui existe physiquement.

Chaque processus qui tourne a besoin de mémoire ; un espace virtuel est mis à sa disposition. Tous les blocs de 4k d'un même utilisateur est protégé par une clé qui interdit l'accès à d'autres utilisateurs. En cas d'exécution parallèle, tous les processeurs utilisés peuvent accéder au même fichier de pagination.

A noter qu'un programme COBOL ne peut pas exécuter de code de façon parallèle.

Les **allocations dynamiques** sont généralement utilisées pour allouer des fichiers et sont exécutées par z/OS. Allouer consiste à créer le bloc de contrôle pour accéder au fichier. L'accès au fichier est réalisé grâce à un catalogue dans lequel est renseigné le fichier au moment de sa création.

Les **canaux** permettent de gérer la communication entre la carte mère et ses unités, avec internet, etc. Dans un ordinateur IBM, plusieurs canaux peuvent être branchés au même disque. On peut donc accéder à une ressource avec 4 canaux au maximum.

Concernant les données :
- **Fichiers ou bases de données**. Une base de données est un ensemble de fichiers auxquels on accède forcément à travers un SGBD.
- **Catalogue**. Il contient les informations sur le stockage des informations dans les unités de disque.

Les **programmes** sont stockés dans des **bibliothèques**. Ils doivent être **ré-entrants**, c'est-à-dire que plusieurs processus peuvent utiliser le même programme en simultané. Le chargement est **optimisé**, c'est-à-dire que certains programmes ou certaines bibliothèques résident en mémoire (et donc en pagination) pour accélérer leur exécution.

### Quelques notions

Quand on veut exécuter un programme *batch*, on crée un ***job***. Un job est découpé en **étapes** ou *steps*, exécutées de façon séquentielle. Une tâche correspond à une étape, et correspond à l'unité de travail pouvant prétendre à une exécution CPU.

Pour créer, soumettre et exécuter les jobs, on utilise le **JCL** (*Job Control Language*). Il renseigne à z/OS quelles sont les ressources demandées (espace mémoire nécessaire, fichiers utilisés, etc.) et les critères d'utilisation (partager ou non les fichiers accédés, etc.).

## Les apports de z/OS

### MVS/ESA : Le SYSPLEX

Le **SYSPLEX** est l'un des apports de z/OS. Il permet :
- L'assemblage d'éléments matériels et de services logiciels.
- D'augmenter les capacités du système à la demande.
- Le XCF, ou *Cross-System Coupling Facility*.
- De 1 à 8 systèmes MVS suivant le nombre de processeurs disponibles.
- Les commandes JES2. Il s'occupe de gérer les tâches, mais aussi les bandes magnétiques et imprimantes par exemple.

### MVS/ESA : Les évolutions et simplifications

IBM a **simplifié** beaucoup de choses, comme les terminaux par exemple qui sont aujourd'hui automatisés dans leur configuration. On dispose également d'**interfaces de programmation** pour contrôler les opérations, en créant par exemple des filtres sur les informations à afficher. On dispose également d'un utilitaire de ***recovery***, qui permet au système de revenir automatiquement à un état antérieur fonctionnel du système ou du programme.

Chaque système dispose d'une console, un écran sur lequel passent les messages du système.

L'**initialisation** du système correspond au démarrage de l'ordinateur. Historiquement, un grand nombre d'étapes devaient être exécutées avant d'atteindre le système. Le nombre de ces étapes a été réduit ; elles tournent en arrière-plan après le démarrage pour permettre à l'utilisateur d'utiliser directement le système.

### MVS/ESA : Contrôle des opérations et évolutions dans les usages

Le système peut être contrôlé par un utilisateur à l'aide de TSO.

**MCS** (*Multiple Console Support*) est utilisé pour configurer le système et définir les ressources utilisées par quelqu'un.

**HCD** (*Hardware Configuration Definition*) est un programme qui permet de définir le matériel utilisé. Il est exécuté avant de passer une commande chez IBM.

Le **JCL** a connu un certain nombre d'évolutions. On peut notamment stocker des procédures pour les appeler plus rapidement ; on parle de **procédures résidentes**. Il octroie également un côntrole plus important sur la manipulation des périphériques de sortie.

### MVS/ESA : Les tâches

Que ce soit pour une transaction ou un traitement par lot, le **gestionnaire standard de tâches** alloue un certain nombre de ressources, libérées avant la fin du processus.

Le **gestionnaire de tâches multi-transactions** permet à une transaction de rester active entre des conversations intégrées. Il autorise ainsi l'allocation et la désallocation répétée de ressources.

Le **choix du gestionnaire de tâches** dépend des caractéristiques du programme.

### Gestion des espaces

Le système d'exploitation z/OS possède plusieurs sous-systèmes utilisés pour accéder à des espaces et les manipuler. Ce sont :
- **SMS** (*Storage Manager System*). On lui donne tous les disques à gérer, et il décide ensuite de l'emplacement (disque, volume) adapté pour y déposer un fichier. Il gère ainsi les fichiers de leur création à leur suppression, ainsi que les problèmes de performance, de disponibilité et de gestion de l'espace. Par ailleurs, il optimise également la centralisation et l'automatisation de la gestion des disques externes.
- **Produits DFSMS** (*Data Facility SMS*). Ce sont les macros et programmes utilisés par SMS et les autres produits DF.
- **Classes et groupes SMS**. Directives données au système pour lui dire à quel classe ou groupe appartient un programme, pour optimiser l'exécution des tâches.
- **ISMF** (*Interactive Storage Management Facility*).
- **DFHSM**.
- **DFDSS**. Il contient l'ensemble des routines nécessaires pour accéder à un disque.
- **DFSORT**. Il contient le programme de tri.
- **RACF** (*Resource Access Control Facility*). C'est le sous-système de sécurité qui gère les groupes d'utilisateurs, utilisateurs, et leur accès à des ressources du système. Il produit également des statistiques d'utilisation et rapports.

### SMS - BUTS

Dans le nom d'un fichier, on peut donner des informations qui permettent de renseigner par exemple la durée de validité du-dit fichier.

Par exemple, dans un système avec HDD et SSD, le SMS va donner les espaces SSD aux utilisateurs avec une plus grande priorité, et les HDD aux autres.

### ISFM

L'ISFM gère des règles de gestion des volumes et données.

### RACF

C'est un sous-système de sécurité dans lequel on crée un groupe pour un ensemble de profils d'utilisateurs, et différents droits sont accordés aux groupes. Ces droits renvoient aux volumes et données accessibles, aux heures travaillées pour une thématique donnée, etc.

## Gestion des données

La gestion des données correspond à leur lecture et écriture, le type de stockage, etc.

### Les fichiers

Les **fichiers** sont des ensembles d'**enregistrements** et sont connus via un **DSN**, ou *Data Set Name*.

Un **fichier** peut être **multi-volumes** et donc logé sur plusieurs volumes, et sur un volume donné. On peut ainsi avoir jusqu'à 16 morceaux de ce même fichier. Un **morceau** correspond à une portion de l'information, dont la taille est fixe et définie par l'utilisateur. Lorsque la taille du fichier est telle que l'on dépasse ces 16 morceaux, le système renvoie une erreur ABEND ; il faut les redimensionner pour stocker l'information contenue dans le fichier. Tous les morceaux réunis donnent le fichier dans son intégralité. La lecture du fichier nécessite donc d'accéder à tous ces morceaux, cette étape étant réalisée automatiquement par le système.

Un ***batch*** est un programme qui est maître de son environnement : on précise l'ensemble des fichiers nécessaires à son exécution, et il gère leur ouverture et leur fermeture.

La **structure** du fichier est imposée par le programme utilisé pour le créer, en plus des besoins de l'analyste. Par ailleurs, un fichier est forcément créé par un utilitaire ou un JCL.

### Les attributs des fichiers

Chaque fichier possède un nom et contient de l'information. Les systèmes d'exploitation lui associent également des métadonnées : taille des enregistrements, date et heure de création et de mise à jour, indication de mise à jour, protection, taille du fichier, etc. Ce sont les **attributs** du fichier !

Les indicateurs de mise à jour sur les fichiers permettent de savoir s'il faut ou non réaliser un *backup* de celui-ci.

### Les opérations sur les fichiers

Au travers des **appels système**, les systèmes d'exploitation autorisent un certain nombre d'**opérations sur les fichiers**, dont la création, suppression, modification du nom, ouverture, lecture, etc.

### Les fichiers et PDS

![[fichiers_pds_complet.png]]

Imaginons un disque constitué de plusieurs plateaux. Ce disque ou volume porte un nom, son **label**. 

Ce label permet de pointer sur une table, la **VTOC**, ou *Virtual Table Of Contents*, qui renseigne la liste des fichiers et de leurs adresses physiques. La VTOC dit par exemple "ce fichier se situe sur la piste 15.43". La VTOC renseigne aussi les catalogues !

Entre chaque enregistrement ou bloc, il existe un *gap* qui possède une valeur spéciale qui permet aux têtes de lecture qui savoir où commencent et se terminent les fichiers. Un repère identifie le secteur 0, ou démarrage de la piste du fichier.

En écrivant des blocs de 100 caractères, beaucoup d'espaces de *gap* sont nécessaires. Plus on écrit des blocs de taille importante, plus on optimise l'espace de stockage disponible car on diminue le nombre de *gaps* nécessaires. En revanche, les gros blocs nécessitent plus de temps pour être transférés entre différentes unités ou différents canaux. Il faut donc trouver un compromis entre espace de stockage et vitesse de manipulation.

Les types de fichiers :
- Les **fichiers séquentiels (SDS)** sont ceux pour lesquels les enregistrements sont écrits les uns à la suite des autres. Les bandes magnétiques utilisent exclusivement les fichiers séquentiels.
- Les **fichiers partitionnés (PDS)** ou *library* sont constitués de plusieurs morceaux. Le premier morceau, appelé ***directory***, contient et renseigne le nom de tous les autres morceaux restants ou **membres** contenus dans le PDS.

Sur z/OS, on dispose uniquement d'un seul 1 niveau hiérarchique. Une bibliothèque ne peut pas contenir plusieurs niveaux, autrement dit plusieurs bibliothèques imbriquées les unes dans les autres.

### L'enregistrement logique

Un **enregistrement logique**, ou un bloc, est la notation abstraite d'une cellule de taille fixe ou variable. Un fichier est organisé en enregistrements logiques.

On lit plusieurs blocs jusqu'à atteindre la taille maximale du *buffer*. Ces blocs contiennent un certain nombre d'**enregistrements logiques** et sont de taille fixe ou variable, selon les besoins de l'application. En revanche, les fichiers de taille variable sont plus compliqués à gérer et sont donc rares.

Plus les blocs sont petits, plus l'on crée des I/O pour lire la totalité du fichier. Chaque lecture physique possède un certain coût temporel. Plus les blocs sont grands, plus il faut de temps pour les manipuler en entier.

### L'enregistrement physique

La notion abstraite d'**enregistrement logique** est couplée à celle de l'enregistrement physique (exprimé sous forme de bloc ou non), qui dépend du support de stockage et de sa forme. Dans le cas d'un disque, l'enregistrement physique dépend de la taille des cylindres, des secteurs, de la répartition sur le disque, etc.

![[stockage_physique.png]]

Le début d'une piste se situe toujours au centre de l'unité de stockage, qui est de forme cylindrique. Sur la page 45, on dispose de 4 plateaux et de huit pistes, avec 2 pistes par plateau. Plus il y a de plateaux, plus on peut lire d'informations en simultané.

### Les bases de données

Le **TP**, ou *Transaction Processor*, est partagé entre plusieurs utilisateurs en simultané. Il gère les ouvertures et écritures de fichiers.

Les bases de données sont créées et gérées par un SGBD, DB2 dans le cadre de cette formation.

Dans DB2, une base de données comprend 1 ou plusieurs tables. Le SGBD peut également donner des droits à un programme pour accéder à certaines données. Aujourd'hui, cette partie est davantage gérée par RACF.

Les DA (*Data Administrator*) et DBA (*Data Base Administrator*) définissent de quelle manière les données sont organisées dans les BD. La structure d'une base de données est ainsi indépendante de celle du programme qui l'exploite.

## Gestion des catalogues

Les **catalogues** sont des fichiers qui contiennent des informations sur tous les autres fichiers, et qui peuvent pointer vers d'autres catalogues. Il existe les catalogues systèmes et utilisateurs.

Dans le JCL, nous pouvons décider si l'on catalogue ou non un fichier. C'est cependant recommandé. En effet, autrement, on ne sait pas où se trouve un fichier. Il faut alors parcourir l'ensemble des disques manuellement pour le retrouver.

Le système z/OS autorise deux niveaux de catalogues au maximum.

![[catalogues.png]]

**MASTERCAT** : Ce sont les catalogues systèmes, inaccessibles aux programmeurs.

**USERTCAT** : Ce sont les catalogues utilisateurs.

### Les noms de fichiers

Le **nom simple** d'un fichier doit être inférieur à 8 caractères. Plusieurs morceaux de 8 caractères ou moins peuvent être associés pour former un **nom qualifié** de 44 caractères maximum pour les disques, et 17 pour les banques magnétiques.

Chaque morceau s'appelle *qualifier* ou qualifieur. Le premier correspond généralement au *i-qualifier*, qui est utilisé pour la gestion des disques et le rangement des fichiers.

> **Exemple** : Le fichier USER1.SJD.FORMATION sera rangé dans le dossier USER1. En effet, USER1 correspond au plus haut qualifieur.

### Les sauvegardes

Les **sauvegardes** permettent de conserver une copie des fichiers en vue d'une restauration ultérieure, lorsqu'une information a été perdue. Elles peuvent être réalisées sur un fichier, un disque, ou de manière incrémentale. Le mode incrémental concerne uniquement les fichiers qui ont été modifiés par rapport à une sauvegarde précédente.

### Alias et volume

Un même volume peut posséder un ou plusieurs **alias**.

### Intérêt des catalogues utilisateurs

Il faut éviter d'encombrer le *Master Catalogue*, car il faut sinon lui retirer des fichiers pour qu'il soit à nouveau utilisable.

Les catalogues utilisateurs permettent d'assurer l'intégrité et la sécurité des données.

### ICFCAT

L'**ICFCAT** (**ICF**, *Integrated Catalog Facility*) correspond à une couche logicielle utilisée pour les catalogues. Il comprend deux niveaux de catalogues :
- **BCS**, Basic Catalogue Structure. C'est une information permanente et il en existe plusieurs par volume.
- **VSAM VDS**, ou **VVDS** qui remplace le BCS et qui correspond à une extension de la VTOC. Il n'y en a qu'un seul par volume.

### DADSM

Le **DADSM** interdit que deux fichiers de même nom existent. Au moment de la création d'un fichier, il faut renseigner :
- Sa **taille primaire**, l'espace qui lui est alloué directement à la création, comme 10 pistes par exemple. Une fois les 10 pistes dépassées, on passe à la demande de taille secondaire.
- La demande secondaire permet de demander des morceaux. Une fois qu'on a atteint 16 morceaux, il n'est plus possible d'en ajouter. La **taille secondaire** permet d'ajouter un certain nombre de pistes par précaution, au cas où on aurait sous-estimé la taille nécessaire au fichier.

### Méthodes d'accès

La **méthode d'accès** correspond à une **méthode d'organisation** et à une **technique d'accès**.

On dispose de plusieurs **méthodes d'organisation** des fichiers dont dépend l'accès aux informations, à savoir :
- **Séquentielle** → "Donne-moi le premier enregistrement, puis le second, etc.". La lecture du fichier est incrémentale.
- **Directe** → "Je veux la piste n°15.53".
- **Partitionnée** → "Je veux accéder au membre n°5 situé dans ce *directory*".
- **VSAM**. On a seulement accès aux jeux ESDS (fichiers séquentiels avec des enregistrements successifs), KSDS (accès avec une clé identifiant, il nécessite donc 2 I/O pour accéder au fichier par sa clé puis envoyer la valeur) et RRDS (fichier avec numéro relatif par rapport au début du fichier). Les fichiers RRDS sont les plus rapides. Les KSDS sont un peu plus lents, car il y a 2 I/O. On n'a pas le droit d'accéder directement à un enregistrement spécifique d'un ESDS, il faut le parcourir.

Les **techniques d'accès** sont :
- *Queued*. On fait une lecture anticipée de blocs physiques. On charge le cache avec un paquet de 50 blocs ou pistes, pour accélérer l'accès aux informations.
- *Basic*. "Je lis la piste 5, le bloc numéro 4". C'est la technique de base utilisée en lecture et en écriture.

### L'organisation partitionnée

Un **fichier partitionné** réside forcément sur un seul disque ou volume, car il doit être indexé. Il comprend un répertoire et un ensemble de membres qui représentent les données.

On accède aujourd'hui aux fichiers partitionnés par QSAM.

### Organisation d'un PDS

Un **PDS** correspond à un *Partitioned Data Set*. Il comprend une partie qui représente le répertoire, ou *directory*, puis une autre qui comprend tous les membres.

![[pds.png]]

Si on modifie un membre et que la modification est sauvegardée, le membre dans son ancien état persiste sous forme fantôme dans le répertoire et un nouveau membre est créé dans son nouvel état. On peut conserver les fantômes pour réaliser du *versioning*. Les fantômes sont inconnus au répertoire, seul le nouvel état est considéré.

## Job Entry Subsystem

### Notion de job

Un **job** est un ensemble d'étapes exécutées successivement. Le temps total d'exécution du job est appelé *ellapsed time*. Chaque job dispose d'un certain temps de traitement processeur. On peut nous-même spécifier à quelle durée chaque job a droit.

Pour chaque étape, on a toujours **trois phases** :
- **Phase initiale**, *initiator*, durant laquelle on prépare l'ensemble du système pour l'exécution. On vérifie par exemple que les fichiers existent dans le système ou puissent être créés et catalogués. Il y a aussi la partie allocation.
- **Exécution du programme**. Cette partie n'est pas gérée par JES.
- **Phase finale**, *initiator*, durant laquelle on choisit quoi faire des données, comme les sauvegarder ou non. La suppression, la sauvegarde et le catalogage des données sont complètement gérés par JES.

### Le spooling

Le **spooling**, ou *Simultaneous Peripheral Operational OnLine*, correspond à un gros fichier dans lequel on met de l'information en entrée ou en sortie de JES. On distingue les spool d'entrée et de sortie. Le spool vérifie que le JCL soit correct. S'il l'est, on le met en attente, autrement on renvoie une erreur.

![[spool.png]]

Au niveau de la carte JOB, on définit une classe d'entrée et une priorité de sélection. À chaque classe d'entrée correspond une file d'attente sur disque.

### Fonction de JES

**Avant l'exécution**, JES gère les opérations suivantes :
- Entrée et spooling.
- Gestion de la file des jobs.
- Gestion des INIT batch, ou *initiator batch*. Il gère la priorité des jobs compte-tenu de leur classe (ex. "programme lent", auquel cas on lui accorde une priorité inférieure si possible).
- Sélection des jobs pour exécution, compte-tenu de la priorité.

**Pendant l'exécution**, JES réalise un suivi du temps CPU. En cas de dépassement, le job est arrêté. JES a également accès au SPOOL (SYSIN, SYSOUT).

**Après l'exécution**, JES gère les imprimantes et récupère les SYSOUT pour en faire quelque chose.

### Avant exécution

S'il y a une erreur dans le JCL d'un job, JES réalise un JOB FLUSH. Autrement, il met le job dans la JOB QUEUE, la file d'attente des programmes à exécuter.

![[jes_avant.png]]

### Pendant l'exécution

L'initiateur traite la demande du job par rapport à l'interface du sous-système. Il initialise le début du temps processeur, créer la tâche pour exécuter le programme, exécute le programme et récupère les paramètres qu'on a pu stocker dans JES, puis récupère les SYSOUT. Il vérifie continuellement si on dépasse la limite de temps accordée au processus, ou l'OUTLIM associé au programme sur le nombre de lignes à écrire par exemple.

![[jes_pendant.png]]

Durant l'exécution du programme, les informations à écrire à la sortie sont d'abord stockées dans la mémoire, et plus précisément dans le *buffer*.

### Après l'exécution

L'initialisation du sous-système démarre les *writers*, qui prennent les informations SYSOUT et les écrivent sur le disque.

![[jes_apres.png]]

## Gestion des tâches

### La tâche

Une **tâche** est déclenchée par un **job**. Un job correspond à du code JCL isolé dans un fichier et qui commence toujours par :

```
// JOB : Travail
// EXEC PGM=pgm1   -> le programme pgm1 génère une tâche
```

La carte JOB définit le travail ou job. On a ensuite différentes cartes EXEC qui appellent des programmes et qui correspondent chacun à une tâche. Pour chaque carte EXEC, un espace mémoire est alloué.

La tâche correspond donc à l'**exécution d'un programme**, pour laquelle un ensemble de ressources est mis à disposition.

### Les états d'une tâche

À partir du moment ou un programme est mis en mémoire, il est mis en mode READY, signifiant qu'il est prêt à travailler. Quand il travaille, il est mis en mode ACTIF. Il peut être mis en attente avec le mode WAIT à certains moments, avant de repasser en mode ACTIF, jusqu'à la fin du programme.

![[etats_taches.png]]

### Le dispatcher

Le ***dispatcher*** gère la priorité des tâches les unes par rapport aux autres. C'est la même chose pour l'espace des adresses. Pour l'adresse prioritaire, la tâche de priorité maximale et dont le statut est READY est démarrée.

![[dispatcher.png]]

### Synchronisation

Les tâches d'un même espace adresse ne sont pas indépendantes, mais elles n'agissent pas non plus directement les unes sur les autres. Ainsi, les tâches d'un même espace adresse doivent être exécutées de façon séquentielle.

Elles ont besoin du système et d'un espace mémoire pour communiquer les unes avec les autres. La rédaction d'un programme doit tenir compte de cet impératif.

### Sérialisation

Une **tâche** a besoin du code, de la mémoire, des données, des périphériques, de temps CPU, etc. Ce sont les ressources nécessaires à la tâche. Quand une tâche attend une de ces ressources, elle est mise en mode WAIT.

Il existe des utilitaires sous TSO qui mettent un verrou sur une ressource donnée. Si on oublie d'enlever le verrou et qu'on lance la compilation du programme, l'exécution reste en attente jusqu'à ce que le verrou soit retiré. Pour cela, il suffit d'arrêter l'utilitaire en question.

### Notion de ressource

On distingue :
- Les **ressources locales**, attribuées à une seule tâche.
- Les **ressources globales**, attribuées à plusieurs tâches. On les dit "critiques", c'est-à-dire attribuées à une seule tâche à un moment donné, mais qu'on peut partager entre plusieurs utilisateurs en mode lecture.

Un fichier en lecture est donc partageable, tandis que le mode écriture est critique.

### Exemple

![[exemple_p1.png]]

La tâche X lit l'enregistrement CPT, l'édite puis réécrit l'enregistrement. La tâche Y en fait de même. Lorsqu'elles sont exécutées avec une même ressource utilisée dans deux programmes différents, on peut obtenir des résultats aberrants si les verrous sont mal gérés.

![[exemple_p2.png]]

Pour les **mises à jour concurrentes**, il faut donc mettre un verrou en place avec ENQ sur l'enregistrement, réaliser le traitement, puis retirer le verrou avec DEQ. Tant que la ressource est bloquée par le verrou de la tâche X, une autre tâche Y qui souhaiterait y accéder est mise en mode WAIT, jusqu'au DEQ.

### L'interblocage

![[interblocage.png]]

Posons un problème en mode **multiprogrammation**. Pour rappel, dans un programme, nous sommes toujours en attente d'une ressource donnée.

La tâche 1 à $t_1$ demande la ressource 2 et la met en ENQ. La tâche 2 à $t_2$ demande la ressource 2 et la met en ENQ. La tâche 1 au $t_3$ demande la ressource 2, mais n'y a pas accès. La tâche 2 au $t_4$ demande la ressource 1, mais n'y a pas accès. Le système détecte cela et met en place un *deadlock* ; c'est de l'**interblocage**. C'est la tâche qui a réalisé le moins de travail qui est mis en mode WAIT. La tâche qui a réalisé le plus de travail obtient ainsi la ressource qu'elle a demandée.

Dans un job, cela n'arrive pas, car les tâches sont exécutées de façon séquentielle, les unes après les autres. Par contre, ce cas peut arriver lorsque plusieurs jobs fonctionnent en parallèle.

### Éviter les *deadlock*

Pour éviter un deadlock, on peut choisir de ne demander qu'une ressource à la fois, ou de grouper toutes les demandes dans la même unité logique de travail.