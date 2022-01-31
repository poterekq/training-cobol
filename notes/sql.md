# SQL

Bases de données hiérarchiques : IMS, travail avec le langage DL1. Chemin hiérarchique à suivre pour accéder aux données.

Bases de données relationnelles : Travail avec SQL.

Bases de données réseau.

En SQL, le code retour vaut 0 si tout est bon. Il est négatif s'il y a un souci.

Travail sur DB2 avec M.11, et plus précisément avec SPUFI. Sous SPUFI, les commentaires sont marqués par un -- en colonnes 1 et 2.

En DB2 : EXEC SQL et END EXEC en colonne 12.

Dans le JCL, penser à préciser l'emplacement où se trouvera l'extraction SQL, ainsi que le plan stocké au niveau de DB2.

## LDD

Langage de définition des données :
- CREATE (créer un objet, comme une table, un tablespace).
- ALTER (modifier un objet, comme ajouter une colonne).
- DROP (supprimer un objet).

## LMD

Langage de manipulation des données :
- INSERT (insérer une ligne).
- UPDATE (modifier une ligne).
- DELETE (supprimer une ligne).

## LCD

Langage de contrôle des données :
- GRANT (donner des droits sur des objets ou tables).
- REVOKE (retirer des droits).

# LCT

Langage de contrôle de transaction :
- COMMIT (valider une requête exécutée avant l'ordre COMMIT).
- ROLLBACK (annuler les requêtes qui précèdent le ROLLBACK, ou on annule jusqu'au précédent COMMIT).


## Langage SQL

Dans un programme COBOL, un bloc SQL est encadré par des `EXEC SQL ... END-EXEC`. Les requêtes du bloc sont à stocker dans un autre fichier, comme ça on peut tester le programme COBOL indépendamment du SQL.

![[requete_sql_cob.png]]

Il y a un `EXEC SQL ... END-EXEC` par requête !

Dans l'exemple ci-dessus, on stocke le résultat de la requête dans une variable COBOL nommée `MAXSALRY`. Les variables COBOL sont toujours précédées par `:`. On peut stocker l'ensemble des informations d'une ligne dans une variable COBOL composée. Au lieu de déclarer les variables dans la WORKING STORAGE, on va généralement utiliser l'outil `DCLGEN` pour créer toute la déclaration de la table pour la stocker dans un fichier puis l'utiliser avec un `COPY`.

Pour les programmes COBOL, on recommande de ne pas utiliser un `SELECT *`, mais plutôt de spécifier tous les noms de colonnes. Cela évite d'éventuels problèmes si on devait finir par ajouter une nouvelle colonne à la table.

**CHAR :** Chaîne de caractères de longueur fixe. Sous COBOL, ça donne un PIC X(...).
**VARCHAR :** Chaîne de caractères de longueur variable, avec une taille maximale spécifiée lors de la création de la table. Sous COBOL, ça donne une variable composée de deux niveaux, avec le contenu du champ PIC X(...), et la longueur du champ PIC 9(...).

![[ex_tab.png]]

Dans l'exemple ci-dessous, on insère dans la table `EMP` du *tablespace* `DSN8710`.

Quand on fait un `GROUP BY`, on doit utiliser toutes les colonnes précisées dans le `SELECT`, tel que :

```sql
SELECT AUTEUR, GENRE
FROM LIVRES
GROUP BY AUTEUR, GENRE;
```

Ordre des opérations :
1) SELECT
2) FROM
3) WHERE (filtre sur le SELECT)
4) GROUP BY
5) HAVING (filtre sur le GROUP BY)
6) ORDER BY

Pour tester s'il y a *au moins* une ligne retournée par une sous-requête, on utilise un `WHERE EXISTS`.

## Le catalogue DB2

On accède au catalogue DB2 avec `m.11` :
- SPUFI : Exécution du SQL.

- DCLGEN : Générateur de la déclaration COBOL pour une table DB2. Dans l'exemple ci-dessous, la déclaration de trouve dans le PDS `API1.SOURCE.DCLGEN`. Elle est importée dans le programme COBOL à l'aide d'une clause `INCLUDE` au lieu d'une `COPY`, entre des balises `EXEC SQL ... END-EXEC` en colonne 12.

![[m11_dclgen.png]]
Les options numéros 6 à 12 permettent de structurer les attributs. L'option 12 en particulier permet de gérer les valeurs nulles pour le programme COBOL.

