Créer le fichier VSAM ERRVSAM.

En sortie, fusion des fichiers FMVTSE et ASSURES3 pour produire ASSURES4 (KSDS). Création d'un fichier de statistiques et d'anomalies. Le fichier ETATANO est un séquentiel classique. Mettre les fichiers créés sous API1.ACTIF.

Pour un REPRO d'un fichier séquentiel vers un ESDS, un tri n'est pas nécessaire. C'est indispensable pour le KSDS. On le trie tout de même pour les besoins du TP.

Les structures des fichiers se situent sous API0.SOURCE.COPY, sous les CASSURE, CASSURE3, etc. La table des messages se situe sous TABMESS. On a aussi un fichier séquentiel MESS qui contient les mêmes messages. Créer un KSDS avec MESS, dont la clé correspond au 3 premiers caractères. Appeler un sous-programme qui vient lire le VSAM pour nous donner le message correspondant à la clé numéro 007. On renvoie alors le libellé associé à la clé vers le programme principal.

Codes mouvement de MVTPROJ en position 73 :
- C Création
- M Modification
- S Suppression
- X Invalide

Exemple de jeu d'essai sous API0.SOURCE.COPY($JEU).

![[proj1_jeu-test.png]]

On va faire de l'appariement de fichiers entre A et M (nos deux entrées) en mode séquentiel pour aboutir à ASSURES4. ASSURES4 est vide au départ et on le remplit avec des WRITE.

Pour la structure du programme MAJAS :

```
PERFORM INIT-PGM
PERFORM TRAIT-PGM UNTIL FIN-FICHIER
PERFORM STAT-PGM
PERFORM FIN-PGM
GOBACK.
```

Numéroter les paragraphes avec un pas de 10000.

```
PERFORM 10000-INIT-PGM
PERFORM 20000-TRAIT-PGM UNTIL FIN-FICHIER
PERFORM 30000-STAT-PGM
PERFORM 40000-FIN-PGM
GOBACK.
```

Dans un PERFORM de niveau 10000, je ne peux appeler que des paragraphes allant de 11000 à 19000. C'est la méthode IPO (Input Process Output).

![[proj1_ipo.png]]

La méthode IPO permet de facilement remonter les chaînes de PERFORM, du fait de la hiérarchie instaurée. On peut adapter la même nomenclature pour spécifier quels PERFORM appeler pour différentes technologies.

![[proj1_ipo2.png]]

Faire un DEFINE de ASSURES4 sur la base du ASSURES3.

Concernant les COPY, celui pour CASSURE3 est le suivant :

![[proj1_cassure3.png]]

Celui pour CASSURE4 est le suivant :

![[proj1_cassure4.png]]

Celui pour FMVTSE :

![[proj1_fmvtse.png]]

Pour définir un ESDS en COBOL :

![[proj1_def-esds.png]]