# CORRECTIONS - QUESTIONS OUVERTES RÉSEAU 
**Certification TSSR** - CCP4 : Services Réseau

---

## ✔️ Question 1 - Modèle OSI

**Énoncé** : Expliquez le rôle des couches 2, 3 et 4 du modèle OSI. Donnez un exemple de protocole et d'équipement pour chacune.

**Réponse modèle** :

**Couche 2 (Liaison de données)** :
- **Rôle** : Transmission des données entre équipements sur le même réseau local, gestion des adresses MAC, détection d'erreurs
- **Protocole** : Ethernet (802.3), Wi-Fi (802.11), PPP
- **Équipement** : Switch
- **Fonction** : Segmente les domaines de collision, maintient la table MAC (CAM table)

**Couche 3 (Réseau)** :
- **Rôle** : Routage des paquets entre réseaux différents, adressage logique (IP)
- **Protocole** : IP (IPv4/IPv6), ICMP, IGMP
- **Équipement** : Routeur
- **Fonction** : Prend des décisions de routage, segmente les domaines de broadcast

**Couche 4 (Transport)** :
- **Rôle** : Transport de bout en bout, segmentation, fiabilité (TCP) ou rapidité (UDP)
- **Protocole** : TCP, UDP
- **Équipement** : Pare-feu stateful, Load Balancer
- **Fonction** : Gestion des ports, contrôle de flux, réassemblage des segments

---

## ✔️ Question 2 - TCP vs UDP

**Énoncé** : Expliquez les différences entre TCP et UDP. Dans quels cas utilise-t-on l'un plutôt que l'autre ? Donnez 3 exemples pour chaque.

**Réponse modèle** :

**TCP (Transmission Control Protocol)** :
- **Caractéristiques** : Orienté connexion, fiable, contrôle de flux, ordre garanti, accusés de réception
- **Avantages** : Garantit la livraison des données
- **Inconvénients** : Plus lent, overhead important (en-têtes, ACK)
- **Cas d'usage** :
  1. HTTP/HTTPS (navigation web) → Besoin de fiabilité
  2. FTP (transfert de fichiers) → Intégrité des données
  3. SMTP (email) → Pas de perte de messages

**UDP (User Datagram Protocol)** :
- **Caractéristiques** : Non connecté, rapide, pas de garantie de livraison, pas d'ordre
- **Avantages** : Faible latence, léger
- **Inconvénients** : Peut perdre des paquets
- **Cas d'usage** :
  1. DNS (résolution de noms) → Rapidité prioritaire, si échec on retente
  2. VoIP/Streaming (voix/vidéo) → Temps réel, quelques pertes acceptables
  3. Jeux en ligne → Faible latence critique

**Règle** : TCP quand on a besoin de fiabilité, UDP quand on a besoin de rapidité.

---

## ✔️ Question 3 - Subnetting pratique

**Énoncé** : Une entreprise dispose du réseau 192.168.10.0/24. Elle doit créer 4 sous-réseaux égaux. Donnez : le nouveau masque CIDR, les 4 plages d'adresses, le nombre d'hôtes par sous-réseau.

**Réponse modèle** :

**Calcul** :
- Réseau initial : 192.168.10.0/24 (256 adresses)
- 4 sous-réseaux → 2^2 = 4 → Besoin de 2 bits supplémentaires
- Nouveau masque : /24 + 2 = **/26**

**Masque /26** :
- 255.255.255.192
- Bloc de 64 adresses par sous-réseau
- Hôtes utilisables : 64 - 2 = **62 hôtes**

**Les 4 sous-réseaux** :

| Sous-réseau | Plage | Réseau | Broadcast | Hôtes utilisables |
|-------------|-------|--------|-----------|-------------------|
| 1 | 192.168.10.0/26 | 192.168.10.0 | 192.168.10.63 | 10.1 - 10.62 |
| 2 | 192.168.10.64/26 | 192.168.10.64 | 192.168.10.127 | 10.65 - 10.126 |
| 3 | 192.168.10.128/26 | 192.168.10.128 | 192.168.10.191 | 10.129 - 10.190 |
| 4 | 192.168.10.192/26 | 192.168.10.192 | 192.168.10.255 | 10.193 - 10.254 |

