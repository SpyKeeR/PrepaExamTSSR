##  CORRECTIONS DÉTAILLÉES - QUESTIONS 1-20

### ✔️ Question 1 - [ ] B) 7 couches

Le modèle OSI (Open Systems Interconnection) comporte **7 couches** :
1. Physique
2. Liaison de données
3. Réseau
4. Transport
5. Session
6. Présentation
7. Application

**Mnémotechnique** : "**P**lease **D**o **N**ot **T**hrow **S**ausage **P**izza **A**way"

---

### ✔️ Question 2 - [ ] B) Couche 2 (Liaison de données)

Les **switchs** opèrent à la **couche 2** (Liaison de données) :
- Ils utilisent les **adresses MAC**
- Ils segmentent les domaines de collision
- Ils maintiennent une **table MAC** (CAM table)

**Note** : Les switchs de niveau 3 peuvent aussi opérer à la couche 3.

---

### ✔️ Question 3 - [ ] C) TCP

**TCP (Transmission Control Protocol)** garantit la livraison :
- Mode **connecté** (établissement de connexion : 3-way handshake)
- **Accusés de réception** (ACK)
- **Retransmission** en cas de perte
- **Contrôle de flux**

**UDP** est non connecté et ne garantit pas la livraison.

---

### ✔️ Question 4 - [ ] B) 48 bits

Une adresse MAC = **48 bits** = **6 octets** :
- Format : `AA:BB:CC:DD:EE:FF`
- 3 premiers octets = OUI (Organizationally Unique Identifier)
- 3 derniers octets = Identifiant unique du constructeur

**Exemple** : `00:1A:2B:3C:4D:5E`

---

### ✔️ Question 5 - [ ] A) 1.0.0.0 à 126.255.255.255

**Classe A** :
- Plage : **1.0.0.0 à 126.255.255.255**
- Masque par défaut : **255.0.0.0** (/8)
- 1er octet : 1-126
- **127.x.x.x** est réservé pour le loopback

**Note** : 0.0.0.0 n'est pas utilisable (réseau par défaut)

---

### ✔️ Question 6 - [ ] B) La boucle locale (loopback)

**127.0.0.0/8** = plage de loopback :
- **127.0.0.1** = localhost
- Utilisé pour tester la pile TCP/IP locale
- Le trafic ne sort JAMAIS de la machine

**Commande** : `ping 127.0.0.1` teste la carte réseau locale

---

### ✔️ Question 7 - [ ] C) 256 adresses

Un réseau **/24** contient **256 adresses** au total :
- Formule : 2^(32-24) = 2^8 = **256**
- Adresses **utilisables** : 256 - 2 = **254**
- 2 adresses réservées : réseau + broadcast

---

### ✔️ Question 8 - [ ] B) 255.255.255.192

**/26** = **255.255.255.192** :
- 26 bits à 1, 6 bits à 0
- Dernier octet : 11000000 = 128 + 64 = **192**
- Bloc = 256 - 192 = **64**
- Hôtes utilisables : 2^6 - 2 = **62**

---

### ✔️ Question 9 - [ ] B) 172.16.50.100

Les **3 plages privées RFC 1918** :
- **10.0.0.0/8** (10.0.0.0 - 10.255.255.255)
- **172.16.0.0/12** (172.16.0.0 - 172.31.255.255) 
- **192.168.0.0/16** (192.168.0.0 - 192.168.255.255)

**172.16.50.100** est dans la plage 172.16.0.0/12

---

### ✔️ Question 10 - [ ] B) 14 hôtes

**/28** = 4 bits pour les hôtes :
- Formule : 2^4 - 2 = 16 - 2 = **14 hôtes utilisables**
- Total d'adresses : 16
- 2 réservées (réseau + broadcast)

**Masque /28** = 255.255.255.240

---

### ✔️ Question 11 - [ ] D) 192.168.1.255

Pour **192.168.1.0/24** :
- Réseau : **192.168.1.0**
- Broadcast : **192.168.1.255**
- Première IP : 192.168.1.1
- Dernière IP : 192.168.1.254

**Règle** : Broadcast = dernière adresse du réseau

---

### ✔️ Question 12 - [ ] B) Résoudre une adresse IP en adresse MAC

**ARP (Address Resolution Protocol)** :
- Résout **IP → MAC**
- Couche 2 du modèle OSI
- Utilise des **broadcasts** ARP
- Table ARP stockée en cache

**Exemple** : "Qui a l'IP 192.168.1.10 ? Donne-moi ton MAC !"

**RARP** fait l'inverse : MAC → IP (obsolète)

---

### ✔️ Question 13 - [ ] C) 443

**HTTPS** = HTTP sécurisé (SSL/TLS) :
- Port **TCP 443** 
- HTTP = port **TCP 80**

**Ports HTTPS à retenir** :
- 443 = HTTPS standard
- 8443 = HTTPS alternatif

---

### ✔️ Question 14 - [ ] C) ping

**ICMP (Internet Control Message Protocol)** :
- Utilisé par **ping** (echo request/reply)
- Utilisé par **traceroute** (TTL exceeded)
- Messages d'erreur IP
- **N'a PAS de numéro de port** (pas de couche 4)

**Autres commandes** :
- ssh = TCP 22
- telnet = TCP 23
- ftp = TCP 21

---

### ✔️ Question 15 - [ ] C) 192.168.1.64/27

**Résolution** :
- /27, Bloc = 256 - 224 = **32**
- 75 ÷ 32 = 2 → 2 × 32 = **64**
- Réseau : **192.168.1.64/27**
- Plage : 192.168.1.64 - 192.168.1.95

---

### ✔️ Question 16 - [ ] B) Segmenter un réseau en domaines de broadcast

