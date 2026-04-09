# Incident 01 - Bruteforce Authentication sur serveur Linux

## Contexte
Plusieurs tentatives de connexion SSH échouées ont été observées sur un serveur Linux exposé.

## Logs observés

Mar 18 02:11:04 web01 sshd[18422]: Failed password for invalid user admin from 185.220.101.4 port 49822 ssh2
Mar 18 02:11:07 web01 sshd[18424]: Failed password for invalid user test from 185.220.101.4 port 49831 ssh2
Mar 18 02:11:10 web01 sshd[18426]: Failed password for root from 185.220.101.4 port 49840 ssh2
Mar 18 02:11:13 web01 sshd[18429]: Failed password for root from 185.220.101.4 port 49849 ssh2
Mar 18 02:11:17 web01 sshd[18433]: Failed password for root from 185.220.101.4 port 49856 ssh2

Analyse

Multiples échecs de connexion en quelques secondes
Même IP source répétée
Plusieurs noms d’utilisateurs testés
Activité typique d’un bruteforce ou password spraying ciblant SSH

Hypothèse

Tentative de compromission d’accès à distance par bruteforce sur le service SSH.
Impact possible
Compromission d’un compte valide
Accès initial au serveur
Déploiement de malware ou pivot interne si succès

Réponse N1

Confirmer le volume et la fréquence des tentatives
Identifier si une connexion réussie a suivi les échecs
Bloquer ou faire bloquer l’IP source au niveau pare-feu
Vérifier les comptes ciblés
Escalader au N2 si succès d’authentification observé

Escalade au N2

Escalade nécessaire si :
un login réussi apparaît après les échecs
le compte visé est privilégié
d’autres hôtes subissent le même pattern

Conclusion

L’activité observée est cohérente avec un bruteforce SSH depuis une IP externe, nécessitant containment rapide et vérification d’un éventuel accès réussi.
