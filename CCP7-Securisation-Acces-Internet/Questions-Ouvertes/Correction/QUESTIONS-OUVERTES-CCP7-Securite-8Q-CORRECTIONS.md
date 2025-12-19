# CORRECTIONS - QUESTIONS OUVERTES CCP7 - SÉCURITÉ RÉSEAUX

**Certification TSSR** - CCP7 : Maintenir et sécuriser interconnexions  
**8 questions - Corrections détaillées**

---

## ✔️ Question 1 : Architecture DMZ (10 pts)

**Énoncé** : Schématisez architecture DMZ avec pare-feu, définissez règles pour serveur web accessible Internet mais isolé LAN.

**Réponse modèle** (10 pts) :

**Architecture** (4 pts) :
```
Internet
   |
[Pare-feu WAN]
   |
   DMZ (192.168.2.0/24)
   |-- Serveur Web (192.168.2.10)
   |-- Serveur Mail (192.168.2.20)
   |
[Pare-feu LAN]
   |
LAN (192.168.1.0/24)
```

**Règles pare-feu** (6 pts) :

**WAN → DMZ** :
```
Allow TCP 80,443 → 192.168.2.10 (Web)
Allow TCP 25,587 → 192.168.2.20 (Mail)
Deny All
```

**DMZ → LAN** :
```
Deny All (isolation totale)
```

**LAN → DMZ** :
```
Allow TCP 22,3389 → DMZ (admin SSH/RDP)
Allow All (gestion serveurs)
```

---

## ✔️ Question 2 : VPN IPsec Site-to-Site (12 pts)

**Énoncé** : Configurez VPN IPsec entre Siège (10.0.0.0/24) et Agence (10.0.1.0/24). Paramètres phase 1 et 2 ?

**Réponse modèle** (12 pts) :

**Phase 1 (ISAKMP)** (6 pts) :
```
Mode: Main Mode (sécurisé)
Encryption: AES-256
Hash: SHA-256
DH Group: 14 (2048-bit)
Lifetime: 28800s (8h)
Authentication: Pre-Shared Key
```

**Phase 2 (ESP)** (6 pts) :
```
Protocol: ESP
Encryption: AES-256
Hash: SHA-256
PFS: Enabled (DH 14)
Lifetime: 3600s (1h)
Local Network: 10.0.0.0/24
Remote Network: 10.0.1.0/24
```

**pfSense config** :
```
VPN → IPsec → Add P1
  Remote Gateway: <IP publique agence>
  Pre-Shared Key: <clé secrète partagée>
  Phase 1 : Paramètres ci-dessus

Add P2
  Local Network: 10.0.0.0/24
  Remote Network: 10.0.1.0/24
  Phase 2 : Paramètres ci-dessus
```

---

## ✔️ Question 3 : Proxy Squid filtrage (8 pts)

**Énoncé** : Bloquer réseaux sociaux (Facebook, Twitter) pour IP 192.168.1.0/24 sauf 192.168.1.50 (direction). Config Squid ?

**Réponse modèle** (8 pts) :

```bash
# ACLs
acl reseau_interne src 192.168.1.0/24
acl direction src 192.168.1.50

acl reseaux_sociaux dstdomain .facebook.com .twitter.com .instagram.com
acl sites_streaming dstdomain .youtube.com .netflix.com

# Règles (ordre important)
http_access allow direction           # Direction autorisée tout
http_access deny reseau_interne reseaux_sociaux   # Autres bloqués RS
http_access allow reseau_interne      # Autres autorisés reste
http_access deny all                  # Deny défaut
```

**Points clés** :
- Règle direction AVANT blocage (ordre crucial)
- `.facebook.com` = inclut sous-domaines (m.facebook.com)
- `deny all` final = sécurité

---

## ✔️ Question 4 : VLAN segmentation (10 pts)

**Énoncé** : Créez 3 VLANs (Admin 10, Users 20, Invités 30). Config switch + pfSense + règles pare-feu.

**Réponse modèle** (10 pts) :

**Switch Cisco** (4 pts) :
```
# Créer VLANs
vlan 10
 name Admin
vlan 20
 name Users
vlan 30
 name Invites

# Ports access
interface Fa0/1
 switchport mode access
 switchport access vlan 10

# Port trunk vers pfSense
interface Gi0/1
 switchport mode trunk
 switchport trunk allowed vlan 10,20,30
```

**pfSense VLANs** (3 pts) :
```
Interfaces → Assignments → VLANs
  VLAN 10 → Parent: em0, Tag: 10, Description: Admin
  VLAN 20 → Parent: em0, Tag: 20, Description: Users
  VLAN 30 → Parent: em0, Tag: 30, Description: Invites
```

