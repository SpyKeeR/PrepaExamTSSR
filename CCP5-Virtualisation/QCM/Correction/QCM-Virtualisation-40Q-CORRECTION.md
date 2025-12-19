# ✅ CORRECTIONS DÉTAILLÉES

### ✔️ Partie 1 : Fondamentaux Virtualisation

**Question 1 : B) Créer des VM sur un serveur physique**

Virtualisation :
- **Principe** : Partitionner un serveur physique en plusieurs machines virtuelles
- **Bénéfices** :
  - **Consolidation** : 1 serveur physique → 10-50 VM
  - **Isolation** : Chaque VM indépendante
  - **Flexibilité** : Création/suppression rapide
  - **Économies** : Réduction matériel, énergie, espace

**Question 2 : B) Couche logicielle gérant les VM**

Hyperviseur (Virtual Machine Monitor) :
- Gère l'accès au matériel (CPU, RAM, disque, réseau)
- Isole les VM entre elles
- Répartit les ressources

**Question 3 : B) Type 1 = bare-metal, Type 2 = hosted**

| Critère | Type 1 (Bare-Metal) | Type 2 (Hosted) |
|---------|-------------------|-----------------|
| **Installation** | Directement sur matériel | Sur un OS existant |
| **Performance** | Excellente | Moyenne (overhead OS) |
| **Usage** | Production datacenter | Tests, développement |
| **Exemples** | VMware ESXi, Hyper-V, Xen, KVM | VMware Workstation, VirtualBox, Parallels |

**Architecture Type 1** :
```
VM1  VM2  VM3
     ↓
Hyperviseur (ESXi)
     ↓
Matériel physique
```

**Architecture Type 2** :
```
VM1  VM2
     ↓
Hyperviseur (Workstation)
     ↓
OS Hôte (Windows/Linux)
     ↓
Matériel physique
```

**Question 4 : A) Consolidation, réduction coûts, HA, déploiement rapide**

Avantages détaillés :

**1. Consolidation** :
- Avant : 50 serveurs physiques (1 app/serveur, utilisation 10%)
- Après : 5 serveurs physiques avec 50 VM (utilisation 70%)

**2. Économies** :
- Matériel : -80% de serveurs
- Énergie : -70% consommation électrique
- Espace : -80% surface datacenter
- Refroidissement : -70%

**3. Haute disponibilité** :
- HA : Redémarrage auto si panne
- vMotion : Migration sans coupure
- FT : Tolérance zéro downtime

**4. Déploiement rapide** :
- Serveur physique : 2-4 semaines (commande, livraison, install)
- VM : 5 minutes (clone de template)

**5. Sauvegarde/Restauration** :
- Snapshot instantané
- Backup niveau hyperviseur
- Restauration complète en minutes

**6. Tests et Développement** :
- Environnements isolés
- Clonage facile
- Retour arrière rapide

**Question 5 : B) Nombre de VM par serveur physique**

Ratio de consolidation :
- **Moyenne** : 15-20 VM/serveur
- **Cas légers** : 50 VM/serveur (serveurs web, AD)
- **Cas lourds** : 5-10 VM/serveur (SQL, bases de données)

**Calcul** :
```
Serveur physique : 48 CPU cores, 512 Go RAM
VM moyenne : 2 vCPU, 16 Go RAM

Ratio théorique CPU : 48/2 = 24 VM
Ratio théorique RAM : 512/16 = 32 VM
→ Goulot = CPU → 24 VM max
```

**Question 6 : B) Allouer plus que disponible**

Overcommit (sur-allocation) :

**CPU Overcommit** :
- Serveur physique : 16 cores
- Allocation VM : 40 vCPU (ratio 2.5:1)
- Fonctionne car : VM n'utilisent pas 100% CPU en permanence

**RAM Overcommit** :
- Serveur physique : 256 Go RAM
- Allocation VM : 320 Go RAM
- Techniques :
  - **Transparent Page Sharing** (TPS) : Partage pages mémoire identiques
  - **Ballooning** : Récupère RAM inutilisée
  - **Compression** : Compresse pages mémoire
  - **Swap** : En dernier recours (lent)

