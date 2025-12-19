Q1 : **Différence sauvegarde différentielle et incrémentale**

**✅ Réponse : B) Différentielle = depuis dernière complète / Incrémentale = depuis dernière sauvegarde**

**Tableau** :

| Type | Référence | Vitesse | Espace |
|------|-----------|---------|--------|
| **Complète** | - | ⚠️ Lente | ⚠️ Important |
| **Différentielle** | Dernière complète | ⚡ Moyenne | ⚡ Croissant |
| **Incrémentale** | Dernière sauvegarde | ✅ Rapide | ✅ Optimal |

**Exemple** :
```
Diff : Dim [FULL] ← Lun [10GB] ← Mar [20GB depuis Dim]
Incr : Dim [FULL] ← Lun [10GB] ← Mar [5GB depuis Lun]
```

Q2 : **Restauration sauvegarde différentielle : jeux nécessaires**

**✅ Réponse : B) 2 jeux (Complète + dernière Différentielle)**

**Process** :
```
ÉTAPE 1 : Restaurer COMPLÈTE (Samedi)
ÉTAPE 2 : Restaurer DIFF (Jeudi)
```

**Comparaison** :

| Stratégie | Jeux nécessaires |
|-----------|--------------------|
| **Complète** | 1 (Samedi) |
| **Différentielle** | 2 (Sam + Jeu) |
| **Incrémentale** | N (Sam + Dim+Lun+Mar+Mer+Jeu) |

**Avantage** : Simplicité vs incrémentale.
| **Complète** | 1 seul | Samedi (500 GB) | 4h |
| **Différentielle** | 2 jeux | Samedi (500 GB) + Jeudi (120 GB) | 5h |
| **Incrémentale** | N jeux | Samedi (500 GB) + Dim+Lun+Mar+Mer+Jeu (30+25+28+32+35 GB) | 6h30 |

**⚙️ Exemple de calendrier hebdomadaire** :

```
Sam 00:00 : [COMPLÈTE] 500 GB
Dim 23:00 : [DIFF] 30 GB (depuis Samedi)
Lun 23:00 : [DIFF] 80 GB (depuis Samedi, cumul Dim+Lun)
Mar 23:00 : [DIFF] 95 GB (depuis Samedi, cumul Dim+Lun+Mar)
Mer 23:00 : [DIFF] 110 GB (depuis Samedi)
Jeu 23:00 : [DIFF] 120 GB (depuis Samedi)

PANNE VENDREDI 10:00
→ Restauration : Samedi 500 GB + Jeudi 120 GB = 620 GB à traiter
```

**💡 Calcul du temps de restauration** :
- Débit lecture bande : 150 MB/s
- Temps = (500 GB + 120 GB) / 150 MB/s = 620000 MB / 150 MB/s ≈ 4133 secondes = **69 minutes**
- + Temps d'indexation et vérification : ~30 minutes
- **Temps total réel : ~1h40**

**🎯 Avantage clé** : Simplicité de restauration (2 bandes maximum) vs incrémentale (potentiellement 7+ bandes pour une semaine complète).

Q3 : **Règle 3-2-1 sauvegardes**

**✅ Réponse : A) 3 copies, 2 supports différents, 1 externe**

**Décomposition** :
```
3️⃣ = 3 COPIES (Production + Backup local + Backup distant)
2️⃣ = 2 SUPPORTS (Disque + Bande OU Cloud)
1️⃣ = 1 HORS-SITE (Cloud, datacenter distant)
```

**Exemple PME** :
- Production : Serveur local SSD
- Backup 1 : NAS 10TB (local)
- Backup 2 : AWS S3 Glacier (distant)

**Protection** : Panne disque, incendie, ransomware.

**🔹 Exemples concrets d'implémentation** :

| Scénario | Production | Backup 1 | Backup 2 (externe) | Coût mensuel |
|----------|-----------|----------|-------------------|--------------|
| **PME** | Serveur local SSD | NAS 10TB (RAID 5) | AWS S3 Glacier 2TB | ~150€ |
| **Grande entreprise** | Baie SAN 100TB | Disques de déduplication | Bandes LTO-9 + coffre | ~5000€ |
| **Cloud-first** | VM Azure (Premium SSD) | Azure Disk Snapshot | AWS S3 cross-region | ~800€ |

**⚙️ Exemple détaillé PME** :

```
🖥️ PRODUCTION (1 copie)
└─ Serveur Dell PowerEdge : 4x 2TB SSD RAID 10 = 4TB utilisables

💾 BACKUP LOCAL (2ème copie, support différent #1)
└─ NAS Synology DS1821+ : 8x 4TB HDD RAID 6 = 24TB utilisables
   └─ Protocole : iSCSI + Veeam Backup Repository
   └─ Rétention : 7 jours complètes + 4 semaines hebdo

☁️ BACKUP EXTERNE (3ème copie, support différent #2)
└─ AWS S3 Glacier Deep Archive : 2TB stockés
   └─ Veeam Cloud Tier (automatique)
   └─ Rétention : 12 mois archives annuelles
   └─ Localisation : Ireland (eu-west-1) ≠ Paris datacenter
```

**🛡️ Protection contre les risques** :

| Risque | Protection 3-2-1 |
|--------|------------------|
| **Panne disque production** | → Restauration depuis NAS local (backup 1) |
| **Incendie datacenter** | → Restauration depuis Cloud (backup 2 externe) |
| **Ransomware** | → Backups immutables Cloud (hors portée attaquant) |
| **Erreur humaine (suppression)** | → Versions multiples sur NAS + Cloud |
| **Défaillance NAS local** | → Production encore OK + Cloud disponible |

**💡 Évolution moderne : règle 3-2-1-1-0** :
- **3-2-1** : Règle de base
- **+1** : 1 copie **air-gapped** (déconnectée du réseau, protection ransomware)
- **+0** : 0 erreur lors des tests de restauration (validation régulière)

**🎯 Point critique** : La copie externe DOIT être géographiquement éloignée (>100 km recommandé) pour survivre aux catastrophes régionales (inondations, incendies de forêt, etc.).

Q4 : **RPO et RTO**

**✅ Réponse : B) Recovery Point Objective et Recovery Time Objective**

**Définitions** :
```
🎯 RPO = Perte données MAX acceptable (4h, 24h)
       = Fréquence backups

⏱️ RTO = Temps indisponibilité MAX acceptable (1h, 8h)
       = Stratégie restauration
```

**Exemples** :

| Secteur | RPO | RTO |
|---------|-----|-----|
| **Banque** | 0 sec | 5 min |
| **E-commerce** | 15 min | 1h |
| **PME** | 24h | 8h |

**Impact** : Plus RPO/RTO bas = Plus coûteux.

**🔹 Exemples par secteur d'activité** :

| Secteur | Système | RPO | RTO | Justification | Coût/an |
|---------|---------|-----|-----|---------------|---------|
| **Banque** | Transactions bancaires | **0 seconde** | **5 min** | Perte financière directe | 500k€+ |
| **E-commerce** | Site de vente | **15 min** | **1 heure** | Perte CA + réputation | 100k€ |
| **Hôpital** | Dossiers patients | **1 heure** | **4 heures** | Vie patients en jeu | 200k€ |
| **PME** | ERP/CRM | **24 heures** | **8 heures** | Activité ralentie acceptable | 20k€ |
| **Administration** | Fichiers bureautiques | **1 jour** | **24 heures** | Impact limité | 5k€ |

**🔹 Calculs et exemples concrets** :

**Exemple 1 : Site e-commerce (CA 1M€/an)**
```
💰 Calcul RTO :
   - CA journalier = 1 000 000 € / 365 = 2740 €/jour
   - CA horaire = 2740 € / 24 = 114 €/heure
   - Indisponibilité 4h = 456 € de perte + impact réputation
   → RTO cible : 1 heure (perte acceptable : 114€)

📊 Calcul RPO :
   - Commandes/heure en pic : 50 commandes
   - Panier moyen : 80 €
   - Perte si RPO = 1h : 50 × 80 = 4000 € + clients mécontents
   → RPO cible : 15 minutes (perte : ~1000€)
```

**Exemple 2 : Base de données SQL Server**
```
⚙️ Configuration pour RPO 15 min / RTO 1h :
   ├─ Log Shipping toutes les 15 minutes → RPO
   ├─ AlwaysOn Availability Groups (synchrone) → RTO
   ├─ Serveur secondaire en warm standby
   └─ Procédure de bascule automatique : 10 minutes

🔧 Backups complémentaires :
   ├─ Snapshots toutes les 15 min → VSS + CBT
   ├─ Full backup nightly → rétention longue
   └─ Log backup toutes les 15 min → point-in-time recovery
```

**🔹 Relation entre RPO/RTO et SLA (Service Level Agreement)** :

```
📋 Exemple SLA contractuel :
   "Le Prestataire s'engage à garantir :
    - Disponibilité mensuelle : 99,9% (RTO implicite)
    - RPO maximum : 4 heures
    - RTO maximum : 8 heures
    - Pénalités : 5% du contrat mensuel par heure dépassée"

💡 Calcul disponibilité 99,9% :
   - Downtime mensuel autorisé = 30 jours × 24h × 0,1% = 43 minutes
   - Si panne 2 heures → violation SLA → pénalités
```

**⚙️ Stratégies techniques selon RPO/RTO** :

| RPO | RTO | Stratégie technique | Coût |
|-----|-----|-------------------|------|
| **0 sec** | **< 5 min** | Réplication synchrone + cluster actif-actif | 💰💰💰💰💰 |
| **< 15 min** | **< 1h** | Réplication asynchrone + warm standby | 💰💰💰💰 |
| **< 4h** | **< 8h** | Snapshots + backup déduplication | 💰💰💰 |
| **< 24h** | **< 24h** | Backup quotidien + cloud | 💰💰 |
| **< 1 sem** | **< 3 jours** | Backup hebdomadaire sur bande | 💰 |

**💡 Formule de coût** :
```
Coût solution = f(RPO, RTO)
Plus RPO/RTO sont bas → Plus la solution est coûteuse

RPO ÷ 2 ≈ Coût × 2
RTO ÷ 2 ≈ Coût × 3
```

**🎯 Point clé** : RPO et RTO doivent être définis par métier/application (pas un chiffre unique pour toute l'entreprise). Une messagerie peut avoir RPO 1h, mais le système de paie peut avoir RPO 24h.

Q5 : **CBT (Changed Block Tracking)**

**✅ Réponse : B) Détection blocs modifiés depuis dernière sauvegarde**

**Fonctionnement** :
```
Jour 1 : Backup 100 GB → Bitmap [0000]
Jour 2 : Modif 500 MB → Bitmap [0100]
Backup incr : Lit 500 MB seulement
```

**Comparaison** :

| Technologie | Depuis |
|-------------|--------|
| VMware CBT | vSphere 4.0 |
| Hyper-V RCT | WS 2016 |

**Impact** : Scan 1 TB sans CBT = 2h46, avec CBT = 1min  
**Gain** : 99%

Q6 : **Snapshot vs Backup**

**✅ Réponse : A) Snapshot = état instant même disque / Backup = copie indépendante**

**Comparaison** :

| Critère | Snapshot | Backup |
|---------|----------|--------|
| **Nature** | Pointeur | Copie complète |
| **Stockage** | ⚠️ Même volume | ✅ Séparé |
| **Vitesse** | ✅ Instantanée | ⚠️ Minutes/heures |
| **Panne disque** | ❌ NON | ✅ OUI |
| **Durée** | Court terme | Long terme |

**Usage** :  
- Snapshot : Tests, patches (<24h)
- Backup : PRA, conformité, archives

**🔹 Limitations critiques des snapshots** :

```
⚠️ DANGER 1 : Même stockage physique
┌─────────────────────────────────┐
│  Datastore ESXi : datastore01   │
│  ├─ VM-PROD.vmdk (100 GB)       │ ← Disque production
│  ├─ VM-PROD-000001-delta.vmdk   │ ← Snapshot jour 1 (5 GB)
│  └─ VM-PROD-000002-delta.vmdk   │ ← Snapshot jour 2 (8 GB)
└─────────────────────────────────┘
                ⬇️
   💥 PANNE BAIE DE STOCKAGE 💥
                ⬇️
    ❌ Production ET snapshots perdus !

⚠️ DANGER 2 : Impact performance
- Snapshot > 24h → dégradation I/O ~10%
- Snapshot > 72h → dégradation I/O ~30%
- Snapshot > 7j → risque corruption/blocage
→ VMware recommande : < 24-72h maximum

⚠️ DANGER 3 : Consommation espace
VM 500 GB + taux modification 10%/jour
- Snapshot J+1 : 50 GB
- Snapshot J+7 : 350 GB (cumul)
→ Saturation datastore possible !
```

**🔹 Fonctionnement technique snapshot VMware** :

```
📸 Création snapshot :
1. VM-PROD.vmdk → lecture seule (frozen)
2. Création VM-PROD-000001-delta.vmdk
3. Nouvelles écritures → delta uniquement
4. Fichier pointeur : VM-PROD-000001.vmdk

🔄 Chaîne de snapshots :
[Base VMDK] ← [Delta 1] ← [Delta 2] ← [Delta 3 ACTIF]
    100 GB      + 20 GB    + 15 GB    + 10 GB

⚠️ Lecture nécessite de traverser TOUTE la chaîne
→ Plus de snapshots = Plus lent

🗑️ Suppression snapshot (consolidation) :
1. Merge delta → base VMDK
2. Opération I/O intensive (éviter heures prod)
3. Durée : ~1h pour 100 GB delta
```

**⚙️ Cas d'usage professionnels** :

```
✅ BON usage snapshot :
├─ Avant patch Windows (rollback si échec)
├─ Test nouvelle version application
├─ Clone dev rapide (linked clone)
└─ Protection backup (snapshot → backup → delete)
   Durée vie : < 24 heures

❌ MAUVAIS usage snapshot :
├─ "Sauvegarde" long terme (jours/semaines)
├─ Protection ransomware (même volume = cryptable)
├─ Conformité légale (non immuable)
└─ Alternative au backup (aucune copie externe)
```

**🔹 Architecture correcte avec les deux** :

```
🏢 Infrastructure production :

┌──────────────────────────────────────┐
│  PRODUCTION (Site principal)         │
│  ├─ VM-SQL (datastore-prod01)        │
│  │   ├─ Snapshot pré-patch (2h)      │ ← Protection court terme
│  │   └─ Snapshot Veeam backup (30s)  │ ← Cohérence backup
│  │                                    │
│  └─ Backup local (NAS)                │
│      └─ Veeam Repository (10 TB)     │ ← Backup moyen terme (30j)
└──────────────────────────────────────┘
                  ⬇️ Réplication
┌──────────────────────────────────────┐
│  SITE DISTANT (Cloud)                 │
│  └─ AWS S3 Glacier                    │ ← Backup long terme (7 ans)
│      └─ Immutable (WORM)              │ ← Protection ransomware
└──────────────────────────────────────┘
```

**🔹 Snapshot Storage (SAN moderne)** :

```
📦 Snapshots matériels (NetApp, Dell EMC, Pure Storage) :
   ├─ Copy-on-Write (COW) : espace delta uniquement
   ├─ Redirect-on-Write (ROW) : nouvelles écritures ailleurs
   ├─ Impact performance : < 5%
   ├─ Nombre snapshots : 1000+ possible
   └─ Restauration : instantanée (flip pointeur)

⚡ Exemple NetApp ONTAP :
   - 255 snapshots/volume maximum
   - Space reservation : 0% (uniquement delta)
   - Restauration : mount snapshot (lecture seule)
```

**💡 Règle d'or** :
```
📋 Snapshot ≠ Backup
   Snapshot = Point de restauration TEMPORAIRE (même infrastructure)
   Backup = Copie PERMANENTE (infrastructure séparée)

✅ Stratégie correcte : Snapshot + Backup
   - Snapshot : Rollback rapide < 24h (erreur humaine, test)
   - Backup : Protection désastre (incendie, ransomware, conformité)
```

**🎯 Cas réel** : Ransomware CryptoLocker. VMs chiffrées + snapshots chiffrés (même datastore accessible). Seul le backup externe immutable (S3 Object Lock) a permis la restauration complète.

Q7 : **tar archive compressée**

**✅ Réponse : B) tar -czf backup.tar.gz /data**

**Options** :

| Option | Description |
|--------|-------------|
| `-c` | Create |
| `-x` | Extract |
| `-z` | gzip (`.gz`) |
| `-j` | bzip2 (`.bz2`) |
| `-v` | Verbose |
| `-f` | File |

**Exemples** :
```bash
tar -czf backup.tar.gz /data      # Créer
tar -xzf backup.tar.gz            # Extraire
tar -tzf backup.tar.gz            # Lister
tar -czf backup.tar.gz --exclude='*.log' /data
```
| **-c** | --create | Créer nouvelle archive | `tar -cf` |
| **-x** | --extract | Extraire archive | `tar -xf` |
| **-t** | --list | Lister contenu | `tar -tf` |
| **-f** | --file | Spécifier fichier archive | `tar -f backup.tar` |
| **-z** | --gzip | Compression gzip (.gz) | `tar -czf` |
| **-j** | --bzip2 | Compression bzip2 (.bz2) | `tar -cjf` |
| **-J** | --xz | Compression xz (.xz) | `tar -cJf` |
| **-v** | --verbose | Mode verbeux | `tar -cvf` |
| **-p** | --preserve-permissions | Préserver permissions | `tar -cpf` |
| **-r** | --append | Ajouter fichiers | `tar -rf` |
| **-u** | --update | Mettre à jour | `tar -uf` |
| **--exclude** | - | Exclure motif | `--exclude="*.log"` |

**🔹 Exemples création/extraction** :

