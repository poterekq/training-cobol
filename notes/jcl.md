# JCL

## Introduction

Le JCL, ou *Job Control Language*, est un langage utilisé pour communiquer avec z/OS et lui donner des instructions.

Le JES, ou *Job Entry Subsystem*, est utilisé comme interface entre JCL et z/OS et gère notamment les I/O et les périphériques (imprimantes, lecteurs, etc.).

### Définition du JCL

Le **JCL** est un **langage codifié** qui permet de décrire un **travail** ou ***job*** à exécuter. Il fait appel à certaines fonctions du système pour exécuter un job, et décrit (1) ses conditions générales d'exécution, (2) le nom de chacune de ses étapes et conditions d'exécution, (3) les ressources physiques allouées à ces étapes et leur utilisation.

Le rôle principal de JCL est de préciser à un initiateur quels sont les éléments nécessaires à l'exécution d'un job.

> **Note :** Dans les environnements *mainframe*, un programme peut être exécuté en mode batch ou transactionnel. Le JCS sert spécifiquement à l'exécution en mode batch.

### Notion de ressource

Pour qu'une tâche puisse s'exécuter, il lui faut des **ressources**, comme de la mémoire, un ou plusieurs CPUs, des périphériques, des blocs de contrôle, des données, un programme d'instructions, etc.

Lorsqu'une ressource est indisponible pour une tâche, le statut de cette dernière passe en attente `WAIT`, et devient seulement active lorsque le verrou sur la ressource en question est libéré.

Une ressource est **locale** lorsqu'elle est utilisée par une seule tâche. Si la ressource est **globale**, il faut préciser si elle est **critique** (attribuée à une seule tâche à un moment donné) ou **partageable**.

### Notion de job

Un **job** correspond à un ensemble d'**étapes** (*steps*) exécutées de façon séquentielle. Il possède trois grands types de déclarations JCL, à savoir :
- Une carte `JOB`, pour identifier l'unité de travail que l'on souhaite soumettre au système.
- Une ou plusieurs cartes `EXEC`, selon le nombre d'étapes à réaliser au sein du job.
- Une ou plusieurs cartes `DD`, pour identifier les jeux de données d'entrée et de sortie.

Par ailleurs, les étapes d'un job peuvent être **conditionnées** compte-tenu de plusieurs critères, notamment sur la base des codes d'erreur obtenus pour chacune. Par exemple, si la première étape d'un job entraîne un `ABEND`, l'étape 4 sera exécutée plutôt que l'étape 2.

Lorsque les jobs sont soumis avec la commande primaire `SUB`, ils sont d'abord stockés dans le *spool* d'entrée de JES. Si leur contenu est valide, qu'ils sont marqués comme étant `READY` et qu'ils répondent positivement à un certain nombre de conditions de classe et de priorité, ils sont exécutés. À la fin d'un job et s'il n'y a pas eu d'erreur, JES sort l'ensemble des fichiers `SYSOUT` demandés et élimine les fichiers temporaires alloués au moment de l'exécution.

### Le spooling

Le terme de ***spooling*** correspond à l'abréviation de *Simultaneous Peripheral Operation Online*. C'est une fonction du système qui sauvegarde des données dans un fichier de spooling, de façon concurrente au traitement, pour être traités ou imprimés plus tard. 

Les données d'entrée passent dans le ***spool* d'entrée**. Les tâches sont ensuite exécutées par JES compte-tenu des priorités (`PRTY`) et classes (`CLASS`, `SYSOUT`, `MSGCLASS`) assignées à leurs jobs. Enfin, les données de sorties sont passées au ***spool* de sortie** et les messages à l'**imprimante**.

![[spool.png]]

### Notion de classe

Au sein des fichiers JCL, il existe la possibilité de définir des **classes d'entrée et de sortie**. Une classe prend une seule valeur, à savoir n'importe quel caractère [A-Z] ou chiffre [0-9].

Il est possible d'assigner une **classe d'entrée** à un job, directement dans la déclaration de sa carte avec le paramètre `CLASS`. *In fine*, cela sert à regrouper plusieurs jobs aux caractéristiques similaires, comme une heure de lancement particulière, ou pour l'accès à des périphériques externes spécifiques par exemple.

Les **classes de sortie** peuvent être assignées à un job ou à un fichier et servent à définir un mode de gestion particulier des sorties en question. Ces classes sont déclarées à l'aide :
- Du paramètre `SYSOUT` de la carte `DD`. Il est utilisé pour afficher la sortie du JCL dans la zone de *spool* notamment.
- Du paramètre `MSGCLASS` de la carte `JOB`. Il est utilisé pour spécifier le format d'une sortie, ou encore pour définir le système utilisé pour afficher ou imprimer des messages.

> **Note :** La queue de sortie de JES peut être consultée avec **SDSF** en `m.5`.

### La priorité

Le paramètre `PRTY` est utilisé pour assigner une **priorité** à un job, ou à une classe d'entrée. Ainsi, il s'utilise comme paramètre de la carte `JOB`. 

Le paramètre `PRTY` prend une valeur numérique comprise entre 0 et 15 pour JES2, ou 0 et 14 pour JES3. Plus cette valeur est élevée, plus la priorité est grande, et plus le job en question est exécuté rapidement. Des jobs de priorité égale sont exécutés selon un principe FIFO.

### Fonctions de JES

Le système z/OS partage la gestion des jobs et des ressources avec le ***Job Entry Subsystem*** (JES). JES reçoit les jobs, planifie leur traitement par z/OS et détermine de quelle manière les sorties d'un job sont traitées. Il gère également la file d'attente des jobs, leur priorité, ainsi que les jeux de données d'entrée et de sortie qui leur sont associés.

Un **initiateur** est une partie intégrante de z/OS qui lit, interprète et exécute le JCL. Il se sert des déclarations fournies dans le JCL pour spécifier les ressources utilisées par chaque job, après que celui-ci ait été libéré ou dispatché par JES. L'initiateur se charge également de la mise en place d'un environnement d'exécution dans un espace adresse, pour y lancer un job. L'existence de plusieurs initiateurs disposant chacun de leur propre espace adresse permet l'exécution de plusieurs jobs en parallèle.

Bien que JES réalise une partie du traitement JCL, il sert avant tout de chef d'orchestre. C'est l'initiateur qui se charge réellement le plus du traitement JCL.

![[basic_batch_flow.png]]

