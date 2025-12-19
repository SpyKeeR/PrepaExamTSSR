# ☁️ QCM VIRTUALISATION - 40 QUESTIONS
## VMware vSphere, ESXi, vCenter, HA, DRS, Snapshots, VDI, Storage

> **Pour l'examen** : Architecture VMware, différence snapshot/clone, HA/DRS, stockage (VMFS/NFS/iSCSI), VDI

---

## 📋 PARTIE 1 : FONDAMENTAUX VIRTUALISATION (Questions 1-10)

### Question 1
Qu'est-ce que la **virtualisation** ?
- [ ] A) Installer plusieurs OS sur plusieurs ordinateurs
- [ ] B) Créer des machines virtuelles (VM) qui s'exécutent sur un serveur physique (hyperviseur)
- [ ] C) Un système de sauvegarde
- [ ] D) Un pare-feu

### Question 2
Qu'est-ce qu'un **hyperviseur** ?
- [ ] A) Un administrateur système
- [ ] B) La couche logicielle qui permet de créer et gérer des machines virtuelles
- [ ] C) Un système d'exploitation
- [ ] D) Un câble réseau

### Question 3
Quelle est la différence entre hyperviseur **Type 1** (bare-metal) et **Type 2** (hosted) ?
- [ ] A) Aucune différence
- [ ] B) Type 1 = installé directement sur le matériel (ESXi, Hyper-V) / Type 2 = sur un OS (VMware Workstation, VirtualBox)
- [ ] C) Type 1 = gratuit, Type 2 = payant
- [ ] D) Type 1 = Windows, Type 2 = Linux

### Question 4
Quels sont les avantages de la virtualisation ?
- [ ] A) Consolidation serveurs, réduction coûts, haute disponibilité, déploiement rapide
- [ ] B) Uniquement économique
- [ ] C) Aucun avantage
- [ ] D) Plus lent que le physique

### Question 5
Qu'est-ce que le **ratio de consolidation** ?
- [ ] A) La vitesse du réseau
- [ ] B) Le nombre de VM par serveur physique (ex: 20 VM sur 1 ESXi)
- [ ] C) La taille des disques
- [ ] D) Le nombre de snapshots

### Question 6
Que signifie **overcommit** (sur-allocation) en virtualisation ?
- [ ] A) Allouer moins de ressources que disponible
- [ ] B) Allouer plus de ressources (CPU/RAM) que physiquement disponible
- [ ] C) Désactiver une VM
- [ ] D) Un type de stockage

### Question 7
Qu'est-ce qu'une **VM Template** (modèle) ?
- [ ] A) Une VM en cours d'exécution
- [ ] B) Une VM master pré-configurée, servant de base pour déployer rapidement de nouvelles VM
- [ ] C) Un snapshot
- [ ] D) Un datacenter

### Question 8
Que signifie **vMotion** (VMware) ?
- [ ] A) Déplacer une VM en cours d'exécution d'un hôte ESXi à un autre sans interruption
- [ ] B) Arrêter une VM
- [ ] C) Cloner une VM
- [ ] D) Créer un snapshot

### Question 9
Quelle technologie permet de partager les ressources CPU inutilisées entre VM ?
- [ ] A) Snapshot
- [ ] B) Overcommit / Partage dynamique (CPU Shares, Reservations, Limits)
- [ ] C) Template
- [ ] D) ISO

### Question 10
Qu'est-ce que le **VDI** (Virtual Desktop Infrastructure) ?
- [ ] A) Un serveur web
- [ ] B) Des postes de travail virtualisés (bureaux virtuels) accessibles à distance
- [ ] C) Un système de sauvegarde
- [ ] D) Un protocole réseau

---

## 🖥️ PARTIE 2 : VMWARE vSPHERE - ARCHITECTURE (Questions 11-20)

### Question 11
Qu'est-ce que **VMware ESXi** ?
- [ ] A) Un système d'exploitation Windows
- [ ] B) Un hyperviseur Type 1 (bare-metal) qui héberge les VM
- [ ] C) Un logiciel de sauvegarde
- [ ] D) Un client de virtualisation

### Question 12
Qu'est-ce que **VMware vCenter Server** ?
- [ ] A) Un hyperviseur
- [ ] B) La console centralisée de gestion des hôtes ESXi et des VM
- [ ] C) Un système de fichiers
- [ ] D) Un protocole