```bash
# 1. CRÉATION archive basique (non compressée)
tar -cf backup.tar /var/www/html
# Résultat : backup.tar (même taille que source)

# 2. CRÉATION archive compressée GZIP (standard)
tar -czf backup.tar.gz /var/www/html
# Résultat : backup.tar.gz (compression ~60-70%)
# Vitesse : ⚡⚡⚡⚡ | Ratio : ⚡⚡⚡

# 3. CRÉATION archive compressée BZIP2 (meilleure compression)
tar -cjf backup.tar.bz2 /var/www/html
# Résultat : backup.tar.bz2 (compression ~75-85%)
# Vitesse : ⚡⚡ | Ratio : ⚡⚡⚡⚡⚡

# 4. CRÉATION archive compressée XZ (compression maximale)
tar -cJf backup.tar.xz /var/www/html
# Résultat : backup.tar.xz (compression ~80-90%)
# Vitesse : ⚡ | Ratio : ⚡⚡⚡⚡⚡⚡

# 5. CRÉATION avec exclusions (logs, caches)
tar -czf backup.tar.gz \
    --exclude='*.log' \
    --exclude='cache/*' \
    --exclude='tmp/*' \
    /var/www/html

# 6. CRÉATION verbose (affiche progression)
tar -czvf backup.tar.gz /var/www/html
# Output : affiche chaque fichier ajouté

# 7. EXTRACTION archive
tar -xzf backup.tar.gz
# Extrait dans répertoire courant

# 8. EXTRACTION dans dossier spécifique
tar -xzf backup.tar.gz -C /restore/destination

# 9. LISTER contenu sans extraire
tar -tzf backup.tar.gz
# Affiche liste fichiers

# 10. EXTRAIRE fichier spécifique
tar -xzf backup.tar.gz var/www/html/index.php
# Extrait uniquement index.php
```

**🔹 Comparaison des compressions** :

```
📊 Test sur dossier /var/www (1 GB, fichiers mixtes) :

┌──────────────┬──────────┬───────────┬──────────────┬──────────┐
│ Méthode      │ Taille   │ Ratio     │ Temps créa   │ Temps ext│
├──────────────┼──────────┼───────────┼──────────────┼──────────┤
│ tar (aucun)  │ 1000 MB  │   0%      │   20 sec     │  15 sec  │
│ tar.gz       │  350 MB  │  65%      │   45 sec     │  30 sec  │
│ tar.bz2      │  280 MB  │  72%      │  120 sec     │  90 sec  │
│ tar.xz       │  250 MB  │  75%      │  180 sec     │  60 sec  │
└──────────────┴──────────┴───────────┴──────────────┴──────────┘

✅ RECOMMANDATION : tar.gz (bon compromis vitesse/compression)
```

**⚙️ Scripts professionnels** :

```bash
#!/bin/bash
# Script backup quotidien avec rotation

BACKUP_DIR="/backup"
DATE=$(date +%Y%m%d)
SOURCE="/var/www /etc /home"
RETENTION_DAYS=7

# Création archive
tar -czf "${BACKUP_DIR}/backup-${DATE}.tar.gz" \
    --exclude='*/cache/*' \
    --exclude='*/tmp/*' \
    --exclude='*.log' \
    ${SOURCE}

# Vérification intégrité
tar -tzf "${BACKUP_DIR}/backup-${DATE}.tar.gz" > /dev/null
if [ $? -eq 0 ]; then
    echo "✅ Backup OK : backup-${DATE}.tar.gz"
else
    echo "❌ Backup ERREUR"
    exit 1
fi

# Suppression archives > 7 jours
find ${BACKUP_DIR} -name "backup-*.tar.gz" -mtime +${RETENTION_DAYS} -delete

# Log résultat
echo "$(date) - Backup terminé" >> /var/log/backup.log
```

**🔹 Options avancées** :

```bash
# Backup incrémental (depuis fichier snapshot)
tar -czf backup-incr.tar.gz \
    --listed-incremental=snapshot.file \
    /data

# Split archive en morceaux de 1GB (DVD, transfert)
tar -czf - /data | split -b 1G - backup.tar.gz.
# Résultat : backup.tar.gz.aa, backup.tar.gz.ab, ...

# Restauration split
cat backup.tar.gz.* | tar -xzf -

# Backup via SSH distant
tar -czf - /data | ssh user@remote "cat > /backup/backup.tar.gz"

# Test intégrité complète
tar -tzf backup.tar.gz > /dev/null && echo "OK" || echo "CORRUPT"
```

**💡 Best practices** :
- Toujours tester extraction après création (`tar -tzf`)
- Utiliser `-p` pour préserver permissions (critique pour restore système)
- Exclure caches et logs (gain espace ~30-40%)
- Nommer archives avec date ISO 8601 : `backup-20250101-235959.tar.gz`

**🎯 Cas réel** : Backup serveur web 50 GB → `tar -czf` → 12 GB archive → Upload S3 (économie bande passante 76%).

Q8 : **Outil Windows backup CLI**

**✅ Réponse : B) wbadmin**

**Commandes** :

| Commande | Description |
|----------|-------------|
| `start backup` | Lancer backup |
| `start recovery` | Restaurer |
| `get versions` | Lister backups |
| `enable backup` | Planifier |

**Exemples** :
```powershell
wbadmin start backup -backupTarget:E: -include:C: -quiet
wbadmin get versions -backupTarget:E:
wbadmin start recovery -version:12/19/2025-02:00
```
| **start backup** | Démarrer sauvegarde | `wbadmin start backup -backupTarget:E:` |
| **start systemstatebackup** | Backup état système | `wbadmin start systemstatebackup -backupTarget:E:` |
| **start recovery** | Restaurer fichiers | `wbadmin start recovery` |
| **get versions** | Lister backups | `wbadmin get versions` |
| **get items** | Lister fichiers backup | `wbadmin get items -version:...` |
| **get status** | État backup en cours | `wbadmin get status` |
| **get disks** | Lister disques | `wbadmin get disks` |
| **enable backup** | Planifier backup | `wbadmin enable backup -addtarget:E:` |
| **disable backup** | Désactiver planification | `wbadmin disable backup` |

**🔹 Syntaxe complète exemples** :

```powershell
# 1. BACKUP COMPLET vers disque externe
wbadmin start backup `
    -backupTarget:E: `
    -include:C:,D: `
    -allCritical `
    -quiet

# 2. BACKUP ÉTAT SYSTÈME (AD, Registry, IIS)
wbadmin start systemstatebackup `
    -backupTarget:\\NAS\Backups `
    -quiet

# 3. BACKUP vers partage réseau
wbadmin start backup `
    -backupTarget:\\SERVER-NAS\Backup$ `
    -include:C:\Data `
    -user:DOMAIN\BackupUser `
    -password:P@ssw0rd `
    -vssFull `
    -quiet

# 4. PLANIFICATION backup quotidien 2h du matin
wbadmin enable backup `
    -addtarget:E: `
    -schedule:02:00 `
    -include:C:,D: `
    -allCritical `
    -quiet

# 5. LISTER versions de backup disponibles
wbadmin get versions `
    -backupTarget:E:

# Résultat exemple :
# Version: 12/19/2025-02:00
# Backup location: E:\WindowsImageBackup\SERVER01

# 6. RESTAURER fichier spécifique
wbadmin start recovery `
    -version:12/19/2025-02:00 `
    -itemType:File `
    -items:C:\Data\Important.docx `
    -recoverytarget:C:\Restore `
    -notRestoreAcl `
    -quiet

# 7. RESTAURER volume complet
wbadmin start recovery `
    -version:12/19/2025-02:00 `
    -itemType:Volume `
    -items:D: `
    -recoverytarget:F: `
    -quiet

# 8. RESTAURER ÉTAT SYSTÈME (mode WinRE)
# Redémarrer en WinRE puis :
wbadmin start systemstaterecovery `
    -version:12/19/2025-02:00 `
    -backupTarget:E: `
    -quiet
```

**🔹 Options importantes** :

```powershell
📋 Options principales :

-backupTarget:      # Destination (disque, réseau)
-include:           # Volumes à sauvegarder (C:,D:)
-allCritical        # Tous volumes critiques système
-systemState        # État système (AD, Registry)
-vssFull            # VSS Full (pas Copy)
-quiet              # Mode silencieux (scripts)
-user: -password:   # Credentials réseau
-schedule:          # Planification (HH:MM)
-recoverytarget:    # Destination restauration
-itemType:          # File ou Volume
-version:           # Version backup (date-heure)
```

**⚙️ Script PowerShell complet** :

```powershell
<#
.SYNOPSIS
    Backup automatisé Windows Server
.DESCRIPTION
    Backup quotidien avec gestion erreurs et email notification
#>

# Configuration
$BackupTarget = "\\NAS-BACKUP\Serveurs\SERVER01"
$IncludeVolumes = "C:,D:"
$LogFile = "C:\Logs\backup.log"
$EmailTo = "admin@societe.fr"
$SMTPServer = "smtp.societe.fr"

# Fonction logging
function Write-Log {
    param($Message)
    $Timestamp = Get-Date -Format "yyyy-MM-dd HH:mm:ss"
    "$Timestamp - $Message" | Tee-Object -FilePath $LogFile -Append
}

try {
    Write-Log "Début backup"
    
    # Vérifier espace disque cible
    $FreeSpace = (Get-PSDrive E).Free / 1GB
    if ($FreeSpace -lt 100) {
        throw "Espace disque insuffisant : $FreeSpace GB"
    }
    
    # Lancer backup
    $Result = wbadmin start backup `
        -backupTarget:$BackupTarget `
        -include:$IncludeVolumes `
        -allCritical `
        -vssFull `
        -quiet
    
    if ($LASTEXITCODE -eq 0) {
        Write-Log "✅ Backup réussi"
        
        # Lister versions
        $Versions = wbadmin get versions -backupTarget:$BackupTarget
        Write-Log "Versions disponibles : $($Versions.Count)"
        
        # Email succès
        Send-MailMessage `
            -To $EmailTo `
            -From "backup@societe.fr" `
            -Subject "✅ Backup OK - SERVER01" `
            -Body "Backup terminé avec succès" `
            -SmtpServer $SMTPServer
    } else {
        throw "wbadmin a retourné code erreur $LASTEXITCODE"
    }
    
} catch {
    Write-Log "❌ Erreur : $($_.Exception.Message)"
    
    # Email échec
    Send-MailMessage `
        -To $EmailTo `
        -From "backup@societe.fr" `
        -Subject "❌ Backup ÉCHEC - SERVER01" `
        -Body "Erreur : $($_.Exception.Message)" `
        -SmtpServer $SMTPServer `
        -Priority High
    
    exit 1
}
```

**🔹 Tâche planifiée Windows** :

```powershell
# Créer tâche planifiée
$Action = New-ScheduledTaskAction `
    -Execute "PowerShell.exe" `
    -Argument "-File C:\Scripts\Backup-Server.ps1"

$Trigger = New-ScheduledTaskTrigger `
    -Daily `
    -At 2:00AM

$Principal = New-ScheduledTaskPrincipal `
    -UserId "SYSTEM" `
    -LogonType ServiceAccount `
    -RunLevel Highest

Register-ScheduledTask `
    -TaskName "Backup Quotidien" `
    -Action $Action `
    -Trigger $Trigger `
    -Principal $Principal `
    -Description "Backup automatique serveur"
```

**🔹 Restauration contrôleur de domaine** :

```powershell
# 1. Démarrer en mode restauration services annuaire (DSRM)
# F8 au boot → "Directory Services Restore Mode"

# 2. Restaurer état système
wbadmin start systemstaterecovery `
    -version:12/19/2025-02:00 `
    -backupTarget:E: `
    -authsysvol `
    -quiet

# 3. Redémarrer
shutdown -r -t 0

# 4. Marquer comme autoritaire (si nécessaire)
ntdsutil "authoritative restore" "restore subtree dc=societe,dc=fr" quit quit
```

**💡 Différence vs autres outils** :
- **robocopy** : Copie fichiers (pas VSS, pas état système)
- **xcopy** : Obsolète, basique
- **wbadmin** : VSS-aware, backup système complet, restore bare-metal

**🎯 Limitation** : wbadmin ne supporte QUE les backups locaux/réseau. Pas de cloud natif (utiliser Azure Backup pour cela).

Q9 : **NAS (Network Attached Storage)**

**✅ Réponse : B) Network Attached Storage**

**Protocoles** :

| Protocole | Système | Port |
|-----------|---------|------|
| SMB/CIFS | Windows | 445 |
| NFS | Linux | 2049 |
| iSCSI | Multi (bloc) | 3260 |

**RAID** : RAID 5/6 (tolérance pannes)

**Usage** : Partages, backups Veeam, snapshots

**Marques** : Synology, QNAP, Netgear
| **SMB/CIFS** | Windows | 445 | AD/Local | ⚡⚡⚡⚡ | Partages Windows |
| **NFS** | Linux/Unix | 2049 | UID/GID | ⚡⚡⚡⚡⚡ | ESXi datastores, Linux |
| **AFP** | macOS | 548 | Apple ID | ⚡⚡⚡ | Mac (obsolète) |
| **FTP/FTPS** | Multi | 21 | User/Pass | ⚡⚡ | Transferts Internet |
| **WebDAV** | Multi | 80/443 | HTTP Auth | ⚡⚡ | Accès web |
| **iSCSI** | Multi | 3260 | CHAP | ⚡⚡⚡⚡⚡ | Bloc (pas fichier) |

**🔹 Niveaux RAID** :

```
📦 Configuration RAID NAS :

RAID 0 (Striping) : 2+ disques, 0 rédondance
├─ Capacité : 100% (2x 4TB = 8TB)
├─ Performance : ⭐⭐⭐⭐⭐
└─ Sécurité : ❌ (1 disque en panne = TOUT perdu)

RAID 1 (Mirroring) : 2 disques, miroir
├─ Capacité : 50% (2x 4TB = 4TB)
├─ Performance : ⭐⭐⭐
└─ Sécurité : ✅ (1 disque toléré)

RAID 5 : 3+ disques, parité distribuée
├─ Capacité : (N-1) x taille (4x 4TB = 12TB)
├─ Performance : ⭐⭐⭐⭐
└─ Sécurité : ✅ (1 disque toléré) ⚠️ Rebuild long

RAID 6 : 4+ disques, double parité
├─ Capacité : (N-2) x taille (6x 4TB = 16TB)
├─ Performance : ⭐⭐⭐
└─ Sécurité : ✅✅ (2 disques tolérés)

RAID 10 : 4+ disques, miroir + stripe
├─ Capacité : 50% (4x 4TB = 8TB)
├─ Performance : ⭐⭐⭐⭐⭐
└─ Sécurité : ✅✅ (N/2 disques tolérés)

SHR (Synology Hybrid RAID) : propriétaire
├─ Capacité : Optimisé auto (tailles mixtes OK)
├─ Performance : ⭐⭐⭐⭐
└─ Sécurité : ✅ (1 ou 2 disques selon config)
```

**🔹 Usages professionnels** :

```
🏢 Exemple PME (50 collaborateurs) :

🖥️ NAS Principal : Synology DS1821+ (8 baies)
├─ Disques : 8x 8TB WD Red Pro (RAID 6)
├─ Capacité utile : 48 TB
├─ Mémoire : 32 GB ECC
└─ Réseau : 2x 10 GbE (LAG)

💼 Cas d'usage :
├─ Partages SMB : Commun, Compta, RH, Projets
├─ Backup Veeam : Repository principal (20 VMs)
├─ Snapshots : Toutes les 4h (protection ransomware)
├─ Réplication : NAS secondaire (site distant)
└─ Cloud Sync : AWS S3 (archives annuelles)

💰 Coût :
├─ NAS : 2000 €
├─ Disques : 8x 250€ = 2000 €
├─ RAM : 300 €
└─ TOTAL : ~4500 € (vs SAN 50k€+)
```

**⚙️ Configuration SMB (Windows)** :

```powershell
# Monter partage NAS en PowerShell
New-PSDrive -Name "Z" `
    -PSProvider FileSystem `
    -Root "\\NAS-BACKUP\Veeam" `
    -Credential (Get-Credential) `
    -Persist

# Vérifier accès
Test-Path Z:\

# Lister fichiers
Get-ChildItem Z:\ | Select-Object Name, Length, LastWriteTime
```

**⚙️ Configuration NFS (Linux)** :

```bash
# Installation client NFS
sudo apt install nfs-common

# Monter partage NFS
sudo mount -t nfs \
    192.168.1.100:/volume1/backup \
    /mnt/nas

# Vérifier montage
df -h | grep nas

# Montage permanent (/etc/fstab)
echo "192.168.1.100:/volume1/backup /mnt/nas nfs defaults 0 0" | sudo tee -a /etc/fstab
```

**⚙️ Configuration iSCSI (VMware)** :

```bash
# 1. Sur NAS : Créer LUN iSCSI (ex: 500GB pour datastore)
# 2. Sur ESXi : Ajouter adaptateur iSCSI

esxcli iscsi software set --enabled=true
esxcli iscsi adapter discovery sendtarget add \
    --address=192.168.1.100 \
    --adapter=vmhba33

# 3. Rescan
esxcli storage core adapter rescan --adapter=vmhba33

# 4. Formater VMFS
vmkfstools -C vmfs6 -S DATASTORE-NAS /vmfs/devices/disks/naa.xxx
```

**🔹 Marques professionnelles** :

| Marque | Gamme | Prix | Cible | Points forts |
|--------|-------|------|-------|-------------|
| **Synology** | DS/RS | €€€ | PME/Pro | DSM (OS excellent), apps |
| **QNAP** | TS/TVS | €€ | PME | Performances, VM natives |
| **Netgear** | ReadyNAS | € | TPE | Simplicité, prix |
| **Buffalo** | TeraStation | € | TPE | Basique, fiable |
| **TrueNAS** | Mini/R | €€€ | Expert | Open-source, ZFS |

**🔹 NAS vs SAN vs DAS** :

| Caractéristique | NAS | SAN | DAS |
|-----------------|-----|-----|-----|
| **Accès** | Fichiers (SMB/NFS) | Blocs (FC/iSCSI) | Direct (SATA/SAS) |
| **Réseau** | Ethernet (1/10 GbE) | Fibre Channel (16 Gbps) | Câble direct |
| **Partage** | ✅ Multi-clients | ✅ Multi-serveurs | ❌ 1 serveur |
| **Coût** | €€ | €€€€€ | € |
| **Complexité** | Faible | Élevée | Très faible |
| **Performance** | ⚡⚡⚡⚡ | ⚡⚡⚡⚡⚡ | ⚡⚡⚡ |
| **Cas d'usage** | Partages, backups | Bases SQL, VMs | Backup local |

**💡 Best practices** :
- RAID 6 ou SHR-2 pour backups critiques (tolérance 2 pannes)
- UPS obligatoire (coupure = corruption possible)
- Snapshots activés (protection ransomware)
- Mise à jour firmware régulière (sécurité)
- Test restauration mensuel

**🎯 Cas réel** : PME infectée ransomware. Serveurs cryptés MAIS snapshots NAS (immutables) ont permis restauration complète en 4h. Perte limitée : 2h de données (dernier snapshot).

