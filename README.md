*This project has been created as part of the 42 curriculum by [adamez-f](https://github.com/Nop-o).*


# NetPractice

## Table of Contents
- [NetPractice](#netpractice)
	- [Table of Contents](#table-of-contents)
	- [Description](#description)
	- [Instructions](#instructions)
	- [Resources](#resources)
	- [Modèle OSI](#modèle-osi)
	- [Protocole TCP](#protocole-tcp)
		- [Caractéristiques principales](#caractéristiques-principales)
		- [L'établissement de connexion : le "3-way handshake"](#létablissement-de-connexion--le-3-way-handshake)
	- [IPv4 vs IPv6](#ipv4-vs-ipv6)
		- [Exemple](#exemple)

---

## Description
## Instructions
## Resources

## Modèle OSI

Le modèle OSI (Open Systems Interconnection) est un modèle de référence théorique créé par l'ISO pour décrire comment les données circulent dans un réseau. Il découpe la communication en 7 couches

|   | Couche                 | Rôle                                                            | Exemples / unité de données                       |
|---|------------------------|-----------------------------------------------------------------|---------------------------------------------------|
| 7 | **Application**        | Interface avec les logiciels utilisateur                        | HTTP, FTP, SMTP, DNS — données 			       |
| 6 | **Présentation**       | Formatage, chiffrement, compression des données                 | SSL/TLS, JPEG, ASCII — données 				   |
| 5 | **Session**            | Ouverture, gestion et fermeture des sessions de communication   | NetBIOS, RPC — données 						   |
| 4 | **Transport**          | Transport fiable ou rapide des données, découpage en segments   | **TCP**, UDP — segments 						   |
| 3 | **Réseau**             | Adressage logique et routage entre réseaux                      | **IP (IPv4/IPv6)**, ICMP — paquets                |
| 2 | **Liaison de données** | Adressage physique (MAC), détection d'erreurs sur le lien local | Ethernet, Wi-Fi, switch — trames                  |
| 1 | **Physique** 			 | Transmission des bits sur le support physique                   | Câbles, signaux électriques/optiques, hubs — bits |

Le protocole TCP (Transmission Control Protocol) se situe en couche 4 (Transport), IP se situe en couche 3 (Réseau) —> "pile TCP/IP".

## Protocole TCP

TCP (Transmission Control Protocol) est un protocole de la couche Transport (couche 4 OSI). Il assure une transmission fiable, ordonnée et vérifiée des données entre deux machines.

### Caractéristiques principales
- **Orienté connexion** : une connexion doit être établie avant tout échange de données.
- **Fiable** : chaque segment envoyé est accusé de réception (ACK) ; en cas de perte, il y a retransmission.
- **Ordonné** : les segments sont numérotés et réassemblés dans le bon ordre à l'arrivée.
- **Contrôle de flux et de congestion** : TCP adapte le débit d'envoi selon la capacité du réseau et du récepteur.
  
### L'établissement de connexion : le "3-way handshake"

1. **SYN** — le client envoie un segment SYN (synchronize) pour demander l'ouverture d'une connexion.
2. **SYN-ACK** — le serveur répond avec SYN-ACK pour accepter et synchroniser à son tour.
3. **ACK** — le client confirme avec un ACK. La connexion est établie.

## IPv4 vs IPv6

IP (Internet Protocol) est le protocole de la couche Réseau (couche 3 OSI). Il assure l'adressage des machines et le routage des paquets entre réseaux différents.

| 					| IPv4  				   | IPv6                                 |
|-------------------|--------------------------|--------------------------------------|
| Taille adresse    | 32 bits                  | 128 bits                             |
| Ecriture			| Séparation par des "."   | Séparation par des ":" ou ":"        | 
| Notation          | Décimale (`192.168.1.1`) | Hexadécimale (`2001:db8::1`)         |
| Nombre d'adresses | ~4,3 milliards           | ~340 undécillions             		  |
| NAT               | Nécessaire (pénurie)     | Généralement inutile, assez de place |
|

### Exemple

Etapes de navigation web:
1. **Application (couche 7)** : ton navigateur génère une requête HTTP.
2. **Transport (couche 4, TCP)** : la requête est découpée en segments, avec un handshake préalable pour établir la connexion.
3. **Réseau (couche 3, IP)** : chaque segment est encapsulé dans un paquet IP (v4 ou v6) avec l'adresse de destination.
4. **Liaison + Physique (couches 2 et 1)** : le paquet est transformé en trames puis en signaux électriques/optiques envoyés sur le câble ou en Wi-Fi.