### Question 13
Quelle est la différence entre **ESXi** et **vCenter** ?
- [ ] A) Aucune différence
- [ ] B) ESXi = hyperviseur (hôte) / vCenter = gestionnaire centralisé (plusieurs ESXi)
- [ ] C) ESXi = gratuit, vCenter = payant
- [ ] D) ESXi = Linux, vCenter = Windows

### Question 14
Peut-on gérer un hôte ESXi sans vCenter ?
- [ ] A) Non, vCenter obligatoire
- [ ] B) Oui, via l'interface web ESXi (Host Client) mais fonctionnalités limitées
- [ ] C) Impossible de gérer ESXi
- [ ] D) Uniquement en ligne de commande

### Question 15
Qu'est-ce qu'un **Datacenter** dans vSphere ?
- [ ] A) Un bâtiment physique
- [ ] B) Un conteneur logique dans vCenter pour organiser hôtes, VM, stockage
- [ ] C) Un serveur
- [ ] D) Un réseau

### Question 16
Qu'est-ce qu'un **Cluster** vSphere ?
- [ ] A) Un groupe de VM
- [ ] B) Un groupe d'hôtes ESXi partageant ressources, avec HA et DRS
- [ ] C) Un disque dur
- [ ] D) Un snapshot

### Question 17
Quelle est la hiérarchie de l'architecture vSphere ?
- [ ] A) Datacenter → Cluster → ESXi → VM
- [ ] B) VM → ESXi → Cluster → Datacenter
- [ ] C) ESXi → VM → Cluster
- [ ] D) Cluster → Datacenter → VM

### Question 18
Quel port utilise vCenter pour communiquer avec ESXi ?
- [ ] A) 80
- [ ] B) 443 (HTTPS)
- [ ] C) 22
- [ ] D) 3389

### Question 19
Qu'est-ce que **VMware Tools** ?
- [ ] A) Un hyperviseur
- [ ] B) Des drivers et utilitaires installés dans la VM pour améliorer performances et intégration
- [ ] C) Un système d'exploitation
- [ ] D) Un pare-feu

### Question 20
Pourquoi installer VMware Tools dans une VM ?
- [ ] A) Ce n'est pas utile
- [ ] B) Améliore performances, synchronisation horloge, shutdown propre, copier-coller hôte/VM, drivers optimisés
- [ ] C) Obligatoire pour démarrer la VM
- [ ] D) Pour la sauvegarde uniquement

---

## 🔄 PARTIE 3 : HA, DRS, VMOTION (Questions 21-28)

### Question 21
Qu'est-ce que **vSphere HA** (High Availability) ?
- [ ] A) Un outil de monitoring
- [ ] B) Redémarre automatiquement les VM sur un autre hôte si un hôte ESXi tombe
- [ ] C) Clone les VM
- [ ] D) Crée des snapshots

### Question 22
Comment fonctionne vSphere HA lors d'une panne d'hôte ?
- [ ] A) Les VM restent arrêtées
- [ ] B) HA détecte la panne, les VM sont redémarrées sur les hôtes survivants du cluster
- [ ] C) Les VM sont supprimées
- [ ] D) Il faut redémarrer manuellement

### Question 23
Qu'est-ce que **vSphere DRS** (Distributed Resource Scheduler) ?
- [ ] A) Un système de sauvegarde
- [ ] B) Équilibre automatiquement la charge CPU/RAM en déplaçant les VM entre hôtes (via vMotion)
- [ ] C) Un pare-feu
- [ ] D) Un snapshot

### Question 24
Quelle est la différence entre **HA** et **DRS** ?
- [ ] A) Aucune différence
- [ ] B) HA = disponibilité (reprise après panne) / DRS = performance (équilibrage charge)
- [ ] C) HA = gratuit, DRS = payant
- [ ] D) HA = Windows, DRS = Linux

### Question 25
Qu'est-ce que **vMotion** permet de faire ?
- [ ] A) Créer une VM
- [ ] B) Migrer une VM allumée d'un hôte ESXi à un autre sans interruption (Live Migration)
- [ ] C) Supprimer une VM
- [ ] D) Créer un template

### Question 26
Quelles sont les conditions pour utiliser vMotion ?
- [ ] A) Stockage partagé (SAN/NAS), réseau vMotion dédié, CPU compatibles
- [ ] B) Aucune condition
- [ ] C) VM éteinte obligatoire
- [ ] D) Un seul hôte ESXi

