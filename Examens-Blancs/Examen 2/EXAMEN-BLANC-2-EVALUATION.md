# 📝 EXAMEN BLANC 2 - SUJET
## TSSR - Technicien Supérieur Systèmes et Réseaux

**Type** : Épreuve écrite (simulation Jour 1)  
**Durée totale** : **3h30** (210 minutes)  
**Total** : **200 points**  

---

## ⏱️ RÉPARTITION DU TEMPS

| Partie | Durée | Points | Début | Fin |
|--------|-------|--------|-------|-----|
| **PARTIE 1 : MSP Écrite** | 1h30 | 100 | 9h00 | 10h30 |
| **PARTIE 2 : Questionnaire Technique** | 2h00 | 100 | 10h30 | 12h30 |

---

## 📋 CONSIGNES

### Partie 1 - MSP (Mise en Situation Professionnelle)
- **3 incidents techniques** à traiter
- Expliquer votre **démarche de diagnostic**
- Fournir des **solutions détaillées** (commandes, procédures)
- **Justifier** vos choix techniques

### Partie 2 - Questionnaire Technique
- **6 sections thématiques** : Linux, Réseau, Windows/AD, Virtualisation, Sécurité/Backup, Anglais
- Réponses **courtes et précises**
- **Commandes** et **configurations** attendues
- **Calculs détaillés** pour les questions réseau

### Matériel autorisé
- ❌ **Aucun** document, calculatrice, téléphone, ordinateur
- ✅ **Uniquement** : Stylo, brouillon fourni