**VLAN (Virtual LAN)** :
- Segmente un réseau en **domaines de broadcast séparés**
- Améliore la **sécurité** (isolation)
- Réduit le trafic broadcast
- Flexibilité de configuration

**Exemple** : VLAN 10 (ADMIN) isolé du VLAN 20 (USERS)

---

### ✔️ Question 17 - [ ] B) Transporter plusieurs VLAN

**Port Trunk** (802.1Q) :
- Transporte **plusieurs VLAN** sur un seul lien 
- Utilisé entre switchs ou switch-routeur
- Étiquetage (tagging) des trames

**Port Access** :
- Transporte **UN SEUL VLAN**
- Pour les équipements finaux (PC, imprimante)

---

### ✔️ Question 18 - [ ] B) Port 67

**DHCP utilise UDP** :
- Serveur DHCP : **UDP 67** 
- Client DHCP : **UDP 68**

**Processus DHCP (DORA)** :
1. **D**iscover (client → broadcast)
2. **O**ffer (serveur → client)
3. **R**equest (client → serveur)
4. **A**ck (serveur → client)

---

### ✔️ Question 19 - [ ] B) UDP port 53

**DNS (Domain Name System)** :
- Par défaut : **UDP port 53** 
- Transferts de zone : **TCP port 53**
- Résout nom de domaine → IP

**Exemple** :
- www.google.com → 142.250.185.206

---

### ✔️ Question 20 - [ ] B) 4 couches

**Modèle TCP/IP** = **4 couches** :
1. **Accès réseau** (correspond à couches 1-2 OSI)
2. **Internet** (correspond à couche 3 OSI)
3. **Transport** (correspond à couche 4 OSI)
4. **Application** (correspond à couches 5-6-7 OSI)

---

## 📊 BARÈME PARTIE 1 (Questions 1-20)

**Score** : _____ / 20

| Score | Niveau |
|-------|--------|
| 18-20 | ⭐⭐⭐ Excellent |
| 15-17 | ⭐⭐ Très bien |
| 12-14 | ⭐ Bien |
| 9-11 | ⚠️ À revoir |
| 0-8 | 🔴 Révision urgente |

---

## 💡 CONSEILS POUR RÉVISER

### Comment utiliser ce QCM efficacement ?

**1. Première passe** : Fais le QCM SANS regarder les corrections
- Note tes réponses sur papier
- Chronomètre-toi (20 minutes max pour 20 questions)

**2. Correction** : Compare avec les réponses modèles
- Lis TOUTES les corrections, même pour les bonnes réponses
- Note les points que tu ne comprends pas

**3. Révision ciblée** : Révise tes points faibles
- Score < 15/20 → Relis la fiche correspondante
- Refais le QCM 2-3 jours après

**4. Objectif examen** : Minimum 18/20

---

## 🔍 POINTS CLÉS À RETENIR (PARTIE 1)

### Modèle OSI - 7 couches (mnémotechnique)
```
7. Application   → "Away"
6. Présentation  → "Pizza"
5. Session       → "Sausage"
4. Transport     → "Throw"
3. Réseau        → "Not"
2. Liaison       → "Do"
1. Physique      → "Please"
```

### Équipements par couche OSI
- **Couche 1** : Hub, câbles, répéteur
- **Couche 2** : Switch, pont
- **Couche 3** : Routeur, switch L3
- **Couche 4** : Pare-feu, Load Balancer
- **Couche 7** : Proxy, WAF

### Classes d'adresses IPv4 (à connaître PAR CŒUR)

| Classe | Plage | Masque | Usage |
|--------|-------|--------|-------|
| A | 1.0.0.0 - 126.255.255.255 | 255.0.0.0 (/8) | Très grands réseaux |
| B | 128.0.0.0 - 191.255.255.255 | 255.255.0.0 (/16) | Moyens réseaux |
| C | 192.0.0.0 - 223.255.255.255 | 255.255.255.0 (/24) | Petits réseaux |
| D | 224.0.0.0 - 239.255.255.255 | - | Multicast |
| E | 240.0.0.0 - 255.255.255.255 | - | Expérimental |

**Plages PRIVÉES RFC 1918** (EXAMEN FRÉQUENT) :
- **10.0.0.0/8** → 10.0.0.0 à 10.255.255.255
- **172.16.0.0/12** → 172.16.0.0 à 172.31.255.255
- **192.168.0.0/16** → 192.168.0.0 à 192.168.255.255

### CIDR - Conversion rapide

| CIDR | Masque | Hôtes |
|------|--------|-------|
| /24 | 255.255.255.0 | 254 |
| /25 | 255.255.255.128 | 126 |
| /26 | 255.255.255.192 | 62 |
| /27 | 255.255.255.224 | 30 |
| /28 | 255.255.255.240 | 14 |
| /29 | 255.255.255.248 | 6 |
| /30 | 255.255.255.252 | 2 (liens P2P) |

### Ports TCP/UDP essentiels (TOP EXAMEN)

**Ports TCP** :
- 20/21 : FTP
- 22 : SSH
- 23 : Telnet
- 25 : SMTP
- 80 : HTTP
- 110 : POP3
- 143 : IMAP
- 443 : HTTPS
- 3389 : RDP

**Ports UDP** :
- 53 : DNS
- 67/68 : DHCP
- 69 : TFTP
- 123 : NTP
- 161/162 : SNMP

### TCP vs UDP (différences ESSENTIELLES)

**TCP (Transmission Control Protocol)** :
-  Orienté connexion (3-way handshake)
-  Fiable (accusés de réception)
-  Contrôle de flux et d'erreurs
-  Ordre garanti
- ❌ Plus lent
- **Usage** : HTTP, FTP, SSH, email