**Résumé** :
- Nouveau CIDR : **/26**
- Hôtes par sous-réseau : **62**
- Total d'adresses par sous-réseau : **64**

---

## ✔️ Question 4 - VLAN

**Énoncé** : Expliquez la différence entre un port Access et un port Trunk. Donnez un exemple d'utilisation pour chacun.

**Réponse modèle** :

**Port Access** :
- **Configuration** : `switchport mode access` + `switchport access vlan X`
- **Fonction** : Appartient à **UN SEUL VLAN**
- **Trames** : Non étiquetées (untagged)
- **Usage** : Connexion d'équipements finaux (PC, imprimante, téléphone IP)
- **Exemple** : Port connecté à un PC du service RH → VLAN 10

**Port Trunk** :
- **Configuration** : `switchport mode trunk` + `switchport trunk allowed vlan X,Y,Z`
- **Fonction** : Transporte **PLUSIEURS VLAN** simultanément
- **Trames** : Étiquetées avec tag 802.1Q (sauf VLAN natif)
- **Usage** : Liaison entre switchs ou entre switch et routeur
- **Exemple** : Lien entre switch d'étage et switch coeur de réseau transportant VLAN 10, 20, 30

**Différence clé** : Access = 1 VLAN, Trunk = plusieurs VLAN

---

## ✔️ Question 5 - Protocole ARP

**Énoncé** : Expliquez le fonctionnement du protocole ARP. Quelle est la commande pour afficher la table ARP sur Windows et sur Linux ?

**Réponse modèle** :

**Fonctionnement ARP** :
1. **Problème** : Machine A (192.168.1.10) veut communiquer avec Machine B (192.168.1.20) mais ne connaît que son IP
2. **ARP Request** : A envoie un broadcast "Qui a l'IP 192.168.1.20 ? Donne-moi ton adresse MAC !"
3. **ARP Reply** : B répond en unicast "C'est moi ! Mon MAC est AA:BB:CC:DD:EE:FF"
4. **Mise en cache** : A stocke l'association IP ↔ MAC dans sa table ARP
5. **Communication** : A peut maintenant envoyer des trames Ethernet à B

**Commandes pour afficher la table ARP** :

**Windows** :
```cmd
arp -a
```

**Linux** :
```bash
arp -n
# ou
ip neigh show
```

**Note** : La table ARP a une durée de vie limitée (timeout), généralement quelques minutes.

---

## ✔️ Question 6 - DHCP

**Énoncé** : Expliquez le processus DORA du protocole DHCP. Quels ports UDP sont utilisés ?

**Réponse modèle** :

**Processus DORA** :

**1. DISCOVER (Découverte)** :
- Client envoie un **broadcast** : "Je cherche un serveur DHCP !"
- Source : 0.0.0.0, Destination : 255.255.255.255
- Port : UDP 68 → UDP 67

**2. OFFER (Offre)** :
- Serveur répond : "Voici une adresse IP disponible : 192.168.1.100"
- Propose aussi masque, passerelle, DNS, durée de bail
- Envoi en broadcast (ou unicast selon config)

**3. REQUEST (Requête)** :
- Client accepte l'offre en broadcast : "J'accepte l'IP 192.168.1.100"
- Broadcast car plusieurs serveurs ont pu répondre

**4. ACKNOWLEDGMENT (Accusé de réception)** :
- Serveur confirme : "OK, l'IP 192.168.1.100 est à toi pour X heures"
- Client configure son interface réseau

**Ports UDP utilisés** :
- Serveur DHCP : **UDP 67**
- Client DHCP : **UDP 68**

**Renouvellement** : À 50% du bail, le client tente de renouveler (DHCP REQUEST direct au serveur).

---

## ✔️ Question 7 - Routage statique

**Énoncé** : Sur un routeur Cisco, vous devez créer une route statique pour que le réseau 192.168.2.0/24 passe par la passerelle 10.0.0.2. Quelle commande utilisez-vous ? Expliquez chaque paramètre.

**Réponse modèle** :

**Commande Cisco** :
```
R1(config)# ip route 192.168.2.0 255.255.255.0 10.0.0.2
```

