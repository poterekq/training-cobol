## Index des jobs JCL  programmes COBOL/DB2

**Fichiers :**
**Structures notables :**
**Verbes notables :**
**Requêtes notables :**

**Utilitaires utilisés :** 

## BINPL (J)

Bind du plan.

**Utilitaires utilisés :** IKJEFT01.

## COMPDB20 (J)

Précompilation du programme DB2, production du DBRM, bind du plan et exécution du programme.

**Utilitaires utilisés :** COMPDB2, IKJEFT01.

## COMPDB21 (J)

Précompilation du programme DB2 et production du DBRM.

**Utilitaires utilisés :** COMPDB2.

## COMPDB22 (J)

Exécution du programme.

**Utilitaires utilisés :** IKJEFT01.

## COMPLKD (J)

Compilation d'un programme principal sans DB2.

**Utilitaires utilisés :** IGYWCL.

## COMPOPT (J)

Création d'une table SQL, précompilation d'un programme DB2, production du DBRM, bind du plan, exécution du programme.

**Utilitaires utilisés :** IKJEFT01, COMPDB2, IKJEFT01, IDCAMS.

## EXECDB2 (J)

Exécution d'un programme DB2.

**Utilitaires utilisés :** IKJEFT01.

## MAJASJB (J)

Exécution de la chaîne de traitements MAJAS avec les premières intégrations DB2.

**Utilitaires utilisés :** IDCAMS, SORT, IKJEFT01.

## MAOPTJ00/01/10/20/3A/3D/3V (J)

Versions pré-finalisées de la chaîne MAJAS avec DB2. 
3A : DB2 et VSAM. 
3D : DB2.
3V : VSAM

**Utilitaires utilisés :** IKJEFT01, COMPDB2, IGYWCL, IDCAMS.

## TESTSQL (J)

Test de création d'un job JCL pour la création de tables DB2.

**Utilitaires utilités :** IKJEFT01.

## MAOPTJ30

Version finale du job pour l'exécution de la chaîne de traitements MAJAS.

**Utilitaires utilisés :** IKJEFT01, COMPDB2, IGYWCL, IDCAMS, SORT.

## EXEMPLE

Exemple de programme COBOL/DB2.

**Fichiers :** table DB2.
**Structures notables :**
**Verbes notables :**
**Requêtes notables :** 
SELECT ... INTO ... FROM ... WHERE

## INSDBMES

Insertion des messages d'erreur d'un fichier séquentiel vers une table DB2.

**Fichiers :** séquentiel, table DB2.
**Structures notables :** COMP, table COBOL.
**Verbes notables :** 
**Requêtes notables :**
include ...
insert into ... values ...

## MAJASOPT

Programme de vérification des mouvements pour l'optimisation de la chaîne MAJAS.

**Fichiers :** séquentiel, table DB2.
**Structures notables :**
**Verbes notables :**
redefines
**Requêtes notables :**
include
insert into ... values ...

## MAJASSP

Sous-programme de récupération des labels des messages d'erreur à partir d'un code, un par un, et ce depuis une table DB2.

**Fichiers :** table DB2.
**Structures notables :**
**Verbes notables :**
move spaces to ...
**Requêtes notables :**
include
select ... into ... from ... where ...

## MAJASSP1

Sous-programme de récupération des labels des messages d'erreur à partir d'un code, stockés dans une table COBOL, et ce depuis une table DB2.

**Fichiers :** table DB2.
**Structures notables :** table indexée COBOL.
**Verbes notables :**
set ... to ...
**Requêtes notables :**
open
fetch ... into ...
close ...

## MAOPTP00/10/20/30/31/3A

Versions pré-finalisées du programme MAJAS.

**Fichiers :** séquentiel, table DB2.
**Structures notables :** COMP, COMP-3, COMP-4
**Verbes notables :**
call ... using ...
move ... to ... of ...
redefines
**Requêtes notables :**
include
insert into ... values ..

## MAOPTS01/02/11/12/3S

Sous-programmes ou accesseurs utilisés par les versions pré-finalisées du programme MAJAS.

**Fichiers :**
**Structures notables :** COMP, table COBOL, niveau 88, COMP-3.
**Verbes notables :** 
copy
read ... into ...
set ... to ...
move ... to ... of ...
**Requêtes notables :**
include
insert into ... values ...
open
close
fetch ... into ...
declare ... cursor for ... for fetch only
select ... from ... as ... inter join ... as ... on ... order by ...
update ... set ... where ....
delete from ... where ...

## MAOPTP32

Version finale du programme MAJAS avec ses accesseurs VSAM et DB2.

