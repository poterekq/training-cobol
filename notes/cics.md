# CICS

Code de transaction sur 4 caractères.

On n'utilise de JCL. Toute la dimension interactive est prédéfinie par le développeur. On sort de CICS avec F12.

Send : affichage d'un écran
Receive
Analyse de la saisie

![[ex_cics.png]]

Dans le PP, on a ind = 0 au début. On fait le send, puis on move 1 dans ind. Alors, on appelle le pp comme SP (récursivité). Comme ind ne vaut pas 0, on passe dans la branche de réception et d'analyse. On sort du SP, puis on libère le programme CICS avant de terminer le PP.