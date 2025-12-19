# FICHE REVISION CCP5 - VMWARE vSPHERE / VIRTUALISATION

**Certification TSSR** - CCP5 : Maintenir infrastructure virtualisée

---

## 1. ARCHITECTURE vSPHERE

### Composants essentiels

```
vCenter Server (gestionnaire centralisé)
    └── Datacenter
          ├── Cluster (HA/DRS activés)
          │     ├── ESXi Host 1 (hyperviseur)
          │     │     ├── VM1, VM2, VM3
          │     │     └── vSwitch (réseau virtuel)
          │     └── ESXi Host 2
          │           └── VM4, VM5, VM6
          └── Datastores (stockage partagé)
                ├── VMFS (VMware)
                └── NFS (Linux)
```

**ESXi** : Hyperviseur bare-metal (type 1), installé directement sur serveur  
**vCenter** : Gestion centralisée, requis pour HA/DRS/vMotion  
**Datastore** : Stockage VMs (disques VMDK, ISO, templates)

---

## 2. SNAPSHOTS

### Définition
**Snapshot** = Photo instantanée état VM (disque + mémoire + config)

### Utilisation
```
AVANT mise à jour risquée → Créer snapshot
SI problème → Restaurer snapshot
SI OK → Supprimer snapshot (consolider disques)
```

### Commandes ESXi (vim-cmd)
```bash
# Lister VMs
vim-cmd vmsvc/getallvms

# Créer snapshot
vim-cmd vmsvc/snapshot.create <vmid> "Avant_MaJ_Windows" "Snapshot avant KB5012345" 0 0

# Lister snapshots
vim-cmd vmsvc/snapshot.get <vmid>

# Restaurer snapshot
vim-cmd vmsvc/snapshot.revert <vmid> <snapshot_id> 0

# Supprimer snapshot
vim-cmd vmsvc/snapshot.remove <vmid> <snapshot_id>
```

### ATTENTION
- ❌ **Pas de sauvegarde** : Snapshot ≠ backup (même datastore)
- ⚠️ **Performance** : Snapshot > 72h ralentit VM (fichiers delta croissants)
- ⚠️ **Espace disque** : Snapshot consomme espace (delta disks)
- ✅ **Bonne pratique** : Supprimer après validation (consolide automatiquement)

---

## 3. vMOTION

### Définition
**vMotion** = Migration à chaud VM entre hôtes ESXi **SANS interruption**

### Prérequis
1. ✅ vCenter Server configuré
2. ✅ Réseau vMotion dédié (VMkernel avec vMotion enabled)
3. ✅ Stockage partagé (même datastore accessible par les 2 ESXi)
4. ✅ Processeurs compatibles (même fabricant Intel/AMD, EVC mode si nécessaire)
5. ✅ Licences vSphere Standard minimum

### Configuration réseau vMotion
```bash
# ESXi Shell : Créer VMkernel pour vMotion
esxcli network ip interface add -i vmk1 -p "vMotion"
esxcli network ip interface ipv4 set -i vmk1 -I 10.0.10.10 -N 255.255.255.0 -t static
vim-cmd hostsvc/vmotion/vnic_set vmk1
```

### Types vMotion
- **vMotion standard** : Migration VM (stockage reste)
- **Storage vMotion** : Migration disques VM (VM reste sur même hôte)
- **Cross vSwitch vMotion** : Change vSwitch pendant migration
- **Long Distance vMotion** : Entre sites (latence <150ms, vSphere 6+)

---

## 4. vSPHERE HA (High Availability)

### Fonctionnement
Si **ESXi hôte crash** → VMs redémarrent automatiquement sur autres hôtes cluster

### Configuration
```
vCenter → Cluster → Configure → vSphere HA
  ☑ Turn On vSphere HA
  Admission Control : 
    - Host failures tolerated: 1 (réserve ressources pour 1 panne)
  VM Monitoring:
    - VM Monitoring: Enabled (redémarre VM si guest OS freeze)
```

### Prérequis
- Minimum 2 hôtes ESXi dans cluster
- Stockage partagé (datastores accessibles par tous hôtes)
- Réseau management fiable (heartbeat entre hôtes)

