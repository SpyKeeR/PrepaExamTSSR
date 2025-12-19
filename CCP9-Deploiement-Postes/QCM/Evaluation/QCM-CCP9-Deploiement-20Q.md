# QCM CCP9 - DÉPLOIEMENT POSTES

**Certification TSSR** - CCP9 : Déploiement et maintenance postes  
**20 questions**

---

### Q1 : Sysprep utilité
Que fait Sysprep /generalize ?
- [ ] A) Compresse image
- [ ] B) Supprime SID et infos machine spécifiques 
- [ ] C) Chiffre disque
- [ ] D) Crée partition

---

### Q2 : WDS prérequis
Prérequis WDS ?
- [ ] A) Active Directory + DHCP + NTFS 
- [ ] B) Internet uniquement
- [ ] C) VMware vSphere
- [ ] D) Linux serveur

---

### Q3 : PXE Boot
Que signifie PXE ?
- [ ] A) Pre-eXecution Environment (boot réseau) 
- [ ] B) Partition eXtended Environment
- [ ] C) Post eXecution Engine
- [ ] D) Protocol eXchange Ethernet

---

### Q4 : Image WIM
Format image Windows Deployment ?
- [ ] A) ISO
- [ ] B) WIM (Windows Imaging) 
- [ ] C) VMDK
- [ ] D) VHD

---

### Q5 : MDT vs WDS
Avantage MDT sur WDS seul ?
- [ ] A) Plus rapide
- [ ] B) Automatisation complète (Task Sequences, drivers, apps) 
- [ ] C) Gratuit
- [ ] D) Boot PXE

---

### Q6 : Unattend.xml
Utilité fichier unattend.xml ?
- [ ] A) Pilotes réseau
- [ ] B) Automatise réponses installation Windows 
- [ ] C) Chiffrement données
- [ ] D) Compression image

---

### Q7 : DHCP options WDS
Options DHCP pour PXE ?
- [ ] A) 66 (serveur TFTP) + 67 (fichier boot) 
- [ ] B) 3 (gateway) + 6 (DNS)
- [ ] C) 15 (domain) + 44 (WINS)
- [ ] D) 82 (relay)

---

### Q8 : Clonezilla mode
Mode Clonezilla pour déployer 1 image → 50 PCs simultanément ?
- [ ] A) Device-device
- [ ] B) Multicast 
- [ ] C) Unicast
- [ ] D) Broadcast

---

### Q9 : DISM commande
Monter image WIM en lecture/écriture ?
- [ ] A) `Dism /Mount-Wim /WimFile:x.wim /Index:1 /MountDir:C:\mount` 
- [ ] B) `Dism /Open-Image`
- [ ] C) `Mount-WindowsImage`
- [ ] D) `wiminfo`

---

### Q10 : Activation KMS
Différence KMS vs MAK ?
- [ ] A) KMS = serveur local renouvelle 180j, MAK = activations limitées Internet 
- [ ] B) Aucune
- [ ] C) KMS payant, MAK gratuit
- [ ] D) KMS home, MAK entreprise

---

### Q11 : Join domaine auto
Commande PowerShell joindre domaine ?
- [ ] A) `Join-Domain`
- [ ] B) `Add-Computer -DomainName "entreprise.local"` 
- [ ] C) `Set-Domain`
- [ ] D) `Connect-AD`

---

### Q12 : Drivers injection
Injecter drivers dans image offline ?
- [ ] A) `Dism /Add-Driver /Driver:C:\drivers /Recurse` 
- [ ] B) `Install-Driver`
- [ ] C) `Import-Drivers`
- [ ] D) Impossible offline

---

### Q13 : WinPE utilité
Windows PE (Preinstallation Environment) sert à ?
- [ ] A) Environnement minimal boot réseau/USB 
- [ ] B) Antivirus
- [ ] C) Navigateur web
- [ ] D) Suite bureautique

---

### Q14 : Multicast avantage
Avantage déploiement multicast ?
- [ ] A) Plus sécurisé
- [ ] B) 1 flux réseau pour N postes (économie BP) 
- [ ] C) Plus rapide
- [ ] D) Pas besoin DHCP

---

### Q15 : Task Sequence MDT
Que contient Task Sequence ?
- [ ] A) Drivers uniquement
- [ ] B) Séquence automatisée (partition + OS + drivers + apps + config) 
- [ ] C) Fichier ISO
- [ ] D) Clé produit

---

### Q16 : Chocolatey
Qu'est-ce que Chocolatey ?
- [ ] A) Gestionnaire paquets Windows (apps) 
- [ ] B) Logiciel sauvegarde
- [ ] C) Antivirus
- [ ] D) Navigateur

---

### Q17 : GPO Software Installation
Format requis déploiement logiciel GPO ?
- [ ] A) .EXE
- [ ] B) .MSI 
- [ ] C) .ZIP
- [ ] D) .BAT

---

### Q18 : Image optimisation
Réduire taille image WIM ?
- [ ] A) `Dism /Export-Image /Compress:max` 
- [ ] B) `Dism /Shrink`
- [ ] C) `Compact-Image`
- [ ] D) ZIP

---

### Q19 : Nommage standard
Standard nommage PC entreprise ?
- [ ] A) PC001, PC002
- [ ] B) SITE-DEPT-TYPE-NUM (ex: PAR-RH-DT-001) 
- [ ] C) Nom utilisateur
- [ ] D) Aléatoire

---

### Q20 : Capture image WDS
Étapes capture image référence ?
- [ ] A) Sysprep → Boot WinPE → Capture → Import WDS 
- [ ] B) Copier C:\ directement
- [ ] C) ZIP dossier Windows
- [ ] D) Snapshot VM
