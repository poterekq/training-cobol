# Index des programmes COBOL

**Fichiers :**
**Structures notables :**
**Verbes notables :**

## ALGO1

**Fichiers :** séquentiel.
**Structures notables :** PIC d'édition.
**Verbes notables :**

## ALGO2

Extraction et exploration d'une table COBOL.

**Fichiers :** séquentiel.
**Structures notables :** table.
**Verbes notables :** redefines, occurs.

## ALGO3 et ALGO3V2

Fusion de fichiers.

**Fichiers :** séquentiel.
**Structures notables :**
**Verbes notables :**

## ALGO4 et ALGO4V2

Assortiment de fichiers.

**Fichiers :** séquentiel.
**Structures notables :** table.
**Verbes notables :** occurs.

## ALGO5

Mise en forme des statistiques sur la population.

**Fichiers :** séquentiel.
**Structures notables :** PIC d'édition.
**Verbes notables :**

## ALGO6

Analyse de données clients.

**Fichiers :** séquentiel.
**Structures notables :** 
**Verbes notables :** redefines, evaluate.

## ALGO7

Calcul de statistiques descriptives sur les notes des étudiants.

**Fichiers :** séquentiel.
**Structures notables :** PIC d'édition.
**Verbes notables :** compute.

## BNCHESDS

Copie du contenu d'un séquentiel sur un ESDS.

**Fichiers :** séquentiel, ESDS.
**Structures notables :** 
**Verbes notables :** 

## BNCHKSDS

Copie du contenu d'un séquentiel sur un KSDS.

**Fichiers :** séquentiel, KSDS.
**Structures notables :** 
**Verbes notables :** copy.

## COBKSAX

Accès au contenu d'un KSDS via son AIX (alternate index).

**Fichiers :** KSDS.
**Structures notables :** 
**Verbes notables :** copy, start, read next.

## COBKSDS

Opérations sur KSDS.

**Fichiers :** KSDS.
**Structures notables :** 
**Verbes notables :** 
open i-o, start
evaluate
read next
read ... key is,
rewrite
delete

## CODERET

Renvoie un code retour invalide.

**Fichiers :**
**Structures notables :**
**Verbes notables :**

## COMPTYP

Benchmarking sur les différentes types COMP.

**Fichiers :**
**Structures notables :** COMP, COMP-3, COMP-5.
**Verbes notables :** perform ... times.

## EXCDATE

Utilisation des programmes CEEDAYS et CEEDATE.

**Fichiers :**
**Structures notables :** COMP, structures spécifiques aux programmes CEEDATE et CEEDAYS.
**Verbes notables :** call.

## EXCDAYS

Utilisation du programme CEEDAYS.

**Fichiers :**
**Structures notables :** COMP, structures spécifiques au programme CEEDAYS.
**Verbes notables :** call.

## EXLOCPP

Utilisation d'un sous-programme EXLOCSP.

**Fichiers :**
**Structures notables :**
**Verbes notables :** 
call ... using by reference ... by content
... renames ... thru

## EXLOCSP

Comparaison du fonctionnement de la WORKING-STORAGE, de la LOCAL-STORAGE et de la LINKAGE.

**Fichiers :**
**Structures notables :**
**Verbes notables :** add.

## EXODATE

**Fichiers :**
**Structures notables :** PIC d'édition.
**Verbes notables :** 
add
compute
function current-date

## EXORPRT et EXORPRT2

Statistiques sur le programme (nombre de lignes, commentaires, lignes blanches).

**Fichiers :** séquentiel.
**Structures notables :** PIC d'édition.
**Verbes notables :** 
add
compute
function current-date
unstring ... into ...

## EXOSTR

Exercice de manipulation des chaînes de caractères.

**Fichiers :**
**Structures notables :** paramètres en LINKAGE, COMP-3.
**Verbes notables :** 
accept
initialize
inspect ... converting ... to ....
inspect ... tallying ... for all ...
string ... delimited by ... into ... end-string
string ... delimited by space ... into ... with pointer ... end-string
unstring ... delimited by ... into ... delimiter in ... count in ... end-unstring

## MKDATE
Sous-programme pour la création et le formatage d'en-têtes contenant des dates.

**Fichiers :** 
**Structures notables :** table.
**Verbes notables :**
redefines
occurs
unstring ... into ...
accept ... from ...
string ... delimited by size ... into ... end-string

## MVTLABE2

Sous-programme d'évaluation du code erreur.

**Fichiers :**
**Structures notables :**
**Verbes notables :**

## MVTLABEL

Sous-programme d'évaluation du code erreur.

**Fichiers :**
**Structures notables :**
**Verbes notables :**

## NOMCOND

Exemple d'utilisation des noms condition (niveau 88).

**Fichiers :**
**Structures notables :** niveau 88.
**Verbes notables :** set ... to ....

## PROG01

Exemples d'interaction avec l'utilisateur.

**Fichiers :**
**Structures notables :** 
**Verbes notables :**
accept ... from date ...
move corresponding ... to ...
display ... upon console
accept ... from console

## PROG02

Utilisation du PERFORM.