Q10 : **Capacité LTO-9**

**✅ Réponse : C) 18 TB**

**Générations** :

| Génération | Natif | Compressé | Année |
|-------------|-------|-----------|------|
| LTO-7 | 6 TB | 15 TB | 2015 |
| LTO-8 | 12 TB | 30 TB | 2017 |
| **LTO-9** | **18 TB** | **45 TB** | 2021 |
| LTO-10 | 36 TB | 90 TB | ~2024 |

**Usage** : Archives long terme, conformité  
**WORM** : Write Once (protection fraude)
| **LTO-2** | 200 GB | 400 GB | 40 MB/s | 80 MB/s | 2003 | LTO-1 (R/W) |
| **LTO-3** | 400 GB | 800 GB | 80 MB/s | 160 MB/s | 2005 | LTO-2 (R/W), LTO-1 (R) |
| **LTO-4** | 800 GB | 1,6 TB | 120 MB/s | 240 MB/s | 2007 | LTO-3 (R/W), LTO-2 (R) |
| **LTO-5** | 1,5 TB | 3 TB | 140 MB/s | 280 MB/s | 2010 | LTO-4 (R/W), LTO-3 (R) |
| **LTO-6** | 2,5 TB | 6,25 TB | 160 MB/s | 400 MB/s | 2012 | LTO-5 (R/W), LTO-4 (R) |
| **LTO-7** | **6 TB** | **15 TB** | 300 MB/s | 750 MB/s | 2015 | LTO-6 (R/W), LTO-5 (R) |
| **LTO-8** | **12 TB** | **30 TB** | 360 MB/s | 900 MB/s | 2017 | LTO-7 (R/W) |
| **LTO-9** | **18 TB** | **45 TB** | 400 MB/s | 1000 MB/s | 2021 | LTO-8 (R/W) |
| **LTO-10** | 36 TB (prévu) | 90 TB | 1100 MB/s | - | ~2024 | LTO-9 (R/W) |

**🔹 Capacités et coûts** :

```
💰 Analyse coût/TB (décembre 2025) :

LTO-7 :
├─ Capacité : 6 TB natif / 15 TB compressé
├─ Prix cartouche : 35 €
├─ Coût/TB natif : 5,80 €/TB
└─ Coût/TB compressé : 2,33 €/TB

LTO-8 :
├─ Capacité : 12 TB natif / 30 TB compressé
├─ Prix cartouche : 80 €
├─ Coût/TB natif : 6,67 €/TB
└─ Coût/TB compressé : 2,67 €/TB

LTO-9 :
├─ Capacité : 18 TB natif / 45 TB compressé
├─ Prix cartouche : 150 €
├─ Coût/TB natif : 8,33 €/TB
└─ Coût/TB compressé : 3,33 €/TB

VS Cloud (AWS S3 Glacier Deep Archive) :
├─ Coût stockage : 0,99 €/TB/mois
├─ Coût 5 ans : 59,40 €/TB
└─ + Coûts restauration (4-12h délai)

🎯 BREAK-EVEN : ~7 ans (bande < cloud)
```

**🔹 Use cases professionnels** :

```
🏢 Cas 1 : Grande entreprise (archives longue durée)
├─ Données : 500 TB archives 10 ans (conformité)
├─ Solution : Librairie HPE StoreEver MSL6480
├─ Cartouches : 28x LTO-9 (18 TB chacune)
├─ Coût cartouches : 28 × 150€ = 4200 €
├─ Coût librairie : 25 000 € (80 slots)
└─ TOTAL : 29 200 € (vs 300k€ cloud 10 ans)

🏢 Cas 2 : PME (backup mensuel hors-site)
├─ Données : 10 TB backup mensuel
├─ Solution : Lecteur HPE StoreEver LTO-8 Ultrium
├─ Cartouches : 12x LTO-8 (rotation annuelle)
├─ Coût lecteur : 2500 €
├─ Coût cartouches : 12 × 80€ = 960 €
└─ TOTAL : 3460 € première année
```

**⚙️ Configuration Veeam avec LTO** :

```powershell
# 1. Ajouter librairie de bandes
Add-VBRTapeLibrary -Name "HPE-MSL6480" -Shared

# 2. Créer média pool
New-VBRTapeMediaPool -Name "Backup-Mensuel" `
    -Type Custom `
    -Library "HPE-MSL6480"

# 3. Créer job backup vers bande
New-VBRTapeBackupJob -Name "Archivage-LTO" `
    -MediaPool "Backup-Mensuel" `
    -BackupRepository "Repository-Disk" `
    -GFSWeekly 4 `
    -GFSMonthly 12 `
    -GFSYearly 7

# 4. Démarrer job
Start-VBRTapeBackupJob -Name "Archivage-LTO"

# 5. Vérifier cartouches
Get-VBRTapeMedium | Select-Object Name, Capacity, FreeSpace, Location
```

**🔹 WORM (Write Once Read Many)** :

```
🔒 LTO-WORM (protection ransomware/fraude) :

├─ Cartouches spéciales : LTO-x WORM
├─ Propriété : Écriture unique, impossible modifier/effacer
├─ Prix : +20% vs cartouche standard
├─ Usage : Conformité légale (banques, hopitaux, justice)
└─ Certifications : SEC 17a-4, HIPAA, RGPD

Exemple : Audit comptable 10 ans obligatoire
→ Backup mensuel sur LTO-9 WORM
→ Stockage coffre-fort (protection physique)
```

**🔹 Délai restauration** :

```
⏱️ Temps restauration 10 TB :

LTO-9 (400 MB/s natif) :
10 TB = 10 000 GB = 10 240 000 MB
10 240 000 MB / 400 MB/s = 25 600 secondes
= 7,1 heures

+ Temps localisation cartouche (librairie) : 2 min
+ Temps chargement : 1 min
+ Temps positionnement (seek) : 1-5 min

TOTAL : ~7-8 heures

VS Disque (500 MB/s) : ~5-6 heures
VS Cloud (100 MB/s) : ~28 heures + latence réseau
```

**🔹 Durée de vie** :

```
📅 Longévité cartouches LTO :

├─ Nombre passes : 20 000 (lecture/écriture)
├─ Durée stockage : 30 ans (conditions optimales)
├─ Conditions stockage :
│   ├─ Température : 16-25°C
│   ├─ Humidité : 20-50%
│   └─ Position : verticale
└─ Vérification : tous les 2-3 ans (re-tension)

⚠️ Comparaison HDD : 5-10 ans maximum
```

**💡 Avantages vs Cloud** :
- ✅ Coût fixe (pas d'abonnement)
- ✅ Offline (air-gapped, protection ransomware)
- ✅ Pas de bande passante nécessaire
- ✅ Conformité (WORM natif)
- ❌ Délai restauration (heures vs minutes)
- ❌ Gestion physique (transport coffre)

**🎯 Point clé** : LTO idéal pour archives long terme (7+ ans), conformité légale, et protection air-gapped. Pour RTO court (<1h), privilégier disques ou réplication.

Q11 : **rsync préserve permissions**

**✅ Réponse : B) -a (archive)**

**Options** :

| Option | Description |
|--------|-------------|
| `-a` | Archive (=`-rlptgoD`) |
| `-v` | Verbose |
| `-z` | Compression |
| `-r` | Récursif |
| `-p` | Permissions |
| `--delete` | Miroir exact |

**Exemples** :
```bash
rsync -av /source/ /dest/
rsync -avz user@remote:/backup/ /local/
rsync -av --delete --exclude='*.log' /data/ /backup/
```
|--------|------------|-------------|-----------|
| **-a** | --archive | Mode archive (préserve TOUT) | `-rlptgoD` |
| **-v** | --verbose | Mode verbeux (affiche progression) | - |
| **-z** | --compress | Compression transfert | - |
| **-r** | --recursive | Récursif (sous-dossiers) | - |
| **-l** | --links | Préserve liens symboliques | - |
| **-p** | --perms | Préserve permissions | - |
| **-t** | --times | Préserve dates modification | - |
| **-g** | --group | Préserve groupe | - |
| **-o** | --owner | Préserve propriétaire | - |
| **-D** | --devices | Préserve devices/special files | `--devices --specials` |
| **-h** | --human-readable | Tailles lisibles (KB, MB) | - |
| **-P** | --partial --progress | Reprend transferts interrompus + barre progression | - |
| **--delete** | - | Supprime fichiers destination absents source | - |
| **--exclude** | - | Exclure motifs | - |
| **-n** | --dry-run | Simulation (pas de modification) | - |

**🔹 Décomposition de -a (archive)** :

```bash
# -a est équivalent à :
rsync -rlptgoD source/ destination/

# Détail :
# -r : recursive (sous-dossiers)
# -l : links (liens symboliques)
# -p : permissions (chmod 755, etc.)
# -t : times (dates modif)
# -g : group (groupe propriétaire)
# -o : owner (user propriétaire)
# -D : devices + special files

✅ Résultat : Copie IDENTIQUE (permissions, dates, propriétaires)
```

**🔹 Exemples pratiques** :

```bash
# 1. SYNCHRONISATION locale basique
rsync -av /source/ /destination/
# -a = archive, -v = verbose

# 2. SYNCHRONISATION distante (SSH)
rsync -avz /local/data/ user@remote:/backup/data/
# -z = compression (optimise bande passante)

# 3. SYNCHRONISATION avec suppression (miroir exact)
rsync -av --delete /source/ /destination/
# Fichiers supprimés dans source → supprimés destination

# 4. SYNCHRONISATION avec exclusions
rsync -av \
    --exclude='*.log' \
    --exclude='cache/' \
    --exclude='tmp/' \
    /var/www/ /backup/www/

# 5. SYNCHRONISATION avec barre progression
rsync -avP /source/ /destination/
# Affiche : fichier actuel + pourcentage + vitesse

# 6. DRY-RUN (test sans modification)
rsync -avn --delete /source/ /destination/
# Affiche ce qui SERAIT fait (sécurité)

# 7. SYNCHRONISATION avec limite bande passante
rsync -av --bwlimit=10000 /source/ user@remote:/backup/
# Limite : 10 MB/s (10000 KB/s)

# 8. SYNCHRONISATION avec sauvegarde versions précédentes
rsync -av --backup --backup-dir=/backup/old /source/ /destination/
# Fichiers modifiés/supprimés → copiés dans /backup/old

# 9. SYNCHRONISATION incrémentale (hardlinks)
rsync -av --link-dest=/backup/previous /source/ /backup/current/
# Fichiers identiques → hardlinks (gain espace)

# 10. SYNCHRONISATION via rsync daemon (port 873)
rsync -av rsync://backup-server/module/ /local/backup/
```

**⚙️ Script cron automatique** :

```bash
#!/bin/bash
# /usr/local/bin/backup-rsync.sh

# Configuration
SOURCE="/var/www /home /etc"
DESTINATION="backup@192.168.1.100:/backup/serveur01"
LOGFILE="/var/log/rsync-backup.log"
EXCLUDE_FILE="/etc/rsync-exclude.txt"
EMAIL="admin@societe.fr"

# Exclusions (/etc/rsync-exclude.txt)
cat > $EXCLUDE_FILE <<'EOF'
*.log
*.tmp
cache/
tmp/
.cache/
node_modules/
EOF

# Fonction log
log() {
    echo "$(date '+%Y-%m-%d %H:%M:%S') - $1" | tee -a $LOGFILE
}

log "Début synchronisation rsync"

# Exécution rsync
for DIR in $SOURCE; do
    log "Synchronisation : $DIR"
    
    rsync -avz \
        --delete \
        --exclude-from=$EXCLUDE_FILE \
        --stats \
        $DIR $DESTINATION 2>&1 | tee -a $LOGFILE
    
    if [ ${PIPESTATUS[0]} -eq 0 ]; then
        log "✅ Succès : $DIR"
    else
        log "❌ Échec : $DIR (code ${PIPESTATUS[0]})"
        
        # Email alerte
        echo "Erreur backup rsync sur $DIR" | \
            mail -s "❌ Échec backup rsync" $EMAIL
    fi
done

log "Fin synchronisation rsync"

# Statistiques
SIZE=$(du -sh /var/www | awk '{print $1}')
log "Taille totale synchronisée : $SIZE"
```

**⚙️ Crontab (automatisation)** :

```bash
# Ajouter dans crontab
crontab -e

# Synchronisation quotidienne 3h du matin
0 3 * * * /usr/local/bin/backup-rsync.sh

# Synchronisation toutes les 6 heures
0 */6 * * * /usr/local/bin/backup-rsync.sh

# Synchronisation hebdomadaire (dimanche 2h)
0 2 * * 0 /usr/local/bin/backup-rsync.sh
```

**🔹 Synchronisation incrémentale (snapshots)** :

```bash
#!/bin/bash
# Backup incrémental avec hardlinks (style Time Machine)

BACKUP_ROOT="/backup/snapshots"
DATE=$(date +%Y%m%d-%H%M%S)
LATEST="$BACKUP_ROOT/latest"
CURRENT="$BACKUP_ROOT/backup-$DATE"

# Créer snapshot avec hardlinks vers précédent
if [ -d "$LATEST" ]; then
    rsync -av \
        --delete \
        --link-dest="$LATEST" \
        /source/ "$CURRENT/"
else
    rsync -av /source/ "$CURRENT/"
fi

# Mettre à jour lien "latest"
rm -f "$LATEST"
ln -s "$CURRENT" "$LATEST"

echo "Snapshot créé : $CURRENT"

# Résultat :
# /backup/snapshots/backup-20250101-030000/ (complet)
# /backup/snapshots/backup-20250102-030000/ (hardlinks + diff)
# /backup/snapshots/backup-20250103-030000/ (hardlinks + diff)
# → Gain espace énorme (uniquement diff stocké)
```

**🔹 Performance et bande passante** :

```
📈 Test réel : Synchronisation 100 GB (10 000 fichiers)

1ère synchro (complète) :
├─ Sans compression : 15 minutes (réseau 1 Gbps)
└─ Avec -z : 18 minutes (CPU overhead > gain compression)

Synchros suivantes (incrémentales, 1 GB modifié) :
├─ Sans compression : 90 secondes
└─ Avec -z : 75 secondes

🎯 Recommandation :
- LAN (1 Gbps+) : Pas de compression (-z inutile)
- WAN/Internet : Compression obligatoire (-z)
```

**🔹 rsync vs autres outils** :

| Outil | Usage | Avantages | Inconvénients |
|-------|-------|-----------|----------------|
| **rsync** | Synchro incrémentale | Delta-transfer, SSH, compression | Config complexe |
| **scp** | Copie simple SSH | Simplicité | Pas incrémental |
| **rclone** | Cloud sync | Multi-cloud (S3, Azure, GDrive) | Pas SSH natif |
| **tar** | Archives | Compression, portable | Pas incrémental |

**💡 Delta-transfer** :
```
🧠 Algorithme rsync :

Fichier 1 GB avec 10 MB modifiés :
1. rsync calcule checksums blocs (source + destination)
2. Compare checksums
3. Transfert UNIQUEMENT 10 MB différents
4. Reconstruction fichier destination

→ Économie bande passante : 99%
```

**🎯 Cas réel** : Site web 50 GB, modifications quotidiennes 500 MB. Backup rsync quotidien : 2 minutes au lieu de 45 minutes (backup complet). Gain : 95% temps + bande passante.

Q12 : **Déduplication de données**

**✅ Réponse : B) Élimination blocs dupliqués pour économiser espace**

**Fonctionnement** :
```
1. Découpage en blocs (4-8 KB)
2. Hash unique (SHA-256)
3. Stockage : 1er bloc physique, dupliqués = pointeur
```

**Taux** :

| Environnement | Taux |
|---------------|------|
| VDI | 90-95% |
| VMs Windows | 80-85% |
| Bureautique | 70-80% |

**Technologies** : Veeam, Dell EMC Data Domain, NetApp
| **Inline** | Pendant écriture | ⚠️ Impact léger | ✅ 100% | ⚠️⚠️ Élevé | Baie all-flash |
| **Post-process** | Après écriture (scheduled) | ✅ Aucun impact | ⚡ 98-99% | ⚡ Modéré | Backup repos |
| **Hybride** | Inline + post-process | ⚡ Équilibré | ✅ ~99% | ⚡⚡ Moyen | Enterprise |

**🔹 Fonctionnement technique** :

```
🔬 Processus de déduplication :

1. CHUNKING (découpage en blocs)
   ├─ Taille blocs : 4 KB, 8 KB ou variable
   └─ Méthode : Fixed-size ou Variable-size

2. HASHING (empreinte unique)
   ├─ Algorithme : SHA-256, MD5, ou propriétaire
   └─ Résultat : Hash unique par bloc
   
   Exemple :
   Bloc 1 : "Hello World..." → SHA-256 → a3b5c8d1...
   Bloc 2 : "Hello World..." → SHA-256 → a3b5c8d1... (identique !)

3. INDEXATION (table de hachage)
   ┌─────────────────────┬──────────────────────┐
   │ Hash                │ Pointeur bloc physique│
   ├─────────────────────┼──────────────────────┤
   │ a3b5c8d1...         │ /storage/block0001   │
   │ f7e2b9a4...         │ /storage/block0002   │
   │ c4d8e1f5...         │ /storage/block0003   │
   └─────────────────────┴──────────────────────┘

4. STOCKAGE
   ├─ Premier bloc : stocké physiquement
   ├─ Blocs dupliqués : référence (pointeur)
   └─ Compteur références (ref count)

📊 Exemple :
Fichier A : Bloc1, Bloc2, Bloc3
Fichier B : Bloc1, Bloc4, Bloc3

Stockage physique : Bloc1 (ref:2), Bloc2 (ref:1), Bloc3 (ref:2), Bloc4 (ref:1)
→ 4 blocs stockés au lieu de 6 = Gain 33%
```

**🔹 Taux de déduplication réels** :

```
📈 Statistiques secteurs (moyenne) :

💼 Environnement bureautique :
├─ VDI (Virtual Desktop) : 90-95%
├─ Partages fichiers : 70-80%
└─ Messagerie Exchange : 60-70%

🖥️ Infrastructure virtuelle :
├─ VMs Windows Server : 80-85%
├─ VMs similaires (même OS) : 90-95%
└─ VMs mixtes (Linux+Windows) : 60-70%

🗄️ Bases de données :
├─ SQL Server backups : 50-60%
├─ Oracle backups : 40-50%
└─ MongoDB : 30-40%

