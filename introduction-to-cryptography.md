<h1 align="center">Introduction to Cryptography - Walktrough By Cr4Sh</h1>

> Pour rejoindre [TryHackMe](https://tryhackme.com/signup?referrer=63efffa0f0738c004b13a210 "Apprenez la cybersécurité avec Try Hack Me")


***Contexte*** :

  > Cette salle vise à enseigner les concepts de base de la cryptography. Vous découvrirez les algorithmes de chiffrement tels que AES, l'échange de clef Diffie-Hellman, le hashage, PKI (Public Key Infrastructure) et TLS (Transport Layer Security).

---

> ***Clause de non-responsabilité:*** *Le créateur de ce walktrough n'est pas responsable de l'utilisation abusive des outils présentés dans le cadre de la présentation de ce CTF. Il est destiné uniquement à des fins éthiques et éducatives. Il est rappelé aux utilisateurs qu'ils doivent se conformer à toutes les lois et réglementations applicables dans leur juridiction. Tous les outils présentés et utilisés dans le cadre de ce walktrough ainsi que les commandes réalisées pour mener à bien ce CTF sont conçus pour être utilisés dans un environnement contrôlé et doivent être utilisés conformément à toutes les lois et réglementations applicables. L'utilisation abusive ou illégale de cet outil est strictement interdite et n'est pas soutenue par le créateur.*

> ***Disclaimer:*** *The creator of this walkthrough is not responsible for any misuse of the tools presented in this CTF presentation. It is intended for ethical and educational purposes only. Users are reminded that they must comply with all applicable laws and regulations in their jurisdiction. All tools presented and used in this walktrough, as well as the commands performed to complete this CTF, are designed for use in a controlled environment and must be used in compliance with all applicable laws and regulations. Misuse or illegal use of this tool is strictly prohibited and is not supported by the creator.*

---

<a name="room"></a>

> ***Room : [Introduction to Cryptography](https://tryhackme.com/room/cryptographyintro)***

---

***Introduction to Cryptography***

1. [Introduction](#intro)
2. [Symmetric Encryption](#symmetric)
3. [Asymmetric Encryption](#asymmetric)
4. [Diffie-Hellman Key Exchange](#diffie-hellman)
5. [Hashing](#hashing)
6. [PKI & SSL/TLS](#pki-tls)
7. [Authenticating with Passwords](#auth-pass)
8. [Cryptography and Data - Example](#crypto-data)
9. [Conclusion](#conclusion)

  > L'objectif, dans ce walktrough est de vous guider et vous montrer une manière de faire. Vous aurez souvent plusieurs possibilités pour arriver au même résultat. 
  >
  > ***Notes:*** *Les titres des tâches sont volontairement écrits en anglais afin de pouvoir être au plus proche des dénominations et ne pas risquer une déformation lors de la traduction.*
  > De même afin de ne pas spoiler celles et ceux d'entre vous qui souhaiteraient essayer de trouver les réponses par eux même (ce que je vous encourage fortement) les réponses potentiellement évoquées dans le texte seront nommées sous la mention ``[redacted]``

---

<a name="intro"></a>

**[TASK 1] - Introduction**

Dans cette tâche nous découvrons le chiffrement de César, qui est le code le plus simple et le plus connu en cryptographie. Il consiste à remplacer une lettre en la substituant par une autre qui se trouve plus loin dans l'alphabet.

Dans l'alphabet latin, il n'a que 26 états possibles (le 0 étant aucune clef, le 25 étant le maximum), nous avons donc 25 clefs.

> Exemple : Si nous prenons une clef de 3, A devient D, B devient E ...

Méthode que nous allons utiliser ici, je vous invite donc aller lire la partie sur le "transposition cipher" qui est très bien expliqué sur la salle. 

> Plus d'infos sur la salle [TryHackMe Introduction to Cryptography](#room)

***Q1 : Vous réussissez à deviner qu'il s'agit d'une citation. Qui l'a cité ?***

Dans l'énoncé on nous informe que l'on a reçu le message chiffré suivant et on nous demande de trouver le contenu du message en clair et plus précisément l'auteur de cette citation.

> ``“Xjnvw lc sluxjmw jsqm wjpmcqbg jg wqcxqmnvw; xjzjmmjd lc wjpm sluxjmw jsqm bqccqm zqy.” Zlwvzjxj Zpcvcol``

Nous avions la possibilité de le faire à la main, partant du principe que, selon la fréquence des lettres, ainsi que leurs "taux d'utilisation", il est possible que nous puissions retrouver certaines lettres substituées et déchiffrer le message.

***Textes anglais :***
D'après les statistiques, dans les textes anglais (TryHackMe étant en anglais), les lettres les plus courantes sont le *"e"* (fréquence de 13%), le *"t"* (fréquence de 9.1%) et le *"a"* (fréquence de 8.2%).

On sait également d'après ces mêmes statistiques que les premières lettres les plus courantes sont le *"t"* (fréquence de 16%), le *"a"* (fréquence de 11.7%) et le *"o"* (fréquence de 7.6%).

***Textes français :***
Pour les textes français, l'Université de Toulouse a tiré une table de fréquence qui est la suivante (en 2008 selon la base du corpus de Wikipedia en français).

Nous retrouvons donc pour les 3 premières lettres, le *"e"* (fréquence de 12.1%), le *"a"* (fréquence de 7.11%) et le *"i"* (fréquence de 6.59%).

> Prudence, cette analyse étant basée sur le corpus précité, elle peut sensiblement varier en fonction du corpus choisi, cependant, le *"e"* et le *"a"* semble être majoritairement cités.


Une autre possibilité qui s'offre à nous, et nous l'utiliserons ici, même si je vous invite à prendre du temps pour essayer de réaliser vous même le déchiffrement avec les fréquence d'apparition des lettres.

> c'est sympa et vous aurez un plus grand sentiment de satisfaction, sans compter que pour épater les copains en soirée ça en jette.

Nous allons donc ici utiliser le site web ``www.quipqiup.com`` pour décriffrer le message.

<p align="center"><img src="/assets/img/intro-to-cryptography/intro-to-cryptography-result-quipqiup.png" alt="TryHackMe Introduction to Cryptography Result Quipqiup"/></p>

![TryHackMe Introduction to Cryptography Result Quipqiup](/assets/img/intro-to-cryptography/intro-to-cryptography-result-quipqiup.png)

Il ne nous reste plus qu'à renseigner le nom de l'auteur dans la réponse.

> Attention, l'ordre n'est pas forcément toujours le même. Recherchez la phrase qui vous semble cohérente.

---

<a name="symmetric"></a>

**[TASK 2] - Symmetric Encryption**

Comme expliqué dans la salle, un algorithme de chiffrement symétrique utilise la même clef pour le cryptage et le décryptage. 

De fait, les parties qui communiquent doivent se mettre d'accord sur une clef secrète avant de pouvoir échanger.

> ATTENTION : Comme indiqué sur la salle, l'algorithme DES (qui est déprécié depuis 1998), tout comme le 3DES (déprécié en 2023 et rejeté en 2024)

Il existe un grand nombre d'algorithmes de chiffrement considérés comme sécurisés. Nous allons nous concentrer ici sur celles que nous devront utiliser ici.

- AES (Advanced Encryption Standard) publié par le NIST (National Institute of Standard and Technology) qui utilise des tailles de clef de 128, 192 ou 256 bits 
- ou CAMELLIA créé par Mitsubishi Electric et NTT au Japon et qui utilise également des tailles de clef de 128, 192 ou 256 bits 

> Plus d'infos sur la salle [TryHackMe Introduction to Cryptography](#room)

> Les questions sont liées au fichier téléchargé au début de la tâche OU sur votre AttackBox au path ``/root/Rooms/cryptographyintro/task02``

***Q1 : Déchiffrez le fichier ``quote01`` (chiffré en utilisant AES256) avec la clef ``s!kR3T55`` en utilisant ``gpg``. Quel est le 3e mot du fichier ?***

Nous devons ici déchiffrer le fichier quote01 qui a été chiffré en utilisant GPG (GnuPG) avec un algorithme AES256. 

> Si vous n'avez pas GnuPG sur votre machine linux, ``sudo apt-get install gpg``

Vu qu'on a la clef il nous suffit donc de lancer la commande suivante dans le terminal :

```bash 
gpg --output quote01_clear.txt  --decrypt quote01.txt.gpg 
#quote01_clear.txt étant le nom du fichier qui sera créé avec le fichier déchiffré.
```
Pensez préalablement à vous placer dans le dossier contenant les fichiers à déchiffrer.

> Note :  J'ai choisi d'enregistrer la sortie dans un fichier par le biais de l'option ``--output`` car j'aime bien pouvoir récupérer le fruit de mon travail en cas de besoin pendant que je travaille dessus, cependant, j'aurais très bien pu utiliser un simple ``gpg --decrypt quote01.txt.gpg`` pour afficher le résultat directement dans le terminal.  

<p align="center"><img src="/assets/img/intro-to-cryptography/intro-to-cryptography-decrypt-quote01-gpg-decrypt.png" alt="TryHackMe Introduction to Cryptography Decrypt Quote01 gpg decrypt"/></p>

![TryHackMe Introduction to Cryptography Decrypt Quote01 gpg decrypt](/assets/img/intro-to-cryptography/intro-to-cryptography-decrypt-quote01-gpg-decrypt.png)

Rentrez la clef demandée dans la boite de dialogue.

<p align="center"><img src="/assets/img/intro-to-cryptography/intro-to-cryptography-decrypt-quote01-gpg-decrypt-key.png" alt="TryHackMe Introduction to Cryptography Decrypt Quote01 gpg decrypt key"/></p>

![TryHackMe Introduction to Cryptography Decrypt Quote01 gpg decrypt key](/assets/img/intro-to-cryptography/intro-to-cryptography-decrypt-quote01-gpg-decrypt-key.png)

Comme nous pouvons le voir ci-dessous, notre fichier ``quote01_clear.txt`` a bien été créé. Il ne nous reste plus qu'à lire le fichier en clair pour répondre à la question avec ``cat quote1_clear.txt``.

<p align="center"><img src="/assets/img/intro-to-cryptography/intro-to-cryptography-read-quote01_clear.png" alt="TryHackMe Introduction to Cryptography Read Quote01_clear"/></p>

***Q2 : Déchiffrez le fichier ``quote02`` (chiffré en utilisant AES256-CBC) avec la clef ``s!kR3T55`` en utilisant ``openssl``. Quel est le 3e mot du fichier ?***

Cette fois-ci on nous demande de déchiffrer le fichier ``quote02``, nous allons donc utiliser la commande suivant :

```bash
openssl aes-256-cbc -d -in quote02 -out quote02_clear.txt
```
> - ici on précise l'algorithme de chiffrement : ``aes-256-cbc``
> - -d pour *decryption*
> - -in pour le fichier que l'on souhaite déchiffrer
> - -out pour enregister la sortie dans le fichier spécifié

<p align="center"><img src="/assets/img/intro-to-cryptography/intro-to-cryptography-decrypt-quote02-openssl.png" alt="TryHackMe Introduction to Cryptography Decrypt Quote02 openssl"/></p>

Une fois le fichier déchiffré, il ne nous reste plus qu'à le lire avec ``cat quote02_clear.txt`` et trouver la réponse.

<p align="center"><img src="/assets/img/intro-to-cryptography/intro-to-cryptography-read-quote02-clear.png" alt="TryHackMe Introduction to Cryptography Read Quote02 clear"/></p>

***Q3 : Déchiffrez le fichier ``quote03`` (chiffré en utilisant CAMELLIA256) avec la clef ``s!kR3T55`` en utilisant ``gpg``. Quel est le 3e mot du fichier ?***

Pour cette 3e question, on nous donne un fichier quote03.txt.gpg qui est chiffré avec gpg selon un algorithme CAMELLIA256. 

<p align="center"><img src="/assets/img/intro-to-cryptography/intro-to-cryptography-decrypt-quote03-gpg.png" alt="TryHackMe Introduction to Cryptography Decrypt Quote03 gpg"/></p>

Une fois votre mot de passe rentré et le résultat enregistré, il ne vous reste plus qu'à afficher le message en clair avec ``cat quote03_clear.txt``.

<p align="center"><img src="/assets/img/intro-to-cryptography/intro-to-cryptography-read-quote03-clear.png" alt="TryHackMe Introduction to Cryptography Read Quote03 clear"/></p>

---

<a name="asymmetric"></a>

**[TASK 3] - Asymmetric Encryption**

Le chiffrement symétrique fonctionnant par clef, nécessite d'échanger ces clefs par canal sécurisé afin que seules les personnes autorisées puissent s'en servir.

Le chiffrement asymétrique, permet quant à lui, grâce à son système de paire de clef (une clef publique, une clef privée), d'échanger des messages chiffrés sans avoir besoin d'utiliser de calan sécurisé, ce qui veut dire que nous n'avons plus besoin de nous assurer de la confidentialité du canal, mais seulement de l'intégrité.

> Plus d'infos sur la salle [TryHackMe Introduction to Cryptography](#room)

> Les questions sont liées au fichier téléchargé au début de la tâche 2 OU sur votre AttackBox au path ``/root/Rooms/cryptographyintro/task03``

***Q1 : Bob a reçu le fichier ``ciphertext_message``qui a été envoyé par Alice. Vous pouvez trouver la clef nécessaire dans le même dossier. Quel est le 1er mot du texte en clair original ?***

Nous voyons dans ce dossier que nous avons, 1 message chiffré (ciphertext_message), 2 clef publiques (celle de Bob et celle d'Alice) et une clef privée (celle de bob).

Pour lire le message ``ciphertext_message`` en clair, il nous faut donc le déchiffré avec la clef publique adaptée. 

L'énoncé nous indique que Bob a reçu le message chiffré envoyé par Alice. Par conséquent, au vu du fonctionnement du chiffrement asymétrique, nous pouvons en conclure qu'Alice a utilisé la clef publique de Bob pour encrypter le message.

Je vais donc essayé de le déchiffrer ici avec la clef privée de Bob :

```bash
openssl pkeyutl --decrypt -in ciphertext_message -inkey private-key-bob.pem  --out cleartext_message.txt
```

<p align="center"><img src="/assets/img/intro-to-cryptography/intro-to-cryptography-decrypt-ciphertext-message.png" alt="TryHackMe Introduction to Cryptography Decrypt ciphertext_message"/></p>

Le fichier ``cleartext_message.txt`` a bien été créé, il ne me reste donc plus qu'à le lire avec ``cat cleartext_message.txt`` pour voir apparaître le message déchiffré et récupérer la réponse.

<p align="center"><img src="/assets/img/intro-to-cryptography/intro-to-cryptography-read-cleartext-message.png" alt="TryHackMe Introduction to Cryptography Read cleartext_message.txt"/></p>

***Q2 : Regardez la clef RSA privée de Bob. Quelle est le dernier octer de p ?***

Nous avons vu dans la salle que le chiffrement asymétrique RSA fonctionnait de la sorte : 

- 2 nombre premiers aléatoires ``p`` et ``q`` permettant de calculer ``N``, selon que ``N = p x q``

- 2 entiers ``e`` et ``d`` tels que ``e x d = 1 mod ϕ(N)`` ou ``ϕ(N) = N - q - p + 1`` (cette étape nous permet de créer la clef publique (N,e) et la clef privée (N,d)).

- L'expéditeur peut donc chiffrer une valeur ``x`` par le calcul ``y = xe mod N`` (Modulus)

- Puis le destinateur peut déchiffrer ``y`` en calculant ``x = yd mod N``. 

> Plus d'infos sur la salle [TryHackMe Introduction to Cryptography](#room)

Lorsque nous lisons le contenu de la clef privée avec la commande :

```bash
openssl rsa -in private-key-bob.pem -text -noout
```
Nous pouvons voir ce resultat : 

```bash 
RSA Private-Key: (2048 bit, 2 primes)
modulus:
    00:e8:5e:73:7d:54:55:0a:cc:56:64:87:b3:4b:8e:
    24:df:96:b8:b9:5f:19:d4:71:a5:b9:5a:a8:d9:ab:
    b4:7b:7f:59:08:c5:9c:47:0d:73:92:97:b8:ef:67:
    b7:a6:5a:59:2c:e3:4c:ca:7f:53:1c:9e:34:32:0e:
    c6:7c:60:b2:d6:1f:30:b2:ed:da:14:e9:15:78:80:
    71:92:3c:26:32:a9:2b:3a:15:4e:48:2d:93:04:a5:
    21:c7:da:15:6c:dd:bc:89:0e:cc:54:be:84:d6:40:
    b8:47:59:d1:b2:27:c9:0d:43:55:de:33:dd:01:8f:
    bf:6c:3e:79:31:dd:e4:90:8d:c3:35:72:31:85:15:
    ae:ac:5a:96:c8:34:90:0e:32:4e:86:45:55:78:fb:
    13:ed:a4:fb:f0:64:b4:61:04:f6:7c:e3:56:aa:03:
    a3:43:1e:40:0b:98:1f:73:66:4a:5c:3a:25:69:2c:
    d9:92:f8:69:c1:5b:61:b7:f2:3a:68:28:e9:2b:75:
    08:9c:a4:63:9e:71:2b:63:aa:99:75:cf:78:00:23:
    fc:5a:df:2d:95:14:2f:e6:10:5d:a0:ff:4d:07:c8:
    d3:bb:2d:8f:0d:8a:fc:ab:43:5d:35:53:dc:72:a2:
    74:5b:c0:88:0d:ee:c3:1f:7b:1c:74:1a:5e:e1:c1:
    88:31
publicExponent: 65537 (0x10001)
privateExponent:
    02:3b:3b:4b:58:ce:a2:eb:e8:bd:ce:65:1f:b4:9d:
    bb:5d:41:d3:85:e0:ee:f3:fd:c3:69:e6:1f:db:a6:
    40:09:59:06:dc:89:98:fa:68:17:0a:f3:46:59:43:
    4a:35:a9:3a:e5:1e:8c:fd:ec:03:ba:56:85:f9:de:
    58:be:14:f9:8e:bd:c8:fa:15:13:5e:54:4b:c9:45:
    4d:ec:db:46:61:44:28:ff:f6:0b:26:0f:8e:06:87:
    ec:83:60:f1:4a:af:cf:76:74:ea:86:14:80:7a:33:
    f5:7b:71:fd:63:f9:bf:9c:30:96:e6:fd:ed:a5:e9:
    10:ab:b3:93:91:ad:ea:e0:17:99:e8:7b:3d:64:58:
    b1:74:3e:0e:81:5b:6d:fa:41:7a:23:26:4f:f1:24:
    a8:73:f3:36:24:a2:65:17:7d:5b:52:8e:1f:fc:b7:
    e6:53:bc:89:b0:e5:18:65:71:29:34:cb:f7:65:51:
    39:0c:62:33:24:b8:60:bf:89:8b:c8:f5:0d:7d:e5:
    85:cf:57:cf:c3:d8:44:10:8f:54:6c:04:99:8d:d7:
    fd:e2:74:18:7b:5c:6c:3c:e1:30:0a:8b:8b:55:70:
    88:8a:67:64:63:5c:65:8f:fa:92:cf:94:04:b9:8d:
    53:28:bb:31:d8:31:3c:4c:06:cd:b6:17:e9:51:d8:
    81
prime1:
    00:ff:ea:65:3e:e5:96:96:0b:66:55:f1:f9:d0:37:
    66:e9:35:a5:c3:43:ca:66:75:40:49:46:8d:85:a7:
    ff:f4:73:97:69:11:a1:1e:37:f9:e3:38:cb:c0:5e:
    56:e9:1a:0d:f2:9f:80:56:87:2a:99:bb:88:8e:93:
    35:5a:9a:c6:f7:99:44:90:88:09:33:a6:0d:ea:b4:
    56:98:66:20:9c:34:e7:b9:33:64:4f:08:01:08:62:
    44:68:8f:df:79:0d:84:2b:77:e7:03:8b:3c:7a:e3:
    e0:e0:ee:23:64:22:51:ed:dd:b8:1c:b3:75:c4:3f:
    4a:cf:fc:7c:57:0b:95:75:[redacted]
prime2:
    00:e8:72:11:5c:b5:5c:14:19:85:ce:e7:d2:e9:54:
    7b:58:ae:32:e9:e6:39:a7:65:b4:90:2f:53:b5:9d:
    22:62:84:fe:52:86:f5:01:a2:9c:b0:4f:80:ee:d4:
    07:27:3b:69:02:70:33:da:7d:97:56:b9:3e:f3:a1:
    84:9e:73:6a:47:e5:99:8c:44:86:75:c1:bf:71:89:
    06:b0:ee:dd:16:45:e7:05:fa:02:bd:e6:3e:b7:f2:
    fe:e7:22:0b:ed:ca:23:a0:68:0b:fe:fb:c3:57:19:
    21:58:6e:73:1d:9d:3c:2a:8a:c1:7e:ea:73:67:5a:
    cb:3d:a8:9b:be:50:08:9e:[redacted]
exponent1:
    1e:20:56:c8:df:b8:29:73:b0:19:60:01:fb:8b:fa:
    16:6c:15:56:76:4d:86:60:39:30:27:19:13:e9:e2:
    0c:c1:ea:ca:18:a4:31:ed:7f:02:4b:b6:58:b0:02:
    65:30:87:01:cf:db:08:d4:a2:a4:34:5a:70:06:4e:
    5a:9b:2b:df:0b:f0:f1:5e:c2:4e:8d:36:c8:31:70:
    9c:42:31:86:92:07:d1:5a:86:6d:73:50:c3:ce:e5:
    a4:b5:83:26:39:fc:1c:2d:e2:49:1d:84:02:27:7f:
    5a:9b:4e:19:44:9d:06:76:7a:6d:0e:87:47:91:f7:
    d9:a2:2c:75:06:cd:12:73
exponent2:
    28:a9:f3:e1:9d:14:9b:ab:8f:5e:0f:ee:34:c5:83:
    c2:92:ce:f3:5e:44:4d:c5:9c:1d:f1:39:9a:b6:ff:
    91:ee:a4:33:39:ca:d8:db:62:bf:f1:58:a3:ef:51:
    c5:0a:3e:a7:9f:8b:62:b8:bf:e5:fb:08:49:44:c3:
    57:98:e7:49:e6:9f:c3:0b:25:de:a9:e3:5c:f0:54:
    cc:55:2d:36:3d:4a:5a:20:4f:a4:7b:08:13:d4:1d:
    c5:bf:8e:08:ae:69:27:21:ac:9f:91:d9:ad:7e:06:
    f8:5a:72:27:07:1f:c4:6d:7b:c6:41:2b:a9:34:18:
    04:14:60:12:9e:1b:b3:d7
coefficient:
    6e:69:83:47:fb:63:da:cc:a5:bb:98:e6:ff:a5:18:
    06:d2:7d:17:19:26:d7:bc:7a:72:13:5a:e3:7e:bc:
    e4:6b:ba:5c:ad:fd:b5:df:73:a0:2f:53:c4:70:f0:
    21:5b:86:13:46:96:ab:2e:4c:e1:c9:63:d0:13:73:
    9f:90:d8:20:59:3a:23:86:cf:1a:03:3b:4a:21:da:
    e8:77:28:3e:41:70:df:07:6e:7f:c0:25:6d:84:26:
    18:18:bc:78:07:2c:05:1f:b6:b8:73:38:c6:2b:ce:
    56:e7:e2:ff:12:bd:06:c4:0a:a6:f4:36:d1:cf:93:
    a6:d5:75:d3:22:b7:3b:3a
```

> On nous demande en question 2 le dernier octet de p (et en question 3 le dernier octer de q)

Nous pouvons alors voir : 
- le ``modulus`` dont nous avons parlé plus haut
- le ``public exponent`` et le ``private exponent``
- le prime1 (le premier nombre premier qui correspond à p)
- le prime 2 (le second nombre premier qui correspond à q)
- les ``exponent 1`` et ``exponent 1``
- et enfin le coefficient. 

Concernant les informations nécessaires pour répondre à la question 2, nous allons prendre le dernier octet de prime1 qui correspond à p. 


***Q3 : Regardez la clef RSA privée de Bob. Quelle est le dernier octer de q ?***

Pour répondre à la question 3, nous allons prendre le dernier octet de prime2 qui correspond à q.

---

<a name="diffie-hellman"></a>

**[TASK 4] - Diffie-Hellman Key Exchange**

Lorsqu'Alice et Bob comminiquent sur un canal non sécurisé (comprenez où peuvent se trouver des "yeux indiscrètes" qui souhaiteraient lire le message), ils ont besoin de se mettre d'accord sur une clef secrète.

C'est là que l'échange Diffie-Hellman intervient.

Diffie-Hellman est un algorithme de chiffrement asymétrique qui permet l'échange de secrets sur un canal public.

Sans rentrer trop dans le détail ici car vous avez les informations nécessaires sur la salle, nous aurons besoins de 2 opérations mathématiques :  la puissance et le modulo.

> Plus d'infos sur la salle [TryHackMe Introduction to Cryptography](#room)

> Les questions sont liées au fichier téléchargé au début de la tâche 2 OU sur votre AttackBox au path ``/root/Rooms/cryptographyintro/task04``

***Q1 : Un ensemble de paramètres Diffie-Hellman se trouvent dans le fichier dhparam.pem. Quelle est la taille du nombre premier en bits ?***

Nous savons que nous pouvons utilisé openssl avec l'option dhparam pour générer des paramètres Diffie-Hellman avec la taille spécifiée (2048 ou 4096 bits).

Nous pouvons trouver dans le dossier task04 le fichier dhparams.pem 
Nous pouvons également utiliser openssl pour voir le nombre premier ``p`` et du générateur ``g`` en utilisant la commande :

```bash
openssl dhparam -in dhparams.pem -text -noout
```

Ce qui nous donne le résultat suivant :

```bash 
DH Parameters: ([redacted] bit)
        prime:
            00:c0:10:65:c6:ad:ed:88:04:88:1e:e7:50:1b:30:
            0f:05:2c:2d:d4:ea:60:44:9e:2a:f7:90:02:89:a4:
            7e:05:99:32:38:dc:75:50:0a:c7:f6:6b:f7:b4:9a:
            df:ef:ca:e0:ce:55:5d:31:48:3e:9c:35:5a:ad:03:
            9c:87:d7:1c:48:e4:2e:29:dc:a3:90:81:23:7f:fa:
            30:5c:fb:d8:62:7b:96:35:ef:9a:0f:84:49:c4:48:
            97:b5:63:38:91:01:49:f1:42:15:fd:da:84:a6:90:
            4d:2d:05:10:41:cf:06:53:52:80:eb:1b:11:ad:5d:
            63:ed:fe:b1:f7:a7:60:1c:79:b8:88:54:a3:e4:64:
            4d:d3:04:a7:d5:76:17:00:d4:44:19:d6:12:a9:1f:
            aa:2b:ac:73:d6:52:50:92:17:a9:cd:f6:b0:ee:55:
            57:a4:db:82:6e:4f:00:20:6f:6f:f5:b1:72:97:b0:
            c5:3a:88:47:86:c6:e5:dd:fc:91:2f:82:08:05:0c:
            5c:c2:f8:62:92:67:9e:f1:53:24:c0:76:f1:3d:0c:
            50:31:5b:56:26:0a:3b:05:a3:b7:be:f9:ee:a4:82:
            f8:9d:46:ab:a9:dd:b9:04:25:61:58:aa:2a:bb:7c:
            2c:c8:e1:ef:ac:f9:50:e3:64:2e:30:9c:fd:48:26:
            25:7e:75:c0:56:58:10:8d:d7:61:b4:df:f7:ce:bd:
            9c:ef:6f:8b:47:8c:0e:cf:29:ab:eb:33:56:17:99:
            19:ee:30:5f:d9:9d:80:6e:3c:91:05:e6:cd:55:ca:
            25:f2:e3:d9:c8:68:74:1d:9e:4a:e7:53:25:1f:17:
            27:3f:4e:29:c2:19:83:da:4d:8f:b5:6b:5c:de:67:
            4f:01:10:48:84:99:32:c0:e5:e0:8b:9f:eb:4e:18:
            f7:ff:c6:47:b1:47:b8:b2:7f:3c:9c:bd:93:c2:71:
            b3:b4:37:fc:ad:2e:d9:af:2d:2c:f9:de:7f:42:8b:
            39:21:d7:47:8f:18:c4:de:ad:70:0b:11:79:c4:df:
            ef:0f:3a:9a:af:85:4e:95:05:ca:35:9e:6d:93:9b:
            e4:66:23:78:2b:d9:f4:47:e4:fe:29:1e:aa:cb:95:
            66:a2:f2:2a:c3:5a:fa:c0:a0:7d:53:bd:74:37:1d:
            b1:c7:66:67:b7:7b:5f:32:bc:2f:fa:82:0a:12:15:
            2f:41:10:cd:12:70:cc:ee:29:e7:1c:b7:07:d4:28:
            1f:73:3c:15:c0:a2:1d:2b:db:07:57:f7:10:28:c7:
            ed:e4:3a:69:c4:d9:4f:0f:c2:b4:4a:97:2a:2c:b3:
            75:77:5e:1a:21:94:8c:85:fb:0d:5e:95:0f:c8:72:
            59:6c:[redacted]
        generator: 2 (0x2)

```

> ATTENTION : Bien que l'échange de clef Diffie-Hellman permet à 2 parties de se mettre d'accord sur un secret via un canal non sécurisé, elle il peut être sujet à une attaque de type "Man-in-the-Middle" (MitM). 
> Un attaquant pourrait répondre à Alice en se faisant passer pour Bob et inversement. Une solution à ce problème sera vu en tâche 6.


***Q2 : Quel est la valeur du dernier octet du nombre premier p ?***

Il nous suffit de reprendre la commande précédente et la réponse est le dernier octet, substitué ici par ``[redacted]``

---

<a name="hashing"></a>

**[TASK 5] - Hashing**

La fonction de hashage cryptographique est un algorithme qui prend en entrée des données de taille arbitraire puis renvoie en sortie une valeur de taille fixe, appelé "digest" ou "checksum".

Si vous avez tenté d'installer des packets sur votre machine, vous avez probablement déjà rencontré les sommes de contrôles vous permettant de vous assurer que le fichier téléchargé correspond bien au fichier fourni initiallement par le site ou le mirroir. 

Par exemple, sha256sum calcul le "digest" (le condensé du message) SHA256 (Secure Hash Algorithm 256 bits) écrit sur 32 octets.

> Rappel : Un chiffre hexadécimal représentant 4 bits, la somme se controle SHA256 (sha256sum) de 256 bits peut être représentée par 64 chiffres héxadécimaux.

> Plus d'infos sur la salle [TryHackMe Introduction to Cryptography](#room)

> Les questions sont liées au fichier téléchargé au début de la tâche 2 OU sur votre AttackBox au path ``/root/Rooms/cryptographyintro/task05``

***Q1 : Quel est le "checksum" (somme de contrôle) SHA256 du fichier order.json ?***

Une fois le bref rappel effectué plus haut, nous pouvons contrôler la somme de contrôle du fichier order.json 

<p align="center"><img src="/assets/img/intro-to-cryptography/intro-to-cryptography-sha256sum-orderJson.png" alt="TryHackMe Introduction to Cryptography sha256sum order.json"/></p>


***Q2 : Ouvrez le fichier order.json, changez le montant "amount" de ``1000`` à ``9000``. Quel est maintenant le "checksum" SHA256 ?***

Nous devons maintenant ouvrir notre fichier order.json et modifier la valeur de "amount" à 9000.

> Note je vais utiliser vim dans notre exemple, mais vous pouvez utiliser ce que vous souhaitez pour modifier le texte.

<p align="center"><img src="/assets/img/intro-to-cryptography/intro-to-cryptography-change-orderJson-amount.png" alt="TryHackMe Introduction to Cryptography change order.json amount"/></p>

> Vous pouvez voir ici le résultat avant et après modification

Ensuite il nous suffit de recommencer comme pour le checksum de la première question.

<p align="center"><img src="/assets/img/intro-to-cryptography/intro-to-cryptography-sha256sum-orderJson-amount-modify.png" alt="TryHackMe Introduction to Cryptography sha256sum order.json amount modify"/></p>


***Q3 : Quel est le HMAC (Hash-based message authentication code) du fichier order.txt avec SHA256 et la clef ``3RfDFz82`` ?***

Enfin on nous demande ici un HMAC, je vais donc faire un bref rappel de la salle. 

> HMAC pour Hash-based message authentication code.

Il utilise une clef cryptographique en plus d'une fonction de hashage.

> Plus d'infos sur la salle [TryHackMe Introduction to Cryptography](#room)

Pour calculer le HMAC sur Linux, nous pouvons utiliser des outils comme hmac256 (sha224mac, sha256hmac ...)

Nous savons ici qu'on nous demande le HMAC du fichier order.txt, nous avons la clef et nous devons utiliser l'argorithm SHA256.

Je décide donc d'utiliser la méthode ``hmac256 3RfDFz82 order.txt``

<p align="center"><img src="/assets/img/intro-to-cryptography/intro-to-cryptography-hmac256-orderTxt.png" alt="TryHackMe Introduction to Cryptography hmac256 order.txt"/></p>

---

<a name="pki-tls"></a>

**[TASK 6] - PKI and SSL/TLS**

Comme vu précédemment un échange de clef Diffie-Hellman nous permet de convenir d'une clef secrète entre Alice et Bob à l'abri des regards indiscrets. 

Cependant, nous avons vu que cette technique ne nous mets pas à l'abri d'une attaque de type Man-in-the-Middle (MitM) car Alice n'a aucun moyen de s'arrurer qu'elle est bien en train de communiquer avec Bob et inversement.

> Plus d'infos sur la salle [TryHackMe Introduction to Cryptography](#room)

Pour faire face à cette vulnérabilité, nous avons besoin dun mécanisme nous permettant de nous assurer de parler à la bonne personne, ce qui amène à l'infrastructure à clef publique (PKI - Public Key Infrastructure)

Vous l'utilisez régulièrement, peut-être même sans en avoir conscience. 

Lorsque vous naviguez par exemple sur TryHackMe, vous vérifiez que vous êtes bien sur un lien débutant par HTTPS et certains navigateurs vous présente un cadenas, parfois vert, vous indiquant que la connexion est chiffrée. 

La connexion est chiffrée par HTTPS grâce à un certificat valide.

En cliquant sur le cadenas vous pouvez obtenir plus d'informations sur le certificat en cours, l'organisme de certification, la date de validité ...

Nous n'allons pas créer un certificat ici mais je vous invite à vous référer à la salle.

> Plus d'infos sur la salle [TryHackMe Introduction to Cryptography](#room)


> Les questions sont liées au fichier téléchargé au début de la tâche 2 OU sur votre AttackBox au path ``/root/Rooms/cryptographyintro/task06``

***Q1 : Quelle est la taille de la clef publique en bits ?***

Nous avons dans notre dossier 2 fichiers. Le ``cert.pem`` et la ``key.pem``.

Pour répondre à la question nous allons nous référer au certificat nommé ``cert.pem`` en utilisant openssl avec la commande :

```bash
openssl x509 -in cert.pem -text
```
> - x509 : Certificate Data Management
> - -in pour indiquer le fichier que nous allons utiliser
> - -text : Afficher le certificat au format texte

<p align="center"><img src="/assets/img/intro-to-cryptography/intro-to-cryptography-x509-certPem.png" alt="TryHackMe Introduction to Cryptography x509 cert.pem"/></p>

***Q2 : Jusqu'à quelle année le certificat est-il valide ?***

La fin de validité du certificat se trouve également dans l'affichage de la commande de la question 1.
---

<a name="auth-pass"></a>

**[TASK 7] - Authenticating with Passwords**

Protéger les données en transit c'est bien, mais il est essentiel de penser également à protéger vos données au repos, ou autrement dit vos mots de passes.

A ce jour il n'est pas acceptable de voir des mots de passe stockées en clair sur des bases de données, car toute violation de données révèlerait les mots de passe utilisateur en clair et laisserait le champs libre aux attaquants pour les exploiter.

Une approche un peu plus améliorée serait d'enregistrer le nom d'utilisateur et une version hashée du mot de passe, ce qui exposerait les hash en cas de violation de données mais pas les données en clair et limiterait leur exploitation par l'attaquant, sous condition d'utiliser un hash performant (SHA256 ou plus par exemple)

> A ce jour, un hash MD5 ou sha1 ne sont plus considérés comme sécurisés 

> Même si le hash semble plus sûr, la présence de "rainbow table", contenant des listes de mots de passe ainsi que leurs hash, permettent aux attaquant de découvrir votre mot de passe en partant d'un hash.

Une approche plus sûre serait de sauvegarder les mots de passe avec un salt (grain de sel) avant de le hasher.

> Le salt est une valeur aléatoire nous permettant de renforcer la sécurité des données au repos.

> Plus d'infos sur la salle [TryHackMe Introduction to Cryptography](#room)

***Q1 : Lors de votre audit du système, vous avez découvert que le hash MD5 du mot de passe de l'administrateur était ``3fc0a7acf087f549ac2b266baf94b8b1``. Quelle est le mot de passe original ?***

Nous avons ici plusieurs manières de le faire, cependant, je vais partir du principe que vous n'avez pas vu les concept de John the Ripper et je vais donc utiliser un outil en ligne ``crackstation.net`` 

<p align="center"><img src="/assets/img/intro-to-cryptography/intro-to-cryptography-crackstation.png" alt="TryHackMe Introduction to Cryptography crackstation"/></p>

---

<a name="cryto-data"></a>

**[TASK 8] - Cryptography and Data - Example**

> Plus d'infos sur la salle [TryHackMe Introduction to Cryptography](#room)


***Q1 : Assurez-vous d'avoir bien lu et compris le scénario ci-dessus. L'objectif est de voir comment les algorithmes de chiffrements symétriques et asymétriques sont utilisés avec le hachage dans de nombreuses communications sécurisées : Aucune réponse***

---

<a name="conclusion"></a>

**[TASK 9] - Conclusion**

La cryptographie est un vaste sujet et nous avons vu dans cette salle une introduction centré sur les concepts de bases qui vous aiderons à mieux comprendre les termes couramment utilisés en cryptographie. 

Bien que vous ne vous en rendiez peut-être pas encore compte, ces connaissances sont essentielles pour comprendre les options de configuration des systèmes qui utilisent le chiffrement et le hashage.


***Q1 : Assurez-vous d'avoir pris des notes de tous les concepts et commandes abordées dans cette salle : Aucune réponse***

---

J'espère que cela vous a plu et vous a aidé à mieux comprendre les base de la cryptographie, leurs différences ainsi que leurs complémentarité.

Je vous invite sincèrement à tout lire, à tester, expérimenter et à consulter les ressources (même externes) fournies tout au long de la salle afin d'approfondir vos connaissances ...


---

***Remerciements :***

Un grand merci à toute la team de [TryHackMe](https://tryhackme.com/signup?referrer=63efffa0f0738c004b13a210 "Apprenez la cybersécurité avec Try Hack Me") pour le travail effectué sur la plateforme et particulièrement à **strategos** qui a réalisé cette salle. 

*"Happy Hacking and have a fun"*
***Cr4Sh***