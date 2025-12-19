# 🔢 FICHE DE RÉVISION EXPRESS - PORTS/PROTOCOLES/ACRONYMES
## Essentiels pour l'examen TSSR

> **⚠️ CRITIQUE** : Ces informations sont demandées à CHAQUE examen TSSR !

---

## 🌐 PORTS ET PROTOCOLES - À CONNAÎTRE PAR CŒUR

### Ports les plus importants pour l'examen

| Port | Protocole | Type | Service/Utilisation |
|------|-----------|------|---------------------|
| **20** | FTP-DATA | TCP | Transfert de données FTP |
| **21** | FTP | TCP | Contrôle FTP (File Transfer Protocol) |
| **22** | SSH | TCP | Secure Shell (connexion sécurisée) |
| **23** | Telnet | TCP | Connexion non sécurisée (obsolète) |
| **25** | SMTP | TCP | Envoi d'emails (Simple Mail Transfer Protocol) |
| **53** | DNS | TCP/UDP | Domain Name System (résolution noms) |
| **67** | DHCP Server | UDP | Serveur DHCP |
| **68** | DHCP Client | UDP | Client DHCP |
| **69** | TFTP | UDP | Trivial FTP (transfert fichiers simple) |
| **80** | HTTP | TCP | Web non sécurisé |
| **110** | POP3 | TCP | Réception emails (Post Office Protocol) |
| **123** | NTP | UDP | Network Time Protocol (synchronisation temps) |
| **143** | IMAP | TCP | Réception emails (Internet Message Access Protocol) |
| **161** | SNMP | UDP | Supervision réseau (Simple Network Management Protocol) |
| **162** | SNMP Trap | UDP | Alertes SNMP |
| **389** | LDAP | TCP | Annuaire (Lightweight Directory Access Protocol) |
| **443** | HTTPS | TCP | Web sécurisé (HTTP + SSL/TLS) |
| **445** | SMB | TCP | Partage de fichiers Windows (Server Message Block) |
| **465** | SMTPS | TCP | SMTP sécurisé (obsolète, utiliser 587) |
| **514** | Syslog | UDP | Logs système |
| **587** | SMTP | TCP | SMTP moderne pour envoi emails (STARTTLS) |
| **636** | LDAPS | TCP | LDAP sécurisé (LDAP + SSL) |
| **993** | IMAPS | TCP | IMAP sécurisé (IMAP + SSL/TLS) |
| **995** | POP3S | TCP | POP3 sécurisé (POP3 + SSL/TLS) |
| **1433** | MS SQL | TCP | Microsoft SQL Server |
| **1521** | Oracle | TCP | Oracle Database |
| **2049** | NFS | TCP/UDP | Network File System (partage Linux) |
| **3306** | MySQL | TCP | Base de données MySQL/MariaDB |
| **3389** | RDP | TCP | Remote Desktop Protocol (Bureau à distance Windows) |
| **3724** | WoW | TCP | World of Warcraft (exemple jeu) |
| **5432** | PostgreSQL | TCP | Base de données PostgreSQL |
| **8080** | HTTP-Alt | TCP | HTTP alternatif (souvent proxy/web) |
| **8443** | HTTPS-Alt | TCP | HTTPS alternatif |

### 🎯 Mnémotechnique pour les ports ESSENTIELS
```
22 = SSH → "22 c'est l'âge pour conduire secure"
25 = SMTP → "25 décembre = on envoie des cartes (mails)"
53 = DNS → "53 régions, chacune a son nom"
80 = HTTP → "8-0 = H-ttp (ressemble au H)"
110 = POP3 → "110 = police, on récupère (mails)"
143 = IMAP → "1-4-3 = I LOVE YOU = j'aime avoir mes mails partout"
443 = HTTPS → "443 + 80 = HTTPS + HTTP"
3389 = RDP → "3389 = Windows à distance"
```

---

## 📧 PROTOCOLES DE MESSAGERIE - DIFFÉRENCES IMPORTANTES

### POP3 vs IMAP (question FRÉQUENTE à l'examen)

| Caractéristique | POP3 (Port 110/995) | IMAP (Port 143/993) |
|----------------|---------------------|---------------------|
| **Téléchargement** | Télécharge ET supprime du serveur | Garde sur le serveur |
| **Synchronisation** | ❌ Pas de synchro | ✅ Synchro multi-appareils |
| **Accès hors ligne** | ✅ Oui (emails locaux) | ⚠️ Limité |
| **Gestion serveur** | ❌ Faible | ✅ Forte (dossiers, flags) |
| **Usage disque local** | 📈 Beaucoup | 📉 Peu |
| **Usage disque serveur** | 📉 Peu | 📈 Beaucoup |
| **Meilleur pour** | 1 seul appareil, espace serveur limité | Multi-appareils, accès web |

