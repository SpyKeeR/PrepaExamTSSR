# 🐧 MSP PRATIQUE - SERVICES LINUX
## Installation et Configuration DNS, DHCP, Apache (Debian 12)

> **Pour l'examen** : Tu auras 45 minutes pour résoudre 3 incidents pratiques. Maîtrise ces installations de base !

---

## 🎯 OBJECTIF DU MSP

**Contexte entreprise** : PME 50 utilisateurs, infrastructure à mettre en place

**Services à déployer** :
1. **DNS** (Bind9) : Résolution de noms interne
2. **DHCP** (isc-dhcp-server) : Attribution IP automatique
3. **Apache2** : Serveur web intranet

**Système** : Debian 12 (Bookworm)

---

## 📋 PRÉREQUIS

### Configuration réseau de base
```bash
# Afficher configuration réseau
ip addr show
ip route show

# Configurer IP statique (fichier /etc/network/interfaces)
sudo nano /etc/network/interfaces

# Contenu :
auto ens33
iface ens33 inet static
    address 192.168.1.10
    netmask 255.255.255.0
    gateway 192.168.1.1
    dns-nameservers 8.8.8.8

# Redémarrer réseau
sudo systemctl restart networking
```

### Mise à jour système
```bash
# Mettre à jour les dépôts
sudo apt update

# Mettre à jour le système
sudo apt upgrade -y

# Vérifier version Debian
cat /etc/debian_version
```

---

## 🌐 PARTIE 1 : INSTALLATION DNS (BIND9)

### Étape 1 : Installation de Bind9

```bash
# Installer Bind9 et outils
sudo apt install bind9 bind9utils bind9-doc dnsutils -y

# Vérifier installation
dpkg -l | grep bind9

# Vérifier version
named -v
```

### Étape 2 : Configuration du serveur DNS

**Fichier principal de configuration** :
```bash
sudo nano /etc/bind/named.conf.options
```

**Contenu** :
```bind
options {
    directory "/var/cache/bind";

    // Forwarders (DNS externes si résolution externe nécessaire)
    forwarders {
        8.8.8.8;
        8.8.4.4;
    };

    // Autoriser requêtes depuis le réseau local
    allow-query { localhost; 192.168.1.0/24; };

    // Désactiver transfert de zone (sécurité)
    allow-transfer { none; };

    // Écouter sur IPv4 uniquement
    listen-on { any; };
    listen-on-v6 { none; };

    // Autoriser récursion pour le réseau local
    recursion yes;
    allow-recursion { localhost; 192.168.1.0/24; };

    dnssec-validation auto;
};
```

### Étape 3 : Créer la zone de recherche directe

**Déclarer la zone** :
```bash
sudo nano /etc/bind/named.conf.local
```

**Contenu** :
```bind
// Zone de recherche directe (Forward)
zone "entreprise.local" {
    type master;
    file "/etc/bind/zones/db.entreprise.local";
    allow-update { none; };
};

// Zone de recherche inversée (Reverse)
zone "1.168.192.in-addr.arpa" {
    type master;
    file "/etc/bind/zones/db.192.168.1";
    allow-update { none; };
};
```

### Étape 4 : Créer les fichiers de zones

**Créer le répertoire** :
```bash
sudo mkdir /etc/bind/zones
```

**Zone de recherche directe** :
```bash
sudo nano /etc/bind/zones/db.entreprise.local
```

**Contenu** :
```bind
$TTL    86400
@       IN      SOA     ns1.entreprise.local. admin.entreprise.local. (
                              2024111201         ; Serial (YYYYMMDDNN)
                              3600               ; Refresh (1h)
                              1800               ; Retry (30min)
                              604800             ; Expire (1 semaine)
                              86400 )            ; Negative Cache TTL (1 jour)
;
; Serveurs de noms
@       IN      NS      ns1.entreprise.local.

; Enregistrements A (IPv4)
ns1             IN      A       192.168.1.10
dns             IN      A       192.168.1.10
srv-web         IN      A       192.168.1.20
srv-fichiers    IN      A       192.168.1.30
pc01            IN      A       192.168.1.100
pc02            IN      A       192.168.1.101

; Alias (CNAME)
www             IN      CNAME   srv-web
intranet        IN      CNAME   srv-web
ftp             IN      CNAME   srv-fichiers

; Enregistrement MX (Mail)
@               IN      MX  10  mail.entreprise.local.
mail            IN      A       192.168.1.25
```