**Explication des paramètres** :
- `ip route` : Commande de création de route statique
- `192.168.2.0` : Réseau de destination
- `255.255.255.0` : Masque de sous-réseau du réseau destination (/24)
- `10.0.0.2` : Adresse IP du prochain saut (next-hop) ou interface de sortie

**Signification** : "Pour atteindre le réseau 192.168.2.0/24, envoie les paquets vers 10.0.0.2"

**Variantes** :
```
# Via interface de sortie
ip route 192.168.2.0 255.255.255.0 GigabitEthernet0/1

# Route par défaut
ip route 0.0.0.0 0.0.0.0 10.0.0.1

# Avec distance administrative (priorité)
ip route 192.168.2.0 255.255.255.0 10.0.0.2 10
```

**Vérification** :
```
show ip route
show ip route static
```

---

## ✔️ Question 8 - Dépannage réseau

**Énoncé** : Un utilisateur ne peut pas accéder à Internet. Décrivez les 5 premières étapes de dépannage que vous effectueriez (commandes à utiliser incluses).

**Réponse modèle** :

**Les 5 premières étapes** :

**1. Vérifier la couche physique** :
- Câble réseau branché ?
- Voyant de la carte réseau allumé ?
- Commande Windows : `ipconfig` → "Média déconnecté" ?
- Commande Linux : `ip link show` → État UP ?

**2. Vérifier la configuration IP** :
```cmd
Windows : ipconfig /all
Linux : ip addr show
```
- IP correcte (pas 169.254.x.x = APIPA) ?
- Masque correct ?
- Passerelle configurée ?

**3. Tester la connectivité locale (passerelle)** :
```cmd
ping 192.168.1.1
```
- Si échec → Problème local (câble, switch, VLAN)
- Si succès → Problème au-delà du réseau local

**4. Tester la résolution DNS** :
```cmd
nslookup www.google.com
ping 8.8.8.8
```
- Si ping IP fonctionne mais pas nslookup → Problème DNS
- Vérifier les serveurs DNS : `ipconfig /all`

**5. Vérifier le routage** :
```cmd
Windows : tracert www.google.com
Linux : traceroute www.google.com
```
- Où les paquets sont-ils bloqués ?
- Vérifier pare-feu local : `netsh advfirewall show allprofiles`

**Méthodologie** : Approche en couches (OSI) de bas en haut.

---

## ✔️ Question 9 - NAT/PAT

**Énoncé** : Expliquez la différence entre NAT et PAT. Pourquoi utilise-t-on majoritairement le PAT dans les réseaux d'entreprise ?

**Réponse modèle** :

**NAT (Network Address Translation)** :
- **NAT statique** : 1 IP privée ↔ 1 IP publique (mapping permanent)
- **NAT dynamique** : Pool d'IPs publiques (mapping temporaire)
- **Limitation** : Besoin d'autant d'IPs publiques que de connexions simultanées

**Exemple NAT statique** :
```
192.168.1.10 → 200.50.100.10 (toujours)
192.168.1.20 → 200.50.100.11 (toujours)
```

**PAT (Port Address Translation)** = NAT overload :
- **Principe** : Plusieurs IPs privées → 1 seule IP publique
- **Différenciation** : Utilise les ports sources
- **Avantage** : Économie d'adresses IPv4 publiques

**Exemple PAT** :
```
192.168.1.10:50000 → 200.50.100.1:50000
192.168.1.20:50001 → 200.50.100.1:50001
192.168.1.30:50002 → 200.50.100.1:50002
```

**Pourquoi PAT est majoritaire** :
- **Pénurie d'IPv4** : Peu d'IPs publiques disponibles
- **Coût** : IPs publiques coûteuses
- **Simplicité** : 1 seule IP publique suffit pour toute l'entreprise
- **Sécurité** : Machines internes non directement accessibles

**Usage NAT statique** : Serveurs publics (web, mail) ayant besoin d'une IP fixe.

---

## ✔️ Question 10 - DNS

**Énoncé** : Un utilisateur tape www.google.com dans son navigateur. Expliquez le processus de résolution DNS étape par étape.

**Réponse modèle** :

**Processus de résolution DNS** :

**1. Vérification du cache local** :
- Le navigateur vérifie son cache DNS
- Si trouvé → Utilise l'IP en cache
- Commande : `ipconfig /displaydns` (Windows)

