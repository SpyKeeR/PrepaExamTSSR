# QCM CCP7 - SÉCURITÉ RÉSEAUX & INTERCONNEXIONS

**Certification TSSR** - CCP7 : Maintenir et sécuriser accès Internet  
**25 questions essentielles**

---

### Q1 : Pare-feu règles
Dans quel ordre un pare-feu évalue-t-il ses règles de filtrage ?
- [ ] A) Alphabétique
- [ ] B) Haut vers bas, première correspondance appliquée 
- [ ] C) Plus restrictive toujours prioritaire
- [ ] D) Aléatoire

---

### Q2 : NAT sortant
Quel est le rôle du NAT sortant (SNAT) ?
- [ ] A) Traduit IP publique → privée
- [ ] B) Traduit IP privée → publique 
- [ ] C) Bloque trafic sortant
- [ ] D) Chiffre données

---

### Q3 : Port Forwarding
Vous devez rediriger le port WAN:80 vers un serveur en DMZ (10.0.1.50:8080). Quel type de règle NAT devez-vous configurer ?
- [ ] A) Destination NAT (DNAT) 
- [ ] B) Source NAT
- [ ] C) PAT
- [ ] D) Static NAT

---

### Q4 : DMZ utilité
Quelle est la raison principale de placer un serveur web dans une DMZ ?
- [ ] A) Performances accrues
- [ ] B) Isoler des ressources internes sensibles 
- [ ] C) Économie coûts
- [ ] D) Simplifier administration

---

### Q5 : VPN Site-to-Site
Quelle est la principale différence entre un VPN Site-to-Site et un VPN Remote Access ?
- [ ] A) Aucune
- [ ] B) Site-to-Site connecte 2 réseaux, Remote Access connecte utilisateur nomade 
- [ ] C) Site-to-Site plus lent
- [ ] D) Remote Access nécessite matériel spécial

---

### Q6 : Protocole VPN
Parmi les protocoles VPN suivants, lequel est considéré comme le plus sécurisé ?
- [ ] A) PPTP
- [ ] B) L2TP/IPsec 
- [ ] C) GRE
- [ ] D) SSTP

---

### Q7 : VLAN avantage
Quel est le principal avantage de l'utilisation des VLANs ?
- [ ] A) Augmente vitesse réseau
- [ ] B) Segmentation logique sur même switch physique 
- [ ] C) Remplace routeur
- [ ] D) Chiffre données

---

### Q8 : Trunk 802.1Q
Que transporte un port configuré en mode Trunk ?
- [ ] A) Un seul VLAN
- [ ] B) Plusieurs VLANs taggués 
- [ ] C) Uniquement management
- [ ] D) Trafic non VLAN

---

### Q9 : Proxy transparent
Quelle est la différence entre un proxy standard et un proxy transparent ?
- [ ] A) Transparent chiffre, standard non
- [ ] B) Transparent intercepte trafic sans config navigateur 
- [ ] C) Standard plus rapide
- [ ] D) Aucune

---

### Q10 : ACL Squid
La syntaxe suivante pour bloquer Facebook via Squid est-elle correcte ?
```
acl facebook dstdomain .facebook.com
http_access deny facebook 
```
- [ ] A) Correct 
- [ ] B) Inverser deny/allow
- [ ] C) Utiliser src au lieu dstdomain
- [ ] D) Erreur syntaxe

---

### Q11 : IDS vs IPS
Quelle est la différence fondamentale entre un IDS et un IPS ?
- [ ] A) IDS détecte, IPS détecte + bloque 
- [ ] B) Aucune
- [ ] C) IDS matériel, IPS logiciel
- [ ] D) IPS détecte, IDS bloque

---

### Q12 : Suricata mode
Quels sont les modes de fonctionnement disponibles pour Suricata ?
- [ ] A) IDS uniquement
- [ ] B) IPS uniquement
- [ ] C) IDS et IPS 
- [ ] D) Firewall uniquement