**Zone de recherche inversée** :
```bash
sudo nano /etc/bind/zones/db.192.168.1
```

**Contenu** :
```bind
$TTL    86400
@       IN      SOA     ns1.entreprise.local. admin.entreprise.local. (
                              2024111201         ; Serial
                              3600               ; Refresh
                              1800               ; Retry
                              604800             ; Expire
                              86400 )            ; Negative Cache TTL
;
; Serveurs de noms
@       IN      NS      ns1.entreprise.local.

; Enregistrements PTR (IP → Nom)
10      IN      PTR     ns1.entreprise.local.
20      IN      PTR     srv-web.entreprise.local.
30      IN      PTR     srv-fichiers.entreprise.local.
100     IN      PTR     pc01.entreprise.local.
101     IN      PTR     pc02.entreprise.local.
25      IN      PTR     mail.entreprise.local.
```

### Étape 5 : Vérifier la configuration

```bash
# Vérifier syntaxe configuration principale
sudo named-checkconf

# Vérifier syntaxe zone directe
sudo named-checkzone entreprise.local /etc/bind/zones/db.entreprise.local

# Vérifier syntaxe zone inversée
sudo named-checkzone 1.168.192.in-addr.arpa /etc/bind/zones/db.192.168.1

# Si tout OK : "OK" affiché
```

### Étape 6 : Démarrer et activer le service

```bash
# Redémarrer Bind9
sudo systemctl restart bind9

# Vérifier statut
sudo systemctl status bind9

# Activer au démarrage
sudo systemctl enable bind9

# Vérifier que le service écoute sur le port 53
sudo ss -tunlp | grep :53
```

### Étape 7 : Tester la résolution DNS

```bash
# Tester résolution directe (nom → IP)
dig @192.168.1.10 ns1.entreprise.local
dig @192.168.1.10 srv-web.entreprise.local
nslookup www.entreprise.local 192.168.1.10

# Tester résolution inversée (IP → nom)
dig @192.168.1.10 -x 192.168.1.20

# Tester depuis un autre PC (configurer DNS = 192.168.1.10)
ping srv-web.entreprise.local
ping www.entreprise.local
```

### Étape 8 : Configurer le serveur comme DNS principal

```bash
# Modifier /etc/resolv.conf
sudo nano /etc/resolv.conf

# Contenu :
nameserver 192.168.1.10
search entreprise.local

# Rendre permanent (sinon écrasé au reboot)
sudo chattr +i /etc/resolv.conf
```

### 🛠️ Dépannage DNS

**Problème : Service ne démarre pas**
```bash
# Vérifier logs
sudo journalctl -xeu bind9
sudo tail -f /var/log/syslog | grep named

# Erreurs fréquentes :
# - Syntaxe zone → named-checkzone
# - Port 53 occupé → sudo ss -tunlp | grep :53
# - Permissions → sudo chown -R bind:bind /etc/bind/zones
```

**Problème : Résolution ne fonctionne pas**
```bash
# Vérifier pare-feu
sudo ufw allow 53/tcp
sudo ufw allow 53/udp

# Tester localement
dig @localhost entreprise.local

# Vérifier ACL (allow-query dans named.conf.options)
```

---

## 📡 PARTIE 2 : INSTALLATION DHCP (ISC-DHCP-SERVER)

### Étape 1 : Installation du serveur DHCP

```bash
# Installer isc-dhcp-server
sudo apt install isc-dhcp-server -y

# Vérifier installation
dpkg -l | grep isc-dhcp-server
```

### Étape 2 : Configurer l'interface réseau

```bash
# Spécifier l'interface d'écoute
sudo nano /etc/default/isc-dhcp-server
```

**Contenu** :
```bash
# Interface IPv4
INTERFACESv4="ens33"

# Désactiver IPv6 (si non utilisé)
INTERFACESv6=""
```

### Étape 3 : Configuration du serveur DHCP

```bash
# Éditer le fichier de configuration principal
sudo nano /etc/dhcp/dhcpd.conf
```

