# TP2-UNIX

## Source : 
- https://www.hostinger.fr/tutoriels
- https://www.delafond.org/traducmanfr/man/man5/sshd_config.5.html
- man
- chatgpt

## 1 Secure Shell : SSH
### 1.1
- Pour me connecter en root : 
  
  - Lancement de la VM : Vbox LicenceProPreInstalled-2024
    - Connexion : login et password = root

  - Sur la VM : 
    - sudo apt update
    - sudo systemctl restart ssh
  
   - Sur la machine :
     - Tester la connexion : ssh root@10.20.0.142 ou la commande "ip a" pour voir l'ip.

Source : man sshd_config.

- L'élément de la configuration ssh que j'ai changé est : PermitRootLogin yes.

- Il existe plusieurs options : 
  - yes : Permet au root de se connecter par ssh.
  - prohibit-password (without-password ) : Désactive l'authentification par mot de passe donc impose l'authentification par clé.
    - Permet de se connecter en tant que root et empêche les attaques par force brute.
  - forced-commands-only : Permet au root de se connecte par authentification de clé publique.
    - Cela permet d'effectuer des sauvegvardes à distance.
    - Désactive toutes les autres methodes d'authentification pour routes.
  - no : Ne permet pas au root de se connecter.

### 1.2

Pour générer une clé : 
  - Commande "ssh-keygen" : Permet de generer une clé.
  - Demande l'emplacement pour stocker la clé. => entrée
  - Demande la phrase de passe. => entrée
  - Confirmation phrase de passe. => entrée
  - Resultat :
    ```bash
    Your identification has been saved in /root/.ssh/id_rsa
    Your public key has been saved in /root/.ssh/id_rsa.pub
    The key fingerprint is:
    SHA256:hJtxmwzSiAZNIZCC23BKD4uw7xtSUgWcK1P4Dia2/ck root@serveur-correction
    The key's randomart image is:
    +---[RSA 3072]----+
    |**++.            |
    |B=*o o .         |
    |=XB.o = o        |
    |X*+. . O o       |
    |=*+   o S        |
    | +o.             |
    |... o .          |
    | ... E           |
    |  ..             |
    +----[SHA256]-----+
    ```
  
- En cas réel c'est une mauvaise idée de ne pas mettre de phrase de passe car elle renforce la sécurite de connexion au serveur. Et si une personne parvient a avoir la clé, il lui faudra cette phrase pour l'utiliser.

  
### 1.3
- Avoir la clé : cat ~/.ssh/id_rsa.pub
  
  ```bash
  AAAAB3NzaC1yc2EAAAADAQABAAABgQCy/kMlcFKp23zkr3zHJHUheryyZXHQk6uIHONRqDRMKNQVysN584LExtLSIYWy1RfHQnalyDWDsqGGiZs6kGcsu3lsjLTZp3862Q5GjexUl+1WMMFYXBNaMnDNapKYmRMVWwETI8Me8cgP5ac5146v5OeLuYnRA2MxL2W1Mej4lXd7wAWbayr7JY4O8NEoahoesBbvkf+RqqvCeHaqgOuWfXolINgyBsDwcrM01Cc3YzA8RE932MmV+pepjpksG5UrHJHCI6ScGwgVd3yNSHaQ86D2S1UOSQMtV9BlI4GSjrIoMFn/bdq3WJzGrX1HGzPcldilbNr7uRpo9IfnZUpzA1Frtt7MYdujJhMwlJLsfmOimXNHHMl019HGqiqPmYQ8kX4mNqpsJu5xlnhh+AlkMUPE8d779wsi4dUXbDnBSsa/VX1KxZ3nOCn9LSB5WUuyYPMxymSSJY2bMnn98YxI8YUMzqudr1lLR4vb9DDiKqKdRpnTVlukWIUYjtCXwIk=
  ```
  
- Création du fichier dans ".ssh" : cat>authorized_keys
- Colle la clé puis ctrl+d
- Donner les droits au root : chmod 700 ~/.ssh/authorized_keys
- Déconnexion : exit