### Différence HA vs FT
| Critère | HA | FT (Fault Tolerance) |
|---------|----|----|
| **Tolérance** | Redémarrage VM (downtime 1-2min) | **0 downtime** (VM miroir active) |
| **Ressources** | Économique | Double ressources (2 VMs actives) |
| **Cas usage** | Applications standard | Applications critiques (base prod) |

---

## 5. TYPES DE DISQUES VIRTUELS

### Thick Provision (épais)

**Lazy Zeroed Thick** :
- Espace réservé immédiatement
- Zéros écrits à la demande (plus rapide création)
- Performance : ⭐⭐⭐

**Eager Zeroed Thick** :
- Espace réservé + rempli de zéros immédiatement
- Création longue mais **requis pour FT**
- Performance : ⭐⭐⭐⭐⭐ (meilleure)

### Thin Provision (dynamique)
- Espace alloué à la demande (VM 100GB peut occuper 30GB)
- **Risque** : Sur-allocation → datastore plein
- Performance : ⭐⭐ (fragmentation)
- **Cas usage** : Environnements tests, VDI

### Commande conversion
```bash
vmkfstools -i source.vmdk -d thin destination.vmdk
vmkfstools -i source.vmdk -d eagerzeroedthick destination.vmdk
```

---

## 6. DATASTORES

### VMFS (VMware File System)
- Système fichiers VMware propriétaire
- **Support** : Bloc (SAN iSCSI, Fibre Channel)
- **Avantages** : Locking fichiers, snapshots matériels
- **Version actuelle** : VMFS6 (blocs 512e/4Kn)

### NFS (Network File System)
- Système fichiers réseau standard
- **Support** : NAS (Synology, QNAP, serveurs Linux)
- **Avantages** : Simplicité administration, économique
- **Versions** : NFSv3 (default), NFSv4.1 (authentification Kerberos)

### Comparaison
| Critère | VMFS | NFS |
|---------|------|-----|
| **Type** | Bloc | Fichier |
| **Performance** | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐ |
| **Coût** | Élevé (SAN) | Économique (NAS) |
| **Snapshots** | Matériels | Logiciels |

### Commandes
```bash
# Lister datastores
esxcli storage filesystem list

# Monter datastore NFS
esxcli storage nfs add -H 192.168.1.100 -s /volume1/VMstore -v NFS_Datastore01

# Augmenter datastore VMFS
vmkfstools -X 500G /vmfs/volumes/datastore1
```

---

## 7. DRS (Distributed Resource Scheduler)

### Fonctionnement
**Load balancing automatique** : Déplace VMs entre hôtes pour équilibrer CPU/RAM

### Modes
- **Manual** : DRS recommande, admin exécute
- **Partially Automated** : DRS place VMs au démarrage, recommande migrations
- **Fully Automated** : DRS migre automatiquement (vMotion)

### Règles DRS
```
Affinity Rules (affinité) :
  - Keep VMs together : VM1 + VM2 sur même hôte (performance réseau)
  
Anti-Affinity Rules (anti-affinité) :
  - Separate VMs : VM_DC1 et VM_DC2 sur hôtes différents (HA)
```

---

## 8. RÉSEAU vSPHERE

### vSwitch Standard
- Configuré localement par hôte ESXi
- Gratuit (inclus ESXi)
- Administration manuelle multi-hôtes

### vSwitch Distributed (dvSwitch)
- Configuration centralisée via vCenter
- Licence Enterprise Plus requise
- **Avantages** : Policies réseau uniformes, Network I/O Control

### Port Groups
```
Port Group "Production" → VLAN 10
Port Group "DMZ"        → VLAN 20
Port Group "Admin"      → VLAN 99
```

### VMkernel Ports (vmkX)
- **vmk0** : Management (accès ESXi)
- **vmk1** : vMotion
- **vmk2** : vSAN
- **vmk3** : Fault Tolerance

---

## 9. TEMPLATES & CLONES

### Template
VM modèle figée (non démarrable), base déploiements rapides

**Workflow** :
```
1. Installer OS sur VM + personnaliser
2. Sysprep Windows / cloud-init Linux
3. Convertir VM en template
4. Déployer VMs depuis template (nom, IP, etc.)
```