**Contenu** :
```dhcpd
# Configuration globale
default-lease-time 86400;              # Bail par défaut : 1 jour
max-lease-time 604800;                 # Bail maximum : 7 jours
authoritative;                         # Serveur autoritaire

# Options globales
option domain-name "entreprise.local";
option domain-name-servers 192.168.1.10;

# Logging
log-facility local7;

# Sous-réseau 192.168.1.0/24
subnet 192.168.1.0 netmask 255.255.255.0 {
    # Plage d'adresses dynamiques
    range 192.168.1.100 192.168.1.200;
    
    # Passerelle par défaut
    option routers 192.168.1.1;
    
    # Serveurs DNS
    option domain-name-servers 192.168.1.10, 8.8.8.8;
    
    # Serveur NTP (optionnel)
    option ntp-servers 192.168.1.10;
    
    # Nom de domaine
    option domain-name "entreprise.local";
}

# Exclusions (adresses non distribuées)
# Déjà gérées par la plage (100-200)
# Serveurs : 192.168.1.1-99 et 201-254 (IP statiques)

# Réservations (IP fixes par adresse MAC)
host imprimante-compta {
    hardware ethernet 00:1A:2B:3C:4D:5E;
    fixed-address 192.168.1.50;
}

host pc-direction {
    hardware ethernet 00:11:22:33:44:55;
    fixed-address 192.168.1.51;
}

host serveur-backup {
    hardware ethernet 08:00:27:AB:CD:EF;
    fixed-address 192.168.1.60;
}
```

### Étape 4 : Vérifier la configuration

```bash
# Vérifier syntaxe du fichier
sudo dhcpd -t -cf /etc/dhcp/dhcpd.conf

# Si OK : "Configuration file valid"
```

### Étape 5 : Démarrer et activer le service

```bash
# Redémarrer le service DHCP
sudo systemctl restart isc-dhcp-server

# Vérifier statut
sudo systemctl status isc-dhcp-server

# Activer au démarrage
sudo systemctl enable isc-dhcp-server

# Vérifier que le service écoute sur le port 67
sudo ss -unp | grep :67
```

### Étape 6 : Tester le serveur DHCP

**Depuis un client** :
```bash
# Libérer le bail actuel
sudo dhclient -r

# Demander nouvelle IP
sudo dhclient -v

# Vérifier IP obtenue
ip addr show

# Vérifier configuration reçue
cat /var/lib/dhcp/dhclient.leases
```

**Sur le serveur** :
```bash
# Voir les baux actifs
cat /var/lib/dhcp/dhcpd.leases

# Logs en temps réel
sudo tail -f /var/log/syslog | grep dhcpd
```

### Étape 7 : Configurer le pare-feu

```bash
# Autoriser DHCP
sudo ufw allow 67/udp
sudo ufw allow 68/udp

# Vérifier règles
sudo ufw status
```

### 🛠️ Dépannage DHCP

**Problème : Service ne démarre pas**
```bash
# Vérifier logs
sudo journalctl -xeu isc-dhcp-server
sudo tail -50 /var/log/syslog | grep dhcpd

# Erreurs fréquentes :
# - Interface incorrecte → /etc/default/isc-dhcp-server
# - Syntaxe config → dhcpd -t
# - Subnet ne correspond pas à l'interface
```

**Problème : Clients n'obtiennent pas d'IP**
```bash
# Vérifier que le serveur écoute
sudo ss -unp | grep :67

# Vérifier logs lors de la demande
sudo tail -f /var/log/syslog | grep dhcpd

# Tester avec dhcping (installer si nécessaire)
sudo apt install dhcping
sudo dhcping -s 192.168.1.10
```

**Problème : Réservation ne fonctionne pas**
```bash
# Vérifier adresse MAC du client
ip link show

# Format MAC : aa:bb:cc:dd:ee:ff (minuscules, avec ":")
# Redémarrer service après modification réservation
sudo systemctl restart isc-dhcp-server
```

---

## 🌐 PARTIE 3 : INSTALLATION APACHE2

### Étape 1 : Installation d'Apache2

```bash
# Installer Apache2
sudo apt install apache2 -y

# Vérifier installation
apache2 -v

# Vérifier statut
sudo systemctl status apache2
```

### Étape 2 : Configuration de base

```bash
# Fichier de configuration principal
sudo nano /etc/apache2/apache2.conf

# Options importantes (déjà configurées par défaut) :
# - ServerRoot "/etc/apache2"
# - DocumentRoot "/var/www/html"
# - Port 80
```

### Étape 3 : Configuration du serveur

**Configurer le ServerName** :
```bash
sudo nano /etc/apache2/sites-available/000-default.conf
```

