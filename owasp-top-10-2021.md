<p align="center"><img src="https://imgur.com/sP6d0iZ" alt="Owasp Top Ten 2021 - Try Hack Me Walktrough - by Cr4Sh"></p>

<h1 align="center">OWASP top 10 2021 - By Cr4Sh</h1>

****

***Contexte*** :

  > L'OWASP (Open Web Application Security) est une communauté ouverte. Son objectif est de permettre de développer, acheter et maintenir des application et APIS qui sont fiables. [OWASP](https://owasp.org/Top10/A00-about-owasp/)
  >
  > L'OWASP top 10 est l'un des projets les plus connus de l'OWASP. Elle recense les 10 vulnérabilités les plus critiques.

****

***Top 10 Vulnérabilités - 2021***

1. [Broken Access Control](#broken)
2. [Cryptographic Failures](#crypto)
3. Injection
4. Insecure Design
5. Security Misconfiguration
6. Vulnerable and Outdated Components
7. Identification and Authentications Failures
8. Software and Data Integrity Failures
9. Security Logging & Monitoring Failures
10. Server-Side Request Forgery (SSRF)

<details>
  <summary>Plus d'informations</summary>

  > L'objectif, dans ce walktrough est de vous guider et vous montrer une manière de faire. Vous aurez souvent plusieurs possibilités pour arriver au même résultat. 
  > 
  > Nous allons nous concentrer ici sur les tâches en lien avec les vulnérabilités, je passe volontairement les 2 premières taches liées à la présentation de la salle et à l'utilisation de la connexion OpeVPN et de l'AttackBox
  >
  > ***Notes:*** *Les titres des vulnérabilités est volontairement écrits en anglais afin de pouvoir être au plus proche des dénominations de l'OWASP et ne pas risquer une déformation lors de la traduction*

</details>

****

**[TASK 1] - Introduction**

Q1 : Lire le contenu : Aucune réponse

---

**[TASK 2] - Accessing Machines**

Q1 : Se connecter au réseau via votre openVPN ou votre AttackBox : Aucune réponse

---
<a name="broken"></a>
**[TASK 3] - 1. Broken Access Control**

Les sites internet comportent souvent des pages ou contenus qui sont protégés et en accès restreint.

*Par exemple : Seuls les administrateurs peuvent gérer les utilisateurs d'un site internet.*

Si une personne non autorisée accédait à des fonctionnalités ou des données utilisateurs ce serait catastrophique pour les utilisateurs de la plateforme (et la réputation en passant)

Une [vulnérabilité découverte en 2019](https://bugs.xdavidhu.me/google/2021/01/11/stealing-your-private-videos-one-frame-at-a-time/) a permis à un attaquant d'obtenir n'importe quelle image d'une vidéo Youtube marquée comme privée.

Q1 : Lire et comprendre à quoi correspond la vulnérabilité Broken Access Control : Aucune réponse

---

**[TASK 4] - Broken Access Control (IDOR Challenge)**

> IDOR = Insecure Direct Object Reference (Référence directe à l'objet)

Cette vulnérabilité vous permet d'accéder à des ressources qui ne devrait pas être accessible au vues de nos droits utilisateurs. 

Ça peut se produire quand par exemple on utilise une référence d'objet directe (exemple sur cette salle  un paramètre ``id=`` avec la référence de notre compte ``https://bank.thm/account?id=111111`` )

Si un utilisateur tente de modifier le numéro d'identifiant dans l'url et que le site est mal configuré, alors il peut se retrouver avec les informations bancaires de son voisin.

Q1 : Lire et comprendre ce qu'est une IDOR et comment ça fonctionne : Aucune réponse

Q2 : Déployer la machine distante et y accéder avec les identifiants fournis : Aucune réponse

Q3 : Regarder les notes des autres utilisateurs. Quel est le flag ?

> HINT : L'url contient un paramètre ``note_id=1``. En jouant un peu avec ce paramètre, on trouve une note qui réfèle un flag.

> <details>
>  <summary>Voir le flag</summary>
> 
> ![TryHackMe IDOR](/assets/img/owasp10-IDOR-flag.png)
> > Flag : flag{fivefourthree}
>
> </details>

---
<a name="crypto"></a>
**[TASK 5] - 2. Cryptographic Failures**

Il s'agit de toute vulnérabilite qui résulte d'une mauvaise ou d'un manque d'utilisation des algorithmes cryptographiques nécessaires à assurer la confidentialité des utilisateurs au sein d'une application web par exemple.


Pour reprendre une thématique actuelle, lorsque vous utlisez une solution de messagerie "sécurisée", vous tenez à ce que vos messages soient protégés de toute utilisation frauduleuse et vous souhaitez être certains que seuls vos correspondants légitimes ne puissent voir votre message déchiffré. 

Avec une faille cryptographique, vous ne pouvez plus être sûr que vos données sensibles ne soient pas dévoilées. (je vous invite à lire l'exemple qui est plus complet sur le site de [TryHackMe](https://tryhackme.com/signup?referrer=63efffa0f0738c004b13a210 "Apprenez la cybersécurité avec Try Hack Me")

Q1 : Lire l'introduction Cryptographic Failures et déployez la machine : Aucune réponse

---

**[TASK 6] - Cryptographic Failures (Supporting Material 1)**

Lorsque les développeurs conçoivent des application web (pour l'exemple) ils s'attendent généralement à ce que plusieurs utilisateurs interragissent en même temps avec leur plateforme. L'utilisation d'une base de donnée est donc idéale. 

Les bases de données suivent généralement une syntace du langage SQL (ou noSQL que nous n'aborderons pas ici).

Dans un environnement de production il n'est pas rare d'utiliser un SGBD (Système de gestion de bases de données) sur un serveur dédié, mais ce n'est pas toujours le cas. Elles peuvent également être présentes sous forme de fichiers car c'est plus simple à mettre en place et peut suffire quelques fois.

Ici la salle se concentre sur les fichiers "flat-file" ou fichier à plat, lesquels sont parfois stockés à la racine du site, et donc (accessible depuis le site internet).

Si tel était le cas, il suffirait de naviguer dans votre navigateur pour le trouver et le télécharger. 

Le plus courant et le plus simple des bases de données en fichier à plat est SQLite, qui dispose d'un client léger ``sqlite3`` qui est présent par défaut sur de nombreuses distributions Linux.

(Voir sur la salle [Try Hack Me - Owasp Top 10 2021](https://tryhackme.com/room/owasptop102021) pour plus de détails sur son utilisation)

Q1 : Lire et comprendre les bases de SQLite : Aucune réponse

---

**[TASK 7] - Cryptographic Failures (Supporting Material 2)**

Les mots de passes sont généralement (et ça devrait constamment être le cas) stokés de manière sécurisée.

Un mot de passe chiffré (et réversible par définition) ne respecte pas les règles de la CNIL, c'est pourquoi nous utilisons généralement des hash.

Attention cependant à ne pas utiliser des fonctions cryptographiques dépassées (comme md5 ou sha1) car de simples outils disponibles en ligne comme Crackstation présenté ici ou préinstallés sur Kali linux par exemple permettraient de le cracker.

Ici le hash présenté était ``5f4dcc3b5aa765d61d8327deb882cf99`` qui correspond au mot de passe ``password`` une fois passé dans Crackstation.

Pour plus de détails de la procédure sur la salle [Try Hack Me - Owasp Top 10 2021](https://tryhackme.com/room/owasptop102021/){:target="_blank"}

<a href="https://tryhackme.com/room/owasptop102021" target="_blank">
Try Hack Me - Owasp Top 10 2021</a>

Q1 : Lire et comprendre les bases du crak de hash : Aucune réponse

[REDACTION EN COURS]
