Q1 : **Ordre des règles firewall**
**Explication** : Règles lues séquentiellement. Placer règles spécifiques en haut, génériques en bas.

### 🔐 Principe

Firewalls traitent règles **de haut en bas**, appliquent la **première correspondante**.

**Ordre prioritaire** :
```
1. Règles anti-spoofing
2. Règles spécifiques (IP:port précis)
3. Règles par réseau
4. Règles générales
5. Deny All (implicite)
```

**Exemple** :
```
[Règle 1] Allow 192.168.1.10:443 → WAN
[Règle 2] Allow 192.168.1.0/24:80 → WAN
[Règle 3] Block * → * [Log]
```

💡 **Impact** : Règles spécifiques en haut = traitement rapide.

Q2 : **SNAT (Source NAT)**
**Explication** : SNAT modifie IP source. 192.168.x.x → IP publique routeur pour accès Internet.

### 🔄 Principe

Le **Source NAT** remplace l'IP source privée par l'IP publique du routeur.

```
Avant : [PC 192.168.1.10] → [Internet] ❌
Après : [PC 192.168.1.10] → [Routeur SNAT] → [Internet avec IP publique] ✅
```

### 📊 Types de NAT

| Type | Modifie | Usage |
|------|---------|-------|
| **SNAT** | IP source | LAN → Internet |
| **DNAT** | IP destination | Port forwarding |
| **PAT** | IP + Port | Multi-clients |

### 💻 Configuration

**iptables** :
```bash
iptables -t nat -A POSTROUTING -s 192.168.1.0/24 -o eth0 -j MASQUERADE
```

**pfSense** : Firewall > NAT > Outbound (mode automatique)

Q3 : **DNAT (Destination NAT) / Port Forwarding**
**Explication** : DNAT redirige trafic entrant vers IP:port internes.

### 🎯 Principe

**Exemple** : `[Internet] → [IP Publique:80] → DNAT → [Serveur Web 192.168.1.10:80]`

**Ports courants** :

| Service | Port | Exemple |
|---------|------|---------|  
| HTTP | 80 | WAN:80 → 192.168.1.10:80 |
| HTTPS | 443 | WAN:443 → 192.168.1.10:443 |
| SSH | 2222 | WAN:2222 → 192.168.1.20:22 |

**Config iptables** :
```bash
iptables -t nat -A PREROUTING -p tcp --dport 80 \
  -j DNAT --to-destination 192.168.1.10:80
```

### 🏗️ Schéma : Architecture DNAT

```
              [Internet]
                  |
          [Routeur/Firewall]
          IP Publique : 203.0.113.5
                  |
         [LAN 192.168.1.0/24]
                  |
    +-------------+-------------+
    |                           |
[Web 192.168.1.10:80]   [SSH 192.168.1.20:22]
```

**Règle DNAT** :
- Trafic vers `203.0.113.5:80` → `192.168.1.10:80`
- Trafic vers `203.0.113.5:2222` → `192.168.1.20:22`

### 🔐 DMZ vs Redirection Simple

| Approche | Sécurité | Complexité | Usage |
|----------|----------|------------|-------|
| **DNAT vers LAN** | ⚠️ Moyen | Simple | Dev/test, petite structure |
| **DNAT vers DMZ** | ✅ Élevée | Moyenne | Production, serveurs publics |
| **Reverse Proxy** | ✅✅ Très élevée | Complexe | Multi-services, WAF intégré |

### ⚠️ Risques Sécurité : Exposition de services

**Dangers** :
- Service exposé = surface d'attaque
- Vulnérabilité serveur web → accès LAN si pas de DMZ
- Scan de ports automatique par bots

**Mitigations** :
1. ✅ Placer serveurs exposés en **DMZ**
2. ✅ Règles firewall strictes (Allow uniquement ports nécessaires)
3. ✅ IPS/IDS devant serveurs publics
4. ✅ Fail2ban / rate limiting
5. ✅ Logs et monitoring

### 💻 Configuration Multi-Services

**pfSense** :
```
Firewall > NAT > Port Forward

[Règle 1]
Interface : WAN
Protocol : TCP
Destination : WAN address
Destination port : 80
Redirect target IP : 192.168.1.10
Redirect target port : 80
Description : Web Server

[Règle 2]
Interface : WAN
Protocol : TCP
Destination : WAN address
Destination port : 2222
Redirect target IP : 192.168.1.20
Redirect target port : 22
Description : SSH Server (port custom)
```

**iptables (Linux)** :
```bash
# DNAT vers serveur web
iptables -t nat -A PREROUTING -i eth0 -p tcp --dport 80 -j DNAT --to-destination 192.168.1.10:80

# DNAT SSH avec changement de port
iptables -t nat -A PREROUTING -i eth0 -p tcp --dport 2222 -j DNAT --to-destination 192.168.1.20:22

# FORWARD permettant trafic
iptables -A FORWARD -d 192.168.1.10 -p tcp --dport 80 -j ACCEPT
iptables -A FORWARD -d 192.168.1.20 -p tcp --dport 22 -j ACCEPT
```

**Vérification** :
```bash
# Depuis Internet
curl http://203.0.113.5  # Doit afficher site sur 192.168.1.10
ssh -p 2222 user@203.0.113.5  # Se connecte à 192.168.1.20:22
```

Q4 : **DMZ (Zone Démilitarisée)**

**Explication** : DMZ = zone tampon entre Internet et LAN. Compromission serveur web n'atteint pas LAN.

### 🏗️ Architecture 3-Zones

La DMZ crée une **segmentation réseau** avec 3 zones de confiance distinctes :

```
                    [INTERNET]
                  (Zone Non-Fiable)
                         |
                    [Firewall]
                         |
            +------------+------------+
            |                         |
          [DMZ]                     [LAN]
    (Zone Semi-Fiable)        (Zone de Confiance)
            |                         |
    +-------+-------+         +-------+-------+
    |       |       |         |       |       |
  [Web]  [Mail]  [DNS]    [Users]  [DB]  [Files]
```

### 📊 Tableau : Zones et Règles Firewall

| Source | Destination | Règle | Ports Autorisés | Justification |
|--------|-------------|-------|-----------------|---------------|
| **Internet → DMZ** | ✅ Allow | 80, 443, 25, 53 | Accès services publics |
| **Internet → LAN** | ❌ Deny | Aucun | Protection réseau interne |
| **DMZ → Internet** | ✅ Allow | 80, 443, 53 | Updates, requêtes sortantes |
| **DMZ → LAN** | ❌ Deny (sauf DB) | 3306, 5432 | Isolation : compromission DMZ ≠ accès LAN |
| **LAN → DMZ** | ✅ Allow | 80, 443, 22 | Admin serveurs DMZ |
| **LAN → Internet** | ✅ Allow | Tous (proxy) | Navigation utilisateurs |

### 🖥️ Serveurs typiques en DMZ

| Serveur | Fonction | Ports Exposés | Pourquoi en DMZ ? |
|---------|----------|---------------|-------------------|
| **Serveur Web** | Site vitrine | 80, 443 | Accessible Internet → cible attaques |
| **Serveur Mail** | SMTP, IMAP | 25, 465, 993 | Reçoit mails externes → spam/malware |
| **DNS Public** | Résolution domaine | 53 | Répond requêtes Internet |
| **Proxy Reverse** | Nginx, HAProxy | 80, 443 | Filtre requêtes avant serveurs internes |
| **VPN Endpoint** | OpenVPN, IPsec | 1194, 500 | Point entrée externe |

⚠️ **Ne JAMAIS mettre en DMZ** : Contrôleurs de domaine, serveurs de fichiers internes, bases de données sensibles.

### 🛡️ Règles Firewall DMZ (pfSense)

**Interface WAN → DMZ** :
```
[Règle 1] Pass | TCP | Any | * | DMZ_WebServer | 80, 443 | "HTTP/HTTPS public"
[Règle 2] Pass | TCP | Any | * | DMZ_MailServer | 25 | "SMTP entrant"
[Règle 3] Block | * | Any | * | DMZ subnet | * | "Deny all other" [Log]
```

**Interface DMZ → LAN** :
```
[Règle 1] Pass | TCP | DMZ_WebServer | * | LAN_DBServer | 3306 | "Web → MySQL"
[Règle 2] Block | * | DMZ subnet | * | LAN subnet | * | "Isolation DMZ" [Log]
```

**Interface LAN → DMZ** :
```
[Règle 1] Pass | TCP | LAN_AdminSubnet | * | DMZ subnet | 22 | "SSH admin"
[Règle 2] Pass | * | LAN subnet | * | DMZ subnet | * | "Accès complet depuis LAN"
```

### 🔒 Différence DMZ vs LAN

| Critère | DMZ | LAN |
|---------|-----|-----|
| **Exposition Internet** | ✅ Services publics | ❌ Aucune (sauf sortie) |
| **Niveau Confiance** | ⚠️ Moyen | ✅ Élevé |
| **Accès LAN** | ❌ Bloqué (sauf exceptions) | ✅ Complet |
| **Hardening** | ✅✅ Maximal | ✅ Standard |
| **Monitoring** | ✅✅ IDS/IPS obligatoire | ✅ Recommandé |

### 💡 Scénario : Isolation après Compromission

**Sans DMZ** :
```
1. Attaquant exploite vulnérabilité serveur web
2. Serveur web dans LAN → accès direct autres machines
3. Pivot vers contrôleur domaine, serveurs fichiers
4. ❌ Compromission complète réseau
```

**Avec DMZ** :
```
1. Attaquant exploite vulnérabilité serveur web
2. Serveur web en DMZ → firewall bloque DMZ → LAN
3. Attaquant isolé dans DMZ
4. ✅ LAN protégé, temps pour réaction SOC
```

### 📡 Topologie Physique

**Firewall 3 interfaces** :
```
eth0 : WAN (Internet)
eth1 : LAN (192.168.1.0/24)
eth2 : DMZ (10.0.10.0/24)
```

**Ou VLAN sur switch manageable** :
```
VLAN 10 : LAN
VLAN 20 : DMZ
VLAN 99 : WAN
```

Q5 : **VPN Site-to-Site vs Remote Access**

**Explication** : S2S = tunnel permanent (Siège ↔ Agence). Remote = connexion ponctuelle utilisateur.

### 📊 Comparaison

| Critère | Site-to-Site | Remote Access |
|---------|--------------|---------------|
| **Usage** | Réseaux permanents | Utilisateur mobile |
| **Topologie** | Réseau ↔ Réseau | Client ↔ Réseau |
| **Connexion** | 24/7 | À la demande |
| **Équipement** | Firewall 2 côtés | Client VPN + Serveur |
| **Protocoles** | IPsec, GRE | OpenVPN, IKEv2, L2TP |
| **Exemple** | Paris ↔ Lyon | Télétravail |

**S2S** : Partage fichiers inter-sites  
**Remote** : Laptop nomade → Réseau entreprise

### 🏢 Schéma : VPN Site-to-Site

```
[SIÈGE PARIS]                           [AGENCE LYON]
192.168.1.0/24                          192.168.2.0/24
       |                                         |
  [Firewall A]                            [Firewall B]
  IP WAN: 203.0.113.5                     IP WAN: 198.51.100.20
       |                                         |
       +-------- [Internet - Tunnel IPsec] ----+

Tunnel permanent :
- Phase 1 : IKE (authentification, clé)
- Phase 2 : ESP (chiffrement trafic)
- Réseau A voit réseau B comme local
```

**Cas d'usage** :
- Partage fichiers entre sites
- Serveur centralisé accessible depuis agences
- VoIP inter-sites
- Réplication bases de données

### 💻 Schéma : VPN Remote Access

```
[UTILISATEUR NOMADE]                    [RÉSEAU ENTREPRISE]
  Laptop en WiFi café                   192.168.1.0/24
       |                                         |
 [Client OpenVPN]                        [Serveur VPN]
 IP publique: Dynamic                    IP WAN: 203.0.113.5
       |                                         |
       +-------- [Internet - Connexion SSL] ---+
                     Port 1194 UDP

Tunnel à la demande :
- User lance client VPN
- Auth : user/pass + OTP (2FA)
- IP attribuée : 10.8.0.6 (pool VPN)
- Accès ressources comme sur site
```

**Cas d'usage** :
- Télétravail
- Accès serveurs depuis domicile
- Technicien intervention externe
- Consultant temporaire

### 🔐 Configuration IPsec Site-to-Site (pfSense)

**Site A (Paris)** :
```
VPN > IPsec > Tunnels

Phase 1 :
- Remote Gateway : 198.51.100.20 (IP WAN Site B)
- Authentication : Mutual PSK
- Pre-Shared Key : SuperSecretKey123!
- Encryption : AES256-GCM
- Hash : SHA256
- DH Group : 14 (2048-bit)

Phase 2 :
- Local Network : 192.168.1.0/24
- Remote Network : 192.168.2.0/24
- Protocol : ESP
- Encryption : AES256
- Hash : SHA256
- PFS Group : 14
```

**Site B (Lyon)** : Configuration miroir (inverse local/remote)

### 🔓 Configuration OpenVPN Remote Access

**Serveur (pfSense)** :
```
VPN > OpenVPN > Servers

- Server Mode : Remote Access (SSL/TLS + User Auth)
- Protocol : UDP on port 1194
- Device Mode : tun (routed)
- IPv4 Tunnel Network : 10.8.0.0/24 (pool clients)
- IPv4 Local Network : 192.168.1.0/24 (LAN accessible)
- DNS : 192.168.1.1 (DNS interne)
- Authentication : Local User Manager + TOTP (2FA)
- Encryption : AES-256-GCM
```

**Client (Windows/Linux/Mac)** :
```ovpn
client
dev tun
proto udp
remote vpn.entreprise.com 1194
resolv-retry infinite
nobind
cipher AES-256-GCM
auth SHA256
auth-user-pass
<ca>
[Certificat CA]
</ca>
<cert>
[Certificat client]
</cert>
<key>
[Clé privée client]
</key>
```

### 🌐 Protocoles Modernes

**IKEv2** (Remote Access mobile) :
- ✅ Reconnexion rapide (changement réseau)
- ✅ Natif iOS/Android
- ✅ Performance élevée
- Usage : VPN mobile, roaming WiFi ↔ 4G

**WireGuard** (Nouveau standard) :
```bash
# Ultra-performant, code minimal
# Config serveur (wg0.conf)
[Interface]
Address = 10.0.0.1/24
ListenPort = 51820
PrivateKey = <server_private_key>

[Peer]  # Client 1
PublicKey = <client1_public_key>
AllowedIPs = 10.0.0.2/32
```

- ✅ 4000 lignes code vs 600,000 OpenVPN
- ✅ Handshake 1-RTT (ultra-rapide)
- ⚠️ Moins mature entreprise (audit en cours)

Q6 : **Protocoles VPN (L2TP/IPsec, PPTP, OpenVPN)**

**Explication** : L2TP/IPsec = chiffrement AES. PPTP obsolète. OpenVPN excellent.

### 📊 Comparaison

| Protocole | Sécurité | Vitesse | Ports | Recommandation |
|-----------|----------|---------|-------|----------------|
| **PPTP** | ❌ Faible | ✅ Rapide | TCP 1723 | ❌ NE PAS UTILISER |
| **L2TP/IPsec** | ✅ Bonne | ⚠️ Moyenne | UDP 500, 1701 | ✅ Remote Access |
| **OpenVPN** | ✅✅ Excellente | ✅ Bonne | UDP 1194 | ✅✅ Meilleur choix |
| **IKEv2/IPsec** | ✅✅ Excellente | ✅✅ Rapide | UDP 500 | ✅✅ Mobile |
| **WireGuard** | ✅✅ Excellente | ✅✅✅ Ultra | UDP 51820 | ✅✅ Futur |

**PPTP** : Vulnérable, à bannir  
**OpenVPN** : TCP 443 contourne firewalls

### 🔴 PPTP (Point-to-Point Tunneling Protocol)

**Caractéristiques** :
```
Port : TCP 1723 + GRE (protocol 47)
Chiffrement : MPPE (Microsoft Point-to-Point Encryption)
Auth : MS-CHAPv2
Création : 1999 (Microsoft)
```

**⚠️ Vulnérabilités critiques** :
- MS-CHAPv2 crackablé en 23 heures (2012)
- MPPE utilise RC4 (cassé)
- Pas d'authentification serveur forte
- GRE bloqué par certains firewalls

**Verdict** : ❌❌❌ **OBSOLÈTE - NE JAMAIS UTILISER EN PRODUCTION**

### 🟡 L2TP/IPsec (Layer 2 Tunneling Protocol)

**Caractéristiques** :
```
Ports : UDP 500 (IKE), UDP 4500 (NAT-T), UDP 1701 (L2TP)
Chiffrement : AES-256, 3DES
Auth : PSK ou Certificats X.509
Double encapsulation : L2TP (tunnel) + IPsec (chiffrement)
```

