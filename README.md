# TP2-UNIX

## Exercice 1
### 1.1
- sudo apt update
- sudo systemctl restart ssh
- Tester la connexion : ssh root@10.20.0.142

L'élément de la configuration ssh que j'ai changé est : PermitRootLogin.
Il existe plusieurs options : 
  - yes : permet au root de se connecter par ssh.
  - prohibit-password : impose l'authentification par clé.
    - Permet de se connecter en tant que root et empêche les attaques par force brute.
  - forced-commands-only : 
  - no : ne permet pas au root de se connecter par ssh.