### Notation
- Barème indiqué pour chaque question
- **Points partiels** possibles si démarche correcte
- **Relire** vos réponses (10 min en fin d'épreuve)

---

---

# PARTIE 1 : MSP ÉCRITE (100 points - 1h30)

---

## 🏢 CONTEXTE GÉNÉRAL

**Entreprise** : **MegaStore SARL**  
**Secteur** : Distribution (3 magasins + siège)  
**Effectif** : 85 personnes  
**Infrastructure** :
- Siège : Paris (datacenter principal)
- Magasins : Lyon, Marseille, Lille
- VPN site-à-site (MPLS)
- Active Directory : `megastore.local`
- Serveurs : Windows Server 2022 + Debian 12
- Virtualisation : VMware vSphere 8

**Votre rôle** : Technicien support N2  
**Contexte** : Lundi 9h00, vous arrivez et trouvez 3 tickets urgents escaladés par le N1.

---

---

## 🚨 INCIDENT 1 : PROBLÈME DNS - RÉSOLUTION NOMS (30 points)

### 📌 Ticket #2547 - Priorité : HAUTE

**Rapporté par** : Sophie (Comptabilité)  
**Symptômes** :
- Depuis ce matin, **impossible d'accéder aux ressources par nom**
- Exemples : `\\serveur-fichiers\partages` → Erreur "Nom introuvable"
- Accès par **IP fonctionne** : `\\192.168.10.20\partages` → OK
- **Messagerie Outlook** : Erreur de connexion Exchange
- **Internet** : Sites web inaccessibles (Google, etc.)

**Informations complémentaires** :
- Affecte **tous les postes** du service Comptabilité (15 PC)
- Autres services (Commercial, RH) : **Aucun problème**
- Les PC ont été **redémarrés** ce week-end (mises à jour Windows)
- Plage IP Comptabilité : `192.168.30.0/24`
- Serveur DNS principal : `SRV-DNS-01` (192.168.10.10)
- Serveur DNS secondaire : `8.8.8.8` (Google DNS public)

**Tests effectués par le N1** :
```cmd
C:\> ipconfig /all
   Carte Ethernet :
   IPv4 : 192.168.30.105
   Masque : 255.255.255.0
   Passerelle : 192.168.30.1
   Serveurs DNS : (vide)

C:\> ping 192.168.10.10
   Réponse de 192.168.10.10 : octets=32 temps<1ms TTL=64

C:\> ping serveur-fichiers
   Impossible de trouver l'hôte serveur-fichiers.
```

---

### ❓ Questions :

**Question 1.1** (5 points) : Quelle est la **cause racine** du problème ? Justifiez.

```
Votre réponse :
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
```

**Question 1.2** (8 points) : Proposez une **solution immédiate** (temporaire) pour permettre aux utilisateurs de travailler en attendant une correction définitive. Détaillez la procédure.

```
Votre réponse :
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
```

**Question 1.3** (10 points) : Quelle est la **solution pérenne** ? Où se situe le problème (serveur DHCP, GPO, configuration manuelle, etc.) ? Détaillez les étapes de correction.

```
Votre réponse :
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
```

**Question 1.4** (4 points) : Comment **tester et valider** que la résolution DNS fonctionne correctement après correction ? (3 tests différents)

```
Votre réponse :
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
```

**Question 1.5** (3 points) : Expliquez le rôle du fichier **hosts** (`C:\Windows\System32\drivers\etc\hosts`) et dans quel contexte il peut être utilisé comme solution de contournement.

```
Votre réponse :
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
```


---

---

## 🔐 INCIDENT 2 : SCRIPT BASH - SAUVEGARDE AUTOMATISÉE (30 points)

### 📌 Ticket #2548 - Priorité : HAUTE

**Rapporté par** : Marc (Responsable IT)  
**Contexte** :
Vous devez créer un **script Bash** pour automatiser la sauvegarde des fichiers de configuration critiques sur le serveur **SRV-DEBIAN-01** (Debian 12).

**Objectifs du script** :
1. **Sauvegarder** les fichiers suivants dans une archive compressée :
   - `/etc/network/interfaces` (config réseau)
   - `/etc/fstab` (montages disques)
   - `/etc/exports` (partages NFS)
   - `/etc/apache2/sites-available/` (config Apache)
   - `/etc/bind/` (config DNS Bind9)

2. **Nommer l'archive** : `backup_config_AAAAMMJJ_HHMMSS.tar.gz`  
   Exemple : `backup_config_20251112_143025.tar.gz`

3. **Stocker** l'archive dans `/srv/backups/configs/`

4. **Rotation** : Supprimer automatiquement les archives de **plus de 30 jours**

5. **Log** : Enregistrer les opérations dans `/var/log/backup_config.log` avec horodatage

6. **Email** : Envoyer un email récapitulatif à `admin@megastore.local` avec :
   - Statut (succès/échec)
   - Taille de l'archive créée
   - Nombre de fichiers sauvegardés

7. **Planification** : Le script doit s'exécuter **automatiquement chaque jour à 2h00 du matin**

---

### ❓ Questions :

**Question 2.1** (15 points) : Écrivez le **script Bash complet** répondant à tous les objectifs ci-dessus.

```bash
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
```


**Question 2.2** (5 points) : Quelle commande **cron** utilisez-vous pour planifier l'exécution quotidienne à 2h00 ? Expliquez la syntaxe.

```
Votre réponse :
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
```

**Question 2.3** (5 points) : Comment **tester** le script manuellement avant de le planifier ? Détaillez les étapes de validation.

```
Votre réponse :
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
```

**Question 2.4** (3 points) : Ajoutez une fonctionnalité pour **vérifier l'intégrité** de l'archive après création (somme de contrôle MD5). Comment l'intégrer au script ?

```
Votre réponse :
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
```

**Question 2.5** (2 points) : Quelles **permissions** attribuer au script pour qu'il soit exécutable uniquement par `root` ? Commande complète.

```
Votre réponse :
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
```

---

## 🖧 INCIDENT 3 : PROBLÈME VMWARE - VM INACCESSIBLE (40 points)

### 📌 Ticket #2549 - Priorité : CRITIQUE

**Rapporté par** : Directeur (alerte monitoring)  
**Symptômes** :
- La VM **SRV-WEB-PROD** (serveur web de production) est **inaccessible** depuis 30 minutes
- Clients ne peuvent plus commander sur le site e-commerce
- **Perte estimée** : 2000€/heure

**Informations infrastructure** :
- **Cluster vSphere** : 3 hôtes ESXi (ESXi-01, ESXi-02, ESXi-03)
- **vSphere HA** : Activé
- **Datastore** : SAN iSCSI partagé (VMFS6) - `DS_PROD_01` (2 To)
- **VM concernée** :
  - Nom : `SRV-WEB-PROD`
  - OS : Ubuntu Server 22.04
  - vCPU : 4 | RAM : 8 Go | Disque : 100 Go (thin provisioning)
  - Dernière action : **Snapshot** créé vendredi soir pour tests

**Logs vSphere (vCenter)** :
```
[2025-11-12 08:25:14] WARNING: SRV-WEB-PROD - Snapshot chain too long (4 snapshots)
[2025-11-12 08:28:32] ERROR: Datastore DS_PROD_01 - Low disk space (95% used)
[2025-11-12 08:30:45] CRITICAL: SRV-WEB-PROD - Cannot power on: Insufficient disk space
[2025-11-12 08:31:12] INFO: HA attempted restart on ESXi-02 - FAILED (same error)
```

**Tests effectués par le N1** :
- Connexion vSphere Client : OK
- VM affichée comme **"Powered Off"**
- Tentative de démarrage manuel : **Erreur** "Insufficient resources"
- Datastore `DS_PROD_01` : **1.9 To utilisés sur 2 To** (95%)

**Inventaire Datastore DS_PROD_01** :
```
SRV-WEB-PROD/
  SRV-WEB-PROD.vmx (config)
  SRV-WEB-PROD.vmdk (100 Go - disque de base)
  SRV-WEB-PROD-000001.vmdk (25 Go - snapshot 1)
  SRV-WEB-PROD-000002.vmdk (40 Go - snapshot 2)
  SRV-WEB-PROD-000003.vmdk (55 Go - snapshot 3)
  SRV-WEB-PROD-000004.vmdk (80 Go - snapshot 4)
  SRV-WEB-PROD.vswp (8 Go - swap file)
  Total VM : ~308 Go

Autres VMs :
  SRV-APP-01 : 200 Go
  SRV-DB-01 : 400 Go
  SRV-FILE-01 : 600 Go
  [...autres VMs] : ~442 Go
```

---

### ❓ Questions :

**Question 3.1** (6 points) : Quelle est la **cause racine** du problème ? Expliquez la relation entre les snapshots et la saturation du datastore.

```
Votre réponse :
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
```

**Question 3.2** (12 points) : Proposez une **solution d'urgence** pour redémarrer rapidement la VM de production. Détaillez la procédure **étape par étape** (actions dans vSphere Client).

```
Votre réponse :
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
```

**Question 3.3** (8 points) : Comment **consolider les snapshots** sans perdre de données ? Expliquez les risques et précautions à prendre.

```
Votre réponse :
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
```

**Question 3.4** (6 points) : Proposez **3 actions préventives** pour éviter que ce problème ne se reproduise (monitoring, bonnes pratiques, alertes).

```
Votre réponse :
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
```

**Question 3.5** (8 points) : Le directeur demande une solution **haute disponibilité** pour ce serveur web critique. Proposez une architecture avec :
- **DRS** pour équilibrage de charge
- **vSphere HA** pour redémarrage automatique
- **Réplication de la VM** sur un second datastore

```
Votre réponse :
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
```

---

# PARTIE 2 : QUESTIONNAIRE TECHNIQUE (100 points - 2h00)

---

## 🐧 SECTION A : LINUX (20 points)

### Question A1 (4 points) - Commande tar

Vous devez créer une archive compressée **gzip** du dossier `/var/www/html/` et l'enregistrer dans `/backups/web_backup.tar.gz`.

**a)** Quelle commande complète utilisez-vous ? (2 pts) 

