<h1 align="center">Microsoft Windows Hardening - Walktrough By Cr4Sh</h1>

> Pour rejoindre [TryHackMe](https://tryhackme.com/signup?referrer=63efffa0f0738c004b13a210 "Apprenez la cybersécurité avec Try Hack Me")


***Contexte*** :

  > Cette salle vise à enseigner les concepts de base nécessaire au renforcement d'un poste de travail sous Windows (Hardening) ainsi que les services / logiciels / applications pouvant entraîner un piratage ou une violation de données.

---

> ***Clause de non-responsabilité:*** *Le créateur de ce walktrough n'est pas responsable de l'utilisation abusive des outils présentés dans le cadre de la présentation de ce CTF. Il est destiné uniquement à des fins éthiques et éducatives. Il est rappelé aux utilisateurs qu'ils doivent se conformer à toutes les lois et réglementations applicables dans leur juridiction. Tous les outils présentés et utilisés dans le cadre de ce walktrough ainsi que les commandes réalisées pour mener à bien ce CTF sont conçus pour être utilisés dans un environnement contrôlé et doivent être utilisés conformément à toutes les lois et réglementations applicables. L'utilisation abusive ou illégale de cet outil est strictement interdite et n'est pas soutenue par le créateur.*

> ***Disclaimer:*** *The creator of this walkthrough is not responsible for any misuse of the tools presented in this CTF presentation. It is intended for ethical and educational purposes only. Users are reminded that they must comply with all applicable laws and regulations in their jurisdiction. All tools presented and used in this walktrough, as well as the commands performed to complete this CTF, are designed for use in a controlled environment and must be used in compliance with all applicable laws and regulations. Misuse or illegal use of this tool is strictly prohibited and is not supported by the creator.*

---

<a name="room"></a>

> ***Room : [Microsoft Windows Hardening](https://tryhackme.com/room/microsoftwindowshardening)***

> ***Prérequis :*** *je vous recommande vivement de connaître les bases de Windows ou de les apprendre sur les modules [Windows Fundamentals 1, 2 et 3](https://tryhackme.com/module/windows-fundamentals)*

---

***Microsoft Windows Hardening***

1. [Introduction](#intro)
2. [Understanding General Concepts](#concepts)
3. [Identity & Access Management](#identity-access)
4. [Network Management](#network)
5. [Application Management](#application)
6. [Storage Management](#storage)
7. [Updating Windows](#updating)
8. [Cheatsheet for Hardening Windows](#cheatsheet)

  > L'objectif, dans ce walktrough est de vous guider et vous montrer une manière de faire. Vous aurez souvent plusieurs possibilités pour arriver au même résultat. 
  >
  > ***Notes:*** *Les titres des tâches sont volontairement écrits en anglais afin de pouvoir être au plus proche des dénominations et ne pas risquer une déformation lors de la traduction.*
  > De même afin de ne pas spoiler celles et ceux d'entre vous qui souhaiteraient essayer de trouver les réponses par eux même (ce que je vous encourage fortement) les réponses potentiellement évoquées dans le texte seront nommées sous la mention ``[redacted]``

---

<a name="intro"></a>

**[TASK 1] - Introduction**

***Q1 : Pouvez-vous vous connecter à la machine : Aucune réponse***

---

<a name="concepts"></a>

**[TASK 2] - Understanding General Concepts**


Les services Windows crééent et gèrent des fonction essentielles, telles que la connectivité réseau / le stockage / la mémoire / le son etc. 

Ces services sont gérés par le *Service Control Manager* et divisés en 3 catégories, local, réseau et system. 

Dans cette première question onnous demande de trouver le type de démarrage du service *App Readiness*, nous allons donc devoir aller vérifier la liste des services en lançant ``services.msc`` dans l'app ``Run``.

<p align="center">![TryHackMe Microsoft Windows Hardening Run services.msc](/assets/img/microsoft-windows-hardening/microsoft-windows-hardening-run-sevices-smc.png)</p>

Une fois le *Service Control Manager* ouvert, il ne nous reste plus qu'à rechercher la ligne correspondant au service *App Readiness*.

<p align="center">![TryHackMe Microsoft Windows Hardening startup-type App Readiness](/assets/img/microsoft-windows-hardening/microsoft-windows-hardening-startup-type-app-readiness.png)</p>

> Plus d'infos sur la salle [TryHackMe Microsoft Windows Hardening](#room)

***Q1 : Quel est le type de démarrage du service APP Readiness dans le panneau de service ?***
> <details>
>   <summary>Voir la réponse</summary>
>
> >  manual
</details>

On nous demande ensuite de trouver la valeur par défaut de la clef *tryhackme* dans l'éditeur de registre.

> Rappel : L'éditeur de registre est une base de donnée unifiée qui stocke les paramètres de configuration, les clefs essentielles et le préférences partagées pour Windows.
> En général un programme malveillant y apporte des modifications indésirables et tente de profiter des activités de routine du système. 
>
> Plus d'infos sur la salle [TryHackMe Microsoft Windows Hardening](#room)

Dans l'app ``Run`` nous allons donc appeler ``regedit`` pour accéder à l'éditeur de registre.

<p align="center">![TryHackMe Microsoft Windows Hardening Run regedit](/assets/img/microsoft-windows-hardening/microsoft-windows-hardening-run-regedit.png)</p>

Une fois l'éditeur de registre ouvert, nous pouvons constater un grand nombre de dossiers existants

<p align="center">![TryHackMe Microsoft Windows Hardening registry editor](/assets/img/microsoft-windows-hardening/microsoft-windows-hardening-registry-editor.png)</p>

> Note : (Je vous invite à regarder un peu l'architeture l'éditeur de registre ainsi que son contenu pour mieux l'appréhender).

Cependant nous savons ici que nous recherchons la valeur par défaut de la clef *tryhackme*, je décide donc de faire une recherche rapide en cliquant sur ``Edit > Find`` ou le raccourci clavier ``Ctrl+F``, de rentrer ma recherche et de cliquer sur ``Find Next``

<p align="center">![TryHackMe Microsoft Windows Hardening find key tryhackme](/assets/img/microsoft-windows-hardening/microsoft-windows-hardening-find-key-tryhackme.png)</p>

L'éditeur de registre m'affiche donc à l'écran la clef *tryhackme* ainsi que le flag recherché.

<p align="center">![TryHackMe Microsoft Windows Hardening registry editor tryhackme flag](/assets/img/microsoft-windows-hardening/microsoft-windows-hardening-registy-editor-tryhackme-flag.png)</p>


***Q2 : Ouvrez l'éditeur de registre et trouvez la clef tryhackme. Quelle est la valeur par défaut de cette clef ?***
> <details>
>   <summary>Voir la réponse</summary>
>
> >  {THM_REG_FLAG}
</details>

Nous allons parcourir les logs contenus dans le dossier *Diagnosis* afin de trouver le flag demandé. 

Nous allons donc utiliser Telemetry. 

Il s'agit d'un système de collectes de données pour améliorer l'expérience utilisateur en utilisant à l'avance les problèmes de sécurité et de fonctionnement des logiciels.

Comme indiqué dans le contenu de la salle, le dossier Diagnosis se trouve au path ``%ProgramData%\Microsoft\Diagnosis``

Ayant le chemin dans l'énoncé, je décide de me rendre dans le path `C:\ProgramData\Microsoft\Diagnosis`` à l'aide d'un terminal administrateur. (L'accès est refusé depuis un terminal non admin).

<p align="center">![TryHackMe Microsoft Windows Hardening Diagnosis denied](/assets/img/microsoft-windows-hardening/microsoft-windows-hardening-diagnosis-denied.png)</p>

Ensuite énumérons les fichiers présents dans ce dossier à l'aide de ``dir``.

<p align="center">![TryHackMe Microsoft Windows Hardening diagnosis enum files](/assets/img/microsoft-windows-hardening/microsoft-windows-hardening-diagnosis-enum-files.png)</p>

Il nous suffit maintenant de le lire avec la commande ``more flag.txt.txt``

> Plus d'infos sur la salle [TryHackMe Microsoft Windows Hardening](#room)

***Q3 : Ouvrez le dossier "Diagnosis" et parcourez les différents fichiers de logs. Arriverez-vous à trouver le flag ?***
> <details>
>   <summary>Voir la réponse</summary>
>
> > {THM_1000710}
</details>

Event Viewer est une application qui affiche les détails de journaux de tous les évènements survenus sur notre ordinateur, aussi bien les mises à jour de pilote, défaillances matérielles, changement d'OS, tentative d'authentification non valide ...

> Plus d'infos sur la salle [TryHackMe Microsoft Windows Hardening](#room)

***Q4 : Ouvrez l'observateur d'évènements Event Viewer et jouez avec les différents filtres, tels que Information, Erreur, Avertissement ... Quelle type d'erreur a le plus grand nombre de logs ? : Aucune réponse***

---

<a name="identity-access"></a>

**[TASK 3] - Identity & Access Management**

Il existe 2 type de compte dans Windows, le *Standard* et l'*Administrateur*.

> Notes : Pour une bonne pratique, nous devons seulement utiliser le compte 
> - *Administrateur* : pour des tâches importantes telles que l'installation d'un logiciel, accéder à l'éditeur de registre, au panneau de configuration (control panel) etc. 
> - *Standard* : pour les fonctions de routines telles qu'accéder à des applications habituelles telles que Microsoft Office, le navigateur web ...

Pour cette tâche on nous demande de trouver le nom du compte administrateur de la VM. 

Nous devons donc nous rendre dans ``Control Panel > User Accounts > User Account``

<p align="center">![TryHackMe Microsoft Windows Hardening user account](/assets/img/microsoft-windows-hardening/microsoft-windows-hardening-user-account.png)</p>

> Plus d'infos sur la salle [TryHackMe Microsoft Windows Hardening](#room)

***Q1 : Trouvez le nom du compte administrateur rattaché à la VM ? :***
> <details>
>   <summary>Voir la réponse</summary>
>
> > Harden
</details>

Pour trouver le niveau de notification par défaut, nous devons nous rendre dans des *User Account Control settings* dans ``Control Panel > System and Security``

<p align="center">![TryHackMe Microsoft Windows Hardening System and Security](/assets/img/microsoft-windows-hardening/microsoft-windows-hardening-system-and-security.png)</p>

Puis dans la partie *Security and Maintenance* cliquer sur ``> Change User Account Control settings``

<p align="center">![TryHackMe Microsoft Windows Hardening User Account Security settings](/assets/img/microsoft-windows-hardening/microsoft-windows-hardening-user-account-security-settings.png)</p>

> Plus d'infos sur la salle [TryHackMe Microsoft Windows Hardening](#room)

***Q2 : Rendez-vous dans le panneau de configuration du contrôle de compte ``Control Panel > All Control Panel Items > User Accounts``. Quel est le niveau de notification par défaut ?***
> <details>
>   <summary>Voir la réponse</summary>
>
> > Always notify
</details>

Enfin, nous devons vérifier le nombre de comptes standards créés dans la VM.

Nous revenons donc dans la partie *User Accounts* et nous rendons sur ``Manage another account``.

<p align="center">![TryHackMe Microsoft Windows Hardening Manage another account](/assets/img/microsoft-windows-hardening/microsoft-windows-hardening-manage-another-account.png)</p>

Il ne nous reste plus qu'à compter le nombre d'utilisateurs ayant le niveau "Standard".

> Plus d'infos sur la salle [TryHackMe Microsoft Windows Hardening](#room)

***Q3 : Combien de comptes standard sont créés dans la VM ?***
> <details>
>   <summary>Voir la réponse</summary>
>
> > 0
</details>
---

<a name="network"></a>

**[TASK 4] - Network Management**

Windows contient un parefeu dont vous avez déjà probablement entendu parlé. Il s'agit de *Windows Defender Firewall*.

Il s'agit d'une application intégrée et qui protège les ordinateurs contre les attaques malveillantes et bloque le trafic non autorisé au moyen de règles ou de filtres pour les flux entrants et sortants (à conditions qu'il soit enclenché et bien configuré).

Je ne vais pas m'étendre plus ici sur ce qu'est *Windows Defender Firewall*, la salle le fait déjà très bien et mon objectif n'est certainement pas de m'y substituer, mais vu que nous allons l'utiliser pour répondre à la question, je voulais au moins vous en parler brièvement.

> Plus d'infos sur la salle [TryHackMe Microsoft Windows Hardening](#room)

Ouvrons donc le *Windows Defender Firewall* et rendons nous dans monitoring. 

> Notes : Pour y accéder, nous avons 2 choix. 
> - Pour réaliser cette tâche et tomber directement sur le Monitoring dans le panel gauche, je vous conseille de rentrer la commande ``WF.msc`` dans la boite de dialogue *Run*.
> 
> - Si vous êtes allé trop vite et êtes rentré directement dans *Windows Defender Firewall* depuis le menu, vous pouvez accédé au monitoring en cliquant sur *Advanced settings*

<p align="center">![TryHackMe Microsoft Windows Hardening Windows Defender Firewall](/assets/img/microsoft-windows-hardening/microsoft-windows-hardening-windows-defender-firewall.png)</p>

Une fois dans la fenêtre avec le panel gauche, vous pouvez y voir Monitoring. Je vous invite à cliquer dessus pour voir le monitoring apparaître. 

<p align="center">![TryHackMe Microsoft Windows Hardening Windows Defender Firewall - Monitoring](/assets/img/microsoft-windows-hardening/microsoft-windows-hardening-windows-defender-firewall-monitoring.png)</p>

***Q1 : Ouvrez Windows Defender Firewall et cliquez sur le Monitoring dans le panneau gauche. Lequel des profils suivant est actif? [ Domain, Public, Private ]***
> <details>
>   <summary>Voir la réponse</summary>
>
> > Private
</details>

Pour trouver l'adresse IP du site *tryhack.me* dans la VM, nous devons nous rendre dans le fichier *host* situé à l'adresse ``C:\Windows\system32\Drivers\etc\hosts``

Il nous suffit donc de lire le fichier *hosts* et de trouver la ligne liée au DNS (Domain Name System) *tryhack.me*.

<p align="center">![TryHackMe Microsoft Windows Hardening hots file - tryhack.me IP](/assets/img/microsoft-windows-hardening/microsoft-windows-hardening-hosts-file-tryhack-me-IP.png)</p>

> Notez ici que j'y ai accédé depuis un powershell Administrateur, mais en passant par un powershell normal ça passait aussi.

> Plus d'infos sur la salle [TryHackMe Microsoft Windows Hardening](#room)

***Q2 : Trouvez l'adresse IP pour le site web tryhack.me dans la VM selon le fichier host local.***
> <details>
>   <summary>Voir la réponse</summary>
>
> > 192.168.1.140
</details>

Ici, tout est indiqué dans l'énoncé donc il nous suffit d'ouvrir l'invite de commande (Command Prompt) et lancer la commande ``arp -a`` pour avoir notre réponse. 

> ARP = Address Resolution Protocol, résoud les adresses MAC à partir des adresses IP enregistrées dans le cache ARP de notre station de travail. 

<p align="center">![TryHackMe Microsoft Windows Hardening Physical address](/assets/img/microsoft-windows-hardening/microsoft-windows-hardening-physical-address.png)</p>

> Je vous invite fortement à aller sur la salle pour connaître les spécificité de ce que nous venons d'utiliser, comment empêcher l'accès à distance de notre machine ...

> Plus d'infos sur la salle [TryHackMe Microsoft Windows Hardening](#room)

***Q3 : Ouvrez le terminal (invite de commande) et entrez ``arp -a``. Quelle est l'adresse physique de l'adresse IP 255.255.255.255 ?***
> <details>
>   <summary>Voir la réponse</summary>
>
> > ff-ff-ff-ff-ff-ff
</details>

---

<a name="application"></a>

**[TASK 5] - Application  Management**

> ***Bonnes pratiques***
> Microsoft Store - Une source d'applications de confiance.
> 
> La boutique de Microsoft met à disposition une gamme complète d'applications, de jeux ... et permet de télécharger des fichiers non malveillants simplement.
> 
> Contrairement aux applications que vous trouvez sur internet, qui peuvent avoir été comprimises en y intégrant des fichiers malveillants (chevaux de Troie, virus ...), les application du Microsoft Store garantissent que le logiciel téléchargé est sûr et exempt de tout fichier malveillant.
>
> Pour y accéder vous pouvez lancer la commande ``ms-windows-store:`` dans la boite de dialogue *Run*, alors pensez-y la prochaine fois que vous recherchez une application 😉
>
> Plus d'infos sur la salle [TryHackMe Microsoft Windows Hardening](#room)


Windows Defender Antivirus se trouve dans ``Windows Security > Virus & threat protection``.

Nous nous y rendons donc pour accéder aux différentes options 

<p align="center">![TryHackMe Microsoft Windows Hardening Windows Defender Antivirus](/assets/img/microsoft-windows-hardening/microsoft-windows-hardening-windows-defender-antivirus.png)</p>

Puis nous cliquons sur le lien *Manage settings* dans la partie *Virus & threat protection settings*

<p align="center">![TryHackMe Microsoft Windows Hardening Windows Defender Antivirus - virus & protection manage settings](/assets/img/microsoft-windows-hardening/microsoft-windows-hardening-windows-defender-antivirus-virus-protection-manage-settings.png)</p>

Nous pouvons maintenant accéder à la liste des extentions exclues en cliquant sur *Add or remove exclusions* dans la partie *Exclusion*

<p align="center">![TryHackMe Microsoft Windows Hardening Windows Defender Antivirus - virus & protection manage settings exclusions](/assets/img/microsoft-windows-hardening/microsoft-windows-hardening-windows-defender-antivirus-virus-protection-manage-settings-exclusions.png)</p>


> Plus d'infos sur la salle [TryHackMe Microsoft Windows Hardening](#room)

***Q1 : Windows Defender Antivirus est configuré pour exclure une extension précise lors d'un scan. Quelle est cette extension ?***
> <details>
>   <summary>Voir la réponse</summary>
>
> > .ps
</details>

> Plus d'infos sur la salle [TryHackMe Microsoft Windows Hardening](#room)

***Q2 : Un document Word à été reçu depuis une adresse email inconnue. Est-ce une bonne pratique de l'ouvrir immédiatement depuis votre ordinateur ? (yay/nay)***
> <details>
>   <summary>Voir la réponse</summary>
>
> > nay
</details>

> Microsoft Office Hardening
> 
> La suite Microsoft Office est l'une des applications les plus utilisées dans tous les secteurs, y compris la finance, les télécoms, l'éducation ...
> 
> Qui dit "plus utilisée" dit forcément plus d'attrait pour les acteurs malveillants, qui abusent de ses fonctionnalités par le biais de macros, d'applets Flash, d'object linking ... pour effectuer des RCE (Remote Code Execution).
> 
> Du fait qu'il soit utilisé dans différents domaines métiers, le "durcissement" (hardening) de Microsoft Office peut varier d'un utilisateur à l'autre, leurs besoins n'étant pas les même qu'ils travaillent dans l'éducation ou la finance. 
> 
> Dans l'éducation par exemple, la désactivation des macros peut s'avérer être une bonne idée pour se prémunire de cette potentielle vulnérabilité, mais dans la finance, les macros sont monnaies courantes et nécessaire à l'application de certaines tâches. 
> 
> Pour renforcer Microsoft Office et réduire la surface d'attaque, il existe des règles sur la [doc de Microsoft - Attack Surface reduction rules reference](https://learn.microsoft.com/en-us/microsoft-365/security/defender-endpoint/attack-surface-reduction-rules-reference?view=o365-worldwide) que je vous invite à consulter. 

> Plus d'infos sur la salle [TryHackMe Microsoft Windows Hardening](#room)


Pour ce qui est de la VM et de notre tâche, vous pourrez retrouver un fichier ``office.bat`` que vous devez exécuter en faisant un clic droit et en le lançant dans un terminal *Command prompt* administrateur en cliquant sur ``Run as administrator``. Le flag apparaîtra à la fin du processus.

> Note : Pensez renseigner ``Yes`` quand le terminal vous demandera si vous souhaitez écraser a configuration existante.

<p align="center">![TryHackMe Microsoft Windows Hardening Microsoft Office Hardening - flag ](/assets/img/microsoft-windows-hardening/microsoft-windows-hardening-microsoft-office-hardening-flag.png)</p>

> Les questions de cette tâche est terminée mais je vous conseille vivement d'aller voir tous les autres points abordés directement sur la salle. 

> Plus d'infos sur la salle [TryHackMe Microsoft Windows Hardening](#room)

***Q3 : Quel flag s'affiche après avoir exécuté le fichier Office Hardening Batch ?***
> <details>
>   <summary>Voir la réponse</summary>
>
> > {THM_1101110}
</details>

---

<a name="storage"></a>

**[TASK 6] - Storage Management**

Le chiffrement d'un ordinateur est l'une des choses les plus importantes et qui est malheureusement trop souvent délaissé.

Pourquoi est-ce si important? Tout simplement car le chiffrement de vos données garantit que seul vous ou les personnes avec lesquelles vous partages la clef de récupération puissent accéder aux données stockées et chiffrées.

BitLocker est un outil de chiffrement de données utilisé pour l'édition professionnelle de Windows.

Je suis parti du principe qu'une des misconfig existante est la copie de la clef dans un fichier, souvent un endroit facilement accessible et je vais donc rechercher dans l'explorateur de fichier. 

<p align="center">![TryHackMe Microsoft Windows Hardening BitLocker Recovery Key file](/assets/img/microsoft-windows-hardening/microsoft-windows-hardening-bitlocker-recovery-key-file.png)</p>


On y voit une "Bitlocker Recovery key" en fichier text, il nous suffit donc juste de l'ouvrir pour voir ce qu'il y a dedans et répondre aux questions 1 et 2.

<p align="center">![TryHackMe Microsoft Windows Hardening BitLocker Recovery Key](/assets/img/microsoft-windows-hardening/microsoft-windows-hardening-bitlocker-recovery-key.png)</p>


> Plus d'infos sur la salle [TryHackMe Microsoft Windows Hardening](#room)

***Q1 : Un ingénieur en sécurité a mal configuré notre VM et a stocké une clef de récupération BitLocker sur le même ordinateur. Pouvez-vous lire les 6 derniers chiffres de la clef de récuparation ?***
> <details>
>   <summary>Voir la réponse</summary>
>
> > 377564
</details>


> Plus d'infos sur la salle [TryHackMe Microsoft Windows Hardening](#room)

***Q2 : Combien de caractères comporte la clef de récupération BitLocker dans la VM ?***
> <details>
>   <summary>Voir la réponse</summary>
>
> > 48
</details>


> Plus d'infos sur la salle [TryHackMe Microsoft Windows Hardening](#room)

***Q3 : Un fichier de sauvegarde est placé sur le Bureau de la VM. Quelle est l'extension du fichier ?***
> <details>
>   <summary>Voir la réponse</summary>
>
> > .bkf
</details>


---

<a name="updating"></a>

**[TASK 7] - Updating Windows**

Pour trouver le score de vulnérabilité de la CVE-2022-32230, je décide tout simplement d'aller sur le site``cvedetails.com`` (comme mentionné sur la salle) et de rechercher la *CVE-2022-32230* dans la barre de recherche. 

<p align="center">![TryHackMe Microsoft Windows Hardening CVE Details - CVE-2022-32230 ](/assets/img/microsoft-windows-hardening/microsoft-windows-hardening-cve-details-cve-2022-32230.png)</p>

On voit ici qu'il s'agit d'une faille de Windows SMB (Server Message Block).

> Microsoft Windows SMBv3 souffre d'un déréférencement de pointeur nul dans les versions de Windows antérieures au jeu de correctifs d'avril 2022

Il nous suffit plus qu'à rentrer le score de cette CVE dans la réponse.

> Nous aurions également pu utiliser ``https://cve.mitre.org/`` ou ``nvd.nist.gov``

> Plus d'infos sur la salle [TryHackMe Microsoft Windows Hardening](#room)

***Q1 : Quelle est le score CVE de la vulnérabilité CVE-2022-32230 ?***
> <details>
>   <summary>Voir la réponse</summary>
>
> > 7.8
</details>

---

<a name="cheatsheet"></a>

**[TASK 8] - Cheatsheet for Hardening Windows**

Cette section est à lire et vous avez un super contenu à télécharger pour vous aider tout au long de votre apprentissage. 

***Q1 : J'ai lu l'intégralité de la salle : Aucune réponse***

---



---

J'espère que cela vous a plu et vous a aidé à mieux comprendre les différentes manières de procéder et comment fonctionnait l'Hardening sur Windows.

Je vous invite sincèrement à tout lire, à tester, expérimenter et à télécharger les ressources fournies en fin de salle. ...


---

***Remerciements :***

Un grand merci à toute la team de [TryHackMe](https://tryhackme.com/signup?referrer=63efffa0f0738c004b13a210 "Apprenez la cybersécurité avec Try Hack Me") pour le travail effectué sur la plateforme et particulièrement à **1337rce** qui a réalisé cette salle. 

*"Happy Hacking and have a fun"*
***Cr4Sh***