L'**exécution** d'un job suit donc grossièrement les **étapes suivantes** :
- Sélection d'un job depuis la file d'entrée. Cette étape est réalisée par JES.
- Avant le traitement du job, réservation des ressources d'entrée et de sortie nécessaires à l'exécution. Durant cette étape, on s'assure qu'il n'y ait pas de conflit d'usage, et on cherche les programmes utilisés lors de l'exécution.
- Pendant le traitement, gestion des données spoolées `SYSIN` et `SYSOUT`.
- Après le traitement, libération des ressources utilisées par les jobs terminés, pour les rendre disponibles à d'autres jobs.

![[job_processing_flow.png]]

Durant la vie d'un job, JES et z/OS contrôlent différentes **phases du traitement**. En effet, les jobs sont organisés sous forme de **files**, avec :
- La **file de conversion**. Elle contient les jobs qui attendent d'être lancés.
- La **file d'exécution**. Elle contient les jobs qui sont actuellement exécutés.
- La **file de sortie**. Elle contient les jobs qui attendent que leurs sorties soient générées.
- La **file de copie en dur**. Elle contient les jobs dont les sorties sont en cours de production.
- La **file de purge**. Elle contient les jobs qui attendent d'être purgés du système.

### Les ordres JCL

Les **ordres de base** sont indispensables à la déclaration d'un job JCL. Ces ordres sont :
- `JOB`, qui définit l'ensemble du **travail**.
- `EXEC`, qui définit les **étapes** du travail.
- `DD`, qui définit les **données** utilisées en entrée et en sortie.

Les **ordres de procédure** sont utilisés pour décrire des blocs d'instructions réutilisables, et sont similaires à des fonctions dans leur philosophie. Ces ordres sont :
- `PROC`, qui est utilisé pour la **déclaration** d'une procédure.
- `PEND`, qui correspond à la **fin de déclaration** d'une procédure.

Il existe de nombreux autres ordres, dont les usages varient en fonction des besoins des utilisateurs.

### Définitions

Un **job** correspond à une unité de travail, constituée d'une ou plusieurs étapes. Un fichier JCL contient un seul et unique job, nommé et déclaré à l'aide de la carte `JOB`, suivie de paramètres. 

```jcl
//JOBNAME  JOB *PARAMS
//*...
```

Une **étape** est une partie nommée d'un job, renseignée par la carte `EXEC` et qui appelle un programme ou une procédure (cataloguée ou instream). Elle utilise des ressources du système, programmes et fichiers. Enfin, l'exécution d'une étape peut être conditionnée à l'aide de paramètres supplémentaires. 

```jcl
//JOBNAME  JOB *PARAMS
//ETAPE1   EXEC [PGM,PROC]=...
//*...
```

> **Note :** Le nom d'un job ou d'une étape commence en 3e colonne et possède une taille de 8 caractères au maximum.

### Syntaxe du JCL

Dans un fichier JCL, les **déclarations** — outre celles dédiées aux données in-stream — commencent par un `//` et possèdent une longueur de 71 caractères au maximum. 

Si la longueur maximale de la ligne est insuffisante, une déclaration peut se faire sur une première ligne terminée par une virgule, suivie d'une ou plusieurs autres lignes démarrant par `// `. Attention, il y a au moins un espace ` ` entre les `//` et la suite de la déclaration. En effet, cette suite débute forcément entre les colonnes 4 et 16.

```jcl
//MONJOB  JOB CLASSNAME=A,
// MSGCLASS=H,NOTIFY=&SYSUID,TIME=(,1)
```

En JCL, les **ordres** débutent par :
- `/*`, pour marquer la fin de données in-stream.
- `//*`, pour marquer la présence d'un commentaire de ligne.
- `//`, pour toutes les autres situations.

À noter qu'une ligne dont le seul contenu correspond à un `//` marque la **fin du JCL**. Toute autre ligne qui serait située après celle-ci est ignorée.

Les **paramètres** d'un ordre sont toujours séparés par une virgule, sans espace. Lorsqu'il existe plusieurs valeurs possibles pour un paramètre donné, elles sont mises entre parenthèses et restent séparées par une virgule.

```jcl
NOMPARM=valeur
NOMPARM=(v1,v2,v3)
```

En plus des commentaires de ligne `//*`, il est également possible d'ajouter un commentaire à la fin d'un ordre, en le séparant simplement du reste de la déclaration par un espace.

```jcl
//JNAME   JOB PARMS COMMENT
//MONJOB  JOB CLASSNAME=A CREATION D'UN JOB
```

### Paramètres positionnels

Les **paramètres positionnels** apparaissent dans la déclaration dans un ordre et à une position fixes. Ils sont décrits par une chaîne de 20 caractères au maximum, et se trouvent après les ordres `JOB` et `EXEC`, par exemple le `PGM` dans `//MYSTEP EXEC PGM=IEBGENER`.

Pour la carte `JOB`, il existe les paramètres positionnels suivants :
- Les **informations comptables**, qui renvoient à la personne ou au groupe auquel le temps CPU est alloué.
- Le **nom du programmeur**, qui identifie la personne ou le groupe responsable du JCL en question.

Pour la carte `EXEC`, il existe les paramètres positionnels suivants :
- `PGM`, le nom du programme à exécuter dans l'étape du job.
- `PROC`, le nom de la procédure à exécuter dans l'étape du job.

### Paramètres à mots-clés

Les **paramètres à mots-clés** sont optionnels et apparaissent après les paramètres positionnels, dans l'ordre souhaité par l'opérateur. C'est par exemple `CLASS` dans `//MYJOB JOB CLASS=A`.

### Paramètres de l'ordre `JOB`

Les paramètres de l'ordre job qui nous importent sont résumés dans le tableau ci-dessous.

