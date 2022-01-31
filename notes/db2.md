# DB2

4 composantes dans DB2 :
- Database services : Composante principale d'exécution des requête.
- System services : Communication entre les sous-systèmes.
- IRLM : Gère le blocage (hold) des ressources. Quand un table est accédée en écriture, tous les autres utilisateurs ne peuvent qu'y accéder en lecture sur une version avant modification.
- Distributed Data Facility.

L'option m.11 correspond à du DB2I (DB2 Interactif). Le DCLGEN permet de générer la déclaration COBOL pour une table SQL. PROGRAM PREP est utilisé pour faire la pré-compilation, la compilation, le bind et l'exécution du programme. Pour cette dernière opération, on peut passer par le menu m.11, ou bien utiliser un JCL.

Quand on inclut du SQL dans un programme, il faut vérifier le code du langage source, ainsi que le code SQL. Cela implique une séparation du SQL et du code source.

Pour le graphique p. 13 :

La précompilation permet de séparer le code COBOL du code SQL.

![[schema_db2.png]]

Pour la compilation d'un programme COBOL avec DB2, le JCL change légèrement :
- L'étape APPROC fait la précompilation et la compilation.
- STEPDB2.DBRMLIB : Là où se trouve le code SQL suite à la précompilation.
- STEPLNK.SYSLMOD : Là où se trouve le code COBOL.
- La vérification des requêtes SQL se fait dans le BIND. On donne le chemin du DBRM (DBRMLIB), puis on fait le BIND PLAN. Il consiste à créer un plan, dont le nom correspond généralement à celui du programme. Cette étape prend les requêtes SQL du programme, les analyse, vérifie les droits d'accès au BD, puis si tout se passe bien, un plan est généré.
- L'exéccution correspond à la STEPRUN. Pour l'exécution, on passe par le programme IKJEFT01 qui exécute les programmes COBOL avec accès à DB2. Pour exécuter un programme, on donne son nom, puis on précise également le nom du plan.

DBRM : Fichier qui contient la liste des requêtes SQL.
Plan : Objet créé sous DB2 qui est un résultat de l'analyse des requêtes. S'il y a une erreur, le plan n'est pas généré et l'étape de BIND renvoie un code SQL négatif.

Un package correspond à un programme, qui est le résultat de la vérification du code SQL contenu dans le DBRM. Un plan peut être généré à partir d'un ou plusieurs packages ou DBRM. 1 package résulte d'un programme. 1 plan résulte d'un ou plusieurs programmes.

Une collection est l'équivalent d'un PDS sous TSO, c'est simplement un ensemble de packages. Les collections sont facultatives, on peut créer un plan directement à partir des packages.

On pourrait créer un plan à partir d'un DBRM, mais il est recommandé de générer un package à partir du DBRM et d'utiliser celui-ci. Cela permet de réduire la charge d'administration et de maintenance.

BIND : étape de création du plan ou du package.

DBRM : généré lors de la pré-compilation et contient le code SQL.

## SQL dans les programmes COBOL

SQLCA : Zone de communication qui permet de récupérer le résultat d'une requête, SQL Code 0 par exemple. Le code erreur est donc stocké dans la SQLCA.

Dans la zone de communication, la variable qui nous intéresse le plus est SQLCODE, qui renseigne sur le statut de l'exécution de la requête. S'il vaut 0, alors c'est bon. S'il faut 100, alors la requête a renvoyé toutes les lignes de la table, ou bien cette dernière était vide. Une autre variable, SQLSTATE, indique si l'exécution s'est bien déroulée ou non. C'est la même chose.

Dans les programmes que l'on va créer, on va utiliser le WHENEVER. S'il y a un message d'erreur, on appelle le programme DSNTIAR qui affiche le message, puis on met fin au programme.

![[whenever.png]]

Quand une variable peut prendre la valeur NULL, il faut créer une variable indicateur. Celle-ci renseigne si la valeur NULL a été reçue suite à une requête. Pour utiliser une variable indicateur, on utilisation la notation `:ma_variable:ma_variable_indicateur`. Si l'indicateur vaut 0, on peut utiliser `ma_variable` car elle ne vaut pas NULL. S'il vaut -1, on ne doit pas car elle contient NULL.