**Règles pare-feu** (3 pts) :
```
VLAN 10 (Admin) :
  Allow All (admins accès total)

VLAN 20 (Users) :
  Allow LAN net → Any (accès ressources internes + Internet)
  
VLAN 30 (Invites) :
  Allow VLAN30 net → Internet (80,443 uniquement)
  Deny VLAN30 net → LAN networks
```

---

## ✔️ Question 5 : IPS Suricata règle (8 pts)

**Énoncé** : Créez règle Suricata détectant scan port SSH (>10 tentatives connexion port 22 en 60s). Action ?

**Réponse modèle** (8 pts) :

```
# Règle Suricata
drop tcp any any -> $HOME_NET 22 \
  (msg:"Scan SSH detecte"; \
   flags:S; \
   threshold: type threshold, track by_src, count 10, seconds 60; \
   sid:1000001; rev:1;)
```

**Explication** :
- `drop` = IPS mode (bloque + log)
- `flags:S` = SYN flag (tentative connexion)
- `threshold` = 10 connexions/60s même source
- `$HOME_NET` = variable réseau protégé (ex: 192.168.0.0/16)
- `sid` = identifiant unique règle

**Alternative alerte** : Remplacer `drop` par `alert` (IDS mode)

---

## ✔️ Question 6 : Certificat SSL Let's Encrypt (7 pts)

**Énoncé** : Obtenez certificat Let's Encrypt pour `web.entreprise.com`. Commandes Certbot + renouvellement auto.

**Réponse modèle** (7 pts) :

**Installation** (2 pts) :
```bash
# Debian/Ubuntu
apt install certbot python3-certbot-apache

# CentOS/RHEL
yum install certbot python3-certbot-apache
```

**Obtention certificat** (3 pts) :
```bash
# Apache
certbot --apache -d web.entreprise.com -d www.web.entreprise.com

# Nginx
certbot --nginx -d web.entreprise.com

# Standalone (si pas serveur web)
certbot certonly --standalone -d web.entreprise.com
```

**Renouvellement automatique** (2 pts) :
```bash
# Tester renouvellement
certbot renew --dry-run

# Cron quotidien (auto-installé)
0 3 * * * certbot renew --quiet --post-hook "systemctl reload apache2"
```

**Validité** : 90 jours, renouvellement auto 30j avant expiration

---

## ✔️ Question 7 : NAT Port Forwarding (8 pts)

**Énoncé** : Serveur web interne 192.168.1.50:8080 accessible depuis Internet via IP publique 203.0.113.5:80. Config pfSense ?

**Réponse modèle** (8 pts) :

**Port Forward** :
```
Firewall → NAT → Port Forward → Add
  Interface: WAN
  Protocol: TCP
  Destination: WAN address
  Destination port: 80
  Redirect target IP: 192.168.1.50
  Redirect target port: 8080
  Description: Serveur Web interne
  NAT reflection: Enable
```

**Règle pare-feu auto-créée** :
```
Interface: WAN
Action: Pass
Protocol: TCP
Source: Any
Destination: 192.168.1.50
Port: 8080
```

**Test externe** :
```bash
curl http://203.0.113.5
# Doit retourner page web serveur 192.168.1.50:8080
```

---

## ✔️ Question 8 : Double authentification VPN (7 pts)

**Énoncé** : Implémentez 2FA OpenVPN avec Google Authenticator. Étapes configuration ?

**Réponse modèle** (7 pts) :

**Serveur pfSense** (4 pts) :
```
1. System → Package Manager → Installer "freeradius3"

2. Services → FreeRADIUS → Users
   Add User:
     Username: jdupont
     Password: <mot_passe>
     ☑ One-Time Password: Google Authenticator
     
3. VPN → OpenVPN → Servers → Edit
   Backend for authentication: FreeRADIUS
   
4. Générer QR code → Scanner avec Google Authenticator mobile
```

**Client** (3 pts) :
```
Connexion OpenVPN :
  Username: jdupont
  Password: <mot_passe><code_6_chiffres>
  
Exemple:
  MDP: MonMotDePasse123
  Code OTP: 847562
  → Saisir: MonMotDePasse123847562
```

**Avantages** :
- Protection même si MDP compromis
- Code change toutes les 30s
- Conformité sécurité renforcée

---

## BARÈME : 70 POINTS

**Note /20** : Total / 3.5

---

**CONCEPTS CLÉS** :
- DMZ isole serveurs exposés
- VPN IPsec Phase1 (tunnel) + Phase2 (trafic)
- IPS = détection + action automatique
- VLAN segmente logiquement
- 2FA = sécurité renforcée

**Date révision** : Décembre 2025