**Contenu** :
```apache
<VirtualHost *:80>
    ServerAdmin webmaster@entreprise.local
    ServerName srv-web.entreprise.local
    ServerAlias www.entreprise.local intranet.entreprise.local
    
    DocumentRoot /var/www/html
    
    <Directory /var/www/html>
        Options Indexes FollowSymLinks
        AllowOverride All
        Require all granted
    </Directory>
    
    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```

### Étape 4 : Créer une page web de test

```bash
# Supprimer la page par défaut
sudo rm /var/www/html/index.html

# Créer une page personnalisée
sudo nano /var/www/html/index.html
```

**Contenu** :
```html
<!DOCTYPE html>
<html lang="fr">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Intranet Entreprise</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            color: white;
            display: flex;
            justify-content: center;
            align-items: center;
            height: 100vh;
            margin: 0;
        }
        .container {
            text-align: center;
            background: rgba(0,0,0,0.3);
            padding: 50px;
            border-radius: 15px;
        }
        h1 { font-size: 3em; margin-bottom: 20px; }
        p { font-size: 1.2em; }
        .info {
            margin-top: 30px;
            font-size: 0.9em;
            opacity: 0.8;
        }
    </style>
</head>
<body>
    <div class="container">
        <h1>🌐 Bienvenue sur l'Intranet</h1>
        <p>Serveur web Apache2 opérationnel</p>
        <div class="info">
            <p><strong>Serveur :</strong> srv-web.entreprise.local</p>
            <p><strong>Date :</strong> <?php echo date('d/m/Y H:i:s'); ?></p>
        </div>
    </div>
</body>
</html>
```

### Étape 5 : Définir les permissions

```bash
# Propriétaire des fichiers web
sudo chown -R www-data:www-data /var/www/html/

# Permissions
sudo chmod -R 755 /var/www/html/
```

### Étape 6 : Activer modules utiles

```bash
# Module de réécriture d'URL
sudo a2enmod rewrite

# Module SSL (pour HTTPS)
sudo a2enmod ssl

# Module headers
sudo a2enmod headers

# Redémarrer Apache
sudo systemctl restart apache2
```

### Étape 7 : Configurer le pare-feu

```bash
# Autoriser HTTP (port 80)
sudo ufw allow 80/tcp

# Autoriser HTTPS (port 443)
sudo ufw allow 443/tcp

# Ou utiliser le profil Apache
sudo ufw allow 'Apache Full'

# Vérifier
sudo ufw status
```

### Étape 8 : Tester le serveur web

```bash
# Localement
curl http://localhost
curl http://192.168.1.10

# Depuis un navigateur (autre PC)
http://192.168.1.10
http://srv-web.entreprise.local
http://www.entreprise.local
```

### Étape 9 : Installer PHP (optionnel mais recommandé)

```bash
# Installer PHP et module Apache
sudo apt install php libapache2-mod-php php-mysql -y

# Vérifier version PHP
php -v

# Créer page de test PHP
sudo nano /var/www/html/info.php
```

**Contenu** :
```php
<?php
phpinfo();
?>
```

```bash
# Tester : http://srv-web.entreprise.local/info.php

# IMPORTANT : Supprimer après test (sécurité)
sudo rm /var/www/html/info.php

# Redémarrer Apache
sudo systemctl restart apache2
```

### Étape 10 : Créer un Virtual Host supplémentaire (optionnel)

**Pour héberger plusieurs sites** :
```bash
# Créer configuration
sudo nano /etc/apache2/sites-available/intranet2.conf
```

**Contenu** :
```apache
<VirtualHost *:80>
    ServerAdmin admin@entreprise.local
    ServerName intranet2.entreprise.local
    DocumentRoot /var/www/intranet2
    
    <Directory /var/www/intranet2>
        Options Indexes FollowSymLinks
        AllowOverride All
        Require all granted
    </Directory>
    
    ErrorLog ${APACHE_LOG_DIR}/intranet2-error.log
    CustomLog ${APACHE_LOG_DIR}/intranet2-access.log combined
</VirtualHost>
```