**Risques** :
- Overcommit excessif → Swap → Performances dégradées
- Recommandation : Max 1.5:1 pour RAM

**Question 7 : B) VM master pré-configurée**

Template (modèle) :
- **Création** :
  1. Installer OS (Windows/Linux)
  2. Appliquer mises à jour
  3. Installer applications de base
  4. Configurer paramètres standards
  5. Sysprep (Windows) ou généralisation
  6. Convertir en template
- **Usage** : Déployer 50 VM identiques en 30 min
- **Statut** : Lecture seule (ne peut pas être démarré)

**Question 8 : A) Migrer VM allumée sans interruption**

vMotion (Live Migration) :
1. Copie état mémoire VM vers hôte cible
2. Synchronisation continue changements
3. Bascule finale (< 1 seconde de freeze)
4. Redirection réseau vers nouvel hôte
5. VM continue de tourner

**Usages** :
- Maintenance hôte (sans arrêter VM)
- Équilibrage charge (DRS)
- Évacuation hôte

**Prérequis** :
- Stockage partagé (SAN/NAS)
- Réseau vMotion dédié (10 Gbps recommandé)
- CPU compatibles (même famille)
- vCenter (orchestration)

**Question 9 : B) Overcommit / Partage dynamique**

Gestion ressources CPU :

**Shares (Parts)** :
- Priorité relative (Low/Normal/High/Custom)
- VM avec plus de shares → Plus de CPU si contention

**Reservation** :
- CPU garanti minimum (MHz)
- Exemple : 2000 MHz réservés

**Limit** :
- Plafond maximum (MHz)
- Exemple : Max 4000 MHz même si dispo

**Exemple** :
```
VM1 : 2 vCPU, Reservation 2000 MHz, Limit 4000 MHz, Shares High
VM2 : 2 vCPU, Shares Normal
VM3 : 1 vCPU, Shares Low

Si CPU saturé : VM1 > VM2 > VM3
```

**Question 10 : B) Postes de travail virtualisés**

VDI (Virtual Desktop Infrastructure) :

**Architecture** :
- Datacenter : Serveurs ESXi avec VM de bureaux Windows
- Utilisateurs : Thin clients ou PC
- Protocole : VMware Horizon (PCoIP, Blast), Citrix (ICA)

**Avantages** :
- **Centralisé** : Gestion facilitée (images, patches)
- **Sécurité** : Données restent au datacenter
- **Mobilité** : Accès depuis n'importe où
- **BYOD** : Utilisation devices personnels

**Types de VDI** :
- **Persistent** : Bureau dédié par user (personnalisations conservées)
- **Non-persistent** : Bureau générique, réinitialisé après déconnexion

**Cas d'usage** :
- Call centers
- Télétravail
- Contractors/consultants temporaires
- Postes nécessitant haute sécurité

---

### ✔️ Partie 2 : VMware vSphere - Architecture

**Question 11 : B) Hyperviseur Type 1**

VMware ESXi :
- **Version minimale** de l'hyperviseur (< 1 Go)
- Pas d'OS sous-jacent (bare-metal)
- Installation sur serveur physique
- Accès : Interface web (Host Client), SSH, vCenter

**Versions** :
- **ESXi** : Gratuit (fonctionnalités limitées)
- **vSphere** (ESXi + licence) : Payant (Standard, Enterprise, Enterprise Plus)

**Question 12 : B) Console centralisée de gestion**

vCenter Server :
- **Rôle** : Gestion centralisée de multiples hôtes ESXi
- **Fonctions avancées** :
  - vMotion, Storage vMotion
  - HA (High Availability)
  - DRS (Distributed Resource Scheduler)
  - Distributed Switch
  - Templates et clones
- **Installation** : Appliance Linux (VCSA) ou Windows (déprécié)

**Question 13 : B) ESXi = hôte, vCenter = gestionnaire**

Comparaison :

| Critère | ESXi | vCenter |
|---------|------|---------|
| **Rôle** | Hyperviseur (héberge VM) | Gestionnaire (pilote ESXi) |
| **Installation** | Sur serveur physique | Sur VM ou appliance |
| **Gestion** | Hôte unique | Plusieurs hôtes |
| **Fonctions** | Basiques | Avancées (HA, DRS, vMotion) |
| **Licence** | Gratuit ou payant | Payant |

