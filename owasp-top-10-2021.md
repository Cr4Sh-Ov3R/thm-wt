<p align="center"><img src="https://imgur.com/sP6d0iZ" alt="Owasp Top Ten 2021 - Try Hack Me Walktrough - by Cr4Sh"></p>

<h1 align="center">OWASP top 10 2021 - By Cr4Sh</h1>

****

***Contexte*** :

  > L'OWASP (Open Web Application Security) est une communauté ouverte. Son objectif est de permettre de développer, acheter et maintenir des application et APIS qui sont fiables. [OWASP](https://owasp.org/Top10/A00-about-owasp/)
  >
  > L'OWASP top 10 est l'un des projets les plus connus de l'OWASP. Elle recense les 10 vulnérabilités les plus critiques.

****

*** Top 10 Vulnérabilités - 2021 ***

1. Broken Access Control
2. Cryptographic Failures
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
> ![TryHackMe IDOR](#)
> > Flag : flag{fivefourthree}
>
> </details>

[REDACTION EN COURS]