| Paramètre | Valeurs possibles                   | Description                                                                   |
| --------- | ----------------------------------- | ----------------------------------------------------------------------------- |
| CLASS     | [A-Z] ou [0-9]                      | Assigne le job à une classe d'entrée pour définir des conditions d'exécution. |
| COND      | (RC, OP, STEPNAME)                  | Conditionne l'exécution du JCL selon les RC obtenus à chaque étape.           |
| MSGCLASS  | [A-Z] ou [0-9]                      | Assigne le log d'un job à une classe de sortie.                               |
| MSGLEVEL  | (instr [0,1,2], msg [0,1])          | Contrôle l'information produite en sortie par JCL pour un job.                |
| NOTIFY    | SYSUID                              | Précise l'utilisateur auquel envoyer un message quand le job est terminé.     |
| RD        | `R`, `RNC`, `NR`, `NC`              | Demande un redémarrage du travail s'il échoue.                                |
| REGION    | Taille en kb ou Mb                  | Précise l'espace virtuel ou réel nécessaire à l'exécution du job.             |
| RESTART   | `*`, stename, procstepname, checkid | Précise à quelle étape, procédure ou point de contrôle JCL redémarre le job.  |
| TIME      | (minutes, secondes)                 | Précise le temps CPU maximum accordé à un job.                                |
| PRTY      | [0-14] pour JES3, [0-15] pour JES2  | Assigne une priorité à un job.                                                |
| TYPRUN    | `HOLD` ou `SCAN`                    | Demande à JES un mode de traitement particulier pour le job.                  |

Le paramètre `COND` peut être utilisé sur les jobs et les étapes. Il permet de **conditionner leur exécution** selon un code retour `RC`, un opérateur booléen `OP` et éventuellement un nom d'étape `STEPNAME`. La condition est exécutée avant et après chaque étape.

Le paramètre `MSGLEVEL` spécifie le **contenu de la trace d'exécution** ou *log*. Il contient une à deux parties, à savoir une première dédiée aux instructions et la deuxième aux messages. Chacune prend des valeurs spécifiques, à savoir :
- Instructions : `0` pour imprimer l'instruction `JOB` uniquement. `1` pour imprimer tous les ordres JCL et JES, en précisant le contenu des procédures et les valeurs des paramètres symboliques. `2` pour imprimer les ordres JCL et JES uniquement.
- Messages : `1` pour imprimer les messages de JCL uniquement. `1` pour imprimer les messages de JCL, JES, des opérateurs et de SMS.

Le paramètre `RD` demande d'effectuer automatiquement un **redémarrage du job** s'il échoue. Il a 4 valeurs possibles, à savoir :
- `R`, un redémarrage avec points de contrôle autorisés.
- `RNC`, un redémarrage sans points de contrôle.
- `NR`, aucun redémarrage, mais les points de contrôle sont autorisés.
- `NR`, aucun redémarrage, les points de contrôle ne sont pas autorisés.

Le paramètre `TYPRUN` demande un **traitement particulier du job par JES**. Dans notre cas, seules 2 valeurs sont utilisés pour ce paramètre, à savoir :
- `HOLD`, qui bloque le bloque jusqu'à ce que l'opérateur le relâche.
- `SCAN`, qui demande l'analyse du JCL sans réaliser le traitement.

> **Notes :** Dans le cadre de la formation, on utilise `CLASS=A`, `MSGCLASS=H` et `TIME=(,1)`. Le paramètre `NOTIFY` est renseigné avec la valeur `&SYSUID` qui crée un symbolique sur le nom de l'utilisateur exécutant le JCL.

### Exemple pratique (1)

(1) Accéder à `APIn.SOURCE.JCL` en édition.

(2) Créer un premier fichier JCL (ou membre) dans la librairie avec `S JCL01`.

(3) Copier le contenu du membre `SKELJCL` dans `JCL01` avec la commande primaire `COPY SKELJCL`. Ce squelette constitue une première base pour la construction d'un JCL correct. 

(4) Renseigner le nom du job dans la carte `JOB`. Attention, le nom du job ne peut pas correspondre à l'identifiant de l'utilisateur, mais il peut en être inspiré. Renseigner également les paramètres `CLASS` et `MSGCLASS`.

(5) Soumettre le job avec la commande primaire `SUB`.

![[ex_sub_jcl.png]]

(6) Le job devrait renvoyer une erreur, car le JCL est incorrect. Il est possible de consulter les logs grâce à l'utilitaire SDSF, accessible avec `START M.5`. L'option `H` donne alors la file des jobs traités par JES. Plusieurs commandes de ligne sont accessibles pour manipuler ces logs, à savoir :
- `p` qui détruit un log.
- `//p...//` qui détruit un bloc continu de logs.
- `?` qui affiche les différents morceaux d'un log.
- `s` qui affiche le contenu d'un log ou d'un morceau.
- `se` qui permet d'éditer le JCL tel qu'il a été modifié par JES.
- `sj` qui permet d'éditer le JCL tel qu'il a été soumis. Une fois les corrections apportées, on peut directement le soumettre à nouveau avec `SUB`.

Dans SDSF, on appelle l'option `I` pour accéder à l'*input queue*. Si un job est en `HOLD`, on utilise la commande de ligne `a` pour le lancer. Pour l'annuler, on utilise la commande `c`.

On peut aussi utiliser la commande `H ALL` pour afficher les logs de tous les utilisateurs.

(7) Grâce aux logs, identifier la ligne qui pose problème dans le JCL, et la commenter avec `//*`. Relancer le job.

(8) Consulter à nouveau les logs. Des jobs qui portent le même nom auront toujours un identifiant différent, le dernier ayant la valeur de `JOBID` la plus élevée. Réaliser un tri descendant des jobs sur leur identifiant avec `SORT JOBID D`.

(9) Identifier le problème grâce aux logs du dernier job.

(10) Modifier le JCL en précisant le programme à utiliser pour la tâche `STEP1`. Ici, on utilise `PGM=IEFBR14`.

(11) Soumettre le JCL et consulter les logs. Le code retour `RC` devrait maintenant valoir 0, ce qui signifie que tout s'est bien déroulé.

![[ex_sub_jcl_res.png]]

## Carte EXEC

La carte `EXEC` précise l'**étape à démarrer** et marque la fin de la précédente. Elle est utilisée pour demande l'exécution d'un *load module* avec le paramètre positionnel `PGM`, ou d'une procédure lorsqu'elle est suivie `PROC` ou de rien d'autre que le nom de la procédure en question.

Pour chaque étape, il est recommandé de lui donner un nom pour faciliter la lecture des logs après l'exécution d'un job.

> **Note :** JCL autorise un maximum de 255 étapes, soit 255 cartes `EXEC`.

### Généralités

Une carte `EXEC` peut être suivie de paramètres à mots-clés. Les plus utilisés sont `COND`, `PARM` et `TIME`. 

Attention, le `TIME` utilisé pour `EXEC` doit être inférieur à celui associé à la carte `JOB`. Cela permet de limiter une étape spécifique à un certain temps CPU.