📁 Données non compressibles :
├─ Vidéos/Images (JPEG, MP4) : 5-10%
├─ Archives (ZIP, RAR) : 5-10%
└─ Données chiffrées : 0-5%

🎯 Moyenne générale entreprise : 60-70%
```

**⚙️ Technologies commerciales** :

```
🔧 Veeam Backup & Replication :
├─ Type : Post-process
├─ Niveau : Variable block (1 MB par défaut)
├─ Compression : Avant dédup (optimal)
├─ Gain typique : 10:1 (avec compression)
└─ Commande PowerShell :
    Set-VBRJobAdvancedOptions -Job "Backup Job" `
        -EnableDeduplication $true `
        -CompressionLevel 6

🔧 Dell EMC Data Domain :
├─ Type : Inline + post-process
├─ Niveau : Variable block (8 KB avg)
├─ Technologie : Global deduplication
├─ Gain typique : 55:1 (backups)
└─ Particularité : Dédup globale (tous clients)

🔧 HPE StoreOnce :
├─ Type : Inline
├─ Niveau : Variable block
├─ Technologie : Catalyst API
├─ Gain typique : 25:1
└─ Intégration : Veeam, Commvault, Veritas

🔧 NetApp ONTAP :
├─ Type : Post-process (inline option)
├─ Niveau : 4 KB blocks
├─ Scope : Volume ou agrégat
├─ Gain typique : 3:1 (production), 10:1 (backup)
└─ Commande CLI :
    volume efficiency on -vserver svm1 -volume vol1
    volume efficiency start -vserver svm1 -volume vol1
```

**🔹 Calcul économies** :

```
💰 Exemple réel PME :

Infrastructure :
├─ 20 VMs Windows Server 2022
├─ Taille moyenne : 100 GB/VM
├─ Total données : 2 TB
├─ Backups : 7 jours complets + 4 semaines + 12 mois

SANS déduplication :
├─ 7 jours × 2 TB = 14 TB
├─ 4 semaines × 2 TB = 8 TB
├─ 12 mois × 2 TB = 24 TB
└─ TOTAL : 46 TB stockage nécessaire

AVEC déduplication (ratio 10:1) :
├─ 46 TB / 10 = 4,6 TB
└─ Gain : 41,4 TB (90%)

💵 Économie financière :
├─ Disques : 41,4 TB × 150€/TB = 6210€
├─ Bande passante WAN (réplication) : -90%
└─ Cloud storage (si applicable) : -90%
```

**⚙️ Configuration Veeam avancée** :

```powershell
# 1. Activer déduplication sur repository
Set-VBRBackupRepository -Repository "Repo-Principal" `
    -EnableDeduplication:$true

# 2. Configurer job avec compression + dédup optimales
$Job = Get-VBRJob -Name "Backup-Prod"
Set-VBRJobAdvancedOptions -Job $Job `
    -EnableDeduplication $true `
    -CompressionLevel 6 `
    -EnableDeletedFileDataRetention $true `
    -RetainDays 30

# 3. Vérifier taux déduplication
Get-VBRBackupRepository | Select-Object Name, 
    @{N="Capacity GB";E={[math]::Round($_.GetContainer().CachedInfo.Capacity / 1GB, 2)}},
    @{N="Free GB";E={[math]::Round($_.GetContainer().CachedInfo.FreeSpace / 1GB, 2)}},
    @{N="Used GB";E={[math]::Round(($_.GetContainer().CachedInfo.Capacity - $_.GetContainer().CachedInfo.FreeSpace) / 1GB, 2)}}

# 4. Statistiques déduplication par job
Get-VBRBackup | ForEach-Object {
    $Stats = $_.GetAllStorages() | Measure-Object -Property Stats.DataSize, Stats.BackupSize -Sum
    [PSCustomObject]@{
        Job = $_.JobName
        DataSize_GB = [math]::Round($Stats[0].Sum / 1GB, 2)
        BackupSize_GB = [math]::Round($Stats[1].Sum / 1GB, 2)
        Ratio = [math]::Round($Stats[0].Sum / $Stats[1].Sum, 2)
    }
}
```

**🔹 Déduplication source vs target** :

| Type | Où | Quand | Avantage | Inconvénient |
|------|-----|-------|----------|-------------|
| **Source** | Client/serveur production | Avant transfert | Réduit bande passante | ⚠️ Charge CPU production |
| **Target** | Repository backup | Après réception | ✅ Pas d'impact production | Bande passante pleine |
| **Global** | Multi-clients | Centralisé | 🌟 Dédup inter-clients | Complexité élevée |

**💡 Points clés** :
- Déduplication ≠ Compression (complémentaires)
- Ordre optimal : Compression → Déduplication
- VMs similaires : taux dédup maximal
- Données chiffrées : dédup impossible (hashs différents)

**🎯 Cas réel** : 200 VMs Windows (OS identiques). Sans dédup : 20 TB. Avec dédup : 2,5 TB (ratio 8:1). Windows system files dupliqués 200× → stockés 1×.

Q13 : **Différence entre réplication et backup ?**

**✅ Réponse correcte : B) Réplication = copie active temps réel / Backup = copie archive**

**📋 Explication détaillée** :

La réplication et le backup sont deux stratégies complémentaires de protection des données, mais avec des objectifs et mécanismes fondamentalement différents.

**🔹 Tableau comparatif complet** :

| Critère | Réplication | Backup |
|---------|------------|--------|
| **Objectif** | Haute disponibilité (HA) | Protection données / PRA |
| **Fréquence** | Temps réel / Continue | Planifié (heures/jours) |
| **État cible** | 🟢 Actif (bootable) | 📦 Archive (inactif) |
| **RTO** | Minutes (quasi-nul) | Heures |
| **RPO** | Secondes | Heures/jours |
| **Protection erreur humaine** | ❌ NON (répliqué immédiatement) | ✅ OUI (versions historiques) |
| **Protection ransomware** | ❌ NON (chiffrement répliqué) | ✅ OUI (si immutable) |
| **Versions multiples** | ❌ Généralement non | ✅ OUI (GFS) |
| **Coût infrastructure** | 💰💰💰💰 Élevé (serveur actif) | 💰💰 Modéré (stockage) |
| **Complexité** | ⚠️⚠️⚠️ Élevée | ⚡⚡ Moyenne |
| **Bande passante** | ⚠️ Continue (WAN coûteux) | ⚡ Périodique |

**🔹 RTO quasi-nul avec réplication** :

```
⏱️ Scénario panne production :

📊 AVEC BACKUP SEUL :
1. Détection panne : 5 minutes
2. Décision restauration : 10 minutes
3. Préparation infrastructure : 30 minutes
4. Restauration données (1 TB) : 2 heures
5. Tests démarrage : 15 minutes
6. Remise en production : 10 minutes
─────────────────────────────────────
RTO TOTAL : ~3 heures
RPO : Dernière sauvegarde (ex: 12h perdues si backup nocturne)

📊 AVEC RÉPLICATION :
1. Détection panne : 5 minutes
2. Basculement automatique (failover) : 2 minutes
3. Mise en production réplica : 3 minutes
─────────────────────────────────────
RTO TOTAL : ~10 minutes
RPO : Dernière réplication (ex: 30 secondes)

🎯 Gain : RTO divisé par 18 / RPO divisé par 1440
```

**⚙️ Technologies de réplication SQL Server** :

```sql
-- 1. SQL ALWAYS ON (Haute disponibilité)
-- Réplication synchrone entre 2+ serveurs

-- Configuration principal :
CREATE AVAILABILITY GROUP [AG-Production]
FOR DATABASE [DB_ERP], [DB_CRM]
REPLICA ON 
    'SQL-PROD' WITH (
        ENDPOINT_URL = 'TCP://sql-prod.local:5022',
        AVAILABILITY_MODE = SYNCHRONOUS_COMMIT,
        FAILOVER_MODE = AUTOMATIC
    ),
    'SQL-DR' WITH (
        ENDPOINT_URL = 'TCP://sql-dr.local:5022',
        AVAILABILITY_MODE = ASYNCHRONOUS_COMMIT,
        FAILOVER_MODE = MANUAL
    );

-- Résultat :
-- ├─ Réplication continue
-- ├─ Failover automatique : < 1 minute
-- ├─ RPO : 0 (synchrone) ou quelques secondes (asynchrone)
-- └─ Lecture possible sur secondaire (load balancing)

-- 2. LOG SHIPPING (Solution économique)
-- Copie périodique transaction logs

-- Activer sur base source
BACKUP DATABASE [DB_ERP]
    TO DISK = '\\NAS\Backups\DB_ERP_Full.bak'
    WITH INIT;

-- Configuration log shipping (via GUI ou T-SQL)
EXEC sp_add_log_shipping_primary_database 
    @database = 'DB_ERP',
    @backup_directory = '\\NAS\Backups',
    @backup_share = '\\NAS\Backups',
    @backup_job_name = 'LSBackup_DB_ERP',
    @backup_schedule_name = 'LSBackupSchedule',
    @backup_threshold = 60,
    @threshold_alert_enabled = 1;

-- RPO : Fréquence backup log (ex: 15 min)
-- RTO : Temps restauration (ex: 30-60 min)
```

**⚙️ Réplication VMware vSphere** :

```powershell
# 1. vSphere Replication (intégré)
# Configuration réplication VM vers site distant

Connect-VIServer -Server vcenter-prod.local

# Activer réplication sur VM
$VM = Get-VM "SERVEUR-PROD-01"
$ReplicationSpec = New-Object VMware.VimAutomation.ViCore.Types.V1.VIServer.ReplicationSpec
$ReplicationSpec.TargetServer = "vcenter-dr.local"
$ReplicationSpec.TargetDatastore = "DATASTORE-DR"
$ReplicationSpec.RPO = 15  # Minutes

New-VIReplication -VM $VM -Spec $ReplicationSpec

# Vérifier statut
Get-VIReplication -VM $VM | Select-Object VM, State, RPO, LastSyncTime

# 2. Basculement (failover)
Invoke-VMFailover -VM $VM -Destination "Site-DR"

# 3. Retour arrière (failback)
Invoke-VMFailback -VM $VM -Source "Site-DR"
```

**🔹 Exemple architecture réplication + backup** :

```
🏢 ARCHITECTURE COMPLÈTE :

┌─────────────────────────────────────────────────┐
│  SITE PRINCIPAL (Paris)                         │
│                                                 │
│  🖥️ SQL-PROD-01 (actif)                         │
│  ├─ Réplication synchrone ────────┐            │
│  │  RPO: 0 sec / RTO: 1 min       │            │
│  │                                  ↓            │
│  └─ Backup Veeam ───→ 💾 NAS-LOCAL │            │
│     (quotidien 2h)     (Rétention 30j)          │
│                              │                   │
└──────────────────────────────┼───────────────────┘
                               │
                    Réplication WAN (chiffrée)
                               ↓
┌─────────────────────────────────────────────────┐
│  SITE DR (Lyon)                                 │
│                                                 │
│  🖥️ SQL-DR-01 (standby)                         │
│  ├─ Réplica synchrone (read-only)              │
│  └─ Backup local ───→ 💾 NAS-DR                 │
│                        (Rétention 30j)          │
└─────────────────────────────────────────────────┘
                               │
                    Backup cloud (archives)
                               ↓
         ☁️ AWS S3 Glacier (Rétention 7 ans)

🛡️ Protection multi-niveaux :
├─ Panne serveur → Basculement SQL-DR (1 min)
├─ Erreur humaine → Restauration backup NAS (1h)
├─ Sinistre site principal → Site DR opérationnel (5 min)
├─ Ransomware → Backup immutable S3 (4h restore)
└─ Conformité légale → Archives 7 ans S3
```

**⚙️ Hyper-V Replica** :

```powershell
# 1. Activer Hyper-V Replica sur hôtes
Enable-VMReplication -VMName "VM-PROD" `
    -ReplicaServerName "HYPERV-DR.local" `
    -ReplicaServerPort 80 `
    -AuthenticationType Kerberos `
    -CompressionEnabled $true `
    -ReplicationFrequencySec 300  # 5 minutes

# 2. Démarrer réplication initiale
Start-VMInitialReplication -VMName "VM-PROD"

# 3. Vérifier statut
Get-VMReplication -VMName "VM-PROD" | Format-List

# Output :
# State                : Replicating
# Health              : Normal
# LastReplicationTime : 19/12/2025 14:35:22
# ReplicationFrequency: 300 seconds

# 4. Test failover (sans impact production)
Start-VMFailover -VMName "VM-PROD" -Prepare
Start-VMFailover -VMName "VM-PROD" -AsTest

# 5. Failover planifié (maintenance)
Start-VMFailover -VMName "VM-PROD" -Prepare
Start-VMFailover -VMName "VM-PROD"

# 6. Failover non planifié (désastre)
Start-VMFailover -VMName "VM-PROD" -AsUnplanned
```

**🔹 Coûts comparatifs** :

```
💰 Exemple infrastructure 10 TB :

📊 RÉPLICATION SEULE :
├─ Serveurs secondaires : 50 000€
├─ Stockage DR : 15 000€
├─ Interconnexion WAN (1 Gbps) : 2000€/mois
├─ Licences (SQL, VMware) : 30 000€
└─ TOTAL : 95 000€ + 24 000€/an

📊 BACKUP SEUL :
├─ Appliance backup : 15 000€
├─ Stockage NAS : 8 000€
├─ Cloud 10 TB (S3) : 250€/mois
├─ Licences Veeam : 5 000€
└─ TOTAL : 28 000€ + 3 000€/an

📊 RÉPLICATION + BACKUP (recommandé) :
├─ Total initial : 110 000€
└─ Total annuel : 27 000€/an

🎯 Répartition selon criticité :
├─ Applications critiques (10%) : Réplication
└─ Applications standard (90%) : Backup uniquement
```

**💡 Complémentarité réplication + backup** :

```
✅ SCÉNARIOS PROTECTION :

1️⃣ Panne matérielle
   → Réplication (failover automatique)
   
2️⃣ Erreur administrateur (DROP TABLE)
   → Backup (restauration point-in-time)
   
3️⃣ Ransomware
   → Backup immutable (réplication compromis)
   
4️⃣ Sinistre datacenter
   → Réplication (RTO minimal)
   
5️⃣ Conformité légale
   → Backup (archives 7+ ans)

🎯 Conclusion : Les deux stratégies sont COMPLÉMENTAIRES
```

**🎯 Best practice** : Applications critiques = Réplication (HA) + Backup (protection données). Applications standards = Backup uniquement.

Q14 : **Qu'est-ce que la restauration granulaire ?**

**✅ Réponse correcte : B) Extraire objets spécifiques (emails, fichiers) sans restauration complète**

**📋 Explication détaillée** :

La restauration granulaire permet de récupérer des éléments individuels (email, fichier, entrée base de données) depuis un backup complet, sans avoir à restaurer l'intégralité de la VM, du serveur ou de la base.

**🔹 Avantages vs restauration complète** :

```
⏱️ Comparaison temps/impact :

❌ RESTAURATION COMPLÈTE (méthode traditionnelle) :
Scénario : Récupérer 1 email supprimé accidentellement
1. Restaurer VM Exchange (500 GB) → 2 heures
2. Démarrer VM temporaire → 10 minutes
3. Monter base Exchange → 15 minutes
4. Rechercher email → 5 minutes
5. Exporter email → 2 minutes
6. Supprimer VM temporaire → 5 minutes
─────────────────────────────────────
TOTAL : ~2h40 + infrastructure temporaire
Impact : Charge réseau/stockage importante

✅ RESTAURATION GRANULAIRE (Veeam Explorer) :
1. Ouvrir backup dans Explorer → 30 secondes
2. Naviguer vers mailbox → 1 minute
3. Rechercher email → 30 secondes
4. Restaurer email → 30 secondes
─────────────────────────────────────
TOTAL : ~3 minutes
Impact : Minimal (lecture backup uniquement)

🎯 Gain : 98,1% temps + Aucune infrastructure temporaire
```

**🔹 Technologies Veeam Explorer** :

| Explorer | Cible | Objets récupérables | Use case |
|----------|-------|-------------------|----------|
| **Exchange** | MS Exchange | Emails, contacts, calendriers, tâches | User a supprimé email important |
| **Active Directory** | AD | Users, OUs, GPOs, attributs | Suppression accidentelle compte |
| **SQL Server** | SQL DB | Tables, stored procedures, vues | Corruption table spécifique |
| **SharePoint** | SharePoint | Documents, listes, sites | Document supprimé |
| **Oracle** | Oracle DB | Tables, tablespaces | Restauration table |
| **OneDrive** | OneDrive | Fichiers individuels | Fichier écrasé |
| **Teams** | MS Teams | Messages, channels | Conversation supprimée |

**⚙️ Veeam Explorer for Exchange** :

```powershell
# 1. Ouvrir backup Exchange dans Explorer
Start-VBRExchangeItemRestoreSession -RestorePoint (Get-VBRRestorePoint -Name "EXCHANGE-01" | Select-Object -Last 1)

# 2. Explorer structure (GUI ou PowerShell)
$Session = Get-VBRExchangeItemRestoreSession
$Mailboxes = Get-VBRExchangeMailbox -Session $Session

# 3. Rechercher emails spécifiques
$Mailbox = $Mailboxes | Where-Object {$_.Name -eq "jdupont@societe.fr"}
$Items = Get-VBRExchangeItem -Mailbox $Mailbox -Folder "Inbox" |
    Where-Object {$_.Subject -like "*Contrat 2025*"}

# 4. Restaurer vers mailbox d'origine
Restore-VBRExchangeItem -Item $Items -To $Mailbox -Folder "Recovered Items"

# 5. Ou exporter vers PST
Export-VBRExchangeItem -Item $Items -Path "C:\Restore\Emails.pst"

# 6. Fermer session
Stop-VBRExchangeItemRestoreSession -Session $Session
```

**⚙️ Veeam Explorer for Active Directory** :

```powershell
# Cas réel : Administrateur a supprimé une OU avec 50 utilisateurs

# 1. Ouvrir backup DC
Start-VBRADRestoreSession -RestorePoint (Get-VBRRestorePoint -Name "DC-01" | Select-Object -Last 1)

# 2. Explorer objets AD
$Session = Get-VBRADRestoreSession
$Objects = Get-VBRADObject -Session $Session -Path "OU=Comptabilite,DC=societe,DC=fr"

# 3. Restaurer OU complète
Restore-VBRADObject -Object $Objects -ToOriginalLocation

