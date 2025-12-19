# FICHE REVISION CCP9 - DÉPLOIEMENT POSTES

**Certification TSSR** - CCP9 : Déploiement et maintenance postes de travail

---

## 1. WDS (Windows Deployment Services)

### Rôle serveur
Déploiement OS Windows via réseau (PXE boot)

**Installation** :
```powershell
Install-WindowsFeature WDS -IncludeManagementTools
```

### Prérequis
- Active Directory configuré
- Serveur DHCP actif (options 66/67)
- Partition NTFS pour images

### Configuration DHCP
**Option 66** : IP serveur WDS  
**Option 67** : Fichier boot `boot\x64\wdsnbp.com`

---

## 2. IMAGES SYSTÈME

### Sysprep (Généralisation)
Prépare image Windows (supprime infos spécifiques machine)

```cmd
C:\Windows\System32\Sysprep\sysprep.exe /generalize /oobe /shutdown
```

**Paramètres** :
- `/generalize` : Supprime SID, nom machine
- `/oobe` : Redémarre sur écran bienvenue
- `/shutdown` : Éteint après (pour capture)

### Types images WDS

**Image Boot (WinPE)** :
- Démarre poste par réseau
- Capture/applique images install

**Image Install** :
- OS Windows complet
- Capturé après Sysprep
- Format .WIM (Windows Imaging)

---

## 3. MDT (Microsoft Deployment Toolkit)

### Avantages vs WDS
- Automatisation complète (zéro-touch)
- Task sequences personnalisables
- Drivers, applications, configurations
- Intégration WSUS, AD

### Workflow MDT
```
1. Import OS + Drivers + Apps
2. Create Task Sequence
3. Update Deployment Share
4. Boot PXE ou clé USB
5. Déploiement automatisé
```

---

## 4. FICHIERS RÉPONSES (UNATTEND.XML)

### Utilité
Automatise installation Windows (nom, domaine, clé produit, partitions)

**Outil** : Windows System Image Manager (WSIM)

**Sections clés** :
```xml
<settings pass="windowsPE">
  <!-- Partitionnement disque -->
</settings>

<settings pass="specialize">
  <ComputerName>PC-%RAND:4%</ComputerName>
  <TimeZone>Romance Standard Time</TimeZone>
</settings>

<settings pass="oobeSystem">
  <UserAccounts>
    <AdministratorPassword>P@ssw0rd</AdministratorPassword>
  </UserAccounts>
  <AutoLogon>
    <Enabled>true</Enabled>
  </AutoLogon>
</settings>
```

---

## 5. CLONAGE DISQUE

### Clonezilla
Outil libre clonage disque/partition

**Modes** :
- **device-device** : Clonage direct disque → disque
- **device-image** : Sauvegarde disque → image
- **multicast** : 1 image → N postes simultanés

**Commande exemple** :
```bash
ocs-sr -g auto -e1 auto -e2 -r -j2 -c -p true savedisk image_pc /dev/sda
```

### Acronis, Norton Ghost
Solutions commerciales clonage (interface graphique)

---

## 6. PXE BOOT

### Principe
**Preboot eXecution Environment** : Boot réseau sans disque local

**Séquence** :
```
1. PC allume, cherche DHCP
2. DHCP répond : IP + serveur TFTP (WDS)
3. TFTP télécharge boot.wim (WinPE)
4. WinPE charge, liste images disponibles
5. Sélection image → Déploiement
```

**BIOS config** : Boot Order → Network/PXE en premier

---

## 7. DRIVERS INJECTION

### Problème
Image générique ne contient pas drivers spécifiques matériels

### Solutions

**MDT** : Import drivers par modèle
```
Deployment Workbench → Drivers
→ Import Drivers (dossier constructeur)
→ Task Sequence applique selon modèle détecté
```

**DISM** : Injection offline
```powershell
# Monter image
Dism /Mount-Wim /WimFile:C:\install.wim /Index:1 /MountDir:C:\mount

# Ajouter drivers
Dism /Image:C:\mount /Add-Driver /Driver:C:\drivers /Recurse

# Démonter et sauvegarder
Dism /Unmount-Wim /MountDir:C:\mount /Commit
```

---

## 8. APPLICATIONS POST-INSTALL

### GPO Software Installation
```
Computer Config → Policies → Software Settings → Software Installation
→ New → Package
   Path: \\SRV-FILE01\Apps\7zip.msi
   Deployment: Assigned (install auto)
```

**Formats supportés** : .MSI (Windows Installer)

### Scripts post-déploiement
```powershell
# Script dans Task Sequence MDT
Install-Package -Name 7zip -Force
Install-Package -Name GoogleChrome -Force
Install-Package -Name AdobeReader -Force
```

### Chocolatey (gestionnaire paquets)
```powershell
# Installer Chocolatey
Set-ExecutionPolicy Bypass -Force
iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))

# Installer apps
choco install googlechrome firefox 7zip -y
```

---

## 9. CONFIGURATION AUTOMATIQUE