```
Votre réponse :
____________________________________________________________________________
```

**b)** Comment **extraire** cette archive dans `/tmp/restore/` ? (2 pts) 

```
Votre réponse :
____________________________________________________________________________
```


---

### Question A2 (4 points) - Cron et planification

Voici une ligne crontab :
```
30 2 * * 1-5 /usr/local/bin/maintenance.sh
```

**a)** À quelle **fréquence** ce script s'exécute-t-il ? (2 pts) 

```
Votre réponse :
____________________________________________________________________________
```

**b)** Comment modifier pour qu'il s'exécute **tous les jours à minuit** ? (2 pts) 

```
Votre réponse :
____________________________________________________________________________
```

---

### Question A3 (4 points) - Gestion des processus

Un processus consomme **90% CPU** et ralentit le serveur.

**a)** Quelle commande utilisez-vous pour **identifier** ce processus (PID, nom, consommation) ? (2 pts)

```
Votre réponse :
____________________________________________________________________________
``` 

**b)** Comment **arrêter proprement** ce processus (PID 12345) ? Citez 2 méthodes. (2 pts)

```
Votre réponse :
____________________________________________________________________________
```

---

### Question A4 (4 points) - Montage NFS

Vous devez monter un partage NFS distant automatiquement au démarrage.

**Informations** :
- Serveur NFS : `192.168.10.50`
- Partage : `/export/data`
- Point de montage local : `/mnt/nfs_data`