---

### Q13 : Certificat SSL
Quel est le rôle principal d'un certificat SSL dans une connexion HTTPS ?
- [ ] A) Vitesse accrue
- [ ] B) Chiffrement données + authentification serveur 
- [ ] C) SEO uniquement
- [ ] D) Cache amélioré

---

### Q14 : Port HTTPS
Quel est le port par défaut utilisé par le protocole HTTPS ?
- [ ] A) 80
- [ ] B) 443 
- [ ] C) 8080
- [ ] D) 22

---

### Q15 : Let's Encrypt
Quelle est la principale caractéristique de l'autorité de certification Let's Encrypt ?
- [ ] A) Certificats payants
- [ ] B) Certificats gratuits, renouvellement auto 90j 
- [ ] C) Wildcard non supporté
- [ ] D) Uniquement intranets

---

### Q16 : Protocole sécurisé
Par quel protocole sécurisé devez-vous remplacer Telnet pour l'administration à distance ?
- [ ] A) FTP
- [ ] B) SSH 
- [ ] C) HTTP
- [ ] D) SNMP

---

### Q17 : SNMP v3
Quel est l'avantage principal de SNMP v3 par rapport aux versions v1/v2c ?
- [ ] A) Plus rapide
- [ ] B) Authentification + chiffrement 
- [ ] C) Moins de commandes
- [ ] D) Compatible tous équipements

---

### Q18 : Règle pare-feu moindre privilège
En quoi consiste le principe du moindre privilège appliqué à un pare-feu ?
- [ ] A) Tout autoriser par défaut
- [ ] B) Tout bloquer par défaut, autoriser sélectivement 
- [ ] C) Pas de règles
- [ ] D) Autoriser selon utilisateur uniquement

---

### Q19 : Logs pare-feu
Quelle est la raison principale d'activer la journalisation (logs) des règles de pare-feu ?
- [ ] A) Performance
- [ ] B) Audit sécurité + forensic incidents 
- [ ] C) Obligation légale uniquement
- [ ] D) Économie bande passante

---

### Q20 : Alias pfSense
Quelle est l'utilité des Alias dans pfSense ?
- [ ] A) Créer VLANs
- [ ] B) Grouper IPs/ports pour règles simplifiées 
- [ ] C) Chiffrer trafic
- [ ] D) Accélérer routage

---

### Q21 : Attaque DDoS
Qu'est-ce qu'une attaque DDoS (Distributed Denial of Service) ?
- [ ] A) Vol données
- [ ] B) Saturation serveur par trafic massif 
- [ ] C) Phishing
- [ ] D) Ransomware

---

### Q22 : Whitelisting vs Blacklisting
Quelle approche de filtrage est considérée comme la plus sécurisée ?
- [ ] A) Blacklist (bloquer connus malveillants)
- [ ] B) Whitelist (autoriser uniquement connus légitimes) 
- [ ] C) Équivalent
- [ ] D) Aucune

---

### Q23 : Spanning Tree Protocol
Quel est le rôle principal du protocole STP (Spanning Tree Protocol) sur les switchs ?
- [ ] A) Routage inter-VLAN
- [ ] B) Prévenir boucles réseau 
- [ ] C) Chiffrement
- [ ] D) Load balancing

---

### Q24 : ACL routeur
Quelle est la différence entre une ACL standard et une ACL étendue ?
- [ ] A) Standard filtre IP source uniquement, étendue ajoute destination/port/protocole 
- [ ] B) Aucune différence
- [ ] C) Standard plus sécurisée
- [ ] D) Étendue uniquement Cisco

---

### Q25 : MTU
Que signifie un MTU (Maximum Transmission Unit) de 1500 ?
- [ ] A) Vitesse 1500 Mbps
- [ ] B) Taille maximale trame Ethernet = 1500 octets 
- [ ] C) 1500 utilisateurs max
- [ ] D) Distance 1500m