```bash
# Créer répertoire
sudo mkdir /var/www/intranet2

# Page d'accueil
echo "<h1>Site Intranet 2</h1>" | sudo tee /var/www/intranet2/index.html

# Permissions
sudo chown -R www-data:www-data /var/www/intranet2
sudo chmod -R 755 /var/www/intranet2

# Activer le site
sudo a2ensite intranet2.conf

# Recharger Apache
sudo systemctl reload apache2

# Ajouter enregistrement DNS
# Dans /etc/bind/zones/db.entreprise.local :
# intranet2  IN  A  192.168.1.20
```

### 🛠️ Dépannage Apache

**Problème : Apache ne démarre pas**
```bash
# Vérifier configuration
sudo apache2ctl configtest

# Logs détaillés
sudo journalctl -xeu apache2
sudo tail -50 /var/log/apache2/error.log

# Erreurs fréquentes :
# - Port 80 occupé → sudo ss -tunlp | grep :80
# - Syntaxe VirtualHost → apache2ctl configtest
# - Permissions DocumentRoot
```

**Problème : Page non trouvée (404)**
```bash
# Vérifier DocumentRoot
ls -la /var/www/html/

# Vérifier permissions
sudo chown -R www-data:www-data /var/www/html/
sudo chmod -R 755 /var/www/html/

# Vérifier configuration VirtualHost
cat /etc/apache2/sites-enabled/000-default.conf
```

**Problème : Accès interdit (403 Forbidden)**
```bash
# Vérifier directive <Directory> dans VirtualHost
# Doit contenir : Require all granted

# Vérifier SELinux/AppArmor (rare sur Debian)
sudo aa-status

# Vérifier permissions fichiers
ls -la /var/www/html/
```

---

## 🔍 COMMANDES DE VÉRIFICATION RAPIDE

### Vérifier tous les services

```bash
# Statut des 3 services
sudo systemctl status bind9 isc-dhcp-server apache2

# Ports en écoute
sudo ss -tunlp | grep -E '(:53|:67|:80|:443)'

# Vérifier logs
sudo tail -20 /var/log/syslog
```

### Tests de connectivité

```bash
# DNS
dig @localhost entreprise.local
nslookup srv-web.entreprise.local

# DHCP (depuis client)
sudo dhclient -v

# Apache
curl http://localhost
curl http://srv-web.entreprise.local
```

---

## 📊 CHECKLIST COMPLÈTE

### ✅ DNS (Bind9)
- [ ] Bind9 installé et démarré
- [ ] Zone directe configurée (`/etc/bind/zones/db.entreprise.local`)
- [ ] Zone inversée configurée (`/etc/bind/zones/db.192.168.1`)
- [ ] Syntaxe vérifiée (`named-checkzone`)
- [ ] Service écoute sur port 53
- [ ] Résolution directe fonctionne (`dig`, `nslookup`)
- [ ] Résolution inversée fonctionne (`dig -x`)
- [ ] Pare-feu autorise port 53 (TCP/UDP)

### ✅ DHCP (isc-dhcp-server)
- [ ] isc-dhcp-server installé et démarré
- [ ] Interface configurée (`/etc/default/isc-dhcp-server`)
- [ ] Subnet configuré avec plage IP
- [ ] Options DNS et passerelle définies
- [ ] Réservations créées (si nécessaire)
- [ ] Syntaxe vérifiée (`dhcpd -t`)
- [ ] Service écoute sur port 67
- [ ] Clients obtiennent IP automatiquement
- [ ] Pare-feu autorise ports 67-68 (UDP)

### ✅ Apache2
- [ ] Apache2 installé et démarré
- [ ] VirtualHost configuré avec ServerName
- [ ] Page d'accueil créée (`/var/www/html/index.html`)
- [ ] Permissions correctes (www-data:www-data, 755)
- [ ] Modules activés (rewrite, ssl)
- [ ] Configuration testée (`apache2ctl configtest`)
- [ ] Service écoute sur port 80
- [ ] Page accessible depuis navigateur
- [ ] Pare-feu autorise port 80 (HTTP)
- [ ] PHP installé et fonctionnel (optionnel)

---

## ⏱️ TIMING POUR L'EXAMEN (45 minutes)

**Incident 1 : DNS ne résout pas** (15 min)
- Vérifier service : `systemctl status bind9`
- Vérifier syntaxe zones : `named-checkzone`
- Vérifier logs : `/var/log/syslog`
- Tester résolution : `dig`, `nslookup`