Le paramètre `COND` permet de conditionner l'exécution de l'étape, et s'utilise de la même manière qu'avec la carte `JOB`. En revanche, il est plutôt recommandé d'utiliser `IF`, `THEN`, `ELSE` et `ENDIF`. Deux conditions restent utiles, à savoir :
- `COND=EVEN`. Quoi qu'il se passe dans le JCL (bien passé ou mal passé), l'initiateur réalise cette étape. On s'en sert principalement pour restaurer des fichiers.
- `COND=ONLY`. L'initiateur exécute cette étape seulement si quelque chose s'est mal passé dans le reste du JCL.

Le paramètre mot-clé `PARM` donne quant à lui un ensemble de paramètres utilisés par le programme lors de son exécution.

### PGM

Le paramètre `PGM` est un positionnel optionnel. Il indique le **nom du programme** (ou *processing library*) à exécuter. S'il est précisé, ce doit être le premier paramètre passé à l'ordre `EXEC`. 

Le nom du programme possède toujours une longueur de 1 à 8 caractères alphanumériques. Le premier caractère doit être alphabétique ou correspondre à l'un des symboles suivants, à savoir `$`, `#` et `@`.

Le programme doit être un membre d'une bibliothèque système, privée ou temporaire.

### PROC

Le paramètre `PROC` est un positionnel optionnel. Il indique que le système doit **exécuter une procédure cataloguée ou en ligne**. S'il est précisé, ce doit être le premier paramètre passé à l'ordre `EXEC`. 

Le nom de la procédure possède toujours une longueur de 1 à 8 caractères alphanumériques. Le premier caractère doit être alphabétique ou correspondre à l'un des symboles suivants, à savoir `$`, `#` et `@`.

## Carte DD

L'ordre `DD` correspond à une abréviation de *Data Definition*. Tous les **jeux de données d'entrée et de sortie** utilisés pour une étape doivent être précisés par une ou plusieurs cartes `DD`. Elle établit le lien entre un fichier logique dans le programme et le fichier physique situé quelque part dans un disque, puis alloue le jeu de données correspondant au job en cours d'exécution.

La déclaration d'un jeu de données se fait grâce à `//DDNAME DD DSNAME`, avec `DDNAME` le nom logique attribué au jeu de données par l'utilisateur et `DSNAME` le nom physique sur le disque.

Lorsque le lien est établi entre les parties physique et logique, un bloc de contrôle et un buffer sont créés lors de l'ouverture du fichier.

### Choix des paramètres

Lors de la création d'un jeu de données via `DD`, les paramètres suivants sont précisés : `DISP` le statut du fichier, `DSN` le nom du fichier, `VOL` le volume, `UNIT` la nature de l'unité, `SPACE` la taille du fichier, `DCB` le format du fichier.

En lecture, si le fichier est catalogué, on précise simplement `DISP` et `DSN`. S'il n'est pas catalogué, on précise son volume `VOL` et la nature de l'unité `UNIT`.

### Le DDNAME

Le `DDNAME` correspond au **nom logique** attribué par l'opérateur au jeu de données. Il possède toujours une longueur de 1 à 8 caractères alphanumériques. Le premier caractère doit être alphabétique ou correspondre à l'un des symboles suivants, à savoir `$`, `#` et `@`.

Attention, certains noms sont réservés par `DDNAME` ou par le système et ne peuvent pas être utilisés par l'opérateur.

Parmi les noms réservés par `DDNAME`, on retrouve :
- `JOBCAT` et `JOBLIB`, pour les catalogues et librairies utilisés pour tout le job.
- `STEPCAT` et `STEPLIB`, pour les catalogues et librairies utilisés par une étape.

### Les paramètres d'une carte DD

La carte `DD` peut être suivie directement par un **paramètre positionnel** à savoir :
- `*`, qui marque un flux *instream*.
- `DATA`, qui correspond à du JCL utilisé comme complément d'information. Il faut lui fournir un délimiteur qui précise où se trouve la fin des données.
- `DUMMY`, qui utilisé lorsqu'on souhaite ne pas fournir un jeu de données à une étape, celui-ci étant facultatif.
- `DSN`, le nom d'un membre ou d'un fichier séquentiel.

Dans le cas d'un flux *instream*, la déclaration du jeu de données se fait de la façon suivante :

```jcl
//MONDD  DD *
donnee1
donne2
/*
```

### La concaténation de fichiers

Il est possible de **concaténer** plusieurs fichiers en un seul, de façon virtuelle, lors de l'exécution d'un JCL. Pour cela, il faut préciser un seul `DDNAME` pour le fichier logique, mais donner plusieurs ordres `DD`, un pour chaque fichier physique. 

Quand le premier fichier se termine, JES passe automatiquement au second sans même que le programme ne le réalise.

Cette technique fonctionne uniquement en lecture.

```jcl
//F1  DD DSN=...
//    DD DSN=...
//    DD DSN=...
```

Un exemple de concaténation est donné ci-dessous.

![[ex_jcl_cop_fcat_impr.png]]

### Exemple pratique (2)

(1) Repartir depuis le membre précédent `APIn.SOURCE.JCL(JCL01)`. Ici, l'étape exécute le programme `IEFBR14`, utilisé pour la création de jeux de données vides ou la destruction de jeux de données existants.

(2) Donner une carte `DD` au programme `IEFBR14`, décrivant un fichier à créer. Ici, on déclare une carte `DD` avec un `FI1` comme `DDNAME` et `APIn.TEST.JCL1` comme `DSNAME`.

> **Note :** Les noms peuvent être simples ou qualifiés. Un nom simple contient 1 à 8 caractères. Un nom qualifié est un ensemble de noms simples, séparés par des points, et dont la longueur ne dépasse pas les 44 caractères au maximum.

![[ex_jcl_creation_fi1.png]]

(3) Renseigner au système quel est le statut du jeu de données et quoi en faire à la fin de l'étape avec le paramètre `DISP`. Ce paramètre mot-clé peut prendre jusqu'à trois valeurs, séparées par une virgule. Ce sont :
- Le **statut du fichier**. Il permet d'exprimer une forme de **verrou**.
	- `NEW`, le fichier n'existe pas et il n'est pas partagé avec les autres.
	- `OLD`, le fichier doit exister et il n'est pas partagé avec les autres.
	- `MOD`, le fichier existe ou non et il n'est pas partagé avec les autres. Si le fichier n'existe pas, il est créé. S'il existe, il est manipulé en mode *append*.
	- `SHR`, le fichier doit exister et il est partagé avec les autres.
