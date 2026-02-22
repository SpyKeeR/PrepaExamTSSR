# FICHE REVISION CCP2 - WINDOWS SERVER / ACTIVE DIRECTORY / POWERSHELL

**Certification TSSR** - CCP2 : Exploiter des serveurs Windows et un domaine ActiveDirectory

---

## 1. ACTIVE DIRECTORY (AD)

### Structure hiérarchique

```
FORÊT (Forest)
  └── DOMAINE (Domain)
        └── UNITÉS ORGANISATIONNELLES (OU)
              └── OBJETS (Users, Computers, Groups)
```

**Forêt** : Limite sécurité, schéma AD commun, relations d'approbation  
**Domaine** : Limite administrative, réplication, politiques  
**OU** : Conteneurs personnalisables, application GPO  
**Objets** : Utilisateurs, ordinateurs, groupes

### Contrôleur de domaine (DC)

**Rôles FSMO** (5 rôles) :
- **Forêt** (2) : Maître de schéma, Maître d'attribution de noms de domaine
- **Domaine** (3) : Émulateur PDC, Maître RID, Maître d'infrastructure

**Commandes** :
```powershell
# Installer AD DS
Install-WindowsFeature AD-Domain-Services -IncludeManagementTools

# Promouvoir serveur en DC
Install-ADDSForest -DomainName "entreprise.local"

# Vérifier rôles FSMO
Get-ADDomain | Select-Object PDCEmulator, RIDMaster, InfrastructureMaster
```

---

## 2. GESTION UTILISATEURS ET GROUPES

### Créer utilisateur

**PowerShell** :
```powershell
New-ADUser -Name "Jean Dupont" `
  -GivenName "Jean" `
  -Surname "Dupont" `
  -SamAccountName "jdupont" `
  -UserPrincipalName "jdupont@entreprise.local" `
  -Path "OU=Utilisateurs,DC=entreprise,DC=local" `
  -AccountPassword (ConvertTo-SecureString "P@ssw0rd123!" -AsPlainText -Force) `
  -Enabled $true
```

### Créer groupe

```powershell
New-ADGroup -Name "GRP_RH" `
  -GroupScope Global `
  -GroupCategory Security `
  -Path "OU=Groupes,DC=entreprise,DC=local"

# Ajouter membre
Add-ADGroupMember -Identity "GRP_RH" -Members "jdupont"
```

### Recherches utiles

```powershell
# Utilisateurs désactivés
Get-ADUser -Filter {Enabled -eq $false}

# Utilisateurs OU spécifique
Get-ADUser -SearchBase "OU=RH,DC=entreprise,DC=local" -Filter *

# Dernière connexion >90 jours
$date = (Get-Date).AddDays(-90)
Get-ADUser -Filter {LastLogonDate -lt $date} -Properties LastLogonDate
```

---

## 3. STRATÉGIES DE GROUPE (GPO)

### Chemins GPO essentiels

**Configuration ordinateur** :
```
Computer Configuration
  └── Policies
        ├── Software Settings
        ├── Windows Settings
        │     ├── Security Settings (mots de passe, audit, pare-feu)
        │     └── Scripts (démarrage/arrêt)
        └── Administrative Templates
              ├── System (USB, BitLocker, Windows Update)
              └── Network (Wi-Fi, VPN)
```

**Configuration utilisateur** :
```
User Configuration
  └── Policies
        ├── Software Settings
        ├── Windows Settings
        │     ├── Folder Redirection
        │     └── Scripts (ouverture/fermeture session)
        └── Administrative Templates
              ├── Desktop (fond d'écran, widgets)
              └── Start Menu (épingler applications)
```

### GPO courantes

**Bloquer clés USB** :
```
Computer Config → Admin Templates → System → Removable Storage Access
→ All Removable Storage: Deny all access = Enabled
```

**Mappage lecteurs réseau** :
```
User Config → Preferences → Windows Settings → Drive Maps
→ New → Mapped Drive
   Action: Create
   Location: \\SRV-FILE01\Partages
   Drive Letter: P:
```

**Stratégie mots de passe** :
```
Computer Config → Policies → Windows Settings → Security Settings → Account Policies → Password Policy
→ Minimum password length: 12 characters
→ Password must meet complexity requirements: Enabled
→ Maximum password age: 90 days
```

### Commandes GPO

```powershell
# Créer GPO
New-GPO -Name "GPO-Securite-RH"

# Lier GPO à OU
New-GPLink -Name "GPO-Securite-RH" -Target "OU=RH,DC=entreprise,DC=local"

# Forcer application
gpupdate /force

# Rapport GPO utilisateur
gpresult /r
gpresult /h rapport.html
```

---

## 4. DNS WINDOWS SERVER

### Types d'enregistrements