**Fichiers :**
**Structures notables :**
**Verbes notables :**

## PROG03

Utilisation du PERFORM et des phrases.

**Fichiers :**
**Structures notables :**
**Verbes notables :** next sentence.

## PROG04

Utilisation de l'EVALUATE.

**Fichiers :**
**Structures notables :**
**Verbes notables :**

## PROG05

Utilisation des opérateurs arithmétiques des pictures d'édition.

**Fichiers :**
**Structures notables :** PIC d'édition.
**Verbes notables :**
add ..., ..., ... giving ...
add ... to ...
subtract ... from ...
subtract ... from ... giving ...
multiply ... by ... giving ...
dividing ... by ... giving ...
dividing ... by ... giving ... rounded

## PROG06

Utilisation des fichiers, copybooks et pictures d'édition.

**Fichiers :** séquentiel.
**Structures notables :** PIC d'édition.
**Verbes notables :** copy.

## PROG07

Utilisation des fichiers séquentiel en lecture et écriture.

**Fichiers :**
**Structures notables :** 
**Verbes notables :**
copy
move space to ...

## PROG08

Lecture d'un KSDS en mode séquentiel.

**Fichiers :** KSDS.
**Structures notables :** PIC d'édition.
**Verbes notables :** copy.

## PROG09

Lecture d'un KSDS avec accès direct.

**Fichiers :** KSDS.
**Structures notables :**  PIC d'édition.
**Verbes notables :** copy, accept.

## PROG10

Lecture d'un KSDS avec AIX en accès séquentiel.

**Fichiers :** KSDS.
**Structures notables :** PIC d'édition.
**Verbes notables :**
copy
accept
start ... key ...

## PROG11

Lecture d'un KSDS avec AIX en accès direct.

**Fichiers :** KSDS.
**Structures notables :** PIC d'édition.
**Verbes notables :** 
copy
accept
read ... key ...

## PROG12

Lecture d'un RRDS en accès séquentiel.

**Fichiers :** RRDS.
**Structures notables :** PIC d'édition.
**Verbes notables :**

## PROG13

Lecture d'un RRDS en accès direct.

**Fichiers :** RRDS.
**Structures notables :** PIC d'édition, COMP.
**Verbes notables :** accept.

## QUITPP

Programme principal de traitement des quittances.

**Fichiers :**
**Structures notables :** PIC d'édition, COMP.
**Verbes notables :**
call ... using ...
add
string ... delimited by size into ... end-string
unstring function current-date into ...
string ... space ... delimited by size into ... end-string
compute

## QUITSP

Sous-programme accesseur de traitement des quittances.

**Fichiers :** séquentiel.
**Structures notables :** niveau 88.
**Verbes notables :**
open input ...
open i-o ...
open output ...
read ... into ...
write ... from ...
rewrite ... from ...
close ...

# SKELCOB

Squelette d'un programme COBOL.

**Fichiers :**
**Structures notables :** 
**Verbes notables :**

## TDCOBMV/2/3

Chaîne de traitements MAJAS pour la mise à jour des assurés.

**Fichiers :** séquentiel.
**Structures notables :** niveau 88, COMP-3, COMP, table, PIC d'édition.
**Verbes notables :**
redefines
occurs
add ... to ..., ..., ...
write ... from ...
perform ... times ... end-perform
unstring function current-date into ...
accept ... from day-of-week
string ... delimited by size into ... end-string
move all ... to ...
call ... using ...

## TDCOBQ4

Création de quittances de primes pour les assurés.

**Fichiers :** séquentiel.
**Structures notables :** PIC d'édition, COMP-3.
**Verbes notables :**
unstring function current-date(1:12) into ...
string '...' ... delimited by size into ... end-string
perform varying ... from ... by ... until ... end-perform
write ... from ... after advancing page

## TDCOBST

Calcul de statistiques sur les assurés.

**Fichiers :** séquentiel.
**Structures notables :** PIC d'édition.
**Verbes notables :**
redefines
add ... to ..., ..., ...
move all ... to ...
move spaces to ...

## TESTCOMP

Utilisation du COMP-5.

**Fichiers :**
**Structures notables :** COMP-5.
**Verbes notables :** compute.

## TESTJUST

Justification du texte.

**Fichiers :**
**Structures notables :** 01 ... JUST PIC X(...).
**Verbes notables :**

## TMPLTCOB

Template COBOL.

**Fichiers :**
**Structures notables :** 
**Verbes notables :**

## TMPMAJAS

Programme temporaire pour la chaîne de traitements MAJAS.

**Fichiers :** KSDS, ESDS, séquentiel.
**Structures notables :** COMP.
**Verbes notables :**
copy
initialize
call ... using ...

## VENTES/2

Liste des ventes par vendeur et par date avec tables COBOL.

**Fichiers :** séquentiel.
**Structures notables :** table, COMP, COMP-3, PIC d'édition.
**Verbes notables :**
redefines
accept ... from date yyyymmdd
move ...(...) to ... 
perform varying ... from ... by ... until ... end-perform
move ... to ...(..., ...)