### 1.4
Connexion : ssh -i id_rsa.pub root@10.20.0.142 

### 1.5
Revoir dans tléchargement ce qui était là !!!!

La procédure pour sécuriser le machine via ssh pour root par clef seulement : 
  - nano /etc/ssh/sshd_config
  - #PasswordAuthentication yes =>  PasswordAuthentication no
  - #PubkeyAuthentication yes => PubkeyAuthentication yes
  - PermitRootLogin yes => PermitRootLogin prohibit-password

## 2 Processus

### 2.1
- TIME : correspond au temps utilisé par le processus
- Le processeur ayant le plus été utilisé sur la machine est :
    root        1104  200  0.0 R+   15:18:07 00:00:00 ps -eo user,pid,%cpu,%me
- Le premier processus lancé après le démarrage de la machine est :
    root           1  0.0  0.1 Ss   14:14:08 00:00:01 /sbin/init
- Le serveur tourne depuis 1h29min08sec
- Pour établir le nombre de processus créés depuis le démarrage est de approximativement : 1140 :
  - ```bash
    root        1140  100  0.0 R+   15:54:01 00:00:00 ps -eo user,pid,%cpu,%mem,stat,start,time,command
    ```
### 2.2
- L'option permettant d'afficher le PPID d'un processus est ppid : ps -eo pid,ppid ...
- La commande pour afficher dans l'ordre les PPID est
  ```bash
    root@serveur-correction:~# ps -eo pid,ppid --sort=ppid
        PID    PPID
      1       0
      2       0
    324       1
    355       1
    589       1
    591       1
    592       1
    594       1
  ```

### 2.3
apt  search pstree
apt search psmisc
apt install psmisc
```bash
  root@serveur-correction:~# pstree
systemd─┬─cron
        ├─dbus-daemon
        ├─dhclient
        ├─login───bash
        ├─sshd───sshd───bash───pstree
        ├─systemd───(sd-pam)
        ├─systemd-journal
        ├─systemd-logind
        ├─systemd-timesyn───{systemd-timesyn}
        └─systemd-udevd
```

- D'apès la connaissance c'est MAJ + m
```
    PID UTIL.     PR  NI    VIRT    RES    SHR S  %CPU  %MEM    TEMPS+ COM.                          
    324 root      20   0   32948   8528   7436 S   0,0   0,1   0:00.32 systemd-journal               
    594 root      20   0   25404   7792   6772 S   0,0   0,1   0:00.19 systemd-logind                
    637 systemd+  20   0   90064   6716   5840 S   0,0   0,1   0:00.17 systemd-timesyn               
    355 root      20   0   25864   6308   4696 S   0,0   0,1   0:00.20 systemd-udevd
```
- Le plus gourmand est :
```bash
  324 root      20   0   32948   8528   7436 S   0,0   0,1   0:00.32 systemd-journal               
```
Il correspond au systeme de journalisation
- Commanes interactives :
  - Changer la couleur : C'est avec la touche z qu'on change de couleur.
  - Mettre en évidence la colonne de trie : C'est avec la touche x.
  - Changer la colonne de trie :
    Installation de htop : apt-get install htop
  - htop :
    - Avantages :  
      - Cette commande affiche les processus actifs avec une meilleur interface ( couleurs ... ) et organisation ( informations mieux détaillées ).
      - Elle apporte une meilleure navigation et permet des actions groupées sur plusieurs processus.
      - Elle simplifie les actions sur les processus
    - Inconvénients :
      - + De consommation de ressources CPU.
      - Ne fonctionne pas dans tous les environnements.
      - Moins d'informations affichées. 

## 3 Exercice 2 : Arrêt d’un processus

### 3.1

Création du fichier date.sh : 
  - cat>date.sh
  - coller : #!/bin/sh
while true; do sleep 1; echo -n ’date ’; date +%T; done

Création du fichier date-toto.sh : 
- cat>date-toto.sh
- #!/bin/sh
while true; do sleep 1; echo -n ’toto ’; date --date ’5 hour ago’ +%T; done


