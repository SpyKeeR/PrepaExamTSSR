# CORRECTIONS - QUESTIONS OUVERTES CCP9 - DÉPLOIEMENT POSTES

**Certification TSSR** - CCP9 : Déploiement postes de travail  
**6 questions - Corrections détaillées**

---

## ✔️ Question 1 : Workflow déploiement complet (12 pts)

**Énoncé** : Décrivez étapes complètes déploiement 100 postes Windows 10 identiques avec domaine AD.

**Réponse modèle** (12 pts) :

**1. Préparation poste référence** (3 pts) :
```
- Installer Windows 10 propre
- Installer applications standard (Office, Chrome, 7zip)
- Configurer paramètres (fond écran, GPO locales)
- Exécuter Sysprep /generalize /oobe /shutdown
```

**2. Capture image** (2 pts) :
```
- Boot WinPE via PXE
- Capture disque C: → fichier .WIM
- Upload vers WDS : C:\RemoteInstall\Images\
```

**3. Configuration WDS** (2 pts) :
```
- Import image install
- DHCP options 66/67 configurées
- Multicast transmission créée (100 postes simultanés)
```

**4. Déploiement** (3 pts) :
```
- Boot 100 postes PXE (F12)
- Sélection image Windows 10
- Déploiement multicast automatique
- Join domaine via unattend.xml
```

**5. Post-config** (2 pts) :
```
- GPO appliquent : imprimantes, lecteurs réseau, applications
- Activation KMS automatique
- Tests : réseau, applications, domaine
```

---

## ✔️ Question 2 : Sysprep et Unattend.xml (10 pts)

**Énoncé** : Créez fichier unattend.xml pour : nom PC "PC-%RAND:4%", join domaine "entreprise.local", admin MDP "P@ss123".

**Réponse modèle** (10 pts) :

```xml
<?xml version="1.0" encoding="utf-8"?>
<unattend xmlns="urn:schemas-microsoft-com:unattend">
  
  <!-- Partitionnement -->
  <settings pass="windowsPE">
    <component name="Microsoft-Windows-Setup">
      <DiskConfiguration>
        <Disk wcm:action="add">
          <CreatePartitions>
            <CreatePartition wcm:action="add">
              <Order>1</Order>
              <Size>100</Size>
              <Type>Primary</Type>
            </CreatePartition>
            <CreatePartition wcm:action="add">
              <Extend>true</Extend>
              <Order>2</Order>
              <Type>Primary</Type>
            </CreatePartition>
          </CreatePartitions>
        </Disk>
      </DiskConfiguration>
    </component>
  </settings>

  <!-- Nom PC + Domaine -->
  <settings pass="specialize">
    <component name="Microsoft-Windows-Shell-Setup">
      <ComputerName>PC-%RAND:4%</ComputerName>
    </component>
    
    <component name="Microsoft-Windows-UnattendedJoin">
      <Identification>
        <Credentials>
          <Domain>entreprise.local</Domain>
          <Username>admin_deploy</Username>
          <Password>P@ss123</Password>
        </Credentials>
        <JoinDomain>entreprise.local</JoinDomain>
      </Identification>
    </component>
  </settings>

  <!-- OOBE -->
  <settings pass="oobeSystem">
    <component name="Microsoft-Windows-Shell-Setup">
      <OOBE>
        <HideEULAPage>true</HideEULAPage>
        <ProtectYourPC>1</ProtectYourPC>
        <NetworkLocation>Work</NetworkLocation>
      </OOBE>
      <UserAccounts>
        <AdministratorPassword>
          <Value>P@ss123</Value>
          <PlainText>true</PlainText>
        </AdministratorPassword>
      </UserAccounts>
    </component>
  </settings>
  
</unattend>
```

**Points clés** : `%RAND:4%` = 4 chiffres aléatoires (PC-7842), join domaine auto.

---

## ✔️ Question 3 : DISM Drivers injection (8 pts)

**Énoncé** : Injectez drivers réseau Intel depuis C:\Drivers\Intel dans image install.wim (index 1).

**Réponse modèle** (8 pts) :

```powershell
# 1. Monter image (3 pts)
Dism /Mount-Wim /WimFile:"C:\Images\install.wim" /Index:1 /MountDir:"C:\mount"

# 2. Injecter drivers (3 pts)
Dism /Image:"C:\mount" /Add-Driver /Driver:"C:\Drivers\Intel" /Recurse

# Vérifier drivers ajoutés
Dism /Image:"C:\mount" /Get-Drivers

# 3. Démonter et sauvegarder (2 pts)
Dism /Unmount-Wim /MountDir:"C:\mount" /Commit
```