**Architecture** :
```
        vCenter Server
             ↓
    ┌────────┼────────┐
ESXi-1    ESXi-2    ESXi-3
  ↓         ↓         ↓
10 VM     12 VM     15 VM
```

**Question 14 : B) Oui, via Host Client (limité)**

Gestion sans vCenter :
- **Interface web** : https://IP_ESXi/ui (ESXi Host Client)
- **Fonctions disponibles** :
  - Créer/gérer VM
  - Configuration réseau/stockage
  - Monitoring basique
- **Limitations** :
  - Pas de vMotion
  - Pas de HA/DRS
  - Pas de templates
  - Gestion hôte par hôte (pas centralisée)

**Question 15 : B) Conteneur logique vCenter**

Datacenter vSphere :
- Conteneur organisationnel dans vCenter
- Contient : Clusters, hôtes ESXi, VM, réseaux, datastores
- Permet séparation logique (par site, par client, par environnement)

Exemple :
```
vCenter
├─ Datacenter Paris
│  ├─ Cluster Production
│  │  ├─ ESXi-01
│  │  └─ ESXi-02
│  └─ Cluster Dev/Test
│     └─ ESXi-03
└─ Datacenter Lyon
   └─ Cluster DR
      ├─ ESXi-04
      └─ ESXi-05
```

**Question 16 : B) Groupe d'hôtes ESXi partageant ressources**

Cluster vSphere :
- **Pool de ressources** : CPU/RAM partagés
- **Fonctionnalités** :
  - HA (reprise après panne)
  - DRS (équilibrage charge automatique)
  - vMotion (migration VM)
- **Prérequis** : Stockage partagé

**Exemple** :
```
Cluster Production (3 hôtes)
├─ ESXi-01 : 32 cores, 256 Go RAM
├─ ESXi-02 : 32 cores, 256 Go RAM
└─ ESXi-03 : 32 cores, 256 Go RAM
Total : 96 cores, 768 Go RAM disponibles pour toutes les VM
```

**Question 17 : A) Datacenter → Cluster → ESXi → VM**

Hiérarchie complète :
```
vCenter Server
    ↓
Datacenter
    ↓
Cluster
    ↓
Hôte ESXi
    ↓
VM
```

**Question 18 : B) 443 (HTTPS)**

Ports VMware essentiels :

| Service | Port | Usage |
|---------|------|-------|
| **HTTPS (vCenter/ESXi)** | 443 | Gestion web, API |
| **vMotion** | 8000 | Migration VM |
| **NFS** | 2049 | Stockage NAS |
| **iSCSI** | 3260 | Stockage SAN |
| **SSH** | 22 | Administration CLI |

**Question 19 : B) Drivers et utilitaires dans la VM**

VMware Tools :
- **Composants** :
  - Drivers optimisés (réseau, stockage, vidéo)
  - Service de synchronisation horloge
  - Agent de communication avec ESXi
  - Utilitaires (copier-coller, drag & drop)

**Question 20 : B) Améliore performances et intégration**

Bénéfices VMware Tools :

**1. Performances** :
- Drivers paravirtualisés (10-40% plus rapide)
- Optimisation disque/réseau

**2. Gestion** :
- Shutdown/reboot propre depuis ESXi
- Récupération d'infos (IP, hostname)
- Exécution scripts depuis vCenter

**3. Confort** :
- Copier-coller hôte ↔ VM
- Drag & drop fichiers
- Redimensionnement écran automatique

**4. Monitoring** :
- Heartbeat (VM vivante ?)
- Metrics précises (CPU, RAM, disk)

**5. Snapshots** :
- Quiesce (flush cache, cohérence)

**Installation** :
- **Windows** : Installer VMware Tools.exe
- **Linux** : `vmware-install.pl` ou packages distro (open-vm-tools)

---

### ✔️ Partie 3 : HA, DRS, vMotion

**Question 21 : B) Redémarre VM automatiquement si hôte tombe**

vSphere HA (High Availability) :