**Incident 2 : DHCP ne distribue pas d'IP** (15 min)
- Vérifier service : `systemctl status isc-dhcp-server`
- Vérifier interface : `/etc/default/isc-dhcp-server`
- Vérifier config : `dhcpd -t`
- Vérifier logs : `/var/log/syslog`

**Incident 3 : Apache inaccessible** (15 min)
- Vérifier service : `systemctl status apache2`
- Vérifier config : `apache2ctl configtest`
- Vérifier port : `ss -tunlp | grep :80`
- Vérifier permissions : `ls -la /var/www/html/`

---

## 🎯 SCÉNARIOS D'INCIDENTS TYPES

### Scénario 1 : DNS - Zone mal configurée
**Symptôme** : `dig entreprise.local` ne retourne rien

**Causes possibles** :
- Serial non incrémenté
- Manque point final (.) après FQDN
- Syntaxe SOA incorrecte
- Permissions fichier zone

**Solution** :
```bash
sudo named-checkzone entreprise.local /etc/bind/zones/db.entreprise.local
sudo chown bind:bind /etc/bind/zones/*
sudo systemctl restart bind9
```

### Scénario 2 : DHCP - Mauvais subnet
**Symptôme** : Service démarre mais clients n'obtiennent pas d'IP

**Causes possibles** :
- Subnet dans dhcpd.conf ne correspond pas au réseau réel
- Interface incorrecte dans `/etc/default/isc-dhcp-server`
- Plage IP hors subnet

**Solution** :
```bash
# Vérifier réseau réel
ip addr show

# Corriger subnet dans dhcpd.conf
sudo nano /etc/dhcp/dhcpd.conf

# Vérifier interface
ip link show
sudo nano /etc/default/isc-dhcp-server

# Redémarrer
sudo systemctl restart isc-dhcp-server
```

### Scénario 3 : Apache - Permissions incorrectes
**Symptôme** : 403 Forbidden

**Causes possibles** :
- Propriétaire incorrect
- Permissions trop restrictives
- Directive "Require" manquante

**Solution** :
```bash
sudo chown -R www-data:www-data /var/www/html/
sudo chmod -R 755 /var/www/html/
sudo nano /etc/apache2/sites-available/000-default.conf
# Vérifier : Require all granted
sudo systemctl restart apache2
```

---

## 📚 COMMANDES À CONNAÎTRE PAR CŒUR

### DNS (Bind9)
```bash
sudo systemctl restart bind9
sudo systemctl status bind9
named-checkconf
named-checkzone <zone> <fichier>
dig @<serveur> <nom>
nslookup <nom> <serveur>
dig -x <IP>
sudo tail -f /var/log/syslog | grep named
```

### DHCP (isc-dhcp-server)
```bash
sudo systemctl restart isc-dhcp-server
sudo systemctl status isc-dhcp-server
dhcpd -t -cf /etc/dhcp/dhcpd.conf
cat /var/lib/dhcp/dhcpd.leases
sudo tail -f /var/log/syslog | grep dhcpd
sudo dhclient -r
sudo dhclient -v
```

### Apache2
```bash
sudo systemctl restart apache2
sudo systemctl status apache2
apache2ctl configtest
sudo a2ensite <site>
sudo a2dissite <site>
sudo a2enmod <module>
curl http://localhost
sudo tail -f /var/log/apache2/error.log
sudo tail -f /var/log/apache2/access.log
```

### Général
```bash
sudo systemctl status <service>
sudo systemctl restart <service>
sudo journalctl -xeu <service>
sudo ss -tunlp | grep :<port>
sudo ufw status
ip addr show
ip route show
```

---

## 🎓 CONSEILS POUR L'EXAMEN

1. **Lecture attentive** : Bien lire la MSP, noter les infos clés (réseau, noms, IPs)

2. **Méthodologie** : Toujours suivre cette séquence :
   - Vérifier service : `systemctl status`
   - Vérifier logs : `journalctl` ou `/var/log/syslog`
   - Vérifier config : `named-checkconf`, `dhcpd -t`, `apache2ctl configtest`
   - Vérifier réseau : `ss -tunlp`, `ip addr`

3. **Ne pas paniquer** : Si bloqué, passer à l'incident suivant et revenir

4. **Tester après chaque correction** : Toujours valider que la correction fonctionne

5. **Documenter** : Noter les commandes utilisées et résultats (utile pour l'oral)

---

**🚀 Bon courage pour l'examen pratique ! Tu as toutes les clés en main !**