**Explications** :
- `/Recurse` : Scan sous-dossiers
- `/Commit` : Sauvegarde modifs (sinon `/Discard`)
- Index 1 = généralement Windows 10 Pro

---

## ✔️ Question 4 : MDT Task Sequence (10 pts)

**Énoncé** : Créez Task Sequence MDT pour : partition 100GB C:, install Win10, drivers, Office 365, join domaine.

**Réponse modèle** (10 pts) :

**Étapes MDT Deployment Workbench** :

**1. Import OS** (2 pts) :
```
Operating Systems → Import → Full set of source files
Source: D:\Sources\Windows10
```

**2. Import Applications** (2 pts) :
```
Applications → New Application
  Type: Application with source files
  Source: \\SRV\Apps\Office365
  Command: setup.exe /configure config.xml
```

**3. Create Task Sequence** (6 pts) :
```
Task Sequences → New Task Sequence
  ID: W10_Deploy
  Name: Windows 10 Standard Deploy
  Template: Standard Client Task Sequence
  OS: Windows 10 Pro x64
  
Customize Sequence:
  Preinstall:
    └─ Format and Partition (100GB C:, NTFS)
  
  Install:
    └─ Install Operating System
    └─ Apply Windows Updates
  
  Postinstall:
    └─ Install Applications
        └─ Install Office 365
    └─ Inject Drivers (Selection Profile: All Drivers)
    └─ Windows Update (Post-App Installation)
    └─ Recover from Domain (join: entreprise.local)
    └─ Activate Windows (KMS: kms.entreprise.local)
```

**Update Deployment Share** : Génère boot files LiteTouch.

---

## ✔️ Question 5 : WDS Multicast (8 pts)

**Énoncé** : Configurez transmission multicast WDS pour déployer 50 postes simultanément. Quelle économie bande passante ?

**Réponse modèle** (8 pts) :

**Configuration WDS** (5 pts) :
```
WDS Console → Multicast Transmissions → Create Multicast Transmission

  Name: Deploy_Win10_Multicast
  Image Group: ImageGroup1
  Image Name: Windows 10 Pro
  
  Type:
    ☑ Auto-Cast (démarre dès clients connectés)
    Min clients: 5
    Start delay: 2 minutes
  
  Transport Settings:
    Network profile: Custom
    Transfer rate: 50 Mbps
    Multicast IP range: 239.0.0.1 - 239.255.255.255
```

**Économie bande passante** (3 pts) :

**Unicast** (sans multicast) :
- 50 postes × 5 GB image = 250 GB trafic réseau
- 50 flux simultanés = saturation réseau 1 Gbps

**Multicast** :
- 1 flux multicast × 5 GB = 5 GB trafic
- **Économie = 98%** (245 GB économisés)
- Temps déploiement identique pour 1 ou 50 postes

---

## ✔️ Question 6 : Activation KMS (7 pts)

**Énoncé** : Configurez serveur KMS puis activez 10 clients Windows 10. Commandes ?

**Réponse modèle** (7 pts) :

**Serveur KMS** (3 pts) :
```cmd
# Installer clé KMS Host
slmgr /ipk <KMS_Host_Key_Volume_License>

# Activer serveur
slmgr /ato

# Publier dans DNS (auto si AD)
slmgr /sdns

# Vérifier statut
slmgr /dlv
```

**Clients** (4 pts) :
```cmd
# Sur chaque client (ou GPO/script)

# Installer clé GVLK (Generic Volume License Key)
slmgr /ipk W269N-WFGWX-YVC9B-4J6C9-T83GX

# Pointer vers KMS
slmgr /skms kms.entreprise.local:1688

# Activer
slmgr /ato

# Vérifier
slmgr /dli
```

**Renouvellement** : Automatique tous les 180 jours (client contacte KMS).

**Prérequis KMS** : Minimum 25 clients Windows (ou 5 serveurs) pour activation.

---

## BARÈME : 55 POINTS

**Note /20** : Total / 2.75

---

**CONCEPTS CLÉS** :
- **WDS** : Déploiement réseau basique
- **MDT** : Automatisation avancée
- **DISM** : Manipulation images offline
- **Sysprep** : Généralisation
- **Unattend.xml** : Automatisation install
- **KMS** : Activation en volume