# 4. Ou restaurer objets spécifiques
$Users = $Objects | Where-Object {$_.ObjectClass -eq "user"}
Restore-VBRADObject -Object $Users -ToOriginalLocation -OverwriteExisting

# 5. Comparer versions (avant/après)
Compare-VBRADObject -Original $Objects -Current (Get-ADObject -Filter *)
```

**⚙️ Veeam Explorer for SQL Server** :

```powershell
# Cas : Table 'Clients' corrompue, besoin restaurer version hier

# 1. Ouvrir backup SQL
Start-VBRSQLRestoreSession -RestorePoint (Get-VBRRestorePoint -Name "SQL-01" | Select-Object -Last 1)

# 2. Naviguer vers base/table
$Session = Get-VBRSQLRestoreSession
$Database = Get-VBRSQLDatabase -Session $Session -Name "DB_ERP"
$Table = Get-VBRSQLTable -Database $Database -Name "dbo.Clients"

# 3. Restaurer table vers SQL temporaire
Restore-VBRSQLDatabase -Database $Database -ServerName "SQL-TEMP" -DatabaseName "DB_ERP_RESTORE"

# 4. Exporter données table vers CSV
Export-VBRSQLTable -Table $Table -Path "C:\Restore\Clients.csv"

# 5. Ou restaurer directement (overwrite)
Restore-VBRSQLTable -Table $Table -ServerName "SQL-PROD" -DatabaseName "DB_ERP" -OverwriteExisting
```

**🔹 Extraction fichiers VM** :

```powershell
# Veeam Explorer for Storage Snapshots
# Monter VMDK en lecture pour extraire fichiers

# 1. Publier backup comme datastore
$RestorePoint = Get-VBRRestorePoint -Name "FILESERVER-01" | Select-Object -Last 1
Publish-VBRBackupContent -RestorePoint $RestorePoint

# 2. Naviguer dans filesystem
$Files = Get-VBRApplicationItem -RestorePoint $RestorePoint -Path "C:\Shares\Compta"

# 3. Restaurer fichiers spécifiques
$Files | Where-Object {$_.Name -like "Budget_2025*"} |
    Restore-VBRApplicationItem -Destination "C:\Restored"

# 4. Ou copier via Windows Explorer (datastore monté)
# \\VEEAM-SERVER\VBRCatalog\FILESERVER-01\...

# 5. Unpublish après restauration
Unpublish-VBRBackupContent -RestorePoint $RestorePoint
```

**🔹 Commvault IntelliSnap (alternative)** :

```bash
# Commvault : Restauration granulaire Exchange

# 1. Rechercher emails dans catalogue
qoperation search -cs "Mailbox - jdupont" \
    -subject "Contrat" \
    -from "2025-01-01" \
    -to "2025-12-31"

# 2. Restaurer résultats
qoperation restore -cs "Mailbox - jdupont" \
    -target "Recovered Items" \
    -searchResultId 12345

# 3. Export PST
qoperation export -cs "Mailbox - jdupont" \
    -format PST \
    -destination "/restore/export.pst"
```

**🔹 Cas d'usage fréquents** :

```
📧 Email supprimé accidentellement
├─ Utilisateur : "J'ai supprimé un email important d'hier"
├─ Action : Veeam Explorer → Chercher sujet → Restore
└─ Temps : 2 minutes

👤 Compte AD supprimé
├─ Manager : "Impossible de retrouver le compte d'un ancien stagiaire"
├─ Action : Explorer AD → Chercher OU → Restaurer user
└─ Temps : 5 minutes

📊 Table SQL corrompue
├─ DBA : "Table 'Commandes' a 10000 lignes au lieu de 50000"
├─ Action : Explorer SQL → Export table J-1 → Merge données
└─ Temps : 15 minutes

📁 Fichier écrasé
├─ User : "J'ai remplacé le bon fichier par erreur"
├─ Action : Monter backup → Copier version précédente
└─ Temps : 3 minutes
```

**🔹 Indexation pour recherche rapide** :

```
🔍 Veeam Enterprise Manager (indexation catalogue) :

├─ Indexation automatique des backups
├─ Recherche full-text (contenu fichiers/emails)
├─ Interface web self-service utilisateurs
└─ Permissions déléguées (users restaurent eux-mêmes)

Configuration :
1. Activer guest file indexing sur job backup
2. Credentials pour accès filesystem guest
3. Indexation périodique (incrémentale)

Résultat :
└─ Utilisateur cherche "budget Q4" → Trouve fichiers dans tous backups
   → Self-service restore (sans solliciter admin)
```

**💡 Best practices** :
- Indexation activée pour recherches rapides
- Credentials guest sauvegardés (accès filesystem)
- Self-service portal pour users (décharge admins)
- Tests réguliers restauration granulaire (validation)

**🎯 ROI** : Admin sauve 20h/mois grâce restauration granulaire vs restaurations complètes. Self-service users : 40% requêtes en moins au support.

Q15 : **Que signifie GFS (Grandfather-Father-Son) ?**

**✅ Réponse correcte : A) Schéma rotation backups (quotidien/hebdo/mensuel)**

**📋 Explication détaillée** :

GFS est une stratégie de rotation des sauvegardes permettant d'avoir plusieurs niveaux de rétention (court, moyen et long terme) tout en optimisant l'espace de stockage.

**🔹 Schéma 7-4-12 (standard)** :

```
🗓️ ROTATION GFS CLASSIQUE :

👶 SON (Fils) = Backups QUOTIDIENS
├─ Fréquence : Tous les jours
├─ Rétention : 7 jours
├─ Nombre : 7 backups (Lun, Mar, Mer, Jeu, Ven, Sam, Dim)
└─ Type : Incrémental ou Différentiel

👨 FATHER (Père) = Backups HEBDOMADAIRES
├─ Fréquence : Chaque semaine (ex: dimanche)
├─ Rétention : 4 semaines (1 mois)
├─ Nombre : 4 backups
└─ Type : Complet

👴 GRANDFATHER (Grand-père) = Backups MENSUELS
├─ Fréquence : Chaque mois (ex: dernier dimanche)
├─ Rétention : 12 mois (1 an)
├─ Nombre : 12 backups
└─ Type : Complet

📋 TOTAL BACKUPS : 7 + 4 + 12 = 23 points de restauration
```

**🔹 Calendrier détaillé** :

```
📅 JANVIER 2025 :

 Lun  Mar  Mer  Jeu  Ven  Sam  Dim
      01   02   03   04   05
 [S]  [S]  [S]  [S]  [S]  [S]  [F]
      │    │    │    │    │    └─ Hebdo #1
      └────┴────┴────┴────┴──── Quotidiens

 06   07   08   09   10   11   12
[S]  [S]  [S]  [S]  [S]  [S]  [F]
                               └─ Hebdo #2

 13   14   15   16   17   18   19
[S]  [S]  [S]  [S]  [S]  [S]  [F]
                               └─ Hebdo #3

 20   21   22   23   24   25   26
[S]  [S]  [S]  [S]  [S]  [S]  [F+G]
                               └─ Hebdo #4 + MENSUEL

 27   28   29   30   31
[S]  [S]  [S]  [S]  [S]

Légende :
[S] = SON (quotidien, supprimé après 7j)
[F] = FATHER (hebdo, supprimé après 4 sem)
[G] = GRANDFATHER (mensuel, conservé 12 mois)
```

**🔹 Rétentions multiples** :

| Niveau | Type | Fréquence | Rétention | Quantité | Stockage (si 1TB/backup) |
|--------|------|-----------|-----------|----------|------------------------|
| **Son** | Quotidien | Chaque jour | 7 jours | 7 | 7 TB |
| **Father** | Hebdomadaire | Dimanche | 4 semaines | 4 | 4 TB |
| **Grandfather** | Mensuel | Dernier dim | 12 mois | 12 | 12 TB |
| **TOTAL** | - | - | - | **23** | **23 TB** |

**🔹 Variantes GFS** :

```
📋 GFS STANDARD (7-4-12) :
Quotidien (7j) + Hebdo (4 sem) + Mensuel (12 mois)
✅ PME standard

📋 GFS ÉTENDU (7-4-12-7) :
Quotidien + Hebdo + Mensuel + ANNUEL (7 ans)
✅ Conformité légale (comptabilité, hopitaux)

📋 GFS COURT (5-4-6) :
Quotidien (5j ouvrables) + Hebdo (4 sem) + Mensuel (6 mois)
✅ PME avec contraintes budget

📋 GFS LONG (14-8-24-10) :
Quotidien (2 sem) + Hebdo (2 mois) + Mensuel (2 ans) + Annuel (10 ans)
✅ Grande entreprise réglementée (banques)
```

**⚙️ Configuration Veeam GFS** :

```powershell
# Créer job backup avec GFS
$Job = Get-VBRJob -Name "Backup-Production"

# Activer GFS sur job existant
Set-VBRJobOptions -Job $Job `
    -GFSWeekly:$true `
    -GFSWeeklyKeep 4 `
    -GFSMonthly:$true `
    -GFSMonthlyKeep 12 `
    -GFSYearly:$true `
    -GFSYearlyKeep 7

# Vérifier configuration
Get-VBRJobOptions -Job $Job | Select-Object GFS*

# Résultat :
# GFSWeekly          : True
# GFSWeeklyKeep      : 4
# GFSMonthly         : True
# GFSMonthlyKeep     : 12
# GFSYearly          : True
# GFSYearlyKeep      : 7

# Lister points de restauration GFS
Get-VBRRestorePoint | Where-Object {$_.IsGFS} | 
    Select-Object VMName, CreationTime, Type, @{N="GFSType";E={$_.GFSFlags}}
```

**⚙️ Script PowerShell rotation manuelle** :

```powershell
<#
.SYNOPSIS
    Implémentation GFS manuelle pour backups fichiers
.DESCRIPTION
    Rotation 7-4-12 avec gestion automatique suppressions
#>

$BackupRoot = "F:\Backups"
$Date = Get-Date
$DayOfWeek = $Date.DayOfWeek
$DayOfMonth = $Date.Day

# Déterminer type backup
if ($DayOfMonth -eq 1) {
    # Premier du mois = MENSUEL (Grandfather)
    $Type = "MONTHLY"
    $Folder = "$BackupRoot\Monthly"
    $Retention = 365  # 12 mois
} 
elseif ($DayOfWeek -eq "Sunday") {
    # Dimanche = HEBDOMADAIRE (Father)
    $Type = "WEEKLY"
    $Folder = "$BackupRoot\Weekly"
    $Retention = 28   # 4 semaines
} 
else {
    # Autre jour = QUOTIDIEN (Son)
    $Type = "DAILY"
    $Folder = "$BackupRoot\Daily"
    $Retention = 7    # 7 jours
}

# Créer backup
$BackupFile = "$Folder\Backup-$Type-$(Get-Date -Format 'yyyyMMdd-HHmmss').zip"
Compress-Archive -Path "C:\Data" -DestinationPath $BackupFile

Write-Host "✅ Backup $Type créé : $BackupFile"

# Nettoyer anciens backups (respecter rétention)
Get-ChildItem -Path $Folder -Filter "*.zip" | 
    Where-Object {$_.LastWriteTime -lt (Get-Date).AddDays(-$Retention)} |
    ForEach-Object {
        Remove-Item $_.FullName -Force
        Write-Host "❌ Supprimé (rétention dépassée) : $($_.Name)"
    }

# Logs
Add-Content -Path "$BackupRoot\rotation.log" -Value "$(Get-Date) - $Type - $BackupFile"
```

**🔹 Calcul stockage nécessaire** :

```
📊 Exemple : Backup quotidien 500 GB

SANS rotation (backups cumulatifs) :
365 jours × 500 GB = 182,5 TB (❌ Impossible)

AVEC rotation simple (30 jours) :
30 × 500 GB = 15 TB (⚠️ Rétention courte)

AVEC GFS 7-4-12 :
├─ Quotidiens : 7 × 500 GB = 3,5 TB
├─ Hebdomadaires : 4 × 500 GB = 2 TB
├─ Mensuels : 12 × 500 GB = 6 TB
└─ TOTAL : 11,5 TB

AVEC GFS + Incrémental (optimisé) :
├─ 1 Complet : 500 GB
├─ Incrémentaux quotidiens : 7 × 50 GB = 350 GB
├─ Synthétiques hebdo : 4 × 500 GB = 2 TB
├─ Mensuels : 12 × 500 GB = 6 TB
└─ TOTAL : ~8,9 TB

🎯 GFS + Incrémental = Meilleur compromis rétention/espace
```

**🔹 Cas d'usage par secteur** :

```
🏦 BANQUE (conformité stricte) :
GFS 14-8-60-10
├─ Quotidien : 14 jours
├─ Hebdo : 8 semaines (2 mois)
├─ Mensuel : 60 mois (5 ans)
└─ Annuel : 10 ans
💰 Stockage : ~100 TB pour 1 TB prod

🏭 PME STANDARD :
GFS 7-4-12
├─ Quotidien : 7 jours
├─ Hebdo : 4 semaines
└─ Mensuel : 12 mois
💰 Stockage : ~12 TB pour 1 TB prod

🛠️ TPE (budget limité) :
GFS 5-4-6
├─ Quotidien : 5 jours ouvrables
├─ Hebdo : 4 semaines
└─ Mensuel : 6 mois
💰 Stockage : ~6,5 TB pour 1 TB prod
```

**💡 Avantages GFS** :
- ✅ Multiples points restauration (court + moyen + long terme)
- ✅ Optimisation stockage (vs backups cumulatifs)
- ✅ Conformité légale (archives annuelles)
- ✅ Flexibilité restauration (choix granularité)

**🎯 Point clé** : GFS = Standard industrie pour équilibrer rétention longue et gestion espace. Adapter les chiffres (7-4-12) selon besoins/contraintes.

Q16 : **Fréquence recommandée tests de restauration ?**

**✅ Réponse correcte : B) Mensuel pour systèmes critiques / Trimestriel pour standards**

**📋 Explication détaillée** :

Tester les restaurations est AUSSI important que faire les sauvegardes. Un backup non testé = pas de backup. Des études montrent que 30% des restaurations échouent lors d'un véritable sinistre.

**🔹 Fréquences selon criticité** :

| Criticité | Exemples | Fréquence tests | Type test | RTO objectif |
|-----------|----------|-----------------|-----------|-------------|
| **CRITIQUE** | AD, Exchange, SQL Prod, ERP | 🟢 **Mensuel** | Restauration complète | < 1 heure |
| **IMPORTANT** | Serveurs fichiers, CRM, DHCP/DNS | 🟡 **Trimestriel** | Restauration sélective | < 4 heures |
| **STANDARD** | Postes utilisateurs, archives | 🟠 **Semestriel** | Vérification intégrité | < 24 heures |
| **NON-CRITIQUE** | Tests/dev, données temporaires | ⚪ **Annuel** | Validation process | < 1 semaine |

**🔹 Automatisation tests** :

```powershell
<#
.SYNOPSIS
    Tests automatiques restauration Veeam
.DESCRIPTION
    Vérifie mensuellement restauration VMs critiques
#>

param(
    [string]$VeeamServer = "VEEAM-01",
    [string]$TestNetwork = "VLAN-Isolated-Test",
    [string]$TestDatastore = "DATASTORE-TEST",
    [string[]]$CriticalVMs = @("DC-01", "EXCHANGE-01", "SQL-PROD")
)

# Connect Veeam
Connect-VBRServer -Server $VeeamServer

$Report = @()