![[variable_indicateur.png]]

DB2 renvoie une erreur si on ne déclare pas de variable indicateur à côté d'une variable dans laquelle on stocke la valeur d'une colonne qui peut prendre NULL.

Avec les curseurs, le FETCH est l'équivalent du READ qu'on ferait sur un fichier, et permet de récupérer une ligne. Si on oublie le CLOSE, le curseur se ferme à la fin du programme, mais il est recommandé de le préciser. Pour l'instant, on va surtout déclarer les curseurs en mode FOR FETCH ONLY (lecture seule) ou FOR UPDATE (lecture avec possibilité de mise à jour). Toute la requête est écrite dans le DECLARE du curseur. Le OPEN permet d'exécuter la requête, la table résultante étant créée en mémoire. Le FETCH permet de récupérer ligne par ligne la table résultante, lorsqu'on englobe le bloc EXEC SQL par un PERFORM UNTIL.

Ordre des opérations réalisées :
- DECLARE (de préférence dans la WORKING STORAGE, mais fonctionne en PROCEDURE DIVISION).
- OPEN (PROCEDURE DIVISION).
- FETCH (PROCEDURE DIVISION).
- CLOSE (PROCEDURE DIVISION).

Dans la WORKING STORAGE :

```cobol
       EXEC SQL
	     DECLARE CURSEUR_1 CURSOR FOR
	       SELECT AUTEUR, TITRE
	       FROM LIVRES
	     FOR FETCH ONLY
	   END-EXEC
```

Dans la PROCEDURE DIVISION :

```cobol
       EXEC SQL
	     OPEN CURSEUR_1
	   END-EXEC

	   EXEC SQL
	     FETCH CURSEUR_1
	     INTO :AUTEUR, :TITRE
	   END-EXEC
	  
	   PERFORM UNTIL SQLCODE NOT = ZERO
	     IF AUTEUR = 'HUGO'
           EXEC SQL
	         UPDATE LIVRES
	         SET PRIX = PRIX * 1.1
	  * MET A JOUR UNIQUEMENT LE PRIX POUR LA LIGNE ACTUELLE DU CURSEUR
	  * C'EST UNE MISE A JOUR POSITIONNELLE
	         WHERE CURRENT OF CURSEUR_1
	       END-EXEC
	     END-IF
	  
	     DISPLAY AUTEUR ', ' TITRE
	  
	     EXEC SQL
	       FETCH CURSEUR_1
	       INTO :AUTEUR, :TITRE
	     END-EXEC
       END-PERFORM

	   EXEC SQL
	     CLOSE CURSEUR_1
	   END-EXEC
```

Les COMMIT intermédiaires sont utilisés pour des batchs redémarrables. Ils permettent de sauvegarder une partie du travail qui a été fait sur un très gros fichier. Si le programme plante, on reprend le traitement au moment du dernier COMMIT. Pas besoin d'un COMMIT pour les batchs non redémarrables.

Pour créer un package via m.11 :
- Pré-compilation pour créer le DBRM sous DBRMLIB.
- Créer le package à partir du membre précédent dans le menu BIND.
- A partir du même menu (BIND), on peut créer le plan.

![[creer_package.png]]

Dans cet exemple, les packages sont créés dans la collection TEST.

Pour créer un plan, on reste en m.11 mais on utilise la commande 1. Ensuite, on précise différents champs. INCLIDE PACKAGE LIST pour inclure des package dans un plan. On valide ensuite.

![[creer_plan_1.png]]

On arrive alors sur un nouveau menu où il faut préciser la position des collections, le nom des collections, et les packages souhaités.

![[creer_plan_2.png]]

On remplit les lignes, puis on valide avec F3. DB2I affiche alors un BIND FOR PLAN SUCCESSFUL.

Pour créer le plan seulement à partir d'un DBRM :

![[cree_plan_3.png]]

Si on veut le créer à partir de plusieurs DBRM, il suffit de passer ADDITIONAL DBRMS à YES. Si on veut créer un plan qui contient des DBRMS et packages, on passe le INCLUDE PACKAGE LIST à YES.

Si on veut, on peut aussi le faire depuis le JCL, avec :

![[creer_plan_4.png]]

