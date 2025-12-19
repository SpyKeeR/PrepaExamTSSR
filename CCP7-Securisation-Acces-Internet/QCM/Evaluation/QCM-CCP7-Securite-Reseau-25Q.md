# QCM CCP7 - SÉCURITÉ RÉSEAUX & INTERCONNEXIONS

**Certification TSSR** - CCP7 : Maintenir et sécuriser accès Internet  
**25 questions essentielles**

---

### Q1 : Pare-feu règles
Ordre d'évaluation règles pare-feu ?
- [ ] A) Alphabétique
- [ ] B) Haut vers bas, première correspondance appliquée 
- [ ] C) Plus restrictive toujours prioritaire
- [ ] D) Aléatoire

---

### Q2 : NAT sortant
Que fait NAT sortant (SNAT) ?
- [ ] A) Traduit IP publique → privée
- [ ] B) Traduit IP privée → publique 
- [ ] C) Bloque trafic sortant
- [ ] D) Chiffre données

---

### Q3 : Port Forwarding
Rediriger port WAN:80 vers serveur DMZ 10.0.1.50:8080, quelle règle NAT ?
- [ ] A) Destination NAT (DNAT) 
- [ ] B) Source NAT
- [ ] C) PAT
- [ ] D) Static NAT

---

### Q4 : DMZ utilité
Pourquoi placer serveur web dans DMZ ?
- [ ] A) Performances accrues
- [ ] B) Isoler des ressources internes sensibles 
- [ ] C) Économie coûts
- [ ] D) Simplifier administration

---

### Q5 : VPN Site-to-Site
Différence VPN Site-to-Site vs Remote Access ?
- [ ] A) Aucune
- [ ] B) Site-to-Site connecte 2 réseaux, Remote Access connecte utilisateur nomade 
- [ ] C) Site-to-Site plus lent
- [ ] D) Remote Access nécessite matériel spécial

---

### Q6 : Protocole VPN
Quel protocole VPN le plus sécurisé ?
- [ ] A) PPTP
- [ ] B) L2TP/IPsec 
- [ ] C) GRE
- [ ] D) SSTP

---

### Q7 : VLAN avantage
Principal avantage VLANs ?
- [ ] A) Augmente vitesse réseau
- [ ] B) Segmentation logique sur même switch physique 
- [ ] C) Remplace routeur
- [ ] D) Chiffre données

---

### Q8 : Trunk 802.1Q
Que transporte port Trunk ?
- [ ] A) Un seul VLAN
- [ ] B) Plusieurs VLANs taggués 
- [ ] C) Uniquement management
- [ ] D) Trafic non VLAN

---

### Q9 : Proxy transparent
Différence proxy standard vs transparent ?
- [ ] A) Transparent chiffre, standard non
- [ ] B) Transparent intercepte trafic sans config navigateur 
- [ ] C) Standard plus rapide
- [ ] D) Aucune

---

### Q10 : ACL Squid
Syntaxe bloquer Facebook via Squid ?
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
Différence IDS/IPS ?
- [ ] A) IDS détecte, IPS détecte + bloque 
- [ ] B) Aucune
- [ ] C) IDS matériel, IPS logiciel
- [ ] D) IPS détecte, IDS bloque

---

### Q12 : Suricata mode
Modes fonctionnement Suricata ?
- [ ] A) IDS uniquement
- [ ] B) IPS uniquement
- [ ] C) IDS et IPS 
- [ ] D) Firewall uniquement

---

### Q13 : Certificat SSL
Pourquoi certificat SSL HTTPS ?
- [ ] A) Vitesse accrue
- [ ] B) Chiffrement données + authentification serveur 
- [ ] C) SEO uniquement
- [ ] D) Cache amélioré

---

### Q14 : Port HTTPS
Port par défaut HTTPS ?
- [ ] A) 80
- [ ] B) 443 
- [ ] C) 8080
- [ ] D) 22

---

### Q15 : Let's Encrypt
Caractéristique Let's Encrypt ?
- [ ] A) Certificats payants
- [ ] B) Certificats gratuits, renouvellement auto 90j 
- [ ] C) Wildcard non supporté
- [ ] D) Uniquement intranets

---

### Q16 : Protocole sécurisé
Remplacer Telnet (non sécurisé) par ?
- [ ] A) FTP
- [ ] B) SSH 
- [ ] C) HTTP
- [ ] D) SNMP

---

### Q17 : SNMP v3
Avantage SNMP v3 vs v1/v2c ?
- [ ] A) Plus rapide
- [ ] B) Authentification + chiffrement 
- [ ] C) Moins de commandes
- [ ] D) Compatible tous équipements

---

### Q18 : Règle pare-feu moindre privilège
Principe moindre privilège pare-feu ?
- [ ] A) Tout autoriser par défaut
- [ ] B) Tout bloquer par défaut, autoriser sélectivement 
- [ ] C) Pas de règles
- [ ] D) Autoriser selon utilisateur uniquement

---

### Q19 : Logs pare-feu
Pourquoi activer logs règles ?
- [ ] A) Performance
- [ ] B) Audit sécurité + forensic incidents 
- [ ] C) Obligation légale uniquement
- [ ] D) Économie bande passante

---

### Q20 : Alias pfSense
Utilité Alias pfSense ?
- [ ] A) Créer VLANs
- [ ] B) Grouper IPs/ports pour règles simplifiées 
- [ ] C) Chiffrer trafic
- [ ] D) Accélérer routage

---

### Q21 : Attaque DDoS
Qu'est-ce qu'attaque DDoS ?
- [ ] A) Vol données
- [ ] B) Saturation serveur par trafic massif 
- [ ] C) Phishing
- [ ] D) Ransomware

---

### Q22 : Whitelisting vs Blacklisting
Approche plus sécurisée ?
- [ ] A) Blacklist (bloquer connus malveillants)
- [ ] B) Whitelist (autoriser uniquement connus légitimes) 
- [ ] C) Équivalent
- [ ] D) Aucune

---

### Q23 : Spanning Tree Protocol
Rôle STP switchs ?
- [ ] A) Routage inter-VLAN
- [ ] B) Prévenir boucles réseau 
- [ ] C) Chiffrement
- [ ] D) Load balancing

---

### Q24 : ACL routeur
ACL standard vs étendue ?
- [ ] A) Standard filtre IP source uniquement, étendue ajoute destination/port/protocole 
- [ ] B) Aucune différence
- [ ] C) Standard plus sécurisée
- [ ] D) Étendue uniquement Cisco

---

### Q25 : MTU
Que signifie MTU 1500 ?
- [ ] A) Vitesse 1500 Mbps
- [ ] B) Taille maximale trame Ethernet = 1500 octets 
- [ ] C) 1500 utilisateurs max
- [ ] D) Distance 1500m