**Principe** :
1. Cluster HA surveille tous les hôtes
2. Si hôte tombe → Les VM sont redémarrées sur hôtes survivants
3. Temps d'indisponibilité : 2-5 minutes (reboot VM)

**Configuration** :
- **Admission Control** : Réserve capacité pour pannes (N+1, N+2)
- **Priorities** : Ordre de redémarrage VM

**Exemple** :
```
Cluster 3 hôtes : ESXi-1 (10 VM), ESXi-2 (12 VM), ESXi-3 (8 VM)
↓
ESXi-1 tombe (panne matérielle)
↓
HA détecte (30 sec - 3 min)
↓
10 VM redémarrées sur ESXi-2 et ESXi-3
```

**Question 22 : B) Détection + Redémarrage automatique**

Mécanisme HA :

**Détection panne** :
- **Heartbeat réseau** : Ping entre hôtes toutes les secondes
- **Heartbeat datastore** : Écriture fichier partagé
- Si les 2 échouent → Hôte déclaré mort

**Actions** :
1. Master HA prend contrôle
2. Vérifie ressources disponibles
3. Place VM sur hôtes selon priorités
4. Power On VM

**Limitations** :
- VM arrêtées brutalement (pas shutdown propre)
- Applications doivent supporter arrêt brutal

**Question 23 : B) Équilibrage automatique charge CPU/RAM**

vSphere DRS (Distributed Resource Scheduler) :

**Principe** :
- Surveille charge CPU/RAM de tous les hôtes
- Si déséquilibre → Migre VM automatiquement (via vMotion)
- Maintient cluster équilibré

**Modes** :
- **Manual** : Recommandations seulement
- **Partially Automated** : Recommande + applique au power on
- **Fully Automated** : Applique automatiquement (recommandé)

**Exemple** :
```
Avant DRS :
ESXi-1 : 90% CPU (10 VM)
ESXi-2 : 30% CPU (3 VM)

Après DRS :
ESXi-1 : 60% CPU (7 VM) ← 3 VM migrées
ESXi-2 : 60% CPU (6 VM)
```

**Question 24 : B) HA = disponibilité, DRS = performance**

Comparaison :

| Critère | HA | DRS |
|---------|----|----|
| **Objectif** | Disponibilité | Performance |
| **Déclencheur** | Panne hôte | Déséquilibre charge |
| **Action** | Redémarre VM | Migre VM (vMotion) |
| **Downtime** | 2-5 min (reboot) | 0 (live migration) |
| **Quand** | Réactif (après incident) | Proactif (avant problème) |

**Utilisation combinée** :
- DRS équilibre en continu
- Si panne → HA redémarre
- Après redémarrage HA → DRS rééquilibre

**Question 25 : B) Migration Live VM sans interruption**

vMotion détails :

**Étapes** :
1. **Pré-copie mémoire** : Copie RAM VM vers hôte cible (VM tourne)
2. **Itérations** : Copie pages modifiées (plusieurs passes)
3. **Switchover** : Freeze VM brièvement (< 1 sec), bascule finale
4. **Activation** : VM reprend sur nouvel hôte
5. **Nettoyage** : Ancien hôte libère ressources

**Durée** : 10-60 secondes selon taille mémoire et bande passante

**Impact utilisateur** : Aucun (imperceptible)

**Question 26 : A) Stockage partagé, réseau vMotion, CPU compatibles**

Prérequis vMotion :

**1. Stockage partagé** :
- SAN (FC, iSCSI) ou NAS (NFS)
- VM stockées sur datastore accessible par les 2 hôtes

**2. Réseau vMotion** :
- VMkernel port type "vMotion"
- **Recommandé** : Réseau dédié 10 Gbps
- Même VLAN entre hôtes

**3. CPU compatibles** :
- Même famille/génération (Intel ↔ Intel, AMD ↔ AMD)
- EVC (Enhanced vMotion Compatibility) pour masquer différences mineures

**4. vCenter** :
- Orchestration vMotion

**5. Licences** :
- vSphere Standard minimum

**Question 27 : B) Déplacer disques VM entre datastores**