**a)** Quelle ligne ajouter dans `/etc/fstab` ? (3 pts)

```
Votre réponse :
____________________________________________________________________________
```

**b)** Comment tester le montage **sans redémarrer** ? (1 pt) 

```
Votre réponse :
____________________________________________________________________________
```

---

### Question A5 (4 points) - Gestion des logs

**a)** Où se trouvent les logs système principaux sous Debian 12 ? (1 pt) /var/log/journald

**b)** Commande pour afficher les **20 dernières lignes** du fichier `/var/log/syslog` en temps réel. (2 pts)

```
Votre réponse :
____________________________________________________________________________
```

**c)** Comment rechercher toutes les lignes contenant "error" dans ce fichier (insensible à la casse) ? (1 pt)

```
Votre réponse :
____________________________________________________________________________
```

---

---

## 🌐 SECTION B : RÉSEAU (25 points)

### Question B1 (6 points) - Subnetting CIDR

Vous devez sous-diviser le réseau **10.20.0.0/22** en **4 sous-réseaux égaux**.

**a)** Quel est le **nouveau préfixe CIDR** (/X) pour chaque sous-réseau ? (2 pts)

```
Votre réponse :
____________________________________________________________________________
```

**b)** Donnez les **4 réseaux** avec leur masque. (4 pts)

```
Votre réponse :
____________________________________________________________________________
```

---

### Question B2 (5 points) - ACL Cisco étendue

Créez une **ACL étendue** nommée `ACL_WEB_SERVER` qui autorise :
- Trafic **HTTP** (port 80) et **HTTPS** (port 443) vers le serveur web `192.168.100.50`
- Depuis n'importe quelle source
- Bloquer **tout le reste**

Fournissez les commandes Cisco IOS complètes.

```
Votre réponse :
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
```


### Question B3 (5 points) - Routage inter-VLAN

**Topologie** :
```
Switch L3
  ├─ VLAN 10 (192.168.10.0/24) - Gestion
  ├─ VLAN 20 (192.168.20.0/24) - Commercial
  └─ VLAN 30 (192.168.30.0/24) - Comptabilité
```

**Objectif** : Configurer le routage inter-VLAN sur un switch Cisco L3.

**a)** Commandes pour créer les **interfaces SVI** des 3 VLANs avec les IP `.1` (passerelles). (4 pts)

```
Votre réponse :
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
```


