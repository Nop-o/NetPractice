# Cours : Protocole TCP, Modèle OSI et IPv4/IPv6

## 1. Le modèle OSI

Le modèle **OSI** (*Open Systems Interconnection*) est un modèle de référence théorique créé par l'ISO pour décrire comment les données circulent dans un réseau. Il découpe la communication en **7 couches**, chacune ayant un rôle précis, de la couche la plus proche du matériel à celle la plus proche de l'utilisateur.

### Les 7 couches (de bas en haut)

| # | Couche | Rôle | Exemples / unité de données |
|---|--------|------|------------------------------|
| 7 | **Application** | Interface avec les logiciels utilisateur | HTTP, FTP, SMTP, DNS — données |
| 6 | **Présentation** | Formatage, chiffrement, compression des données | SSL/TLS, JPEG, ASCII — données |
| 5 | **Session** | Ouverture, gestion et fermeture des sessions de communication | NetBIOS, RPC — données |
| 4 | **Transport** | Transport fiable ou rapide des données, découpage en segments | **TCP**, UDP — segments |
| 3 | **Réseau** | Adressage logique et routage entre réseaux | **IP (IPv4/IPv6)**, ICMP — paquets |
| 2 | **Liaison de données** | Adressage physique (MAC), détection d'erreurs sur le lien local | Ethernet, Wi-Fi, switch — trames |
| 1 | **Physique** | Transmission des bits sur le support physique | Câbles, signaux électriques/optiques, hubs — bits |

**Moyen mnémotechnique (français)** : *"Après Presque Six Tasses, Réal Livre Pizza"* → Application, Présentation, Session, Transport, Réseau, Liaison, Physique.

### Point clé à retenir
- Chaque couche communique avec son équivalent sur la machine distante (communication "horizontale" logique).
- En réalité, les données descendent la pile (**encapsulation**) chez l'émetteur, puis remontent (**décapsulation**) chez le récepteur.
- **TCP** se situe en couche 4 (Transport), **IP** se situe en couche 3 (Réseau) — c'est pour ça qu'on parle souvent de la "pile TCP/IP".

---

## 2. Le protocole TCP

**TCP** (*Transmission Control Protocol*) est un protocole de la couche Transport (couche 4 OSI). Il assure une transmission **fiable, ordonnée et vérifiée** des données entre deux machines.

### Caractéristiques principales
- **Orienté connexion** : une connexion doit être établie avant tout échange de données.
- **Fiable** : chaque segment envoyé est accusé de réception (ACK) ; en cas de perte, il y a retransmission.
- **Ordonné** : les segments sont numérotés et réassemblés dans le bon ordre à l'arrivée.
- **Contrôle de flux et de congestion** : TCP adapte le débit d'envoi selon la capacité du réseau et du récepteur.

### L'établissement de connexion : le "3-way handshake"

1. **SYN** — le client envoie un segment SYN (synchronize) pour demander l'ouverture d'une connexion.
2. **SYN-ACK** — le serveur répond avec SYN-ACK pour accepter et synchroniser à son tour.
3. **ACK** — le client confirme avec un ACK. La connexion est établie.

La fermeture se fait généralement avec un échange de segments **FIN/ACK** (souvent en 4 étapes).

### Structure simplifiée d'un segment TCP
- Port source / port destination
- Numéro de séquence
- Numéro d'accusé de réception (ACK)
- Drapeaux (flags) : SYN, ACK, FIN, RST, PSH...
- Fenêtre de réception (window size)
- Données

### TCP vs UDP (pour situer)

| | TCP | UDP |
|---|---|---|
| Connexion | Oui (handshake) | Non |
| Fiabilité | Oui (ACK, retransmission) | Non |
| Ordre garanti | Oui | Non |
| Vitesse | Plus lent (overhead) | Plus rapide |
| Usage typique | Web (HTTP), email, transfert de fichiers | Streaming, jeux en ligne, DNS |

---

## 3. IPv4 et IPv6

**IP** (*Internet Protocol*) est le protocole de la couche Réseau (couche 3 OSI). Il assure l'**adressage** des machines et le **routage** des paquets entre réseaux différents.

### IPv4

- Adresses codées sur **32 bits**, notées en 4 groupes de nombres décimaux séparés par des points.
  - Exemple : `192.168.1.1`
- Espace d'adressage : environ **4,3 milliards** d'adresses possibles.
- Problème majeur : **pénurie d'adresses** face à la croissance d'Internet (partiellement compensée par le NAT).
- Adresses privées courantes : `10.0.0.0/8`, `172.16.0.0/12`, `192.168.0.0/16`.

### IPv6

- Adresses codées sur **128 bits**, notées en 8 groupes hexadécimaux séparés par `:`.
  - Exemple : `2001:0db8:85a3:0000:0000:8a2e:0370:7334`
  - Peut être raccourcie : `2001:db8:85a3::8a2e:370:7334` (les zéros consécutifs remplacés par `::`, une seule fois dans l'adresse)
- Espace d'adressage : environ **340 undécillions** d'adresses (3,4 × 10³⁸) — pratiquement illimité.
- Conçu pour remplacer IPv4 à terme, avec des améliorations :
  - Pas besoin de NAT (assez d'adresses pour tout le monde)
  - En-tête simplifié → routage plus efficace
  - Sécurité (IPsec) intégrée nativement dans la conception initiale
  - Auto-configuration des adresses (SLAAC)

### IPv4 vs IPv6

| | IPv4 | IPv6 |
|---|---|---|
| Taille adresse | 32 bits | 128 bits |
| Notation | Décimale (`192.168.1.1`) | Hexadécimale (`2001:db8::1`) |
| Nombre d'adresses | ~4,3 milliards | ~340 undécillions |
| NAT | Nécessaire (pénurie) | Généralement inutile |
| Compatibilité | Aucune entre les deux — coexistence via double pile ou tunnels |

---

## Comment tout ça s'articule

Quand tu navigues sur un site web :
1. **Application (couche 7)** : ton navigateur génère une requête HTTP.
2. **Transport (couche 4, TCP)** : la requête est découpée en segments, avec un handshake préalable pour établir la connexion.
3. **Réseau (couche 3, IP)** : chaque segment est encapsulé dans un paquet IP (v4 ou v6) avec l'adresse de destination.
4. **Liaison + Physique (couches 2 et 1)** : le paquet est transformé en trames puis en signaux électriques/optiques envoyés sur le câble ou en Wi-Fi.