Cela permet d'ajouter des paquets. Si on veut mettre tous les paquets d'une collection TEST, alors on écrit simplement `TEST.*`. Si on préfère utiliser uniquement les DBRM ou des packages discrets, alors dans MEMBRE on met la liste des éléments, séparés par des virgules.

## Gestion programme principal et sous-programme

Lorsqu'on crée un programme principal, il est recommandé de placer toute la partie SQL dans un sous-programme jouant le rôle d'accesseur.

A ce moment-là, il faut compiler le sous-programme avec le fichier `COMPDB21`, qui réalise la pré-compilation et crée un DBRM.

![[compdb1.png]]

Ensuite, on se rend en `m.11` pour créer d'abord un package à partir du DBRM (renseigner nom de collection et nom de package), puis ensuite un plan (préciser nom du plan, `ENTREE`, puis renseigner les noms de collection et de package et confirmer avec `F3`.

Pour créer le paquet, il faut renseigner les lignes 2 et 4 :

![[bind-package.png]]

Et pour le plan, on précise la ligne 5 en ayant nettoyé les 4 premières.

![[bind-plan.png]]

![[bind-plan-infos.png]]

Ensuite, il faut compiler le programme principal avec `COMPLKD`.

![[db2-complkd.png]]

Enfin, on compile le tout avec `COMPDB22`. Dans ce fichier, il faut penser à ajouter le PDS dans lequel se situe le programme principal compilé, sur la carte STEPLIB. Ici, la compilation avec CMPLKD l'a placé sous API1.COBOL.LOAD. Dans le RUN, il faut préciser PROGRAM(mon_programme_principal) et PLAN (mon_plan).

![[compdb2.png]]

## Projet

Pour les test :

Utiliser API10.COB.MVTERR. On continue le traitement s'il y a une erreur !

Tester les high-value, low-value, espaces...

matricule non vide ?
matricule numérique ?
adresse renseignée ?
nom renseigné ?
etc.

Outil ICETOOL pour retirer les doublons :

![[icetool.png]]

Pour sauvegarder une SYSOUT, on peut utiliser l'option XDC. On l'utilise comme option de ligne sur l'un des morceaux du log de sortie. On renseigne ensuite les champs :

![[xdc.png]]

Pour exécuter du SQL via un JCL :

![[jcl-sql.png]]

---

## Notes évaluation

DB2 dispose de 4 composantes :
- Database services : Exécute les ordres SQL, prépare le traitement des programmes d'application et contrôle leur exécution.
- System services : Assure la connexion entre DB2 et les autres sous-systèmes (TSO, IMS, CICS), et réalise des fonctions de service (journalisation, statistiques).
- IMS Resource Lock Manager (IRLM) : Gère le blocage des ressources demandées simultanément par plusieurs utilisateurs.
- Distributed Data Facility (DDF) : Composant optionnel qui permet l'accès à des données résidant sur un autre système relationnel.

DB2 peut être accéder de 4 façons :
- A partir de TSO via QMF ou DB2I.
- A partir de programme fonctionnant avec IMS/DC.
- Depuis des programmes batch DL/1.
- Depuis des programmes fonctionnant avec CICS.

SPUFI (SQL Processor Using File Input). Exécute des ordres SQL, qui se terminent par un `;`.

Les ordres SQL peuvent être placés dans des programmes hôtes. Ces programmes doivent être préparés et exécutés de façon particulière pour accéder aux ressources DB2.

DCLGEN (Générateur de déclarations) définie la structure d'une table en un format adapté à COBOL. Cette structure est intégrée via un `INCLUDE` contenu dans un bloc `EXEC SQL`.

---

## Nouvelle version du projet...

![[projet_db2_new.png]]

![[projet_db2_new2.png]]

1 PP qui fait l'ACCEPT et le CALL.
Le SP évalue le code fonction, réalise l'opération demandée, puis renvoie un code-retour au PP.

Penser à toujours réserver une zone de FILLER pour un ACCEPT ou la définition d'un fichier ! Cela permet d'ajouter de nouvelles choses si besoin.

![[ppsp_data.png]]

Note : Pas besoin de LINKAGE dans le PP pour récupérer la SYSIN.