**b)** Comment **activer le routage IP** sur le switch L3 ? (1 pt)

```
Votre réponse :
____________________________________________________________________________
```

---

### Question B4 (5 points) - Diagnostic réseau

Un utilisateur ne peut pas atteindre le serveur `10.50.1.100`.

**a)** Citez **3 commandes** de diagnostic réseau (Windows ou Linux) et leur utilité. (3 pts)
```
Votre réponse :
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
```

**b)** Expliquez la différence entre `tracert` et `pathping` (Windows). (2 pts)
```
Votre réponse :
____________________________________________________________________________
____________________________________________________________________________
```

---

### Question B5 (4 points) - NAT et PAT

**a)** Quelle est la différence entre **NAT** (Network Address Translation) et **PAT** (Port Address Translation) ? (2 pts) 

```
Votre réponse :
____________________________________________________________________________
____________________________________________________________________________
```

**b)** Donnez un exemple concret d'utilisation du PAT dans un réseau d'entreprise. (2 pts)
```
Votre réponse :
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
```

---

---

## 🪟 SECTION C : WINDOWS / ACTIVE DIRECTORY (25 points)

### Question C1 (5 points) - Commandes PowerShell AD

**a)** Commande PowerShell pour **lister tous les utilisateurs** désactivés dans l'OU `OU=Personnel,DC=megastore,DC=local`. (3 pts) 

```
Votre réponse :
____________________________________________________________________________
____________________________________________________________________________
```

**b)** Commande pour **réactiver** le compte utilisateur `jdupont`. (2 pts) 

```
Votre réponse :
____________________________________________________________________________
____________________________________________________________________________
```

---

### Question C2 (5 points) - GPO de sécurité

Vous devez créer une GPO qui applique les paramètres suivants aux PC du service Comptabilité :
- **Désactiver l'USB** (empêcher utilisation clés USB)
- **Verrouillage automatique** après 5 minutes d'inactivité
- **Complexité** des mots de passe (longueur min 12 caractères)

**a)** Dans quel(s) nœud(s) de la GPO configurez-vous chaque paramètre ? (3 pts)

```
Votre réponse :
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
```

**b)** Comment **forcer l'application immédiate** de cette GPO sur un PC sans attendre ? (2 pts)

```
Votre réponse :
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
```

---

### Question C3 (5 points) - DNS Windows Server

**a)** Quelle est la différence entre une **zone principale** et une **zone secondaire** DNS ? (2 pts)

```
Votre réponse :
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
```

**b)** Comment créer un **enregistrement A** pour `web.megastore.local` pointant vers `192.168.100.50` ? (GUI ou PowerShell) (3 pts)

```
Votre réponse :
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
```

---

### Question C4 (5 points) - Sauvegarde Windows Server

**a)** Citez **2 méthodes natives** de sauvegarde sous Windows Server 2022. (2 pts)

```
Votre réponse :
____________________________________________________________________________
____________________________________________________________________________
```


**b)** Quelle commande **wbadmin** permet de lancer une sauvegarde complète du système vers le disque `E:` ? (3 pts)

```
Votre réponse :
____________________________________________________________________________

```

---

### Question C5 (5 points) - Stratégies de mot de passe

**a)** Comment configurer une **stratégie de mot de passe fine** (Fine-Grained Password Policy) pour un groupe spécifique d'utilisateurs ? (3 pts)

```
Votre réponse :
____________________________________________________________________________
____________________________________________________________________________
```


**b)** Quelle est la différence avec la stratégie de mot de passe de domaine par défaut ? (2 pts)

```
Votre réponse :
____________________________________________________________________________
____________________________________________________________________________
```

---

---

## 💻 SECTION D : VIRTUALISATION (15 points)

### Question D1 (5 points) - Types de disques VMware

**a)** Expliquez les différences entre disque **Thick Provision Lazy Zeroed**, **Thick Provision Eager Zeroed**, et **Thin Provision**. (3 pts) 