| TYPE | DESCRIPTION | EXEMPLE |
|------|-------------|---------|
| **A** | IPv4 vers nom | intranet.entreprise.local → 192.168.10.30 |
| **AAAA** | IPv6 vers nom | serveur → 2001:db8::1 |
| **CNAME** | Alias | www → intranet |
| **MX** | Serveur mail | mail.entreprise.local priorité 10 |
| **PTR** | Reverse (IP→nom) | 192.168.10.30 → SRV-DC01 |
| **SRV** | Service AD | _ldap._tcp.entreprise.local |

### Commandes DNS

```powershell
# Créer zone principale
Add-DnsServerPrimaryZone -Name "entreprise.local" -ZoneFile "entreprise.local.dns"

# Ajouter enregistrement A
Add-DnsServerResourceRecordA -Name "intranet" `
  -ZoneName "entreprise.local" `
  -IPv4Address "192.168.10.30"

# Ajouter CNAME
Add-DnsServerResourceRecordCName -Name "www" `
  -ZoneName "entreprise.local" `
  -HostNameAlias "intranet.entreprise.local"

# Tests
nslookup intranet.entreprise.local
Resolve-DnsName intranet.entreprise.local
```

---

## 5. PERMISSIONS NTFS vs PARTAGE

### NTFS (local + réseau)

**Permissions** :
- **Lecture** : Voir fichiers/dossiers
- **Écriture** : Créer fichiers
- **Modification** : Lire + Écrire + Supprimer
- **Contrôle total** : Toutes permissions + modifier ACL

**Commandes** :
```powershell
# Voir permissions
Get-Acl "C:\Partages\RH"

# Ajouter permission
$acl = Get-Acl "C:\Partages\RH"
$rule = New-Object System.Security.AccessControl.FileSystemAccessRule(
    "ENTREPRISE\GRP_RH", "Modify", "ContainerInherit,ObjectInherit", "None", "Allow"
)
$acl.SetAccessRule($rule)
Set-Acl "C:\Partages\RH" $acl
```

### Partage SMB (réseau uniquement)

**Permissions** :
- **Lecture** : Lire fichiers
- **Modification** : Lire + Écrire
- **Contrôle total** : Toutes permissions

**Commandes** :
```powershell
# Créer partage
New-SmbShare -Name "RH_Partage" -Path "C:\Partages\RH" -FullAccess "Tout le monde"

# Modifier permissions partage
Grant-SmbShareAccess -Name "RH_Partage" -AccountName "ENTREPRISE\GRP_RH" -AccessRight Full
```

### Règle cumulative

**NTFS Lecture + SMB Contrôle total = Lecture finale** (plus restrictive gagne)

**Bonne pratique** :
- Partage SMB : "Tout le monde" = Contrôle total
- Gérer finesse via NTFS uniquement

---

## 6. POWERSHELL AVANCÉ

### Variables et types

```powershell
$nom = "Dupont"                    # String
$age = 35                          # Int
$actif = $true                     # Boolean
$liste = @("PC01", "PC02")         # Array
$hash = @{Nom="Dupont"; Age=35}    # Hashtable
```

### Boucles

```powershell
# ForEach
$pcs = Get-ADComputer -Filter *
foreach ($pc in $pcs) {
    Write-Host $pc.Name
}

# ForEach-Object (pipeline)
Get-ADUser -Filter * | ForEach-Object { $_.SamAccountName }

# While
$i = 0
while ($i -lt 10) {
    Write-Host $i
    $i++
}
```

### Conditions

```powershell
if ($age -ge 18) {
    Write-Host "Majeur"
} elseif ($age -ge 16) {
    Write-Host "Mineur avec permis"
} else {
    Write-Host "Mineur"
}
```

### Fonctions

```powershell
function Get-DiskSpace {
    param(
        [string]$ComputerName = "localhost"
    )
    
    Get-WmiObject Win32_LogicalDisk -ComputerName $ComputerName | 
        Where-Object {$_.DriveType -eq 3} | 
        Select-Object DeviceID, 
            @{Name="SizeGB";Expression={[math]::Round($_.Size/1GB,2)}},
            @{Name="FreeGB";Expression={[math]::Round($_.FreeSpace/1GB,2)}}
}

# Utilisation
Get-DiskSpace -ComputerName "SRV-FILE01"
```

### Modules AD

```powershell
# Importer module
Import-Module ActiveDirectory

# Commandes essentielles
Get-ADUser -Identity "jdupont"
Get-ADComputer -Filter {OperatingSystem -like "*Server*"}
Get-ADGroup -Identity "GRP_RH"
Get-ADGroupMember -Identity "Domain Admins"
```

---

## 7. SERVICES WINDOWS

### Gestion services

```powershell
# Lister services
Get-Service | Where-Object {$_.Status -eq "Running"}

# Démarrer/Arrêter
Start-Service -Name "Spooler"
Stop-Service -Name "Spooler"
Restart-Service -Name "Spooler"

# Configurer démarrage automatique
Set-Service -Name "Spooler" -StartupType Automatic
```