Storage vMotion :
- **Usage** : Migration stockage sans downtime
- **Scénarios** :
  - Datastore plein → Migrer vers autre
  - Upgrade SAN
  - Maintenance stockage
  - Optimisation performance (SSD vs HDD)

**Différence avec vMotion** :
- **vMotion** : Migration compute (hôte ESXi)
- **Storage vMotion** : Migration stockage (datastore)
- **Combiné** : Migration compute + stockage simultanée

**Question 28 : B) Réplication synchrone VM (0 downtime)**

Fault Tolerance (FT) :

**Principe** :
- VM primaire + VM secondaire (miroir exact)
- Exécution synchrone (lockstep)
- Si primaire tombe → Secondaire prend le relais instantanément (0 downtime)

**Différence avec HA** :

| Critère | HA | FT |
|---------|----|----|
| **Downtime** | 2-5 min (reboot) | 0 sec (instantané) |
| **Overhead** | Faible | Élevé (2x ressources) |
| **Usage** | Standard | Applications ultra-critiques |

**Limitations** :
- Max 8 vCPU par VM FT
- Performance impact (30-50%)
- Coût (double ressources)

**Cas d'usage** :
- Bases de données critiques
- Serveurs de paiement
- Contrôle industriel

---

### ✔️ Partie 4 : Stockage

**Question 29 : B) Espace de stockage pour VM**

Datastore :
- **Définition** : Container logique de stockage
- **Contient** : Fichiers VM (.vmdk, .vmx, .vswp, snapshots)
- **Sources** : SAN (FC, iSCSI), NAS (NFS), Local

**Types** :
- **VMFS** : Système de fichiers VMware (bloc)
- **NFS** : Network File System (fichiers)
- **vSAN** : Stockage distribué software-defined

**Question 30 : B) Système de fichiers propriétaire VMware**

VMFS (Virtual Machine File System) :

**Caractéristiques** :
- **Accès concurrent** : Plusieurs ESXi en lecture/écriture simultanée
- **Clustering** : Lock distribué
- **Taille** : Jusqu'à 64 To (VMFS6)
- **Fichiers** : Jusqu'à 62 To

**Versions** :
- VMFS5 : vSphere 5.x/6.x (max 2 To fichiers)
- VMFS6 : vSphere 6.5+ (max 62 To fichiers, GPT)

**Question 31 : B) Fibre Channel, iSCSI, NFS**

Protocoles de stockage :

| Protocol | Type | Transport | Port | Avantages | Inconvénients |
|----------|------|-----------|------|-----------|---------------|
| **Fibre Channel** | Bloc | FC SAN | - | Très rapide, fiable | Coût élevé, complexe |
| **iSCSI** | Bloc | IP (Ethernet) | 3260 | Coût réduit, flexible | Performance moyenne |
| **NFS** | Fichier | IP (Ethernet) | 2049 | Simple, pas de LUN | Latence réseau |

**Question 32 : B) NFS = fichiers (NAS), iSCSI = bloc (SAN)**

Différences :

**iSCSI** :
- **Type** : Stockage bloc (SAN sur IP)
- **Format** : LUN (volumes bruts)
- **Système fichiers** : VMFS créé par ESXi
- **Initiateur** : ESXi (client iSCSI)
- **Cible** : Serveur de stockage (target)

**NFS** :
- **Type** : Stockage fichier (NAS)
- **Format** : Partage réseau
- **Système fichiers** : NFS (géré par NAS)
- **Montage** : ESXi monte export NFS
- **Permissions** : Lecture/écriture

**Choix** :
- **iSCSI** : Performance, boot depuis SAN
- **NFS** : Simplicité, flexibilité, déduplication côté NAS

**Question 33 : B) Unité logique de stockage (volume)**

LUN (Logical Unit Number) :
- **Définition** : Volume logique présenté par SAN/NAS
- **Exemple** : SAN présente LUN0 (2 To), LUN1 (5 To)
- **Usage** : ESXi formate LUN en VMFS → Datastore

**Architecture** :
```
Baie de stockage SAN (20 To)
├─ LUN 0 : 2 To (Datastore_Prod)
├─ LUN 1 : 5 To (Datastore_Dev)
└─ LUN 2 : 10 To (Datastore_Backup)
```