**UDP (User Datagram Protocol)** :
-  Non connecté
-  Rapide
-  Faible overhead
- ❌ Pas de garantie de livraison
- ❌ Pas d'ordre garanti
- **Usage** : DNS, DHCP, streaming, VoIP, jeux

### VLAN - Concepts clés

**Port Access** :
- Appartient à **UN SEUL VLAN**
- Connecte un équipement final (PC, imprimante)
- Trames **non étiquetées**

**Port Trunk** :
- Transporte **PLUSIEURS VLAN**
- Connecte switch à switch ou switch à routeur
- Trames **étiquetées** (802.1Q)

**VLAN natif** (Native VLAN) :
- VLAN par défaut sur un trunk
- Trames **non étiquetées** sur le trunk
- Par défaut : VLAN 1

### Protocoles couche 2 vs couche 3

**Couche 2 (Liaison)** :
- ARP (résolution IP → MAC)
- Ethernet (802.3)
- Wi-Fi (802.11)
- PPP

**Couche 3 (Réseau)** :
- IP (IPv4/IPv6)
- ICMP (ping, traceroute)
- IGMP (multicast)

**Couche 4 (Transport)** :
- TCP
- UDP

---

## 🎯 ERREURS FRÉQUENTES À ÉVITER

❌ **Confondre les plages privées** :
- 172.16.0.0/12 va jusqu'à 172.**31**.255.255 (pas 172.16.255.255)

❌ **Oublier les 2 adresses réservées** :
- /24 = 256 adresses, mais 254 utilisables (réseau + broadcast)

