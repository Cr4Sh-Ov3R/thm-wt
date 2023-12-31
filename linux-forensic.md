<h1 align="center">Linux Forensic - Walktrough By Cr4Sh</h1>

> Pour rejoindre [TryHackMe](https://tryhackme.com/signup?referrer=63efffa0f0738c004b13a210 "Apprenez la cybersécurité avec Try Hack Me")


***Contexte*** :

  > Linux consitue une part importante des serveurs qui hébergent différents service pour les entreprises.
  >
  > En environnement d'entreprise, les 2 points d'entrée les plus courants par un attaquant sont les serveurs publics et les terminaux. Du fait que Linux puisse être utilisé pour ces 2 points d'entrées il peut être utile de savoir comment retrouver des informations forensics sur une machine linux.

---

> ***Clause de non-responsabilité:*** *Le créateur de ce walktrough n'est pas responsable de l'utilisation abusive des outils présentés dans le cadre de la présentation de ce CTF. Il est destiné uniquement à des fins éthiques et éducatives. Il est rappelé aux utilisateurs qu'ils doivent se conformer à toutes les lois et réglementations applicables dans leur juridiction. Tous les outils présentés et utilisés dans le cadre de ce walktrough ainsi que les commandes réalisées pour mener à bien ce CTF sont conçus pour être utilisés dans un environnement contrôlé et doivent être utilisés conformément à toutes les lois et réglementations applicables. L'utilisation abusive ou illégale de cet outil est strictement interdite et n'est pas soutenue par le créateur.*

> ***Disclaimer:*** *The creator of this walkthrough is not responsible for any misuse of the tools presented in this CTF presentation. It is intended for ethical and educational purposes only. Users are reminded that they must comply with all applicable laws and regulations in their jurisdiction. All tools presented and used in this walktrough, as well as the commands performed to complete this CTF, are designed for use in a controlled environment and must be used in compliance with all applicable laws and regulations. Misuse or illegal use of this tool is strictly prohibited and is not supported by the creator.*

---

<a name="room"></a>

> ***Room : [Linux Forensics](https://tryhackme.com/room/linuxforensics)***

> ***Prérequis :*** *je vous recommande vivement de connaître les bases de Linux ou de les apprendre sur les modules [Linux Fundamentals](https://tryhackme.com/module/linux-fundamentals)*

---

***Linux Forensics***

1. [Introduction](#intro)
2. [Linux Forensics](#forensics)
3. [OS and account information](#os-account)
4. [System Configuration](#sys-config)
5. [Persistence mechanisms](#persistence)
6. [Evidence of Execution](#evidence)
7. [Log files](#log-files)
8. [Conclusion](#conclusion)

  > L'objectif, dans ce walktrough est de vous guider et vous montrer une manière de faire. Vous aurez souvent plusieurs possibilités pour arriver au même résultat. 
  >
  > ***Notes:*** *Les titres des tâches sont volontairement écrits en anglais afin de pouvoir être au plus proche des dénominations et ne pas risquer une déformation lors de la traduction.*
  > De même afin de ne pas spoiler celles et ceux d'entre vous qui souhaiteraient essayer de trouver les réponses par eux même (ce que je vous encourage fortement) les réponses potentiellement évoquées dans le texte seront nommées sous la mention ``[redacted]``

---

<a name="intro"></a>

**[TASK 1] - Introduction**

***Q1 : Prendre connaissance des objectifs : Aucune réponse***

---

<a name="forensics"></a>

**[TASK 2] - Linux Forensics**

Linux est présent dans de nombreux systèmes et même s'il n'est pas aussi facile à utiliser que Windows et macOs, il présente de nombreux avantages et est très polyvalent et léger.

On peut le retrouver sur des serveurs, des ordinateurs, des smartphones ... Il peut même se retrouver dans les unités de divertissement de votre voiture. 

Le fait qu'il soit open-source lui permet également d'être personnalisé en fonction des besoins.

> Plus d'infos sur la salle [TryHackMe Linux Forensics](#room)

***Q1 : Arriverez-vous à associer les logos aux noms des distributions linux : Aucune réponse***

---

<a name="os-account"></a>

**[TASK 3] - OS and account information**

Nous devons commencer à faire de la reconnaissance et à identifier le système pour trouver des informations de bases à son sujet.

Contrairement à Windows qui utilise le registre, Linux stocke tout dans un fichier. Il nous faut donc identifier les artefacts forensics, les localiser et savoir comment les lire.

Nous pouvons retrouver les informations de l'``OS`` dans le fichier ``os-release`` situé au chemin ``/etc/os-release``. Pour le lire nous utilisons l'utilitaire ``cat``. (Pour plus d'infos sur ``cat`` il vous suffit de lire la doc en tapant ``man cat`` dans votre terminal)

```bash
user@machine:~$ cat /etc/os-release
NAME="Ubuntu"
VERSION="20.04.1 LTS (Focal Fossa)"
ID=ubuntu
ID_LIKE=debian
PRETTY_NAME="Ubuntu 20.04.1 LTS"
VERSION_ID="20.04"
HOME_URL="https://www.ubuntu.com/"
SUPPORT_URL="https://help.ubuntu.com/"
BUG_REPORT_URL="https://bugs.launchpad.net/ubuntu/"
PRIVACY_POLICY_URL="https://www.ubuntu.com/legal/terms-and-policies/privacy-policy"
VERSION_CODENAME=focal
UBUNTU_CODENAME=focal
```

Dans la question 1, on nous demande de trouver les utilisateurs membres du groupe ``audio``. Allons donc faire un saut dans le fichiers des groupes ``/etc/group``.

Vu que nous ne recherchons que les utilisateurs du groupe ``audio`` pour la question, nous allons utiliser grep pour faire un tri rapide et ne ressortir que les informations utiles pour répondre à cette question. (je vous invite quand même à regarder tout le fichier pour vous familiariser ou approfondir les groupes et utilisateurs de la machine avec ``cat /etc/group``)

```bash
user@machine:~$ grep -i audio /etc/group
audio:x:29:[redacted],[redacted]
```
> Note : J'ai utilisé l'option Matching Control ``-i`` afin de d'ignorer la casse lors de ma recherche. (plus d'infos sur les options avec ``man grep``)

***Q1 : Quels sont les 2 utilisateurs qui sont membres du groupe ``audio`` ?***
> <details>
>   <summary>Voir la réponse</summary>
>
> >  ubuntu, pulse
</details>


Concernant les informations sur les utilisateurs de notre machine, ils se trouvent dans le fichier ``/etc/passwd``, nous pouvons bien évidemment les lire de la même manière ``cat /etc/passwd`` 
mais par soucis de lisibilité il est préférable de modifier un peu la commande en utilisant une formatage avec l'utilitaire ``column`` (je vous invite à aller voir les possibilité avec la commande ``man column``)

```bash
user@machine:~$ cat /etc/passwd | column -t -s :
root      x  0            0            root                   /root             /bin/bash
daemon    x  1            1            daemon                 /usr/             /usr/sbin/nologin
ubuntu    x  1000         1000         Ubuntu                 /home/ubuntu      /bin/bash
gdm       x  129          135          Gnome Display Manager  /var/lib/gdm3     /bin/false
tryhackme x  [redacted]   [redacted]   tryhackme,,,           /home/tryhackme   /bin/bash
```
> HINT : Pensez à récupérer la valeur de l'uid de l'utilisateur ``tryhackme`` qui vous est demandé dans la question 2.

> *Petit rappel sur Linux :* En se concentrant sur le user ``ubuntu``, on peut voir que son mot de passe contient un ``x`` ce qui signifie que son mot de passe est est enregistré dans le fichier ``/etc/shadow``. La valeur de son uid est à ``1000`` tout comme son gid. Ensuite nous retrouvons son username ``Ubuntu``, son dossier ``/home/ubuntu/`` et enfin son shell par défaut ``/bin/bash``

***Q2 : Dans la VM, il y a un utilisateur nommé ``tryhackme``, quel est son uid ?***
> <details>
>   <summary>Voir la réponse</summary>
>
> >  1001
</details>

La question 3 nous demande de retrouver la durée de la session lancée le ``Sat Apr 16 20:10``. 

Les informations relatives aux logs se trouvent généralement dans le dossier ``/var/log``. Il en existe différents types. 

Nous allons nous attarder ici sur le fichier ``wtmp`` qui garde une trace de toutes les connexions / déconnexions au système.

> Note : Le contenu d'un fichier wtmp n'est pas intelligible à l'écran, ce qui veux dire que si vous essayez de le lire avec ``cat`` par exemple, les informations qui s'afficheront ne seront pas claires.

Nous allons donc utiliser ``last`` avec un grep qui recherchera la journée du Samedi 16 Avril (ceci n'est pas obligatoire ici car il n'y a que 7 lignes dans le fichier) mais qui peut le plus peut le moins.

```bash
user@machine:~$ last -f /var/log/wtmp | grep -i "Sat Apr 16"
reboot   system boot  5.4.0-1029-aws   Sat Apr 16 22:51 - 23:10  (00:18)
reboot   system boot  5.4.0-1029-aws   Sat Apr 16 20:10 - 21:43  ([redacted])
wtmp begins Sat Apr 16 20:10:29 2022
```

***Q3 : Une session à été lancé sur cette machine le ``Samedi 16 Avril à 20:10``. Combien de temps a durée cette session ?***
> <details>
>   <summary>Voir la réponse</summary>
>
> >  01:32
</details>

---

<a name="sys-config"></a>

**[TASK 4] - System Configuration**

Maintenant que nous avons identifié l'OS et les informations liées au compte, il est temps d'éxaminer la configuration du système.

Tout d'abord on nous demande le nom d'hôte rattaché à la VM. Cette information se trouve dans le fichier ``/etc/hostname``

```bash
user@machine:~$ cat /etc/hostname
[redacted]
```
***Q1 : Quel est le nom d'hôte rattaché à la VM ?***
> <details>
>   <summary>Voir la réponse</summary>
>
> >  Linux4n6
</details>

On nous demande également le fuseau horaire de la VM. Cette information se trouve dans le fichier ``/etc/timezone``

```bash
user@machine:~$ cat /etc/timezone 
[redacted]
```

***Q2 : Quel est le fuseau horaire de la VM ?***
> <details>
>   <summary>Voir la réponse</summary>
>
> >  Asia/Karachi
</details>

Trouvons maintenant quel programme écoute à l'adresse ``127.0.0.1:5901``.

Pour voir les connexions actives, nous pouvons utiliser ``netstat``. Le soucis est que si nous tapons juste ``netstat`` dans notre teminal nous aurons énormément d'informations et il faudra rechercher ce que l'on cherche. 

Si vous consultez la doc ``man netstat`` vous trouverez des informations intéressante qui vous permettra d'être plus précis dans votre recherche, et de fait de gagner du temps.

Sur cette machine par exemple, ``netstat`` retourne 567 lignes de résultats. D'autre part, un simple ``netstat`` ne nous affichera pas le programme rattaché.

Nous avons donc plusieurs possibilités, nous allons en voir 2 ici.

La première, présentée sur la salle, où nous allons utiliser les options :
 - ``-n`` pour avoir les adresses au format numérique
 - ``-a`` pour afficher tous les sockets qui écoutent et ceux qui n'écoutent pas.
 - ``-t`` pour filtrer le protocole tcp
 - ``-p`` pour afficher le PID et le programme rattaché

```bash
user@machine:~$ netstat -natp
(Not all processes could be identified, non-owned process info
 will not be shown, you would have to be root to see it all.)
Active Internet connections (servers and established)
Proto Recv-Q Send-Q Local Address     Foreign Address     State       PID/Program name    
tcp   0       0     127.0.0.1:5901    0.0.0.0:*           LISTEN      921/[redacted]       
tcp   0       0     0.0.0.0:80        0.0.0.0:*           LISTEN      -                   
tcp   0       0     127.0.0.53:53     0.0.0.0:*           LISTEN      -                   
tcp   0       0     0.0.0.0:22        0.0.0.0:*           LISTEN      -                   
tcp   0       0     127.0.0.1:631     0.0.0.0:*           LISTEN      -                   
tcp   0       2     127.0.0.1:43802   127.0.0.1:5901      ESTABLISHED -                   
tcp   0       0     10.10.45.248:80   10.100.1.202:60852  ESTABLISHED -                   
tcp   0       0     127.0.0.1:5901    127.0.0.1:43802     ESTABLISHED 921/[redacted]       
tcp6  0       0     ::1:5901          :::*                LISTEN      921/[redacted]       
tcp6  0       0     :::22             :::*                LISTEN      -                   
tcp6  0       0     ::1:631           :::*                LISTEN      -    
```

La seconde vu qu'on sait que ça se tourne sur le port 5901.

```bash
user@machine:~$ netstat -pn | grep 5901
(Not all processes could be identified, non-owned process info
 will not be shown, you would have to be root to see it all.)
tcp   0   0   127.0.0.1:43802      127.0.0.1:5901        ESTABLISHED -                   
tcp   0   0   127.0.0.1:5901       127.0.0.1:43802       ESTABLISHED 921/[redacted]       
```

***Q3 : Quel programme écoute à l'adresse 127.0.0.1:5901 ?***
> <details>
>   <summary>Voir la réponse</summary>
>
> >  Xtigervnc
</details>

Maintenant il nous devons trouver le chemin complet du programme. 

Nous savons qu'il est en cours d'execution, grâce à nos précédentes recherches, nous allons donc vérifier les process en cours avec ``ps``.

Afin d'épurer les résultats, nous allons ``grep`` le nom du programme (ou le PID ou le port)

```bash 
user@machine:~$ ps x| grep [redacted]  
    921 ?        S      0:08 /[redacted]/[redacted]/redacted] :1 -desktop [machine]:1 (user) -auth /home/user/.Xauthority -geometry 1900x1200 -depth 24 -rfbwait 30000 -rfbauth /home/user/.vnc/passwd -rfbport 5901 -pn -localhost -SecurityTypes VncAuth
   2101 pts/0    S+     0:00 grep --color=auto [redacted]

```

***Q4 : Quel est le chemin complet de ce programme ?***
> <details>
>   <summary>Voir la réponse</summary>
>
> >  /usr/bin/Xtigervnc
</details>

***Q5 : Lire le contenu de cette tâche, les flags utilisés et les pages du manuel : Aucune réponse***

---

<a name="persistence"></a>

**[TASK 5] - Persistence mechanisms**

Les mécanismes de persistances permettent à un programme de survivre après un redémarrage du système. Ça permet aux attaquants de l'assurer que leur accès aux systèmes soient maintenus, même une fois redémarrés.

Parmis les mécanismes de persistance on retrouve les ``cron jobs``, les ``service startup``

> Plus d'infos sur la salle [TryHackMe Linux Forensics](#room)

Pour répondre à la question de cette tâche, nous allons rapidement aborder le fichier ``.bashrc``.

> Quand un shell bash est lancé, il exécute les commandes renseignées dans le fichier ``.bashrc``. C'est un peu comme une liste de démarrage des actions à effectuer au lancement. Il peut donc être intéressant de s'y pencher lors de la recherche de persistances.

Revenons à la question, il nous est indiqué que nous devons chercher la taille définie pour le fichier history. 

Nous allons donc le consulter pour rechercher notre réponse.

```bash 
user@machine:~$ cat .bashrc
# ~/.bashrc: executed by bash(1) for non-login shells.
# see /usr/share/doc/bash/examples/startup-files (in the package bash-doc)
# for examples

# If not running interactively, don't do anything
case $- in
    *i*) ;;
      *) return;;
esac

# don't put duplicate lines or lines starting with space in the history.
# See bash(1) for more options
HISTCONTROL=ignoreboth

# append to the history file, don't overwrite it
shopt -s histappend

# for setting history length see HISTSIZE and HISTFILESIZE in bash(1)
HISTSIZE=1000
HISTFILESIZE=[redacted]

# check the window size after each command and, if necessary,
# update the values of LINES and COLUMNS.
shopt -s checkwinsize

[...] # resized for this example
```

***Q1 : Dans le fichier ``.bashrc``, quelle est la taille définie pour le fichier history pour l'utilisateur Ubuntu ?***
> <details>
>   <summary>Voir la réponse</summary>
>
> >  2000
</details>

---

<a name="evidence"></a>

**[TASK 6] - Evidence of Execution**

Connaître les programmes et commandes ont été exécutées sur un hôte est l'un des principaux objectif de l'analyse forensique. 

Sur Linux, nous pouvons trouver des éléments de preuves dans les fichiers de log (par exemple ``/var/log/auth.log*``), dans le fichier ``.viminfo`` dans le répertoire de travail de l'utilisateur concerné ou encore dans le fichier ``.bash_history``, là encore dans le répertoire de travail de l'utilisateur concerné.

Pour notre cas, vu que dans la question 1 il s'agit de retrouver une commande effectuée par l'utilisateur *tryhackme* pour installer un paquet, nous allons nous concentrer par le fichier ``.bash_history``.

> HINT : Attention, nous sommes connecté en tant que *Ubuntu*, et on nous demande les commandes effectuées par l'utilisateur *tryhackme*

Je vous invite à consulter la partie ***Sudoers List*** dans la tache 3 sur la salle afin de comprendre pourquoi notre commande fonctionne.

```bash 
user@machine:~$ sudo cat /home/tryhackme/.bash_history
ls -a /home/tryhackme/
cd ../tryhackme/
rm -rf .bash_logout 
history -w
ls -a /home/tryhackme/
cd ../tryhackme/
rm -rf .bash_logout 
history -w
cat .bash_history
[redacted] apt-get [redacted] [redacted]
```

***Q1 : L'utilisateur tryhackme utilise apt-get pour installer un paquet. Quelle commande a-t-il utilisé ?***
> <details>
>   <summary>Voir la réponse</summary>
>
> >  sudo apt-get install apache2
</details>

Ensuite on nous demande dans quel repertoire de travail, la commande pour installer le paquet net-tools à été tapée.

De toute évidence, au vu des résultats de la commande pour la réponse précédente, nous n'avons pas de trace d'installation d'un paquet net-tools.

Essayons donc dans notre repertoire de travail avant d'aller chercher ailleurs.

```bash
user@machine:~$ cat .bash_history
ls -a
ls -al
unlink .bash_history
ls -a
rm -rf bash_logout
history -w
ls -a
ls -al
unlink .bash_history
ls -a
rm -rf bash_logout
history -w
ls -a
cat .bash_history 
sudo adduser tryhackme
cat /etc/passwd
last -f /var/log/btmp 
sudo last -f /var/log/btmp 
sudo last -f /var/log/wtmp 
vncpasswd
netstat -natp
cat /etc/passwd
sudo vim /etc/hostname
cat .bash_history
sudo apt-get install net-tools
netstat -natp
sudo last -f /var/log/wtmp
sudo dpkg-reconfigure tzdata 
sudo reboot
```

Même si nous le déduisons, vérifions rapidement le répertoire dans lequel nous sommes actuellement avec la commande ``pwd``.

```bash
user@machine:~$ pwd
/[redacted]/[redacted]

```

***Q2 : Dans quel repertoire de travail la commande install net-tools a-t-elle été tapée ?***
> <details>
>   <summary>Voir la réponse</summary>
>
> >  /home/ubuntu
</details>
---

<a name="log-files"></a>

**[TASK 7] - Log Files**

L'une des source d'informations les plus importantes sur l'activité d'un hôte Linux se trouve dans les log dans le dossier ``var/log``.

Vous en trouverez de toutes sortes et je vous invite à les consulter car nous allons ici nous concentré sur l'un d'entre eux (enfin plus exactement 2 d'entre eux).

Nous recherchons l'ancien nom d'hôte de notre machine, nous allons donc nous concentrer sur les fichiers ``auth.log*``qui nous permet de surveiller l'accès au système. Il n'est donc pas impensable que le nom d'hôte s'y trouve. 

Ces fichiers sont parfois volumineux, il est donc utile de connaître certains utilitaires pour nous simplifier la tâche. Par exemple ``tail`` ou ``head``.

Nous allons commencer par la commande ``cat /var/log/auth.log``. Toutes les lignes de résultat nous donnes le nom d'hôte que nous avions trouvé dans la tâche 4, je ne vais donc pas m'attarder dessus. 

Il nous reste 2 fichiers ``auth.log.1`` et ``auth.log.2.gz`` (qui semble le plus ancien et qui est au format *.gz* et que nous pourrions consulter avec la commande ``zmore``)

Nous allons d'abord vérifier le fichier intermédiaire. Je choisis ici, vu la taille du fichier de me concentrer sur les logs les plus anciens. Je vais donc aller chercher les premiers logs renseignés dans le fichier avec l'utilitaire ``head``

```bash 
user@machine:/var/log$ cat auth.log.1 | head
Apr 17 00:01:31 [redacted] CRON[732]: pam_unix(cron:session): session opened for user ubuntu by (uid=0)
Apr 17 00:01:31 [redacted] CRON[733]: pam_unix(cron:session): session opened for user ubuntu by (uid=0)
Apr 17 00:01:31 [redacted] sudo:   ubuntu : TTY=unknown ; PWD=/home/ubuntu ; USER=root ; COMMAND=/usr/bin/python3 -m websockify 80 localhost:5901 -D
Apr 17 00:01:31 [redacted] sudo: pam_unix(sudo:session): session opened for user root by (uid=0)
Apr 17 00:01:31 [redacted] sudo:   ubuntu : TTY=unknown ; PWD=/home/ubuntu ; USER=root ; COMMAND=/usr/sbin/runuser -l ubuntu -c vncserver :1 -depth 24 -geometry 1900x1200
Apr 17 00:01:31 [redacted] sudo: pam_unix(sudo:session): session opened for user root by (uid=0)
Apr 17 00:01:31 [redacted] systemd: pam_unix(systemd-user:session): session opened for user ubuntu by (uid=0)
Apr 17 00:01:31 [redacted] runuser: pam_unix(runuser-l:session): session opened for user ubuntu by (uid=0)
Apr 17 00:01:31 [redacted] sshd[845]: Server listening on 0.0.0.0 port 22.
Apr 17 00:01:31 [redacted] sshd[845]: Server listening on :: port 22.
```

Bingo, nous avons bien un nom d'hôte différents plus ancien. 

***Q1 : Bien que nous ayons trouvé le nom d'hôte de notre VM dans la tâche 4, celui-ci était auparavant différent. Quel était le nom d'hôte précédent ?***
> <details>
>   <summary>Voir la réponse</summary>
>
> >  sudo apt-get install apache2
</details>
---

<a name="conclusion"></a>

**[TASK 8] - Conclusion**

Vous pouvez retrouver sur la salle des tâches complémentaires à effectuer et des cheatsheet à télécharger pour l'analyse forensique sur Linux. 


---

J'espère que cela vous a plu et vous a aidé à mieux comprendre les différentes manières de procéder.

Je vous invite à télécharger les taches en fin de salle afin de pratiquer et de vous entraîner à l'analyse de logs, de fichiers, à bien connaître l'architecture des machines Linux ...

---

***Remerciements :***

Un grand merci à toute la team de [TryHackMe](https://tryhackme.com/signup?referrer=63efffa0f0738c004b13a210 "Apprenez la cybersécurité avec Try Hack Me") pour le travail effectué sur la plateforme et particulièrement à **umairalizafar** qui a réalisé cette salle. 

*"Happy Hacking and have a fun"*
***Cr4Sh***