### SMTP (envoi) vs POP3/IMAP (réception)
- **SMTP (25/587/465)** : Protocole d'ENVOI d'emails
  - Port 25 : Serveur à serveur
  - Port 587 : Client à serveur (moderne, recommandé)
  - Port 465 : SMTPS (obsolète)
  
- **POP3/IMAP** : Protocoles de RÉCEPTION d'emails
  - Utilisés par le client pour récupérer les emails depuis le serveur

### Configuration client de messagerie (question d'examen)
**Paramètres à configurer** :
1. **Serveur SMTP** (envoi) : smtp.example.com, port 587, STARTTLS
2. **Serveur POP3 ou IMAP** (réception) : imap.example.com, port 993, SSL
3. **Identifiants** : adresse email + mot de passe
4. **Type de sécurité** : SSL/TLS ou STARTTLS

---

## 🔐 PROTOCOLES DE SÉCURITÉ

### Cryptage et Authentification

| Protocole | Port | Utilisation | Sécurité |
|-----------|------|-------------|----------|
| **SSH** | 22 | Connexion sécurisée, SFTP | ✅ Chiffré |
| **Telnet** | 23 | Connexion non sécurisée | ❌ En clair (DANGER) |
| **SFTP** | 22 | Transfert fichiers sécurisé (via SSH) | ✅ Chiffré |
| **FTPS** | 990 | FTP sécurisé (FTP + SSL/TLS) | ✅ Chiffré |
| **HTTPS** | 443 | HTTP sécurisé (HTTP + SSL/TLS) | ✅ Chiffré |
| **LDAPS** | 636 | LDAP sécurisé (LDAP + SSL) | ✅ Chiffré |
| **RDP** | 3389 | Bureau à distance (peut être chiffré) | ⚠️ Configurable |