**Masking** : Attribuer LUN à hôtes spécifiques (sécurité)

**Question 34 : B) iSCSI (port 3260)**

Ports stockage :

| Protocol | Port | Type |
|----------|------|------|
| **iSCSI** | 3260 TCP | SAN |
| **NFS** | 2049 TCP/UDP | NAS |
| **SMB/CIFS** | 445 TCP | NAS Windows |

---

### ✔️ Partie 5 : Snapshots, Clones, Templates

**Question 35 : B) État figé à un instant T (delta disks)**

Snapshot VMware :

**Principe** :
- Fige état VM (disque + optionnel mémoire)
- Création d'un **delta disk** (.vmdk-000001.vmdk)
- Nouvelles écritures → Delta disk
- Disque de base (parent) → Lecture seule

**Contenu** :
- **Disk** : Changements disque
- **Memory** (option) : Contenu RAM
- **VM Settings** : Configuration

**Utilisation** :
- Avant mise à jour (retour arrière si problème)
- Tests (déploiement, config)
- Développement

**Question 36 : B) Snapshot = même datastore temporaire, Backup = externe long terme**

Comparaison :

| Critère | Snapshot | Backup |
|---------|----------|--------|
| **Emplacement** | Même datastore | Externe (NAS, cloud, bande) |
| **Durée** | Court terme (heures/jours) | Long terme (semaines/mois/ans) |
| **But** | Retour arrière rapide | Protection sinistre, archivage |
| **Vitesse création** | Secondes | Minutes/heures |
| **Vitesse restauration** | Instantané (revert) | Lent |
| **Protection** | ❌ Si datastore HS → Perdu | ✅ Copie indépendante |
| **Impact** | Performance (chaîne delta) | Aucun |

**IMPORTANT** : Snapshot ≠ Backup !

**Question 37 : B) Non ! Impact performances**

Problèmes snapshots long terme :

**1. Performance** :
- Chaîne delta disks : VM → Snap1 → Snap2 → Snap3
- Lecture = parcourir chaîne (lent)
- Max 32 snapshots par VM

**2. Espace disque** :
- Delta disks grandissent (toutes les écritures)
- Peut atteindre taille disque de base
- Datastore plein → VM freeze !

**3. Consolidation** :
- Fusion snapshots (consolidate) = opération lourde
- Downtime possible

**Bonnes pratiques** :
- Max **72 heures** de rétention
- Éviter snapshots sur VM production
- Surveiller taille delta disks
- Supprimer après usage

**Question 38 : B) Clone = copie modifiable, Template = master RO**

Différences :

| Critère | Clone | Template |
|---------|-------|----------|
| **Type** | VM complète indépendante | Master en lecture seule |
| **Modifiable** | ✅ Oui | ❌ Non (convert en VM first) |
| **Démarrage** | ✅ Peut démarrer | ❌ Ne peut pas démarrer |
| **Usage** | Copie unique | Déploiements multiples |
| **Espace** | Full (copie complète) | Full |

**Clone** :
- Copie VM existante
- Indépendant (changements n'affectent pas source)

**Template** :
- VM convertie en template
- Utilisé pour déployer 10/50/100 VM identiques

**Question 39 : B) Clone partageant disque parent**

Linked Clone :

**Principe** :
- Disque de base (parent) partagé
- Chaque clone a son delta disk (différences)
- Économie d'espace massive

**Exemple** :
```
Template Windows (40 Go)
├─ Linked Clone 1 : Delta 2 Go
├─ Linked Clone 2 : Delta 3 Go
└─ Linked Clone 3 : Delta 1 Go

Total : 40 + 2 + 3 + 1 = 46 Go (vs 120 Go pour 3 clones complets)
```

**Inconvénients** :
- **Dépendance** : Si parent corrompu → Tous les clones HS
- **Performance** : Légèrement plus lent

**Usage** :
- VDI (bureaux virtuels non-persistants)
- Labs/tests

**Question 40 : B) .vmx, .vmdk, .nvram, .vswp, .log**

Fichiers VM VMware :