### Services critiques AD

- **DNS Client** : Résolution noms
- **Netlogon** : Authentification domaine
- **Active Directory Domain Services** : Contrôleur domaine
- **DFS Replication** : Réplication fichiers
- **Windows Time** : Synchronisation horloge (crucial AD)

---

## 8. WINDOWS UPDATE / WSUS

### WSUS (Windows Server Update Services)

**Rôle** : Serveur central distribution mises à jour Windows

**Avantages** :
- Contrôle déploiement mises à jour
- Économie bande passante (1 téléchargement)
- Tests avant production

**GPO WSUS** :
```
Computer Config → Admin Templates → Windows Components → Windows Update
→ Configure Automatic Updates: Enabled
→ Specify intranet Microsoft update service location:
   http://srv-wsus.entreprise.local:8530
```

### Commandes

```powershell
# Forcer recherche mises à jour
Start-WUScan

# Installer mises à jour
Install-WindowsUpdate -AcceptAll -AutoReboot

# Historique
Get-WindowsUpdateLog
```

---

## 9. SAUVEGARDE WINDOWS SERVER

### Windows Server Backup

```powershell
# Installer fonctionnalité
Install-WindowsFeature Windows-Server-Backup

# Sauv egarde complète vers disque externe
wbadmin start backup -backupTarget:E: -include:C: -allCritical -quiet

# Sauvegarde état système (AD, registre, boot)
wbadmin start systemstatebackup -backupTarget:E: -quiet

# Restauration fichiers
wbadmin start recovery -version:12/11/2025-10:00 -itemType:File -items:C:\Data
```

---

## 10. SCRIPTS POWERSHELL UTILES

### Script 1 : Création utilisateurs en masse (CSV)

**users.csv** :
```
Prenom,Nom,Login,OU
Jean,Dupont,jdupont,"OU=RH,DC=entreprise,DC=local"
Marie,Martin,mmartin,"OU=Comptabilite,DC=entreprise,DC=local"
```

**Script** :
```powershell
Import-Csv "users.csv" | ForEach-Object {
    $password = ConvertTo-SecureString "P@ssw0rd123!" -AsPlainText -Force
    
    New-ADUser -Name "$($_.Prenom) $($_.Nom)" `
        -GivenName $_.Prenom `
        -Surname $_.Nom `
        -SamAccountName $_.Login `
        -UserPrincipalName "$($_.Login)@entreprise.local" `
        -Path $_.OU `
        -AccountPassword $password `
        -Enabled $true
    
    Write-Host "Utilisateur $($_.Login) cree" -ForegroundColor Green
}
```

### Script 2 : Rapport espace disque serveurs

```powershell
$serveurs = @("SRV-DC01", "SRV-FILE01", "SRV-WEB01")
$rapport = @()

foreach ($srv in $serveurs) {
    $disques = Get-WmiObject Win32_LogicalDisk -ComputerName $srv -Filter "DriveType=3"
    
    foreach ($disque in $disques) {
        $rapport += [PSCustomObject]@{
            Serveur = $srv
            Lecteur = $disque.DeviceID
            TailleGo = [math]::Round($disque.Size/1GB, 2)
            LibreGo = [math]::Round($disque.FreeSpace/1GB, 2)
            PourcentLibre = [math]::Round(($disque.FreeSpace/$disque.Size)*100, 2)
        }
    }
}

$rapport | Export-Csv "rapport_disques.csv" -NoTypeInformation
$rapport | Where-Object {$_.PourcentLibre -lt 20} | Format-Table -AutoSize
```

### Script 3 : Désactiver comptes inactifs >90 jours

```powershell
$date = (Get-Date).AddDays(-90)

Get-ADUser -Filter {Enabled -eq $true} -Properties LastLogonDate | 
    Where-Object {$_.LastLogonDate -lt $date} | 
    ForEach-Object {
        Disable-ADAccount -Identity $_.SamAccountName
        Write-Host "Compte $($_.SamAccountName) desactive (inactif depuis $($_.LastLogonDate))" -ForegroundColor Yellow
    }
```

---

## COMMANDES ESSENTIELLES À RETENIR

### Active Directory
```powershell
Get-ADUser, New-ADUser, Set-ADUser, Remove-ADUser
Get-ADGroup, New-ADGroup, Add-ADGroupMember
Get-ADComputer
```

### GPO
```powershell
New-GPO, New-GPLink
gpupdate /force
gpresult /r
```

### DNS
```powershell
Add-DnsServerResourceRecordA
Add-DnsServerResourceRecordCName
nslookup, Resolve-DnsName
```

### Permissions
```powershell
Get-Acl, Set-Acl
New-SmbShare, Grant-SmbShareAccess
```

### Services
```powershell
Get-Service, Start-Service, Stop-Service, Restart-Service
```