### Types clones
- **Full Clone** : Copie complète indépendante
- **Linked Clone** : Disque parent partagé (delta disks)
  - Avantage : Gain espace (10 VMs = 1 parent + 10 petits deltas)
  - Inconvénient : Dépendance (parent corrompu = tous clones KO)

---

## 10. COMMANDES ESXCLI ESSENTIELLES

### Réseau
```bash
esxcli network nic list                    # Cartes réseau physiques
esxcli network vswitch standard list       # vSwitches
esxcli network ip interface ipv4 get       # Config IP VMkernel
```

### Stockage
```bash
esxcli storage core adapter list           # Adaptateurs (vmhbaX)
esxcli storage vmfs extent list            # Datastores VMFS
esxcli storage nfs list                    # Datastores NFS
```

### VMs
```bash
vim-cmd vmsvc/getallvms                    # Liste VMs
vim-cmd vmsvc/power.on <vmid>              # Démarrer VM
vim-cmd vmsvc/power.off <vmid>             # Éteindre VM
```

### Maintenance
```bash
esxcli system maintenanceMode set --enable true   # Mode maintenance
esxcli system shutdown reboot -r "Patch mensuel"  # Redémarrage
```

---

## 11. BONNES PRATIQUES

### Snapshots
- ❌ Jamais snapshot sur VM base de données en production (corruption)
- ✅ Supprimer dans 24-72h maximum
- ✅ Planifier fenêtre maintenance pour consolidation (I/O intensif)

### Ressources VMs
- ✅ Réserver RAM critique (SQL Server, Exchange)
- ✅ Limiter CPU shares pour VMs tests (éviter monopolisation)
- ❌ Ne pas allouer 100% ressources physiques (overhead ESXi)

### Stockage
- ✅ Thin provision pour VDI/tests, Thick pour production
- ✅ Séparer datastores : OS / Données / Logs
- ✅ Laisser 20% espace libre datastore (performances)

### Réseau
- ✅ Réseau vMotion dédié (10 Gbps recommandé)
- ✅ Teaming NICs (failover automatique)
- ✅ Jumbo Frames (MTU 9000) pour vMotion/NFS

---

## 12. DÉPANNAGE RAPIDE

### VM ne démarre pas
```bash
# Vérifier logs
tail -f /vmfs/volumes/datastore1/VM01/vmware.log

# Causes fréquentes :
- Datastore déconnecté
- Fichier .vmx verrouillé (autre hôte)
- Snapshot corrompu
```

### vMotion échoue
```
Erreur commune : "The VMotion migration failed..."
Checks :
1. Réseau vMotion actif : esxcli network ip interface list
2. VMkernel vMotion enabled
3. Stockage partagé accessible par les 2 hôtes
4. Compatibilité CPU (EVC mode si nécessaire)
```

### Datastore plein
```bash
# Top 10 VMs les plus volumineuses
du -h /vmfs/volumes/datastore1/* | sort -rh | head -10

# Consolider snapshots
vim-cmd vmsvc/snapshot.removeall <vmid>

# Supprimer vieux logs
find /vmfs/volumes/datastore1 -name "*.log" -mtime +30 -delete
```

---

## COMMANDES À RETENIR

```bash
# Snapshots
vim-cmd vmsvc/snapshot.create <vmid> "nom" "description" 0 0
vim-cmd vmsvc/snapshot.revert <vmid> <snapshot_id> 0
vim-cmd vmsvc/snapshot.removeall <vmid>

# Datastores
esxcli storage nfs add -H <IP_NAS> -s <chemin> -v <nom>
esxcli storage filesystem list

# Réseau vMotion
esxcli network ip interface add -i vmk1 -p "vMotion"
vim-cmd hostsvc/vmotion/vnic_set vmk1

# Maintenance
esxcli system maintenanceMode set --enable true
vim-cmd vmsvc/getallvms
```

---

**Date révision** : 12 novembre 2025  
**Examen TSSR** : 17 novembre 2025

**Cette fiche couvre 75% des questions CCP5 Virtualisation !**