foreach ($VMName in $CriticalVMs) {
    Write-Host "🔍 Test restauration : $VMName" -ForegroundColor Cyan
    
    try {
        # 1. Récupérer dernier restore point
        $RestorePoint = Get-VBRRestorePoint -Name $VMName | 
            Sort-Object CreationTime -Descending | 
            Select-Object -First 1
        
        if (-not $RestorePoint) {
            throw "Aucun restore point trouvé"
        }
        
        # 2. Lancer restauration test (réseau isolé)
        $StartTime = Get-Date
        
        Start-VBRRestoreVM -RestorePoint $RestorePoint `
            -VMName "TEST-$VMName" `
            -ServerName (Get-VBRServer -Type ESXi | Select-Object -First 1) `
            -Datastore $TestDatastore `
            -NetworkMapping $TestNetwork `
            -PowerOn:$true `
            -RunAsync:$false
        
        $RestoreTime = (Get-Date) - $StartTime
        
        # 3. Vérifier boot
        Start-Sleep -Seconds 120  # Attendre boot
        
        $TestVM = Get-VM -Name "TEST-$VMName"
        $VMwareTools = $TestVM.ExtensionData.Guest.ToolsRunningStatus
        
        if ($VMwareTools -eq "guestToolsRunning") {
            $Status = "✅ SUCCÈS"
            $Details = "VM bootée, VMware Tools actifs"
        } else {
            $Status = "⚠️ AVERTISSEMENT"
            $Details = "VM bootée mais Tools non démarrés"
        }
        
        # 4. Tests supplémentaires selon type
        if ($VMName -like "*SQL*") {
            # Tester connexion SQL
            $SQLTest = Invoke-Sqlcmd -ServerInstance $TestVM.Guest.IPAddress[0] `
                -Query "SELECT @@VERSION" -ErrorAction SilentlyContinue
            
            if ($SQLTest) {
                $Details += " | SQL Server opérationnel"
            }
        }
        
        # 5. Nettoyer VM test
        Stop-VM -VM $TestVM -Confirm:$false
        Remove-VM -VM $TestVM -DeletePermanently -Confirm:$false
        
        # 6. Logger résultats
        $Report += [PSCustomObject]@{
            VM = $VMName
            Date = Get-Date -Format "yyyy-MM-dd HH:mm"
            Status = $Status
            RestoreTime = $RestoreTime.ToString("hh\\:mm\\:ss")
            Details = $Details
        }
        
        Write-Host "$Status - $VMName - $($RestoreTime.TotalMinutes.ToString('F1')) min" -ForegroundColor Green
        
    } catch {
        $Report += [PSCustomObject]@{
            VM = $VMName
            Date = Get-Date -Format "yyyy-MM-dd HH:mm"
            Status = "❌ ÉCHEC"
            RestoreTime = "N/A"
            Details = $_.Exception.Message
        }
        
        Write-Host "❌ ÉCHEC - $VMName : $($_.Exception.Message)" -ForegroundColor Red
    }
}

# Générer rapport HTML
$HTML = $Report | ConvertTo-Html -Title "Tests Restauration Mensuel" -PreContent "<h1>Résultats Tests Restauration</h1>"
$HTML | Out-File "C:\Reports\Restore-Test-$(Get-Date -Format 'yyyyMM').html"

# Envoyer email
Send-MailMessage `
    -To "admin@societe.fr" `
    -From "veeam@societe.fr" `
    -Subject "Tests Restauration Mensuel - $(Get-Date -Format 'MMMM yyyy')" `
    -Body ($Report | Format-Table | Out-String) `
    -Attachments "C:\Reports\Restore-Test-$(Get-Date -Format 'yyyyMM').html" `
    -SmtpServer "smtp.societe.fr"

Disconnect-VBRServer
```

**⚙️ Tâche planifiée** :

```powershell
# Planifier tests mensuels (1er du mois, 3h)
$Action = New-ScheduledTaskAction `
    -Execute "PowerShell.exe" `
    -Argument "-File C:\Scripts\Test-Restauration.ps1"

$Trigger = New-ScheduledTaskTrigger `
    -Monthly `
    -At 3:00AM `
    -DaysOfMonth 1

Register-ScheduledTask `
    -TaskName "Tests Restauration Mensuel" `
    -Action $Action `
    -Trigger $Trigger `
    -User "SYSTEM" `
    -RunLevel Highest
```

**🔹 Métriques à collecter** :

```
📈 INDICATEURS CLÉS (KPIs) :

1️⃣ TAUX DE RÉUSSITE
   ├─ Formule : (Tests réussis / Tests totaux) × 100
   ├─ Objectif : > 95%
   └─ Exemple : 18/20 = 90% (⚠️ Amélioration nécessaire)

2️⃣ RTO RÉEL
   ├─ Mesure : Temps restauration effectif
   ├─ Comparaison : vs RTO objectif
   └─ Exemple : RTO réel 45 min vs objectif 60 min ✅

3️⃣ RPO RÉEL
   ├─ Vérification : Dernière transaction présente
   ├─ Calcul : Différence timestamp backup vs prod
   └─ Exemple : Backup 02h00, test 08h00 → RPO 6h

4️⃣ INTÉGRITÉ DONNÉES
   ├─ Vérification : Checksums, nombre enregistrements
   ├─ Tests : Requêtes SQL, fichiers création/modif
   └─ Exemple : Table 'Clients' 50k lignes (prod) vs 50k (restore) ✅

5️⃣ FONCTIONNALITÉ SERVICES
   ├─ Tests : AD, SQL, Exchange opérationnels
   ├─ Méthode : Scripts validation automatiques
   └─ Exemple : Connexion SQL OK, requete test OK ✅
```

**🔹 Checklist validation restauration** :

```markdown
☑️ CHECKLIST TEST RESTAURATION

▢ PRÉPARATION
  ▢ Réseau isolé test prêt
  ▢ Ressources (CPU/RAM/stockage) disponibles
  ▢ Credentials test préparés
  ▢ Documentation procédure à jour

▢ RESTAURATION
  ▢ Sélection restore point récent
  ▢ Lancement restauration
  ▢ Chronométrage (RTO)
  ▢ Logs erreurs vérifiés

▢ VALIDATION
  ▢ VM/Serveur démarré
  ▢ Services principaux actifs
  ▢ Connectivité réseau OK
  ▢ Données accessibles
  ▢ Intégrité vérifiée (checksums)
  ▢ Tests fonctionnels spécifiques

▢ DOCUMENTATION
  ▢ Rapport test complété
  ▢ Temps restauration noté
  ▢ Problèmes identifiés documentés
  ▢ Actions correctives planifiées

▢ NETTOYAGE
  ▢ Environnement test arrêté
  ▢ Ressources libérées
  ▢ Backups test supprimés
```

**🔹 Veeam SureBackup (automatisation avancée)** :

```powershell
# Créer application group pour tests
Add-VBRViApplicationGroup -Name "Test-Critiques" `
    -VM "DC-01","SQL-PROD","EXCHANGE-01" `
    -StartupOptions @{
        "DC-01" = @{Delay=0; MaxWaitTime=600}
        "SQL-PROD" = @{Delay=120; MaxWaitTime=900}
        "EXCHANGE-01" = @{Delay=240; MaxWaitTime=900}
    }

# Créer job SureBackup
$VirtualLab = Get-VBRVirtualLab -Name "Lab-Isolated"
$BackupJob = Get-VBRJob -Name "Backup-Production"

Add-VBRSureBackupJob -Name "Tests-Mensuels" `
    -VirtualLab $VirtualLab `
    -ApplicationGroup "Test-Critiques" `
    -LinkedJob $BackupJob `
    -ScheduleEnabled:$true `
    -ScheduleOptions @{
        Type = "Monthly"
        DayOfMonth = 1
        StartTime = "03:00"
    }

# Tests avancés (scripts personnalisés)
# Veeam exécute scripts dans VMs testées
Add-VBRSureBackupTestScript -Path "\\scripts\test-sql.ps1" -Type PowerShell
Add-VBRSureBackupTestScript -Path "\\scripts\test-exchange.ps1" -Type PowerShell
```

**🔹 ROI tests réguliers** :

```
📊 CAS RÉEL : Découverte problèmes lors tests

📅 JANVIER : Test mensuel DC-01
└─ ❌ Échec restauration (driver RAID manquant backup)
└─ ✅ Correction immédiate (ajout drivers)
└─ 🛡️ Évité panne critique 3 mois plus tard !

💰 COÛT PRÉVENTION :
├─ Temps tests mensuels : 4h × 12 = 48h/an
├─ Coût admin (50€/h) : 2400€/an
└─ Infrastructure test : 5000€ (one-time)

VS COÛT SINISTRE (backup non fonctionnel) :
├─ Indisponibilité 24h : 50 000€
├─ Perte données : 100 000€
├─ Réputation : Inestimable
└─ TOTAL : > 150 000€

🎯 ROI : Tests préventifs = 7400€ vs Sinistre = 150k€
        Ratio : 1:20 (investissement minimal pour sécurité maximale)
```

**💡 Règle d'or** : "Un backup non testé est un backup qui n'existe pas". Tests = Seule garantie que restauration fonctionnera le jour J.

**🎯 Statistiques industrie** : 34% entreprises ne testent JAMAIS leurs restaurations. 60% de celles-ci échouent lors d'un sinistre réel (source: Veeam 2024 Report).

Q17 : **Algorithme chiffrement recommandé pour backups ?**

**✅ Réponse correcte : C) AES-256**

**📋 Explication détaillée** :

AES-256 (Advanced Encryption Standard 256-bit) est le standard actuel pour le chiffrement des sauvegardes, offrant un équilibre optimal entre sécurité et performance.

**🔹 Algorithmes de chiffrement** :

| Algorithme | Taille clé | Sécurité | Performance | Statut | Usage backup |
|------------|-----------|----------|------------|--------|-------------|
| **DES** | 56 bits | ❌ Obsolète | ⚡⚡⚡⚡⚡ | 🚫 Déprécié (1977) | JAMAIS |
| **3DES** | 168 bits | ⚠️ Faible | ⚡⚡⚡ | 🚫 Phase-out 2023 | JAMAIS |
| **AES-128** | 128 bits | ✅ Bon | ⚡⚡⚡⚡⚡ | ✅ Valide | Acceptable |
| **AES-192** | 192 bits | ✅✅ Très bon | ⚡⚡⚡⚡ | ✅ Valide | Bon |
| **AES-256** | 256 bits | ✅✅✅ Excellent | ⚡⚡⚡⚡ | 🌟 **Recommandé** | **OUI** |
| **RSA** | 2048-4096 bits | ✅✅✅ Excellent | ⚠️ Lent | ✅ Clés | Asymmétrique |
| **ChaCha20** | 256 bits | ✅✅✅ Excellent | ⚡⚡⚡⚡⚡ | ✅ Moderne | Alternatif |

**⚠️ MD5 et SHA ne sont PAS des algorithmes de chiffrement, ce sont des fonctions de HASH (empreinte)**

**🔹 At-rest vs In-transit** :

```
🔒 CHIFFREMENT AT-REST (données stockées) :

├─ Définition : Chiffrement des backups sur supports stockage
├─ Protection : Vol physique (disques, bandes, serveurs)
├─ Algorithme : AES-256
├─ Clé : Symétrique (même clé chiffre/déchiffre)
└─ Exemple :
    ┌───────────────────────────────┐
    │ Backup.vbk (chiffré AES-256) │
    │ Clé : "P@ssw0rd!2025"        │
    │ Stockage : NAS, S3, LTO       │
    └───────────────────────────────┘

🔒 CHIFFREMENT IN-TRANSIT (données en transit) :

├─ Définition : Chiffrement pendant transfert réseau
├─ Protection : Interception réseau (man-in-the-middle)
├─ Protocoles : TLS 1.2/1.3, SSL (obsolète), SSH
├─ Algorithme : AES-256-GCM (avec TLS)
└─ Exemple :
    [Serveur] ───[TLS 1.3]───> [NAS]
      ├─ Handshake : RSA/ECDHE
      ├─ Chiffrement : AES-256-GCM
      └─ Authentification : SHA-384

🎯 BEST PRACTICE : Chiffrer AT-REST **ET** IN-TRANSIT
```

**⚙️ Gestion des clés (KMS)** :

```
🔑 STRATÉGIES GESTION CLÉS :

1️⃣ PASSPHRASE (mot de passe)
   ├─ Simplicité : ✅✅✅✅✅
   ├─ Sécurité : ⚠️⚠️ Faible (humain)
   ├─ Usage : PME/TPE
   └─ Risque : Oubli, faiblesse mot de passe

2️⃣ KEY FILE (fichier clé)
   ├─ Simplicité : ✅✅✅✅
   ├─ Sécurité : ✅✅✅
   ├─ Usage : PME
   └─ Stockage : Séparé backups (USB, coffre)

3️⃣ ENTERPRISE KMS (Azure Key Vault, AWS KMS)
   ├─ Simplicité : ⚠️⚠️ Complexe
   ├─ Sécurité : ✅✅✅✅✅ Excellente
   ├─ Usage : Entreprise
   ├─ Avantages : Rotation auto, HSM, audit
   └─ Coût : ~100-500€/mois

4️⃣ HARDWARE SECURITY MODULE (HSM)
   ├─ Simplicité : ⚠️⚠️⚠️ Très complexe
   ├─ Sécurité : 🌟🌟🌟 Maximum (FIPS 140-2 Level 3)
   ├─ Usage : Banques, gouvernement
   └─ Coût : 10 000€+ (hardware)
```

**⚙️ Configuration Veeam avec chiffrement** :

```powershell
# 1. Ajouter password (chiffrement backup)
$Password = Read-Host -AsSecureString "Entrez passphrase"
Add-VBREncryptionPassword -Password $Password -Description "Clé 2025"

# 2. Activer chiffrement sur job
$Job = Get-VBRJob -Name "Backup-Production"
Set-VBRJobAdvancedOptions -Job $Job `
    -EnableEncryption:$true `
    -EncryptionPassword (Get-VBREncryptionPassword -Description "Clé 2025")

# 3. Niveau chiffrement (AES-256 par défaut)
# Veeam utilise AES-256 nativement

# 4. Vérifier chiffrement actif
Get-VBRJobOptions -Job $Job | Select-Object EncryptionOptions

# 5. Chiffrement réseau (traffic backup)
Set-VBRJobOptions -Job $Job `
    -BackupTrafficEncryption:$true

# 6. Restauration (passphrase nécessaire)
Start-VBRRestoreVM -RestorePoint $RestorePoint `
    -Password (Get-VBREncryptionPassword -Description "Clé 2025")
```

**⚙️ AWS S3 chiffrement (SSE-S3 vs SSE-KMS)** :

```bash
# SSE-S3 (chiffrement AWS géré, AES-256)
aws s3 cp backup.vbk s3://bucket-backups/ \
    --server-side-encryption AES256

# SSE-KMS (clés AWS Key Management Service)
aws s3 cp backup.vbk s3://bucket-backups/ \
    --server-side-encryption aws:kms \
    --ssekms-key-id arn:aws:kms:eu-west-1:123456789:key/abc-def

# Client-side encryption (chiffré AVANT upload)
openssl enc -aes-256-cbc -salt -in backup.vbk -out backup.vbk.enc -k "P@ssw0rd"
aws s3 cp backup.vbk.enc s3://bucket-backups/

# Vérifier chiffrement
aws s3api head-object \
    --bucket bucket-backups \
    --key backup.vbk \
    --query 'ServerSideEncryption'
```

**🔹 Impact performance** :

```
📉 TESTS PERFORMANCE (backup 1 TB) :

SANS chiffrement :
├─ Vitesse : 500 MB/s
├─ CPU usage : 15%
└─ Durée : 35 minutes

AVEC AES-256 (software) :
├─ Vitesse : 480 MB/s (-4%)
├─ CPU usage : 25% (+10%)
└─ Durée : 36 minutes (+3%)

AVEC AES-256 (AES-NI hardware) :
├─ Vitesse : 495 MB/s (-1%)
├─ CPU usage : 17% (+2%)
└─ Durée : 35 minutes (+0%)

🎯 Conclusion : Impact négligeable avec CPU moderne (AES-NI)

AES-NI = Instructions CPU dédiées (Intel/AMD depuis 2010)
Vérifier : grep aes /proc/cpuinfo (Linux)
          CPUID AES (Windows)
```

**🔹 Conformité légale** :

```
📋 RÉGLEMENTATIONS :

🇪🇺 RGPD (Europe) :
├─ Article 32 : "Chiffrement données personnelles"
├─ Obligatoire : Données sensibles
├─ Recommandé : AES-256
└─ Sanctions : Jusqu'à 4% CA mondial

🇺🇸 HIPAA (Santé USA) :
├─ Obligation : Chiffrement données médicales
├─ Standard : AES-256, RSA 2048+
└─ Sanctions : 50k$ par violation

🏦 PCI-DSS (Paiements) :
├─ Exigence 3.4 : Chiffrement PAN (numéros cartes)
├─ Algorithmes : AES-256, RSA 2048+
└─ Audit : Annuel obligatoire

🔒 ISO 27001 :
├─ Contrôle A.10.1.1 : Chiffrement
└─ Recommandation : AES-256, gestion clés
```

**🔹 Cas d'usage ransomware** :

```
🛡️ PROTECTION RANSOMWARE :

Scénario : Attaque ransomware WannaCry

❌ SANS chiffrement backups :
1. Ransomware infecte production
2. Ransomware chiffre aussi backups réseau (SMB accessible)
3. Production ET backups perdus
4. Ransom 50 BTC (1,5M€) demandé
5. Entreprise KO

✅ AVEC chiffrement + immutabilité :
1. Ransomware infecte production
2. Backups S3 immutables + chiffrés (inaccessibles)
3. Restauration depuis backups (4h)
4. Perte limitée : 4h données (dernier backup)
5. 0€ ransom payé

🎯 Chiffrement + Immutabilité + Air-gap = Trio gagnant
```

**🔹 Rotation clés** :

```powershell
# Best practice : Rotation annuelle clés chiffrement

# 1. Générer nouvelle clé
$NewPassword = Read-Host -AsSecureString "Nouvelle passphrase 2026"
Add-VBREncryptionPassword -Password $NewPassword -Description "Clé 2026"

# 2. Re-chiffrer backups existants (Active Full)
$Job = Get-VBRJob -Name "Backup-Production"
Set-VBRJobAdvancedOptions -Job $Job `
    -EncryptionPassword (Get-VBREncryptionPassword -Description "Clé 2026")

Start-VBRJob -Job $Job -FullBackup

# 3. Désactiver ancienne clé (après vérification)
Remove-VBREncryptionPassword -Password (Get-VBREncryptionPassword -Description "Clé 2025")

# 4. Documenter changement (wiki, KeePass)
```

**💡 Règle d'or** : Chiffrez TOUJOURS les backups contenant données sensibles. AES-256 = Standard or. Stockez clés SÉPARÉMENT des backups.

**🎯 Point critique** : Perte clé chiffrement = Perte TOTALE backups. Redondance clés obligatoire (coffre, HSM, KeePass chiffré).

Q18 : **Quelle commande Linux clone un disque entier bit à bit ?**

**✅ Réponse correcte : B) dd if=/dev/sda of=/dev/sdb**

**📋 Explication détaillée** :

`dd` (Data Duplicator, surnommé "Disk Destroyer" car dangereux si mal utilisé) est l'outil Unix/Linux de bas niveau pour copier des données brutes bit à bit, sans se préoccuper du système de fichiers.

**🔹 Syntaxe complète dd** :

| Option | Description | Valeur | Exemple |
|--------|-------------|---------|---------|
| **if=** | Input File (source) | Fichier/Device | `if=/dev/sda` |
| **of=** | Output File (destination) | Fichier/Device | `of=/dev/sdb` |
| **bs=** | Block Size (taille bloc) | Bytes (K, M, G) | `bs=4M` (optimal SSD) |
| **count=** | Nombre blocs à copier | Nombre | `count=1000` |
| **skip=** | Sauter blocs en entrée | Nombre | `skip=100` |
| **seek=** | Sauter blocs en sortie | Nombre | `seek=100` |
| **conv=** | Options conversion | sync, noerror, sparse | `conv=noerror,sync` |
| **status=** | Affichage progression | none, progress | `status=progress` |

**🔹 Cas d'usage clonage** :

```bash
# 1. CLONAGE DISQUE COMPLET (bit à bit)
sudo dd if=/dev/sda of=/dev/sdb bs=4M status=progress
# Copie TOUT : partitions, MBR, boot, données
# Attention : /dev/sdb sera ÉCRASÉ complètement !

# Résultat :
# /dev/sdb devient clone EXACT de /dev/sda
# ├─ Même taille (ou plus grand)
# ├─ Même partitions
# ├─ Même UUID (problème si boot simultané)
# └─ Bootable si source bootable

# 2. CLONAGE PARTITION SPÉCIFIQUE
sudo dd if=/dev/sda1 of=/dev/sdb1 bs=4M status=progress
# Clone uniquement partition 1

# 3. SAUVEGARDE DISQUE vers IMAGE
sudo dd if=/dev/sda of=/backup/disk_image.img bs=4M status=progress conv=sync,noerror
# Crée fichier image complet du disque
# conv=noerror : Continue malgré erreurs lecture
# conv=sync : Remplit blocs erreur par zéros

# 4. RESTAURATION depuis IMAGE
sudo dd if=/backup/disk_image.img of=/dev/sda bs=4M status=progress

# 5. CLONAGE avec COMPRESSION (économie espace)
sudo dd if=/dev/sda bs=4M status=progress | gzip -c > /backup/disk_image.img.gz
# Compression à la volée

# Restauration :
gunzip -c /backup/disk_image.img.gz | sudo dd of=/dev/sda bs=4M status=progress

# 6. CLONAGE via RÉSEAU (SSH)
sudo dd if=/dev/sda bs=4M | ssh user@remote "dd of=/dev/sdb bs=4M"
# Transfère et clone sur machine distante

# 7. CLONAGE MBR uniquement (512 bytes)
sudo dd if=/dev/sda of=/backup/mbr_backup.img bs=512 count=1
# Sauvegarde Master Boot Record

# Restauration :
sudo dd if=/backup/mbr_backup.img of=/dev/sda bs=512 count=1

# 8. WIPE DISQUE (écrasage sécurisé)
sudo dd if=/dev/zero of=/dev/sda bs=4M status=progress
# Remplit disque de zéros (effacement)

# Ou plus sécurisé (random) :
sudo dd if=/dev/urandom of=/dev/sda bs=4M status=progress

# 9. BENCHMARK DISQUE (test vitesse écriture)
sudo dd if=/dev/zero of=/tmp/testfile bs=1G count=1 oflag=direct
# Mesure vitesse écriture pure (sans cache)

# 10. COPIE FICHIER avec progression
dd if=large_file.iso of=/dev/sdb bs=4M status=progress
# Copie ISO vers clé USB bootable
```

**🔹 Block Size (bs) optimal** :

```
📊 IMPACT BLOCK SIZE sur performance :

Test : Copie disque 100 GB HDD → SSD

bs=512 (par défaut) :
├─ Taille bloc : 512 bytes
├─ Nombre opérations I/O : 200 millions
├─ Temps : ⚠️ 8 heures
└─ Vitesse : 3,5 MB/s

bs=4K :
├─ Taille bloc : 4 KB
├─ Nombre opérations I/O : 25 millions
├─ Temps : 90 minutes
└─ Vitesse : 18 MB/s

bs=1M :
├─ Taille bloc : 1 MB
├─ Nombre opérations I/O : 100 000
├─ Temps : 15 minutes
└─ Vitesse : 110 MB/s

bs=4M (recommandé SSD) :
├─ Taille bloc : 4 MB
├─ Nombre opérations I/O : 25 000
├─ Temps : ✅ 12 minutes
└─ Vitesse : ✅ 140 MB/s

bs=64M (trop gros) :
├─ Taille bloc : 64 MB
├─ Nombre opérations I/O : 1 562
├─ Temps : ⚠️ 16 minutes
└─ Vitesse : 105 MB/s (overhead mémoire)

🎯 RECOMMANDATIONS :
├─ HDD → HDD : bs=4M ou bs=8M
├─ SSD → SSD : bs=4M ou bs=16M
├─ NVMe → NVMe : bs=16M ou bs=32M
└─ Réseau : bs=1M (latence)
```

**⚙️ Script clonage sécurisé** :

```bash
#!/bin/bash
# clone-disk.sh : Clonage sécurisé avec vérifications

SOURCE="/dev/sda"
DESTINATION="/dev/sdb"
LOG="/var/log/disk-clone.log"

# Fonction log
log() {
    echo "$(date '+%Y-%m-%d %H:%M:%S') - $1" | tee -a $LOG
}

# Vérifications préliminaires
log "Début clonage $SOURCE → $DESTINATION"

# 1. Vérifier source existe
if [ ! -b "$SOURCE" ]; then
    log "❌ ERREUR : $SOURCE n'existe pas"
    exit 1
fi

# 2. Vérifier destination existe
if [ ! -b "$DESTINATION" ]; then
    log "❌ ERREUR : $DESTINATION n'existe pas"
    exit 1
fi

# 3. Vérifier destination pas montée
if mount | grep -q "$DESTINATION"; then
    log "❌ ERREUR : $DESTINATION est montée, démontez d'abord"
    exit 1
fi

# 4. Vérifier taille destination >= source
SOURCE_SIZE=$(blockdev --getsize64 $SOURCE)
DEST_SIZE=$(blockdev --getsize64 $DESTINATION)

log "Taille source : $(numfmt --to=iec-i --suffix=B $SOURCE_SIZE)"
log "Taille destination : $(numfmt --to=iec-i --suffix=B $DEST_SIZE)"

if [ $DEST_SIZE -lt $SOURCE_SIZE ]; then
    log "❌ ERREUR : Destination trop petite"
    exit 1
fi

# 5. Confirmation utilisateur
echo "⚠️ ATTENTION : $DESTINATION sera COMPLÈTEMENT ÉCRASÉ !"
echo "Source : $SOURCE ($SOURCE_SIZE bytes)"
echo "Destination : $DESTINATION ($DEST_SIZE bytes)"
read -p "Continuer ? (yes/no) : " CONFIRM

if [ "$CONFIRM" != "yes" ]; then
    log "Annulé par utilisateur"
    exit 0
fi

# 6. Lancer clonage
log "Début copie bit à bit..."
START_TIME=$(date +%s)

dd if=$SOURCE of=$DESTINATION bs=4M status=progress conv=noerror,sync 2>&1 | tee -a $LOG

EXIT_CODE=${PIPESTATUS[0]}
END_TIME=$(date +%s)
DURATION=$((END_TIME - START_TIME))

# 7. Vérifier résultat
if [ $EXIT_CODE -eq 0 ]; then
    log "✅ Clonage terminé avec succès"
    log "Durée : $DURATION secondes ($((DURATION / 60)) minutes)"
    
    # 8. Vérification intégrité (checksums)
    log "Vérification intégrité (peut prendre du temps)..."
    
    SOURCE_MD5=$(dd if=$SOURCE bs=4M | md5sum | awk '{print $1}')
    DEST_MD5=$(dd if=$DESTINATION bs=4M count=$((SOURCE_SIZE / 4194304)) | md5sum | awk '{print $1}')
    
    if [ "$SOURCE_MD5" == "$DEST_MD5" ]; then
        log "✅ Intégrité vérifiée : Checksums identiques"
    else
        log "❌ AVERTISSEMENT : Checksums différents !"
        log "   Source MD5 : $SOURCE_MD5"
        log "   Dest MD5   : $DEST_MD5"
    fi
    
else
    log "❌ Clonage échoué (code $EXIT_CODE)"
    exit 1
fi

log "Processus terminé"
```

**🔹 dd vs autres outils** :

| Outil | Niveau | Vitesse | Compression | Réseau | Système fichiers | Use case |
|-------|--------|---------|-------------|--------|-----------------|----------|
| **dd** | Bloc brut | ⚡⚡⚡⚡ | ❌ (manuel) | ⚠️ SSH | ❌ Ignore | Clone exact, forensics |
| **rsync** | Fichiers | ⚡⚡⚡⚡⚡ | ✅ -z | ✅ Natif | ✅ Aware | Sync incrémentale |
| **clonezilla** | Partition | ⚡⚡⚡⚡ | ✅ Intégré | ✅ Multicast | ✅ Aware | Déploiement mass |
| **partclone** | Partition | ⚡⚡⚡⚡⚡ | ✅ Intégré | ❌ | ✅ Aware | Backup optimisé |
| **tar** | Fichiers | ⚡⚡⚡ | ✅ Intégré | ⚠️ SSH | ✅ Aware | Archives portables |

**🔹 Pièges fréquents dd** :

```
⚠️ ERREURS COURANTES :

1️⃣ INVERSION if/of (catastrophique) :
❌ dd if=/dev/sdb of=/dev/sda
   → Écrase PRODUCTION par disque vide !

✅ dd if=/dev/sda of=/dev/sdb
   → Clone production vers backup

2️⃣ OUBLI sudo :
❌ dd if=/dev/sda of=/dev/sdb
   → Permission denied

✅ sudo dd if=/dev/sda of=/dev/sdb

3️⃣ DESTINATION TROP PETITE :
❌ dd if=/dev/sda (1TB) of=/dev/sdb (500GB)
   → Erreur ou données tronquées

✅ Vérifier tailles avec : lsblk -b

4️⃣ UUID DUPLIQUÉS :
Problème : Clone a même UUID que source
→ Conflits si boot simultané

Solution :
# Générer nouveaux UUIDs
sudo tune2fs -U random /dev/sdb1
sudo tune2fs -U random /dev/sdb2

5️⃣ PARTITION MONTÉE :
❌ dd vers partition montée
   → Corruption garantie

✅ Démonter d'abord : sudo umount /dev/sdb1
```

**⚙️ dd pour création clé USB bootable** :

```bash
# Créer clé USB bootable Ubuntu
# ⚠️ ÉCRASE COMPLÈTEMENT clé USB !

# 1. Identifier clé USB
lsblk
# Résultat : /dev/sdc (8 GB)

# 2. Démonter si monté
sudo umount /dev/sdc*

# 3. Copier ISO sur clé
sudo dd if=ubuntu-24.04-desktop-amd64.iso of=/dev/sdc bs=4M status=progress oflag=sync

# 4. Synchroniser (flush buffers)
sudo sync

# 5. Éjecter
sudo eject /dev/sdc

# Clé bootable prête !
```

**🔹 Forensics / Investigation** :

```bash
# Copie forensique (investigation cybersécurité)
# But : Préserver image exacte pour analyse

# 1. Copie avec hash intégrité
sudo dd if=/dev/sda bs=4M conv=noerror,sync | tee >(sha256sum > /evidence/disk.sha256) > /evidence/disk.img

# 2. Vérifier intégrité
sha256sum -c /evidence/disk.sha256

# 3. Montage en lecture seule (analyse)
sudo mount -o ro,loop,offset=1048576 /evidence/disk.img /mnt/forensic

# Analyse sans modifier preuve
```

**💡 Points clés** :
- dd = Copie BRUTE (ignorer système fichiers)
- bs=4M optimal pour SSD/HDD modernes
- TOUJOURS vérifier if/of (inversion = catastrophe)
- status=progress indispensable (suivi)
- conv=noerror,sync pour disques défectueux

**🎯 Cas réel** : Upgrade serveur (HDD 1TB → SSD 2TB). `dd if=/dev/sda of=/dev/sdb bs=4M status=progress`. Temps : 18 minutes. Serveur redémarre sur SSD sans réinstall.

Q19 : **Qu'est-ce que l'Instant VM Recovery (Veeam) ?**

**✅ Réponse correcte : B) Démarrer VM directement depuis repository backup**

**📋 Explication détaillée** :

Instant VM Recovery est une fonctionnalité Veeam permettant de démarrer une VM directement depuis le repository de backup en quelques minutes, sans attendre la restauration complète des données (qui peut prendre des heures).

**🔹 Fonctionnement technique** :

```
⚡ PROCESSUS INSTANT VM RECOVERY :

ÉTAPE 1 : Détection panne VM production
├─ VM-PROD-SQL crashe (corruption, panne serveur)
├─ Temps écoulé : 0 minute
└─ Décision : Instant Recovery

ÉTAPE 2 : Montage backup comme datastore
├─ Veeam publie backup sur ESXi
├─ Backup Repository monté via NFS
├─ VM lit données DIRECTEMENT depuis backup
├─ Temps : ⚡ 2-3 minutes
└─ État : VM démarre depuis backup

ÉTAPE 3 : VM opérationnelle (temporaire)
├─ VM fonctionne en production
├─ Lectures : Depuis backup repository
├─ Écritures : Vers snapshot delta (cache)
├─ Temps : +2 minutes (boot VM)
└─ RTO : ✅ ~5 minutes TOTAL

ÉTAPE 4 : Migration automatique (Quick Migration)
├─ Storage vMotion en arrière-plan
├─ Données copiées vers datastore production
├─ Transparent pour utilisateurs (0 downtime)
├─ Durée : 30 min - 2h selon taille
└─ Fin : VM sur stockage production, repository libéré

┌─────────────────────────────────────────┐
│ TEMPS TOTAL RTO                         │
├─────────────────────────────────────────┤
│ Restauration traditionnelle : 2-4h      │ ❌
│ Instant VM Recovery : 5 min             │ ✅
├─────────────────────────────────────────┤
│ GAIN : 96-98% temps indisponibilité     │
└─────────────────────────────────────────┘
```

**🔹 Architecture technique** :

```
🏗️ COMPOSANTS IMPLIQUÉS :

┌──────────────────────────────────────────┐
│  VEEAM BACKUP SERVER                     │
│  ├─ Contrôleur Instant Recovery          │
│  └─ Orchestration migration              │
└────────────┬─────────────────────────────┘
             │
    ┌────────▼────────┐
    │ BACKUP REPOSITORY│
    │  ├─ Backup files │
    │  └─ NFS Export   │
    └────────┬─────────┘
             │ NFS Mount
    ┌────────▼─────────────────────────────┐
    │  ESXI HOST                            │
    │  ├─ Datastore : [Veeam-Instant-xxx]  │ ← Backup monté
    │  ├─ VM démarre depuis backup         │
    │  └─ Snapshot delta (écritures)       │
    └───────────────────────────────────────┘
             │ Storage vMotion
    ┌────────▼─────────────────────────────┐
    │  DATASTORE PRODUCTION                 │
    │  └─ VM migrée (final)                │
    └───────────────────────────────────────┘
```

**⚙️ Procédure PowerShell** :

```powershell
# 1. Identifier restore point
$VM = "SERVEUR-SQL-PROD"
$RestorePoint = Get-VBRRestorePoint -Name $VM | 
    Sort-Object CreationTime -Descending | 
    Select-Object -First 1

Write-Host "Restore point : $($RestorePoint.CreationTime)"

# 2. Lancer Instant VM Recovery
$InstantRecovery = Start-VBRInstantRecovery `
    -RestorePoint $RestorePoint `
    -ServerName "ESXI-HOST-02.local" `
    -VMName "$VM-InstantRecovery" `
    -Reason "Panne serveur production" `
    -PowerOn

Write-Host "✅ VM démarrée en mode Instant Recovery"
Write-Host "Temps écoulé : $((Get-Date) - $StartTime)"

# 3. Vérifier état VM
Get-VBRInstantRecovery | Where-Object {$_.VMName -like "*$VM*"} | 
    Select-Object VMName, State, StartTime, HostName

# Output :
# VMName                      : SERVEUR-SQL-PROD-InstantRecovery
# State                       : Working
# StartTime                   : 19/12/2025 14:35:00
# HostName                    : ESXI-HOST-02.local

# 4. Tester connectivité/services
Start-Sleep -Seconds 60  # Attendre boot complet

$VMGuest = Get-VM -Name "$VM-InstantRecovery"
$IP = $VMGuest.Guest.IPAddress[0]

Write-Host "IP VM : $IP"
Test-Connection -ComputerName $IP -Count 2

# 5. Migration vers production (Quick Migration)
# Méthode 1 : Automatique (recommandé)
Start-VBRQuickMigration `
    -InstantRecovery $InstantRecovery `
    -TargetDatastore "DATASTORE-PROD-01" `
    -DeleteSourceVM:$false

# Méthode 2 : Manuelle (Storage vMotion GUI)
# vSphere Client → VM → Migrate → Change storage → Select datastore

# 6. Surveiller progression migration
Get-VBRQuickMigration | Where-Object {$_.VMName -like "*$VM*"} |
    Select-Object VMName, State, Progress, StartTime

# Output :
# VMName    : SERVEUR-SQL-PROD-InstantRecovery
# State     : Migrating
# Progress  : 45%
# StartTime : 19/12/2025 14:40:00

# 7. Finaliser (après migration complète)
Stop-VBRInstantRecovery -InstantRecovery $InstantRecovery

Write-Host "✅ Migration terminée, VM sur stockage production"
```

**🔹 Comparaison restauration traditionnelle vs Instant Recovery** :

```
📊 SCÉNARIO : VM SQL Server 500 GB

❌ RESTAURATION TRADITIONNELLE :
┌─────────────────────────────────────────┐
│ ÉTAPES                                  │
├─────────────────────────────────────────┤
│ 1. Créer VM vide           : 5 min      │
│ 2. Restaurer VMDK          : 90 min     │
│ 3. Attacher disques        : 2 min      │
│ 4. Démarrer VM             : 3 min      │
│ 5. Vérifier intégrité SQL  : 10 min     │
├─────────────────────────────────────────┤
│ RTO TOTAL                  : 110 min    │ ⚠️
└─────────────────────────────────────────┘

✅ INSTANT VM RECOVERY :
┌─────────────────────────────────────────┐
│ ÉTAPES                                  │
├─────────────────────────────────────────┤
│ 1. Publier backup          : 2 min      │
│ 2. Démarrer VM depuis backup: 3 min     │
│ 3. Vérifier SQL            : 2 min      │
├─────────────────────────────────────────┤
│ RTO TOTAL                  : 7 min      │ ✅
│ Migration arrière-plan     : 60 min     │ (transparent)
└─────────────────────────────────────────┘

🎯 GAIN RTO : 110 min → 7 min (94% réduction)
```

**🔹 Limitations et considérations** :

```
⚠️ LIMITATIONS INSTANT VM RECOVERY :

1️⃣ PERFORMANCE :
├─ Lectures depuis repository (plus lent que production)
├─ Impact : 20-40% performance vs storage prod
├─ Acceptable : Court terme (quelques heures)
└─ Mitigation : Migration rapide vers production

2️⃣ CHARGE REPOSITORY :
├─ I/O soutenu pendant utilisation VM
├─ Impact autres backups/restores
└─ Best practice : Repository dédié (SSD)

3️⃣ RÉSEAU :
├─ Trafic I/O via réseau (NFS/iSCSI)
├─ Bande passante : 1 Gbps minimum (10 Gbps recommandé)
└─ Latence : < 5ms (backup repository proche ESXi)

4️⃣ SNAPSHOTS :
├─ Écritures VM → Snapshot delta
├─ Croissance snapshot rapide (haute activité)
└─ Migration obligatoire avant saturation

5️⃣ LICENSING :
├─ Fonctionnalité : Veeam Enterprise Plus
├─ Pas disponible : Standard/Essentials
└─ Alternative : U-AIR (Universal Application-Item Recovery)
```

**⚙️ Configuration repository optimisé** :

```
🚀 BEST PRACTICES REPOSITORY INSTANT RECOVERY :

💾 STOCKAGE :
├─ Type : SSD ou All-Flash (IOPS élevés)
├─ RAID : 10 ou 6 (performance + redondance)
├─ Capacité : 3-5× taille VM critiques
└─ Déduplication : Post-process (pas inline)

🌐 RÉSEAU :
├─ Connexion : 10 GbE minimum
├─ VLAN dédié : Trafic backup/restore isolé
├─ LAG/LACP : 2× 10 GbE agrégés = 20 Gbps
└─ Jumbo Frames : MTU 9000

📊 EXEMPLE CONFIGURATION :
┌─────────────────────────────────────────┐
│ Repository : Dell PowerVault ME4024     │
│ ├─ Disques : 24× 1,92 TB SSD (RAID 10)  │
│ ├─ Capacité : 23 TB utilisables         │
│ ├─ IOPS : 450 000 lectures              │
│ ├─ Connexion : 4× 10 GbE (LAG)          │
│ └─ Coût : ~45 000€                      │
└─────────────────────────────────────────┘
```

**🔹 Cas d'usage réels** :

```
🏥 CAS 1 : Hôpital - Serveur PACS (imagerie médicale)
Incident : Crash serveur PACS (panne RAID controller)
├─ VM : 2 TB données images médicales
├─ Criticité : MAXIMALE (vie patients)
├─ Solution : Instant VM Recovery
├─ Temps restauration : 8 minutes
├─ Résultat : Radiologues accès images, 0 patient impacté
└─ Migration prod : 3 heures (arrière-plan, nuit)

💰 CAS 2 : E-commerce - Serveur web Black Friday
Incident : Corruption base MySQL (bug applicatif)
├─ VM : 800 GB (DB + logs)
├─ Perte CA : 5000€/heure
├─ Solution : Instant VM Recovery (backup J-1)
├─ Temps : 6 minutes
├─ Résultat : Site en ligne, perte CA minimale (500€)
└─ RPO : 24h acceptable (données transactionnelles)

🏦 CAS 3 : Banque - Contrôleur domaine AD
Incident : Ransomware chiffre DC principal
├─ VM : 200 GB
├─ Impact : Authentification impossible (2000 users)
├─ Solution : Instant VM Recovery (backup 4h avant)
├─ Temps : 4 minutes
├─ Résultat : Users ré-authentifiés immédiatement
└─ RPO : 4h (acceptable pour AD)
```

**🔹 Alternatives Hyper-V** :

```
📌 HYPER-V ÉQUIVALENTS :

1️⃣ VEEAM INSTANT VM RECOVERY (Hyper-V)
├─ Même principe que VMware
├─ Backup publié via SMB
└─ Migration via Live Migration

2️⃣ WINDOWS SERVER BACKUP (natif)
├─ Restauration vers Hyper-V
├─ Pas de "Instant" (restauration complète)
└─ Gratuit mais limité

Code PowerShell Hyper-V :
# Instant Recovery Hyper-V
Start-VBRHvInstantRecovery `
    -RestorePoint $RestorePoint `
    -Server "HYPERV-02" `
    -DatastoreName "Volume-Backup" `
    -VMName "VM-Recovery"
```

**🔹 Checklist utilisation** :

```markdown
☑️ CHECKLIST INSTANT VM RECOVERY

▢ AVANT INCIDENT :
  ▢ Repository SSD configuré
  ▢ Réseau 10 GbE entre Veeam/ESXi
  ▢ Tests mensuels Instant Recovery
  ▢ Documentation procédure à jour
  ▢ Licences Enterprise Plus actives

▢ PENDANT INCIDENT :
  ▢ Identifier dernier restore point valide
  ▢ Vérifier ressources ESXi disponibles (CPU/RAM)
  ▢ Lancer Instant Recovery
  ▢ Surveiller boot VM
  ▢ Tester services/connectivité

▢ APRÈS RECOVERY :
  ▢ Planifier Quick Migration
  ▢ Exécuter migration (heures creuses)
  ▢ Vérifier VM sur stockage prod
  ▢ Arrêter Instant Recovery
  ▢ Post-mortem incident (causes, améliorations)
```

**💡 Points clés** :
- Instant Recovery = RTO de quelques MINUTES (vs heures)
- VM fonctionne depuis backup (temporaire)
- Migration arrière-plan vers production (transparent)
- Nécessite repository performant (SSD, 10 GbE)

**🎯 ROI** : Downtime évité = ROI immédiat. Exemple : E-commerce 5k€/h indispo → Instant Recovery (6 min) économise 4900€ vs restauration standard (110 min).

Q20 : **Quelle rétention GFS typique pour conformité légale ?**

**✅ Réponse correcte : A) 7 quotidiens, 4 hebdomadaires, 12 mensuels, 7 annuels**