**2. Requête au resolver DNS** :
- Si pas en cache, le PC contacte son serveur DNS configuré (ex: 8.8.8.8)
- Requête : "Quelle est l'IP de www.google.com ?"

**3. Vérification cache du resolver** :
- Le resolver vérifie son cache
- Si trouvé → Renvoie l'IP au client

**4. Résolution récursive (si pas en cache)** :

a) **Serveur racine (Root server)** :
- Resolver contacte un serveur racine (13 clusters)
- "Je cherche www.google.com, qui gère .com ?"
- Réponse : Adresse des serveurs TLD .com

b) **Serveur TLD (Top Level Domain)** :
- Resolver contacte serveur TLD .com
- "Qui gère google.com ?"
- Réponse : Adresse des serveurs NS de google.com

c) **Serveur autoritaire** :
- Resolver contacte le serveur NS de google.com
- "Quelle est l'IP de www.google.com ?"
- Réponse : 142.250.185.206

**5. Retour au client** :
- Le resolver renvoie l'IP au PC
- Mise en cache (TTL = durée de vie)

**6. Connexion HTTP** :
- Le navigateur se connecte à 142.250.185.206:80 ou :443

**Types d'enregistrements DNS** :
- **A** : IPv4
- **AAAA** : IPv6
- **CNAME** : Alias
- **MX** : Serveur mail
- **NS** : Serveur de noms

---

### ✔️ Question 11 - VPN IPsec

**Réponse modèle** :

**Mode Transport** :
- **Chiffrement** : Seulement les **données** (payload) du paquet IP
- **En-tête IP** : Reste en clair (adresses source/destination visibles)
- **Usage** : Communication de **bout en bout** entre 2 hôtes
- **Exemple** : Client VPN vers serveur distant (accès à distance)
- **Avantage** : Moins d'overhead (en-tête IP original conservé)

**Mode Tunnel** :
- **Chiffrement** : **Tout le paquet IP** (en-tête + données)
- **Nouvel en-tête IP** : Ajouté par les passerelles VPN
- **Usage** : VPN **site-à-site** entre routeurs/pare-feu
- **Exemple** : Connexion siège ↔ agence
- **Avantage** : Sécurité maximale (tout est chiffré)

**Choix** :
- **Transport** → Accès distant (road warriors)
- **Tunnel** → Site-à-site (interconnexion de réseaux)

**Schéma conceptuel** :
```
Transport : [En-tête IP original][Données chiffrées]
Tunnel    : [Nouvel en-tête IP][En-tête IP original chiffré][Données chiffrées]
```

---

### ✔️ Question 12 - Protocoles de routage

**Réponse modèle** :

**RIP (Routing Information Protocol)** :
- **Métrique** : Nombre de sauts (hop count)
- **Limite** : Maximum 15 sauts (16 = infini)
- **Convergence** : **Lente** (30-60 secondes)
- **Mises à jour** : Toutes les 30 secondes (broadcast/multicast)
- **Bande passante** : Ne prend PAS en compte
- **Cas d'usage** : Petits réseaux simples (<10 routeurs)

**OSPF (Open Shortest Path First)** :
- **Métrique** : Coût basé sur la **bande passante**
- **Limite** : Aucune limite de sauts
- **Convergence** : **Rapide** (<5 secondes)
- **Mises à jour** : Événementielles (seulement si changement)
- **Hiérarchie** : Supporte les areas (scalabilité)
- **Cas d'usage** : Réseaux moyens à grands

**Recommandation pour 500 postes / 3 sites** :
→ **OSPF**

**Justification** :
- Convergence rapide (critique pour 500 utilisateurs)
- Supporte VLSM et CIDR
- Scalable (pas de limite de sauts)
- Métrique intelligente (bande passante)
- Standard ouvert (interopérabilité)

**Configuration minimale OSPF** :
```
R1(config)# router ospf 1
R1(config-router)# network 192.168.1.0 0.0.0.255 area 0
```

---

### ✔️ Question 13 - ACL Cisco

**Réponse modèle** :

**Commandes ACL étendue** :

```cisco
! 1. Créer l'ACL
R1(config)# access-list 100 deny tcp 192.168.1.0 0.0.0.255 any eq 80
R1(config)# access-list 100 permit ip 192.168.1.0 0.0.0.255 any

! 2. Appliquer l'ACL sur l'interface sortante (vers Internet)
R1(config)# interface GigabitEthernet0/1
R1(config-if)# ip access-group 100 out
```