- **Que faire** avec le fichier si l'opération s'est **bien terminée**.
- **Que faire** avec le fichier si l'opération s'est **mal terminée**.

(4) Définir l'espace de stockage alloué au fichier avec `SPACE`, `LRECL` et `BLKSIZE`.

(5) Utiliser le paramètre mot-clé `RECFM`, qui indique le format et les caractéristiques des enregistrements dans un jeu de données. Ici, on lui passe la valeur `FB`, signifiant que les enregistrements sont bloqués et possèdent une longueur fixe.

(6) Soumettre le job et visualiser les logs avec SDSF. Pour afficher une liste complète des logs, utiliser la commande `LOG`.

![[ex_jcl_creation_logs.png]]

(7) Jusqu'à présent, nous avons créé des fichiers séquentiels. En effet, pour une déclaration `DD`, le paramètre `SPACE` permet de définir la nature du jeu de données. Lorsqu'on ne précise pas la valeur du sous-paramètre de `DIRECTORY`, c'est un fichier séquentiel qui est créé. Autrement, lorsqu'on lui donne une valeur, cela revient à préciser le nombres de blocs alloués au répertoire du jeu partitionné.

La syntaxe pour `SPACE` est donc la suivante : `//MYDD DD DNS=...,SPACE=(TRK/CYL,(primary_qtty,secondary_qtty,directory)`.

![[ex_jcl_creation_library.png]]

(8) On repasse sur du fichier séquentiel, le but étant maintenant de copier le contenu d'un PS sur un autre. Pour réaliser cette opération, il faut utiliser le programme `IEBGENER`. Il prend 4 ordres `DD`, à savoir :
- `SYSUT1`, le fichier séquentiel source. On peut lui fournir un `DSN`, ou bien utiliser un *instream* avec la carte `DD *`. Dans ce cas, le contenu à copier est rédigé directement sur les lignes qui suivent, sans aucun `//`.
- `SYSUT2`, le fichier séquentiel de destination.
- `SYSPRINT`, les messages donnés par `IEBGENER`. Ici, on lui passe la valeur `SYSOUT=H`, ce qui permet à JES de s'occuper des sorties d'impression.
- `SYSIN`, un fichier de paramètres qui donne des informations supplémentaires sur l'opération à réaliser.

![[ex_jcl_iebgener.png]]

> **Note :** À noter que cette technique peut également être employée sur les membres d'une librairie, puisqu'un membre est considéré comme un fichier séquentiel.

(9) Utiliser la même technique pour écrire dans le membre d'une librairie. Attention, il faut absolument utiliser les valeurs `OLD` ou `SHR` pour le paramètre `DISP`. Autrement, l'exécution du programme entraînerait la suppression de la librairie en cours de manipulation.

![[ex_jcl_iebgener_lib.png]]

(10) Cette fois-ci, copier le membre d'une librairie vers une autre.

![[ex_jcl_iebgener_copmem.png]]

(11) Copier le membre d'une librairie dans un fichier séquentiel.

![[ex_jcl_iebgener_copmemlib.png]]

(12) Pour imprimer le contenu d'un fichier, il suffit de donner `SYSOUT=H` au paramètre `SYSUT2` de `IEBGENER`. Par opposition, si on souhaite le stocker dans un fichier, il faut renseigner le paramètre `DSN`.

![[ex_jcl_print.png]]

### Le paramètre DATA

Le paramètre `DATA` est utilisé pour entamer un jeu de données *instream*, susceptible de contenir des `//` en colonnes 1 et 2. Cela donne la possibilité d'utiliser directement du JCL dans des déclarations de données.

Avec `DATA`, le flux *instream* prend fin lorsque JCL rencontre un `/*`. Une autre possibilité consiste à utiliser le paramètre mot-clé `DLM`, auquel on passe une valeur, celle-ci étant alors recherchée par JCL pour marquer la fin du flux.

Dans l'exemple ci-dessous, on définit un flux avec le délimiteur `DLM=XX` qui marque sa fin. Ce flux correspond à 3 instructions JCL.

![[ex_jcl_data_dlm.png]]

### Le paramètre DCB

Le paramètre `DCB` (*Data Control Block*) permet de spécifier les attributs d'un fichier, utilisés pour **construire un bloc de contrôle**. 

Parmi les sous-paramètres à notre disposition pour `DCB`, seuls `BLKSIZE`, `LRECL` et `RECFM` sont à utiliser.

### Le paramètre DISP

Le paramètre `DISP` indique l'**état du fichier** et les **dispositions à prendre** après la fin de l'étape ou du travail.

Plusieurs instructions peuvent être précisées comme sous-paramètres de `DISP` afin de préciser que faire du fichier en cas de fin normale ou anormale. Ce sont :
- `CATLG`, pour cataloguer le fichier.
- `UNCATLG`, pour dé-cataloguer le fichier.
- `DELETE`, pour supprimer le fichier.
- `KEEP`, pour écrire le fichier sur le volume sans le cataloguer.
- `PASS`, pour des fichiers temporaires qu'on souhaite passer aux étapes suivantes. Cette valeur est disponible uniquement pour les fins normales.

### Le paramètre DSNAME

Le paramètre `DSNAME` ou `DSN` correspond au **nom du fichier physique**.

Les fichiers temporaires ont leur nom précédé par `&&`, comme `&&NOMFICH`. Ils sont perdus à la fin du traitement s'ils n'ont pas été sauvegardés sur la machine.

En donnant un `DSN` de valeur `NULLFILE`, cela équivaut à utiliser `DUMMY`.

### Exemple pratique (3)

IEBGENER peut aussi être utilisé pour partitionner un jeu de données, réaliser des éditions ou travailler avec des routines utilisateur. Dans ces circonstances, on commence toujours par utiliser la déclaration `GENERATE` propre à l'outil `IEBGENER` dans l'une de ses cartes `DD`. Les autres déclarations que l'outil propose peuvent ensuite être utilisées, `RECORD` dans notre cas.

(1) Donner un flux *instream* à `SYSIN` qui correspond à un tableau obtenu par formatage du fichier `APIn.COB.ASSURES`. Le formatage est réalisé avec `IEBGENER`. On commence par utiliser la déclaration `GENERATE` pour démarrer l'environnement d'édition de l'entrée. Cette déclaration prend deux paramètres `MAXFLDS` et `MAXLITS`, qui correspondent respectivement aux nombres de champs (*fields*) et de littéraux utilisés dans la suite de l'opération. Ils sont remplis à la fin.