**📋 Explication détaillée** :

La rétention GFS (Grandfather-Father-Son) pour conformité légale doit tenir compte des obligations réglementaires de conservation des données, qui varient selon les secteurs mais nécessitent généralement des archives longue durée (5-10 ans minimum).

**🔹 Schéma GFS étendu 7-4-12-7** :

```
📅 ROTATION GFS CONFORMITÉ LÉGALE :

👶 SON (Quotidiens) : 7 jours
├─ Fréquence : Chaque jour ouvrable
├─ Rétention : 1 semaine
├─ Quantité : 7 backups
└─ Usage : Récupération rapide erreurs récentes

👨 FATHER (Hebdomadaires) : 4 semaines
├─ Fréquence : Chaque dimanche
├─ Rétention : 1 mois (4 semaines)
├─ Quantité : 4 backups
└─ Usage : Point restauration moyen terme

👴 GRANDFATHER (Mensuels) : 12 mois
├─ Fréquence : Dernier dimanche du mois
├─ Rétention : 1 an (12 mois)
├─ Quantité : 12 backups
└─ Usage : Archives trimestrielles/annuelles

🏛️ GREAT-GRANDFATHER (Annuels) : 7 ans
├─ Fréquence : 31 décembre de chaque année
├─ Rétention : 7 ans (conformité comptable)
├─ Quantité : 7 backups
└─ Usage : Archives légales/audit

┌─────────────────────────────────────────┐
│ TOTAL POINTS RESTAURATION : 7+4+12+7=30 │
└─────────────────────────────────────────┘
```

