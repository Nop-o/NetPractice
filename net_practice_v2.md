# NetPractice — Glossaire des termes techniques

Ce document reprend en détail les notions de base à connaître pour aborder le projet **NetPractice** : configuration d'adresses IP, connexion d'appareils via un routeur, et rôle de la passerelle dans un réseau.

---

## Adresse IP

Une **adresse IP** (Internet Protocol) est un identifiant numérique unique attribué à chaque appareil connecté à un réseau. Elle joue le même rôle qu'une adresse postale : elle permet de savoir précisément où envoyer des données et d'où elles viennent. Sans adresse IP, un appareil est invisible sur le réseau et ne peut ni envoyer ni recevoir de données.

- **IPv4** : format sur 32 bits, écrit en 4 groupes de nombres décimaux (appelés **octets**) séparés par des points, chaque groupe allant de 0 à 255 (puisque 2⁸ = 256 valeurs possibles par octet, de 0 à 255).
- Une adresse IP se décompose toujours en deux parties : la **partie réseau** (identifie à quel réseau appartient l'appareil) et la **partie hôte** (identifie l'appareil lui-même à l'intérieur de ce réseau). C'est exactement comme une adresse postale : le nom de la rue identifie le quartier, le numéro identifie la maison précise.
- C'est le **masque de sous-réseau** qui détermine où se situe la frontière entre ces deux parties (voir section suivante).

**Exemple :**
Prenons l'adresse `192.168.1.10` avec un masque `255.255.255.0` (soit `/24`).
- Les 3 premiers octets (`192.168.1`) forment la partie réseau.
- Le dernier octet (`10`) forme la partie hôte : c'est l'identifiant de cette machine précise dans le réseau `192.168.1.0`.

---

## Masque de sous-réseau (Subnet Mask)

Le **masque de sous-réseau** est ce qui permet de savoir, à partir d'une adresse IP, quelle portion appartient au réseau et quelle portion appartient à l'hôte. Deux appareils ne peuvent communiquer directement (sans routeur) que s'ils appartiennent au **même réseau**, c'est-à-dire si l'application du masque donne la même adresse réseau pour les deux.

- Il a le même format qu'une adresse IPv4 : 4 octets (ex. `255.255.255.0`).
- En binaire, les bits à `1` désignent la partie réseau (fixe), les bits à `0` désignent la partie hôte (variable, disponible pour les appareils).
- Plus il y a de bits à `1` (masque "long"), plus le réseau est petit mais plus il y a de sous-réseaux possibles. Inversement, un masque "court" donne un réseau plus grand avec plus d'hôtes possibles mais moins de sous-réseaux.

**Exemple :**
Le masque `255.255.255.0` s'écrit en binaire :
```
11111111.11111111.11111111.00000000
```
Les 24 premiers bits (3 octets) sont à `1` → ils définissent le réseau.
Les 8 derniers bits (dernier octet) sont à `0` → ils définissent la partie hôte, donc 2⁸ = 256 adresses possibles dans ce réseau (de `.0` à `.255`), moins 2 réservées (adresse réseau et broadcast, voir plus bas) = **254 hôtes utilisables**.

Autre exemple avec un masque plus court, `255.255.0.0` (/16) :
```
11111111.11111111.00000000.00000000
```
Ici seuls les 2 premiers octets définissent le réseau, et les 2 derniers (16 bits) sont disponibles pour les hôtes → 2¹⁶ = 65536 adresses possibles dans ce seul réseau.

---

## Notation CIDR

La **notation CIDR** (Classless Inter-Domain Routing) est une manière abrégée d'écrire un masque de sous-réseau : au lieu d'écrire les 4 octets du masque, on écrit un `/` suivi directement du **nombre de bits à `1`** dans ce masque. C'est beaucoup plus rapide à lire et à écrire, et c'est la notation la plus utilisée en pratique (y compris dans NetPractice).

| Masque décimal | Notation CIDR | Bits réseau | Bits hôte | Nb d'hôtes utilisables |
|---|---|---|---|---|
| `255.255.255.0` | `/24` | 24 | 8 | 254 |
| `255.255.255.128` | `/25` | 25 | 7 | 126 |
| `255.255.255.192` | `/26` | 26 | 6 | 62 |
| `255.255.0.0` | `/16` | 16 | 16 | 65534 |

**Exemple :**
`192.168.1.10/24` signifie : l'appareil a l'adresse IP `192.168.1.10`, et les 24 premiers bits de cette adresse (soit `192.168.1`) représentent la partie réseau. Cela équivaut exactement à écrire `192.168.1.10` avec un masque `255.255.255.0`.

---

## Réseau (Network) et adresse réseau

Un **réseau** (ou sous-réseau) regroupe un ensemble d'appareils qui partagent la même partie "réseau" dans leur adresse IP, et qui peuvent donc communiquer **directement** entre eux, sans passer par un routeur. Dès que deux appareils ont une adresse réseau différente, ils ont besoin d'un routeur pour communiquer.

L'**adresse réseau** est la toute première adresse d'une plage : on l'obtient en mettant **tous les bits de la partie hôte à `0`**. Cette adresse sert uniquement à désigner le réseau dans son ensemble — elle **ne peut jamais être attribuée à un appareil**.

**Exemple :**
Pour l'IP `192.168.1.10` avec le masque `255.255.255.0` (/24) :
- Partie réseau (24 bits) : `192.168.1`
- Partie hôte (8 bits) mise à `0` : `.0`
- → Adresse réseau = `192.168.1.0`

Cela signifie que tous les appareils ayant une IP entre `192.168.1.1` et `192.168.1.254` appartiennent au même réseau `192.168.1.0/24` et peuvent communiquer directement entre eux.

---

## Adresse de broadcast (diffusion)

L'**adresse de broadcast** est, à l'inverse de l'adresse réseau, la **dernière** adresse d'une plage réseau : on l'obtient en mettant **tous les bits de la partie hôte à `1`**. Elle sert à envoyer un même message à **tous les appareils du réseau en une seule fois** (par exemple pour des annonces réseau comme le DHCP). Comme l'adresse réseau, elle ne peut **jamais** être attribuée individuellement à un appareil.

**Exemple :**
Toujours pour `192.168.1.0/24` :
- Partie réseau (24 bits) : `192.168.1`
- Partie hôte (8 bits) mise à `1` : `.255` (binaire `11111111` = décimal 255)
- → Adresse de broadcast = `192.168.1.255`

Si un appareil envoie un paquet à `192.168.1.255`, ce message est reçu par **tous** les appareils du réseau `192.168.1.0/24`.

---

## Plage d'adresses utilisables (Host Range)

C'est l'ensemble des adresses **réellement attribuables** à des appareils : toutes les adresses **strictement comprises entre l'adresse réseau et l'adresse de broadcast** (ces deux-là étant exclues, puisqu'elles sont réservées).

**Exemple :**
Pour le réseau `192.168.1.0/24` :
- Adresse réseau (réservée) : `192.168.1.0`
- Adresse de broadcast (réservée) : `192.168.1.255`
- → Plage utilisable : de `192.168.1.1` à `192.168.1.254`, soit **254 adresses** disponibles pour des ordinateurs, routeurs, imprimantes, etc.

Autre exemple avec un plus petit sous-réseau `192.168.1.0/30` (seulement 2 bits pour la partie hôte) :
- Adresse réseau : `192.168.1.0`
- Broadcast : `192.168.1.3`
- → Plage utilisable : seulement `192.168.1.1` et `192.168.1.2` (2 adresses). Ce type de sous-réseau très petit est souvent utilisé dans NetPractice pour les liens point-à-point entre deux routeurs, car on n'a besoin que de 2 adresses.

---

## Routeur (Router)

Un **routeur** est un appareil qui relie **plusieurs réseaux différents** entre eux et qui achemine (route) les paquets de données d'un réseau vers un autre, en se basant sur l'adresse IP de destination. C'est l'équivalent d'un aiguillage ferroviaire : il choisit la bonne direction pour que les données arrivent à destination, même si celle-ci se trouve sur un réseau totalement différent.

- Un routeur possède plusieurs **interfaces**, chacune reliée à un réseau distinct, et chaque interface a sa **propre adresse IP appartenant au réseau auquel elle est connectée**.
- Contrairement à un switch (qui reste à l'intérieur d'un seul réseau), le routeur est le seul appareil capable de faire transiter des données **entre deux réseaux différents**.
- Dans NetPractice, l'exercice consiste justement à configurer correctement les interfaces des routeurs pour que tous les réseaux puissent communiquer entre eux.

**Exemple :**
Un routeur relie deux réseaux :
- Interface 1 : `192.168.1.1/24` → connectée au réseau `192.168.1.0/24` (le réseau du bureau A)
- Interface 2 : `192.168.2.1/24` → connectée au réseau `192.168.2.0/24` (le réseau du bureau B)

Un ordinateur du réseau A (`192.168.1.50`) qui veut envoyer un message à un ordinateur du réseau B (`192.168.2.50`) doit obligatoirement passer par ce routeur, car les deux ordinateurs ne sont pas sur le même réseau.

---

## Passerelle (Gateway)

La **passerelle par défaut** (default gateway) est l'adresse IP à laquelle un appareil envoie ses données lorsque la destination **ne se trouve pas sur son propre réseau local**. C'est en pratique l'adresse de l'interface du routeur connectée à ce réseau local. On peut la voir comme la "porte de sortie" d'un réseau vers le reste du monde (ou vers les autres réseaux).

- Sans passerelle correctement configurée, un appareil ne peut communiquer **qu'avec les autres appareils de son propre réseau** — il ne pourra jamais atteindre un réseau différent, même si le chemin physique existe.
- La passerelle doit obligatoirement être une adresse IP **appartenant au même réseau** que l'appareil qui l'utilise (sinon la configuration est invalide — c'est une erreur fréquente à corriger dans NetPractice).

**Exemple :**
Reprenons le routeur précédent avec l'interface `192.168.1.1/24` connectée au réseau A.
Tous les ordinateurs du réseau A (par ex. `192.168.1.50`, `192.168.1.51`, ...) doivent avoir comme **passerelle par défaut** l'adresse `192.168.1.1` (l'interface du routeur sur leur réseau).
Ainsi, quand `192.168.1.50` veut contacter `192.168.2.50` (réseau B), il envoie d'abord le paquet à sa passerelle `192.168.1.1`, qui se charge ensuite de le router vers le réseau B via son interface `192.168.2.1`.

---

## Interface réseau

Une **interface réseau** est un point de connexion (physique ou logique) par lequel un appareil se raccorde à un réseau. On peut l'imaginer comme une "prise" : chaque câble branché correspond à une interface, et chaque interface peut avoir sa propre adresse IP.

- Un **routeur** possède généralement plusieurs interfaces (une par réseau auquel il est connecté), car son rôle est justement de relier plusieurs réseaux.
- Un **ordinateur** ou un **switch** n'a généralement qu'une seule interface active reliée à un seul réseau à la fois.
- Dans NetPractice, chaque lien entre deux appareils correspond à une interface de chaque côté, et il faut leur attribuer des adresses IP cohérentes.

**Exemple :**
Un routeur avec 3 câbles branchés a 3 interfaces, chacune avec sa propre IP :
- eth0 : `192.168.1.1/24` (vers le réseau du bureau A)
- eth1 : `192.168.2.1/24` (vers le réseau du bureau B)
- eth2 : `10.0.0.1/30` (vers un autre routeur, sur un lien point-à-point)

---

## Switch (Commutateur)

Un **switch** est un appareil qui connecte plusieurs appareils **au sein d'un même réseau local**. Il reçoit les données et les redirige uniquement vers le bon destinataire (contrairement à un ancien hub qui les enverrait à tout le monde). Un switch **ne fait jamais de routage** entre réseaux différents : il reste toujours à l'intérieur d'un seul et même réseau, à la différence du routeur.

**Exemple :**
Un switch relie 4 ordinateurs (`192.168.1.10`, `.11`, `.12`, `.13`), tous sur le même réseau `192.168.1.0/24`. Le switch permet à ces 4 ordinateurs de communiquer entre eux directement. Mais si l'un d'eux veut contacter un appareil sur un autre réseau (ex. `192.168.2.20`), le switch ne peut pas l'aider : il faut passer par un routeur.

---

## Table de routage (Routing Table)

La **table de routage** est une liste de règles stockées dans un routeur (ou dans un ordinateur) qui indique, pour chaque réseau de destination possible, par quelle interface ou vers quelle passerelle envoyer les paquets. C'est en quelque sorte le "plan de circulation" que suit le routeur pour prendre ses décisions d'aiguillage.

**Exemple :**
Table de routage simplifiée d'un routeur central relié à 3 réseaux :

| Réseau de destination | Interface de sortie |
|---|---|
| `192.168.1.0/24` | eth0 |
| `192.168.2.0/24` | eth1 |
| `192.168.3.0/24` | eth2 |

Quand un paquet arrive avec pour destination `192.168.2.50`, le routeur regarde sa table, voit que `192.168.2.0/24` est accessible par eth1, et fait donc sortir le paquet par cette interface.

---

## Adresses IP privées et publiques

- **Adresse IP privée** : utilisée uniquement à l'intérieur d'un réseau local (LAN), non routable directement sur Internet — c'est-à-dire qu'aucun routeur public ne saura acheminer un paquet vers ces adresses depuis l'extérieur. Elles sont réutilisables dans des millions de réseaux locaux différents à travers le monde, un peu comme des numéros de poste interne dans une entreprise. Plages réservées (définies par la RFC 1918) :
  - `10.0.0.0` – `10.255.255.255`
  - `172.16.0.0` – `172.31.255.255`
  - `192.168.0.0` – `192.168.255.255`
- **Adresse IP publique** : unique au monde entier, elle permet à un appareil d'être identifié et joignable directement depuis Internet. Elle est attribuée par les fournisseurs d'accès (FAI) ou les organismes régionaux (RIPE, ARIN, etc.).

**Exemple :**
Chez vous, votre box Internet a une IP privée (ex. `192.168.1.1`) qu'elle attribue à tous vos appareils (`192.168.1.10`, `192.168.1.11`...). Mais côté Internet, votre box possède aussi une IP publique unique (ex. `82.45.67.123`) que voit le reste du monde. C'est ce mécanisme (NAT) qui permet à plusieurs appareils privés de partager une seule IP publique.

---

## Sous-réseaux (Subnetting)

Le **sous-adressage** (subnetting) consiste à découper un réseau en plusieurs réseaux plus petits, appelés **sous-réseaux**, en "empruntant" des bits à la partie hôte pour les ajouter à la partie réseau. Cela permet d'organiser un grand réseau en segments plus petits et isolés (un par service, par étage, par lien entre routeurs, etc.), au lieu de gaspiller des centaines d'adresses sur un seul réseau plat.

- Chaque fois qu'on **allonge le masque d'un bit** (ex. `/24` → `/25`), on **divise le réseau en deux** sous-réseaux de taille moitié.
- Chaque sous-réseau garde toujours sa propre **adresse réseau** (première adresse) et sa propre **adresse de broadcast** (dernière adresse), qui restent réservées et ne peuvent pas être attribuées à un hôte.
- Le nombre d'hôtes utilisables se calcule avec la formule : **2^(bits hôte) − 2** (les −2 correspondent à l'adresse réseau et à l'adresse de broadcast).

---

### Table de référence (à partir d'un bloc /24)

| Masque | Bits hôte | Nb de sous-réseaux (depuis /24) | Nb d'hôtes utilisables par sous-réseau |
|---|---|---|---|
| /24 | 8 | 1 | 254 |
| /25 | 7 | 2 | 126 |
| /26 | 6 | 4 | 62 |
| /27 | 5 | 8 | 30 |
| /28 | 4 | 16 | 14 |
| /29 | 3 | 32 | 6 |
| /30 | 2 | 64 | 2 |

Le `/30` est très utilisé pour les **liens point-à-point** entre deux routeurs, car on n'a besoin que de 2 adresses (une par routeur), pas plus.

---

### Exemple pas à pas : découper 192.168.1.0/24

Partons du réseau `192.168.1.0/24` (256 adresses, de `.0` à `.255`) et découpons-le progressivement.

**Étape 1 — Passage en /25 (on emprunte 1 bit) :**
On divise le réseau en 2 blocs de 128 adresses chacun :

| Sous-réseau | Adresse réseau | Plage utilisable | Broadcast |
|---|---|---|---|
| Bloc 1 | `192.168.1.0/25` | `.1` à `.126` | `.127` |
| Bloc 2 | `192.168.1.128/25` | `.129` à `.254` | `.255` |

**Étape 2 — On redécoupe le Bloc 2 en /26 (on emprunte 1 bit de plus) :**
On divise `192.168.1.128/25` en 2 blocs de 64 adresses :

| Sous-réseau | Adresse réseau | Plage utilisable | Broadcast |
|---|---|---|---|
| Bloc 2a | `192.168.1.128/26` | `.129` à `.190` | `.191` |
| Bloc 2b | `192.168.1.192/26` | `.193` à `.254` | `.255` |

**Étape 3 — On redécoupe le Bloc 2a en /30 (lien point-à-point) :**
On divise `192.168.1.128/26` en blocs de 4 adresses (dont 2 utilisables) :

| Sous-réseau | Adresse réseau | Plage utilisable | Broadcast |
|---|---|---|---|
| Bloc 2a-1 | `192.168.1.128/30` | `.129` et `.130` | `.131` |
| Bloc 2a-2 | `192.168.1.132/30` | `.133` et `.134` | `.135` |
| ... | ... | ... | ... |

On voit ici la logique : chaque sous-réseau est **totalement indépendant et non chevauchant** — c'est la règle absolue à respecter.

---

### La règle à ne jamais casser : pas de chevauchement

Deux sous-réseaux ne doivent **jamais** partager une même adresse. Si un sous-réseau `/25` couvre `192.168.1.128` à `192.168.1.255`, alors **aucun** autre sous-réseau ne doit utiliser une adresse dans cette plage — même avec un masque différent.

**Exemple d'erreur fréquente :**
- Sous-réseau A : `108.198.14.128/25` → couvre `.128` à `.255`
- Sous-réseau B : `108.198.14.192/26` → couvre `.192` à `.255`

Ces deux sous-réseaux **se chevauchent** : le `/26` est entièrement contenu à l'intérieur du `/25`. Un appareil dans le `/25` verra donc les adresses du `/26` comme faisant partie de son propre réseau local (puisque son masque est plus large), ce qui casse le routage : le routeur ne saura pas s'il doit traiter ces adresses comme locales ou les faire suivre vers un autre routeur.

**Comment corriger :** il faut que chaque sous-réseau ait une plage bien à lui, sans intersection. Par exemple, utiliser un `/30` dédié pour le lien point-à-point (2 adresses seulement) plutôt qu'un `/25` beaucoup trop large, afin de laisser la place au `/26` sans conflit :
- Lien point-à-point : `108.198.14.0/30` (`.1` et `.2` utilisables)
- Sous-réseau C : `108.198.14.192/26` (`.193` à `.254` utilisables)

Ici, aucune adresse n'est partagée entre les deux blocs : la configuration est valide.

---

### Méthode rapide pour trouver la plage d'un sous-réseau

1. **Trouver le "saut" (bloc de taille)** : `256 − (dernier octet du masque décimal)`.
   *Exemple : masque `255.255.255.192` → 256 − 192 = **64**. Les sous-réseaux commencent donc tous les 64 : `.0`, `.64`, `.128`, `.192`.*
2. **Repérer dans quel bloc tombe l'adresse IP donnée** (le multiple de 64 juste en dessous ou égal).
3. **Adresse réseau** = ce multiple. **Broadcast** = (multiple suivant − 1). **Plage utilisable** = tout ce qu'il y a entre les deux.

**Exemple :** Pour l'IP `108.198.14.251` avec un masque `/26` (`255.255.255.192`, saut de 64) :
- Multiples de 64 : `.0, .64, .128, .192, .256(=.0 octet suivant)`
- `251` tombe entre `.192` et `.255` → adresse réseau = `108.198.14.192`, broadcast = `108.198.14.255`
- Plage utilisable = `108.198.14.193` à `108.198.14.254`

---

### Points clés à retenir

1. Allonger le masque d'un bit **divise le réseau en deux** — c'est la base de tout découpage en sous-réseaux.
2. Chaque sous-réseau a sa **propre** adresse réseau et sa **propre** adresse de broadcast, non attribuables à un hôte.
3. **Deux sous-réseaux ne doivent jamais se chevaucher**, même avec des masques différents — c'est l'erreur la plus fréquente dans les exercices de type NetPractice.
4. Pour un lien entre deux routeurs (point-à-point), un `/30` (2 adresses utilisables) est largement suffisant et évite de gaspiller un bloc plus grand.
5. Avant de valider un schéma, toujours vérifier que la **plage complète** de chaque sous-réseau (adresse réseau → broadcast) ne recoupe **aucune** plage d'un autre sous-réseau du schéma.

## Résumé visuel d'un exemple complet

Pour le réseau `192.168.1.0/24` :

| Élément | Valeur |
|---|---|
| Adresse réseau | `192.168.1.0` |
| Masque | `255.255.255.0` (/24) |
| Première IP utilisable | `192.168.1.1` |
| Dernière IP utilisable | `192.168.1.254` |
| Adresse de broadcast | `192.168.1.255` |
| Passerelle typique | `192.168.1.1` (souvent la première IP utilisable, attribuée au routeur) |

---

## Points clés à retenir pour NetPractice

1. Deux appareils ne peuvent communiquer **directement** que s'ils sont sur le **même réseau** (même adresse réseau une fois le masque appliqué).
   - *Exemple :* `192.168.1.10/24` et `192.168.1.20/24` sont sur le même réseau (`192.168.1.0/24`) → communication directe possible. Mais `192.168.1.10/24` et `192.168.2.10/24` sont sur des réseaux différents → il faut un routeur.
2. Pour communiquer avec un réseau différent, un appareil doit passer par sa **passerelle**, qui doit obligatoirement être une IP du routeur **appartenant à son propre réseau**.
   - *Exemple d'erreur classique dans NetPractice :* configurer la passerelle d'un PC sur `192.168.1.50` avec la valeur `192.168.2.1` (qui appartient à un autre réseau) → configuration invalide, la communication échouera.
3. Chaque interface d'un routeur doit avoir une IP **cohérente avec le réseau** auquel elle est physiquement connectée (même partie réseau que les autres appareils de ce segment).
4. Il ne peut jamais y avoir deux interfaces avec la **même adresse IP**, ni deux réseaux qui se chevauchent (mêmes plages d'adresses) sur le schéma.
5. L'adresse réseau et l'adresse de broadcast d'un sous-réseau ne sont **jamais** attribuables à un appareil — seules les adresses de la plage utilisable le sont.