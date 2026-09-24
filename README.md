*This project has been created as part of the 42 curriculum by [adamez-f](https://github.com/Nop-o).*

# NetPractice

## Table of Contents

- [NetPractice](#netpractice)
	- [Table of Contents](#table-of-contents)
	- [Description](#description)
	- [Instructions](#instructions)
		- [Running the training interface](#running-the-training-interface)
		- [Exporting configurations](#exporting-configurations)
		- [Submission requirements](#submission-requirements)
	- [Resources](#resources)
		- [References](#references)
		- [Networking concepts studied](#networking-concepts-studied)
		- [Use of AI](#use-of-ai)
	- [Networking Concepts](#networking-concepts)
		- [OSI Model](#osi-model)
		- [TCP Protocol](#tcp-protocol)
		- [IPv4 vs IPv6](#ipv4-vs-ipv6)
		- [Subnet Masks and CIDR](#subnet-masks-and-cidr)
		- [Default Gateway, Routers and Switches](#default-gateway-routers-and-switches)
		- [Example: loading a web page](#example-loading-a-web-page)

---

## Description

NetPractice is a system administration and networking project from the 42 curriculum. Its goal is to introduce the fundamentals of TCP/IP addressing and network configuration through hands-on practice.

The project provides a dedicated web-based training interface in which we have to solve **10 progressively harder levels**. Each level presents a network topology (computers, routers, switches, the Internet) with misconfigured or missing parameters: IP addresses, subnet masks, routing tables and default gateways. The goal is to fix the configuration so that every machine can communicate as expected.

To succeed, we have to understand how addressing works in a network that includes devices such as routers and switches, and how TCP/IP addressing, subnetting and routing fit together.

## Instructions

### Running the training interface

To launch the dedicated browser-based interface, run:

```bash
# enter the project directory
cd net_practice

# start the training interface
./run.sh
```

The interface then opens in the dedicated browser. From there, you can start either:

- the **regular mode**, which makes you solve the levels one by one;
- the **exam mode**, which selects 3 hard levels to solve.

### Exporting configurations

Each level configuration can be exported from the interface:

1. Solve the level (all the required connections must work).
2. Use the interface's **export** feature to save the configuration of the level.
3. Repeat for each of the 10 levels.

### Submission requirements

- **10 exported configuration files** (one per level) must be placed **at the root of the Git repository**.
- A `README.md` must also be present at the root of the repository.

## Resources

### References

- [IP address (Wikipedia)](https://fr.wikipedia.org/wiki/Adresse_IP)
- [TCP and routing (Schoolmouv)](https://www.schoolmouv.fr/cours/routage-tcp-/fiche-de-cours)
- [Subnet mask cheat sheet](https://www.aelius.com/njh/subnet_sheet.html)
- [OSI model (GeeksforGeeks)](https://www.geeksforgeeks.org/computer-networks/open-systems-interconnection-model-osi/)
- [IPv4 vs IPv6 (Juniper)](https://www.juniper.net/fr/fr/research-topics/what-is-ipv4-vs-ipv6.html)

### Networking concepts studied

- **TCP/IP addressing**: structure of IPv4/IPv6 addresses, network and host parts.
- **Subnet masks and CIDR notation**: splitting networks, computing network address, broadcast address and number of usable hosts.
- **Default gateways**: the router address a machine uses to reach other networks.
- **Routers and switches**: routing between networks (layer 3) versus local frame switching (layer 2), and routing tables.
- **OSI layers**: the 7-layer reference model and where IP, TCP and Ethernet fit in.
- **TCP protocol**: reliable, connection-oriented transport and the 3-way handshake.
- **IPv4 vs IPv6**: address size, notation and address space.
- **Public vs private addresses** and NAT.

### Use of AI

[Claude](https://claude.ai/new) was used for:

- Generating custom lessons to check my understanding of the subject.
- Proofreading, correcting and translating this README.

## Networking Concepts

### OSI Model

The OSI (Open Systems Interconnection) model is a theoretical reference model created by the ISO to describe how data travels through a network. It splits communication into 7 layers:

| # | Layer            | Role                                                         | Examples / data unit                            |
|---|------------------|--------------------------------------------------------------|-------------------------------------------------|
| 7 | **Application**  | Interface with user software                                 | HTTP, FTP, SMTP, DNS — data                     |
| 6 | **Presentation** | Data formatting, encryption, compression                     | SSL/TLS, JPEG, ASCII — data                     |
| 5 | **Session**      | Opening, managing and closing communication sessions         | NetBIOS, RPC — data                             |
| 4 | **Transport**    | Reliable or fast data transport, split into segments         | **TCP**, UDP — segments                         |
| 3 | **Network**      | Logical addressing and routing between networks              | **IP (IPv4/IPv6)**, ICMP — packets              |
| 2 | **Data Link**    | Physical (MAC) addressing, error detection on the local link | Ethernet, Wi-Fi, switch — frames                |
| 1 | **Physical**     | Transmission of raw bits over the physical medium            | Cables, electrical/optical signals, hubs — bits |

TCP (Transmission Control Protocol) sits at layer 4 (Transport) and IP at layer 3 (Network), which gives the "TCP/IP stack".

### TCP Protocol

TCP is a Transport layer (OSI layer 4) protocol. It ensures reliable, ordered and verified data transmission between two machines.

**Main characteristics**

- **Connection-oriented**: a connection must be established before any data is exchanged.
- **Reliable**: every segment sent is acknowledged (ACK); lost segments are retransmitted.
- **Ordered**: segments are numbered and reassembled in the correct order on arrival.
- **Flow and congestion control**: TCP adapts its sending rate to the capacity of the network and of the receiver.

**Connection establishment: the 3-way handshake**

1. **SYN** — the client sends a SYN (synchronize) segment to request a connection.
2. **SYN-ACK** — the server replies with a SYN-ACK to accept and synchronize in turn.
3. **ACK** — the client confirms with an ACK. The connection is established.

### IPv4 vs IPv6

IP (Internet Protocol) is the Network layer (OSI layer 3) protocol. It handles the addressing of machines and the routing of packets between different networks.

|                   | IPv4                     | IPv6                                 |
|-------------------|--------------------------|--------------------------------------|
| Address size      | 32 bits                  | 128 bits                             |
| Separator         | Dots (`.`)               | Colons (`:`)                         |
| Notation          | Decimal (`192.168.1.1`)  | Hexadecimal (`2001:db8::1`)          |
| Number of addresses | ~4.3 billion           | ~340 undecillion                     |
| NAT               | Necessary (address shortage) | Generally unnecessary (enough space) |

### Subnet Masks and CIDR

A subnet mask splits an IP address into a **network part** and a **host part**. CIDR (Classless Inter-Domain Routing) notation (`/n`) gives the number of bits used by the network part.

| CIDR | Subnet mask       | Total addresses | Subnets |
|------|-------------------|-----------------|---------|
| /24  | 255.255.255.0     | 256             | 1       |
| /25  | 255.255.255.128   | 128             | 2       |
| /26  | 255.255.255.192   | 64              | 4       |
| /27  | 255.255.255.224   | 32              | 8       |
| /28  | 255.255.255.240   | 16              | 16      |
| /29  | 255.255.255.248   | 8               | 32      |
| /30  | 255.255.255.252   | 4               | 64      |

For a given network:

- The **network address** is the first address (all host bits at 0).
- The **broadcast address** is the last address (all host bits at 1).
- **Usable hosts** = 2^(32 − n) − 2 (the network and broadcast addresses cannot be assigned to a machine).

Two machines can talk directly only if they are in the **same subnet**, that is, if they share the same network address once the mask is applied.

### Default Gateway, Routers and Switches

- **Switch** (layer 2): connects machines of the **same network** and forwards frames using MAC addresses. It has no IP address of its own in these exercises.
- **Router** (layer 3): connects **different networks** and forwards packets between them using its routing table. It has one IP address per interface, each in the network it is connected to.
- **Default gateway**: the address of the router a machine sends its packets to when the destination is outside its own subnet. It must be in the **same subnet** as the machine.
- **Routing table**: a list of rules (destination network → next hop) that tells a router or a machine where to send a packet. The `0.0.0.0/0` entry is the default route, used when no other rule matches.
- **Public vs private addresses**: private ranges (`10.0.0.0/8`, `172.16.0.0/12`, `192.168.0.0/16`) are not routable on the Internet, and must be translated by NAT to reach it.

### Example: loading a web page

Steps of a web navigation:

1. **Application (layer 7)**: the browser generates an HTTP request.
2. **Transport (layer 4, TCP)**: the request is split into segments, after a handshake to establish the connection.
3. **Network (layer 3, IP)**: each segment is encapsulated in an IP packet (v4 or v6) with the destination address.
4. **Data Link + Physical (layers 2 and 1)**: the packet is turned into frames, then into electrical/optical signals sent over the cable or through Wi-Fi.