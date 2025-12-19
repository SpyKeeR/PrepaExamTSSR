# 🔧 COMMANDES CISCO IOS - FICHE ESSENTIELLE
## Routeurs et Switchs - Commandes de base pour l'examen

> **Pour l'examen** : Connais les commandes de configuration de base, VLAN, routage statique, SSH, et sauvegarde !

---

## 📋 TABLE DES MATIÈRES

1. [Modes d'utilisation](#modes)
2. [Commandes de base](#base)
3. [Configuration initiale](#config)
4. [Interface et IP](#interface)
5. [VLAN (Switch)](#vlan)
6. [Routage statique](#routage)
7. [SSH et Telnet](#ssh)
8. [Sauvegarde et restauration](#sauvegarde)
9. [Diagnostic réseau](#diagnostic)
10. [Commandes TOP 20 pour l'examen](#top20)

---

## 🎯 MODES D'UTILISATION <a id="modes"></a>

### 1. Mode Utilisateur (User EXEC)
```
Router>
```
- **Accès** : Par défaut à la connexion
- **Droits** : Consultation uniquement
- **Commandes** : `show`, `ping`, `traceroute`

### 2. Mode Privilégié (Privileged EXEC)
```
Router#
```
- **Accès** : `Router> enable`
- **Droits** : Toutes les commandes show + commandes de debug
- **Commandes** : Tous les `show`, `debug`, `copy`, `reload`

### 3. Mode Configuration Globale
```
Router(config)#
```
- **Accès** : `Router# configure terminal` (ou `conf t`)
- **Droits** : Configuration du routeur/switch
- **Commandes** : Hostname, mots de passe, interfaces, routage

### 4. Mode Configuration d'Interface
```
Router(config-if)#
```
- **Accès** : `Router(config)# interface FastEthernet0/0`
- **Droits** : Configuration d'une interface spécifique

### 5. Mode Configuration de Ligne
```
Router(config-line)#
```
- **Accès** : `Router(config)# line vty 0 4`
- **Droits** : Configuration des lignes de connexion (console, VTY)

---

## 🔹 COMMANDES DE BASE <a id="base"></a>

### Navigation entre modes

| Commande | Action |
|----------|--------|
| `enable` | User → Privileged |
| `configure terminal` (ou `conf t`) | Privileged → Config globale |
| `interface <nom>` | Config globale → Config interface |
| `line console 0` / `line vty 0 4` | Config globale → Config ligne |
| `exit` | Retour au mode précédent |
| `end` | Retour direct au mode Privileged |
| `disable` | Privileged → User |

### Aide et complétion

| Commande | Action |
|----------|--------|
| `?` | Liste des commandes disponibles |
| `sh?` | Commandes commençant par "sh" |
| `show ?` | Options de la commande show |
| `Tab` | Auto-complétion |
| `Ctrl+C` | Interruption de commande |
| `Ctrl+Z` | Retour au mode Privileged |

---

## ⚙️ CONFIGURATION INITIALE <a id="config"></a>

### Nom du routeur/switch
```
Router> enable
Router# configure terminal
Router(config)# hostname R1
R1(config)#
```

### Mot de passe ENABLE (mode privilégié)
```
R1(config)# enable secret Cisco123!
```
> ⚠️ `enable secret` est **chiffré** (MD5), préférable à `enable password`

### Mot de passe Console
```
R1(config)# line console 0
R1(config-line)# password Console123
R1(config-line)# login
R1(config-line)# exit
```

### Mot de passe VTY (Telnet/SSH)
```
R1(config)# line vty 0 4
R1(config-line)# password VTY123
R1(config-line)# login
R1(config-line)# transport input ssh telnet
R1(config-line)# exit
```

### Bannière de connexion
```
R1(config)# banner motd #
Accès réservé au personnel autorisé !
#
```

### Chiffrement des mots de passe
```
R1(config)# service password-encryption
```
> Chiffre tous les mots de passe en clair (type 7 - faible)

---

## 🌐 CONFIGURATION INTERFACE ET IP <a id="interface"></a>

### Configurer une IP sur une interface

**Routeur** :
```
R1(config)# interface GigabitEthernet0/0
R1(config-if)# ip address 192.168.1.1 255.255.255.0
R1(config-if)# description LAN-ADMIN
R1(config-if)# no shutdown
R1(config-if)# exit
```

**Switch (VLAN 1 par défaut)** :
```
SW1(config)# interface vlan 1
SW1(config-if)# ip address 192.168.1.10 255.255.255.0
SW1(config-if)# no shutdown
SW1(config-if)# exit
```

### Passerelle par défaut (Switch uniquement)
```
SW1(config)# ip default-gateway 192.168.1.1
```

### Vérifier la configuration
```
R1# show ip interface brief
R1# show interfaces GigabitEthernet0/0
R1# show ip route
```

---

## 🔀 VLAN (SWITCH UNIQUEMENT) <a id="vlan"></a>

### Créer un VLAN
```
SW1(config)# vlan 10
SW1(config-vlan)# name ADMIN
SW1(config-vlan)# exit

SW1(config)# vlan 20
SW1(config-vlan)# name USERS
SW1(config-vlan)# exit
```

### Assigner un port à un VLAN (mode Access)
```
SW1(config)# interface FastEthernet0/5
SW1(config-if)# switchport mode access
SW1(config-if)# switchport access vlan 10
SW1(config-if)# exit
```

### Configurer un port Trunk (inter-switch ou switch-routeur)
```
SW1(config)# interface GigabitEthernet0/1
SW1(config-if)# switchport mode trunk
SW1(config-if)# switchport trunk allowed vlan 10,20,30
SW1(config-if)# exit
```

### Vérifier les VLAN
```
SW1# show vlan brief
SW1# show interfaces trunk
SW1# show interfaces FastEthernet0/5 switchport
```

### Supprimer un VLAN
```
SW1(config)# no vlan 10
```

---

## 🛤️ ROUTAGE STATIQUE <a id="routage"></a>

### Route statique classique
```
R1(config)# ip route <réseau_destination> <masque> <next_hop_ou_interface>
```

**Exemple 1** : Via next-hop
```
R1(config)# ip route 192.168.2.0 255.255.255.0 10.0.0.2
```
> Route vers 192.168.2.0/24 via le routeur 10.0.0.2

**Exemple 2** : Via interface de sortie
```
R1(config)# ip route 192.168.3.0 255.255.255.0 GigabitEthernet0/1
```

### Route par défaut (default route)
```
R1(config)# ip route 0.0.0.0 0.0.0.0 <next_hop>
```

**Exemple** :
```
R1(config)# ip route 0.0.0.0 0.0.0.0 200.100.50.1
```
> Toutes les destinations inconnues → 200.100.50.1

### Vérifier les routes
```
R1# show ip route
R1# show ip route static
```

### Supprimer une route
```
R1(config)# no ip route 192.168.2.0 255.255.255.0 10.0.0.2
```

---

## 🔐 SSH ET TELNET <a id="ssh"></a>

### Configuration SSH (VERSION 2 RECOMMANDÉE)

**Étape 1** : Nom d'hôte et domaine
```
R1(config)# hostname R1
R1(config)# ip domain-name entreprise.local
```

**Étape 2** : Générer les clés RSA
```
R1(config)# crypto key generate rsa
How many bits in the modulus [512]: 1024
```
> ⚠️ Minimum 768 bits pour SSHv2, **1024 bits recommandé**

**Étape 3** : Créer un utilisateur local
```
R1(config)# username admin privilege 15 secret AdminPass123!
```

**Étape 4** : Configurer les lignes VTY
```
R1(config)# line vty 0 4
R1(config-line)# transport input ssh
R1(config-line)# login local
R1(config-line)# exit
```

**Étape 5** : Forcer SSH version 2
```
R1(config)# ip ssh version 2
R1(config)# ip ssh time-out 60
R1(config)# ip ssh authentication-retries 3
```

### Vérifier SSH
```
R1# show ip ssh
R1# show ssh
```

### Se connecter en SSH (depuis un autre équipement)
```
R1# ssh -l admin 192.168.1.1
```

---

## 💾 SAUVEGARDE ET RESTAURATION <a id="sauvegarde"></a>

### Voir les configurations

| Commande | Description |
|----------|-------------|
| `show running-config` | Config active (RAM) |
| `show startup-config` | Config de démarrage (NVRAM) |

### Sauvegarder la configuration

**Méthode 1** : Commande classique
```
R1# copy running-config startup-config
Destination filename [startup-config]? [Entrée]
```

**Méthode 2** : Commande courte
```
R1# write memory
```
ou
```
R1# wr
```

### Restaurer la configuration de démarrage
```
R1# copy startup-config running-config
```

### Effacer la configuration
```
R1# erase startup-config
R1# reload
```
> ⚠️ **reload** redémarre l'équipement

### Sauvegarder vers un serveur TFTP
```
R1# copy running-config tftp:
Address or name of remote host []? 192.168.1.100
Destination filename [r1-confg]? backup-r1.cfg
```

### Restaurer depuis un serveur TFTP
```
R1# copy tftp: running-config
Address or name of remote host []? 192.168.1.100
Source filename []? backup-r1.cfg
```

---

## 🩺 DIAGNOSTIC RÉSEAU <a id="diagnostic"></a>

### Commandes de vérification

| Commande | Usage |
|----------|-------|
| `show version` | Version IOS, uptime, mémoire |
| `show running-config` | Configuration active |
| `show startup-config` | Configuration sauvegardée |
| `show ip interface brief` | **ESSENTIEL** - État des interfaces |
| `show interfaces` | Détails de toutes les interfaces |
| `show ip route` | **ESSENTIEL** - Table de routage |
| `show vlan brief` | Liste des VLAN (switch) |
| `show mac address-table` | Table MAC (switch) |
| `show arp` | Table ARP |
| `show ip protocols` | Protocoles de routage actifs |
| `show clock` | Date et heure |
| `show flash` | Contenu de la mémoire Flash |

### Tests de connectivité

**Ping** :
```
R1# ping 192.168.1.10
R1# ping 192.168.1.10 repeat 100
R1# ping 192.168.1.10 size 1500
```

**Traceroute** :
```
R1# traceroute 8.8.8.8
```

**Telnet** (test de port) :
```
R1# telnet 192.168.1.10 80
```

### Debug (avec précaution !)
```
R1# debug ip icmp
R1# debug ip routing
R1# no debug all  ← TOUJOURS arrêter le debug !
```
> ⚠️ Le debug peut saturer le CPU en production

---

## ⭐ TOP 20 COMMANDES POUR L'EXAMEN <a id="top20"></a>

| # | Commande | Usage |
|---|----------|-------|
| 1 | `enable` | Passer en mode privilégié |
| 2 | `configure terminal` | Entrer en mode config |
| 3 | `hostname <nom>` | Changer le nom |
| 4 | `enable secret <mdp>` | Mot de passe enable chiffré |
| 5 | `interface <nom>` | Configurer une interface |
| 6 | `ip address <IP> <masque>` | Assigner une IP |
| 7 | `no shutdown` | Activer une interface |
| 8 | `ip route <réseau> <masque> <next_hop>` | Route statique |
| 9 | `show ip interface brief` | **État des interfaces** |
| 10 | `show ip route` | **Table de routage** |
| 11 | `show running-config` | Config active |
| 12 | `copy running-config startup-config` | **Sauvegarder** |
| 13 | `vlan <numéro>` | Créer un VLAN |
| 14 | `switchport mode access` | Port en mode access |
| 15 | `switchport access vlan <n>` | Assigner VLAN |
| 16 | `switchport mode trunk` | Port en mode trunk |
| 17 | `line vty 0 4` | Config lignes SSH/Telnet |
| 18 | `transport input ssh` | Autoriser SSH |
| 19 | `ping <IP>` | Test connectivité |
| 20 | `reload` | Redémarrer |

---

## 📌 SCÉNARIOS D'EXAMEN FRÉQUENTS

### Scénario 1 : Configuration initiale routeur

```
Router> enable
Router# configure terminal
Router(config)# hostname R1
R1(config)# enable secret Cisco123!
R1(config)# line console 0
R1(config-line)# password Console123
R1(config-line)# login
R1(config-line)# exit
R1(config)# interface GigabitEthernet0/0
R1(config-if)# ip address 192.168.1.1 255.255.255.0
R1(config-if)# no shutdown
R1(config-if)# exit
R1(config)# exit
R1# copy running-config startup-config
```

### Scénario 2 : Configuration VLAN sur switch

```
SW1# configure terminal
SW1(config)# vlan 10
SW1(config-vlan)# name ADMIN
SW1(config-vlan)# exit
SW1(config)# vlan 20
SW1(config-vlan)# name USERS
SW1(config-vlan)# exit
SW1(config)# interface FastEthernet0/5
SW1(config-if)# switchport mode access
SW1(config-if)# switchport access vlan 10
SW1(config-if)# exit
SW1(config)# interface GigabitEthernet0/1
SW1(config-if)# switchport mode trunk
SW1(config-if)# exit
SW1# write memory
```

### Scénario 3 : Configuration SSH

```
R1(config)# hostname R1
R1(config)# ip domain-name lab.local
R1(config)# crypto key generate rsa
[1024]
R1(config)# username admin privilege 15 secret Admin123!
R1(config)# line vty 0 4
R1(config-line)# transport input ssh
R1(config-line)# login local
R1(config-line)# exit
R1(config)# ip ssh version 2
```

### Scénario 4 : Routage statique

```
R1(config)# ip route 192.168.2.0 255.255.255.0 10.0.0.2
R1(config)# ip route 192.168.3.0 255.255.255.0 10.0.0.2
R1(config)# ip route 0.0.0.0 0.0.0.0 200.50.100.1
R1(config)# exit
R1# show ip route
```

---

## 💡 ASTUCES POUR L'EXAMEN

### ✅ À RETENIR ABSOLUMENT

1. **no shutdown** après config d'interface (sinon reste down)
2. **copy run start** pour sauvegarder (sinon perdu au reboot)
3. **enable secret** est chiffré, **enable password** non
4. **SSH nécessite** : hostname + domain-name + crypto key + user local
5. **Mode trunk** = plusieurs VLANs, **mode access** = un seul VLAN
6. **ip default-gateway** sur switch, **ip route 0.0.0.0 0.0.0.0** sur routeur

### 🔥 ERREURS FRÉQUENTES

❌ Oublier `no shutdown` → interface reste down  
❌ Oublier `copy run start` → config perdue au reboot  
❌ Confondre `transport input ssh` et `transport input telnet`  
❌ Oublier `login` ou `login local` sur line vty → connexion refusée  
❌ Mauvais masque de sous-réseau  
❌ Route statique sans next-hop  

---

## 🎯 CHECKLIST AVANT L'EXAMEN

- [ ] Je connais les 5 modes (User, Privileged, Config, Interface, Line)
- [ ] Je sais configurer une IP sur une interface
- [ ] Je sais créer des VLAN et les assigner aux ports
- [ ] Je sais configurer un port trunk
- [ ] Je sais configurer une route statique
- [ ] Je sais configurer SSH (hostname, domain, crypto, user, vty)
- [ ] Je sais sauvegarder la config (`copy run start`)
- [ ] Je connais les commandes `show ip interface brief` et `show ip route`
- [ ] Je sais faire un ping et un traceroute
- [ ] Je sais distinguer mode access et mode trunk

---

**🚀 Tu es prêt pour les commandes Cisco IOS ! Passe au QCM Réseau maintenant !**