| Fichier | Rôle |
|---------|------|
| **.vmx** | Configuration VM (CPU, RAM, network) |
| **.vmdk** (descriptor) | Pointeur vers fichier disque |
| **-flat.vmdk** | Données disque (gros fichier) |
| **.nvram** | BIOS/EFI settings |
| **.vswp** | Swap file (taille = RAM VM) |
| **.log** | Logs (vmware.log, vmware-1.log...) |
| **.vmsd** | Métadonnées snapshots |
| **-000001.vmdk** | Delta disk (snapshot) |
| **.vmsn** | État snapshot (avec mémoire) |

**Exemple structure** :
```
Datastore1/
└─ VM_Web/
   ├─ VM_Web.vmx (10 Ko)
   ├─ VM_Web.vmdk (1 Ko)
   ├─ VM_Web-flat.vmdk (40 Go)
   ├─ VM_Web.nvram (256 Ko)
   ├─ VM_Web.vswp (8 Go)
   └─ vmware.log (500 Ko)
```

---

## 📊 BARÈME ET ÉVALUATION

**Nombre de bonnes réponses** : _____ / 40

| Score | Niveau | Commentaire |
|-------|--------|-------------|
| 36-40 | ⭐⭐⭐ Expert | Excellent ! Maîtrise VMware vSphere |
| 30-35 | ⭐⭐ Très bien | Solides connaissances, détails à revoir |
| 24-29 | ⭐ Bien | Bases OK, approfondir concepts avancés |
| 18-23 | 🟡 Moyen | Révision nécessaire (cours virtualisation) |
| 0-17 | 🔴 Insuffisant | Revoir intégralement module virtualisation |

---

## 🎯 POINTS CLÉS À RETENIR

### Architecture vSphere
```
vCenter Server (gestionnaire)
    ↓
Datacenter (conteneur logique)
    ↓
Cluster (pool ressources + HA/DRS)
    ↓
ESXi (hyperviseur)
    ↓
VM (machines virtuelles)
```

### HA vs DRS vs FT
| Critère | HA | DRS | FT |
|---------|----|----|-----|
| **But** | Disponibilité | Performance | Zéro downtime |
| **Downtime** | 2-5 min | 0 | 0 |
| **Déclencheur** | Panne hôte | Déséquilibre | Panne hôte |
| **Overhead** | Faible | Faible | Élevé (2x) |

### Snapshot vs Backup vs Clone
```
Snapshot : Retour arrière rapide (court terme, même datastore)
Backup : Protection long terme (externe, sinistre)
Clone : Copie indépendante complète
Template : Master pour déploiements multiples
Linked Clone : Clone léger (partage parent)
```

### Stockage
```
Fibre Channel : SAN haute performance (cher)
iSCSI : SAN sur IP (bon compromis)
NFS : NAS simple (partage fichiers)

LUN → Formaté en VMFS → Datastore → Stocke VM
```

### vMotion Prérequis
- ✅ Stockage partagé (SAN/NAS)
- ✅ Réseau vMotion (VMkernel port)
- ✅ CPU compatibles (EVC)
- ✅ vCenter
- ✅ Licences vSphere

---

## 📚 COMMANDES ESSENTIELLES

### ESXi CLI (via SSH)
```bash
# Lister VM
vim-cmd vmsvc/getallvms

# État VM
vim-cmd vmsvc/power.getstate <vmid>

# Power On/Off
vim-cmd vmsvc/power.on <vmid>
vim-cmd vmsvc/power.off <vmid>

# Info réseau
esxcli network nic list
esxcli network ip interface list

# Info stockage
esxcli storage vmfs extent list
esxcli storage core device list
```

### PowerCLI (PowerShell VMware)
```powershell
# Connexion vCenter
Connect-VIServer -Server vcenter.local

# Lister VM
Get-VM

# Créer snapshot
New-Snapshot -VM "VM01" -Name "Avant_MAJ"

# vMotion
Move-VM -VM "VM01" -Destination "ESXi-02"

# Cloner VM
New-VM -Name "VM_Clone" -VM "VM_Source" -VMHost "ESXi-01"
```

---

**🎓 La virtualisation est au cœur du métier TSSR ! Maîtrise ces concepts pour l'examen !**