### SSL/TLS
- **SSL** (Secure Sockets Layer) : Ancien protocole (obsolète)
- **TLS** (Transport Layer Security) : Remplaçant moderne de SSL
- **Versions actuelles** : TLS 1.2 et TLS 1.3 (à connaître pour l'exam)
- **Utilisation** : HTTPS, SMTPS, IMAPS, POP3S, FTPS, LDAPS

### VPN - Protocoles
- **PPTP** : Point-to-Point Tunneling Protocol (obsolète, faible sécurité)
- **L2TP** : Layer 2 Tunneling Protocol (souvent avec IPsec)
- **IPsec** : Internet Protocol Security (VPN site-à-site)
- **OpenVPN** : Open source, très utilisé
- **WireGuard** : Moderne, performant
- **SSL VPN** : VPN via SSL/TLS (accès via navigateur)

---

## 🌐 PROTOCOLES RÉSEAU

### Suite TCP/IP

| Couche OSI | Couche TCP/IP | Protocoles | Rôle |
|------------|---------------|------------|------|
| 7-6-5 (Application, Présentation, Session) | **Application** | HTTP, FTP, SMTP, DNS, DHCP | Services utilisateur |
| 4 (Transport) | **Transport** | TCP, UDP | Fiabilité, ports |
| 3 (Réseau) | **Internet** | IP, ICMP, ARP | Adressage, routage |
| 2-1 (Liaison, Physique) | **Accès réseau** | Ethernet, Wi-Fi | Transmission physique |

### TCP vs UDP (TRÈS IMPORTANT)

| Caractéristique | TCP (Transmission Control Protocol) | UDP (User Datagram Protocol) |
|----------------|-------------------------------------|------------------------------|
| **Connexion** | ✅ Orienté connexion (handshake) | ❌ Sans connexion |
| **Fiabilité** | ✅ Garantit la livraison | ❌ Pas de garantie |
| **Ordre** | ✅ Ordre préservé | ❌ Pas d'ordre garanti |
| **Vitesse** | 🐢 Plus lent | 🐇 Plus rapide |
| **Overhead** | 📈 Plus lourd | 📉 Léger |
| **Cas d'usage** | Web, Email, FTP, SSH | DNS, Streaming, VoIP, Jeux |
| **Exemple** | Téléchargement fichier | Streaming vidéo |

**Mnémotechnique** :
- **TCP** = "**T**rès **C**omplètement **P**récis" → pour ce qui doit arriver intact
- **UDP** = "**U**ltra **D**irect **P**ide" → pour ce qui doit être rapide

### Protocoles importants

#### DNS (Domain Name System) - Port 53 UDP/TCP
- **Rôle** : Résoudre noms de domaine → adresses IP
- **Exemple** : google.com → 142.250.185.46
- **Types d'enregistrements** :
  - **A** : IPv4
  - **AAAA** : IPv6
  - **MX** : Serveur mail
  - **CNAME** : Alias
  - **PTR** : Résolution inverse (IP → nom)
  - **NS** : Serveur de noms
  - **TXT** : Texte (SPF, DKIM, etc.)

#### DHCP (Dynamic Host Configuration Protocol) - Port 67/68 UDP
- **Rôle** : Attribution automatique d'IP aux clients
- **Processus DORA** :
  1. **D**iscover : Client cherche serveur DHCP
  2. **O**ffer : Serveur propose une IP
  3. **R**equest : Client demande l'IP proposée
  4. **A**cknowledge : Serveur confirme
- **Informations fournies** :
  - Adresse IP
  - Masque de sous-réseau
  - Passerelle par défaut
  - Serveurs DNS
  - Durée de bail (lease time)

#### ICMP (Internet Control Message Protocol)
- **Rôle** : Messages de contrôle et d'erreur
- **Commandes** :
  - `ping` : Test de connectivité (echo request/reply)
  - `traceroute` : Tracer le chemin des paquets

#### ARP (Address Resolution Protocol)
- **Rôle** : Résoudre adresse IP → adresse MAC
- **Commande** : `arp -a` (afficher table ARP)

---

## 🔌 PORTS SPÉCIAUX ET PLAGES

### Plages de ports
- **0-1023** : Ports **bien connus** (well-known) - Services système
- **1024-49151** : Ports **enregistrés** (registered) - Applications
- **49152-65535** : Ports **dynamiques/privés** (ephemeral) - Connexions temporaires

### Ports à éviter en production
- **Telnet (23)** : Non sécurisé, utiliser SSH
- **HTTP (80)** : Non chiffré, utiliser HTTPS
- **FTP (21)** : Identifiants en clair, utiliser SFTP/FTPS
- **SNMP v1/v2 (161)** : Faible sécurité, utiliser SNMPv3

---

## 🏷️ ACRONYMES ESSENTIELS - À CONNAÎTRE

### Sécurité et Réglementation
- **ANSSI** : Agence Nationale de la Sécurité des Systèmes d'Information
- **CNIL** : Commission Nationale de l'Informatique et des Libertés
- **RGPD** : Règlement Général sur la Protection des Données
- **PSSI** : Politique de Sécurité des Systèmes d'Information
- **RSSI** : Responsable de la Sécurité des Systèmes d'Information
- **DPO** : Data Protection Officer (Délégué à la Protection des Données)
- **2FA** : Two-Factor Authentication (Authentification à deux facteurs)
- **MFA** : Multi-Factor Authentication
- **PKI/IGC** : Public Key Infrastructure / Infrastructure de Gestion de Clés
- **DEEE** : Déchets d'Équipements Électriques et Électroniques

### Réseau et Infrastructure
- **LAN** : Local Area Network
- **WAN** : Wide Area Network
- **MAN** : Metropolitan Area Network
- **PAN** : Personal Area Network
- **VLAN** : Virtual Local Area Network
- **VPN** : Virtual Private Network
- **DMZ** : Demilitarized Zone (Zone démilitarisée)
- **NAT** : Network Address Translation
- **PAT** : Port Address Translation
- **DHCP** : Dynamic Host Configuration Protocol
- **DNS** : Domain Name System
- **DDNS** : Dynamic DNS
- **QoS** : Quality of Service
- **ACL** : Access Control List
- **SNMP** : Simple Network Management Protocol
- **NTP** : Network Time Protocol
- **RADIUS** : Remote Authentication Dial-In User Service
- **802.1X** : Norme d'authentification réseau

### Systèmes et Services
- **OS** : Operating System
- **AD** : Active Directory
- **DC** : Domain Controller (Contrôleur de domaine)
- **OU** : Organizational Unit
- **GPO** : Group Policy Object (Stratégie de groupe)
- **LDAP** : Lightweight Directory Access Protocol
- **NTFS** : New Technology File System
- **FAT32** : File Allocation Table 32
- **EXT4** : Fourth Extended File System
- **XFS** : X File System
- **LVM** : Logical Volume Manager
- **RAID** : Redundant Array of Independent Disks
- **NAS** : Network Attached Storage
- **SAN** : Storage Area Network
- **NFS** : Network File System
- **SMB/CIFS** : Server Message Block / Common Internet File System
- **iSCSI** : Internet Small Computer Systems Interface
- **UEFI** : Unified Extensible Firmware Interface
- **BIOS** : Basic Input/Output System
- **GRUB** : Grand Unified Bootloader

### Virtualisation et Cloud
- **VM** : Virtual Machine
- **VDI** : Virtual Desktop Infrastructure
- **HA** : High Availability (Haute disponibilité)
- **DRS** : Distributed Resource Scheduler
- **HCI** : Hyper-Converged Infrastructure
- **IaaS** : Infrastructure as a Service
- **PaaS** : Platform as a Service
- **SaaS** : Software as a Service
- **IaC** : Infrastructure as Code

### ITIL et Gestion
- **ITIL** : Information Technology Infrastructure Library
- **ITSM** : IT Service Management
- **SLA** : Service Level Agreement
- **KPI** : Key Performance Indicator
- **CMDB** : Configuration Management Database
- **MCO** : Maintien en Condition Opérationnelle
- **DSI** : Direction des Systèmes d'Information
- **MSP** : Managed Service Provider
- **GLPI** : Gestionnaire Libre de Parc Informatique

### Sauvegarde et Continuité
- **PRA** : Plan de Reprise d'Activité
- **PCA** : Plan de Continuité d'Activité
- **RTO** : Recovery Time Objective (Temps de reprise)
- **RPO** : Recovery Point Objective (Perte de données acceptable)
- **PRI** : Plan de Reprise Informatique

### Développement et BDD
- **SQL** : Structured Query Language
- **NoSQL** : Not Only SQL
- **SGBD** : Système de Gestion de Base de Données
- **CRUD** : Create, Read, Update, Delete
- **API** : Application Programming Interface
- **REST** : Representational State Transfer
- **JSON** : JavaScript Object Notation
- **XML** : eXtensible Markup Language
- **HTTP** : HyperText Transfer Protocol
- **HTML** : HyperText Markup Language
- **CSS** : Cascading Style Sheets
- **DevOps** : Development + Operations
- **CI/CD** : Continuous Integration / Continuous Deployment

### Divers
- **BYOD** : Bring Your Own Device
- **MDM** : Mobile Device Management
- **IoT** : Internet of Things
- **USB** : Universal Serial Bus
- **CPU** : Central Processing Unit
- **GPU** : Graphics Processing Unit
- **RAM** : Random Access Memory
- **ROM** : Read-Only Memory
- **SSD** : Solid State Drive
- **HDD** : Hard Disk Drive
- **MAC** : Media Access Control (adresse physique)
- **IP** : Internet Protocol
- **URL** : Uniform Resource Locator
- **URI** : Uniform Resource Identifier
- **UID** : User IDentifier
- **GID** : Group IDentifier
- **PID** : Process IDentifier

---

## 🎯 QUESTIONS TYPES D'EXAMEN

### Format habituel
**"Citez les ports (numéro) et types (TCP/UDP) des protocoles suivants :"**
- FTP : 21 (TCP) + 20 (TCP data)
- SSH : 22 (TCP)
- SMTP : 25 (TCP) / 587 (TCP moderne)
- DNS : 53 (UDP/TCP)
- DHCP : 67-68 (UDP)
- HTTP : 80 (TCP)
- HTTPS : 443 (TCP)
- POP3 : 110 (TCP)
- IMAP : 143 (TCP)
- SNMP : 161-162 (UDP)
- LDAP : 389 (TCP)
- SMB : 445 (TCP)
- RDP : 3389 (TCP)

### Questions fréquentes
1. **"Quelle est la différence entre POP3 et IMAP ?"**
   → POP3 télécharge et supprime, IMAP synchronise

2. **"Quels sont les protocoles utilisés dans un VPN ?"**
   → IPsec, L2TP, OpenVPN, PPTP, SSL VPN

3. **"Quelle est la différence entre TCP et UDP ?"**
   → TCP est fiable mais lent, UDP est rapide mais sans garantie

4. **"Que signifie RGPD et CNIL ?"**
   → RGPD = protection données EU, CNIL = autorité française

5. **"Citez 3 types de Cloud ?"**
   → IaaS, PaaS, SaaS

---

## 📝 EXERCICE D'AUTO-ÉVALUATION

### Test rapide (à faire sans regarder)
1. Port HTTPS : 443_____
2. Port SSH : 22_____
3. Port DNS : 53_____
4. Port SMTP : 25, 587, 465, 2525_____
5. Port RDP : 3389_____
6. Port MySQL : 3306_____
7. Port LDAP : 389_____
8. Port SNMP : 161/162_____
9. Différence POP3/IMAP : _____
10. Différence TCP/UDP : _____

**Réponses** : 1) 443, 2) 22, 3) 53, 4) 25/587, 5) 3389, 6) 3306, 7) 389, 8) 161, 9) POP3 télécharge, IMAP synchronise, 10) TCP fiable/lent, UDP rapide/non fiable

---

## 💡 ASTUCE RÉVISION RAPIDE

### Technique de mémorisation
1. **Écris 10 fois** la liste des 20 ports principaux
2. **Récite à voix haute** ports + protocoles + utilisation
3. **Crée des flash cards** : port d'un côté, protocole de l'autre
4. **Teste-toi** tous les jours pendant 5 minutes
5. **Associe à des images mentales** (ex: 22 = paire de chaussettes SSH)

### Juste avant l'examen
📌 Relis cette page en 10 minutes chrono !

---

**🎓 Maîtriser cette fiche = Gagner facilement 10-15 points à l'examen ! 💪**
