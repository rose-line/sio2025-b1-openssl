# TP Cryptographie - OpenSSL

## Introduction à OpenSSL

**OpenSSL** est une boîte-à-outils cryptographique qui implémente les protocoles réseau _Secure Sockets Layer_ (SSL v2/v3, couche de sockets sécurisés) et _Transport Layer Security_ (TLS v1.3, sécurité pour la couche de transport) ainsi que les standards cryptographiques liés dont ils ont besoin. OpenSSL est actuellement utilisé par plus des deux-tiers des sites-web pour la création de certificats (HTTPS). De nombreuses infrastructures y ont également recours, car les certificats permettent également d'authentifier des applications/services, des équipements...

OpenSSL comporte deux bibliothèques écrites en C (une de cryptographie générale et une implémentant le protocole SSL). Il contient aussi le programme `openssl`, un outil de ligne de commande pour utiliser les différentes fonctions cryptographiques de la librairie crypto d’OpenSSL à partir du shell. Voici un aperçu de ses différents usages :

— chiffrement et déchiffrement symétrique (AES...)

— chiffrement et déchiffrement asymétrique (RSA...)

— création de clefs (RSA, DSA...)

— signatures digitales

— hachage cryptographique (SHA, MD5, RIPEMD160...)

— création de certificats X509

— tests SSL/TLS client et serveur

Les paramètres du programme shell `openssl` sont très nombreux et permettent notamment d’indiquer le type de chiffrement (pour ne citer que ceux à clef secrète symétrique : _Blowfish_, AES, DES ou 3-DES, _Camellia_, RC2, RC4, _Aria_...), d’encodage (Base64...) et de hachage (MD5, SHA...).

Site web officiel : http://www.openssl.org

## Partie 1 - Installation

Sous Linux, il est possible que OpenSSL soit déjà installé sur votre distribution. Sinon, utilisez votre gestionnaire de paquets.

Sous Windows, l'installation de _Git Bash_ inclut OpenSSL. Le [GitHub du projet](https://github.com/openssl/openssl/releases) contient les binaires du programme. Il est aussi possible de télécharger une version précompilée avec installateur depuis le [wiki du site officiel](https://wiki.openssl.org/index.php/Binaries).

1/ Installez OpenSSL sur votre système si besoin.

2/ Vérifiez l'installation : `openssl version -a`

3/ Affichez l'aide : `openssl help`

## Partie 2 - Déchiffrement symétrique

L'aide d'une sous-commande _cmd_ de `openssl` s'obtient en tapant : `openssl cmd -help`. La sous-commande pour le chiffrement/déchiffement symétrique est _enc_.

4/ Affichez l'aide de la sous-commande _enc_.