(2) Utiliser la déclaration `RECORD FIELD` pour définir un groupe enregistrement et fournir des informations d'édition sur celui-ci. Les données sont formatées à la manière de champs.

> **Note :** Un groupe enregistrement correspond à un ensemble d'enregistrements qui doivent être traités de façon identique.

La déclaration `RECORD FIELD` prend 4 paramètres `(length,content,conv,output_pos)`. Le sous-paramètre `length` correspond au nombre de caractères de la zone d'entrée ou du littéral. Le sous-paramètre `content` correspond à un littéral renseigné par l'utilisateur, ou à la position en colonne dans le fichier d'entrée du début de la zone à copier. Le sous-paramètre `conv` renseigne s'il faut ou non convertir l'encodage de l'entrée. Enfin, le sous-paramètre `output_pos` correspond à la position en colonnes dans la destination où l'utilitaire doit coller l'information sur l'enregistrement.

Ici, on ne précise pas le champ de conversion, car on ne souhaite pas passer d'un encodage à un autre.

(3) Donner une valeur à `MAXFLDS` et `MAXLITS`. 

![[ex_jcl_genrec_v2.png]]

La liste résultante n'est pas triée pour l'instant. On va pouvoir le faire sur un fichier temporaire, fourni ensuite à `IEBGENER` pour produire un tableau trié. Pour cela, on utilise dès le début du JCL le programme `SORT`, un autre outil disponible dans z/OS.

(4) Pour la carte `//SORTOUT` du programme `SORT`, créer un fichier temporaire `&&ASSTRIES` et le passer aux étapes suivantes avec `DISP=(NEW,PASS)`. C'est lui qui va être utilisé par la suite lors de la création du tableau de champs.

(5) Préciser les critères de tri utilisés avec la carte `//SYSIN`. Par critère de tri, on dispose de 4 zones à renseigner qui sont les suivantes :
- La position dans le fichier.
- La longueur de la zone en entrée.
- Le type de données, `ch` pour les caractères, ou `bi` pour les binaires.
- L'ordre ascendant ou descendant.

> **Note :** Il est par ailleurs possible de chaîne les critères de tri entre les mêmes parenthèses. Il suffit de continuer à les séparer par des virgules.

(6) Appeler ensuite `IEBGENER` sur le fichier temporaire `ASSTRIES`.

![[ex_jcl_tri_genrec.png]]

(7) Réaliser un tri compte-tenu d'un deuxième critère.

![[ex_jcl_deux_tris.png]]

### Le paramètre LRECL

Le paramètre `LRECL` décrit le nombre d'octets de l'enregistrement logique. Dans notre cas, en JCL, COBOL, etc. on décrit des fichiers de 80 caractères. Pour les fichiers de données, cela va dépendre des besoins en matière de stockage d'informations.

### Le paramètre OUTLIM

