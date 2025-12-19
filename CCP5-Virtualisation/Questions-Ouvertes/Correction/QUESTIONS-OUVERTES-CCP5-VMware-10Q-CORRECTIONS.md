# CORRECTIONS - CCP5 VMWARE vSPHERE

**Certification TSSR** - CCP5 : Maintenir infrastructure virtualisée  
**10 questions - Corrections détaillées**

---

## ✔️ Question 1 : Snapshots - Utilisation et risques (8 points)

**Énoncé** :  
Expliquez ce qu'est un snapshot VMware, donnez 2 cas d'usage et 3 risques liés à leur utilisation prolongée.

**Réponse modèle** :

**Définition** (2 pts) :
Photo instantanée état VM (disque + mémoire + config) à instant T. Crée fichiers delta (.vmdk) pour enregistrer modifications après snapshot.

**Cas d'usage** (2 pts) :
1. Avant mise à jour Windows/applicative risquée
2. Avant modification configuration système critique

**Risques prolongés** (4 pts) :
1. **Performance dégradée** : Chaque écriture passe par chaîne deltas (lent)
2. **Espace disque** : Fichiers delta croissent → datastore plein
3. **Corruption** : Chaîne snapshots longue → risque corruption consolidation

**Commandes** :
```bash
# Créer
vim-cmd vmsvc/snapshot.create 42 "Avant_MaJ" "KB5012345" 0 0

# Supprimer tous
vim-cmd vmsvc/snapshot.removeall 42
```

**Bonne pratique** : Supprimer sous 72h maximum

**Points** : 8/8

---

## ✔️ Question 2 : vMotion - Prérequis technique (10 points)

**Énoncé** :  
Listez les 5 prérequis pour effectuer un vMotion entre 2 hôtes ESXi. Un vMotion échoue avec erreur "Incompatible CPU", quelle solution ?

**Réponse modèle** :

**5 prérequis** (5 pts) :
1. **vCenter Server** configuré et opérationnel
2. **Réseau vMotion** dédié (VMkernel avec vMotion enabled sur chaque ESXi)
3. **Stockage partagé** accessible par les 2 hôtes (même datastore)
4. **Processeurs compatibles** (même fabricant Intel/AMD, jeux instructions similaires)
5. **Licences** vSphere Standard minimum

**Solution erreur CPU** (5 pts) :

**EVC Mode (Enhanced vMotion Compatibility)** :
- Active au niveau cluster
- Masque instructions CPU récentes pour compatibilité
- Exemple : Cluster avec ESXi gen Intel Xeon v3 + v5 → EVC "Intel Xeon v3"

**Configuration** :
```
vCenter → Cluster → Configure → VMware EVC
  Enable EVC : Intel "Haswell" Generation
```

**Alternative** : Mettre VM hors tension, migrer (pas vMotion à chaud), redémarrer

**Points** : 10/10

---

## ✔️ Question 3 : vSphere HA vs Fault Tolerance (8 points)

**Énoncé** :  
Quelle différence entre vSphere HA et Fault Tolerance ? Dans quel cas privilégier FT ?

**Réponse modèle** :

**Tableau comparatif** (6 pts) :

| Critère | vSphere HA | Fault Tolerance (FT) |
|---------|------------|---------------------|
| **Fonctionnement** | Redémarre VM sur autre hôte si crash | VM miroir active en temps réel |
| **Downtime** | 1-2 minutes | **0 seconde** (bascule transparente) |
| **Ressources** | Standard | **Double** (2 VMs actives) |
| **Prérequis** | Stockage partagé | Stockage partagé + réseau FT 10 Gbps |
| **Limites** | Aucune | Max 4 vCPU, 128 GB RAM (vSphere 7) |

**Cas usage FT** (2 pts) :
- ✅ **Applications critiques** : Base de données production 24/7
- ✅ **Aucune tolérance downtime** : Automates industriels, systèmes bancaires
- ❌ **Pas pour** : VMs test, serveurs web (HA suffit)

**Prérequis FT** :
- Disques **Eager Zeroed Thick** (pas Thin)
- Réseau VMkernel FT logging dédié

**Points** : 8/8

---

## ✔️ Question 4 : Types disques - Thick vs Thin (7 points)

**Énoncé** :  
Expliquez Thick Provision Lazy/Eager Zeroed et Thin Provision. Quel type obligatoire pour Fault Tolerance ?

**Réponse modèle** :

**Thick Provision Lazy Zeroed** (2 pts) :
- Espace **réservé immédiatement** sur datastore
- Zéros écrits à la demande (rapide création)
- Performance : Bonne

