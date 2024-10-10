# TP2-UNIX

## Source : 
- https://www.hostinger.fr/tutoriels
- https://www.delafond.org/traducmanfr/man/man5/sshd_config.5.html

## Exercice 1
### 1.1
- sudo apt update
- sudo systemctl restart ssh
- Tester la connexion : ssh root@10.20.0.142 ou ip a.

Source : man sshd_config.

L'élément de la configuration ssh que j'ai changé est : PermitRootLogin.
Il existe plusieurs options : 
  - yes : Permet au root de se connecter par ssh.
  - prohibit-password (without-password ) : Désactive l'authentification par mot de passe donc impose l'authentification par clé.
    - Permet de se connecter en tant que root et empêche les attaques par force brute.
  - forced-commands-only : Permet au root de se connecte par authentification de clé publique.
    - Cela permet d'effectuer des sauvegvardes à distance.
    - Désactive toutes les autres methodes d'authentification pour routes.
  - no : Ne permet pas au root de se connecter.

### 1.2

- ssh-keygen : Permet de generer une clé.
- Demande l'emplacement pour stocker la clé.
- Demande la phrase de passe.
- Confirmation phrase de passe.
- Resultat :
  - Your identification has been saved in /root/.ssh/id_rsa
    Your public key has been saved in /root/.ssh/id_rsa.pub
    The key fingerprint is:
    SHA256:hXbGIaWwIfJg4I2TkMs2RBsTqiadbU5H6ORm6IEu/ZQ root@serveur-correction
    The key's randomart image is:
    +---[RSA 3072]----+
    |+B= . o ..o      |
    |=oB+ o + = .     |
    |+B .+ o + =      |
    |o*.B . . +       |
    |=.* O . S        |
    |+o B o           |
    |..o E            |
    |.  o             |
    |    .            |
    +----[SHA256]-----+
  - En cas réel c'est une mauvaise idée de ne pas mettre de phrase de passe car la phrase de        passe renforce la sécurite de connexion au serveur. Et si une personne parvient a avoir la clé, il lui faudra cette phrase pour l'utiliser.

  
### 1.3
- Avoir la clé : cat ~/.ssh/id_rsa.pub
  - AAAAB3NzaC1yc2EAAAADAQABAAABgQCZpqXUklKwzgIdjXknUGlHFEcteaPjXaLC/d1kDKnadUaTH1kwPezXOPNjKxJdSsyEisbssaVBQXQqEgHoYMVIyvVFTq/u7z4oe6Dep73JuzYOdv4eG1/CBVEM9567KD499SoxAoEkRtBhH/7uBf0k+7q/fNaj5gpT4E+b2hP8w+At+i/dfEZqloFJunGbFa4Z2ftNAq2lYn6d6VqXL4ZzQZ0/Ns6YNKsGHaIj72rH+y7PWGKfdfyh8UhsO0Fx8SSYKU4bKwOMBkH99uuAQbcS6DoxFg3xx3ixVzd0pqqaSxSGupyygxOLaKnCAECB2hkxtSI6HzcP8bIdhEZE6yyKukHnRqtUAyuwmMP04AA05FtZOHhjkyN7b62Yuim1NnsXx9IRMd4LbSEP9mZvVmiiQGqGo0FHLhxFgQya8mAnsOxKOepwF/vNBnuLfOAP3D2/zMIVaftlwFKUN/q10QvWjeFA5UdsTYHTxRlCr1WWkg/VGj0ugt2RlniB5KV3Ri8=
- Création : cat>authorized_keys
- Colle la clé puis ctrl+d
  
- Donner les droits au root : chmod 700 ~/.ssh/authorized_keys

### 1.4
- ssh -i id_rsa.pub root@10.20.0.142

### 1.5
- ssh-copy-id root@10.20.0.142 : Number of key(s) added: 1

Now try logging into the machine, with:   "ssh 'root@10.20.0.142'"
and check to make sure that only the key(s) you wanted were added.


nano /etc/ssh/sshd_config 
-  #PasswordAuthentication yes =>  PasswordAuthentication no
-  PermitRootLogin yes => PermitRootLogin prohibit-password







    