- date.sh :
  - Droit du fichier :
    ```bash
      root@serveur-correction:/# chmod +x date.sh
      ```  
  - ```bash
    root@serveur-correction:/# ^C
    root@serveur-correction:/# ./date.sh
    ’date ’13:51:35
    ’date ’13:51:36
    ’date ’13:51:37
    ```
  - ```bash
    ^Z
    [1]+  Stoppé                 ./date.sh
    ```
  - ```bash
    root@serveur-correction:/# jobs
    [1]+  Stoppé                 ./date.sh
    ```
  - ```bash
    root@serveur-correction:/# fg
    ./date.sh
    ’date ’13:51:51
    ’date ’13:51:52
    ’date ’13:51:53
    ’date ’13:51:54
    ’date ’13:51:55
    ’date ’13:51:56
    ```


Changement de fichier date-toto.sh :
  - ```bash
    root@serveur-correction:/# ./date-toto.sh
    toto 11:29:51
    toto 11:29:52
    toto 11:29:54
    ^Z
    [2]+  Stoppé                 ./date-toto.sh
    ```
  - ```bash
    root@serveur-correction:/# jobs
    [1]+  Stoppé                 ./date-toto.sh
     ```
  - ```bash
    root@serveur-correction:/# fg
    ./date-toto.sh
    toto 11:31:44
    toto 11:31:45
    toto 11:31:46
    toto 11:31:47
    ^C
    ```
  - ```bash
    root@serveur-correction:/# ./date.sh
    ’date ’14:03:45
    ’date ’14:03:46
    ^Z
    [2]+  Stoppé                 ./date.sh
    root@serveur-correction:/# ps
    PID TTY          TIME CMD
    653 pts/0    00:00:00 bash
    923 pts/0    00:00:00 date.sh
    924 pts/0    00:00:00 sleep
    931 pts/0    00:00:00 date.sh
    936 pts/0    00:00:00 sleep
    937 pts/0    00:00:00 ps
    root@serveur-correction:/# kill
    kill : utilisation :kill [-s sigspec | -n signum | -sigspec] pid | jobspec ... ou kill -l [sigspec]
    root@serveur-correction:/# kill 923
    ```
  - ```bash
    root@serveur-correction:/# ps
    PID TTY          TIME CMD
    669 pts/0    00:00:00 bash
    711 pts/0    00:00:00 date-toto.sh
    748 pts/0    00:00:00 sleep
    766 pts/0    00:00:00 ps
    root@serveur-correction:/# kill -9 711
    root@serveur-correction:/# ps
    PID TTY          TIME CMD
    669 pts/0    00:00:00 bash
    767 pts/0    00:00:00 ps
    [1]+  Processus arrêté      ./date-toto.sh
    ```

```bash
- #!/bin/sh
while true; do sleep 1; echo -n ’date ’; date +%T; done
```
- Permet d'afficher date suivi de l'heure actuelle et de mettre a jour chaques secondes.

```bash
while true; do sleep 1; echo -n ’toto ’; date --date ’5 hour ago’ +%T; done
```
- Permet d'afficher toto suivi de l'heure qu'il était il y a 5 heures et met à jour chaques secondes.

## 4 Exercice 3 : les tubes

- La différence entre tee et cat est que cat permet d'afficher le contenu d'un fichier alors que tee permet d'afficher le resultat d'une commande et enregistre en même temps dans le fichier.
- Les commandes :
  - ls | cat : Permet d'afficher la liste de tout ce qu'il y a dans le repertoire actuel.
    ```bash
    root@serveur-correction:/# ls | cat
    bin
    boot
    date.sh
    date-toto.sh
    ...
    ```
  - ls -l | cat > liste : la première partie va lister tout ce qu'il y a dans le repertoire actuel avec plus de détails. La deuxieme partie va créer un fichier liste et tout enregistrer à l'interieur.
  - ls -l | tee liste : La première partie est comme la première partie précédente. La deuxieme partie va afficher le tout et l'écrire aussi dans le fichier.
    ```bash
    root@serveur-correction:/# ls -l | tee liste
    total 72
    lrwxrwxrwx   1 root root     7  5 oct.   2023 bin -> usr/bin
    drwxr-xr-x   3 root root  4096  5 oct.   2023 boot
    -rwxr-xr-x   1 root root    70 14 oct.  13:41 date.sh
    -rwxr-xr-x   1 root root    86 14 oct.  16:29 date-toto.sh
    ...
      ```
  - ls -l | tee liste | wc -l : Pareil concernant les 2 premières parties. La troisième partie compte le nombre de ligne concernant ce que la commande recoit venant de "tee".
    ```bash
    root@serveur-correction:/# ls -l | tee liste | wc -l 
    30
    ```