**Avantages** :
- ✅ Natif Windows/Mac/iOS/Android (pas d'app)
- ✅ Sécurité correcte avec AES-256
- ✅ Standard IETF

**Inconvénients** :
- ⚠️ Double encapsulation = overhead performance
- ⚠️ UDP 500/4500 parfois bloqués
- ⚠️ Configuration complexe (2 protocoles)

**Config Windows** :
```powershell
Add-VpnConnection -Name "Entreprise L2TP" `
  -ServerAddress "vpn.entreprise.com" `
  -TunnelType L2tp `
  -EncryptionLevel Required `
  -AuthenticationMethod MSChapv2 `
  -L2tpPsk "PreSharedKey123" `
  -PassThru
```

### 🟢 OpenVPN

**Caractéristiques** :
```
Port : UDP 1194 (ou TCP 443 si firewall restrictif)
Chiffrement : AES-256-GCM, AES-256-CBC, ChaCha20
Auth : Certificats SSL/TLS + optionnel user/pass
Librairie : OpenSSL
```

**Avantages** :
- ✅✅ Sécurité éprouvée (audité + open-source)
- ✅ TCP 443 = contourne tous firewalls
- ✅ Flexible : routed (tun) ou bridged (tap)
- ✅ Traverse NAT parfaitement
- ✅ Multi-plateforme (app dispo partout)

**Inconvénients** :
- ⚠️ Application requise (pas natif)
- ⚠️ Performance moyenne vs WireGuard/IKEv2

**Exemple config serveur** :
```bash
port 1194
proto udp
dev tun
ca ca.crt
cert server.crt
key server.key
dh dh2048.pem
server 10.8.0.0 255.255.255.0
push "route 192.168.1.0 255.255.255.0"
cipher AES-256-GCM
auth SHA256
keepalive 10 120
comp-lzo
persist-key
persist-tun
status openvpn-status.log
verb 3
```

### 🔵 IKEv2/IPsec

**Caractéristiques** :
```
Ports : UDP 500 (IKE), UDP 4500 (NAT-T)
Chiffrement : AES-256-GCM
Auth : EAP-TLS, EAP-MSCHAPv2, Certificats
Mobilité : MOBIKE (Multi-path, reconnexion auto)
```

**Avantages** :
- ✅✅ **Meilleur choix mobile** (iOS/Android natif)
- ✅ Reconnexion instantanée (WiFi ↔ 4G)
- ✅ Performance excellente
- ✅ Handshake rapide (4 messages vs 6 IKEv1)

**Config StrongSwan (Linux)** :
```bash
# /etc/ipsec.conf
conn remote-access
  left=%any
  leftsubnet=0.0.0.0/0
  leftcert=serverCert.pem
  right=%any
  rightdns=192.168.1.1
  rightsourceip=10.10.0.0/24
  auto=add
  ike=aes256-sha256-modp2048!
  esp=aes256-sha256!
```

### ⚡ WireGuard (Nouveau standard)

**Caractéristiques** :
```
Port : UDP 51820 (custom)
Chiffrement : ChaCha20-Poly1305, Curve25519
Code : 4,000 lignes (vs 600,000 OpenVPN)
Performance : 1000 Mbps+ sur hardware moderne
```

**Avantages** :
- ✅✅✅ **Ultra-rapide** (4x plus rapide OpenVPN)
- ✅✅ Code minimal = surface attaque réduite
- ✅ Intégré kernel Linux 5.6+
- ✅ Cryptographie moderne (Noise protocol)

**Config exemple** :
```ini
# Serveur wg0.conf
[Interface]
Address = 10.0.0.1/24
ListenPort = 51820
PrivateKey = [SERVER_PRIVATE_KEY]

[Peer]  # Client Alice
PublicKey = [ALICE_PUBLIC_KEY]
AllowedIPs = 10.0.0.2/32

[Peer]  # Client Bob
PublicKey = [BOB_PUBLIC_KEY]
AllowedIPs = 10.0.0.3/32
```

**Commandes** :
```bash
# Générer clés
wg genkey | tee privatekey | wg pubkey > publickey

# Activer tunnel
wg-quick up wg0

# Vérifier
wg show
```

Q7 : **VLAN (Virtual Local Area Network)**

**Explication** : VLAN sépare trafic (Admin, Users, Invités) sans switches dédiés. Sécurité + organisation.

### 🏢 Principe de Segmentation Logique

Les VLANs créent des **réseaux virtuels isolés** sur la même infrastructure physique.

```
            [Switch Physique Unique]
                      |
         +------------+------------+
         |            |            |
    [VLAN 10]    [VLAN 20]    [VLAN 30]
     Admin         Users        Guests
  192.168.10.0  192.168.20.0  192.168.30.0
         |            |            |
    Serveurs      Postes PC    WiFi Public
```

**Sans VLAN** : Tous devices dans même broadcast domain → risque sécurité
**Avec VLAN** : Isolation complète → communication uniquement via routeur/firewall

### 🏷️ Tags 802.1Q (Standard IEEE)

**Plage VLAN ID** : 1 - 4094
- VLAN 1 : VLAN par défaut (ne pas utiliser pour prod)
- VLAN 2-1001 : Normal range
- VLAN 1002-1005 : Réservés (Token Ring, FDDI)
- VLAN 1006-4094 : Extended range

**Frame 802.1Q** :
```
[Dest MAC][Src MAC][802.1Q TAG][EtherType][Data][FCS]
                      |____|___|
                      4 bytes ajoutés
                      - TPID : 0x8100
                      - PCP : Priority (QoS)
                      - DEI : Drop eligible
                      - VID : VLAN ID (12 bits)
```

### 📊 Exemples d'Organisation VLAN

| VLAN ID | Nom | Subnet | Usage | Sécurité |
|---------|-----|--------|-------|----------|
| **10** | Management | 192.168.10.0/24 | Admin switch/serveurs | 🔒 Accès restreint IT |
| **20** | Employees | 192.168.20.0/24 | Postes utilisateurs | 🛡️ Firewall standard |
| **30** | Guests | 192.168.30.0/24 | WiFi visiteurs | ⚠️ Internet uniquement |
| **40** | Servers | 192.168.40.0/24 | Serveurs production | 🔒 DMZ-like |
| **50** | VoIP | 192.168.50.0/24 | Téléphones IP | 📡 QoS prioritaire |
| **99** | Native/Unused | - | VLAN natif trunk | 🗑️ Aucun device |

### 🔌 Trunk Ports vs Access Ports

**Access Port** (Mode non-tagé) :
```
[PC] ---[Access Port VLAN 20]--- [Switch]

- Port assigné à UN SEUL VLAN
- Frames non-tagées (device ignore 802.1Q)
- Usage : connexion end-devices (PC, imprimantes)
```

**Trunk Port** (Mode tagé) :
```
[Switch A] ---[Trunk Port]--- [Switch B]
              Transporte VLAN 10, 20, 30, 40

- Transporte PLUSIEURS VLANs
- Frames tagées 802.1Q
- Usage : interconnexion switches, switch ↔ routeur
```

### 🏗️ Schéma Complet avec Inter-VLAN Routing

```
                  [Routeur/Firewall]
                   (Layer 3 device)
                          |
                    [Trunk Port]
                 (VLAN 10, 20, 30)
                          |
                     [Switch]
                          |
        +-----------------+-----------------+
        |                 |                 |
   [Port 1-8]        [Port 9-16]       [Port 17-24]
   Access VLAN 10    Access VLAN 20    Access VLAN 30
        |                 |                 |
   [Serveurs]         [Users]          [Guests]

Communication inter-VLAN :
VLAN 20 → VLAN 10 : passe par routeur avec règles firewall
```

### 🔐 Sécurité : Isolation Broadcast Domains

**Sans VLAN** :
```
[PC Admin] envoie broadcast ARP
→ Reçu par TOUS les devices (Users, Guests)
→ Risque sniffing, ARP spoofing
```

**Avec VLAN** :
```
[PC Admin VLAN 10] envoie broadcast ARP
→ Reçu UNIQUEMENT par VLAN 10
→ Isolation complète autres VLANs
```

**Avantages sécurité** :
1. ✅ **Isolation trafic** : VLAN Guests ne voit pas VLAN Servers
2. ✅ **Contrôle accès** : Inter-VLAN via firewall (règles granulaires)
3. ✅ **Réduction broadcast storms**
4. ✅ **Conformité PCI-DSS** : isoler réseau cartes bancaires

### 💻 Configuration Switch Cisco

**Création VLANs** :
```cisco
Switch(config)# vlan 10
Switch(config-vlan)# name Management
Switch(config-vlan)# exit

Switch(config)# vlan 20
Switch(config-vlan)# name Employees
Switch(config-vlan)# exit

Switch(config)# vlan 30
Switch(config-vlan)# name Guests
Switch(config-vlan)# exit
```

**Access Port (PC utilisateur)** :
```cisco
Switch(config)# interface FastEthernet0/1
Switch(config-if)# switchport mode access
Switch(config-if)# switchport access vlan 20
Switch(config-if)# spanning-tree portfast  # Accélère convergence
Switch(config-if)# exit
```

**Trunk Port (vers autre switch)** :
```cisco
Switch(config)# interface GigabitEthernet0/1
Switch(config-if)# switchport mode trunk
Switch(config-if)# switchport trunk allowed vlan 10,20,30,40
Switch(config-if)# switchport trunk native vlan 99  # VLAN non-tagé
Switch(config-if)# exit
```

### 🔀 Inter-VLAN Routing

**Méthode 1 : Router-on-a-Stick** :
```cisco
# Routeur
Router(config)# interface GigabitEthernet0/0.10
Router(config-subif)# encapsulation dot1Q 10
Router(config-subif)# ip address 192.168.10.1 255.255.255.0
Router(config-subif)# exit

Router(config)# interface GigabitEthernet0/0.20
Router(config-subif)# encapsulation dot1Q 20
Router(config-subif)# ip address 192.168.20.1 255.255.255.0
```

**Méthode 2 : Switch Layer 3 (SVI)** :
```cisco
Switch(config)# ip routing  # Activer routage

Switch(config)# interface vlan 10
Switch(config-if)# ip address 192.168.10.1 255.255.255.0
Switch(config-if)# no shutdown

Switch(config)# interface vlan 20
Switch(config-if)# ip address 192.168.20.1 255.255.255.0
Switch(config-if)# no shutdown
```

### 🔍 Vérification

```cisco
# Voir VLANs configurés
show vlan brief

# Voir trunk ports
show interfaces trunk

# Voir VLAN sur interface spécifique
show interfaces Fa0/1 switchport
```

Q8 : **Trunk VLAN (Liaison 802.1Q)**

**Explication** : Trunk = lien multi-VLANs entre switchs ou switch-routeur. Tag 802.1Q identifie VLAN.

### 🔗 Définition : Lien Transportant Plusieurs VLANs

Un **trunk port** est une liaison réseau qui **transporte simultanément plusieurs VLANs** entre équipements, en utilisant le **tagging 802.1Q**.

```
[Switch A]                            [Switch B]
   VLAN 10 ----\                   /---- VLAN 10
   VLAN 20 -----+[TRUNK PORT 802.1Q]+---- VLAN 20
   VLAN 30 ----/                   \---- VLAN 30

Une seule liaison physique transporte 3 VLANs
```

### 🏷️ Tagging 802.1Q (Standard IEEE)

**Encapsulation des frames** :
```
Frame Ethernet standard (sans VLAN) :
[Dest MAC][Src MAC][EtherType][Data][FCS]

Frame 802.1Q (avec tag VLAN) :
[Dest MAC][Src MAC][802.1Q TAG][EtherType][Data][FCS]
                      |___4 bytes___|
                      
802.1Q TAG (4 bytes) :
- TPID (16 bits) : 0x8100 (identifie 802.1Q)
- PCP (3 bits)   : Priority Code Point (QoS 0-7)
- DEI (1 bit)    : Drop Eligible Indicator
- VID (12 bits)  : VLAN ID (1-4094)
```

**Exemple pratique** :
```
PC VLAN 20 envoie frame vers Switch A
→ Switch A reçoit frame non-tagée (access port)
→ Switch A ajoute tag "VLAN 20" (0x8100 + VID=20)
→ Frame envoyée sur trunk vers Switch B
→ Switch B lit tag, route vers port access VLAN 20
→ Switch B retire tag avant envoi au PC destination
```

### 📊 Trunk vs Access Port : Comparaison

| Critère | Access Port | Trunk Port |
|---------|-------------|------------|
| **VLANs transportés** | 1 seul | Plusieurs (10, 20, 30...) |
| **Tagging** | ❌ Non-tagé (untagged) | ✅ Tagé 802.1Q |
| **Connexion** | End-device (PC, imprimante) | Switch-Switch, Switch-Routeur |
| **Config** | `switchport mode access` | `switchport mode trunk` |
| **Frame format** | Ethernet standard | Ethernet + 4 bytes tag |
| **Device awareness** | Device ignore VLANs | Device comprend 802.1Q |

### 🌐 Native VLAN (VLAN Non-Tagé)

Le **Native VLAN** est le VLAN dont les frames sont **envoyées sans tag 802.1Q** sur un trunk.

**Pourquoi ?**
- Compatibilité devices ne supportant pas 802.1Q
- Trafic de management (CDP, VTP, DTP)

**Sécurité : VLAN Hopping Attack** ⚠️
```
Attaquant envoie frame double-tagée :
[Outer Tag: VLAN 99 Native][Inner Tag: VLAN 10 Admin][Data]

1. Switch A retire outer tag (native VLAN)
2. Switch A forward frame avec inner tag vers Switch B
3. Switch B voit VLAN 10 → frame atteint réseau Admin
4. ❌ Attaquant a bypass sécurité !
```

**Mitigation** :
```cisco
# 1. Utiliser VLAN inutilisé comme native
Switch(config-if)# switchport trunk native vlan 999

# 2. Désactiver DTP (Dynamic Trunking Protocol)
Switch(config-if)# switchport nonegotiate

# 3. Shutdown ports inutilisés
Switch(config-if)# shutdown
```

### 🏗️ Schéma : Topologie Multi-Switch avec Trunks

```
                    [Switch Core - Layer 3]
                    VLAN 10, 20, 30, 40
                    IP Routing activé
                            |
                      [Trunk Port]
                   (All VLANs allowed)
                            |
            +---------------+---------------+
            |                               |
    [Trunk Port]                      [Trunk Port]
            |                               |
    [Switch A - Étage 1]              [Switch B - Étage 2]
    VLAN 10, 20                       VLAN 20, 30
            |                               |
    +-------+-------+               +-------+-------+
    |               |               |               |
[Access]      [Access]        [Access]        [Access]
VLAN 10       VLAN 20         VLAN 20         VLAN 30
    |               |               |               |
[Serveur]       [PC User]       [PC User]      [WiFi Guest]
```

### 💻 Configuration Switch Cisco

**Switch A (Distribution)** :
```cisco
# Créer VLANs
vlan 10
 name Servers
vlan 20
 name Employees
vlan 30
 name Guests
vlan 999
 name Unused_Native

# Trunk vers Switch Core
interface GigabitEthernet0/1
 description Trunk to Core Switch
 switchport mode trunk
 switchport trunk allowed vlan 10,20,30
 switchport trunk native vlan 999
 switchport nonegotiate  # Désactive DTP
 spanning-tree guard root  # Protection STP
 exit

# Trunk vers Switch B
interface GigabitEthernet0/2
 description Trunk to Switch B Floor 2
 switchport mode trunk
 switchport trunk allowed vlan 20,30
 switchport trunk native vlan 999
 exit
```

**Switch HP (syntaxe différente)** :
```
# Créer VLANs
vlan 10 name "Servers"
vlan 20 name "Employees"

# Trunk port (tagged)
interface 25
 tagged vlan 10,20,30
 untagged vlan 999  # Native VLAN
 exit
```

### 🔍 Vérification et Troubleshooting

**Commandes Cisco** :
```cisco
# Voir tous les trunks
show interfaces trunk

Port        Mode         Encapsulation  Status        Native vlan
Gi0/1       on           802.1q         trunking      999

Port        Vlans allowed on trunk
Gi0/1       10,20,30

Port        Vlans allowed and active in management domain
Gi0/1       10,20,30

# Détail interface spécifique
show interfaces GigabitEthernet0/1 switchport

Name: Gi0/1
Switchport: Enabled
Administrative Mode: trunk
Operational Mode: trunk
Administrative Trunking Encapsulation: dot1q
Operational Trunking Encapsulation: dot1q
Negotiation of Trunking: Off (nonegotiate)
Access Mode VLAN: 1 (default)
Trunking Native Mode VLAN: 999 (Unused_Native)
Trunking VLANs Enabled: 10,20,30

# Voir trafic par VLAN (nécessite SNMP/RMON)
show interfaces GigabitEthernet0/1 stats
```

### ⚠️ Problèmes Courants

**1. Native VLAN Mismatch** :
```
Switch A : native vlan 1
Switch B : native vlan 999
→ Trafic non-tagé interprété différemment
→ Logs : %CDP-4-NATIVE_VLAN_MISMATCH
```

**2. Trunk Allowed VLANs** :
```cisco
# Erreur : oublier d'ajouter VLAN au trunk
switchport trunk allowed vlan 10,20  # VLAN 30 bloqué !

# Solution : add ou all
switchport trunk allowed vlan add 30
# ou
switchport trunk allowed vlan all
```

**3. DTP Negotiation** :
```
Switch A : switchport mode trunk
Switch B : switchport mode dynamic auto
→ Trunk peut ne pas s'établir

# Solution : forcer mode trunk des 2 côtés + nonegotiate
```

### 💡 Bonnes Pratiques

1. ✅ **Native VLAN inutilisé** : vlan 999 sans devices
2. ✅ **Limiter VLANs autorisés** : pas de "all" en prod
3. ✅ **Désactiver DTP** : `switchport nonegotiate`
4. ✅ **Documenter trunks** : `description` sur chaque interface
5. ✅ **STP protection** : `spanning-tree guard root`

Q9 : **Proxy Transparent vs Standard**

**Explication** : Transparent = redirection automatique port 80/443. Standard nécessite config proxy navigateur.

### 📊 Tableau Comparatif Complet

| Critère | Proxy Standard (Explicit) | Proxy Transparent (Intercepting) |
|---------|---------------------------|----------------------------------|
| **Configuration client** | ⚠️ Manuelle (IP:port) | ✅ Aucune (automatique) |
| **Méthode** | Client envoie `CONNECT` au proxy | Firewall redirige ports 80/443 |
| **Ports** | Custom (3128, 8080) | 80, 443 interceptés |
| **Visibilité user** | ✅ User sait qu'il y a proxy | ❌ Invisible pour user |
| **Contournement** | Possible (changer config) | ❌ Difficile (règle firewall) |
| **Authentication** | ✅ Facile (Proxy-Auth header) | ⚠️ Complexe (IP-based) |
| **HTTPS** | ✅ CONNECT tunnel | ⚠️ SSL Bump requis |
| **Logs** | User-Agent, auth user | IP source uniquement |
| **Déploiement** | GPO, PAC file | Routage/NAT transparent |
| **Usage** | Entreprise avec auth | WiFi public, hotspots |

### 🔵 Proxy Standard (Explicit Proxy)

**Fonctionnement** :
```
1. Navigateur configuré : "Proxy : 192.168.1.100:3128"
2. User demande http://example.com
3. Navigateur envoie requête HTTP au proxy :
   GET http://example.com/ HTTP/1.1
   Host: example.com
   Proxy-Connection: keep-alive
4. Proxy fetch la page et retourne au client
5. Proxy log : user, URL, timestamp
```

**Schéma** :
```
[Client PC]                   [Proxy Squid]              [Internet]
192.168.1.50                  192.168.1.100:3128        example.com
      |                              |                          |
      |--[HTTP Request to Proxy]--->|                          |
      |  "GET http://example.com"   |                          |
      |                              |--[Fetch page]---------->|
      |                              |<-[Response]-------------|  
      |<-[Return to client]----------|                          |

Navigateur affiche : "Connexion via proxy 192.168.1.100:3128"
```

**Configuration navigateur** :
```
Firefox : Paramètres > Réseau > Paramètres de connexion
✅ Configuration manuelle du proxy
  Proxy HTTP : 192.168.1.100
  Port : 3128
  ☑ Utiliser ce serveur proxy pour tous les protocoles
```

**Authentification** :
```bash
# Squid config
auth_param basic program /usr/lib/squid/basic_ncsa_auth /etc/squid/passwd
acl authenticated proxy_auth REQUIRED
http_access allow authenticated

# User voit popup : "Proxy authentication required"
# Username : jdupont
# Password : ********
```

### 🟢 Proxy Transparent (Intercepting Proxy)

**Fonctionnement** :
```
1. Navigateur NON configuré (aucune config proxy)
2. User demande http://example.com
3. Firewall intercepte port 80 et redirige vers proxy :
   iptables -t nat -A PREROUTING -p tcp --dport 80 -j REDIRECT --to-port 3129
4. Proxy traite requête de manière invisible
5. User ne voit aucune différence
```

**Schéma** :
```
[Client PC]                [Firewall/Proxy]           [Internet]
192.168.1.50               192.168.1.1               example.com
      |                          |                          |
      |--[HTTP to example.com]-->|                          |
      |  DST=example.com:80      | [Interception NAT]     |
      |                          | Redirect to :3129       |
      |                          |--[Proxy fetch]--------->|
      |<-[Response]--------------|<-[Response]-------------|  

User pense communiquer directement avec example.com
```

**Configuration iptables** :
```bash
# Redirection transparente port 80
iptables -t nat -A PREROUTING -i eth1 -p tcp --dport 80 -j REDIRECT --to-port 3129

# Redirection HTTPS (nécessite SSL Bump)
iptables -t nat -A PREROUTING -i eth1 -p tcp --dport 443 -j REDIRECT --to-port 3130

# Squid config (mode transparent)
http_port 3129 intercept
https_port 3130 intercept ssl-bump cert=/etc/squid/proxy-cert.pem
```

### 📄 PAC File (Proxy Auto-Config)

**Fichier JavaScript** définissant logique de routage proxy :
```javascript
// proxy.pac
function FindProxyForURL(url, host) {
    // Pas de proxy pour réseau local
    if (isInNet(host, "192.168.0.0", "255.255.0.0")) {
        return "DIRECT";
    }
    
    // Pas de proxy pour domaines internes
    if (shExpMatch(host, "*.entreprise.local")) {
        return "DIRECT";
    }
    
    // YouTube via proxy dédié
    if (shExpMatch(host, "*.youtube.com")) {
        return "PROXY proxy-video.entreprise.com:3128";
    }
    
    // Tout le reste via proxy principal
    return "PROXY proxy.entreprise.com:3128; DIRECT";
}
```

**Déploiement** :
```
# Héberger PAC sur serveur web
http://proxy.entreprise.com/proxy.pac

# GPO Windows :
Configuration utilisateur > Stratégies > Modèles d'administration >
Composants Windows > Internet Explorer > Panneau de configuration Internet >
Page Connexions
☑ Activer configuration automatique du proxy
URL : http://proxy.entreprise.com/proxy.pac
```

### 🌐 WPAD (Web Proxy Auto-Discovery)

**Découverte automatique** sans config manuelle :
```
1. Navigateur cherche PAC file automatiquement :
   - DHCP option 252 : URL du PAC
   - DNS : http://wpad.entreprise.com/wpad.dat
   
2. Télécharge et exécute PAC file

3. ✅ Zéro config utilisateur
```

**Config DHCP (option 252)** :
```bash
# /etc/dhcp/dhcpd.conf
option wpad code 252 = text;

subnet 192.168.1.0 netmask 255.255.255.0 {
    option wpad "http://proxy.entreprise.com/proxy.pac";
}
```

**Config DNS** :
```
# Zone entreprise.com
wpad    IN    A    192.168.1.100

# Serveur web doit répondre sur http://wpad.entreprise.com/wpad.dat
```

⚠️ **Vulnérabilité WPAD** : Attaquant peut usurper serveur WPAD en réseau local (MITM)

### 📊 Cas d'Usage pour Chaque Type

**Proxy Standard** :
- ✅ Entreprise avec authentification AD
- ✅ Filtrage granulaire par user/groupe
- ✅ Logs détaillés avec identités
- ✅ Quotas par utilisateur

**Proxy Transparent** :
- ✅ WiFi public (café, aéroport)
- ✅ Hotspot captive portal
- ✅ Filtrage basique sans authentification
- ✅ Cache web transparent

**PAC File** :
- ✅ Proxy conditionnel (interne vs externe)
- ✅ Load balancing entre proxies
- ✅ Exceptions pour domaines spécifiques

**WPAD** :
- ✅ Déploiement automatisé grande entreprise
- ⚠️ Attention sécurité (MITM possible)

### 💻 Exemple Config Squid

**Mode Standard** :
```bash
# /etc/squid/squid.conf
http_port 3128

acl localnet src 192.168.1.0/24
http_access allow localnet
http_access deny all

visible_hostname proxy.entreprise.com
```

**Mode Transparent** :
```bash
http_port 3129 intercept

acl localnet src 192.168.1.0/24
http_access allow localnet
http_access deny all

# Logs incluent IP originale (X-Forwarded-For)
follow_x_forwarded_for allow localnet
```

Q10 : **Squid ACL (Access Control Lists)**

**Explication** : `dstdomain` = destination. `.facebook.com` inclut sous-domaines.

### 📝 Syntaxe de Base

**Structure Squid ACL** :
```bash
# 1. Définir ACL (critères)
acl <nom_acl> <type> <valeur>

# 2. Appliquer règle d'accès
http_access allow|deny <nom_acl>
```

**Ordre d'évaluation** : TOP-TO-BOTTOM (comme firewall)
```bash
http_access allow acl1     # ✅ Si match → autorisé, stop
http_access deny acl2      # ❌ Si match → bloqué, stop  
http_access deny all       # ❌ Défaut : tout bloquer
```

### 📊 Types d'ACL Courants

| Type ACL | Description | Exemple | Usage |
|----------|-------------|---------|-------|
| **src** | Adresse IP source | `192.168.1.0/24` | Filtrage par réseau |
| **dst** | Adresse IP destination | `203.0.113.5` | Bloquer IP spécifique |
| **dstdomain** | Domaine destination | `.facebook.com` | Bloquer sites |
| **url_regex** | Regex dans URL | `\.exe$` | Bloquer fichiers |
| **port** | Port TCP destination | `80 443` | Autoriser HTTP/HTTPS |
| **proto** | Protocole | `HTTP HTTPS` | Filtrer protocoles |
| **method** | Méthode HTTP | `CONNECT POST` | Bloquer uploads |
| **time** | Plage horaire | `MTWHF 08:00-18:00` | Horaires bureau |
| **proxy_auth** | User authentifié | `REQUIRED` | Auth obligatoire |

### 🚫 Exemples de Blocage Domaines

**Bloquer Facebook** :
```bash
# .facebook.com = facebook.com + tous sous-domaines
acl blocked_sites dstdomain .facebook.com
acl blocked_sites dstdomain .fbcdn.net
acl blocked_sites dstdomain .facebook.net
http_access deny blocked_sites
```

**Fichier externe** :
```bash
# /etc/squid/blocked_domains.txt
.facebook.com
.twitter.com
.youtube.com
.instagram.com
.tiktok.com
.reddit.com

# squid.conf
acl blocked_sites dstdomain "/etc/squid/blocked_domains.txt"
http_access deny blocked_sites
```

### 🕒 ACL par Plages Horaires

**Bloquer réseaux sociaux durant heures travail** :
```bash
# Définir plage horaire
# Format : SMTWHFA (S=Sunday, M=Monday...)
acl heures_bureau time MTWHF 08:00-18:00

# Sites bloqués
acl reseaux_sociaux dstdomain .facebook.com .twitter.com .instagram.com

# Règle : deny durant heures bureau
http_access deny reseaux_sociaux heures_bureau

# Allow en dehors heures bureau
http_access allow reseaux_sociaux
```

**Plages complexes** :
```bash
# Pause déjeuner autorisée
acl matin time MTWHF 08:00-12:00
acl aprem time MTWHF 13:30-18:00
acl heures_travail time matin aprem

http_access deny reseaux_sociaux heures_travail
```

### 🔍 ACL par IP Source

**Autoriser réseaux spécifiques** :
```bash
# Réseaux internes
acl lan_users src 192.168.1.0/24
acl lan_servers src 192.168.10.0/24
acl wifi_guests src 10.0.50.0/24

# Admins ont accès illimité
acl admin_subnet src 192.168.99.0/24
http_access allow admin_subnet

# Users limités
http_access allow lan_users

# Guests très restreints
acl safe_sites dstdomain .google.com .wikipedia.org
http_access allow wifi_guests safe_sites
http_access deny wifi_guests
```

### 📝 ACL Regex (Expressions Régulières)

**Bloquer extensions fichiers** :
```bash
# Bloquer exécutables
acl blocked_files url_regex -i \.exe$
acl blocked_files url_regex -i \.msi$
acl blocked_files url_regex -i \.bat$
acl blocked_files url_regex -i \.zip$
http_access deny blocked_files

# -i = case insensitive (ignore majuscules)
# $ = fin d'URL
```

**Bloquer mots-clés dans URL** :
```bash
acl porn_keywords url_regex -i (porn|xxx|adult|sex)
http_access deny porn_keywords

# Bloque : example.com/porn/video.html
#         site.com/adults-only/
```

### 🔐 ACL Authentification

**Auth utilisateur obligatoire** :
```bash
# Config auth NCSA (fichier passwd)
auth_param basic program /usr/lib/squid/basic_ncsa_auth /etc/squid/passwd
auth_param basic realm "Proxy Entreprise - Authentification Requise"
auth_param basic credentialsttl 2 hours

# ACL authentifiés
acl authenticated proxy_auth REQUIRED

# User spécifiques
acl admin_users proxy_auth jdupont mdupuis
http_access allow admin_users

# Tous authentifiés
http_access allow authenticated
http_access deny all
```

**Créer users** :
```bash
# Générer fichier passwd
htpasswd -c /etc/squid/passwd jdupont
# Password: ********

htpasswd /etc/squid/passwd mdupuis
# (sans -c pour ajouter user)
```

### 📋 Logs Squid : Analyse des Requêtes

**Fichier log** : `/var/log/squid/access.log`

**Format** :
```
1702987654.123    250 192.168.1.50 TCP_MISS/200 5432 GET http://example.com/ jdupont DIRECT/93.184.216.34 text/html

|              |    |             |            |    |    |                    |       |                     |
Timestamp      Durée IP_Client    Résultat    Size Méth URL                User  Destination         MIME
```

**Types de résultats** :
- `TCP_HIT` : Servi depuis cache proxy (rapide)
- `TCP_MISS` : Fetch depuis Internet (cache miss)
- `TCP_DENIED` : Bloqué par ACL
- `TCP_REFRESH_MISS` : Cache expiré, revalidation

**Requêtes bloquées** :
```bash
# Voir uniquement denials
grep TCP_DENIED /var/log/squid/access.log

# Top 10 users bloqués
grep TCP_DENIED /var/log/squid/access.log | awk '{print $8}' | sort | uniq -c | sort -rn | head -10

# Sites les plus bloqués
grep TCP_DENIED /var/log/squid/access.log | awk '{print $7}' | sort | uniq -c | sort -rn | head -10
```

### 🛡️ Filtrage par Catégories : SquidGuard

**Extension Squid** pour filtrage URL par catégories (porn, warez, malware, etc.)

**Installation** :
```bash
apt install squidguard

# Bases de données catégories (Shallalist, Toulouse)
wget http://dsi.ut-capitole.fr/blacklists/download/blacklists.tar.gz
tar -xzf blacklists.tar.gz -C /var/lib/squidguard/db/
```

**Config SquidGuard** :
```bash
# /etc/squidguard/squidGuard.conf
dbhome /var/lib/squidguard/db
logdir /var/log/squidguard

# Catégories
dest adult {
    domainlist adult/domains
    urllist adult/urls
}

dest warez {
    domainlist warez/domains
    urllist warez/urls
}

# ACL par groupe
acl {
    default {
        pass !adult !warez all
        redirect http://proxy.entreprise.com/blocked.html
    }
}
```

**Intégration Squid** :
```bash
# squid.conf
url_rewrite_program /usr/bin/squidGuard -c /etc/squidguard/squidGuard.conf
```

### 💡 Exemple Config Complète

```bash
# /etc/squid/squid.conf

# Port d'écoute
http_port 3128

# Définir réseaux
acl localnet src 192.168.1.0/24
acl admin_subnet src 192.168.1.0/26

# Ports autorisés
acl SSL_ports port 443
acl Safe_ports port 80 443 21 70 210 1025-65535
http_access deny !Safe_ports
http_access deny CONNECT !SSL_ports

# Plages horaires
acl heures_bureau time MTWHF 08:00-18:00

# Sites bloqués
acl blocked_sites dstdomain "/etc/squid/blocked_domains.txt"
http_access deny blocked_sites heures_bureau

# Extensions bloquées
acl blocked_files url_regex -i "\.exe$" "\.msi$" "\.bat$"
http_access deny blocked_files

# Admins illimités
http_access allow admin_subnet

# Users authentifiés
auth_param basic program /usr/lib/squid/basic_ncsa_auth /etc/squid/passwd
acl authenticated proxy_auth REQUIRED
http_access allow authenticated localnet

# Deny all par défaut
http_access deny all

# Cache
cache_dir ufs /var/spool/squid 10000 16 256
maximum_object_size 50 MB

# Logs
access_log /var/log/squid/access.log squid
cache_log /var/log/squid/cache.log
```

Q11 : **IDS vs IPS (Intrusion Detection/Prevention)**

**Explication** : IDS = Intrusion Detection (alerte). IPS = Prevention (action automatique).

### 📊 Tableau Comparatif Complet

| Critère | IDS (Detection) | IPS (Prevention) |
|---------|-----------------|------------------|
| **Mode** | 👁️ Passif (observation) | 🛡️ Actif (inline) |
| **Action** | 🚨 Alerte uniquement | 🚫 Bloque automatiquement |
| **Placement** | Span port / TAP (hors flux) | Inline (dans flux trafic) |
| **Latence** | ✅ Aucune | ⚠️ Légère (analyse en temps réel) |
| **Faux positifs** | ⚠️ Moins critique (juste alerte) | ❌ Critique (bloque trafic légitime) |
| **Disponibilité** | ✅ Panne IDS = pas d'impact | ❌ Panne IPS = coupure trafic |
| **Risque** | ⚠️ Attaque passe si pas de réaction | ✅ Protection automatique |
| **Réaction** | Humaine (SOC analyse) | Automatique (règles) |
| **Exemple** | Snort (NIDS mode), Suricata (IDS) | Snort (inline), Suricata (IPS), Fail2ban |

### 👁️ IDS (Intrusion Detection System)

**Fonctionnement** :
```
            [Internet]
                |
           [Firewall]
                |
          +-----+-----+
          |           |
       [IDS]       [Switch] ---> [LAN]
   (SPAN port)      
   Copie trafic
          |
    [Console SOC]
     Alertes
```

**Caractéristiques** :
- 👁️ **Observe** trafic via port mirror (SPAN) ou TAP network
- 🚨 **Génère alertes** en cas de pattern suspect
- ✅ **Pas d'impact** sur flux (panne IDS = trafic continue)
- 📈 **Analyse post-mortem** : logs, forensics

**Types d'IDS** :
1. **NIDS** (Network IDS) : Analyse trafic réseau (Snort, Suricata)
2. **HIDS** (Host IDS) : Analyse logs/fichiers serveur (OSSEC, Wazuh)

**Exemple config Snort (NIDS)** :
```bash
# /etc/snort/snort.conf
var HOME_NET 192.168.1.0/24
var EXTERNAL_NET any

# Mode IDS (sniffer + logger)
snort -c /etc/snort/snort.conf -i eth0 -A console

# Alerte uniquement, pas de blocage
```

**Règle Snort** :
```bash
# Détecter scan de ports
alert tcp any any -> $HOME_NET any (flags:S; threshold: type both, track by_src, count 20, seconds 60; msg:"Port scan détecté"; sid:1000001;)

# Génère alerte dans /var/log/snort/alert
# Aucune action bloquante
```

### 🛡️ IPS (Intrusion Prevention System)

**Fonctionnement** :
```
         [Internet]
             |
        [Firewall]
             |
          [IPS]  <--- Inline (bridge mode)
      Analyse + Bloque
             |
         [Switch]
             |
          [LAN]
```

**Caractéristiques** :
- 🛡️ **Inline** : tout trafic passe par IPS
- 🚫 **Bloque automatiquement** attaques détectées
- ⚡ **Temps réel** : protection immédiate
- ⚠️ **Point de défaillance** : panne IPS = coupure réseau (sauf bypass hardware)

**Modes IPS** :
1. **Inline Bridge** : Transparent layer 2
2. **Inline Router** : Layer 3 avec routage
3. **Passive** : Mode IDS (fallback)

**Exemple config Suricata (IPS inline)** :
```bash
# /etc/suricata/suricata.yaml
af-packet:
  - interface: eth0
    threads: 4
    cluster-id: 99
    cluster-type: cluster_flow
    defrag: yes
    
# Mode IPS : drop packets
action-order:
  - pass
  - drop  # Bloque si règle "drop"
  - reject
  - alert

# Lancer en mode IPS
sudo suricata -c /etc/suricata/suricata.yaml -i eth0 --af-packet
```

**Règle Suricata (action drop)** :
```bash
# Bloquer exploit EternalBlue (SMBv1)
drop tcp any any -> $HOME_NET 445 (msg:"SMB EternalBlue exploit attempt"; content:"|ff|SMB"; offset:4; depth:5; content:"|00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00|"; distance:0; sid:2000001; rev:1;)

# Action "drop" = packet bloqué + alerte
```

### 🔍 Méthodes de Détection

**1. Signature-Based (Pattern Matching)** :
```
Base de données signatures d'attaques connues

Avantages :
✅ Faibles faux positifs
✅ Rapide

Inconvénients :
❌ Zero-day non détectés (pas de signature)
❌ Mise à jour signatures obligatoire

Exemple : Snort rules, Suricata ET Open rules
```

**2. Anomaly-Based (Behavioral)** :
```
Apprend comportement "normal" du réseau
Détecte déviations statistiques

Avantages :
✅ Détecte zero-days
✅ Détecte attaques inconnues

Inconvénients :
❌ Beaucoup de faux positifs
❌ Phase apprentissage longue

Exemple : Darktrace, Vectra AI
```

**3. Hybrid (Signature + Anomaly)** :
```
Combinaison des deux approches

Meilleur compromis :
✅ Signatures pour attaques connues
✅ Anomalies pour menaces émergentes

Exemple : Suricata avec ML plugins
```

### ⚠️ Faux Positifs vs Faux Négatifs

**Faux Positif (False Positive)** :
```
Alerte sur trafic légitime

Exemple : Scanner vulnérabilité admin déclenché comme attaque

Impact IDS : 📈 Alerte inutile (bruit)
Impact IPS : ❌ Bloque trafic légitime (CRITIQUE)

Mitigation : Whitelisting, tuning règles
```

**Faux Négatif (False Negative)** :
```
Attaque non détectée

Exemple : Zero-day exploit, obfuscation

Impact IDS : ❌ Attaque réussit sans alerte
Impact IPS : ❌ Attaque passe et réussit

Mitigation : Signatures à jour, anomaly detection
```

### 📡 Placement Réseau

**IDS (TAP ou SPAN)** :
```
                [Internet]
                     |
                [Firewall]
                     |
                 [Switch]
                 /   |   \
               /     |     \
           [TAP]  [LAN]  [Servers]
             |
          [IDS Sensor]
       Copie trafic passive
```

**Config SPAN (Cisco)** :
```cisco
# Copier trafic port 1-10 vers port 20 (IDS)
monitor session 1 source interface Gi0/1 - 10
monitor session 1 destination interface Gi0/20

# Port 20 = IDS sensor (receive-only)
```

**IPS (Inline)** :
```
         [Internet]
              |
         [Firewall]
              |
           [IPS]  <--- Bridge transparent
        eth0  eth1
              |
          [Switch]
              |
           [LAN]

Tout trafic traverse IPS obligatoirement
```

### 📊 Cas d'Usage

| Scénario | IDS | IPS |
|----------|-----|-----|
| **SOC avec analystes 24/7** | ✅ Parfait | ⚠️ Complémentaire |
| **PME sans SOC** | ⚠️ Alertes non traitées | ✅ Protection auto |
| **Réseau haute dispo** | ✅ Pas de risque coupure | ⚠️ Bypass hardware requis |
| **Forensics / Audit** | ✅ Logs détaillés | ✅ Logs + actions |
| **Conformité PCI-DSS** | ✅ Requis | ✅ Recommandé |

**Best Practice** : **IDS + IPS** en complémentarité
- IPS en inline : bloque attaques évidentes
- IDS en passif : détection avancée, forensics

Q12 : **Suricata (Moteur IDS/IPS Open-Source)**

**Explication** : Suricata = moteur détection polyvalent (IDS passif ou IPS inline).

### ⚡ Caractéristiques Principales

**Suricata** = Moteur détection réseau **multi-thread** haute performance

```
Avancements vs Snort :
✅ Multi-threading natif (utilise tous CPU cores)
✅ GPU acceleration (optionnel)
✅ Protocol detection avancée (HTTP/2, TLS 1.3)
✅ Output JSON structuré (EVE JSON)
✅ Lua scripting intégré
✅ IDS + IPS + NSM (Network Security Monitoring)
```

### 📊 Suricata vs Snort

| Critère | Snort | Suricata |
|---------|-------|----------|
| **Threading** | ❌ Single-thread | ✅ Multi-thread natif |
| **Performance** | ⚠️ 1-2 Gbps/CPU | ✅ 10+ Gbps multi-core |
| **Protocoles** | HTTP, TLS 1.2, SMB | HTTP/2, TLS 1.3, SMB, SSH, FTP |
| **Outputs** | Logs texte, Unified2 | JSON (EVE), Syslog, PCAP |
| **IPv6** | ⚠️ Partiel | ✅ Support complet |
| **Scripting** | ❌ Limité | ✅ Lua scripts |
| **Communauté** | 💪 Cisco Talos | 💪 OISF (Open Source) |
| **Signatures** | Snort rules | Snort rules + ET rules |
| **Développement** | ⚠️ Ralenti (Snort 3 en dev) | ✅ Actif, releases fréquentes |

### 🔄 Modes de Fonctionnement

**1. IDS Mode (Passive)** :
```bash
# Analyse trafic sans bloquer
sudo suricata -c /etc/suricata/suricata.yaml -i eth0

# Ou depuis PCAP (analyse offline)
sudo suricata -c /etc/suricata/suricata.yaml -r capture.pcap
```

**2. IPS Mode (Inline)** :
```bash
# NFQueue (Linux netfilter)
sudo iptables -I FORWARD -j NFQUEUE --queue-num 0
sudo suricata -c /etc/suricata/suricata.yaml -q 0

# AF_PACKET (plus performant)
sudo suricata -c /etc/suricata/suricata.yaml --af-packet=eth0
```

**3. PCAP Mode (Forensics)** :
```bash
# Analyser fichier capture
sudo suricata -c /etc/suricata/suricata.yaml -r /tmp/suspicious.pcap -l /var/log/suricata/
```

### 💻 Configuration de Base

**Fichier principal** : `/etc/suricata/suricata.yaml`

```yaml
# Variables réseau
vars:
  address-groups:
    HOME_NET: "[192.168.1.0/24, 10.0.0.0/8]"
    EXTERNAL_NET: "!$HOME_NET"
    HTTP_SERVERS: "$HOME_NET"
    SMTP_SERVERS: "$HOME_NET"
    SQL_SERVERS: "$HOME_NET"
    DNS_SERVERS: "$HOME_NET"

# Interfaces
af-packet:
  - interface: eth0
    threads: 4  # Nombre de threads (= CPU cores)
    cluster-id: 99
    cluster-type: cluster_flow
    defrag: yes
    use-mmap: yes
    ring-size: 2048

# Mode IPS : actions possibles
action-order:
  - pass
  - drop   # Bloquer paquet
  - reject # Bloquer + RST/ICMP
  - alert  # Alerte uniquement

# Outputs
outputs:
  - eve-log:
      enabled: yes
      filetype: regular
      filename: eve.json  # JSON structuré
      types:
        - alert
        - http
        - dns
        - tls
        - files
        - ssh
        - stats

  - unified2-alert:
      enabled: no  # Legacy format

# Règles activées
rule-files:
  - suricata.rules
  - /etc/suricata/rules/emerging-threats.rules
  - /etc/suricata/rules/custom.rules
```

### 🚨 Règles (Signatures)

**Sources de règles** :
1. **Emerging Threats Open** (ET Open) - Gratuit
2. **Emerging Threats Pro** (ET Pro) - Payant, plus réactif
3. **Snort Rules** - Compatible Suricata
4. **Custom Rules** - Règles maison

**Installation ET Open** :
```bash
# Suricata-update (outil officiel)
sudo suricata-update

# Active sources ET Open par défaut
# Télécharge dans /var/lib/suricata/rules/

# Mise à jour quotidienne (cron)
sudo crontab -e
0 2 * * * /usr/bin/suricata-update && /usr/bin/suricatasc -c reload-rules
```

**Exemple de règles** :
```bash
# /etc/suricata/rules/custom.rules

# Alert sur scan de ports
alert tcp any any -> $HOME_NET any (msg:"Port scan détecté"; flags:S; threshold: type both, track by_src, count 20, seconds 60; sid:1000001; rev:1;)

# Drop exploit SMB EternalBlue
drop tcp any any -> $HOME_NET 445 (msg:"SMB EternalBlue exploit"; content:"|ff|SMB"; offset:4; depth:5; flow:to_server; sid:1000002; rev:1;)

# Alert sur beaconing C2 (Command & Control)
alert http $HOME_NET any -> $EXTERNAL_NET any (msg:"Possible C2 beaconing"; http.method; content:"POST"; threshold: type both, track by_src, count 10, seconds 60; sid:1000003; rev:1;)

# Reject brute-force SSH
reject tcp any any -> $HOME_NET 22 (msg:"SSH brute-force attempt"; flow:to_server; flags:S; threshold: type both, track by_src, count 5, seconds 60; sid:1000004; rev:1;)
```

### 📊 Logs : EVE JSON (Elasticsearch-Ready)

**Format** : `/var/log/suricata/eve.json`

**Exemple alerte** :
```json
{
  "timestamp": "2024-12-19T14:35:22.123456+0100",
  "flow_id": 1234567890,
  "event_type": "alert",
  "src_ip": "203.0.113.45",
  "src_port": 54321,
  "dest_ip": "192.168.1.50",
  "dest_port": 22,
  "proto": "TCP",
  "alert": {
    "action": "allowed",
    "gid": 1,
    "signature_id": 1000004,
    "rev": 1,
    "signature": "SSH brute-force attempt",
    "category": "Attempted Administrator Privilege Gain",
    "severity": 2
  },
  "flow": {
    "pkts_toserver": 10,
    "pkts_toclient": 0,
    "bytes_toserver": 600,
    "bytes_toclient": 0
  }
}
```

**Avantages JSON** :
- ✅ Parsing facile (jq, Python, ELK)
- ✅ Intégration SIEM directe (Splunk, ELK, Graylog)
- ✅ Champs structurés (filtrage avancé)

**Analyse logs** :
```bash
# Alertes critiques (severity 1)
jq 'select(.event_type=="alert" and .alert.severity==1)' /var/log/suricata/eve.json

# Top 10 IP sources d'alertes
jq -r 'select(.event_type=="alert") | .src_ip' /var/log/suricata/eve.json | sort | uniq -c | sort -rn | head -10

# Requêtes HTTP vers domaines suspects
jq 'select(.event_type=="http") | .http.hostname' /var/log/suricata/eve.json | grep -E '(bit\.ly|goo\.gl|t\.co)'
```

### 🔥 Intégration avec Firewall (IPS Mode)

**Linux iptables + NFQueue** :
```bash
# 1. Rediriger trafic vers Suricata
iptables -I FORWARD -j NFQUEUE --queue-num 0

# 2. Lancer Suricata en mode IPS
suricata -c /etc/suricata/suricata.yaml -q 0 --user suricata

# 3. Règles "drop" bloqueront trafic automatiquement
```

**pfSense + Suricata (Package)** :
```
1. System > Package Manager > Available Packages
2. Installer "suricata"
3. Services > Suricata > Interfaces
   - Enable Suricata on WAN interface
   - Mode : IPS (Inline)
   - Block offenders : Yes
4. Services > Suricata > Download
   - Enable Emerging Threats Open
   - Update rules
```

### 📡 Performance Multi-Cœurs

**Suricata utilise tous les CPU cores** :
```yaml
# suricata.yaml
threading:
  set-cpu-affinity: yes
  cpu-affinity:
    - management-cpu-set:
        cpu: [ 0 ]  # Core 0 pour management
    - receive-cpu-set:
        cpu: [ 1, 2 ]  # Cores 1-2 pour réception
    - worker-cpu-set:
        cpu: [ 3, 4, 5, 6 ]  # Cores 3-6 pour analyse

# Optimize AF_PACKET
af-packet:
  - interface: eth0
    threads: 4  # 1 thread par CPU core
    cluster-type: cluster_flow  # Distribution par flux
```

**Bench** :
- 1 core : ~1-2 Gbps
- 4 cores : ~5-8 Gbps
- 8 cores : ~10-15 Gbps

### 🔍 Monitoring et Statistiques

**Suricatasc (socket control)** :
```bash
# Voir stats temps réel
suricatasc -c dump-counters

# Recharger règles sans redémarrage
suricatasc -c reload-rules

# Uptime
suricatasc -c uptime
```

**Stats dans eve.json** :
```bash
# Paquets traites par seconde
jq 'select(.event_type=="stats") | .stats.capture.kernel_packets' /var/log/suricata/eve.json
```

Q13 : **SSL/TLS et Certificats**

**Explication** : SSL/TLS chiffre HTTP. Certificat prouve identité site (CA validation).

### 🔐 Principe du Chiffrement SSL/TLS

**SSL** (Secure Sockets Layer) → obsolète (SSL 3.0 vulnérable)
**TLS** (Transport Layer Security) → successeur sécurisé

```
HTTP (port 80)  + TLS  =  HTTPS (port 443)

Texte clair     Chiffrement   Communication sécurisée
```

**Objectifs TLS** :
1. 🔒 **Confidentialité** : Chiffrement (AES-256)
2. 📝 **Authentification** : Certificat vérifie identité serveur
3. 🛡️ **Intégrité** : Hashing (SHA-256) détecte modifications

### 🔑 Chiffrement Hybride : Asymétrique + Symétrique

**Asymétrique** (RSA/ECDSA) : 
```
Utilisé uniquement pour HANDSHAKE
- Clé publique (certificat) : chiffrement
- Clé privée (serveur) : déchiffrement

Avantage : Sécurité échange de clés
Inconvénient : Lent (CPU intensif)

Tailles clés :
- RSA : 2048-bit (minimum), 4096-bit (recommandé haute sécu)
- ECDSA : 256-bit (Elliptic Curve, équivalent RSA 3072-bit)
```

**Symétrique** (AES) :
```
Utilisé pour CHIFFREMENT DONNÉES (bulk data)
- Même clé pour chiffrer/déchiffrer
- Générée lors handshake TLS

Avantage : Ultra-rapide
Inconvénient : Échange clé sécurisé requis (d'où asymétrique)

Algorithmes :
- AES-128-GCM : Rapide, sécurisé
- AES-256-GCM : Très sécurisé (recommandé)
- ChaCha20-Poly1305 : Alternative (mobile)
```

### 🤝 Handshake TLS (Poignée de Main)

**Processus en 4 étapes** :
```
[Client]                                    [Serveur]
    |
    |--[1. ClientHello]---------------------->|
    |   - Versions TLS supportées             |
    |   - Cipher suites                       |
    |   - Random bytes                        |
    |                                          |
    |<-[2. ServerHello]-----------------------|
    |   - Version TLS choisie (1.3)           |
    |   - Cipher suite sélectionnée           |
    |   - [Certificate] : Certificat serveur  |
    |   - [ServerKeyExchange] : Clé publique |
    |   - [ServerHelloDone]                   |
    |                                          |
    |--[3. ClientKeyExchange]---------------->|
    |   - Pre-master secret (chiffré RSA)    |
    |   - [ChangeCipherSpec]                  |
    |   - [Finished] (chiffré)               |
    |                                          |
    |<-[4. Server Finished]--------------------|
    |   - [ChangeCipherSpec]                  |
    |   - [Finished] (chiffré)               |
    |                                          |
    |====[Session établie : AES-256-GCM]====|
    |                                          |
```

**TLS 1.3** (simplifié) :
```
Handshake réduit à 1-RTT (Round-Trip Time)
- Plus rapide (latence réduite)
- Cipher suites obsolètes retirés
- Forward Secrecy obligatoire
```

### 📜 Chaîne de Confiance : Certificats

**Hiérarchie** :
```
[Root CA]  <--- Autorité Racine (auto-signée, préinstallée navigateurs)
    |
    +--[Intermediate CA]  <--- Autorité Intermédiaire (signée par Root)
           |
           +--[End-Entity Certificate]  <--- Certificat serveur (example.com)
```

**Exemple** :
```
Root CA : DigiCert Global Root CA
  → Intermediate CA : DigiCert SHA2 Secure Server CA
      → Certificate : www.google.com
```

**Vérification navigateur** :
```
1. Navigateur reçoit certificat www.google.com
2. Vérifie signature Intermediate CA
3. Vérifie signature Root CA (trust store OS)
4. ✅ Chaîne valide → cadenas vert

Si 1 maillon cassé → ❌ "Votre connexion n'est pas privée"
```

### 🏷️ Contenu d'un Certificat X.509

```
Certificate:
    Version: 3 (0x2)
    Serial Number: 0a:3f:...:d2
    Signature Algorithm: sha256WithRSAEncryption
    Issuer: CN=DigiCert SHA2 Secure Server CA, O=DigiCert Inc
    Validity:
        Not Before: Nov  1 00:00:00 2024 GMT
        Not After : Dec  1 23:59:59 2025 GMT  <--- Expiration
    Subject: CN=www.example.com, O=Example Inc, C=US
    Subject Public Key Info:
        Public Key Algorithm: rsaEncryption
        RSA Public-Key: (2048 bit)
    X509v3 Extensions:
        X509v3 Subject Alternative Name:  <--- Domaines supplémentaires
            DNS:www.example.com, DNS:example.com, DNS:*.example.com
        X509v3 Key Usage:
            Digital Signature, Key Encipherment
        X509v3 Extended Key Usage:
            TLS Web Server Authentication
```

**Champs importants** :
- **CN (Common Name)** : Domaine principal (`www.example.com`)
- **SAN (Subject Alternative Name)** : Domaines additionnels, wildcard (`*.example.com`)
- **Validity** : Dates validité (max 398 jours depuis 2020)
- **Issuer** : CA qui a signé le certificat

### ✅ Vérification : CN vs SAN

**Méthode moderne** : **SAN uniquement**
```
Navigateurs modernes ignorent CN, vérifient SAN

Exemple certificat google.com :
SAN : DNS:*.google.com, DNS:*.youtube.com, DNS:google.com, DNS:youtube.com, ...

Requête vers "www.google.com" :
✅ Match SAN "*.google.com" → Valide
```

**Wildcard certificate** :
```
*.example.com couvre :
✅ www.example.com
✅ api.example.com
✅ blog.example.com
❌ example.com (domaine apex non couvert)
❌ sub.api.example.com (sous-sous-domaine non couvert)

Solution : Multi-domain SAN
SAN : DNS:example.com, DNS:*.example.com
```

### 📅 Versions TLS : Quoi Utiliser ?

**Timeline** :
```
SSL 2.0 (1995)  ❌ INSECURE - Désactiver
SSL 3.0 (1996)  ❌ POODLE attack - Désactiver
TLS 1.0 (1999)  ❌ BEAST, CRIME - Désactiver
TLS 1.1 (2006)  ⚠️ Déprécié depuis 2020 - Éviter
TLS 1.2 (2008)  ✅ Sécurisé - Minimum requis
TLS 1.3 (2018)  ✅✅ Recommandé - Plus rapide et sécurisé
```

**Configuration serveur** :
```nginx
# Nginx
ssl_protocols TLSv1.2 TLSv1.3;  # Uniquement 1.2 et 1.3
```

### 🔒 Cipher Suites Recommandées

**Format** : `Protocol_KeyExchange_Authentication_Encryption_MAC`

Exemple : `TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384`
- TLS : Protocole
- ECDHE : Elliptic Curve Diffie-Hellman Ephemeral (Forward Secrecy)
- RSA : Authentification certificat
- AES_256_GCM : Chiffrement symétrique
- SHA384 : Hashing intégrité

**Suites sécurisées 2024** :
```nginx
ssl_ciphers 'ECDHE-ECDSA-AES256-GCM-SHA384:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-CHACHA20-POLY1305:ECDHE-RSA-CHACHA20-POLY1305:ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES128-GCM-SHA256';
ssl_prefer_server_ciphers on;
```

**À éviter** :
- ❌ `RC4` : Cassé
- ❌ `3DES` : Faible (64-bit)
- ❌ `MD5` : Collisions
- ❌ `NULL` : Pas de chiffrement !
- ❌ `EXPORT` : Faible intentionnellement (export USA)

### 📡 Test SSL/TLS

**SSLLabs** (en ligne) :
```
https://www.ssllabs.com/ssltest/

Note A+ = Configuration optimale :
- TLS 1.2 + 1.3 uniquement
- Cipher suites sécurisées
- HSTS activé
- Certificat valide
```

**OpenSSL (ligne de commande)** :
```bash
# Tester TLS 1.3
openssl s_client -connect example.com:443 -tls1_3

# Voir certificat
openssl s_client -connect example.com:443 -showcerts

# Vérifier expiration
openssl s_client -connect example.com:443 2>/dev/null | openssl x509 -noout -dates

# Tester cipher suite spécifique
openssl s_client -connect example.com:443 -cipher ECDHE-RSA-AES256-GCM-SHA384
```


Q14 : **Port HTTPS et Ports Sécurisés**

**Explication** : 443 = HTTPS. 80 = HTTP. 22 = SSH. 8080 = HTTP alternatif.

### 🔐 Port 443 : HTTPS (HTTP Secure)

**Protocole** : HTTP over TLS/SSL
**Transport** : TCP port 443

```
HTTP (80) :  Client <--[Texte clair]--> Serveur
             ❌ MDP, cookies, données visibles

HTTPS (443) : Client <--[AES-256 chiffré]--> Serveur
              ✅ Tout chiffré (URL, headers, body)
```

**Visualisation trame** :
```
HTTP (Wireshark) :
GET /login.php HTTP/1.1
Host: example.com
Cookie: session=abc123
username=admin&password=secret123  <--- ❌ VISIBLE !

HTTPS (Wireshark) :
[Encrypted Application Data]  <--- ✅ Illisible
```

### 📊 Tableau : Ports Sécurisés vs Non-Sécurisés

| Service | Port Non-Sécurisé | Port Sécurisé | Chiffrement | Migration |
|---------|-------------------|----------------|-------------|----------|
| **HTTP** | 80/TCP | 443/TCP (HTTPS) | TLS 1.2/1.3 | ✅ Obligatoire |
| **FTP** | 21/TCP (data 20) | 990/TCP (FTPS) ou 22 (SFTP) | TLS ou SSH | ✅ Recommandé |
| **Telnet** | 23/TCP | 22/TCP (SSH) | SSH-2 | ✅✅ CRITIQUE |
| **SMTP** | 25/TCP | 465/TCP (SMTPS) ou 587+STARTTLS | TLS | ✅ Recommandé |
| **POP3** | 110/TCP | 995/TCP (POP3S) | TLS | ✅ Si utilisé |
| **IMAP** | 143/TCP | 993/TCP (IMAPS) | TLS | ✅ Recommandé |
| **LDAP** | 389/TCP | 636/TCP (LDAPS) | TLS | ✅ Active Directory |
| **MySQL** | 3306/TCP | 3306+SSL | TLS | ⚠️ Config SSL |
| **RDP** | 3389/TCP | 3389+NLA | TLS+NLA | ✅ Activer NLA |
| **VNC** | 5900/TCP | 5900+TLS ou SSH tunnel | TLS ou SSH | ⚠️ Par défaut non chiffré |

### 🔑 Ports Sécurisés Détaillés

**22/TCP - SSH (Secure Shell)** :
```
Usage : Administration serveurs à distance
Chiffrement : AES-256, ChaCha20
Auth : Mot de passe, clés SSH (recommandé), certificats

Commandes :
ssh user@server.com
scp fichier.txt user@server:/tmp/  # Copie sécurisée
sftp user@server.com  # FTP over SSH
```

**443/TCP - HTTPS** :
```
Usage : Sites web sécurisés
Chiffrement : TLS 1.2/1.3 (AES-GCM)
Auth : Certificats SSL/TLS (CA)

Redirection 80→44 :
# Apache
RewriteEngine On
RewriteCond %{HTTPS} off
RewriteRule ^(.*)$ https://%{HTTP_HOST}/$1 [R=301,L]

# Nginx
server {
    listen 80;
    return 301 https://$host$request_uri;
}
```

**465/TCP - SMTPS (SMTP over SSL)** :
```
Usage : Envoi emails chiffrés (dès connexion)
Chiffrement : TLS implicite (dès handshake)
vs 587 STARTTLS : TLS explicite (upgrade après connexion)

Thunderbird config :
Serveur sortant : smtp.example.com
Port : 465
Sécurité connexion : SSL/TLS
```

**993/TCP - IMAPS** :
```
Usage : Réception emails (garde messages serveur)
Chiffrement : TLS
vs POP3S (995) : Télécharge et supprime

Outlook config :
Serveur entrant : imap.example.com
Port : 993
Chiffrement : SSL/TLS
```

**636/TCP - LDAPS (LDAP over SSL)** :
```
Usage : Annuaire Active Directory sécurisé
Chiffrement : TLS

Active Directory :
Bind DN : CN=admin,DC=entreprise,DC=com
LDAPS : ldaps://dc.entreprise.com:636

Vérif : ldapsearch -H ldaps://dc.example.com -x -b "dc=example,dc=com"
```

### 🚫 Ports Non-Sécurisés à Désactiver

**23/TCP - Telnet** ❌❌❌ :
```
Problème : Texte clair complet (MDP visibles Wireshark)
Remplacer par : SSH (port 22)

Désactiver Telnet :
# Linux
sudo systemctl stop telnet.socket
sudo systemctl disable telnet.socket

# Cisco
Router(config)# no service telnet
Router(config)# line vty 0 4
Router(config-line)# transport input ssh
```

**21/TCP - FTP** ❌ :
```
Problème : Credentials en clair, data non chiffrée
Remplacer par : 
- SFTP (SSH File Transfer Protocol, port 22)
- FTPS (FTP over SSL, port 990)

SFTP (recommandé) :
sftp user@server.com
put fichier.txt /remote/path/
```

**80/TCP - HTTP** ⚠️ :
```
Problème : Cookies session volables, MITM
Solution : Redirection 301 vers HTTPS

HSTS (HTTP Strict Transport Security) :
Strict-Transport-Security: max-age=31536000; includeSubDomains; preload

→ Navigateur force HTTPS même si user tape http://
```

### 🔄 Redirection HTTP → HTTPS

**Apache** :
```apache
<VirtualHost *:80>
    ServerName example.com
    Redirect permanent / https://example.com/
</VirtualHost>

<VirtualHost *:443>
    ServerName example.com
    SSLEngine on
    SSLCertificateFile /etc/ssl/certs/example.com.crt
    SSLCertificateKeyFile /etc/ssl/private/example.com.key
    SSLCertificateChainFile /etc/ssl/certs/intermediate.crt
    
    # HSTS
    Header always set Strict-Transport-Security "max-age=31536000; includeSubDomains"
</VirtualHost>
```

**Nginx** :
```nginx
server {
    listen 80;
    server_name example.com;
    return 301 https://$server_name$request_uri;
}

server {
    listen 443 ssl http2;
    server_name example.com;
    
    ssl_certificate /etc/ssl/certs/example.com.crt;
    ssl_certificate_key /etc/ssl/private/example.com.key;
    ssl_protocols TLSv1.2 TLSv1.3;
    ssl_ciphers HIGH:!aNULL:!MD5;
    
    # HSTS
    add_header Strict-Transport-Security "max-age=31536000; includeSubDomains; preload" always;
}
```

### 🔍 Proxy HTTPS : Déchiffrement SSL

**Problème** : Proxy ne peut analyser contenu HTTPS chiffré

**Solution : SSL Interception (MITM légitime)** :
```
[Client] <--TLS--> [Proxy] <--TLS--> [Serveur]
                     |
              Déchiffre et analyse
              
1. Client établit TLS avec Proxy (pas serveur final)
2. Proxy présente certificat signé par CA interne
3. Proxy déchiffre, analyse, rechiffre vers serveur
4. Client doit avoir CA proxy dans trust store
```

**Squid SSL Bump** :
```bash
# Générer CA proxy
openssl req -new -newkey rsa:4096 -days 3650 -nodes -x509 \
  -keyout squid-ca.key -out squid-ca.crt

# squid.conf
https_port 3130 intercept ssl-bump \
  cert=/etc/squid/squid-ca.crt \
  key=/etc/squid/squid-ca.key

sslcrtd_program /usr/lib/squid/security_file_certgen -s /var/lib/squid/ssl_db -M 4MB

acl step1 at_step SslBump1
ssl_bump peek step1
ssl_bump bump all
```

⚠️ **Attention légale** : Informer utilisateurs de l'interception SSL !

### 📊 Ports Alternatifs

**8080/TCP - HTTP alternatif** :
```
Usage : Dev/test, proxies, serveurs app
Exemple : Tomcat, Jenkins, proxy Squid

Non-standard, souvent bloqué par firewalls entreprise
```

**8443/TCP - HTTPS alternatif** :
```
Usage : HTTPS non-root (>1024), admin interfaces
Exemple : pfSense WebGUI, vCenter, Proxmox
```

**2222/TCP - SSH alternatif** :
```
Usage : Éviter bots scannant port 22
Sécurité par obscurité (faible)

ssh -p 2222 user@server.com
```

Q15 : **Let's Encrypt (Autorité de Certification Gratuite)**

**Explication** : Let's Encrypt = CA gratuite, automatisée. Certbot renouvelle avant expiration.

### 🌐 Let's Encrypt : Révolution SSL/TLS

**Avant Let's Encrypt** (pré-2015) :
```
💰 Certificats payants : 50-500€/an
🕑 Processus manuel : CSR, validation, installation
⏳ Renouvellement annuel fastidieux
→ Adoption HTTPS limitée (coût, complexité)
```

**Avec Let's Encrypt** :
```
✅ 100% gratuit
✅ Automatisation complète (ACME protocol)
✅ Renouvellement auto tous les 60-80 jours
✅ Wildcard certificates supportés
✅ Multi-domain (SAN) inclus

→ 90% du web est maintenant HTTPS (vs 40% en 2015)
```

### 🤖 Certbot : Client ACME Officiel

**ACME** = Automatic Certificate Management Environment (protocole)

**Installation** :
```bash
# Debian/Ubuntu
sudo apt install certbot python3-certbot-apache python3-certbot-nginx

# RHEL/CentOS
sudo yum install certbot python3-certbot-apache python3-certbot-nginx

# Snap (universel)
sudo snap install --classic certbot
sudo ln -s /snap/bin/certbot /usr/bin/certbot
```

**Obtenir certificat** :
```bash
# Apache (config auto)
sudo certbot --apache -d example.com -d www.example.com

# Nginx (config auto)
sudo certbot --nginx -d example.com -d www.example.com

# Standalone (serveur temp sur port 80)
sudo certbot certonly --standalone -d example.com

# Webroot (serveur déjà actif)
sudo certbot certonly --webroot -w /var/www/html -d example.com
```

### 🔑 Challenges : Validation Domaine

Let's Encrypt doit **prouver que vous contrôlez le domaine**.

**1. HTTP-01 Challenge** (plus courant) :
```
Processus :
1. Certbot demande certificat pour example.com
2. Let's Encrypt génère token aléatoire : "abc123"
3. Certbot crée fichier : 
   http://example.com/.well-known/acme-challenge/abc123
4. Let's Encrypt fetch fichier via HTTP port 80
5. ✅ Validation OK → Certificat délivré

Requis :
✅ Port 80 ouvert (Internet → serveur)
✅ Domaine pointe vers IP serveur (DNS)
❌ Ne fonctionne pas pour *.example.com (wildcard)
```

**2. DNS-01 Challenge** (wildcard) :
```
Processus :
1. Certbot demande *.example.com (wildcard)
2. Let's Encrypt demande création enregistrement TXT DNS :
   _acme-challenge.example.com TXT "random_token"
3. Certbot ajoute enregistrement via API DNS provider
4. Let's Encrypt vérifie DNS
5. ✅ Validation OK → Certificat wildcard délivré

Requis :
✅ API DNS (Cloudflare, Route53, OVH...)
✅ Pas besoin port 80 ouvert
✅ Fonctionne pour *.example.com

Exemple Cloudflare :
sudo certbot certonly --dns-cloudflare \
  --dns-cloudflare-credentials ~/.secrets/cloudflare.ini \
  -d example.com -d *.example.com
```

**3. TLS-ALPN-01 Challenge** (rare) :
```
Validation via port 443 (si port 80 indisponible)
Utilisé rarement (support limité)
```

### 📅 Renouvellement Automatique

**Durée validité** : 90 jours (vs 1-2 ans certificats traditionnels)

**Pourquoi 90 jours ?**
```
✅ Force automatisation (pas de manuel)
✅ Limite exposition si clé compromise
✅ Réduit impact révocation
```

**Renouvellement auto (cron)** :
```bash
# Certbot installe automatiquement cron/systemd timer

# Cron : /etc/cron.d/certbot
0 */12 * * * root certbot renew --quiet --post-hook "systemctl reload nginx"

# Systemd timer
systemctl list-timers | grep certbot
certbot.timer  # Run 2x/jour

# Test renouvellement (dry-run)
sudo certbot renew --dry-run
```

**Hooks** :
```bash
# Recharger serveur web après renouvellement
sudo certbot renew --post-hook "systemctl reload apache2"
sudo certbot renew --post-hook "systemctl reload nginx"

# Pre-hook (avant renouvellement)
--pre-hook "systemctl stop nginx"

# Deploy-hook (uniquement si renouvelé)
--deploy-hook "systemctl reload haproxy"
```

### 🌟 Wildcard Certificates

**Certificat unique pour tous sous-domaines** :
```
Certificat : *.example.com

Couvre :
✅ www.example.com
✅ api.example.com
✅ blog.example.com
✅ app.example.com
❌ example.com (apex domain)
❌ sub.api.example.com (sous-sous-domaine)

Solution complète :
-d example.com -d *.example.com
```

**Obtention wildcard** (DNS-01 obligatoire) :
```bash
# CloudFlare
sudo certbot certonly --dns-cloudflare \
  --dns-cloudflare-credentials /root/.secrets/cloudflare.ini \
  -d example.com -d *.example.com

# OVH
sudo certbot certonly --dns-ovh \
  --dns-ovh-credentials /root/.secrets/ovh.ini \
  -d example.com -d *.example.com

# AWS Route53
sudo certbot certonly --dns-route53 \
  -d example.com -d *.example.com
```

**Config Cloudflare API** (`~/.secrets/cloudflare.ini`) :
```ini
dns_cloudflare_api_token = YOUR_API_TOKEN_HERE

# Permissions requises :
# Zone.Zone:Read, Zone.DNS:Edit
```

### 💻 Configuration Apache avec Let's Encrypt

**Avant Certbot** :
```apache
<VirtualHost *:80>
    ServerName example.com
    DocumentRoot /var/www/html
</VirtualHost>
```

**Après `certbot --apache`** :
```apache
<VirtualHost *:80>
    ServerName example.com
    RewriteEngine on
    RewriteRule ^ https://%{SERVER_NAME}%{REQUEST_URI} [END,NE,R=permanent]
</VirtualHost>

<VirtualHost *:443>
    ServerName example.com
    DocumentRoot /var/www/html
    
    SSLEngine on
    SSLCertificateFile /etc/letsencrypt/live/example.com/fullchain.pem
    SSLCertificateKeyFile /etc/letsencrypt/live/example.com/privkey.pem
    Include /etc/letsencrypt/options-ssl-apache.conf
</VirtualHost>
```

### 🔧 Configuration Nginx avec Let's Encrypt

```nginx
server {
    listen 80;
    server_name example.com www.example.com;
    
    # Redirection HTTPS
    return 301 https://$server_name$request_uri;
}

server {
    listen 443 ssl http2;
    server_name example.com www.example.com;
    
    # Certificats Let's Encrypt
    ssl_certificate /etc/letsencrypt/live/example.com/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/example.com/privkey.pem;
    
    # Options SSL (générées par Certbot)
    include /etc/letsencrypt/options-ssl-nginx.conf;
    ssl_dhparam /etc/letsencrypt/ssl-dhparams.pem;
    
    # HSTS
    add_header Strict-Transport-Security "max-age=31536000; includeSubDomains; preload" always;
    
    root /var/www/html;
    index index.html;
}
```

### 📜 Structure Fichiers Let's Encrypt

```
/etc/letsencrypt/
├── live/
│   └── example.com/
│       ├── fullchain.pem  → Certificat + Intermediate CA (utiliser celui-ci)
│       ├── privkey.pem    → Clé privée (GARDER SECRET)
│       ├── cert.pem       → Certificat seul (sans chain)
│       └── chain.pem      → Intermediate CA uniquement
├── archive/
│   └── example.com/   → Versions historiques (cert1.pem, cert2.pem...)
├── renewal/
│   └── example.com.conf  → Config renouvellement
└── accounts/
    └── acme-v02.api.letsencrypt.org/  → Clés compte ACME
```

**Permissions** :
```bash
sudo chmod 600 /etc/letsencrypt/live/example.com/privkey.pem
sudo chown root:root /etc/letsencrypt/live/example.com/*
```

### 🔍 Gestion Certificats

```bash
# Lister certificats actifs
sudo certbot certificates

Certificate Name: example.com
  Domains: example.com www.example.com
  Expiry Date: 2025-03-19 12:34:56+00:00 (VALID: 89 days)
  Certificate Path: /etc/letsencrypt/live/example.com/fullchain.pem
  Private Key Path: /etc/letsencrypt/live/example.com/privkey.pem

# Renouveler manuellement
sudo certbot renew

# Renouveler domaine spécifique
sudo certbot renew --cert-name example.com

# Supprimer certificat
sudo certbot delete --cert-name example.com

# Révoquer certificat (si clé compromise)
sudo certbot revoke --cert-path /etc/letsencrypt/live/example.com/cert.pem
```

### ⚠️ Limites Rate Limiting

```
Certificats par domaine : 50 / semaine
Renouvellements : Illimités
Duplicates : 5 / semaine
Comptes par IP : 10 / 3h
Validations échouées : 5 / compte / hostname / heure

→ Utiliser --dry-run pour tester sans consommer quota
```

### 💡 Alternatives Certbot

**acme.sh** (plus léger) :
```bash
curl https://get.acme.sh | sh
acme.sh --issue -d example.com --nginx
acme.sh --issue -d example.com -d *.example.com --dns dns_cf
```

**Caddy** (serveur web avec HTTPS auto) :
```
Caddyfile :
example.com {
    root * /var/www/html
    file_server
}

# HTTPS auto avec Let's Encrypt (zéro config !)
```

Q16 : **SSH vs Telnet (Administration Distance)**

**Explication** : SSH chiffre. Telnet = clair (MDP visible). Port 22 vs 23.

### 📊 Tableau Comparatif Sécurité

| Critère | Telnet | SSH |
|---------|--------|-----|
| **Port** | 23/TCP | 22/TCP |
| **Chiffrement** | ❌ Aucun (texte clair) | ✅ AES-256, ChaCha20 |
| **Authentification** | ⚠️ Mot de passe uniquement (visible) | ✅ MDP, clés SSH, certificats, 2FA |
| **Intégrité** | ❌ Aucune | ✅ HMAC-SHA256 |
| **Confidentialité** | ❌ Tout visible (Wireshark) | ✅ Tout chiffré |
| **Sécurité** | ❌❌❌ CRITIQUE | ✅✅ Excellente |
| **Année création** | 1969 | 1995 |
| **Status** | 🚫 OBSOLÈTE - Ne PAS utiliser | ✅ Standard industrie |

### ❌ Telnet : Vulnérabilité Critique

**Capture Wireshark** :
```
Telnet session (lisible par attaquant MITM) :

login: admin
Password: SuperSecret123!  <--- ❌ VISIBLE EN CLAIR

# whoami
root
# cat /etc/shadow  <--- Toutes commandes visibles
```

**Pourquoi dangereux** :
```
1. ❌ Credentials volables (sniffing réseau)
2. ❌ Session hijacking (injection commandes)
3. ❌ Replay attacks
4. ❌ Aucune authentification serveur (MITM facile)
5. ❌ Données sensibles lisibles
```

**Désactiver Telnet** :
```bash
# Linux
sudo systemctl stop telnet.socket
sudo systemctl disable telnet.socket
sudo apt remove telnetd

# Cisco
Router(config)# no service telnet
Router(config)# line vty 0 4
Router(config-line)# transport input ssh
Router(config-line)# transport output ssh

# Windows
# Panneau de configuration > Programmes > Activer/Désactiver fonctionnalités Windows
# ❌ Décocher "Client Telnet"
```

### ✅ SSH : Sécurité Maximale

**Fonctionnement** :
```
[Client]                          [Serveur SSH]
   |
   |--[1. TCP SYN]------------------>|
   |<-[2. SYN-ACK]-------------------|
   |                                  |
   |--[3. Protocol Exchange]-------->|
   |   SSH-2.0-OpenSSH_8.9           |
   |                                  |
   |<-[4. Key Exchange (DH)]---------|  
   |   Établit clé session AES-256   |
   |                                  |
   |--[5. Authentication]----------->|
   |   (Password ou Public Key)      |
   |                                  |
   |<-[6. Session chiffrée]----------|  
   |====[AES-256-GCM encrypted]======|
```

**Chiffrement** :
```bash
# Algorithmes supportés
ssh -Q cipher
aes128-gcm@openssh.com
aes256-gcm@openssh.com  ← Recommandé
chacha20-poly1305@openssh.com  ← Mobile

# Désactiver algorithmes faibles
# /etc/ssh/sshd_config
Ciphers aes256-gcm@openssh.com,chacha20-poly1305@openssh.com
MACs hmac-sha2-512-etm@openssh.com,hmac-sha2-256-etm@openssh.com
KexAlgorithms curve25519-sha256,curve25519-sha256@libssh.org
```

### 🔑 Authentification SSH : Clés Publiques

**Génération clés** :
```bash
# RSA 4096-bit (traditionnel)
ssh-keygen -t rsa -b 4096 -C "admin@example.com"

# Ed25519 (recommandé, plus court et rapide)
ssh-keygen -t ed25519 -C "admin@example.com"

Fichiers générés :
~/.ssh/id_ed25519      # Clé privée (GARDER SECRET)
~/.ssh/id_ed25519.pub  # Clé publique (copier sur serveur)
```

**Déploiement clé** :
```bash
# Méthode 1 : ssh-copy-id
ssh-copy-id user@server.com

# Méthode 2 : Manuelle
cat ~/.ssh/id_ed25519.pub | ssh user@server.com "mkdir -p ~/.ssh && cat >> ~/.ssh/authorized_keys"

# Permissions critiques
chmod 700 ~/.ssh
chmod 600 ~/.ssh/authorized_keys
```

**Connexion sans mot de passe** :
```bash
ssh user@server.com
# ✅ Connexion immédiate (clé privée utilisée)
```

**Passphrase protection** :
```bash
# Protéger clé privée avec passphrase
ssh-keygen -p -f ~/.ssh/id_ed25519

# ssh-agent (évite retaper passphrase)
eval $(ssh-agent)
ssh-add ~/.ssh/id_ed25519
# Enter passphrase: ********
# Identity added: ~/.ssh/id_ed25519

# ssh-agent garde clé en mémoire pour session
```

### 🔒 Hardening SSH Server

**Configuration `/etc/ssh/sshd_config`** :
```bash
# Désactiver root login
PermitRootLogin no

# Désactiver password auth (clés uniquement)
PasswordAuthentication no
PubkeyAuthentication yes

# Désactiver empty passwords
PermitEmptyPasswords no

# Changer port (security by obscurity)
Port 2222

# Limiter users autorisés
AllowUsers admin deployer
# Ou AllowGroups ssh-users

# Limiter tentatives auth
MaxAuthTries 3
LoginGraceTime 30

# Désactiver X11 forwarding si inutile
X11Forwarding no

# Protocole SSH v2 uniquement
Protocol 2

# Cipher suites sécurisées
Ciphers aes256-gcm@openssh.com,chacha20-poly1305@openssh.com
MACs hmac-sha2-512-etm@openssh.com
KexAlgorithms curve25519-sha256

# Banner legal
Banner /etc/ssh/banner.txt
```

**Redémarrer SSH** :
```bash
sudo systemctl restart sshd

# Tester config avant restart (évite lockout)
sudo sshd -t
```

### 🔐 SSH Tunneling : Port Forwarding

**1. Local Port Forwarding (-L)** :
```bash
# Accéder service distant via tunnel SSH
ssh -L 8080:localhost:80 user@server.com

# Maintenant : http://localhost:8080 → server.com:80 (chiffré)

Cas d'usage : Base de données distante
ssh -L 3306:localhost:3306 user@dbserver.com
mysql -h 127.0.0.1 -P 3306 -u root -p
# Connexion MySQL chiffrée via SSH
```

**2. Remote Port Forwarding (-R)** :
```bash
# Exposer service local vers serveur distant
ssh -R 8080:localhost:80 user@server.com

# Sur server.com : http://localhost:8080 → votre localhost:80

Cas d'usage : Demo app locale à client distant
```

**3. Dynamic Port Forwarding (-D) : SOCKS Proxy** :
```bash
# Créer proxy SOCKS
ssh -D 1080 user@server.com

# Configurer navigateur : SOCKS5 proxy localhost:1080
# Tout le trafic web passe par server.com (chiffré)

Cas d'usage : Contourner restrictions réseau
```

**Background tunnel** :
```bash
# -N : No command (tunnel uniquement)
# -f : Background
ssh -fN -L 3306:localhost:3306 user@dbserver.com

# Tuer tunnel
pkill -f "ssh -fN -L 3306"
```

### 🛡️ Fail2ban : Protection Brute-Force

**Détecte tentatives SSH échouées et bannit IP** :
```bash
# Installation
sudo apt install fail2ban

# /etc/fail2ban/jail.local
[sshd]
enabled = true
port = 22
filter = sshd
logpath = /var/log/auth.log
maxretry = 3
bantime = 3600
findtime = 600

# Redémarrer
sudo systemctl restart fail2ban

# Voir IPs bannies
sudo fail2ban-client status sshd

Status for the jail: sshd
|- Filter
|  |- Currently failed: 2
|  `- Total failed:     45
`- Actions
   |- Currently banned: 3
   |- Total banned:     12
   `- Banned IP list:   203.0.113.45 198.51.100.78 192.0.2.123

# Débannir IP
sudo fail2ban-client set sshd unbanip 203.0.113.45
```

### 📊 Logs SSH

```bash
# Voir connexions SSH
sudo tail -f /var/log/auth.log

# Connexions réussies
sudo grep "Accepted" /var/log/auth.log
Dec 19 14:30:15 server sshd[1234]: Accepted publickey for admin from 192.168.1.50 port 54321 ssh2: ED25519 SHA256:abcd1234...

# Tentatives échouées
sudo grep "Failed" /var/log/auth.log
Dec 19 14:25:10 server sshd[1200]: Failed password for invalid user root from 203.0.113.45 port 12345 ssh2

# Sessions actives
who
admin    pts/0        2024-12-19 14:30 (192.168.1.50)

# Détail session
w
 14:35:22 up 10 days,  3:24,  1 user,  load average: 0.00, 0.01, 0.05
USER     TTY      FROM             LOGIN@   IDLE   JCPU   PCPU WHAT
admin    pts/0    192.168.1.50     14:30    0.00s  0.05s  0.00s w
```

### 💡 Commandes SSH Utiles

```bash
# Copie fichiers sécurisée
scp fichier.txt user@server:/tmp/
scp -r dossier/ user@server:/opt/
scp user@server:/etc/config.conf ./

# SFTP (FTP over SSH)
sftp user@server.com
sftp> put local_file.txt
sftp> get remote_file.txt
sftp> ls
sftp> exit

# Exécuter commande distante
ssh user@server.com "df -h"
ssh user@server.com "sudo systemctl restart nginx"

# Connexion avec clé spécifique
ssh -i ~/.ssh/custom_key user@server.com

# Verbose (debug)
ssh -vvv user@server.com

# Keepalive (évite timeout)
ssh -o ServerAliveInterval=60 user@server.com

# Config ~/.ssh/config
Host myserver
    HostName server.example.com
    User admin
    Port 2222
    IdentityFile ~/.ssh/id_ed25519
    ServerAliveInterval 60

# Connexion simplifiée
ssh myserver
```

Q17 : **SNMP v1/v2c vs v3 (Simple Network Management Protocol)**

**Explication** : v1/v2c = community string clair. v3 = auth (MD5/SHA) + chiffrement (DES/AES).

### 📊 Tableau Comparatif Versions SNMP

| Critère | SNMPv1 | SNMPv2c | SNMPv3 |
|---------|--------|---------|--------|
| **Année** | 1988 | 1996 | 2002 |
| **Authentification** | Community string | Community string | User/Pass + MD5/SHA |
| **Chiffrement** | ❌ Aucun | ❌ Aucun | ✅ DES/AES-128/AES-256 |
| **Intégrité** | ❌ Aucune | ❌ Aucune | ✅ HMAC |
| **Security Level** | ❌ Très faible | ❌ Très faible | ✅ Élevée |
| **Bulk requests** | ❌ GetNext only | ✅ GetBulk | ✅ GetBulk |
| **Informational traps** | ❌ Non | ✅ Inform | ✅ Inform |
| **Usage recommandé** | 🚫 Obsolète | ⚠️ LAN interne uniquement | ✅✅ Production |

### ❌ SNMPv1/v2c : Vulnérabilités Critiques

**Community Strings en clair** :
```
Requête SNMP (Wireshark) :

SNMP GET Request
  Version: v2c
  Community: public  <--- ❌ VISIBLE EN CLAIR
  PDU: GetRequest
    OID: 1.3.6.1.2.1.1.1.0 (sysDescr)

Réponse :
  Community: public
  PDU: Response
    Value: "Cisco IOS Software, Version 15.2"
```

**Community par défaut** :
```
Lecture (read-only) :
- public   <--- 90% des équipements
- guest
- snmp

Écriture (read-write) :
- private  <--- TRÈS DANGEREUX si exposé
- write
- admin

⚠️ Attaquant avec community "private" peut :
- Modifier configuration équipement
- Désactiver interfaces
- Changer mots de passe
- Créer DoS
```

**Scan SNMP** :
```bash
# Nmap : Découverte community strings
nmap -sU -p 161 --script snmp-brute 192.168.1.0/24

# Onesixtyone (brute-force community)
onesixtyone -c /usr/share/wordlists/snmp-community.txt 192.168.1.0/24

Scanning 256 hosts, 1 communities
192.168.1.1 [public] Cisco IOS Software
192.168.1.10 [public] HP ProCurve Switch
192.168.1.50 [private] Cisco ASA Firewall  <--- ❌ RW access !
```

### ✅ SNMPv3 : Sécurité Entreprise

**3 Niveaux de sécurité** :

```
1. noAuthNoPriv
   ❌ Pas d'auth, pas de chiffrement
   = Équivalent v1/v2c
   
2. authNoPriv
   ✅ Authentification (MD5 ou SHA)
   ❌ Pas de chiffrement données
   Usage : Monitoring non-sensible
   
3. authPriv (Recommandé)
   ✅ Authentification (MD5/SHA/SHA-256/SHA-512)
   ✅ Chiffrement (DES/AES-128/AES-192/AES-256)
   Usage : Production
```

**Paramètres SNMPv3** :
```
User Security Name : snmpadmin
Auth Protocol : SHA-256  (ou MD5, SHA, SHA-512)
Auth Password : AuthPass123!  (min 8 caractères)
Priv Protocol : AES-256  (ou DES, AES-128, AES-192)
Priv Password : PrivPass456!  (min 8 caractères)

Context Name : (optionnel, pour multi-tenancy)
Engine ID : Unique identifier (auto-généré)
```

### 🔧 Configuration SNMPv3

**Linux (Net-SNMP)** :
```bash
# Installation
sudo apt install snmpd snmp

# Créer utilisateur SNMPv3
sudo net-snmp-create-v3-user -ro -A AuthPass123! -X PrivPass456! -a SHA-256 -x AES snmpadmin

# /etc/snmp/snmpd.conf
# Écouter sur toutes interfaces
agentaddress udp:161

# User créé automatiquement dans /var/lib/snmp/snmpd.conf
# (ne pas éditer manuellement)

# Access control
rouser snmpadmin authpriv

# System info
sysLocation "Datacenter Paris - Rack 12"
sysContact "admin@example.com"

# Redémarrer
sudo systemctl restart snmpd
```

**Cisco IOS** :
```cisco
# Créer groupe SNMPv3
snmp-server group ADMIN_GROUP v3 priv

# Créer utilisateur
snmp-server user snmpadmin ADMIN_GROUP v3 auth sha AuthPass123! priv aes 256 PrivPass456!

# ACL limiter sources
access-list 10 permit 192.168.1.0 0.0.0.255
snmp-server group ADMIN_GROUP v3 priv access 10

# Désactiver v1/v2c
no snmp-server community public
no snmp-server community private

# Informations système
snmp-server location "Paris Datacenter"
snmp-server contact "admin@example.com"

# Traps
snmp-server enable traps
snmp-server host 192.168.1.100 version 3 priv snmpadmin
```

**HP/Aruba Switch** :
```
# SNMPv3 user
snmpv3 user snmpadmin auth sha AuthPass123! priv aes PrivPass456!

# Restrict access
snmpv3 restricted-access

# Disable v1/v2c
no snmp-server community public
```

### 📡 OID (Object Identifier)

**Structure hiérarchique** :
```
OID = Adresse unique pour chaque métrique

Format : 1.3.6.1.2.1.1.1.0
         |_|_|_|_|_|_|_|_|
          ISO > ORG > DOD > Internet > mgmt > mib-2 > system > sysDescr > .0

OID racine :
.1.3.6.1 = iso.org.dod.internet

OID MIB-2 standard :
.1.3.6.1.2.1.1   = System (sysDescr, sysUptime, sysContact)
.1.3.6.1.2.1.2   = Interfaces (ifDescr, ifSpeed, ifInOctets)
.1.3.6.1.2.1.4   = IP (ipForwarding, ipInReceives)
.1.3.6.1.2.1.6   = TCP (tcpActiveOpens, tcpCurrEstab)
.1.3.6.1.2.1.25  = Host Resources (CPU, RAM, disques)

OID Vendor-specific :
.1.3.6.1.4.1.9   = Cisco
.1.3.6.1.4.1.11  = HP
.1.3.6.1.4.1.2021 = UCD-SNMP (Linux)
```

**OID utiles** :
```
sysDescr      .1.3.6.1.2.1.1.1.0   (Description équipement)
sysUpTime     .1.3.6.1.2.1.1.3.0   (Uptime en centisecondes)
sysContact    .1.3.6.1.2.1.1.4.0   (Contact admin)
sysName       .1.3.6.1.2.1.1.5.0   (Hostname)
sysLocation   .1.3.6.1.2.1.1.6.0   (Localisation)

ifDescr       .1.3.6.1.2.1.2.2.1.2  (Nom interfaces)
ifInOctets    .1.3.6.1.2.1.2.2.1.10 (Octets reçus)
ifOutOctets   .1.3.6.1.2.1.2.2.1.16 (Octets émis)

hrProcessorLoad .1.3.6.1.2.1.25.3.3.1.2 (CPU load)
hrStorageUsed   .1.3.6.1.2.1.25.2.3.1.6 (Storage utilisé)
```

### 🔍 Requêtes SNMPv3

**snmpget (récupérer OID unique)** :
```bash
# Syntax
snmpget -v3 -l authPriv -u snmpadmin -a SHA-256 -A "AuthPass123!" -x AES -X "PrivPass456!" 192.168.1.1 OID

# Exemples
# Description système
snmpget -v3 -l authPriv -u snmpadmin -a SHA-256 -A "AuthPass123!" -x AES -X "PrivPass456!" 192.168.1.1 .1.3.6.1.2.1.1.1.0

SNMPv2-MIB::sysDescr.0 = STRING: Cisco IOS Software, Version 15.2

# Uptime
snmpget -v3 -l authPriv -u snmpadmin -a SHA -A "AuthPass123!" -x AES -X "PrivPass456!" 192.168.1.1 sysUpTime.0

DISMAN-EVENT-MIB::sysUpTimeInstance = Timeticks: (123456789) 14 days, 6:56:07.89
```

**snmpwalk (récupérer arbre OID)** :
```bash
# Tout l'arbre system
snmpwalk -v3 -l authPriv -u snmpadmin -a SHA -A "AuthPass123!" -x AES -X "PrivPass456!" 192.168.1.1 .1.3.6.1.2.1.1

# Toutes les interfaces
snmpwalk -v3 -l authPriv -u snmpadmin -a SHA -A "AuthPass123!" -x AES -X "PrivPass456!" 192.168.1.1 ifDescr

IF-MIB::ifDescr.1 = STRING: GigabitEthernet0/0
IF-MIB::ifDescr.2 = STRING: GigabitEthernet0/1
IF-MIB::ifDescr.3 = STRING: Vlan1
```

**snmpbulkwalk (efficace pour gros volumes)** :
```bash
# SNMPv2c/v3 uniquement (pas v1)
snmpbulkwalk -v3 -l authPriv -u snmpadmin -a SHA -A "AuthPass123!" -x AES -X "PrivPass456!" 192.168.1.1 ifTable
```

### 🛡️ Sécurisation SNMP

**Best Practices** :
```
1. ✅ Utiliser SNMPv3 authPriv UNIQUEMENT
2. ✅ Désactiver SNMPv1/v2c
3. ✅ Changer community par défaut (si v1/v2c obligatoire)
4. ✅ ACL : Limiter IP sources autorisées
5. ✅ Firewall : Bloquer UDP 161/162 depuis Internet
6. ✅ Read-only : Jamais RW sauf absolument nécessaire
7. ✅ Passwords forts (min 12 caractères)
8. ✅ Monitoring : Alertes tentatives auth échouées
```

**Firewall rules** :
```bash
# iptables : Autoriser uniquement serveur monitoring
iptables -A INPUT -p udp --dport 161 -s 192.168.1.100 -j ACCEPT
iptables -A INPUT -p udp --dport 161 -j DROP

# pfSense : Interface LAN
[Règle] Pass | UDP | 192.168.1.100 | * | Firewall | 161 | "SNMP from monitoring"
[Règle] Block | UDP | * | * | Firewall | 161 | "Block SNMP others"
```

### 📊 Outils Monitoring SNMP

**Zabbix** :
```
Configuration > Hosts > Create host
  SNMP interfaces : 192.168.1.1:161
  SNMP version : SNMPv3
  Security level : authPriv
  Authentication : SHA-256 + AuthPass123!
  Privacy : AES + PrivPass456!
  
Templates : Link "Template Net Cisco IOS SNMPv3"
```

**PRTG** :
```
Ajouter Device > SNMP Traffic Sensor
  SNMP Version : v3
  Security : authPriv (SHA-256 + AES-256)
  Context : (vide)
  Username : snmpadmin
```

**Cacti/LibreNMS/Observium** : Support SNMPv3 natif

Q18 : **Politique Deny All par Défaut (Least Privilege)**

**Explication** : Deny All implicite. Créer règles Allow explicites pour besoins métiers uniquement.

### 🔒 Principe "Least Privilege" (Moindre Privilège)

**Philosophie** :
```
❌ Tout autoriser SAUF ce qui est dangereux (Blacklisting)
   → Impossible de tout prévoir
   → Nouvelles menaces passent
   
✅ Tout bloquer SAUF ce qui est nécessaire (Whitelisting)
   → Surface d'attaque minimale
   → Sécurité par défaut
```

**Règle implicite en fin de chaîne** :
```
Firewall ruleset :

[Règle 1] Allow 192.168.1.0/24 → WAN port 443
[Règle 2] Allow 192.168.1.0/24 → WAN port 80  
[Règle 3] Allow 192.168.1.0/24 → 8.8.8.8 port 53
...
[Règle N] DENY ALL  <--- Implicite (invisible mais active)

Tout trafic ne matchant aucune règle Allow → Bloqué
```

### 📊 Deny All vs Allow All

| Approche | Sécurité | Complexité | Usage | Défaut |
|----------|----------|------------|-------|--------|
| **Deny All** | ✅✅ Maximale | ⚠️ Config Allow explicites | Production | ✅ Recommandé |
| **Allow All** | ❌ Minimale | ✅ Simple | Dev/Lab | ❌ Dangereux |

**Exemple Deny All (iptables)** :
```bash
# Policy par défaut : DROP
iptables -P INPUT DROP
iptables -P FORWARD DROP
iptables -P OUTPUT ACCEPT  # Sortie autorisée (peut être DROP aussi)

# Règles Allow explicites
iptables -A INPUT -i lo -j ACCEPT  # Loopback
iptables -A INPUT -m state --state ESTABLISHED,RELATED -j ACCEPT
iptables -A INPUT -p tcp --dport 22 -s 192.168.1.0/24 -j ACCEPT  # SSH LAN uniquement
iptables -A INPUT -p tcp --dport 443 -j ACCEPT  # HTTPS public

# Tout le reste → DROP (policy)
```

**Exemple Allow All (DANGEREUX)** :
```bash
# ❌ NE JAMAIS FAIRE EN PRODUCTION
iptables -P INPUT ACCEPT
iptables -P FORWARD ACCEPT
iptables -P OUTPUT ACCEPT

# Bloquer seulement quelques ports
iptables -A INPUT -p tcp --dport 23 -j DROP  # Telnet
iptables -A INPUT -p tcp --dport 21 -j DROP  # FTP

# Problème : Des milliers d'autres services exposés !
```

### 🔥 Règle Implicite : Implémentations

**iptables (Linux)** :
```bash
# Policy = règle implicite
iptables -P INPUT DROP
iptables -P FORWARD DROP

# Visualiser policies
iptables -L -v

Chain INPUT (policy DROP 0 packets, 0 bytes)
Chain FORWARD (policy DROP 0 packets, 0 bytes)
Chain OUTPUT (policy ACCEPT 0 packets, 0 bytes)
```

**pfSense** :
```
Règle invisible en fin de chaque interface :

[Interface LAN - Rules]
  [User Rule 1] Pass HTTP/HTTPS
  [User Rule 2] Pass DNS
  [Hidden Rule] Block * * * * *  <--- Automatique, non-éditable
  
Configuration :
Firewall > Rules > LAN
  ☑ "Block private networks" (RFC1918 sur WAN)
  ☑ "Block bogon networks" (non-routable IPs)
```

**Cisco ASA** :
```cisco
! Deny implicite en fin de chaque ACL
access-list OUTSIDE_IN extended permit tcp any host 203.0.113.5 eq 443
access-list OUTSIDE_IN extended permit tcp any host 203.0.113.5 eq 80
! Implicite : deny ip any any (invisible)

access-group OUTSIDE_IN in interface outside

! Voir compteurs deny
show access-list OUTSIDE_IN
...
access-list OUTSIDE_IN line 3 extended deny ip any any (hitcnt=45678)  <--- Implicite
```

### 📝 Logs des Deny pour Audit

**Traçabilité** :
```
Pourquoi logger les deny ?

1. ✅ Détection tentatives intrusion (port scans, exploits)
2. ✅ Debug config firewall (règles manquantes)
3. ✅ Conformité (RGPD, PCI-DSS, ISO 27001)
4. ✅ Forensics post-incident
5. ⚠️ Attention volume logs (DDoS peut saturer disque)
```

**iptables avec logs** :
```bash
# Règle LOG avant DROP
iptables -A INPUT -m limit --limit 5/min -j LOG --log-prefix "[IPTABLES DROP] " --log-level 4
iptables -A INPUT -j DROP

# Voir logs
sudo tail -f /var/log/kern.log | grep "IPTABLES DROP"

Dec 19 15:30:45 server kernel: [IPTABLES DROP] IN=eth0 OUT= SRC=203.0.113.45 DST=192.168.1.1 PROTO=TCP SPT=54321 DPT=23
                                              Telnet scan bloqué ^
```

**pfSense logs** :
```
Status > System Logs > Firewall

Dec 19 15:30:45 WAN   Default deny rule   TCP:S   203.0.113.45:54321   192.168.1.1:23
Dec 19 15:30:46 WAN   Default deny rule   TCP:S   203.0.113.45:54322   192.168.1.1:22
                      ^^^^^^^^^^^^^^ Règle implicite qui a bloqué
                      
Filtre : @default  (voir uniquement deny implicites)
```

### ✅ Whitelisting vs ❌ Blacklisting

**Whitelisting (Deny All + Allow explicites)** :
```
Par défaut : DENY ALL
Règles : Allow uniquement besoins métiers

Avantages :
✅ Sécurité maximale
✅ Surface d'attaque minimale
✅ Zero-day ne passe pas (pas autorisé explicitement)

Inconvénients :
⚠️ Config initiale complexe
⚠️ Changements métiers = nouvelles règles
⚠️ Risque blocage légitime si oubli

Usage :
✅ Production
✅ DMZ, serveurs critiques
✅ Conformité stricte
```

**Blacklisting (Allow All + Deny suspects)** :
```
Par défaut : ALLOW ALL
Règles : Deny uniquement menaces connues

Avantages :
✅ Configuration simple
✅ Pas de blocage légitime
✅ Flexibilité max

Inconvénients :
❌ Nouvelles menaces passent
❌ Impossible de blacklist toutes attaques
❌ Maintenance listes volumineuse

Usage :
⚠️ Dev/Lab uniquement
❌ Production déconseillé
```

### 🛠️ Exemples Règles Minimales

**Firewall Entreprise (pfSense)** :
```
[Interface WAN - Deny All par défaut]
  [Règle 1] Block | * | RFC1918 | * | * | * | "Block private IPs on WAN"
  [Règle 2] Block | * | Bogons | * | * | * | "Block non-routable"
  [Règle 3] Pass | TCP | Any | * | WAN IP | 443 | "HTTPS serveur web public"
  [Règle 4] Pass | UDP | Any | * | WAN IP | 1194 | "OpenVPN"
  [Implicite] Block | * | Any | * | Any | * | "Default deny" [Log]
  
[Interface LAN - Allow sortie, deny entrant]
  [Règle 1] Pass | * | LAN subnet | * | Any | * | "Allow all outbound"
  [Implicite] Block | * | Any | * | LAN subnet | * | "Default deny" [Log]
  
[Interface DMZ - Très restrictif]
  [Règle 1] Pass | TCP | DMZ_Web | * | LAN_DB | 3306 | "Web to MySQL"
  [Règle 2] Pass | TCP/UDP | DMZ_Web | * | Any | 80,443 | "Updates"
  [Implicite] Block | * | DMZ subnet | * | Any | * | "Default deny" [Log]
```

**Serveur Linux (iptables)** :
```bash
#!/bin/bash
# Politique Deny All

# Flush existing rules
iptables -F
iptables -X

# Default policies
iptables -P INPUT DROP    # ✅ Deny All entrant
iptables -P FORWARD DROP
iptables -P OUTPUT ACCEPT # Sortie OK (ou DROP + allow explicites)

# Loopback
iptables -A INPUT -i lo -j ACCEPT

# Established connections
iptables -A INPUT -m conntrack --ctstate ESTABLISHED,RELATED -j ACCEPT

# ICMP (ping)
iptables -A INPUT -p icmp --icmp-type echo-request -m limit --limit 5/s -j ACCEPT

# SSH (LAN uniquement)
iptables -A INPUT -p tcp --dport 22 -s 192.168.1.0/24 -m conntrack --ctstate NEW -j ACCEPT

# HTTP/HTTPS (public)
iptables -A INPUT -p tcp -m multiport --dports 80,443 -m conntrack --ctstate NEW -j ACCEPT

# Log denied
iptables -A INPUT -m limit --limit 5/min -j LOG --log-prefix "[DENY] " --log-level 4

# Policy DROP appliquée automatiquement

# Sauvegarder
iptables-save > /etc/iptables/rules.v4
```

**Serveur Windows (Windows Firewall)** :
```powershell
# Powershell : Deny All + Allow explicites

# Default : Block all inbound
Set-NetFirewallProfile -Profile Domain,Public,Private -DefaultInboundAction Block -DefaultOutboundAction Allow

# Allow RDP (Admin subnet)
New-NetFirewallRule -DisplayName "RDP Admin" -Direction Inbound -Protocol TCP -LocalPort 3389 -RemoteAddress 192.168.1.0/24 -Action Allow

# Allow HTTP/HTTPS
New-NetFirewallRule -DisplayName "HTTP" -Direction Inbound -Protocol TCP -LocalPort 80 -Action Allow
New-NetFirewallRule -DisplayName "HTTPS" -Direction Inbound -Protocol TCP -LocalPort 443 -Action Allow

# Enable logging
Set-NetFirewallProfile -Profile Domain -LogAllowed False -LogBlocked True -LogFileName "C:\Windows\System32\LogFiles\Firewall\pfirewall.log"
```

### 📊 Audit et Conformité

**PCI-DSS Requirement 1.2.1** :
```
"Restrict inbound and outbound traffic to that which is necessary 
for the cardholder data environment, and specifically deny all other traffic."

→ Deny All obligatoire pour conformité cartes bancaires
```

**ISO 27001 A.13.1.3** :
```
"Networks shall be segregated... with deny-all, permit-by-exception rule sets."

→ Whitelisting requis
```

**RGPD Article 32** :
```
"Appropriate technical measures... including network access control."

→ Logs deny pour traçabilité accès données personnelles
```

Q19 : **Logs Firewall (Traçabilité et Conformité)**

**Explication** : Logs = traçabilité attaques, analyse trafic, conformité (RGPD, ISO).

### 📝 Pourquoi Logger les Firewall ?

**Objectifs** :
```
1. 🚨 Détection attaques
   - Scans de ports
   - Brute-force
   - Exploitation vulnérabilités
   - DDoS
   
2. 🔍 Analyse trafic
   - Patterns comportementaux
   - Identification anomalies
   - Optimisation règles firewall
   
3. 🛡️ Forensics (post-incident)
   - Timeline attaque
   - IP sources compromises
   - Données exfiltrées
   
4. 📊 Conformité réglementaire
   - RGPD (traçabilité accès)
   - PCI-DSS (logs 1 an)
   - ISO 27001 (audit sécurité)
   - SOC 2, HIPAA
```

### 📊 Format Logs : Informations Capturées

**Champs essentiels** :
```
Timestamp : Date/heure précise (UTC recommandé)
Action : ACCEPT, DENY, DROP, REJECT
Interface : WAN, LAN, DMZ
Source IP : Adresse origine
Source Port : Port source
Dest IP : Adresse destination
Dest Port : Port destination
Protocol : TCP, UDP, ICMP
Flags TCP : SYN, ACK, FIN, RST
Packet Length : Taille paquet
Rule : Règle firewall appliquée
```

**Exemple log pfSense** :
```
Dec 19 16:45:32 firewall filterlog: 5,,,1000000103,em0,match,block,in,4,0x0,,64,12345,0,none,6,tcp,60,203.0.113.45,192.168.1.1,54321,22,0,S,1234567890,,64240,,mss

Décodé :
Date : Dec 19 16:45:32
Action : block
Interface : em0 (WAN)
Protocol : tcp (6)
Source : 203.0.113.45:54321
Dest : 192.168.1.1:22 (SSH)
Flags : S (SYN) → Tentative connexion SSH bloquée
```

**Exemple log iptables** :
```
Dec 19 16:45:32 server kernel: [IPTABLES DENY] IN=eth0 OUT= MAC=00:11:22:33:44:55:66:77:88:99:aa:bb:08:00 SRC=203.0.113.45 DST=192.168.1.1 LEN=60 TOS=0x00 PREC=0x00 TTL=64 ID=12345 DF PROTO=TCP SPT=54321 DPT=22 WINDOW=64240 RES=0x00 SYN URGP=0

Décodé :
Interface : eth0 (entrant)
Source : 203.0.113.45:54321
Dest : 192.168.1.1:22
Proto : TCP
Flags : SYN (tentative connexion)
TTL : 64 (Linux/Unix source)
```

### 💾 Logs pfSense

**Localisation** : `/var/log/filter.log`

**Visualisation WebGUI** :
```
Status > System Logs > Firewall

Filters :
- Interface : WAN, LAN, DMZ
- Action : Pass, Block
- Protocol : TCP, UDP, ICMP
- Source/Dest IP

🔍 Live View (rafraîchissement auto)
```

**Configuration logs** :
```
Status > System Logs > Settings

☑ Log packets matched from the default block rules
☑ Log packets matched from the default pass rules (attention volume)
☑ Log packets blocked by interface rules

Log Rotation :
- Max log entries : 500000
- Log retention : 7 days

Remote Logging (Syslog) :
- Server : 192.168.1.100:514
- Contents : Firewall events
```

**Analyse logs pfSense** :
```bash
# SSH sur pfSense
ssh admin@firewall.local

# Voir logs en temps réel
tail -f /var/log/filter.log

# Top 10 IP sources bloquées
grep "block" /var/log/filter.log | awk '{print $8}' | sort | uniq -c | sort -rn | head -10

  1234 203.0.113.45
   567 198.51.100.78
   432 192.0.2.123

# Top 10 ports ciblés
grep "block" /var/log/filter.log | awk '{print $11}' | sort | uniq -c | sort -rn | head -10

  2345 22   (SSH)
  1890 23   (Telnet)
  1234 445  (SMB)
   987 3389 (RDP)
```

### 🔥 Logs iptables (Linux)

**Configuration logging** :
```bash
# Règle LOG avant DROP
iptables -A INPUT -m limit --limit 5/min -j LOG --log-prefix "[FW-DENY] " --log-level 4
iptables -A INPUT -j DROP

# Options utiles
--log-level 4  # Warning (syslog)
--log-prefix "[PREFIX] "  # Max 29 caractères
--log-tcp-options  # Log options TCP
--log-ip-options   # Log options IP

# Limiter rate (anti-flood logs)
-m limit --limit 5/min --limit-burst 10
```

**Localisation logs** :
```bash
# Debian/Ubuntu
/var/log/kern.log
/var/log/syslog

# RHEL/CentOS
/var/log/messages

# Voir logs firewall
sudo tail -f /var/log/kern.log | grep "FW-DENY"
```

**Analyse** :
```bash
# Attaques SSH
grep "FW-DENY.*DPT=22" /var/log/kern.log | wc -l
1234  # Tentatives bloquées

# Top attackers
grep "FW-DENY" /var/log/kern.log | grep -oP 'SRC=\K[0-9.]+' | sort | uniq -c | sort -rn | head -10

# Ports scannés par IP
grep "SRC=203.0.113.45" /var/log/kern.log | grep -oP 'DPT=\K[0-9]+' | sort -u
```

### 📊 SIEM : Agrégation et Analyse

**SIEM** = Security Information and Event Management

**Solutions** :
```
Commercial :
- Splunk
- IBM QRadar
- ArcSight

Open-Source :
- ELK Stack (Elasticsearch, Logstash, Kibana)
- Graylog
- Wazuh
- OSSIM (AlienVault)
```

**ELK Stack pour Logs Firewall** :

**1. Logstash (collecte)** :
```ruby
# /etc/logstash/conf.d/firewall.conf
input {
  syslog {
    port => 5514
    type => "pfsense"
  }
}

filter {
  if [type] == "pfsense" {
    grok {
      match => { "message" => "%{SYSLOGTIMESTAMP:timestamp}.*filterlog.*,%{DATA:action},%{DATA:direction}.*,%{IP:src_ip},%{IP:dst_ip},%{NUMBER:src_port},%{NUMBER:dst_port}" }
    }
    geoip {
      source => "src_ip"
      target => "geoip"
    }
  }
}

output {
  elasticsearch {
    hosts => ["localhost:9200"]
    index => "firewall-%{+YYYY.MM.dd}"
  }
}
```

**2. Kibana (visualisation)** :
```
Dashboards :
- Top blocked IPs (world map)
- Blocked traffic over time (histogram)
- Top attacked ports (pie chart)
- Alert spikes (line chart)

Alertes :
- Plus de 100 deny/min depuis même IP → DDoS
- Port 22 scanné depuis 10+ IPs → Bot scan
- Trafic vers pays inhabituels → Anomalie
```

### 🔄 Rotation Logs

**Problème** : Logs consomment disque

**Logrotate (Linux)** :
```bash
# /etc/logrotate.d/iptables
/var/log/iptables.log {
    daily                # Rotation quotidienne
    rotate 30            # Garder 30 jours
    compress             # Gzip archives
    delaycompress        # Compresser J-1
    notifempty           # Ignorer si vide
    create 0640 root adm # Permissions nouveau fichier
    postrotate
        /usr/sbin/service rsyslog restart > /dev/null
    endscript
}
```

**pfSense** :
```
Status > System Logs > Settings

☑ Enable circular logging
Log size : 512 KB per log file
Log retention : 7 days

Ou configuration avancée :
Diagnostics > Command Prompt
mkdir /var/log/archive
tar -czf /var/log/archive/firewall-$(date +%Y%m%d).tar.gz /var/log/filter.log
```

### ⚖️ Conformité Réglementaire

**RGPD (Article 32)** :
```
"Capacité à garantir la disponibilité, l'intégrité, la disponibilité 
et la résilience constantes des systèmes."

Requis :
✅ Logs accès aux données personnelles
✅ Traçabilité tentatives accès non-autorisés
✅ Horodatage précis (NTP synchronisé)
✅ Intégrité logs (immutabilité, signature)
✅ Durée conservation adaptée (minimisation)
```

**PCI-DSS (Requirement 10)** :
```
10.2.1 : Tous accès individuels données titulaires
10.2.2 : Toutes actions administrateur/root
10.2.7 : Création/suppression objets système

Rétention :
✅ 3 mois en ligne (accès immédiat)
✅ 12 mois archivés

Logs firewall :
✅ Tous deny/allow vers cardholder data environment
✅ Review quotidien logs sécurité
```

**ISO 27001 (A.12.4.1)** :
```
"Des journaux d'événements enregistrant les activités de l'utilisateur,
les exceptions, les défauts et les événements de sécurité de l'information
doivent être produits, conservés et régulièrement révisés."

Requis :
✅ Logs firewall centralisés
✅ Protection logs contre modification
✅ Review mensuel minimum
✅ Alertes sur événements critiques
```

### 🚨 Alertes sur Logs Critiques

**Scénarios d'alerte** :
```
1. Port Scan Détecté
   Trigger : 20+ ports différents depuis même IP en 1 min
   Action : Email SOC, bannir IP temporairement
   
2. Brute-Force Attack
   Trigger : 10+ tentatives SSH/RDP échouées
   Action : Fail2ban, alerte admin
   
3. DDoS Suspecte
   Trigger : 1000+ deny/min
   Action : Alerte critique, activer mitigation
   
4. Tentative Accès Admin Externe
   Trigger : Connexion port 22/3389 depuis Internet
   Action : Bloquer + alerte immédiate
   
5. Exfiltration Données
   Trigger : Volume sortant anormal (>10GB)
   Action : Alerte + bloquer flux si non-autorisé
```

**Script alerte (exemple)** :
```bash
#!/bin/bash
# /usr/local/bin/firewall-alert.sh

LOGFILE="/var/log/kern.log"
THRESHOLD=50
EMAIL="admin@example.com"

# Compter deny dernière minute
COUNT=$(grep "FW-DENY" $LOGFILE | grep "$(date --date='1 minute ago' '+%b %d %H:%M')" | wc -l)

if [ $COUNT -gt $THRESHOLD ]; then
    echo "ALERTE : $COUNT paquets bloqués en 1 minute" | mail -s "Firewall Alert" $EMAIL
    
    # Top 5 IP sources
    grep "FW-DENY" $LOGFILE | tail -100 | grep -oP 'SRC=\K[0-9.]+' | sort | uniq -c | sort -rn | head -5 >> /tmp/alert.txt
    mail -s "Top Attackers" $EMAIL < /tmp/alert.txt
fi

# Cron : * * * * * /usr/local/bin/firewall-alert.sh
```

### 💡 Bonnes Pratiques Logging

```
1. ✅ Centraliser logs (Syslog, SIEM)
2. ✅ NTP synchronisé (timestamps cohérents)
3. ✅ Logger deny ET allow critiques
4. ✅ Rate limiting logs (anti-flood)
5. ✅ Rotation automatique
6. ✅ Backup logs offsite (immutabilité)
7. ✅ Chiffrer logs sensibles
8. ✅ Accès logs restreint (RBAC)
9. ✅ Alertes temps réel anomalies
10. ✅ Review périodique (SOC)
```


Q20 : **Alias Firewall (Variables et Groupes)**

**Explication** : Alias = variable (ex: "Serveurs_Web" = 10.0.1.10, 10.0.1.11). Modif centralisée.

### 🏷️ Principe des Alias

Un **alias** est une **variable nommée** qui représente un ou plusieurs :
- Adresses IP / réseaux
- Ports
- URLs / domaines

**Avantages** :
```
✅ Lisibilité : "Serveurs_Web" vs "10.0.1.10, 10.0.1.11, 10.0.1.12"
✅ Maintenance : Modifier alias → toutes règles mises à jour
✅ Réutilisabilité : Même alias dans plusieurs règles
✅ Documentation : Nom explicite = intention claire
```

**Sans alias** :
```
[Règle 1] Allow TCP from 10.0.1.10,10.0.1.11,10.0.1.12 to any port 443
[Règle 2] Allow TCP from 10.0.1.10,10.0.1.11,10.0.1.12 to 192.168.1.50 port 3306
[Règle 3] Allow ICMP from 10.0.1.10,10.0.1.11,10.0.1.12 to any

❌ Problème : Ajouter serveur = modifier 3 règles
```

**Avec alias** :
```
Alias "Serveurs_Web" = 10.0.1.10, 10.0.1.11, 10.0.1.12

[Règle 1] Allow TCP from Serveurs_Web to any port 443
[Règle 2] Allow TCP from Serveurs_Web to DB_Server port 3306
[Règle 3] Allow ICMP from Serveurs_Web to any

✅ Ajouter 10.0.1.13 → Modifier uniquement alias
```

### 📊 Types d'Alias

**1. Host(s)** : Adresses IP individuelles
```
Alias : Admin_Workstations
Type : Host(s)
IP : 192.168.1.100
     192.168.1.101
     192.168.1.102
     
Usage : Source autorisée pour SSH/RDP
```

**2. Network(s)** : Subnets CIDR
```
Alias : LAN_Subnets
Type : Network(s)
Network : 192.168.1.0/24
          192.168.10.0/24
          10.0.0.0/8
          
Usage : Règles outbound LAN
```

**3. Port(s)** : Ports TCP/UDP
```
Alias : Web_Services
Type : Port(s)
Ports : 80
        443
        8080
        8443
        
Usage : Autoriser trafic web
```

**4. URL / FQDN** : Domaines
```
Alias : Microsoft_Updates
Type : URL (FQDN)
URLs : update.microsoft.com
       windowsupdate.microsoft.com
       *.update.microsoft.com
       
Usage : Allow sortie vers Windows Update

⚠️ Nécessite résolution DNS par firewall
```

### 💻 Configuration pfSense

**Créer alias** :
```
Firewall > Aliases > IP

[Add]
Name : Serveurs_Web
Description : Serveurs web frontend (Apache/Nginx)
Type : Host(s)
IP or FQDN : 
  10.0.1.10   |  Description : web01.example.com
  10.0.1.11   |  Description : web02.example.com
  10.0.1.12   |  Description : web03.example.com
  
[Save]
[Apply Changes]
```

**Utiliser alias dans règle** :
```
Firewall > Rules > DMZ

[Add]
Action : Pass
Interface : DMZ
Protocol : TCP
Source : Serveurs_Web  <--- Dropdown liste alias
Destination : DB_Server
Destination Port : 3306 (MySQL)
Description : Web servers to database

[Save]
```

**Alias imbriqués (Nested)** :
```
# Alias de base
Alias : Web_Subnet
Type : Network
Network : 10.0.1.0/24

Alias : App_Subnet  
Type : Network
Network : 10.0.2.0/24

Alias : DB_Subnet
Type : Network
Network : 10.0.3.0/24

# Alias composé
Alias : DMZ_All_Subnets
Type : Network(s)
Networks : Web_Subnet
           App_Subnet
           DB_Subnet
           
Usage : [Règle] Block DMZ_All_Subnets to LAN_Subnets
```

### 🔧 Exemples Pratiques

**Scénario 1 : Infrastructure 3-tiers** :
```
# Alias IP
Web_Frontend    : 10.0.1.10, 10.0.1.11, 10.0.1.12
App_Backend     : 10.0.2.10, 10.0.2.11
DB_Servers      : 10.0.3.10, 10.0.3.11
Admin_Subnet    : 192.168.99.0/24

# Alias Ports
Web_Ports       : 80, 443
DB_Ports        : 3306, 5432

# Règles
[1] Allow TCP from Any to Web_Frontend port Web_Ports
[2] Allow TCP from Web_Frontend to App_Backend port 8080
[3] Allow TCP from App_Backend to DB_Servers port DB_Ports
[4] Allow TCP from Admin_Subnet to All_Servers port 22
[5] Block All
```

**Scénario 2 : Filtrage par pays (GeoIP)** :
```
# Alias GeoIP
Alias : Pays_Autorises
Type : GeoIP
Countries : FR (France)
            BE (Belgique)
            CH (Suisse)
            
Alias : Pays_Bloques
Type : GeoIP  
Countries : CN (Chine)
            RU (Russie)
            KP (Corée du Nord)

# Règles WAN
[1] Block from Pays_Bloques to Any
[2] Allow TCP from Pays_Autorises to WAN_IP port 443
```

**Scénario 3 : Services Cloud** :
```
Alias : Office365_IPs
Type : URL Table
URL : https://endpoints.office.com/endpoints/worldwide?clientrequestid=xxx

Alias : AWS_S3
Type : Network(s)
Networks : 52.216.0.0/15
           54.231.0.0/17
           (IP ranges AWS S3)

# Règle
[1] Allow TCP from LAN to Office365_IPs port 443
[2] Allow TCP from App_Servers to AWS_S3 port 443
```

### 🔄 Modification Centralisée

**Avant (sans alias)** :
```
Ajouter serveur web04 (10.0.1.13) :

❌ Modifier Règle 1 : Add 10.0.1.13 to source
❌ Modifier Règle 2 : Add 10.0.1.13 to source
❌ Modifier Règle 5 : Add 10.0.1.13 to source
❌ Modifier Règle 12 : Add 10.0.1.13 to source

= 4 modifications, risque oubli
```

**Après (avec alias)** :
```
Ajouter serveur web04 (10.0.1.13) :

✅ Firewall > Aliases > IP > Serveurs_Web
✅ Add : 10.0.1.13 | web04.example.com
✅ Save > Apply Changes

= Toutes règles utilisant "Serveurs_Web" mises à jour automatiquement
```

### 📝 Bonnes Pratiques Nommage

**Convention de nommage** :
```
✅ CamelCase ou Snake_Case
✅ Noms descriptifs
✅ Préfixe type (optionnel)

Exemples :
IP_Serveurs_Web
NET_DMZ_Subnets
PORT_Services_Web
GEO_Europe_Ouest
URL_CDN_Cloudflare

❌ À éviter :
alias1, temp, test, IPs, servers (trop vague)
```

**Organisation** :
```
# Par fonction
Web_*      : Serveurs web
DB_*       : Bases de données
App_*      : Serveurs applicatifs
Admin_*    : Postes admin

# Par localisation
Paris_*    : Site Paris
Lyon_*     : Site Lyon
Cloud_*    : Resources cloud

# Par environnement
Prod_*     : Production
Dev_*      : Développement
Test_*     : Tests
```

### 📊 Import/Export Alias

**pfSense : Bulk import** :
```
Firewall > Aliases > Import

Format CSV :
type,name,address,descr,detail
host,Web_Servers,"10.0.1.10 10.0.1.11 10.0.1.12","Frontend web","web01|web02|web03"
network,LAN_Subnets,"192.168.1.0/24 192.168.10.0/24","LAN networks","Main|Secondary"
port,Web_Ports,"80 443 8080","HTTP services","HTTP|HTTPS|Alt"

[Upload] > [Process]
```

**Export** :
```
Firewall > Aliases > Export

Format : CSV, JSON, XML
Usage : Backup, migration, documentation
```

### 🔐 Alias pour Whitelist/Blacklist

**IP Blacklist** :
```
Alias : Known_Attackers
Type : Host(s)
IP : 203.0.113.45   | Bot scan SSH
     198.51.100.78  | DDoS source
     192.0.2.123    | Malware C2
     
Source : Threat intelligence feeds

# Règle WAN
[1] Block from Known_Attackers to Any [Log]
```

**URL Blacklist (Table)** :
```
Alias : Malware_Domains
Type : URL Table (Alias)
URL : https://raw.githubusercontent.com/StevenBlack/hosts/master/hosts

Update Frequency : Daily

# Règle LAN
[1] Block from LAN to Malware_Domains port 80,443
```

### ⚡ Dynamic Alias (pfBlockerNG)

**GeoIP + Threat Feeds** :
```
# Package pfBlockerNG
System > Package Manager > Available Packages
Install : pfBlockerNG-devel

Firewall > pfBlockerNG > IP

[Add]
Alias : Spamhaus_DROP
Source : https://www.spamhaus.org/drop/drop.txt
Format : CIDR
Action : Deny Inbound
Update : Daily

[Add]  
Alias : GeoIP_China
Source : MaxMind GeoLite2
Countries : CN
Action : Deny Both

→ Alias dynamiques mis à jour automatiquement
```

### 💡 Alias vs Groupes d'Objets

**pfSense** : Alias = Groupes d'objets intégrés

**Cisco ASA** : object-group
```cisco
# Object group network
object-group network WEB_SERVERS
  network-object host 10.0.1.10
  network-object host 10.0.1.11
  network-object host 10.0.1.12
  
# Object group service
object-group service WEB_PORTS tcp
  port-object eq 80
  port-object eq 443
  port-object eq 8080
  
# ACL utilisant object groups
access-list OUTSIDE_IN extended permit tcp any object-group WEB_SERVERS object-group WEB_PORTS
```

**FortiGate** : Address Groups
```
config firewall address
    edit "web01"
        set subnet 10.0.1.10 255.255.255.255
    next
    edit "web02"
        set subnet 10.0.1.11 255.255.255.255
    next
end

config firewall addrgrp
    edit "Web_Servers"
        set member "web01" "web02" "web03"
    next
end
```


Q21 : **DDoS (Distributed Denial of Service)**

**Explication** : Distributed Denial of Service. Multiples sources submergent cible (indisponibilité).

### 💥 Principe du DDoS

**DoS** (Denial of Service) : 1 source attaque 1 cible
**DDoS** (Distributed DoS) : N sources attaquent 1 cible simultanément

```
DoS (simple) :
[Attaquant] --[Flood]--> [Serveur Victime]
                          ❌ Saturé

DDoS (distribué) :
[Botnet C&C]  
     |
     +--[Bot 1]---\
     +--[Bot 2]----+-[Flood massif]---> [Serveur Victime]
     +--[Bot 3]---/                      ❌❌ Complètement saturé
     ...
     +--[Bot 10000]
     
Volume : 100+ Gbps, millions requêtes/sec
But : Saturer bande passante OU ressources (CPU/RAM)
```

### 📊 Types d'Attaques DDoS

**1. Volumetric Attacks (Layer 3/4)** : Saturation bande passante

**UDP Flood** :
```
Principe : Envoi massif paquets UDP vers services (DNS, NTP, memcached)
Volume : 1-100+ Gbps
Source : Botnets IoT (caméras, DVR)

Exemple :
10,000 bots × 10 Mbps = 100 Gbps → Lien Internet saturé

Mitigation : 
- Rate limiting UDP
- Blackhole routing
- Anycast distribution
```

**SYN Flood** :
```
Principe : Envoi TCP SYN sans ACK final (half-open connections)
Impact : Table connexions serveur pleine

[Attaquant] --SYN--> [Serveur]
[Attaquant] <-SYN/ACK-- [Serveur]
[Attaquant]  (silence, pas de ACK)

Serveur attend timeout (30-120s) × millions connexions = RAM saturée

Mitigation :
- SYN cookies
- Rate limiting
- Firewall stateful inspection
```

**ICMP Flood (Ping Flood)** :
```
Principe : Flood pings (ICMP Echo Request)
Volume : 10-50 Gbps

Mitigation :
- Bloquer ICMP depuis Internet
- Rate limiting ICMP
```

**2. Amplification Attacks** : Réflexion + Amplification

**DNS Amplification** :
```
Principe : Exploiter serveurs DNS ouverts (open resolvers)

1. Attaquant forge requête DNS avec IP source = Victime
2. Requête : 60 bytes ("ANY example.com")
3. Réponse DNS : 3000 bytes
4. Amplification : 50x

[Attaquant] --60B--> [DNS Ouvert] --3000B--> [Victime]
                     SRC spoofed=Victime
                     
1000 serveurs DNS × 50x amplification = 50,000x traffic vers victime

Mitigation :
- Désactiver récursion DNS publics
- BCP38 (anti-spoofing)
- Rate limiting
```

**NTP Amplification** :
```
Commande : monlist (liste 600 dernières IPs)
Requête : 8 bytes
Réponse : 48,000 bytes
Amplification : 6000x !

Mitigation :
- Désactiver monlist (NTP v4.2.7+)
- Firewall : Bloquer NTP depuis Internet sauf NTP pool
```

**Memcached Amplification** :
```
Port : 11211 UDP (cache mémoire)
Amplification : 51,000x (record)

2018 : GitHub hit 1.35 Tbps DDoS via memcached

Mitigation :
- Ne JAMAIS exposer memcached sur Internet
- Bind localhost uniquement
- Firewall strict
```

**3. Application Layer Attacks (Layer 7)** : Viser logique applicative

**HTTP Flood** :
```
Principe : Requêtes HTTP légitimes mais massives
Cible : Pages coûteuses (recherche, login, API)

Exemple :
GET /search?q=something (requête SQL lourde)
× 100,000 requêtes/sec → Base de données saturée

Difficile à détecter : Trafic ressemble à utilisateurs normaux

Mitigation :
- WAF (Web Application Firewall)
- Rate limiting per IP
- CAPTCHA si suspect
- CDN (Cloudflare, Akamai)
```

**Slowloris** :
```
Principe : Connexions HTTP lentes pour épuiser threads serveur

1. Ouvrir connexion HTTP
2. Envoyer headers incomplets
3. Envoyer 1 byte toutes les 10 secondes (keep-alive)
4. Serveur garde connexion ouverte
5. Répéter × 1000 → Threads serveur épuisés

Mitigation :
- Timeout connexions (5-10s)
- Limiter connexions simultanées par IP
- Reverse proxy (Nginx)
```

### 🤖 Botnets : Armée de Zombies

**Composition** :
```
C&C (Command & Control) : Serveur contrôle
    |
    +--[Bots] : Machines infectées (malware)
       - PC utilisateurs (virus)
       - Serveurs compromise (exploit)
       - IoT (caméras, routeurs, DVR) ← Mirai botnet
       
Taille : 10,000 - 10,000,000+ bots

Exemples célèbres :
- Mirai (2016) : 600k IoT devices, 1 Tbps DDoS
- Emotet : 1.5M bots
- Zeus : Banking trojan + DDoS
```

**Mirai Botnet** :
```
Cible : Dispositifs IoT (mots de passe par défaut)

Credentials testés :
admin:admin
root:root
admin:password
root:12345

2016 : DDoS 1.2 Tbps contre Dyn (DNS provider)
→ Twitter, Netflix, GitHub inaccessibles

Mitigation IoT :
✅ Changer mots de passe par défaut
✅ Firmware à jour
✅ Segmentation réseau (VLAN IoT)
✅ Firewall : Bloquer accès Internet sortant IoT
```

### 🛡️ Mitigation DDoS

**1. Rate Limiting** :
```bash
# iptables : Limiter connexions SYN
iptables -A INPUT -p tcp --syn -m limit --limit 10/s --limit-burst 20 -j ACCEPT
iptables -A INPUT -p tcp --syn -j DROP

# Nginx : Rate limiting HTTP
http {
    limit_req_zone $binary_remote_addr zone=one:10m rate=10r/s;
    
    server {
        location / {
            limit_req zone=one burst=20;
        }
    }
}

# Apache : mod_evasive
<IfModule mod_evasive20.c>
    DOSHashTableSize 3097
    DOSPageCount 5        # Max 5 requêtes même page/sec
    DOSSiteCount 50       # Max 50 requêtes site/sec
    DOSBlockingPeriod 10  # Bloquer 10 secondes
</IfModule>
```

**2. SYN Cookies** :
```bash
# Linux kernel : Protection SYN flood
sysctl -w net.ipv4.tcp_syncookies=1
sysctl -w net.ipv4.tcp_max_syn_backlog=2048
sysctl -w net.ipv4.tcp_synack_retries=2

# SYN cookies = pas de stockage connexion avant ACK final
```

**3. Blackhole Routing** :
```
Principe : Router trafic attaque vers null0 (discard)

Cisco :
ip route 203.0.113.45 255.255.255.255 Null0

Linux :
ip route add blackhole 203.0.113.45/32

Usage : DDoS volumetric → Sacrifice IP attaquée temporairement
```

**4. CDN et Anycast** :
```
Cloudflare, Akamai, AWS Shield :

- Anycast : Même IP annoncée depuis multiples PoP mondiaux
- Trafic distribué géographiquement
- Absorption : Centaines Tbps capacité
- Filtrage : WAF, bot detection, CAPTCHA

Exemple Cloudflare :
- 310+ PoP
- 172 Tbps capacité totale
- Proxy inverse : Origine cachée
```

**5. BGP Flowspec** :
```
Principe : Annoncer filtres DDoS via BGP aux routeurs upstream

Flowspec rule :
- Source : 0.0.0.0/0 (any)
- Dest : 203.0.113.5 (victime)
- Protocol : UDP
- Dest port : 53
- Action : Rate-limit 1 Mbps ou Drop

→ ISP filtre trafic en amont (avant saturation lien)
```

### 🚨 Détection DDoS

**Indicateurs** :
```
1. Trafic anormal
   - Augmentation soudaine bande passante (10x+)
   - Pics requêtes/sec (100x normal)
   
2. Patterns suspects
   - Sources multiples (botnet)
   - User-Agents identiques
   - Géolocalisation anormale (pays inhabituels)
   
3. Impact services
   - Lenteur extrême
   - Timeouts
   - Indisponibilité complète
   - CPU/RAM serveur 100%
   
4. Logs firewall
   - Millions deny/min
   - Mêmes ports ciblés (53, 80, 443)
```

**Outils monitoring** :
```bash
# Netstat : Connexions actives
netstat -an | grep SYN_RECV | wc -l
5678  # Anormal si >1000

# ss : Socket statistics
ss -s
TCP:   10234 (estab 234, closed 5, orphaned 0, timewait 10000)
       ^^^^^^ Anormal

# iftop : Bande passante temps réel
iftop -i eth0

# tcpdump : Capturer trafic suspect
tcpdump -i eth0 -n 'udp port 53' -c 1000
```

### 📊 Statistiques DDoS

```
Record DDoS (2024) :
- 3.47 Tbps (Microsoft Azure, 2021)
- 2.54 Tbps (Google, 2017)
- 1.35 Tbps (GitHub, 2018)

Coût DDoS :
- Louer botnet : 50-500€/jour
- Ransom DDoS : 5,000-100,000€
- Coût victime : 20,000-500,000€/heure d'indisponibilité

Motivations :
1. Extorsion (ransom)
2. Concurrence déloyale
3. Hacktivisme (Anonymous)
4. Diversion (pendant attaque principale)
5. Revenge
```

Q22 :

**Explication** : Whitelist = défaut deny. Blacklist = rattraper menaces connues (nouvelles passent).

Q23 :

**Explication** : STP désactive liens redondants créant boucles (broadcast storm). Active si lien principal down.

Q24 :

**Explication** : Standard = IP source. Étendue = src + dst + port + proto (granularité fine).

Q25 :

**Explication** : Maximum Transmission Unit. Ethernet std = 1500. Jumbo Frames = 9000 (optimisation).

---

## RÉPONSES : 1B, 2B, 3A, 4B, 5B, 6B, 7B, 8B, 9B, 10A, 11A, 12C, 13B, 14B, 15B, 16B, 17B, 18B, 19B, 20B, 21B, 22B, 23B, 24A, 25B

**Note /20** : Score / 1.25

---

**PORTS ESSENTIELS** :
- 22 = SSH, 23 = Telnet
- 80 = HTTP, 443 = HTTPS
- 25 = SMTP, 110 = POP3, 143 = IMAP
- 3389 = RDP, 445 = SMB
- 53 = DNS, 67/68 = DHCP