```
Votre réponse :
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
```

**b)** Quel type choisir pour une **VM de production** critique (base de données) ? Pourquoi ? (2 pts)

```
Votre réponse :
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
```
---

### Question D2 (5 points) - vMotion Storage

**a)** Quelle est la différence entre **vMotion** et **Storage vMotion** ? (2 pts)
```
Votre réponse :
____________________________________________________________________________
____________________________________________________________________________

```

**b)** Citez **2 cas d'usage** de Storage vMotion. (2 pts)

```
Votre réponse :
____________________________________________________________________________
____________________________________________________________________________
```


**c)** Peut-on effectuer un Storage vMotion sur une **VM allumée** ? (1 pt)

```
Votre réponse :
____________________________________________________________________________
```

---

### Question D3 (5 points) - Datastore et VMFS

**a)** Quelle est la **taille de bloc maximale** de VMFS6 ? (1 pt)

```
Votre réponse :
____________________________________________________________________________
```

**b)** Combien de **hôtes ESXi** peuvent accéder simultanément à un datastore VMFS ? (1 pt)

```
Votre réponse :
____________________________________________________________________________
```

**c)** Comment **augmenter la capacité** d'un datastore VMFS existant ? (2 méthodes) (3 pts)
```
Votre réponse :
____________________________________________________________________________
____________________________________________________________________________
```

---

---

## 🔒 SECTION E : SÉCURITÉ & SAUVEGARDES (15 points)

### Question E1 (5 points) - Stratégie de sauvegarde GFS

**a)** Expliquez le principe de la stratégie **GFS** (Grandfather-Father-Son). (3 pts)

```
Votre réponse :
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
```

**b)** Combien de bandes/médias sont nécessaires pour une stratégie GFS complète (1 mois) ? (2 pts)

```
Votre réponse :
____________________________________________________________________________
```

---

### Question E2 (5 points) - Ransomware et protection

Un ransomware a chiffré les fichiers d'un utilisateur.

**a)** Citez **3 mesures préventives** contre les ransomwares. (3 pts)

```
Votre réponse :
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
```

**b)** Quelle est l'utilité d'une sauvegarde **offline** (air gap) ? (2 pts)

```
Votre réponse :
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
```

---

### Question E3 (5 points) - Pare-feu et DMZ

**a)** Qu'est-ce qu'une **DMZ** (Zone Démilitarisée) ? (2 pts)

```
Votre réponse :
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
```

**b)** Quels types de serveurs place-t-on généralement dans une DMZ ? (2 pts)

```
Votre réponse :
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
```

**c)** Quelle règle de pare-feu typique applique-t-on entre la DMZ et le réseau interne ? (1 pt)

```
Votre réponse :
____________________________________________________________________________
____________________________________________________________________________
```

---

---

## 🇬🇧 SECTION F : ANGLAIS TECHNIQUE (5 points)

### Question F1 (5 points) - Traduction et compréhension

**Texte anglais** :

> **Network Outage - Post Incident Report**
> 
> On November 10th, 2025, a widespread network outage affected all users from 10:30 AM to 12:15 PM. Root cause analysis revealed that a misconfigured BGP route caused a routing loop, resulting in packet loss and latency spikes. The issue was resolved by rolling back the router configuration to the previous stable version. To prevent recurrence, we implemented automated configuration backups and enhanced monitoring alerts.

---

**a)** Traduisez le texte en français. (3 pts)

```
Votre réponse :
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
```

**b)** Résumez en français (3-4 phrases) : Quelle était la cause du problème et quelle solution a été appliquée ? (2 pts)

```
Votre réponse :
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
```


---

---

---

# ✅ FIN DE L'EXAMEN BLANC 2

**Pensez à relire vos réponses ! ✍️**

**Barème total** : 200 points  
**Durée totale** : 3h30  

**Bon courage ! 🎯**