## 5 Journal syst`eme rsyslog
### 5.1
- Oui, le service rsyslog est lancé sur mon système.
  ```bash
  root@serveur-correction:/# service rsyslog status
  ● rsyslog.service - System Logging Service
     Loaded: loaded (/lib/systemd/system/rsyslog.service; enabled; preset: enab>
     Active: active (running) since Mon 2024-10-14 17:03:01 CEST; 1min 25s ago
  TriggeredBy: ● syslog.socket
       Docs: man:rsyslogd(8)
             man:rsyslog.conf(5)
             https://www.rsyslog.com/doc/
   Main PID: 920 (rsyslogd)
      Tasks: 4 (limit: 11878)
     Memory: 3.1M
        CPU: 17ms
     CGroup: /system.slice/rsyslog.service
             └─920 /usr/sbin/rsyslogd -n -iNONE
  ```

- Le PID du démon est 920.
  ```bash
  root@serveur-correction:/# pidof rsyslogd
  920
  ```
  
### 5.2
- rsyslog ecrit les messages issus des services standards dans :
  ```bash
  # Log commonly used facilities to their own log file
  #
  auth,authpriv.*                 /var/log/auth.log
  cron.*                          -/var/log/cron.log
  kern.*                          -/var/log/kern.log
  mail.*                          -/var/log/mail.log
  ```

### 5.3
Le service cron sert à planifier des tâches automatiques.

### 5.4
- La commande tail -f permet d'afficher les dernieres lignes d'un fichier en temps réel.
- 1. Redémarrer le service cron : systemctl restart cron
  2. Les dernieres lignes du fichier se mettent à jour et donne des informations concernant le status du service cron
     ```bash
     2024-10-15T13:47:31.403481+02:00 serveur-correction systemd[1]: Stopping cron.service - Regular background    program processing daemon...
      2024-10-15T13:47:31.404395+02:00 serveur-correction systemd[1]: cron.service: Deactivated successfully.
      2024-10-15T13:47:31.404984+02:00 serveur-correction systemd[1]: Stopped cron.service - Regular background program processing daemon.
      2024-10-15T13:47:31.433076+02:00 serveur-correction systemd[1]: Started cron.service - Regular background program processing daemon.
      2024-10-15T13:47:31.436792+02:00 serveur-correction cron[753]: (CRON) INFO (pidfile fd = 3)
      2024-10-15T13:47:31.437677+02:00 serveur-correction cron[753]: (CRON) INFO (Skipping @reboot jobs -- not system startup)
  ```
  
### 5.5
Ce fichier concerne les paramètres de configuration principale du service logrotate.

### 5.6
- 
- Le modèle de processus linux detecté sur la machine est 12th Gen Intel(R) Core(TM) i7-12700 :
```bash
root@serveur-correction:/var/log# dmesg | grep -i "CPU"
[    0.195720] smpboot: CPU0: 12th Gen Intel(R) Core(TM) i7-12700 (family: 0x6, model: 0x97, stepping: 0x2)
```
- Le modèle de carte réseau est Intel(R) PRO/1000 Network Connection :
  ```bash
  root@serveur-correction:/var/log# dmesg | grep -i "eth"
  [    2.574826] e1000 0000:00:03.0 eth0: (PCI:33MHz:32-bit) 08:00:27:22:1d:fa
  [    2.574832] e1000 0000:00:03.0 eth0: Intel(R) PRO/1000 Network Connection
  [    2.655814] e1000 0000:00:03.0 enp0s3: renamed from eth0
  ```


    

