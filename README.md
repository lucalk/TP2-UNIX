# TP2-UNIX

## Exercice 1
### 1.1
- sudo apt update
- sudo systemctl restart ssh
- Tester la connexion : ssh root@10.20.0.142

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
  -