- Le reste des options est utilisé pour compiler et exécuter un programme COBOL.


Avec `m.13`, on accède aux outils d'administration DB2 :
- DB2 system catalog : Accès à tous les objets (tablespaces, tables, index, vues). On précise les `Name` (nom de l'objet) et `Owner` (nom de l'utilisateur) pour accéder à un objet. On précise l'option dans la ligne de commande primaire. Une fois la liste des objets visualisée, plusieurs commandes de ligne sont possibles. Il faut les préciser en première position de la colonne `Sel`.

![[ex_m13.png]]

Si on fait un `AL` par exemple pour altérer une table, il faut mettre `s` ensuite à côté de l'option souhaitée, le `ADD column` par exemple.

DBRM correspond au module de requête. Avant de compiler un programme COBOL qui contient du SQL, on réalise une pré-compilation qui permet de séparer les deux. DBRM permet d'accéder à la partie SQL qui a été isolée.

- Execute SQL statements : Utilisé pour exécuter une commande SQL, sans utiliser de membre comme avec SPUFI. SPUFI reste préférable, car les requêtes sont enregistrées quelque part.

# TP SQL/DB2

## Consultation du catalogue

**A consulter :**
Structure de la table SYSIBM.SYSTABLES:  
[https://www.ibm.com/docs/en/db2-for-zos/11?topic=tables-systables](https://www.ibm.com/docs/en/db2-for-zos/11?topic=tables-systables "https://www.ibm.com/docs/en/db2-for-zos/11?topic=tables-systables")  
  
Structure de la table SYSIBM.SYSCOLUMNS:  
[https://www.ibm.com/docs/en/db2-for-zos/11?topic=tables-syscolumns](https://www.ibm.com/docs/en/db2-for-zos/11?topic=tables-syscolumns "https://www.ibm.com/docs/en/db2-for-zos/11?topic=tables-syscolumns")  
  
Structure de la table SYSIBM.SYSINDEXES:  
[https://www.ibm.com/docs/en/db2-for-zos/11?topic=tables-sysindexes](https://www.ibm.com/docs/en/db2-for-zos/11?topic=tables-sysindexes "https://www.ibm.com/docs/en/db2-for-zos/11?topic=tables-sysindexes")

1) Liste des informations concernant les colonnes de la table EMPLOYE.

Aller en `m.13`, renseigner les champs pour filter les tables.

![[cat_q1.png]]

Puis utiliser la commande de ligne `C` pour visualiser les informations sur les colonnes de la table `EMPLOYE`.

Ou :

```sql
SELECT * 
FROM SYSIBM.SYSCOLUMNS 
WHERE TBNAME = 'EMPLOYE' 
  AND TBCREATOR = 'API1';
```

2) Liste des tables contenant la colonne NOM.

![[cat_q2.png]]

Ou :

```sql
SELECT * 
FROM SYSIBM.SYSCOLUMNS 
WHERE NAME = 'NOM';
```

3) Nombre de colonnes de la table EMPLOYE.

Utiliser la commande de ligne `/` sur la table `EMPLOYE` dans le catalogue puis regarder la valeur de `COLCOUNT`. Ici, 6 colonnes.

Ou :

```sql
SELECT NAME, COLCOUNT 
FROM SYSIBM.SYSTABLES 
WHERE NAME='EMPLOYE' 
  AND CREATOR='API1';
```

4) Quelles tables ai-je créé ?

![[cat_q4.png]]

Ou :

```sql
SELECT NAME 
FROM SYSIBM.SYSTABLES 
WHERE CREATOR = 'API1';
```

5) Liste des tables ayant plus de deux index.

Utiliser l'option 2 du `m.13`, puis l'option 1. Utiliser la requête suivante :

```sql
SELECT TBCREATOR, TBNAME, COUNT(*) AS NB_INDEX 
FROM SYSIBM.SYSINDEXES 
GROUP BY TBCREATOR, TBNAME 
HAVING COUNT(*) > 2;
```

6) Nom de la table ayant le plus de lignes.

```sql
SELECT NAME, CARDF
FROM SYSIBM.SYSTABLES 
WHERE CARDF = 
  (SELECT MAX(CARDF) 
   FROM SYSIBM.SYSTABLES);
```