### Join domaine automatique
**Unattend.xml** :
```xml
<settings pass="specialize">
  <JoinDomain>
    <Credentials>
      <Domain>entreprise.local</Domain>
      <Username>admin_deploy</Username>
      <Password>P@ssw0rd</Password>
    </Credentials>
    <JoinDomain>entreprise.local</JoinDomain>
  </JoinDomain>
</settings>
```

**PowerShell** :
```powershell
Add-Computer -DomainName "entreprise.local" -Credential (Get-Credential) -Restart
```

### Rename Computer
```powershell
Rename-Computer -NewName "PC-RH-01" -Restart
```

---

## 10. ACTIVATION WINDOWS

### KMS (Key Management Service)
Activation volume entreprise (180j renouvelables)

**Serveur KMS** :
```cmd
slmgr /ipk <KMS_Host_Key>
slmgr /ato
```

**Client** :
```cmd
slmgr /ipk <Volume_License_Key>
slmgr /skms kms.entreprise.local:1688
slmgr /ato
```

### MAK (Multiple Activation Key)
Clé à activations limitées (ex: 500 activations)

---

## 11. OUTILS DIAGNOSTIC

### Windows PE (WinPE)
Environnement minimal boot réseau/USB

**Création USB WinPE** :
```cmd
copype amd64 C:\WinPE_amd64
MakeWinPEMedia /UFD C:\WinPE_amd64 E:
```

### DISM commandes
```powershell
# Info image
Dism /Get-WimInfo /WimFile:install.wim

# Liste images
Dism /Get-ImageInfo /ImageFile:install.wim

# Appliquer image
Dism /Apply-Image /ImageFile:install.wim /Index:1 /ApplyDir:C:\
```

---

## 12. MAINTENANCE IMAGES

### Mise à jour offline
```powershell
# Monter
Dism /Mount-Wim /WimFile:install.wim /Index:1 /MountDir:C:\mount

# Injecter MAJ
Dism /Image:C:\mount /Add-Package /PackagePath:C:\updates\

# Nettoyer composants
Dism /Image:C:\mount /Cleanup-Image /StartComponentCleanup

# Sauvegarder
Dism /Unmount-Wim /MountDir:C:\mount /Commit
```

### Optimisation taille
```powershell
# Export (recompresse)
Dism /Export-Image /SourceImageFile:install.wim /SourceIndex:1 `
  /DestinationImageFile:install-optimized.wim /Compress:max
```

---

## 13. DÉPLOIEMENT MULTICAST

### WDS Multicast
1 serveur → N postes simultanément (économie bande passante)

**Config WDS** :
```
Multicast Transmissions → Create Multicast Transmission
  Transmission name: Deploy_Win10
  Image: Windows 10 Pro
  Type: Auto-Cast (démarre automatiquement)
```

**Avantages** : 100 postes = 1 flux réseau (vs 100 flux unicast)

---

## 14. BONNES PRATIQUES

### Nommage postes
```
Standard : SITE-DEPT-TYPE-NUM
Exemple : PAR-RH-DT-001
  PAR = Paris
  RH = Service
  DT = Desktop
  001 = Numéro séquentiel
```

### Documentation
- ✅ Inventaire matériel (CPU, RAM, HDD)
- ✅ Drivers spécifiques modèles
- ✅ Applications standard par profil
- ✅ Changelog images (versions, dates)

### Tests
- ✅ VM test avant déploiement masse
- ✅ Vérifier drivers (réseau, vidéo)
- ✅ Join domaine automatique
- ✅ Applications installées

---

## 15. SCÉNARIOS PRATIQUES

### Scénario 1 : Déployer 50 postes identiques
**Solution** : WDS multicast + image Sysprep

### Scénario 2 : 3 profils (RH, IT, Compta)
**Solution** : MDT Task Sequences + Applications sélectives

### Scénario 3 : Mise à jour image mensuelle
**Solution** : DISM /Mount + /Add-Package + /Cleanup + /Commit

### Scénario 4 : Drivers multiples constructeurs
**Solution** : MDT Selection Profiles par modèle

---

## COMMANDES ESSENTIELLES

```powershell
# Sysprep
sysprep /generalize /oobe /shutdown

# DISM
Dism /Mount-Wim /WimFile:x.wim /Index:1 /MountDir:C:\mount
Dism /Image:C:\mount /Add-Driver /Driver:C:\drivers /Recurse
Dism /Unmount-Wim /MountDir:C:\mount /Commit

# Domaine
Add-Computer -DomainName "entreprise.local" -Restart

# Activation
slmgr /ipk <clé>
slmgr /ato

# WDS
wdsutil /Add-Image /ImageFile:install.wim /ImageType:Install
```

---

**WORKFLOW COMPLET** :
```
1. Poste référence : Install Windows + Apps + Sysprep
2. Capturer image : Boot WinPE → Capture vers WDS
3. Import MDT : OS + Drivers + Apps
4. Task Sequence : Partitions + OS + Drivers + Apps + Domain Join
5. Déploiement : PXE boot → Auto-install
6. Tests : Vérif drivers, apps, domaine
7. Production : Multicast 50+ postes
```

**Date révision** : 12 novembre 2025