**Thick Provision Eager Zeroed** (2 pts) :
- Espace réservé + **rempli de zéros immédiatement**
- Création longue (plusieurs minutes pour 100 GB)
- Performance : **Meilleure** (pas d'overhead premier write)

**Thin Provision** (2 pts) :
- Espace alloué **à la demande** (VM 100 GB peut occuper 30 GB physiques)
- Risque : Sur-allocation → datastore plein
- Cas usage : Tests, VDI

**FT requiert Eager Zeroed Thick** (1 pt) :
Garantit blocs disque initialisés pour synchronisation VM primaire/secondaire

**Conversion** :
```bash
vmkfstools -i source.vmdk -d eagerzeroedthick destination.vmdk
```

**Points** : 7/7

---

## ✔️ Question 5 : Datastores VMFS vs NFS (8 points)

**Énoncé** :  
Comparez VMFS et NFS pour datastores vSphere. Donnez 2 avantages de chaque.

**Réponse modèle** :

**VMFS (VMware File System)** (4 pts) :

**Définition** : Système fichiers bloc propriétaire VMware

**Avantages** :
- ✅ **Performance** : Accès bloc direct (SAN iSCSI, Fibre Channel)
- ✅ **Snapshots matériels** : Intégration baies SAN (VAAI)

**Inconvénients** :
- ❌ Coût élevé (infrastructure SAN)
- ❌ Complexité configuration

**NFS (Network File System)** (4 pts) :

**Définition** : Système fichiers réseau standard (NAS)

**Avantages** :
- ✅ **Simplicité** : Montage en 1 commande (IP + chemin)
- ✅ **Économique** : NAS Synology/QNAP abordables

**Inconvénients** :
- ❌ Performance légèrement inférieure (réseau)
- ❌ Dépendance réseau (latence critique)

**Commandes** :
```bash
# Monter NFS
esxcli storage nfs add -H 192.168.1.100 -s /volume1/VMs -v NFS_Prod

# Lister datastores
esxcli storage filesystem list
```

**Recommandation** : NFS pour PME, VMFS pour entreprises (budget + performance)

**Points** : 8/8

---

## ✔️ Question 6 : DRS - Load Balancing (6 points)

**Énoncé** :  
Qu'est-ce que DRS et quels sont les 3 modes d'automatisation ? Donnez un exemple règle d'affinité.

**Réponse modèle** :

**DRS (Distributed Resource Scheduler)** (2 pts) :
Load balancing automatique VMs entre hôtes cluster selon charge CPU/RAM

**3 modes** (3 pts) :
1. **Manual** : DRS recommande, admin valide manuellement
2. **Partially Automated** : DRS place VMs au boot, recommande migrations
3. **Fully Automated** : DRS migre automatiquement via vMotion

**Règle affinité** (1 pt) :
```
Keep VMs together : 
  VM_Web + VM_App sur même hôte (minimise latence réseau)
```

**Règle anti-affinité** (exemple bonus) :
```
Separate VMs :
  VM_DC01 et VM_DC02 sur hôtes différents (haute disponibilité)
```

**Points** : 6/6

---

## ✔️ Question 7 : Réseau vSphere - VMkernel (7 points)

**Énoncé** :  
Qu'est-ce qu'un port VMkernel ? Listez 4 usages différents avec exemple vmkX.

**Réponse modèle** :

**Définition** (2 pts) :
Interface réseau virtuelle ESXi pour services infrastructure (pas VMs)

**4 usages** (5 pts) :

| VMkernel | Usage | Config |
|----------|-------|--------|
| **vmk0** | Management | Accès vCenter, SSH, HTTPS ESXi |
| **vmk1** | vMotion | Migration VMs à chaud |
| **vmk2** | vSAN | Stockage distribué VMware |
| **vmk3** | Fault Tolerance | Logging FT entre VMs miroirs |

**Création VMkernel vMotion** :
```bash
esxcli network ip interface add -i vmk1 -p "vMotion"
esxcli network ip interface ipv4 set -i vmk1 -I 10.0.10.50 -N 255.255.255.0 -t static
vim-cmd hostsvc/vmotion/vnic_set vmk1
```

**Bonne pratique** : Réseau vMotion dédié 10 Gbps minimum

**Points** : 7/7

---

## ✔️ Question 8 : Templates et Clones (8 points)

**Énoncé** :  
Quelle différence entre un template et un clone ? Expliquez Full Clone vs Linked Clone.

**Réponse modèle** :

**Template** (2 pts) :
VM modèle **figée** (non démarrable), base déploiements multiples

**Clone** (2 pts) :
Copie VM existante (démarrable immédiatement)

**Full Clone** (2 pts) :
- Copie complète indépendante
- Occupe espace complet (100 GB source = 100 GB clone)
- Performances : Identiques à source
- Cas usage : Production

**Linked Clone** (2 pts) :
- Disque **parent partagé** + fichiers delta (différences uniquement)
- Économie espace (10 VMs = 1 parent 50 GB + 10 deltas 5 GB)
- **Risque** : Parent corrompu → tous clones KO
- Cas usage : VDI, environnements tests

**Workflow template** :
```
1. Installer OS sur VM + personnaliser
2. Sysprep Windows (généralise SID)
3. Convert to Template
4. Deploy VM from Template (personnalise nom, IP)
```

**Points** : 8/8

---

## ✔️ Question 9 : Mode Maintenance ESXi (6 points)

**Énoncé** :  
Pourquoi mettre un hôte ESXi en mode maintenance ? Que se passe-t-il pour les VMs actives ?

**Réponse modèle** :

**Utilité mode maintenance** (3 pts) :
- Mises à jour ESXi (patches VMware)
- Remplacement matériel (RAM, disques)
- Maintenance préventive
- **Garantit** : Pas de nouvelles VMs démarrées pendant intervention

**Impact VMs** (3 pts) :

**Avec DRS Fully Automated** :
- ✅ VMs migrées automatiquement vers autres hôtes (vMotion)
- ✅ Aucune interruption

**Sans DRS ou Manual** :
- ⚠️ **Choix admin** :
  - Migrer manuellement VMs (vMotion)
  - Suspendre VMs
  - **Éteindre VMs** (downtime)

**Commandes** :
```bash
# Activer
esxcli system maintenanceMode set --enable true

# Désactiver
esxcli system maintenanceMode set --enable false

# Vérifier état
esxcli system maintenanceMode get
```

**Bonne pratique** : Planifier fenêtre maintenance hors heures ouvrées

**Points** : 6/6

---

## ✔️ Question 10 : Dépannage - Datastore plein (10 points)

**Énoncé** :  
Un datastore VMFS 2 TB est plein à 98%. Listez 4 actions pour libérer espace rapidement et la commande pour identifier les fichiers volumineux.

**Réponse modèle** :

**4 actions immédiates** (8 pts) :

**1. Supprimer anciens snapshots** (2 pts) :
```bash
# Lister VMs avec snapshots
vim-cmd vmsvc/getallvms | grep snapshot

# Consolider snapshots VM ID 42
vim-cmd vmsvc/snapshot.removeall 42
```
Gain : 10-50 GB selon durée snapshots

**2. Supprimer fichiers logs anciens** (2 pts) :
```bash
find /vmfs/volumes/datastore1 -name "*.log" -mtime +30 -delete
find /vmfs/volumes/datastore1 -name "vmware-*.log" -size +100M -delete
```
Gain : 5-20 GB

**3. Storage vMotion VMs non critiques** (2 pts) :
Migrer VMs tests/dev vers autre datastore

**4. Thin Out disques VMs** (2 pts) :
Convertir Thick → Thin si VM peu active
```bash
vmkfstools -i source.vmdk -d thin destination.vmdk
```
Gain : 20-40% espace selon utilisation réelle

**Commande identification gros fichiers** (2 pts) :
```bash
# Top 20 fichiers volumineux
du -h /vmfs/volumes/datastore1/* | sort -rh | head -20

# Fichiers >50 GB
find /vmfs/volumes/datastore1 -type f -size +50G -exec du -h {} \;
```

**Prévention** :
- ✅ Alarmes vCenter : Alerte si datastore >80%
- ✅ Politique snapshots : Suppression auto >72h (vRealize Automation)
- ✅ Thin Provision pour tests/VDI

**Points** : 10/10

---

## 📊 BARÈME TOTAL : 78 POINTS

**Répartition** :
- Snapshots (Q1, Q10) : 18 pts
- vMotion/Migration (Q2, Q9) : 16 pts
- HA/FT (Q3) : 8 pts
- Stockage (Q4, Q5, Q10) : 25 pts
- DRS/Réseau (Q6, Q7) : 13 pts
- Clones (Q8) : 8 pts

**Note sur 20** : Total points / 3.9

---

**ASTUCE EXAMEN** :  
Questions VMware souvent pratiques : pensez **commandes ESXi Shell** (esxcli, vim-cmd) et **chemins vCenter** précis. Détaillez prérequis techniques (réseau, stockage) pour maximiser points !