### Question 27
Qu'est-ce que **Storage vMotion** ?
- [ ] A) Déplacer une VM entre hôtes
- [ ] B) Déplacer les disques d'une VM d'un datastore à un autre (VM allumée ou éteinte)
- [ ] C) Créer un snapshot
- [ ] D) Cloner une VM

### Question 28
Qu'est-ce que **Fault Tolerance** (FT) ?
- [ ] A) Un snapshot
- [ ] B) Réplication synchrone d'une VM (VM miroir active en temps réel, 0 downtime si panne)
- [ ] C) Un backup
- [ ] D) Un template

---

## 💾 PARTIE 4 : STOCKAGE (VMFS, NFS, iSCSI) (Questions 29-34)

### Question 29
Qu'est-ce qu'un **Datastore** ?
- [ ] A) Une VM
- [ ] B) Un espace de stockage où sont stockés les fichiers des VM (VMDK, VMX, snapshots)
- [ ] C) Un réseau
- [ ] D) Un hyperviseur

### Question 30
Qu'est-ce que **VMFS** (Virtual Machine File System) ?
- [ ] A) Un protocole réseau
- [ ] B) Le système de fichiers propriétaire VMware pour les datastores (accès concurrent par plusieurs ESXi)
- [ ] C) Un hyperviseur
- [ ] D) Une VM

### Question 31
Quels sont les 3 protocoles de stockage supportés par vSphere ?
- [ ] A) HTTP, FTP, SMB
- [ ] B) **Fibre Channel (FC), iSCSI, NFS**
- [ ] C) SMTP, POP3, IMAP
- [ ] D) SSH, Telnet, RDP

### Question 32
Quelle est la différence entre **NFS** et **iSCSI** ?
- [ ] A) Aucune différence
- [ ] B) NFS = stockage fichiers (partage NAS) / iSCSI = stockage bloc (SAN sur IP)
- [ ] C) NFS = Windows, iSCSI = Linux
- [ ] D) NFS = lent, iSCSI = rapide

### Question 33
Qu'est-ce qu'un **LUN** en stockage iSCSI/FC ?
- [ ] A) Un hyperviseur
- [ ] B) Une unité logique de stockage (volume présenté par le SAN)
- [ ] C) Une VM
- [ ] D) Un snapshot

### Question 34
Quel protocole utilise le port **3260** ?
- [ ] A) NFS
- [ ] B) iSCSI
- [ ] C) Fibre Channel
- [ ] D) SMB

---

## 📸 PARTIE 5 : SNAPSHOTS, CLONES, TEMPLATES (Questions 35-40)

### Question 35
Qu'est-ce qu'un **snapshot** ?
- [ ] A) Une copie complète de la VM
- [ ] B) Un état figé de la VM à un instant T (delta disks, pas une copie complète)
- [ ] C) Un template
- [ ] D) Un backup

### Question 36
Quelle est la différence entre un **snapshot** et un **backup** ?
- [ ] A) Aucune différence
- [ ] B) Snapshot = même datastore, temporaire, retour arrière rapide / Backup = copie externe, long terme
- [ ] C) Snapshot = externe, Backup = local
- [ ] D) Ce sont des synonymes

### Question 37
Peut-on garder des snapshots indéfiniment ?
- [ ] A) Oui, sans problème
- [ ] B) Non ! Impact performances (chaîne delta disks), espace disque, risque de perte si trop nombreux
- [ ] C) Oui, c'est recommandé
- [ ] D) Les snapshots disparaissent automatiquement

### Question 38
Quelle est la différence entre un **clone** et un **template** ?
- [ ] A) Aucune différence
- [ ] B) Clone = copie indépendante (modifiable) / Template = master en lecture seule (pour déploiement multiple)
- [ ] C) Clone = Windows, Template = Linux
- [ ] D) Clone = gratuit, Template = payant

### Question 39
Qu'est-ce qu'un **Linked Clone** ?
- [ ] A) Une copie complète
- [ ] B) Un clone partageant le disque de base (parent) avec des delta disks (économie d'espace)
- [ ] C) Un snapshot
- [ ] D) Un template

### Question 40
Quels fichiers composent une VM VMware ?
- [ ] A) Uniquement .vmx
- [ ] B) **.vmx** (config), **.vmdk** (disque virtuel), **.nvram** (BIOS), **.vswp** (swap), **.log** (logs)
- [ ] C) .exe et .dll
- [ ] D) .iso uniquement

---