Le paramètre `OUTLIM` permet de mettre une limite numérique sur `SYSOUT` par exemple. Si on dépasse cette limite de sorties (lignes d'impression), le programme s'arrête avec un `ABNUM`.

### Le paramètre RECFM

Le paramètre `RECFM` définit le format et les caractéristiques des enregistrements d'un nouveau fichier. Il peut prendre pour valeur :
- `F`, taille fixe. Si le record vaut 80, alors le `BLKSIZE` sera aussi de 80. Pour les autres valeurs de `RECFM`, il faut préciser la longueur.
- `FB`, fixed block.
- `FBS`, fixe avec caractère de saut.
- `V`, variable.
- `VB`, variable bloquée.
- `VBS`, variable bloquée avec saut.
- `VS`, variable avec saut.
- `U`, indéfini. Quand on écrit un fichier déclaré avec `U`, il faudra préciser au lancement du programme s'il s'agit d'un `F`, `FB`, etc.

![[recfm.png]]

### Le paramètre RETPD

Le paramètre `RETPD` correspond à la période de rétention durant laquelle on conserve le fichier. On n'a pas le droit de le supprimer durant cette période.

### Le paramètre SPACE

Le paramètre `SPACE` permet l'allocation d'espace à un nouveau fichier sur volume à accès direct, généralement `TRK` ou `CYL`. 

Il peut prendre un sous-paramètre `RLSE` (*release*), qui signifie qu'on relâche l'espace qu'on a demandé de trop lors de l'allocation d'un fichier. Attention, l'espace à libérer doit être supérieur ou égal à 1 cylindre.

> **Note :** Dans un répertoire, les blocs ont toujours une taille de 512.

Le paramètre `SPACE` possède une valeur décrite par `(unités,quantités)`. Les unités correspondent aux allocations en pistes `TRK` ou en cylindres `CYL`. Les quantités correspondent au nombre d'unités à allouer au fichier. On distingue :
- La quantité primaire, espace de base occupé par le fichier.
- La quantité secondaire, le nombre total de morceaux que l'on est autorisé à ajouter lorsque la quantité primaire est pleine. On a droit à 15 quantités secondaires, soit 16 morceaux au total en tenant compte de la quantité primaire. 
Si la quantité maximale disponible pour un fichier est atteinte, soit 16 morceaux, alors le programme s'arrête.

Par ailleurs, parmi les quantités, on peut également renseigner le nombre de blocs alloué au *directory*. Lorsque cette valeur est renseignée, cela crée d'emblée une librairie.

### Le paramètre SYSOUT

Au lieu de passer la classe `H` à `SYSOUT`, on peut lui donner `INTRDR` ce qui permet de créer un fichier JCL plutôt que de laisser JES gérer la sortie. Pour `INTRDR`, il faut préciser la classe d'entrée du fichier en question, par exemple `A`.

### Les ordres DD particuliers

Il existe un certain nombre d'ordres `DD` particuliers. Ce sont notamment :
- `JOBCAT` et `JOBLIB`, pour les catalogues et librairies utilisés pour tout le job.
- `STEPCAT` et `STEPLIB`, pour les catalogues et librairies utilisés par une étape.
- `SYSIN`, pour passer des paramètres aux commandes d'un utilitaire.

> **Note :** Lorsqu'on donne un `STEPCAT` à une étape, le JCL ne tient pas compte ponctuellement d'un `JOBCAT` qui aurait pu être défini plus tôt. Il en va de même pour les `*LIB`.

## Les procédures

Une **procédure** est un JCL utilisé fréquemment et qui peut être appelé par d'autres JCL. On l'appelle grâce à un ordre `EXEC`. Au sein d'une procédure, il n'est pas possible d'intégrer de carte `JOB`, de fichier instream `DD*`, etc. Elle peut se présenter sous deux formes distinctes, à savoir :
- La **procédure cataloguée**, qui est membre d'un PDS.
- La **procédure instream**, en ligne, qui fait partie intégrante du JCL qui l'appelle.

La procédure doit être définie avant de l'utiliser. En principe, on la renseigne au début du JCL.

Une procédure est **modifiable**, signifiant qu'on peut modifier son exécution en fournissant des données qui écrasent celles qui sont fournies en son sein. Quand on change des lignes en-dehors de la procédure, elles doivent suivre le même ordre que dans la déclaration de la procédure.

Une procédure est **paramétrable**, signifiant qu'on peut déclarer un paramètre `&PARAM` dans une procédure, puis lui donner une valeur plus tard lors de l'exécution de la procédure.

Dans l'exemple ci-dessous, un paramètre `&MEM` est déclaré dans la procédure `IEBGCOPY`, puis une valeur lui est assignée lors des exécutions.

![[ex_jcl_proc_param.png]]

> **Note :** On peut modifier et paramétrer simultanément. Par contre, on ne peut pas modifier une carte paramétrée !

On peut donner une **valeur par défaut** au paramètre, directement après la carte `PROC`. Si on ne fournit pas de valeur au paramètre lors des exécutions, c'est cette valeur par défaut qui est utilisée.

![[ex_jcl_proc_defparam.png]]

Quand on a des paramètres qui se suivent, pour les séparer des autres, on utilise un point, par exemple. Si on a besoin du point, par exemple pour faire suivre le type et la librairie d'un membre, on utilise deux points.

![[ex_jcl_paramschaine.png]]

Il existe un **ordre de priorité** quand on a affaire à des procédures qui portent le même nom :
(1) Procédures in-stream (les procédures directement situées dans le JCL).
(2) Procédures cataloguées privées (nos propres librairies).
(3) Procédures cataloguées publiques (celles fournies par le système).

Ainsi, pour des procédures qui portent le même nom, mais qui sont déclarées dans différents contextes, JES va utiliser en priorité la procédure instream qui est donc déclarée dans le JCL.

### Procédure instream

Une procédure instream commence par un ordre `PROC` et se termine par un ordre `PEND`. Il y en a 15 au maximum par job.

Une procédure *instream* ne peut pas en contenir une autre, ni comporter de jeu de données `DD DATA` ou `DD *`.

### Procédure cataloguée

Une procédure cataloguée est placée dans une librairie et correspond donc à l'un de ses membres. On distingue :
- Les procédures cataloguées système.
- Les procédures cataloguées publiques.
- Les procédures cataloguées privées. Celles-ci ont été développées par l'opérateur.

Le paramètre `PROC` est facultatif pour les procédures cataloguées, mais obligatoire si on a des paramètres à leur donner. Lorsque `PROC` est renseigné, on termine généralement la procédure par un `PEND`.

### L'ordre PROC

L'ordre `PROC` marque le début d'une procédure. Il est obligatoire pour une procédure en ligne et facultatif pour une procédure cataloguée. 

De la même manière, on peut attribuer un nom à une procédure, ceci étant obligatoire pour les procédures en ligne et facultatif pour les procédures cataloguées. Ce nom fait 8 caractères alphanumériques au maximum.

L'ordre `PROC` est suivi des valeurs par défaut des paramètres symboliques utilisés dans la procédure, s'il y en a.

Nous avons ainsi pour une procédure cataloguée :
```jcl
//[nom] PROC [paramètres]
```

Et pour une procédure en ligne :
```jcl
//nom PROC [paramètres]
```

### L'ordre PEND

L'ordre `PEND` marque la fin d'une procédure. Il est obligatoire pour une procédure en ligne et facultatif pour une procédure cataloguée.

### Les paramètres symboliques

Les paramètres symboliques permettent de modifier une valeur, un paramètre ou un sous-paramètre lors de l'exécution du JCL.

Un paramètre symbolique commence par un `&`, suivi du nom du paramètre qui doit faire entre 1 et 8 caractères alphanumériques, par exemple `&MONPARAM`.

Dans la rédaction du JCL, un point est nécessaire après un paramètre symbolique, lorsque celui-ci est suivi d'une partie invariable, d'une lettre ou d'un caractère spécial, ou encore par un point.

### Appel d'une procédure

On appelle une procédure en appelant son nom via l'ordre `EXEC`, et en attribuant éventuellement une valeur aux paramètres symboliques.

```jcl
//STEP1 EXEC PROC=nom_proc,PARAM=VALEUR
```

Quand une procédure contient plusieurs étapes, et qu'on souhaite modifier un paramètre ou un ordre, on fait par exemple :

```jcl
//OUTSTEP EXEC PROC=nom_proc,PARAM=VALEUR
//INSTEP1.SYSIN=...
//INSTEP2.SYSIN=...
//INSTEP2.SYSOUT=...
```

### Exemple pratique (4)

Ces premiers exemples montrent comment utiliser des **procédures internes**. Le caractère modifiable et paramétrable des procédures est également exploré en (3) et en (4) respectivement.

(1) Créer une procédure `IEBG` qui utilise le programme `IEBGENER`. Exécuter cette procédure plus tard dans le programme.

![[ex_jcl_proc.png]]

(2) Placer le flux instream anciennement associé à `SYSIN` dans un fichier `IEBGPARM`, avec un bloc `MM...MM` et la commande primaire `CREATE IEBGPARM`. Placer la carte `//SYSIN` dans la procédure `IEBG` et lui passer `IEBGPARM`. Ici, on a simplement stocké les paramètres d'exécution du programme dans un fichier externe, qu'on appelle ensuite dans la procédure.

![[ex_jcl_proc_sysinmod.png]]

(3) Rappeler la même procédure, en modifiant la valeur de `SYSUT2`. Ici, on souhaite copier la sortie vers l'imprimante. On utilise donc `SYSOUT=H` comme valeur.

![[ex_jcl_proc_sysinmodimp.png]]

> **Note :** Attention, tous les éléments entre deux `EXEC` successives appartiennent à la première `EXEC`. On peut donc modifier les paramètres de cette `EXEC` en les déclarant à nouveau pour écraser les valeurs passées plus tôt. Attention, si on modifie plusieurs paramètres, il est important de respecter l'ordre des déclarations au sein de la procédure. 

(4) Créer un paramètre symbolique `&PARMTRI` qui correspond au fichier décrivant les paramètres fournis à `SYSIN`. Créer les membres `SORTVILL`, `SORTNUM` et `SORTNOM` qui contiennent les paramètres de tri passés plus tôt au programme, par exemple `SORT FIELDS=(50,12,CH,A)` pour les villes.

![[ex_jcl_param_externe.png]]

(5) Lorsqu'une procédure qui contient plusieurs étapes, il est possible de préciser laquelle on souhaite modifier. Dans l'exemple ci-dessous, on a une procédure `FICCR` avec les étapes `DELFI` et `CREFI`.

![[ex_jcl_proc_steps.png]]

On lance 2 fois la procédure, en modifiant les paramètres de base associés à ces étapes. Une première fois, on donne un `DD DUMMY` au `SYSIN` de `DELFIC`. La deuxième fois, la même chose, mais cette fois-ci pour `CREFIC` puis `DELFIC`. 

Pour rappel, il faut bien suivre l'ordre des items d'une procédure lorsqu'on en modifie des paramètres, d'où le `CREFIC` suivi du `DELFIC`.

En complément des procédures internes, on peut également utiliser les **procédures externes ou cataloguées**.

(6) Créer une procédure externe `IEBGCOPY`, qui correspond à un membre de la librairie privée `APIn.SOURCE.JCL`. Lors de l'exécution de la procédure, il suffit de préciser plus haut avec `JCLLIB` quelle est la librairie utilisée et dans laquelle se trouve la procédure cataloguée.

![[ex_jcl_procext.png]]

### Les procédures imbriquées

Une procédure quelle qu'elle soit peut en appeler une autre, jusqu'à 15 niveaux d'imbrication. Attention, une procédure en ligne ne peut pas contenir la définition d'une autre procédure en ligne ! En effet, la séquence `PROC...PROC...PEND...PEND` est invalide.

### Concaténation de librairies

S'il y a une `SYSLIB` qui existe déjà dans la procédure, mais qu'on souhaite en concaténer une autre à ajouter à la procédure, on peut utiliser la notation suivante :

```jcl
SYSLIB DD
       DD DNS = USER.LOADLIB,DISP=SHR
```

Dans l'exemple ci-dessus, on concatène la `USER.LOADLIB` à la `SYSLIB` qui existe déjà dans la procédure.

### Les conditions

Le JCL autorise de poser des conditions avec `IF`, `THEN`, `ELSE` et `ENDIF`. On peut tester les conditions suivantes :
- `nom.etape.RC=valeur`.
- `nom.etape.ABEND=T/F`.
- `nom.etape.RUN=T/F`

Il est possible d'imbriquer les conditions et d'utiliser les opérateurs booléens `OR` et `AND`.

```jcl
//MYTEST  IF etp1.RC=0 AND etp2.RC=0 THEN
//STEP3   EXEC PGM=pgm3
//...
//MYTESTEL ELSE
//STEP4    EXEC PGM=pgm4
//...
//MYTESTF  ENDIF
```

### Exemple pratique (5)

L'objectif est de réaliser un backup d'une librairie. Si le backup s'est bien déroulé, on supprime la librairie et on la recrée en plus grand. Si la recréation s'est bien déroulée, alors on copie le backup dans celle-ci. 

```
Backup d'un librairie (iebcopy test.library sur backup)
Si backup est ok
	Supprimer la librairie (iefbr14)
	Copier le backup dans la nouvelle librairie, plus grande que l'ancienne (iebcopy)
	Si la copie est ok
		Supprimer le backup (iefbr14 delete backup)
	finsi
finsi
```

Commandes supplémentaires :
- `ts`, commande de ligne qui coupe le texte. Une nouvelle ligne est insérée à la position du curseur et le reste du texte est placé sur celle-ci.

Pour sauvegarder un script depuis la machine virtuelle vers son ordinateur :
- `start 6`.
- Transfer > Receive.
- Renseigner le nom du membre, sans préciser le API1.
- Cocher la case TSO/MUSIC.
- Cliquer sur OK.

## Les utilitaires

On commence par 4 utilitaires à oublier :
- `IEHINITT` : Utilisé pour labelliser des bandes magnétiques.
- `IEHLIST` : Liste la VTOC et les répertoires partitionnés.
- `IEHMOVE` : Copie ou déplacé des données
- `IEHPROGM` : Sert à la maintenance de programmes.

On utilise `IEFBR14` pour créer et supprimer des PO et PS.

À la différence de `IEBGENER`, `IEBCOPY` peut être utilisé pour effectuer la copie, compression ou fusion de librairies. Il faut penser à vérifier que la copie se soit bien déroulée en consultant les sorties via SDSF.

![[ex_jcl_iebcopy.png]]

Les utilitaires de tri possèdent des paramètres en commun, à savoir :
- `SYSIN` pour donner les critères de tri.
- `SORTIN` pour le fichier à trier.
- `SORTOUT` pour le fichier trié.
- `SYSOUT` pour les messages de sortie.

## SMS

**SMS** correspond à l'acronyme de *System Managed Storage*. C'est un outil utilisé pour gérer les disques d'un système, selon des règles qui lui sont données par l'équipe système.

Chez IBM, avant d'entreprendre quelque chose, on peut discuter avec les ingénieurs des **groupes de travail SHARE et GUIDE**, pour proposer des améliorations sur les produits IBM. Généralement, les discussions portent davantage sur la facilité d'utilisation des produits. Par exemple, si un client souhaite modifier le fonctionnement d'une commande par défaut, il peut voir avec IBM pour qu'ils implémentent la modification sur le système dans sa globalité. Le changement vaut ainsi pour tous les utilisateurs.

Les **demandes des utilisateurs** concernent ainsi principalement les points suivants :
- Migration automatique.
- Backup automatique géré par le système.
- Recovery automatique avec évaluation des dommages.
- Transfert facile du contenu d'une unité vers une nouvelle.
- Régulation et contrôle dynamiques.
- Sécurité et audit des accès.

## Complément

Exemple de condition sur le JCL.

![[jcl_ex_if.png]]