**Fichiers :**
**Structures notables :**
**Verbes notables :**
call ... using ...
**Requêtes notables :**

## MAOPTS3D

Version finale de l'accesseur DB2 pour le programme MAJAS.

**Fichiers :** table DB2.
**Structures notables :** niveau 88.
**Verbes notables :**
set ... to true
move ... of ... to ... of ...
**Requêtes notables :**
declare ... cursor for ... for fetch only
select ... from ...
update ... set ... where ...
delete from ... where ...
insert into ... values ...
open ...
close ...
fetch ... into ...

## MAOPTS3V

Version finale de l'accesseur VSAM pour le programme MAJAS.

**Fichiers :** KSDS, ESDS.
**Structures notables :** niveau 88.
**Verbes notables :**
copy
replacing
move ... of ... to ... of ...
read ... into ... invalid key ... end-read
rewrite ... from ... invalid key ... end-rewrite
delete ... invalide key ... end-delete
write ... from ... invalid key ... end-write
set ... to true
read ... into ...
**Requêtes notables :**

## PGM31PP

Programme principal de manipulation des tables RAYONS et AUTEURS.

**Fichiers :**
**Structures notables :** COMP, PIC d'édition.
**Verbes notables :**
call ... using ...
move zero to ...
**Requêtes notables :**

## PGM31SP

Accesseur du programme PGM31PP pour la manipulation des tables RAYONS et AUTEURS.

**Fichiers :** table DB2.
**Structures notables :** COMP, PIC d'édition.
**Verbes notables :**
move ... of ... to ...
**Requêtes notables :**
declare ... cursor for ... for fetch only
select distinct ... from ...
select * from ... where ...
open ...
fetch ... into ...
close ...

## SKELDB2

Squelette d'un programme DB2.

**Fichiers :**
**Structures notables :**
**Verbes notables :**
**Requêtes notables :**

## TPPGM2

**Fichiers :** table DB2.
**Structures notables :** COMP-3.
**Verbes notables :**
**Requêtes notables :**
insert into ... values ...
update ... set ... where ...
delete from ... where ...
select ... from ... where ...
declare ... cursor for ... for fetch only
close ...
fetch ... into ...

## TPPGM3

**Fichiers :** table DB2.
**Structures notables :** PIC d'édition.
**Verbes notables :**
move ... of ... to ...
move zero to ...
accept...
**Requêtes notables :**
declare ... cursor for ... for fetch only
select ... from ... where
open ...
fetch ... into ...
close ...
delete from ... where ...
update ... set ... where ...

## TPPGM11

**Fichiers :** table DB2.
**Structures notables :**
**Verbes notables :**
**Requêtes notables :**
declare ... cursor for ... for fetch only
select ... from ... where ...
fetch ... into ...
open ...
close ...

## TPPGM12

**Fichiers :** table DB2.
**Structures notables :** COMP.
**Verbes notables :**
**Requêtes notables :**
declare ... cursor for ... for fetch only
select ... from ... order by ... desc
fetch ... into ...
open ...
close ...

## TPPGM13

**Fichiers :** table DB2.
**Structures notables :** COMP.
**Verbes notables :**
**Requêtes notables :**
declare ... cursor for ... for fetch only
select ... from ... where ...
case ... when ... then ... else ... end
fetch ... into ...
open ...
close ...

## TPPGM30

**Fichiers :** table DB2.
**Structures notables :** COMP, PIC d'édition
**Verbes notables :**
accept
move zeros to ...
move spaces to ...
move ... of ... to ...
**Requêtes notables :**
declare ... cursor for ... for fetch only
select ... from ... where ...
open ...
fetch ... into ...
close ...

## TPPGM31

**Fichiers :** table DB2.
**Structures notables :** COMP, PIC d'édition
**Verbes notables :**
move zero to ...
move spaces to ...
move ... of ... to ...
**Requêtes notables :**
declare ... cursor for ... for fetch only
select distinct ... from ...
select ... from ... where ...
open ...
fetch ... into ...
close ...
delete from ... where ...
update ... set ... where ...

## TPPGM32

**Fichiers :** table DB2.
**Structures notables :** BINARY, table indexée COBOL de taille variable (DEPENDING ON).
**Verbes notables :**
initialize...
move corresponding ... to ...
set ... to ...
set ... up by ...
move ... of ... to ... of ...
display spaces
search ... at end ... when ... end-search ...
**Requêtes notables :**
declare ... cursor for ... for fetch only
select ... from ... order by ...
open ...
fetch ... into ...
close ...