5/ Consultez les algorithmes de chiffrement disponibles (trouvez l'option pour les afficher).

> *Note* : la page suivante fournit également cette information : https://www.openssl.org/docs/man3.0/man1/openssl-ciphers.html

Le dépôt du TP contient un fichier `un_secret.enc` qui a été chiffré avec AES-256 en mode _cbc_.

> *Note* : AES possède en effet différents modes de chiffrement par blocs comme _Cipher Block Chaining_ (`cbc`), _Electronic Code Block_ (`ecb`), _Cipher Feedback_ (`cfb`), etc.

La clé de chiffrement est quant à elle fournie sous forme de _passphrase_ (mot de passe) dans le fichier `passphrase.base64`. Ce fichier est encodé (et non pas chiffré) en base64 ; il vous faudra donc le décoder pour obtenir la _passphrase_.

> On rappelle que l'encodage n'est ni du chiffrement ni du hachage. Bien que considéré comme un système cryptographique, l'encodage peut être inversé facilement (c'est d'ailleurs son but), car il ne nécessite pas de clé secrète. Il consiste à représenter des données dans un format spécifique, généralement pour des raisons de compatibilité ou d’efficacité, notamment pour la transmission sur les réseaux. Dans ce contexte, l’encodage _base64_ est très courant : il transforme des données binaires en une chaîne de caractères hexadécimaux (caractères `[0-9]` et `[A-F]`).

6/ Décodez le contenu du fichier `passphrase.base64`.

7/ Déchiffrez le contenu du fichier `un_secret.enc`. Vous devrez préciser le mode `pbkdf2` (c'est l'_algorithme de dérivation de clé_ qui a été utilisé pour inclure la _passphrase_).

## Partie 3 : Chiffrement asymétrique

> Cette partie est à faire en binôme. Vous exécuterez chacun toutes les actions demandées même si, en pratique, il suffit d'exécuter une partie des actions sur chaque poste pour effectivement échanger la clé secrète. Ici, on échangera en réalité deux clés secrètes pour effectuer toute la procédure par les deux opérateurs.

Vous souhaitez échanger des données de manière confidentielle avec votre binôme, par l'intermédiaire du chiffrement symétrique AES-256 (rapide, sûr et facile à mettre en oeuvre). Le problème qui se pose est celui de la cryptographie symétrique en général : le partage de la clé secrète de chiffrement entre les deux correspondants dont on considère qu'ils ne sont pas sur le même site.

8/ Générez une clé secrète de chiffrement symétrique de 256 bits dans le fichier `cle_secrete_A.txt` (et `cle_secrete_B.txt` pour l'autre binôme).

Vous allez utiliser le chiffrement asymétrique RSA pour la phase initiale essentielle : l'échange de la clé secrète. Cela va vous permettre de transmettre la clé secrète symétrique de manière sécurisée en utilisant la paire de clés asymétrique de votre binôme.

Lorsque vous utilisez OpenSSL pour créer ces clés, il existe deux commandes distinctes : une pour créer une clé privée, et une autre pour extraire la clé publique correspondante à la clé privée. Ces paires de clés sont encodées en base64, et leurs tailles peuvent être spécifiées pendant ce processus.

9/ Générez une clé privée de longueur 2048 bits dans le fichier `cle_privee_A.pem` (et `cle_privee_B.pem` pour l'autre binôme).

10/ Affichez le contenu du fichier.

11/ Utilisez la sous-commande `pkey` pour examiner plus précisément la structure de la clé.

12/ Le fichier généré contient donc en fait la _paire_ de clés asymétriques. Exécutez la commande `openssl` pour extraire la clé publique associée à cette clé privée dans le fichier `cle_publique_A.pem` (respectivement `_B`).

13/ Affichez le contenu du fichier de clé publique pour vous assurer qu'il ne contient bien _QUE_ la clé publique. Il est vite arrivé d'oublier un paramètre dans la commande précédente et de publier ensuite sa clé privée au monde entier. Et ça, c'est mal.

Vous transmettez votre clé publique à votre binôme par email. Cette clé étant _publique_, cette transmission n'a pas besoin d'être sécurisée.

> En réalité, la publication de clés publiques se fait souvent grâce à ce qu'on appelle des PKI (_Public Key Infrastructure_) : des _infrastructures à clés publiques_ (ICP, encore appelées _infrastructure de gestion de clés_).

14/ Chiffrez votre clé secrète symétrique `cle_secrete_A.txt` (respectivement `_B`) en utilisant RSA avec la clé publique de votre binôme `cle_publique_B` (respectivement `_A`). La clé secrète chiffrée sera générée dans le fichier `cle_secrete_chiffree_A.bin` (respectivement `_B`).

Vous transmettez le fichier `cle_secrete_chiffree_A.bin` (respectivement `_B`) par email à votre binôme.

15/ Déchiffrez la clé secrète symétrique `cle_secrete_chiffree_B` (respectivement `_A`) que vous venez de recevoir. La clé secrète déchiffrée sera générée dans le fichier `cle_secrete_dechiffree_B.txt` (respectivement `_A`).

Vérifiez avec votre binôme que l'échange s'est bien passé. Sur le poste A, vous devez avoir le fichier `cle_secrete_dechiffree_B.txt` qui est identique au fichier `cle_secrete_B.txt` de l'autre poste, et respectivement sur le poste B.

## Partie 4 : Chiffrement symétrique

Vous pouvez maintenant utiliser la clé secrète pour échanger des données en toute confidentialité. À ce stade, la cryptographie asymétrique n'est plus requise : l'expéditeur et le destinataire vont chiffrer/déchiffrer les données avec la clé secrète partagée en utilisant le chiffrement symétrique.

Vous n'avez évidemment besoin que d'une seule clé secrète : choisissez l'une des deux clés échangées précédemment. Assurez-vous de bien utiliser la même clé sur les deux postes.

Sur chaque poste, choisissez également un ensemble de documents à chiffrer (la taille importe peu, le chiffrement symétrique étant très rapide, même un ensemble de grande taille prendra un temps raisonnable à chiffrer). Cela peut être n'importe quoi, le chiffrement n'étant bien entendu pas limité aux simples fichiers textuels.

> Pour regrouper plusieurs documents en un fichier, on peut par exemple utiliser un programme de compression (ex. : _7-zip_ sous Windows) ou un programme comme `tar` sous Linux. Ces programmes permettent en effet de regrouper (sans compression obligatoire) plusieurs fichiers en un seul, que l'on pourra ensuite facilement chiffrer.

16/ Regroupez chacun votre ensemble de documents en un fichier unique à l'aide du programme de votre choix. Chiffrez votre archive en utilisant AES-256 et la clé secrète 256 bits. Le document chiffré sera généré dans le fichier `doc_chiffre_A.enc` (respectivement `_B`).

Envoyez votre fichier chiffré à votre binôme.

17/ Déchiffrez le document que vous venez de recevoir dans le fichier `doc_dechiffre_B` (respectivement `_A`). Veillez à bien utiliser la bonne clé.

Ajoutez l'extension du fichier original pour pouvoir l'ouvrir facilement et vérifier que les documents originaux peuvent bien être correctement restaurés.

## Partie 5 : Hachage cryptographique

Le hachage cryptographique est une fonction qui prend en entrée un message de longueur arbitraire et produit une empreinte numérique de longueur fixe. Les valeurs de hachage sont souvent utilisées pour vérifier l'intégrité, mais aussi parfois pour servir dans le même temps d'identifiant unique.

La page de téléchargement du code source d'OpenSSL (https://www.openssl.org/source/) contient un tableau avec les versions récentes. Chaque version est accompagnée de deux valeurs de hachage : SHA1 (sur 160 bits) et SHA256 (sur 256 bits). Ces valeurs peuvent être utilisées pour vérifier que le fichier téléchargé correspond à l'original : l'utilisateur recalcule le condensat localement, puis le compare à l'original. Les systèmes modernes disposent d'utilitaires pour calculer de telles valeurs de hachage. Linux, par exemple, dispose de `md5sum` et `sha256sum`. OpenSSL lui-même fournit des utilitaires en ligne de commande similaires.

Les hachages sont utilisés dans de nombreux domaines de l'informatique. Par exemple, la Blockchain _Bitcoin_ utilise des valeurs de hachage SHA256 comme identifiants de blocs. Miner un Bitcoin consiste à générer une valeur de hachage SHA256 qui tombe en dessous d'un seuil spécifié, ce qui signifie une valeur de hachage avec au moins N zéros en tête. (La valeur de N peut monter ou descendre en fonction de la productivité du minage à un moment donné.) Pour information, les mineurs d'aujourd'hui sont des clusters matériels conçus pour générer des valeurs de hachage SHA256 en parallèle. Il n'est pas rare que le minage de Bitcoin génère environ 75 millions de téra-hachages par seconde (amusez-vous à compter combien ça fait de chiffres ;).

Les protocoles réseaux utilisent également des valeurs de hachage, souvent sous le nom de « somme de contrôle » (_checksum_), pour garantir l'intégrité des messages, c'est-à-dire pour assurer qu'un message reçu est identique à celui envoyé. L'expéditeur du message calcule la somme de contrôle du message et envoie les résultats avec le message. Le destinataire recalcule la somme de contrôle lorsque le message arrive. Si la somme de contrôle envoyée et celle recalculée ne correspondent pas, alors le message en transit ou la somme de contrôle envoyée (ou même les deux) a été altéré. Dans ce cas, le message et sa somme de contrôle doivent être renvoyés, ou au minimum une erreur doit être déclenchée (notez que ceertains protocoles réseau tels que UDP ne se soucient pas des sommes de contrôle).

Il y a de nombreux autres exemples d'utilisation du hachage. On citera encore le hachage de mots de passe d'authentification en base de données, ou l'utilisation dans la signature numérique comme nous l'avons vue en cours (voir partie 5).

Prenons comme exemple la fonction de hachage SHA256. Pour une chaîne de bits d'entrée de longueur quelconque, cette fonction génère une valeur de hachage de _longueur fixe_ de 256 bits. Par conséquent, cette valeur de hachage ne révèle même pas la longueur de la chaîne de bits initiale, et encore moins bien sûr la valeur de chaque bit dans la chaîne. La seule façon de _rétro-ingénierer_ un hash SHA256 vers la chaîne de bits d'entrée est par une recherche par force brute : on essaie chaque chaîne de bits possible jusqu'à ce qu'une correspondance avec le hash cible soit trouvée. Une telle recherche est _irréalisable_ sur une fonction de hachage cryptographique solide telle que SHA256 (en tout cas dans l'état de l'art actuel).

Générez deux fichiers `fichier_a_hacher1.txt` et `fichier_a_hacher2.txt` contenant respectivement les chaînes de caractères `xyz` et `xYz`.

18/ Utilisez le programme `sha256sum` (fourni sous Linux et _Git Bash_) pour calculer les valeurs de hachage de ces deux fichiers.

Vérifiez que vous obtenez ces valeurs de hachage :

```
f34fe622a8fe7565fc15be3ce8bc43d7e32a0dd744ebef509fa0bdb130c0ac31 *fichier_a_hacher1.txt
59a88c6c1d85d8adb3e0c11d1cc7b4d40740842481f3ec6d35c5f22834d33f29 *fichier_a_hacher2.txt
```

Notez comme les valeurs de hachage sont très différentes, alors que les fichiers sont très similaires. C'est l'une des propriétés des bonnes fonctions de hachage : une petite modification dans l'entrée produit une grande modification dans la sortie.

19/ Même question mais en utilisant `openssl`.

## Partie 6 : Signature numérique

La signature numérique est un procédé cryptographique qui permet à un utilisateur de signer électroniquement un document, prouvant ainsi qu'il est bien l'auteur du document et qu'il l'a bien signé.

La signature numérique est basée sur la cryptographie asymétrique et le hachage. Elle utilise une clé privée pour signer le document, et une clé publique pour vérifier la signature. La clé privée est connue uniquement de l'utilisateur, tandis que la clé publique est partagée avec le monde entier.

La signature numérique utilise également une fonction de hachage : celle-ci prend en entrée le document à signer, et produit une empreinte numérique de longueur fixe (le _hash_). C'est cette empreinte qui est ensuite chiffrée en utilisant la clé privée du signataire. **La signature numérique est donc l'empreinte chiffrée du document original**. Elle est ensuite attachée au document, et le document ainsi signé est partagé avec le ou les destinataires.

La vérification de la signature numérique est effectuée en utilisant la clé publique du signataire pour déchiffrer l'empreinte numérique du document original. Le destinaire calcule de son côté l'empreinte du document reçu et la compare à l'empreinte déchiffrée. La vérification de la signature numérique est réussie si les deux empreintes numériques sont identiques.

La signature numérique permet :

— *d'authentifier l'auteur du document* : l'auteur est _le seul_ à avoir pu chiffrer l'empreinte numérique *avec sa clé privée* ;
— *de garantir l'intégrité du document* : toute modification du document entraînerait une modification de l'empreinte numérique, et donc de la signature numérique *qui ne serait plus valide* ;
— *de garantir la non-répudiation au niveau de l'auteur* : celui-ci ne peut pas nier avoir signé le document, car _lui seul a pu chiffrer l'empreinte numérique_ avec sa propre clé privée.

Pour la suite, vous allez réutiliser la paire de clés asymétrique déjà générées. Choisissez un fichier à signer. On le renomme `document.bin`.

20/ Signez le fichier `document.bin` avec `openssl` et votre clé privée. On utilisera SHA256 pour le hachage et la signature doit être générée dans le fichier `signature.sha256`.

21/ Encodez la signature en base64 pour pouvoir la transférer sans problème.

Transférez le document ainsi que sa signature à votre binôme.

22/ Décodez la signature reçue `signature.sha256.base64`.

23/ Vérifiez la signature du fichier `document.bin` en utilisant la clé publique de votre binôme.

> La sortie de la commande doit être `Verified OK`.

24/ Altérez très légèrement le document reçu (par exemple, ajoutez un espace à la fin) et vérifiez à nouveau la signature. Vérifiez de nouveau la signature par rapport à ce fichier altéré. Indiquez alors la sortie de la commande de vérification.