**Explication ligne par ligne** :

**Ligne 1** : `access-list 100 deny tcp 192.168.1.0 0.0.0.255 any eq 80`
- `100` : Numéro ACL étendue (100-199)
- `deny` : Bloquer
- `tcp` : Protocole TCP
- `192.168.1.0 0.0.0.255` : Réseau source avec wildcard mask
- `any` : N'importe quelle destination
- `eq 80` : Port destination égal à 80 (HTTP)

**Ligne 2** : `access-list 100 permit ip 192.168.1.0 0.0.0.255 any`
- `permit ip` : Autoriser tout le reste (tout protocole IP)
- **Important** : Sans cette ligne, tout serait bloqué (implicit deny)

**Application** :
- `ip access-group 100 out` : Applique l'ACL en sortie de l'interface
- Direction : `out` car on filtre le trafic qui sort vers Internet

**Vérification** :
```cisco
show access-lists
show ip interface GigabitEthernet0/1
```

**Alternative avec ACL nommée** :
```cisco
ip access-list extended BLOCK_HTTP
 deny tcp 192.168.1.0 0.0.0.255 any eq 80
 permit ip any any
interface GigabitEthernet0/1
 ip access-group BLOCK_HTTP out
```

---

### ✔️ Question 14 - IPv6

**Réponse modèle** :

**Différences IPv4 vs IPv6** :

| Caractéristique | IPv4 | IPv6 |
|-----------------|------|------|
| **Taille adresse** | 32 bits | 128 bits |
| **Format** | Décimal (4 octets) | Hexadécimal (8 groupes) |
| **Nombre d'adresses** | ~4 milliards | 340 sextillions |
| **Notation** | 192.168.1.1 | 2001:db8::1 |
| **Broadcast** | Oui | Non (remplacé par multicast) |
| **Fragmentation** | Routeurs + hôtes | Seulement hôtes |
| **NAT** | Nécessaire | Optionnel |
| **Configuration** | DHCP ou manuelle | SLAAC, DHCPv6 ou manuelle |
| **ARP** | ARP | NDP (Neighbor Discovery) |

**Types d'adresses IPv6** :

**1. Unicast** :
- **Global Unicast** : 2000::/3 (équivalent IP publiques)
- **Link-Local** : FE80::/10 (communication sur le lien local uniquement)
- **Unique Local** : FC00::/7 (équivalent IP privées)
- **Loopback** : ::1 (équivalent 127.0.0.1)

**2. Multicast** :
- **Plage** : FF00::/8
- **FF02::1** : Tous les nœuds du lien local
- **FF02::2** : Tous les routeurs du lien local
- **Usage** : Remplace le broadcast IPv4

**3. Anycast** :
- Adresse assignée à plusieurs interfaces
- Le paquet va à l'interface la plus proche

**Pas d'adresse broadcast** en IPv6 (remplacé par multicast)

**Exemple d'adresse** :
```
2001:0db8:85a3:0000:0000:8a2e:0370:7334
Simplifié : 2001:db8:85a3::8a2e:370:7334
```

---

### ✔️ Question 15 - Pare-feu

**Réponse modèle** :

**Pare-feu Stateless** :
- **Principe** : Filtre **paquet par paquet** indépendamment
- **Analyse** : Uniquement les en-têtes (IP source/dest, ports, protocole)
- **État** : Ne garde **aucun contexte** des connexions
- **Performance** : Rapide (peu de mémoire)
- **Sécurité** : Faible (pas de suivi des sessions)

**Exemple de règle stateless** :
```
Autoriser : IP source 192.168.1.0/24, TCP port destination 80
Problème : Il faut aussi autoriser les réponses (TCP port source 80)
→ Besoin de 2 règles (entrante + sortante)
```