❌ **Confondre les ports** :
- HTTP = 80, HTTPS = 443 (pas l'inverse)
- POP3 = 110, IMAP = 143

❌ **Confondre DHCP serveur/client** :
- Serveur DHCP = UDP 67
- Client DHCP = UDP 68

❌ **Oublier que DNS utilise UDP par défaut** :
- Requêtes = UDP 53
- Transferts de zone = TCP 53

---

## 📝 EXERCICES FLASH

**Exercice 1** : 192.168.1.130/25 est dans quel réseau ?
<details>
<summary>Réponse</summary>
Réseau : 192.168.1.128/25 (bloc de 128, 130÷128=1, 1×128=128)
</details>

**Exercice 2** : Combien d'hôtes dans 10.0.0.0/22 ?
<details>
<summary>Réponse</summary>
2^10 - 2 = 1022 hôtes utilisables
</details>

**Exercice 3** : Quelle commande pour tester la connectivité ?
<details>
<summary>Réponse</summary>
ping (utilise ICMP)
</details>

**Exercice 4** : 172.20.0.0 est-elle une adresse privée ?
<details>
<summary>Réponse</summary>
OUI (dans la plage 172.16.0.0/12 → 172.16-31.0.0)
</details>

**Exercice 5** : Quel protocole garantit la livraison ?
<details>
<summary>Réponse</summary>
TCP (UDP ne garantit pas)
</details>

---


---

##  CORRECTIONS DÉTAILLÉES - QUESTIONS 21-40

### ✔️ Question 21 - [ ] B) L2TP

**L2TP (Layer 2 Tunneling Protocol)** :
- Port **UDP 1701** 
- Souvent combiné avec **IPsec** (L2TP/IPsec)
- Ne chiffre PAS par lui-même (besoin d'IPsec)

**Autres protocoles VPN** :
- PPTP : TCP 1723
- IPsec : UDP 500 (IKE) + ESP/AH
- OpenVPN : UDP 1194 (par défaut)

---

### ✔️ Question 22 - [ ] A) AH et ESP

**IPsec utilise deux protocoles** :
- **AH (Authentication Header)** : Authentification + Intégrité
- **ESP (Encapsulating Security Payload)** : Chiffrement + Authentification + Intégrité

**Modes IPsec** :
- Mode **Transport** : Chiffre seulement les données
- Mode **Tunnel** : Chiffre tout le paquet IP (utilisé pour VPN site-à-site)

---

### ✔️ Question 23 - [ ] B) Network Address Translation

**NAT (Network Address Translation)** :
- Traduit **IP privée → IP publique**
- Permet d'économiser les adresses IPv4 publiques
- Types de NAT :
  - **NAT statique** : 1 IP privée → 1 IP publique (mapping permanent)
  - **NAT dynamique** : Pool d'IPs publiques
  - **PAT (NAT overload)** : Plusieurs IP privées → 1 IP publique (via ports)

---

### ✔️ Question 24 - [ ] A) Traduire plusieurs IP privées vers une seule IP publique

**PAT (Port Address Translation)** = NAT overload :
- **Plusieurs IP privées** → **1 seule IP publique** 
- Utilise les **ports sources** pour différencier les connexions
- Le plus utilisé dans les réseaux domestiques/PME

**Exemple** :
- 192.168.1.10:50000 → 200.50.100.1:50000
- 192.168.1.20:50001 → 200.50.100.1:50001

---

### ✔️ Question 25 - [ ] C) RIP

**Protocoles de routage à vecteur de distance** :
- **RIP (Routing Information Protocol)** 
- RIPv2 (amélioration avec CIDR et authentification)

**Protocoles à état de liens** :
- **OSPF** (Open Shortest Path First)
- **IS-IS**

**Protocoles hybrides/Path Vector** :
- **BGP** (Border Gateway Protocol)

---

### ✔️ Question 26 - [ ] B) Dijkstra (SPF)

**OSPF (Open Shortest Path First)** :
- Utilise l'algorithme de **Dijkstra** (SPF) 
- Protocole à **état de liens** (link-state)
- Métrique = **Coût** (basé sur la bande passante)
- Distance administrative = **110**

**Avantages OSPF** :
- Convergence rapide
- Supporte VLSM et CIDR
- Pas de limite de sauts

---

### ✔️ Question 27 - [ ] C) Le nombre de sauts (hop count)

**Métrique RIP** = **Nombre de sauts** (hop count) :
- Chaque routeur traversé = 1 saut
- **Maximum 15 sauts** (16 = infini/inatteignable)
- Ne prend PAS en compte la bande passante

**Problème** : Peut choisir un chemin avec plus de bande passante mais plus de sauts

---

### ✔️ Question 28 - [ ] B) 1

**Distance administrative (AD)** = Fiabilité de la source de routage :

| Source | AD |
|--------|-----|
| Interface directement connectée | **0** |
| Route statique | **1**  |
| EIGRP | 90 |
| OSPF | 110 |
| RIP | 120 |
| Externe | 255 (non fiable) |

**Règle** : Plus l'AD est faible, plus la route est prioritaire

---

### ✔️ Question 29 - [ ] B) Le routage entre systèmes autonomes sur Internet

**BGP (Border Gateway Protocol)** :
- Protocole de routage **EXTERNE** (EGP)
- Utilisé entre **Systèmes Autonomes (AS)** sur Internet 
- Protocole **path-vector**
- Port TCP **179**

**Types de BGP** :
- **eBGP** : Entre AS différents
- **iBGP** : Au sein d'un même AS

---

### ✔️ Question 30 - [ ] B) Éviter les boucles dans un réseau de switchs

**STP (Spanning Tree Protocol - 802.1D)** :
- Évite les **boucles** dans un réseau de switchs 
- Bloque des ports redondants
- Élit un **root bridge** (switch racine)

**Évolutions** :
- **RSTP** (802.1w) : Convergence rapide
- **PVST+** : STP par VLAN (Cisco)

**États des ports STP** :
- Blocking, Listening, Learning, Forwarding, Disabled

---

### ✔️ Question 31 - [ ] B) 22

**SSH (Secure Shell)** :
- Port **TCP 22** 
- Remplace Telnet (non sécurisé)
- **Chiffré** (connexion sécurisée)
- Authentification par mot de passe ou clés

**Version recommandée** : SSHv2 (SSHv1 obsolète)

---

### ✔️ Question 32 - [ ] A) 20 et 21

**FTP (File Transfer Protocol)** utilise **2 ports TCP** :
- Port **21** : Canal de contrôle (commandes)
- Port **20** : Canal de données (transfert de fichiers)

**Modes FTP** :
- **Actif** : Serveur se connecte au client (port 20)
- **Passif** : Client se connecte au serveur (port dynamique)

**FTPS** = FTP + SSL/TLS (ports 989/990)

---

### ✔️ Question 33 - [ ] C) 25

**SMTP (Simple Mail Transfer Protocol)** :
- Port **TCP 25** 
- Utilisé pour **ENVOYER** des emails
- Serveur vers serveur

**Autres ports email** :
- SMTP sécurisé : TCP 587 (STARTTLS)
- SMTPS : TCP 465 (SSL/TLS)

---

### ✔️ Question 34 - [ ] A) POP3 télécharge les emails et les supprime du serveur par défaut

**POP3 (Post Office Protocol v3)** :
- Port **TCP 110**
- **Télécharge** les emails et **les supprime du serveur** par défaut 
- Emails stockés **localement**
- Pas de synchronisation multi-appareils

**IMAP (Internet Message Access Protocol)** :
- Port **TCP 143**
- **Synchronise** les emails sur tous les appareils
- Emails restent sur le serveur
- Gestion de dossiers

**POP3S** : TCP 995 (SSL/TLS)  
**IMAPS** : TCP 993 (SSL/TLS)

---

### ✔️ Question 35 - [ ] B) Il transmet les données en clair (non chiffré)

**Telnet** :
- Port **TCP 23**
- **NON CHIFFRÉ**  → Mots de passe visibles en clair !
- Obsolète, remplacé par **SSH**

**Comparaison** :
- Telnet : Non sécurisé, TCP 23
- SSH : Chiffré, TCP 22 

---

### ✔️ Question 36 - [ ] A) 161 et 162

**SNMP (Simple Network Management Protocol)** :
- Port **UDP 161** : Requêtes/réponses (agent SNMP)
- Port **UDP 162** : Traps (alertes envoyées par l'agent)

**Versions SNMP** :
- SNMPv1 : Pas de sécurité
- SNMPv2c : Community strings
- **SNMPv3** : Authentification + chiffrement (recommandé)

---

### ✔️ Question 37 - [ ] B) Synchroniser l'heure entre équipements

**NTP (Network Time Protocol)** :
- Port **UDP 123**
- **Synchronise l'horloge** des équipements réseau 
- Important pour les logs, certificats, Kerberos

**Stratum** : Niveau de distance de la source de temps :
- Stratum 0 : Horloge atomique
- Stratum 1 : Serveurs connectés à stratum 0
- Stratum 2 : Serveurs synchronisés avec stratum 1

---

### ✔️ Question 38 - [ ] B) UDP

**TFTP (Trivial File Transfer Protocol)** :
- Protocole de transport : **UDP** 
- Port **UDP 69**
- **Pas d'authentification**
- Utilisé pour transferts simples (backup configs Cisco, boot PXE)

**Comparaison** :
- FTP : TCP 20/21, authentification
- TFTP : UDP 69, pas d'auth, plus simple

---

### ✔️ Question 39 - [ ] B) 1500 octets

**MTU (Maximum Transmission Unit)** :
- MTU par défaut Ethernet : **1500 octets** 
- MTU IPv4 min : 576 octets
- MTU IPv6 min : 1280 octets

**Jumbo Frames** : MTU jusqu'à 9000 octets (réseaux spécialisés)

**Fragmentation** : Si paquet > MTU, il est fragmenté

---

### ✔️ Question 40 - [ ] B) Prioriser certains types de trafic

**QoS (Quality of Service)** :
- **Priorise certains types de trafic** 
- Garantit bande passante, latence, gigue (jitter)
- Critique pour VoIP, vidéo

**Mécanismes QoS** :
- **Classification** : Identifier le trafic (DSCP, CoS)
- **Marquage** : Étiqueter les paquets
- **Policing** : Limiter le débit
- **Shaping** : Lisser le trafic
- **Queuing** : Files d'attente prioritaires

**Classes de trafic** :
- **Voix** : Priorité maximale (faible latence)
- **Vidéo** : Haute priorité
- **Données critiques** : Priorité moyenne
- **Best Effort** : Pas de garantie

---

## 📊 BARÈME PARTIE 2 (Questions 21-40)

**Score** : _____ / 20

| Score | Niveau |
|-------|--------|
| 18-20 | ⭐⭐⭐ Excellent |
| 15-17 | ⭐⭐ Très bien |
| 12-14 | ⭐ Bien |
| 9-11 | ⚠️ À revoir |
| 0-8 | 🔴 Révision urgente |

---

## 🔍 POINTS CLÉS À RETENIR (PARTIE 2)

### VPN - Protocoles essentiels

**PPTP (Point-to-Point Tunneling Protocol)** :
- Port : TCP 1723
- Ancien, **obsolète** (failles de sécurité)
- Facile à bloquer

**L2TP (Layer 2 Tunneling Protocol)** :
- Port : UDP 1701
- Souvent combiné avec IPsec (L2TP/IPsec)
- Ne chiffre PAS seul

**IPsec** :
- Ports : UDP 500 (IKE), protocoles ESP/AH
- **2 modes** : Transport (données) et Tunnel (tout le paquet)
- **2 protocoles** : AH (auth) et ESP (chiffrement)

**OpenVPN** :
- Port : UDP 1194 (par défaut, configurable)
- Open source, très sécurisé
- Utilise SSL/TLS

### NAT vs PAT

**NAT (Network Address Translation)** :
- **NAT statique** : 1 IP privée ↔ 1 IP publique (permanent)
- **NAT dynamique** : Pool d'IPs publiques (temporaire)

**PAT (Port Address Translation)** = NAT Overload :
- Plusieurs IPs privées → 1 IP publique
- Utilise les **ports** pour différencier
- Le plus courant (box Internet, routeurs PME)

**Exemple PAT** :
```
192.168.1.10:50000 → 200.50.100.1:50000
192.168.1.20:50001 → 200.50.100.1:50001
192.168.1.30:50002 → 200.50.100.1:50002
```

### Protocoles de routage - Comparaison

| Protocole | Type | Métrique | Distance Admin | Convergence |
|-----------|------|----------|----------------|-------------|
| RIP | Vecteur distance | Hop count | 120 | Lente |
| RIPv2 | Vecteur distance | Hop count | 120 | Lente |
| OSPF | État de liens | Coût (bande passante) | 110 | Rapide |
| EIGRP | Hybride | Composite | 90 | Très rapide |
| BGP | Path vector | Policies | 20 (eBGP) | Variable |

**Distance administrative (AD)** :
```
Connectée directement : 0
Route statique : 1
eBGP : 20
EIGRP : 90
OSPF : 110
RIP : 120
iBGP : 200
Inconnue : 255
```

**Règle** : Plus l'AD est **basse**, plus la route est **prioritaire**

### RIP - Caractéristiques

**Limites** :
- Maximum **15 sauts** (16 = infini/inatteignable)
- Mises à jour toutes les **30 secondes**
- Convergence **lente**
- Ne prend PAS en compte la bande passante

**RIPv1 vs RIPv2** :
- RIPv1 : Classful, broadcast
- RIPv2 : CIDR/VLSM, multicast (224.0.0.9), authentification

**Quand l'utiliser** : Petits réseaux simples, compatibilité legacy

### OSPF - Concepts clés

**Avantages** :
- Convergence **rapide**
- Supporte **VLSM**
- Pas de limite de sauts
- Hiérarchique (areas)

**Composants** :
- **Area 0** : Backbone (obligatoire)
- **Router ID** : Identifiant unique du routeur
- **DR/BDR** : Designated Router / Backup (réseaux multi-accès)

**États OSPF** :
1. Down
2. Init
3. 2-Way
4. ExStart
5. Exchange
6. Loading
7. Full

**Métrique OSPF** :
```
Coût = 100 000 000 / Bande passante (bps)
```
Exemple : FastEthernet (100 Mbps) → Coût = 1

### BGP - Internet Routing

**Usage** :
- Routage **entre AS** (Autonomous Systems)
- Utilisé par les **FAI** et grandes entreprises
- Gère les **routes Internet**

**Types** :
- **eBGP** : Entre AS différents (AD = 20)
- **iBGP** : Au sein d'un même AS (AD = 200)

**Caractéristiques** :
- Protocole **path-vector**
- Basé sur **policies** (pas juste la métrique)
- Port **TCP 179**
- Très stable, lente convergence (par design)

### Spanning Tree Protocol (STP)

**Problème résolu** :
- **Boucles** dans un réseau de switchs
- Tempêtes de broadcast

**Fonctionnement** :
1. Élection d'un **Root Bridge** (plus petite priorité)
2. Calcul du **meilleur chemin** vers le root
3. **Blocage** des ports redondants

**Évolutions** :
- **STP** (802.1D) : 30-50 secondes de convergence
- **RSTP** (802.1w) : <6 secondes (Rapid STP)
- **PVST+** : STP par VLAN (Cisco)
- **MST** : Multiple STP (802.1s)

**États des ports** :
- **Blocking** : Bloqué (écoute BPDU)
- **Listening** : Écoute
- **Learning** : Apprentissage MAC
- **Forwarding** : Transfert actif
- **Disabled** : Désactivé

### Ports TCP/UDP avancés

**Email** :
- SMTP : TCP **25** (serveur à serveur)
- SMTP (STARTTLS) : TCP **587** (client à serveur sécurisé)
- SMTPS : TCP **465** (obsolète)
- POP3 : TCP **110**
- POP3S : TCP **995**
- IMAP : TCP **143**
- IMAPS : TCP **993**

**Autres services** :
- FTP : TCP **20/21**
- FTPS : TCP **989/990**
- SSH : TCP **22**
- Telnet : TCP **23**
- RDP : TCP **3389**
- LDAP : TCP **389**
- LDAPS : TCP **636**

### POP3 vs IMAP (EXAMEN FRÉQUENT)

**POP3** :
- **Télécharge** les emails
- **Supprime** du serveur (par défaut)
- Stockage **local**
- **1 appareil** recommandé
- Plus simple, moins de fonctionnalités

**IMAP** :
- **Synchronise** les emails
- Reste **sur le serveur**
- Gestion de **dossiers**
- **Multi-appareils**
- Plus complexe, plus flexible

**Choix** :
- POP3 → 1 ordinateur, pas de backup serveur
- IMAP → Plusieurs appareils, mobilité

### MTU (Maximum Transmission Unit)

**Valeurs standard** :
- Ethernet : **1500 octets** (le plus courant)
- Jumbo frames : 9000 octets (réseaux spécialisés)
- IPv4 minimum : 576 octets
- IPv6 minimum : 1280 octets

**Fragmentation** :
- Si paquet > MTU → fragmentation
- Peut causer des problèmes de performance
- IPv6 : pas de fragmentation en route (uniquement à la source)

**Path MTU Discovery** :
- Découvrir le MTU le plus petit sur le chemin
- Éviter la fragmentation

### QoS (Quality of Service)

**Classes de trafic** (priorité décroissante) :
1. **Voix (VoIP)** : Priorité maximale, latence <150ms
2. **Vidéo** : Haute priorité, sensible à la gigue
3. **Données critiques** : Priorité moyenne
4. **Best Effort** : Pas de garantie (trafic normal)
5. **Scavenger** : Priorité minimale (P2P, etc.)

**Mécanismes QoS** :
- **Classification** : Identifier le trafic (DSCP, CoS)
- **Marking** : Étiqueter les paquets
- **Policing** : Limiter le débit (drop si dépassement)
- **Shaping** : Lisser le trafic (buffer)
- **Queuing** : Files d'attente (FIFO, WFQ, CBWFQ)

**DSCP (Differentiated Services Code Point)** :
- 6 bits dans l'en-tête IP
- EF (Expedited Forwarding) : Voix
- AF (Assured Forwarding) : Classes de service
- BE (Best Effort) : Par défaut

---

## 🎯 SCÉNARIOS D'EXAMEN FRÉQUENTS

### Scénario 1 : Choix du protocole VPN

**Question type** : "Une entreprise souhaite connecter 2 sites distants de façon sécurisée. Quel protocole VPN recommandez-vous ?"

**Réponse** :
- **IPsec site-à-site** en mode tunnel
- Sécurisé, standard, supporté par tous les équipements
- Alternative : OpenVPN si équipements hétérogènes

### Scénario 2 : Problème de routage

**Question type** : "Un routeur a 2 routes vers 10.10.0.0/16 : une statique et une OSPF. Laquelle sera choisie ?"

**Réponse** :
- **Route statique** (AD = 1)
- OSPF a une AD de 110
- Règle : Plus petite AD = priorité

### Scénario 3 : Email ne fonctionne pas

**Question type** : "Les utilisateurs ne peuvent plus recevoir d'emails. Quel port vérifier ?"

**Réponse** :
- Port **25** (SMTP) pour la réception serveur à serveur
- Ports **110** (POP3) ou **143** (IMAP) pour les clients
- Vérifier pare-feu et DNS (enregistrement MX)

---

## 📝 EXERCICES FLASH

**Exercice 1** : Un routeur reçoit 192.168.1.50 et doit faire du NAT. Expliquez.
<details>
<summary>Réponse</summary>
192.168.1.50 (privée) est traduite en une IP publique (ex: 200.50.100.1) pour sortir sur Internet. Le routeur maintient une table de correspondance.
</details>

**Exercice 2** : Différence entre L2TP et IPsec ?
<details>
<summary>Réponse</summary>
L2TP = tunnel (pas de chiffrement). IPsec = chiffrement + authentification. On les combine souvent : L2TP/IPsec.
</details>

**Exercice 3** : Pourquoi RIP est limité à 15 sauts ?
<details>
<summary>Réponse</summary>
Pour éviter les boucles infinies. 16 sauts = infini/inatteignable. C'est une limitation du protocole.
</details>

**Exercice 4** : SMTP utilise TCP ou UDP ?
<details>
<summary>Réponse</summary>
TCP (port 25). L'email nécessite une livraison fiable → TCP.
</details>

**Exercice 5** : Un switch a des boucles. Quel protocole activer ?
<details>
<summary>Réponse</summary>
STP (Spanning Tree Protocol) ou mieux RSTP (Rapid STP). Bloque les ports redondants.
</details>

---

##  CORRECTIONS DÉTAILLÉES - QUESTIONS 41-60

### ✔️ Question 41 - [ ] A) L'adresse IP source uniquement

**ACL Standard Cisco** :
- Filtre **UNIQUEMENT sur l'IP source** 
- Numérotation : **1-99** et **1300-1999**
- Moins flexible qu'une ACL étendue

**Exemple** :
```
R1(config)# access-list 10 permit 192.168.1.0 0.0.0.255
R1(config)# access-list 10 deny any
```

---

### ✔️ Question 42 - [ ] A) IP source, IP destination, protocole, ports

**ACL Étendue Cisco** :
- Filtre sur : **IP source, IP destination, protocole, ports** 
- Numérotation : **100-199** et **2000-2699**
- Plus flexible et précise

**Exemple** :
```
R1(config)# access-list 100 permit tcp 192.168.1.0 0.0.0.255 any eq 80
R1(config)# access-list 100 deny ip any any
```

---

### ✔️ Question 43 - [ ] A) 1-99 et 1300-1999

**Plages de numérotation ACL Cisco** :

| Type ACL | Plage |
|----------|-------|
| **Standard** | **1-99** et **1300-1999**  |
| **Étendue** | **100-199** et **2000-2699** |

**ACL nommées** : Plus lisibles (recommandées)
```
R1(config)# ip access-list standard PERMIT_LAN
```

---

### ✔️ Question 44 - [ ] A) Le stateful suit l'état des connexions

**Pare-feu Stateful** :
- **Suit l'état des connexions** (TCP établies) 
- Autorise automatiquement les réponses aux connexions sortantes
- Plus intelligent et sécurisé

**Pare-feu Stateless** :
- Filtre paquet par paquet indépendamment
- Ne garde pas de contexte
- Plus simple mais moins sécurisé

**Exemple** :
- Stateful : Si PC envoie requête HTTP → réponse autorisée automatiquement
- Stateless : Faut créer règle pour autoriser la réponse

---

### ✔️ Question 45 - [ ] B) Isoler les serveurs publics entre Internet et le LAN interne

**DMZ (DeMilitarized Zone)** :
- Zone **intermédiaire** entre Internet et LAN interne 
- Contient les **serveurs publics** (Web, Mail, FTP)
- Protection à 2 niveaux :
  - Pare-feu externe : Internet → DMZ
  - Pare-feu interne : DMZ → LAN

**Architecture** :
```
Internet → [Pare-feu externe] → DMZ (serveurs publics) → [Pare-feu interne] → LAN interne
```

---

### ✔️ Question 46 - [ ] C) 128 bits

**IPv6** = **128 bits** :
- IPv4 = 32 bits (4 milliards d'adresses)
- IPv6 = 128 bits (**340 sextillions d'adresses**) 

**Format** : 8 groupes de 16 bits (4 caractères hexa)

---

### ✔️ Question 47 - [ ] B) 8 groupes de 16 bits en hexadécimal séparés par :

**Format IPv6** :
- **8 groupes de 16 bits** en **hexadécimal** séparés par **:** 

**Exemple** :
```
2001:0db8:85a3:0000:0000:8a2e:0370:7334
```

**Règles de simplification** :
1. Supprimer les zéros de tête : `0db8` → `db8`
2. Remplacer suite de zéros par `::` (une seule fois)

**Simplifié** :
```
2001:db8:85a3::8a2e:370:7334
```

---

### ✔️ Question 48 - [ ] B) ::1

**Adresse de loopback IPv6** = **::1** 

**Comparaison** :
- IPv4 loopback : **127.0.0.1** (/8 entier : 127.0.0.0/8)
- IPv6 loopback : **::1** (/128)

**Usage** : Test de la pile IPv6 locale
```
ping ::1
```

---

### ✔️ Question 49 - [ ] B) FE80::/10

**Adresses IPv6 Link-Local** :
- Préfixe : **FE80::/10** 
- Auto-configurées sur chaque interface IPv6
- Valides **uniquement sur le lien local** (pas routables)
- Utilisées pour NDP, autoconfiguration

**Exemple** :
```
FE80::1
FE80::A1B2:C3D4:E5F6:7890
```

---

### ✔️ Question 50 - [ ] B) NDP (Neighbor Discovery Protocol)

**NDP (Neighbor Discovery Protocol)** :
- Remplace **ARP** en IPv6 
- Fait partie d'**ICMPv6**
- Fonctions :
  - Résolution d'adresse (comme ARP)
  - Découverte de routeurs
  - Détection de doublons (DAD)
  - Autoconfiguration (SLAAC)

**Messages NDP** :
- Router Solicitation (RS)
- Router Advertisement (RA)
- Neighbor Solicitation (NS) ← comme ARP request
- Neighbor Advertisement (NA) ← comme ARP reply

---

### ✔️ Question 51 - [ ] C) AES

**WPA2 (Wi-Fi Protected Access 2)** :
- Chiffrement : **AES (Advanced Encryption Standard)** 
- Algorithme : **CCMP** (basé sur AES)
- Très sécurisé

**Évolution Wi-Fi Security** :
1. **WEP** : RC4, **obsolète** (cassable en minutes)
2. **WPA** : TKIP, amélioration temporaire
3. **WPA2** : **AES/CCMP**  (standard actuel)
4. **WPA3** : SAE, dernière version (2018)

---

### ✔️ Question 52 - [ ] C) 802.11ac

**Normes Wi-Fi** :

| Norme | Année | Bande | Débit max |
|-------|-------|-------|-----------|
| 802.11b | 1999 | 2,4 GHz | 11 Mbps |
| 802.11g | 2003 | 2,4 GHz | 54 Mbps |
| 802.11n | 2009 | 2,4/5 GHz | 600 Mbps |
| **802.11ac** | 2013 | **5 GHz** | **1,3 Gbps**  |
| 802.11ax (Wi-Fi 6) | 2019 | 2,4/5 GHz | 10 Gbps |

---

### ✔️ Question 53 - [ ] B) Service Set Identifier

**SSID (Service Set Identifier)** :
- **Nom du réseau Wi-Fi** 
- Diffusé dans les beacons (trames balise)
- Peut être **masqué** (hidden SSID) mais pas très sécurisé

**BSSID** : Adresse MAC du point d'accès

---

### ✔️ Question 54 - [ ] C) 8080

**Ports Proxy** :
- Proxy HTTP : Port **8080**  (ou 3128 pour Squid)
- HTTP standard : Port 80
- HTTPS : Port 443

**Usage Proxy** :
- Cache web
- Filtrage de contenu
- Anonymisation
- Contrôle d'accès

---

### ✔️ Question 55 - [ ] B) Répartir le trafic entre plusieurs serveurs

**Load Balancer (Répartiteur de charge)** :
- **Répartit le trafic** entre plusieurs serveurs 
- Améliore la **disponibilité** et **performance**
- Détecte les serveurs défaillants

**Algorithmes de répartition** :
- **Round-robin** : Chacun son tour
- **Least connections** : Serveur le moins chargé
- **IP hash** : Basé sur IP source
- **Weighted** : Selon capacité du serveur

**Exemple** : HAProxy, F5, Nginx

---

### ✔️ Question 56 - [ ] B) Redondance de passerelle

**HSRP / VRRP** = Protocoles de **redondance de passerelle** 

**HSRP (Hot Standby Router Protocol)** :
- Protocole **Cisco propriétaire**
- IP virtuelle partagée entre routeurs
- Élection d'un routeur actif

**VRRP (Virtual Router Redundancy Protocol)** :
- Standard **ouvert** (RFC 5798)
- Fonctionne pareil qu'HSRP

**But** : Si passerelle par défaut tombe, une autre prend le relais automatiquement

---

### ✔️ Question 57 - [ ] A) 389

**LDAP (Lightweight Directory Access Protocol)** :
- Port TCP **389**  (LDAP standard)
- Port TCP **636** (LDAPS - LDAP over SSL/TLS)
- Protocole d'annuaire (Active Directory, OpenLDAP)

**Usage** :
- Authentification centralisée
- Annuaire d'entreprise
- Gestion des utilisateurs et groupes

---

### ✔️ Question 58 - [ ] C) TCP et UDP 88

**Kerberos** :
- Port **TCP et UDP 88** 
- Protocole d'**authentification** réseau
- Utilisé par **Active Directory**
- Basé sur tickets (TGT, TGS)

**Fonctionnement** :
1. Client demande TGT au KDC (Key Distribution Center)
2. Client reçoit TGT chiffré
3. Client demande ticket de service (TGS)
4. Client accède au service avec le ticket

---

### ✔️ Question 59 - [ ] C) 3389

**RDP (Remote Desktop Protocol)** :
- Port **TCP 3389** 
- Protocole **Microsoft** pour bureau à distance
- Connexion graphique à un Windows distant

**Alternatives** :
- **VNC** : Port 5900 (TCP)
- **SSH** : Port 22 (ligne de commande)
- **TeamViewer** : Port dynamique

---

### ✔️ Question 60 - [ ] A) 0.0.0.15

**Wildcard mask** = **Inverse du masque de sous-réseau**

**Calcul** :
```
Masque de sous-réseau : 255.255.255.240
Wildcard mask : 255.255.255.255 - 255.255.255.240 = 0.0.0.15 
```

**Règle wildcard** :
- **0** = Doit correspondre (check)
- **1** = Peut être n'importe quoi (don't care)

**Exemples** :
| Masque | Wildcard |
|--------|----------|
| 255.255.255.0 | 0.0.0.255 |
| 255.255.255.192 | 0.0.0.63 |
| 255.255.255.240 | 0.0.0.15  |
| 255.255.255.252 | 0.0.0.3 |

**Usage** : ACL Cisco, OSPF, EIGRP

---

## 📊 BARÈME PARTIE 3 (Questions 41-60)

**Score** : _____ / 20

| Score | Niveau |
|-------|--------|
| 18-20 | ⭐⭐⭐ Excellent |
| 15-17 | ⭐⭐ Très bien |
| 12-14 | ⭐ Bien |
| 9-11 | ⚠️ À revoir |
| 0-8 | 🔴 Révision urgente |

---

## 📊 SCORE TOTAL QCM RÉSEAU (60 QUESTIONS)

**Partie 1** : _____ / 20  
**Partie 2** : _____ / 20  
**Partie 3** : _____ / 20  

**TOTAL** : _____ / 60

---

## 🎯 ÉVALUATION FINALE

| Score Total | Niveau | Action |
|-------------|--------|--------|
| 54-60 | ⭐⭐⭐ EXPERT | Tu es prêt pour l'examen ! |
| 48-53 | ⭐⭐ TRÈS BON | Révise les erreurs |
| 42-47 | ⭐ BON | Refais les parties faibles |
| 36-41 | ⚠️ MOYEN | Révision approfondie nécessaire |
| 0-35 | 🔴 INSUFFISANT | Relire toutes les fiches |

---

## 📝 ANALYSE DE TES ERREURS

### Points faibles identifiés :

**Partie 1 (Fondamentaux)** :
- [ ] Modèle OSI/TCP-IP
- [ ] Adressage IPv4
- [ ] Subnetting
- [ ] Protocoles de base

**Partie 2 (Routage/VPN)** :
- [ ] VPN et IPsec
- [ ] Routage dynamique (RIP, OSPF, BGP)
- [ ] NAT/PAT
- [ ] Protocoles applicatifs

**Partie 3 (Sécurité/Avancé)** :
- [ ] ACL et pare-feu
- [ ] IPv6
- [ ] Wi-Fi
- [ ] Services avancés

---

## 🔄 PLAN D'ACTION

1. **Note ton score** dans chaque partie
2. **Identifie tes points faibles**
3. **Relis les fiches** correspondantes
4. **Refais le QCM** jusqu'à avoir 90% minimum
5. **Passe aux exercices pratiques**

---