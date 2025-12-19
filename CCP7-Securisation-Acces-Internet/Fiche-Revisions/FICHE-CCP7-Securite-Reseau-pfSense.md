# FICHE REVISION CCP7 - SÉCURITÉ RÉSEAU

**Certification TSSR** - CCP7 : Sécurisation accès Internet

---

## 1. PARE-FEU pfSense

### Règles filtrage
**Ordre évaluation** : Haut → Bas (première correspondance appliquée)

**Action** :
- **Pass** : Autoriser
- **Block** : Bloquer silencieux
- **Reject** : Bloquer + message ICMP

**Exemple règle** :
```
Interface: WAN
Action: Block
Protocol: TCP
Source: Any
Destination: LAN net
Port: 22 (SSH)
Description: Bloquer SSH externe
```

### Interfaces
- **WAN** : Internet (publique)
- **LAN** : Réseau local (privé)
- **DMZ** : Zone démilitarisée (serveurs publics)
- **OPT** : Interfaces optionnelles

---

## 2. NAT (Network Address Translation)

### NAT sortant (Outbound)
```
LAN (192.168.1.0/24) → WAN (IP publique)
PC 192.168.1.10:5000 → Internet 203.0.113.5:5000
```

### NAT entrant (Port Forwarding)
```
WAN:80 → Serveur Web DMZ 192.168.2.10:80
WAN:443 → Serveur Web DMZ 192.168.2.10:443
```

**Configuration pfSense** :
```
Firewall → NAT → Port Forward
Interface: WAN
Protocol: TCP
Destination port: 80
Redirect target IP: 192.168.2.10
Redirect target port: 80
```

---

## 3. VPN (Virtual Private Network)

### Types VPN

**Site-to-Site** :
- Connecte 2 réseaux (Siège ↔ Agence)
- IPsec ou OpenVPN
- Transparent utilisateurs

**Remote Access** :
- Utilisateurs nomades → LAN entreprise
- OpenVPN (client requis)
- Accès ressources internes

### OpenVPN pfSense
```
VPN → OpenVPN → Wizards → Remote Access (SSL/TLS)
  Server mode: Remote Access (SSL/TLS)
  Protocol: UDP, Port: 1194
  Tunnel network: 10.0.8.0/24
  DNS servers: 192.168.1.1 (DNS interne)
```

**Exporter config client** : Client Export Utility

---

## 4. VLAN (Virtual LAN)

### Principe
Segmentation logique réseau sur switch physique

**Exemples** :
- VLAN 10 : Administration
- VLAN 20 : Utilisateurs
- VLAN 30 : Invités (Wi-Fi)

### Configuration switch
```
Interface Fa0/1 : Access VLAN 10
Interface Fa0/24 : Trunk (802.1Q) → pfSense
```

### pfSense VLAN
```
Interfaces → Assignments → VLANs
  Parent: em0
  VLAN Tag: 10
  Description: Admin
```

---

## 5. PROXY Squid

### Filtrage contenu
- Listes noires (blacklist) : Bloquer sites
- ACL (Access Control List) : Autoriser/refuser IP/domaines

**Exemple ACL** :
```
acl reseau_interne src 192.168.1.0/24
acl sites_bloques dstdomain .facebook.com .twitter.com

http_access deny sites_bloques
http_access allow reseau_interne
http_access deny all
```

### Proxy transparent
Interception automatique HTTP (pas config navigateur)

---

## 6. IDS/IPS Suricata

**IDS (Intrusion Detection)** : Détecte + alerte  
**IPS (Intrusion Prevention)** : Détecte + bloque

**Règles Suricata** :
```
alert tcp any any -> $HOME_NET 22 (msg:"Scan SSH"; sid:1000001;)
drop tcp any any -> $HOME_NET 445 (msg:"Attaque SMB"; sid:1000002;)
```

**Installation pfSense** :
```
System → Package Manager → Available Packages → Suricata
Services → Suricata → Interfaces → Add → WAN
```

---

## 7. DMZ (Zone Démilitarisée)

### Architecture
```
Internet → Pare-feu → DMZ (serveurs publics)
                   └→ LAN (postes internes)
```

**Règles** :
- WAN → DMZ : Ports 80/443 autorisés
- DMZ → LAN : **Bloqué** (sécurité)
- LAN → DMZ : Autorisé (admin)

---

## 8. CERTIFICATS SSL/TLS

### Utilisation
- HTTPS (chiffrement web)
- VPN OpenVPN
- Emails sécurisés

### Générer certificat pfSense
```
System → Cert Manager → CAs → Add (Autorité Certification)
System → Cert Manager → Certificates → Add
  Method: Create Internal Certificate
  Common Name: vpn.entreprise.local
```

**Let's Encrypt** (gratuit, public) : Renouvellement auto 90j

---

## 9. PROTOCOLES SÉCURISÉS

| Non sécurisé | Sécurisé | Port |
|--------------|----------|------|
| HTTP | **HTTPS** | 443 |
| FTP | **SFTP/FTPS** | 22/990 |
| Telnet | **SSH** | 22 |
| SMTP | **SMTPS** | 465/587 |
| SNMP v1/v2 | **SNMP v3** | 161 |

---

## 10. BONNES PRATIQUES

### Règles pare-feu
- ✅ **Moindre privilège** : Bloquer par défaut, autoriser sélectivement
- ✅ **Logs** : Activer journalisation règles critiques
- ✅ **Alias** : Grouper IP/ports (facilite gestion)
- ❌ **Any/Any** : Éviter règles trop permissives

### Mots de passe
- 12 caractères minimum
- Complexité (Maj+min+chiffres+symboles)
- Changement régulier (90j)
- Double authentification (2FA)

### Mises à jour
- Firmware pare-feu mensuel
- Correctifs OS critiques (7j max)
- Signatures IDS/IPS quotidiennes

---

## COMMANDES pfSense (Shell)

```bash
# Redémarrer services
pfctl -d           # Désactiver pare-feu (urgent)
pfctl -e           # Activer pare-feu
/etc/rc.reload_all # Recharger config

# Logs
clog /var/log/filter.log | tail -50   # 50 dernières lignes
tcpdump -i em0 port 80                # Capture trafic HTTP

# Interfaces
ifconfig em0       # Info interface WAN
pfctl -s states    # États connexions actives
```

---

## SCÉNARIOS EXAMEN

**Scénario 1** : Bloquer Facebook pour utilisateurs  
→ Proxy Squid ACL + blacklist

**Scénario 2** : Accès serveur web DMZ depuis Internet  
→ NAT Port Forward WAN:80 → DMZ:192.168.2.10:80

**Scénario 3** : VPN utilisateur nomade  
→ OpenVPN Remote Access, exporter config client

**Scénario 4** : Séparer Admin/Users/Invités  
→ VLANs 10/20/30 sur switch + interfaces pfSense

**Scénario 5** : Détecter attaques réseau  
→ IPS Suricata sur WAN, règles ET Open

---

**Date révision** : 12 novembre 2025  
**Mots-clés** : pfSense, NAT, VPN, VLAN, DMZ, IDS/IPS, Proxy