**Pare-feu Stateful** :
- **Principe** : Suit l'**état des connexions** (table d'état)
- **Analyse** : En-têtes + contexte (connexion établie, nouvelle, reliée)
- **État** : Garde en mémoire les sessions actives
- **Performance** : Plus lent (table d'état à maintenir)
- **Sécurité** : Élevée (comprend le contexte)

**Exemple de règle stateful** :
```
Autoriser : IP source 192.168.1.0/24, TCP port destination 80
Résultat : Les réponses sont automatiquement autorisées (connexion établie)
→ 1 seule règle nécessaire
```

**États TCP suivis** :
- **NEW** : Nouvelle connexion
- **ESTABLISHED** : Connexion établie
- **RELATED** : Connexion liée (ex: FTP data channel)
- **INVALID** : Paquet invalide

**Choix** :
- Stateless → ACL simples, routeurs
- **Stateful** → Pare-feu modernes (recommandé)

---

### ✔️ Question 16 - Wi-Fi WPA2

**Réponse modèle** :

**WEP (Wired Equivalent Privacy)** :
- **Chiffrement** : RC4 (64 ou 128 bits)
- **Sécurité** : **OBSOLÈTE** - Cassable en quelques minutes
- **Problème** : Clés faibles, vecteur d'initialisation (IV) réutilisé
- **Verdict** : **NE JAMAIS UTILISER**

**WPA (Wi-Fi Protected Access)** :
- **Chiffrement** : TKIP (amélioration de RC4)
- **Sécurité** : Amélioration temporaire (2003)
- **Authentification** : PSK (Pre-Shared Key) ou 802.1X
- **Problème** : TKIP vulnérable, obsolète
- **Verdict** : **Éviter** (transition vers WPA2)

**WPA2 (Wi-Fi Protected Access 2)** :
- **Chiffrement** : **AES** (Advanced Encryption Standard) avec CCMP
- **Sécurité** : **Très sécurisé** (norme actuelle depuis 2004)
- **Authentification** :
  - **WPA2-Personal** : PSK (mot de passe partagé)
  - **WPA2-Enterprise** : 802.1X + RADIUS (authentification par utilisateur)
- **Verdict** : **RECOMMANDÉ**

**WPA3** (dernière version, 2018) :
- Amélioration de WPA2 (SAE au lieu de PSK)
- Protection contre attaques par dictionnaire
- Chiffrement individualisé (OWE pour réseaux publics)

**Recommandation client** :
→ **WPA2-Personal** (PME/particuliers) ou **WPA2-Enterprise** (grande entreprise)

**Configuration recommandée** :
- **Protocole** : WPA2
- **Chiffrement** : AES
- **Mot de passe** : Minimum 12 caractères (complexe)
- **SSID** : Visible (le cacher n'apporte pas de sécurité)
- **Désactiver WPS** (vulnérable)

---

### ✔️ Question 17 - QoS

**Réponse modèle** :

**Pourquoi la QoS est nécessaire pour la VoIP** :

**Problèmes sans QoS** :
1. **Latence** : Délai > 150ms → Conversation difficile
2. **Gigue (Jitter)** : Variation de latence → Voix saccadée
3. **Perte de paquets** : > 1% → Coupures audio
4. **Bande passante** : Trafic données peut saturer le lien

**Exigences VoIP** :
- Latence : **< 150ms** (idéal < 100ms)
- Gigue : **< 30ms**
- Perte de paquets : **< 1%**
- Bande passante : ~85 kbps par appel (codec G.711)

**Mécanismes QoS à mettre en place** :

**1. Classification et marquage** :
- Identifier le trafic VoIP (ports, adresses)
- **Marquer** avec DSCP EF (Expedited Forwarding)
- Valeur DSCP : 46 (priorité maximale)

**2. Queuing (Files d'attente)** :
- **LLQ (Low Latency Queue)** : File prioritaire pour VoIP
- **CBWFQ** : Files séparées par classe de trafic
- Garantir bande passante minimale pour VoIP

**3. Policing/Shaping** :
- Limiter le trafic non-prioritaire
- Lisser le trafic VoIP (shaping)

**4. Priorisation** :
- **Voix** : Priorité maximale
- **Signalisation VoIP** : Haute priorité (SIP, H.323)
- **Données critiques** : Priorité moyenne
- **Best effort** : Reste

**Configuration exemple (Cisco)** :
```cisco
! Classification
class-map match-all VOIX
 match ip dscp ef

! Policy
policy-map QOS-VOIP
 class VOIX
  priority percent 30
  
! Application
interface GigabitEthernet0/1
 service-policy output QOS-VOIP
```

**Tests de validation** :
- **Ping avec taille** : Vérifier latence
- **Iperf** : Tester bande passante
- **Wireshark** : Analyser DSCP, jitter

---

### ✔️ Question 18 - Load Balancing

**Réponse modèle** :

**Principe du Load Balancing** :
- **Répartir** le trafic entrant entre plusieurs serveurs
- **Améliorer** la disponibilité et les performances
- **Éviter** la surcharge d'un seul serveur
- **Détecter** les serveurs défaillants (health checks)

**Algorithmes de répartition** :

**1. Round-Robin** :
- Chacun son tour (serveur 1, 2, 3, 1, 2, 3...)
- Simple, équitable
- **Problème** : Ne tient pas compte de la charge réelle

**2. Least Connections** :
- Envoie vers le serveur avec le **moins de connexions actives**
- Intelligent, adaptatif
- **Usage** : Sessions longues (base de données)

**3. IP Hash** :
- Basé sur l'**adresse IP source** (hash)
- Même client → Même serveur (persistance de session)
- **Usage** : Applications avec sessions (e-commerce)

**4. Weighted (Pondéré)** :
- Serveurs ont des **poids** selon leur capacité
- Serveur puissant reçoit plus de trafic
- **Usage** : Serveurs hétérogènes

**5. Least Response Time** :
- Serveur avec le **meilleur temps de réponse**
- **Usage** : Optimisation performance

**Cas d'usage concret** :

**Scénario** : Site e-commerce avec 3 serveurs web

**Architecture** :
```
Internet
   ↓
Load Balancer (HAProxy/F5)
   ↓
├── Serveur Web 1 (192.168.1.10)
├── Serveur Web 2 (192.168.1.11)
└── Serveur Web 3 (192.168.1.12)
   ↓
Base de données (backend)
```

**Configuration** :
- **Algorithme** : IP Hash (persistance de session, panier d'achat)
- **Health checks** : HTTP GET / toutes les 5 secondes
- **Failover** : Si serveur down, redirection automatique
- **SSL Offloading** : Load balancer gère HTTPS (décharge serveurs)

**Avantages** :
- Haute disponibilité (si 1 serveur tombe, 2 restent)
- Performance (charge répartie)
- Scalabilité (ajouter serveurs facilement)
- Maintenance (mise à jour serveur par serveur)

---

### ✔️ Question 19 - Spanning Tree

**Réponse modèle** :

**Problème des boucles** :
- **Tempêtes de broadcast** : Paquets broadcast tournent en boucle infiniment
- **Saturation CPU** : Switchs surchargés
- **Duplications de trames** : Même trame reçue plusieurs fois
- **Instabilité table MAC** : Apprentissage incorrect

**Solution STP (Spanning Tree Protocol - 802.1D)** :

**Fonctionnement** :

**1. Élection du Root Bridge** :
- Switch avec la plus **petite priorité** (défaut : 32768 + VLAN ID)
- Si égalité : Plus petite adresse MAC
- Devient le point central de l'arbre

**2. Calcul du coût vers le root** :
- Chaque switch calcule le **meilleur chemin** vers le root
- Coût basé sur la bande passante :
  - 10 Mbps : 100
  - 100 Mbps : 19
  - 1 Gbps : 4
  - 10 Gbps : 2

**3. Blocage des ports redondants** :
- Ports offrant un chemin alternatif sont **bloqués**
- Évite les boucles tout en gardant la redondance

**États des ports STP** :

1. **Blocking** (Bloqué) :
   - N'envoie/reçoit pas de données
   - Écoute seulement les BPDU
   - Durée : Permanent (sauf changement topologie)

2. **Listening** (Écoute) :
   - Transition après blocage
   - Écoute BPDU, prépare à transférer
   - Durée : 15 secondes (Forward Delay)

3. **Learning** (Apprentissage) :
   - Apprend les adresses MAC
   - Ne transfère pas encore de données
   - Durée : 15 secondes

4. **Forwarding** (Transfert) :
   - **État actif** : Transfère les données
   - Port opérationnel

5. **Disabled** (Désactivé) :
   - Port administrativement désactivé
   - Ne participe pas à STP

**Transition Blocking → Forwarding : 30-50 secondes**

**Évolutions** :
- **RSTP (802.1w)** : Convergence < 6 secondes
- **PVST+** : STP par VLAN (Cisco)
- **MST (802.1s)** : Multiple Spanning Tree

**Commandes Cisco** :
```cisco
! Voir l'état STP
show spanning-tree

! Définir priorité (devenir root)
spanning-tree vlan 1 priority 4096

! Activer RSTP
spanning-tree mode rapid-pvst
```

---

### ✔️ Question 20 - Dépannage VLAN

**Réponse modèle** :

**Causes possibles** :

**1. Pas de routage inter-VLAN configuré** :
- Les VLAN sont des domaines de broadcast séparés
- **Solution** : Router-on-a-stick ou switch L3 avec SVI

**2. Trunks mal configurés** :
- VLAN non autorisés sur le trunk
- **Vérification** : `show interfaces trunk`
- **Solution** : `switchport trunk allowed vlan 10,20`

**3. Ports Access mal assignés** :
- Utilisateurs dans le mauvais VLAN
- **Vérification** : `show vlan brief`
- **Solution** : `switchport access vlan X`

**4. Pare-feu / ACL bloque le trafic** :
- Règles trop restrictives sur le routeur
- **Vérification** : `show access-lists`, `show ip interface`
- **Solution** : Ajuster les ACL

**5. VLAN n'existe pas sur tous les switchs** :
- VLAN créé seulement sur certains switchs
- **Vérification** : `show vlan` sur chaque switch
- **Solution** : Créer le VLAN partout

**Méthodologie de dépannage** :

**Étape 1 - Vérifier la connectivité locale** :
```cisco
! PC VLAN 10 peut-il pinger d'autres PC VLAN 10 ?
ping 192.168.10.5
```
- Si échec → Problème VLAN local (port Access, trunk)

**Étape 2 - Vérifier la configuration VLAN** :
```cisco
SW1# show vlan brief
SW1# show interfaces fa0/5 switchport
```
- Port dans le bon VLAN ?
- Mode Access ou Trunk correct ?

**Étape 3 - Vérifier les trunks** :
```cisco
SW1# show interfaces trunk
SW1# show interfaces gi0/1 switchport
```
- VLANs autorisés sur le trunk ?
- Native VLAN cohérent entre switchs ?

**Étape 4 - Vérifier le routage inter-VLAN** :
```cisco
! Sur switch L3 ou routeur
show ip interface brief
show ip route
```
- SVI des VLAN configurés et UP ?
- Passerelle configurée sur les PC ?

**Étape 5 - Tester depuis le routeur** :
```cisco
R1# ping 192.168.10.5
R1# ping 192.168.20.5
```
- Routeur peut-il joindre les 2 VLAN ?

**Étape 6 - Vérifier les ACL/Pare-feu** :
```cisco
show access-lists
show ip interface gi0/0.10
```

**Configuration correcte Router-on-a-Stick** :
```cisco
! Sur le routeur
interface GigabitEthernet0/0.10
 encapsulation dot1Q 10
 ip address 192.168.10.1 255.255.255.0

interface GigabitEthernet0/0.20
 encapsulation dot1Q 20
 ip address 192.168.20.1 255.255.255.0

! Sur le switch
interface GigabitEthernet0/1
 switchport mode trunk
 switchport trunk allowed vlan 10,20
```

**Tests de validation** :
```
PC VLAN 10 → ping passerelle (192.168.10.1)
PC VLAN 10 → ping PC VLAN 20 (192.168.20.5)
Wireshark : Vérifier tags 802.1Q sur trunk
```

---

## 📊 SCORE TOTAL QUESTIONS OUVERTES RÉSEAU


**TOTAL** : _____ / 20

| Score Total | Niveau |
|-------------|--------|
| 18-20 | ⭐⭐⭐ Excellent - Maîtrise complète |
| 15-17 | ⭐⭐ Très bien - Quelques détails |
| 12-14 | ⭐ Bien - Révise les points faibles |
| 9-11 | ⚠️ Moyen - Révision nécessaire |
| 0-8 | 🔴 Insuffisant - Relis les fiches |

---

**✅ Questions Ouvertes Réseau TERMINÉES ! Continue avec les Sauvegardes !**
