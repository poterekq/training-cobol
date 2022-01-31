# Index des fichiers JCL

| Fichier | Description | Outils |
|--------|-------------|-------|
| BACKUP | Créer une copie d'une bibliothèque. | IEBCOPY, IEFBR14, IF. |
| ESDSCRE | Créer un fichier ESDS. | IDCAMS (DELETE, DEF CL et DATA). |
| ESDSLOAD | Alimenter un fichier ESDS | IDCAMS (REPRO). |
| ESDSPRT | Afficher le contenu d'un fichier ESDS. | IDCAMS (REPRO, PRINT). |
| IEBCOPY | Copier une bibliothèque sur une autre. | IEBCOPY (COPY). |
| IEBGCOPY | Définir une procédure cataloguée utilisée par JCL13. Elle affiche le contenu d'un fichier en SYSOUT. | IEBGENER. |
| IEBGNGDG | Créer des fichiers QSAM GDG. | IEBGENER. |
| IEBGPARM | Membre utilisé par JCL11 et contant des paramètres pour IEBGENER. | |
| IEBUPDTE | Stocker des membres dans un PDS | IEBUPDTE. |
| JCL01 | Créer un fichier séquentiel. | IEFBR14. |
| JCL02 | Créer une bibliothèque. | IEFBR14. |
| JCL03 | Ecrire dans un fichier séquentiel. | IEBGENER. |
| JCL04 | Ecrire dans un membre de bibliothèque. | IEBGENER. |
| JCL05 | Copier le contenu d'un membre dans un autre membre. | IEBGENER. |
| JCL06 | Copier le contenu d'un membre dans un fichier séquentiel. | IEBGENER. |
| JCL07 | Afficher le contenu d'un membre dans la SYSOUT. | IEBGENER. |
| JCL08 | Copier des membres concaténés dans un fichier séquentiel. | IEBGENER, DLM. |
| JCL09 | Afficher le contenu de membres concaténés dans la SYSOUT. | IEBGENER. |
| JCL10 | Trier un fichier séquentiel et l'afficher dans la SYSOUT. | SORT (FIELDS), IEBGENER (GENERATE, RECORD). |
| JCL11 | Définir une procédure instream. Trier un fichier séquentiel et l'afficher dans la SYSOUT. | SORT (FIELDS), IEBGENER (GENERATE, RECORD). |
| JCL12 | Définir une procédure instream. Afficher le contenu d'un fichier en SYSOUT. | IEBGENER, IEBGCOPY (P). |
| JCL13 | Définir une procédure instream. Copier le contenu d'un membre dans un autre membre.| IEBGENER. |
| JCL14 | Utiliser une procédure cataloguée. Faire un tri. | JCLLIB, PROCTRI (P). |
| JCL15 | Utiliser une procédure cataloguée. Afficher le contenu d'un membre. | JCLLIB, IEBGCOPY (P). |
| JCL16 | Définir une procédure cataloguée PROCTRI, utilisée par JCL14. | SORT. |
| JCL17 | Définir une procédure instream, utiliser une procédure cataloguée.| JCLLIB, IEFBR14, IEBGCOPY (P), FICCR (P). |
| KSDSAIX | Définir un *alternate index* pour un KSDS. | IDCAMS (DELETE, DEF AIX, DEF PATH, BIX). |
| KSDSCRE | Créer un fichier KSDS. | IDCAMS (DELETE, DEF CL). |
| KSDSLOAD | Alimenter un fichier KSDS avec des données triées. | SORT, IDCAMS (REPRO). |
| KSDSPATH | Définir le PATH d'un KSDS pour y accéder par l'AIX. | IDCAMS (DELETE, DEF PATH). |
| KSDSPRT | Afficher le contenu d'un KSDS. | IDCAMS (REPRO, PRINT). |
| KSDSREOR | Créer une copie d'un KSDS, supprimer la source, créer une nouvelle source puis projeter la copie dessus. | IDCAMS (DEF CL, REPRO DELETE). |
| MAKEAIX | Définir une procédure cataloguée pour créer un AIX sur un KSDS. | IDCAMS. |
| MAKEFICH | Définir une procédure cataloguée pour créer un KSDS. | IDCAMS. |
| MKAIPARM | Membre utilisé par MAKEAIX pour supprimer un AIX, en créer un nouveau, créer un PATH ainsi qu'un BIX, puis afficher l'AIX. | |
| MKCOPY | Définir une procédure cataloguée pour copier le contenu d'un fichier vers un nouveau. | IEBCOPY. |
| MKFIPARM | Membre utilisé par MAKEFICH pour supprimer un VSAM puis en créer un nouveau sur la base d'un modèle. | |
| REORGLIB | Créer la copie d'une bibliothèque, supprimer cette dernière, créer une nouvelle bibliothèque puis y projeter le contenu de la copie. | IEBCOPY, IEFBR14. |
| RRDSCRE | Créer un fichier RRDS. | IDCAMS (DELETE, DEF). |
| RRDSLOAD | Alimenter un fichier RRDS avec des données instream. | IDCAMS (REPRO). |
| RRDSPRT | Afficher le contenu d'un fichier RRDS. | IDCAMS (REPRO, PRINT). |
| SKELJCL | Squelette de JCL. | |
| SORTNOM | Membre contenant des paramètres de tri sur fichier. | |
| SORTNUM | Membre contenant des paramètres de tri sur fichier. | |
| SORTVIL | Membre contenant des paramètres de tri sur fichier. | |
| TD3PROC1 | Définir une procédure cataloguée pour copier un fichier vers un autre. | IEBGENER. |
| TD3PROC2 | Définir une procédure cataloguée pour trier un fichier et l'envoyer vers un autre. | SORT. |
| TDEX4IDC | Exercice 4 du TD sur IDCAMS. | IDCAMS (DELETE, DEF, REPRO, PRINT, LISTCAT), SORT. |
| TDEX5IDC | Exercice 5 du TD sur IDCAMS. | IDCAMS (DELETE, DEF, REPRO), SORT. |
| TDEX6IDC | Exercice 6 du TD sur IDCAMS. | IDCAMS (DELETE, DEF, BIX, PRINT). |
| TDJCL01 | Créer un fichier séquentiel et une bibliothèque. | IEFBR14. |
| TDJCL02 | Copier, trier, afficher des fichiers séquentiels et membres. | IEBGENER (GENERATE, RECORD), SORT. |
| TDJCL03 | Définir des procédures internes de copie et de tri. | JCLLIB, IEBGENER, SORT. |
| TDSORTF | Membre contenant des paramètres de tri sur fichier. | |
| TESTUPDT | Modifier et créer des membres dans un PDS. | IEBUPDTE. |
| TPJCLP1 | Créer un fichier séquentiel et une bibliothèque. | IEFBR14. |
| TPJCLP2 | Copier, trier, afficher des fichiers séquentiels et membres. | IEBGENER (GENERATE, RECORD), SORT. |
| TPJCLP3 | Définir des procédures internes de copie et de tri. | JCLLIB, IEBGENER, SORT. |
| TPJCLP4 | Exercice 4 du TD sur IDCAMS. | IDCAMS (DELETE, DEF, REPRO, PRINT, LISTCAT), SORT. |
| TPJCLP5 | Exercice 5 du TD sur IDCAMS. | IDCAMS (DELETE, DEF, REPRO), SORT. |
| TPJCLP6 | Exercice 6 du TD sur IDCAMS. | IDCAMS (DELETE, DEF, BIX, PRINT). |