**🔹 Obligations légales par secteur** :

| Secteur | Réglementation | Durée conservation | Données concernées | GFS recommandé |
|---------|---------------|-------------------|-------------------|----------------|
| **Comptabilité** | Code commerce (France) | **10 ans** | Factures, bilans, pièces comptables | 7-4-12-10 |
| **Santé** | Code santé publique | **20 ans** | Dossiers médicaux | 7-4-12-20 |
| **Banque** | AMF, ACPR | **5-10 ans** | Transactions, contrats | 7-4-60-10 |
| **Assurance** | Code assurances | **10 ans** | Contrats, sinistres | 7-4-12-10 |
| **Paie/RH** | Code du travail | **5 ans** | Bulletins salaires, contrats | 7-4-12-5 |
| **Marchés publics** | Code marchés publics | **10 ans** | Contrats, marchés | 7-4-12-10 |
| **Données personnelles** | RGPD | **Selon finalité** | Données clients/employés | 7-4-12-X |

**⚙️ Configuration Veeam GFS étendu** :

```powershell
# Configuration GFS 7-4-12-7 (conformité standard)

$Job = Get-VBRJob -Name "Backup-Comptabilite"

# Activer tous niveaux GFS
Set-VBRJobOptions -Job $Job `
    -GFSWeekly:$true `
    -GFSWeeklyKeep 4 `
    -GFSMonthly:$true `
    -GFSMonthlyKeep 12 `
    -GFSYearly:$true `
    -GFSYearlyKeep 7

# Vérifier configuration
Get-VBRJobOptions -Job $Job | Select-Object GFS*

# Résultat :
# GFSWeekly          : True
# GFSWeeklyKeep      : 4
# GFSMonthly         : True
# GFSMonthlyKeep     : 12
# GFSYearly          : True
# GFSYearlyKeep      : 7

# Politique quotidienne (via Retention Policy)
Set-VBRJobOptions -Job $Job `
    -BackupRetentionPolicy Days `
    -BackupRetentionPolicyValue 7

# Lister tous restore points avec type GFS
Get-VBRRestorePoint | Where-Object {$_.IsGFS} | 
    Select-Object VMName, CreationTime, 
        @{N="Type";E={
            switch ($_.GFSFlags) {
                "Weekly" {"Hebdomadaire"}
                "Monthly" {"Mensuel"}
                "Yearly" {"Annuel"}
                default {"Quotidien"}
            }
        }},
        @{N="Expiration";E={$_.ExpirationDate}}

# Output exemple :
# VMName          CreationTime         Type          Expiration
# ------          ------------         ----          ----------
# SRV-COMPTA      19/12/2025 02:00    Quotidien     26/12/2025
# SRV-COMPTA      15/12/2025 02:00    Hebdomadaire  12/01/2026
# SRV-COMPTA      01/12/2025 02:00    Mensuel       01/12/2026
# SRV-COMPTA      31/12/2024 02:00    Annuel        31/12/2031
```

**🔹 Calcul stockage nécessaire** :

```
💾 EXEMPLE : Serveur ERP 1 TB avec GFS 7-4-12-7

MÉTHODE 1 : Backups complets uniquement (non optimisé)
├─ Quotidiens : 7 × 1 TB = 7 TB
├─ Hebdomadaires : 4 × 1 TB = 4 TB
├─ Mensuels : 12 × 1 TB = 12 TB
├─ Annuels : 7 × 1 TB = 7 TB
└─ TOTAL : 30 TB (⚠️ Coûteux)

MÉTHODE 2 : Incrémentaux + Synthétiques (optimisé)
├─ 1 Full initial : 1 TB
├─ Incrémentaux quotidiens (2% change/jour) :
│   └─ 7 jours × 20 GB = 140 GB
├─ Synthétiques hebdomadaires : 4 × 1 TB = 4 TB
├─ Mensuels : 12 × 1 TB = 12 TB
├─ Annuels : 7 × 1 TB = 7 TB
└─ TOTAL : ~24,1 TB (20% économie)

MÉTHODE 3 : Déduplication + Compression (très optimisé)
├─ Ratio dédup : 10:1 (VMs similaires)
├─ Ratio compression : 2:1 (gzip level 6)
├─ Calcul : 24,1 TB / 10 / 2 = 1,2 TB
└─ TOTAL : ~1,2 TB (95% économie !)

🎯 Recommandation : Méthode 3 (Veeam avec dédup + compression)
```

**🔹 Politique de déplacement (tiering)** :

```
🗄️ ARCHIVAGE MULTI-TIERS (optimisation coûts) :

TIER 1 : QUOTIDIENS + HEBDOMADAIRES (chaud)
├─ Support : SSD/NAS (accès rapide)
├─ Données : 7 jours + 4 semaines = 11 backups
├─ Coût : 200€/TB/an
├─ RTO : < 1 heure
└─ Exemple : 11 TB × 200€ = 2200€/an

TIER 2 : MENSUELS (tiède)
├─ Support : HDD/S3 Standard
├─ Données : 12 mois = 12 backups
├─ Coût : 50€/TB/an
├─ RTO : 2-4 heures
└─ Exemple : 12 TB × 50€ = 600€/an

TIER 3 : ANNUELS (froid)
├─ Support : LTO/S3 Glacier Deep Archive
├─ Données : 7 ans = 7 backups
├─ Coût : 12€/TB/an
├─ RTO : 12-48 heures
└─ Exemple : 7 TB × 12€ = 84€/an

COÛT TOTAL ANNUEL : 2200 + 600 + 84 = 2884€
VS Tout SSD : 30 TB × 200€ = 6000€
🎯 ÉCONOMIE : 52% grâce tiering intelligent
```

**⚙️ Configuration AWS S3 Lifecycle (tiering automatique)** :

```json
{
  "Rules": [
    {
      "Id": "GFS-Lifecycle-Policy",
      "Status": "Enabled",
      "Transitions": [
        {
          "Days": 7,
          "StorageClass": "STANDARD_IA"
        },
        {
          "Days": 30,
          "StorageClass": "GLACIER_IR"
        },
        {
          "Days": 365,
          "StorageClass": "DEEP_ARCHIVE"
        }
      ],
      "NoncurrentVersionTransitions": [
        {
          "NoncurrentDays": 7,
          "StorageClass": "GLACIER"
        }
      ],
      "Expiration": {
        "Days": 2555
      }
    }
  ]
}
```

```bash
# Appliquer lifecycle policy
aws s3api put-bucket-lifecycle-configuration \
    --bucket backup-archives-comptabilite \
    --lifecycle-configuration file://lifecycle-gfs.json

# Vérifier politique
aws s3api get-bucket-lifecycle-configuration \
    --bucket backup-archives-comptabilite

# Résultat :
# 0-7 jours : S3 Standard (accès fréquent)
# 7-30 jours : S3 Standard-IA (accès peu fréquent)
# 30-365 jours : S3 Glacier Instant Retrieval
# 365+ jours : S3 Glacier Deep Archive (< 1€/TB/mois)
# Expiration : 2555 jours (7 ans)
```

**🔹 Documentation conformité** :

```markdown
📋 REGISTRE BACKUPS (conformité audit)

Obligatoire pour prouver conformité lors audits/contrôles.

## Politique de Sauvegarde Entreprise XYZ

### 1. Périmètre
- Systèmes : ERP, Compta, Paie, Fichiers partagés
- Données : Factures, contrats, bulletins salaire
- Criticité : CRITIQUE (activité métier)

### 2. Fréquences
- **Quotidien** : Lundi-Vendredi 23h00
- **Hebdomadaire** : Dimanche 01h00
- **Mensuel** : Dernier dimanche du mois 01h00
- **Annuel** : 31 décembre 01h00

### 3. Rétentions (GFS 7-4-12-7)
- Quotidiens : 7 jours
- Hebdomadaires : 4 semaines
- Mensuels : 12 mois
- Annuels : 7 ans (obligation légale comptabilité)

### 4. Supports
- Tier 1 (0-30j) : NAS Synology DS1821+
- Tier 2 (30-365j) : AWS S3 Standard-IA
- Tier 3 (365j+) : AWS S3 Glacier Deep Archive

### 5. Localisation
- Principal : Paris Datacenter
- Secondaire : Lyon Datacenter (réplication)
- Tertiaire : AWS eu-west-1 (Ireland)

### 6. Chiffrement
- Algorithme : AES-256
- Gestion clés : AWS KMS + Coffre-fort (backup clé)

### 7. Tests restauration
- Mensuel : Systèmes critiques (ERP, Compta)
- Trimestriel : Tous systèmes

### 8. Responsable
- DPO : Jean MARTIN (jmartin@xyz.fr)
- Validé le : 01/01/2025
- Révision : Annuelle

### 9. Base légale
- Code de commerce : Art. L123-22 (10 ans)
- Code du travail : Art. L3243-4 (5 ans bulletins)
- RGPD : Art. 5.1.e (limitation conservation)
```

**🔹 Audit et traçabilité** :

```powershell
# Rapport conformité GFS annuel

$ReportPath = "C:\Audits\GFS-Compliance-$(Get-Date -Format 'yyyy').pdf"

# Récupérer tous restore points
$AllRestorePoints = Get-VBRRestorePoint | 
    Where-Object {$_.CreationTime -gt (Get-Date).AddYears(-7)}

# Grouper par type GFS
$Report = $AllRestorePoints | Group-Object -Property {
    if ($_.CreationTime -gt (Get-Date).AddDays(-7)) {"Quotidien"}
    elseif ($_.CreationTime -gt (Get-Date).AddDays(-30)) {"Hebdomadaire"}
    elseif ($_.CreationTime -gt (Get-Date).AddYears(-1)) {"Mensuel"}
    else {"Annuel"}
} | Select-Object @{N="Type";E={$_.Name}}, 
                  @{N="Quantité";E={$_.Count}},
                  @{N="Plus ancien";E={($_.Group | Sort-Object CreationTime)[0].CreationTime}},
                  @{N="Plus récent";E={($_.Group | Sort-Object CreationTime -Descending)[0].CreationTime}}

$Report | Format-Table -AutoSize

# Vérifier conformité objectifs
$Compliance = @{
    Quotidien = ($Report | Where-Object {$_.Type -eq "Quotidien"}).Quantité -ge 7
    Hebdomadaire = ($Report | Where-Object {$_.Type -eq "Hebdomadaire"}).Quantité -ge 4
    Mensuel = ($Report | Where-Object {$_.Type -eq "Mensuel"}).Quantité -ge 12
    Annuel = ($Report | Where-Object {$_.Type -eq "Annuel"}).Quantité -ge 7
}

if ($Compliance.Values -contains $false) {
    Write-Host "⚠️ ALERTE CONFORMITÉ : Objectifs GFS non atteints !" -ForegroundColor Red
    $Compliance
} else {
    Write-Host "✅ Conformité GFS respectée" -ForegroundColor Green
}

# Export PDF (via HTML conversion)
$HTML = $Report | ConvertTo-Html -Title "Rapport Conformité GFS $(Get-Date -Format 'yyyy')"
$HTML | Out-File "$ReportPath.html"

# Envoyer au DPO
Send-MailMessage `
    -To "dpo@societe.fr" `
    -From "backup@societe.fr" `
    -Subject "Rapport Conformité Backups $(Get-Date -Format 'yyyy')" `
    -Body "Veuillez trouver en PJ le rapport annuel de conformité GFS." `
    -Attachments "$ReportPath.html"
```

**💡 Points clés conformité** :
- Durées légales varient selon secteur (5-20 ans)
- GFS 7-4-12-7 = Standard pour comptabilité (10 ans)
- Tiering automatique = Économies massives
- Documentation + audit = Preuve conformité

**🎯 Statistiques** : 68% entreprises non conformes durées légales (source: CNIL 2024). Sanctions RGPD jusqu'à 20M€ ou 4% CA mondial.

---

## RÉPONSES : 1B, 2B, 3A, 4B, 5B, 6A, 7B, 8B, 9B, 10C, 11B, 12B, 13B, 14B, 15A, 16B, 17C, 18B, 19B, 20A

**Note /20** : Score / 1

---

**FORMULES CLÉS** :
- **RPO** = Perte données acceptable
- **RTO** = Temps restauration acceptable
- **Complète** = Tout
- **Différentielle** = Modif depuis dernière Complète
- **Incrémentale** = Modif depuis dernière sauvegarde
