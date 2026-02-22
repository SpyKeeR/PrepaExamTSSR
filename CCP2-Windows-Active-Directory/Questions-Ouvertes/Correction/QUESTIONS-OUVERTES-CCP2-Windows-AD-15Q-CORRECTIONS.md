# CORRECTIONS - CCP2 WINDOWS SERVER / AD

**Certification TSSR** - CCP2 : Exploiter des serveurs Windows et un domaine ActiveDirectory
**15 questions - Corrections détaillées**

---

## ✔️ Question 1 : Architecture Active Directory (8 points)

**Énoncé** :  
Expliquez la différence entre une **forêt**, un **domaine** et une **unité organisationnelle (OU)** dans Active Directory. Donnez un cas d'usage concret pour chacun.

**Réponse modèle** :

**Forêt** (2 pts) :
- Limite sécurité maximale
- Schéma AD commun (définitions objets)
- Relations d'approbation entre domaines
- **Cas d'usage** : Entreprise multi-sociétés (Holding avec 3 filiales → 1 forêt, 3 domaines)

**Domaine** (3 pts) :
- Limite administrative et de réplication
- Base de données AD unique
- Politiques de sécurité (GPO) communes
- **Cas d'usage** : Société avec siège Paris + agence Lyon → 1 domaine `entreprise.local`, réplication automatique

**Unité Organisationnelle (OU)** (3 pts) :
- Conteneur personnalisable d'objets
- Permet délégation administration
- Application sélective de GPO
- **Cas d'usage** : OU "RH", "Comptabilite", "IT" → GPO mappage lecteurs différents par service

**Exemple concret** :
```
Forêt : groupe-multinationale.com
  └── Domaine : france.groupe-multinationale.com
        ├── OU : Paris
        │     ├── OU : RH
        │     └── OU : IT
        └── OU : Lyon
```

**Points** : 8/8

---

## ✔️ Question 2 : Rôles FSMO (6 points)

**Énoncé** :  
Citez les 5 rôles FSMO et expliquez les conséquences de l'indisponibilité du rôle **Émulateur PDC**.

**Réponse modèle** :

**Les 5 rôles FSMO** (3 pts) :
1. **Maître de schéma** (forêt) - Modifications schéma AD
2. **Maître d'attribution de noms** (forêt) - Ajout/suppression domaines
3. **Émulateur PDC** (domaine) - Sync horaire, verrou comptes, réplication prioritaire
4. **Maître RID** (domaine) - Distribution pools identifiants uniques
5. **Maître d'infrastructure** (domaine) - Références objets inter-domaines

**Conséquences indisponibilité Émulateur PDC** (3 pts) :
- ❌ Authentification échoue après 3 tentatives incorrectes (pas de verrouillage)
- ❌ Modifications mots de passe non répliquées immédiatement (délai 15min)
- ❌ Désynchronisation horaire Kerberos → échecs authentification (tolérance 5min)
- ❌ Anciennes GPO appliquées (pas de nouvelles)

**Commande vérification** :
```powershell
netdom query fsmo
Get-ADDomain | Select-Object PDCEmulator, RIDMaster, InfrastructureMaster
```

**Points** : 6/6

---

## ✔️ Question 3 : Stratégie de groupe - Dépannage (10 points)

**Énoncé** :  
Un utilisateur du service RH ne voit pas le lecteur réseau `P:` mappé vers `\\SRV-FILE01\RH_Partage` alors que la GPO est correctement liée à l'OU "RH". Listez 5 causes possibles et la commande de diagnostic à exécuter.

**Réponse modèle** :

**5 causes possibles** (5 pts) :
1. GPO pas appliquée (délai réplication, cache local)
2. Ordre GPO (une autre GPO bloque avec "Appliqué" désactivé)
3. Filtrage sécurité (utilisateur pas dans groupe autorisé)
4. Blocage héritage GPO niveau OU supérieur
5. Partage réseau inaccessible (permissions SMB/NTFS, service arrêté)

**Commande diagnostic** (5 pts) :
```powershell
# Vérifier GPO appliquées
gpresult /r /scope:user
# ou rapport HTML détaillé
gpresult /h C:\rapport_gpo.html /user ENTREPRISE\jdupont

# Forcer application immédiate
gpupdate /force

# Tester accès partage
Test-Path "\\SRV-FILE01\RH_Partage"
Get-SmbShare -Name "RH_Partage" -CimSession SRV-FILE01
```

**Démarche dépannage** :
1. `gpresult /r` → Vérifier si GPO listée
2. Si non listée → `gpupdate /force`
3. Si listée mais lecteur absent → Vérifier permissions utilisateur sur partage
4. Tester manuellement `net use P: \\SRV-FILE01\RH_Partage`

**Points** : 10/10

---

## ✔️ Question 4 : Permissions NTFS vs SMB (8 points)

**Énoncé** :  
Un dossier `C:\Partages\Comptabilite` a les permissions suivantes :
- **NTFS** : Groupe "GRP_Compta" = Modification
- **Partage SMB "Compta_Partage"** : Groupe "GRP_Compta" = Lecture seule

Quelles permissions effectives aura un membre de "GRP_Compta" accédant via `\\SRV01\Compta_Partage` ? Justifiez.

**Réponse modèle** :

**Permissions effectives** (3 pts) :  
✅ **LECTURE SEULE**

**Justification** (5 pts) :
- Règle cumulative : **La plus restrictive gagne**
- Accès local (direct sur serveur) : NTFS seul → **Modification**
- Accès réseau (via `\\`) : Intersection NTFS ∩ SMB → **min(Modification, Lecture) = Lecture**

**Tableau récapitulatif** :
| Accès | NTFS | SMB | Effectif |
|-------|------|-----|----------|
| Local | Modification | N/A | **Modification** |
| Réseau | Modification | Lecture | **Lecture** |

**Bonne pratique** :
```powershell
# Partage SMB permissif (Contrôle total à tous)
New-SmbShare -Name "Compta_Partage" -Path "C:\Partages\Comptabilite" -FullAccess "Tout le monde"

# Sécurité fine via NTFS uniquement
$acl = Get-Acl "C:\Partages\Comptabilite"
$rule = New-Object System.Security.AccessControl.FileSystemAccessRule("GRP_Compta", "Modify", "ContainerInherit,ObjectInherit", "None", "Allow")
$acl.SetAccessRule($rule)
Set-Acl "C:\Partages\Comptabilite" $acl
```

**Points** : 8/8

---

## ✔️ Question 5 : PowerShell - Script création utilisateurs (12 points)

**Énoncé** :  
Écrivez un script PowerShell qui crée 3 utilisateurs à partir d'un fichier CSV avec colonnes `Prenom,Nom,Service`. Le login doit être `prenom.nom`, le mot de passe temporaire `Welcome2025!`, placés dans l'OU correspondant au service (`OU=RH,DC=entreprise,DC=local`).

**Réponse modèle** :

**Fichier CSV** (2 pts) :
```csv
Prenom,Nom,Service
Jean,Dupont,RH
Marie,Martin,Comptabilite
Pierre,Durand,IT
```

**Script PowerShell** (10 pts) :
```powershell
# Importer module AD
Import-Module ActiveDirectory

# Importer CSV
$utilisateurs = Import-Csv -Path "C:\Scripts\utilisateurs.csv"

# Mot de passe temporaire
$password = ConvertTo-SecureString "Welcome2025!" -AsPlainText -Force

# Parcourir utilisateurs
foreach ($user in $utilisateurs) {
    # Construction login
    $login = "$($user.Prenom).$($user.Nom)".ToLower()
    
    # Construction chemin OU
    $ou = "OU=$($user.Service),DC=entreprise,DC=local"
    
    # Vérifier si OU existe
    try {
        Get-ADOrganizationalUnit -Identity $ou -ErrorAction Stop
        
        # Créer utilisateur
        New-ADUser -Name "$($user.Prenom) $($user.Nom)" `
            -GivenName $user.Prenom `
            -Surname $user.Nom `
            -SamAccountName $login `
            -UserPrincipalName "$login@entreprise.local" `
            -Path $ou `
            -AccountPassword $password `
            -Enabled $true `
            -ChangePasswordAtLogon $true
        
        Write-Host "[OK] Utilisateur $login cree dans $ou" -ForegroundColor Green
        
    } catch {
        Write-Host "[ERREUR] OU $ou introuvable pour $login" -ForegroundColor Red
    }
}
```

**Points clés notation** :
- Import-Module + Import-Csv (2 pts)
- Boucle foreach (2 pts)
- Construction login avec `.ToLower()` (2 pts)
- Paramètres New-ADUser corrects (3 pts)
- Gestion erreur Try/Catch (1 pt)

**Points** : 12/12

---

## ✔️ Question 6 : DNS Windows Server (8 points)

**Énoncé** :  
Expliquez la différence entre un enregistrement **A**, **CNAME** et **PTR**. Donnez la commande PowerShell pour créer chacun.

**Réponse modèle** :

**Enregistrement A** (2 pts) :
- Associe **nom d'hôte → adresse IPv4**
- Exemple : `intranet.entreprise.local` → `192.168.10.50`

**Commande** :
```powershell
Add-DnsServerResourceRecordA -Name "intranet" `
  -ZoneName "entreprise.local" `
  -IPv4Address "192.168.10.50"
```

**Enregistrement CNAME (Canonical Name)** (2 pts) :
- **Alias** pointant vers un autre nom DNS
- Exemple : `www` → `intranet.entreprise.local`
- Utile : Plusieurs noms pour même serveur

**Commande** :
```powershell
Add-DnsServerResourceRecordCName -Name "www" `
  -ZoneName "entreprise.local" `
  -HostNameAlias "intranet.entreprise.local"
```

**Enregistrement PTR (Pointer)** (2 pts) :
- **Résolution inverse** : adresse IP → nom d'hôte
- Zone reverse : `10.168.192.in-addr.arpa`
- Utilisé par : vérifications email (anti-spam)

**Commande** :
```powershell
Add-DnsServerResourceRecordPtr -Name "50" `
  -ZoneName "10.168.192.in-addr.arpa" `
  -PtrDomainName "intranet.entreprise.local"
```

**Test** (2 pts) :
```powershell
nslookup intranet.entreprise.local          # Test A
nslookup www.entreprise.local               # Test CNAME
nslookup 192.168.10.50                      # Test PTR
```

**Points** : 8/8

---

## ✔️ Question 7 : GPO - Bloquer clés USB (6 points)

**Énoncé** :  
Un responsable sécurité demande de bloquer l'utilisation des clés USB sur les postes du service Comptabilité, tout en autorisant les souris/claviers USB. Décrivez la configuration GPO et le chemin complet.

**Réponse modèle** :

**Chemin GPO** (3 pts) :
```
Computer Configuration
  └── Policies
        └── Administrative Templates
              └── System
                    └── Removable Storage Access
```

**Configuration** (3 pts) :
1. **Activer** : `All Removable Storage classes: Deny all access`  
   OU (plus sélectif) :
   - `Removable Disks: Deny read access` = **Enabled**
   - `Removable Disks: Deny write access` = **Enabled**
   - `Removable Disks: Deny execute access` = **Enabled**

2. **Exception souris/claviers** :  
   Ne PAS activer `CD and DVD: Deny...` ni restreindre périphériques HID (Human Interface Devices)

**Application** :
```powershell
# Créer GPO
New-GPO -Name "GPO-Blocage-USB-Comptabilite"

# Lier à OU
New-GPLink -Name "GPO-Blocage-USB-Comptabilite" `
  -Target "OU=Comptabilite,DC=entreprise,DC=local" `
  -LinkEnabled Yes

# Forcer application
Invoke-GPUpdate -Computer "PC-COMPTA-01" -Force
```

**Test** :  
Brancher clé USB → Message "Accès refusé par stratégie de groupe"

**Points** : 6/6

---

## ✔️ Question 8 : WSUS - Gestion mises à jour (7 points)

**Énoncé** :  
Expliquez le rôle de WSUS et comment configurer les postes clients pour utiliser le serveur WSUS `srv-wsus.entreprise.local` via GPO.

**Réponse modèle** :

**Rôle WSUS** (3 pts) :
- **Windows Server Update Services** : Serveur local de distribution mises à jour
- Avantages :
  - ✅ Contrôle déploiement (tester avant production)
  - ✅ Économie bande passante (1 seul téléchargement depuis Microsoft)
  - ✅ Planification maintenance

**Configuration GPO** (4 pts) :

**Chemin** :
```
Computer Configuration → Policies → Administrative Templates
  → Windows Components → Windows Update
```

**Paramètres à configurer** :
1. **Configure Automatic Updates** :  
   - Enabled
   - Option : `4 - Auto download and schedule the install`
   - Scheduled install day : `0 - Every day` ou jour spécifique
   - Scheduled install time : `03:00` (nuit)

2. **Specify intranet Microsoft update service location** :
   - Enabled
   - Set the intranet update service : `http://srv-wsus.entreprise.local:8530`
   - Set the intranet statistics server : `http://srv-wsus.entreprise.local:8530`

3. **Allow Automatic Updates immediate installation** :
   - Enabled (installe mises à jour mineures sans reboot)

**Commandes test client** :
```powershell
# Afficher config
gpresult /r | Select-String "Update"

# Forcer détection
wuauclt /detectnow
# Ou Windows 10/11
UsoClient StartScan

# Vérifier connexion WSUS
Get-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate"
```

**Points** : 7/7

---

## ✔️ Question 9 : Sauvegarde Active Directory (8 points)

**Énoncé** :  
Quelle est la différence entre une sauvegarde complète du serveur et une sauvegarde de l'état système ? Donnez les commandes pour effectuer chacune.

**Réponse modèle** :

**Sauvegarde complète (Full Backup)** (3 pts) :
- Contenu : Tous les fichiers, applications, OS, données
- Taille : Volumineuse (plusieurs dizaines/centaines GB)
- Temps restore : Long
- Cas d'usage : Disaster recovery (serveur entier détruit)

**Sauvegarde état système (System State)** (3 pts) :
- Contenu spécifique Windows :
  - Base AD (NTDS.dit)
  - Registre système
  - Fichiers démarrage (Boot files)
  - SYSVOL (GPO, scripts)
  - Certificats (si serveur CA)
- Taille : Quelques GB
- Temps restore : Rapide
- Cas d'usage : Corruption AD, récupération GPO

**Commandes** (2 pts) :

**Installation fonctionnalité** :
```powershell
Install-WindowsFeature Windows-Server-Backup -IncludeManagementTools
```

**Sauvegarde complète** :
```powershell
wbadmin start backup -backupTarget:E: -include:C:,D: -allCritical -quiet
```

**Sauvegarde état système** :
```powershell
wbadmin start systemstatebackup -backupTarget:E: -quiet
```

**Restauration état système** :
```powershell
# Redémarrer en mode restauration AD (DSRM)
wbadmin start systemstaterecovery -version:12/11/2025-10:00
```

**Bonne pratique** : Sauvegarde état système quotidienne + complète hebdomadaire

**Points** : 8/8

---

## ✔️ Question 10 : Délégation d'administration OU (7 points)

**Énoncé** :  
Le service RH doit pouvoir réinitialiser les mots de passe des utilisateurs de l'OU "RH" sans être administrateurs du domaine. Expliquez comment déléguer cette permission.

**Réponse modèle** :

**Méthode GUI** (3 pts) :
1. Ouvrir **Active Directory Users and Computers**
2. Clic droit sur OU "RH" → **Delegate Control**
3. Ajouter groupe **GRP_RH_Support**
4. Tâches à déléguer :
   - ☑ Reset user passwords and force password change at next logon
5. Terminer

**Méthode PowerShell** (4 pts) :
```powershell
# Importer module
Import-Module ActiveDirectory

# Créer groupe délégation
New-ADGroup -Name "GRP_RH_Support" -GroupScope Global -Path "OU=Groupes,DC=entreprise,DC=local"

# Déléguer permission
$ou = "OU=RH,DC=entreprise,DC=local"
$group = "ENTREPRISE\GRP_RH_Support"

# Récupérer ACL OU
$acl = Get-Acl "AD:\$ou"

# Créer règles permissions
$identity = [System.Security.Principal.NTAccount]$group

# Permission : Reset password
$guidPwdReset = [GUID]"00299570-246d-11d0-a768-00aa006e0529"
$ruleReset = New-Object System.DirectoryServices.ActiveDirectoryAccessRule(
    $identity,
    [System.DirectoryServices.ActiveDirectoryRights]::ExtendedRight,
    [System.Security.AccessControl.AccessControlType]::Allow,
    $guidPwdReset,
    [System.DirectoryServices.ActiveDirectorySecurityInheritance]::Descendents
)
$acl.AddAccessRule($ruleReset)

# Appliquer ACL
Set-Acl "AD:\$ou" $acl

Write-Host "Delegation reussie pour $group sur $ou"
```

**Test** :
Membre de GRP_RH_Support peut :
- ✅ Réinitialiser mots de passe OU RH
- ❌ Modifier autres propriétés utilisateurs (sauf si délégué)

**Points** : 7/7

---

## ✔️ Question 11 : Stratégie de mots de passe fine (FGPP) (8 points)

**Énoncé** :  
Les utilisateurs standard doivent avoir des mots de passe de 12 caractères minimum, mais les administrateurs doivent utiliser 16 caractères. Comment implémenter cela avec les stratégies de mots de passe fines (FGPP) ?

**Réponse modèle** :

**Contexte** (2 pts) :
- Avant Windows Server 2008 : 1 seule stratégie mot de passe par domaine
- FGPP (Fine-Grained Password Policy) : Stratégies différentes par groupe/utilisateur
- Stockées dans : `CN=Password Settings Container,CN=System,DC=entreprise,DC=local`

**Configuration** (6 pts) :

**Stratégie 1 - Utilisateurs standard** :
```powershell
New-ADFineGrainedPasswordPolicy -Name "PSO-Standard" `
  -Precedence 200 `
  -MinPasswordLength 12 `
  -PasswordHistoryCount 24 `
  -MaxPasswordAge 90.00:00:00 `
  -MinPasswordAge 1.00:00:00 `
  -ComplexityEnabled $true `
  -ReversibleEncryptionEnabled $false `
  -LockoutDuration 00:30:00 `
  -LockoutObservationWindow 00:30:00 `
  -LockoutThreshold 5

# Appliquer au groupe utilisateurs standard
Add-ADFineGrainedPasswordPolicySubject -Identity "PSO-Standard" -Subjects "GRP_Utilisateurs"
```

**Stratégie 2 - Administrateurs** :
```powershell
New-ADFineGrainedPasswordPolicy -Name "PSO-Admins" `
  -Precedence 100 `
  -MinPasswordLength 16 `
  -PasswordHistoryCount 48 `
  -MaxPasswordAge 60.00:00:00 `
  -MinPasswordAge 1.00:00:00 `
  -ComplexityEnabled $true `
  -ReversibleEncryptionEnabled $false `
  -LockoutDuration 01:00:00 `
  -LockoutObservationWindow 00:15:00 `
  -LockoutThreshold 3

# Appliquer aux groupes admins
Add-ADFineGrainedPasswordPolicySubject -Identity "PSO-Admins" -Subjects "Domain Admins","GRP_IT_Admins"
```

**Points clés** :
- `Precedence` plus FAIBLE = priorité plus HAUTE (100 > 200)
- Admins : 16 car, historique 48, verrouillage 3 tentatives
- Standard : 12 car, historique 24, verrouillage 5 tentatives

**Vérification** :
```powershell
Get-ADFineGrainedPasswordPolicy -Filter *
Get-ADUserResultantPasswordPolicy -Identity "jdupont"
```

**Points** : 8/8

---

## ✔️ Question 12 : DFS - Espace de noms (6 points)

**Énoncé** :  
Qu'est-ce que DFS (Distributed File System) et quel avantage apporte-t-il pour le partage de fichiers en entreprise ?

**Réponse modèle** :

**Définition DFS** (2 pts) :
- **DFS Namespaces** : Virtualisation chemins UNC
- **DFS Replication** : Synchronisation multi-sites

**Avantages** (4 pts) :

**Sans DFS** (problème) :
```
Utilisateur Paris : \\SRV-PARIS\Partages\Commun
Utilisateur Lyon : \\SRV-LYON\Partages\Commun
```
→ Chemins différents, confusion, scripts à adapter

**Avec DFS** (solution) :
```
Tous les utilisateurs : \\entreprise.local\DFS\Commun
```
→ Redirection automatique vers serveur le plus proche

**Exemple configuration** :
```powershell
# Créer espace de noms
New-DfsnRoot -Path "\\entreprise.local\DFS" -TargetPath "\\SRV-DC01\DFS" -Type DomainV2

# Ajouter dossier virtuel
New-DfsnFolder -Path "\\entreprise.local\DFS\Commun" -TargetPath "\\SRV-PARIS\Partages\Commun"

# Ajouter cible Lyon (réplication)
New-DfsnFolderTarget -Path "\\entreprise.local\DFS\Commun" -TargetPath "\\SRV-LYON\Partages\Commun"
```

**Avantages clés** :
- ✅ Tolérance panne (si SRV-PARIS down → bascule SRV-LYON)
- ✅ Optimisation géographique (routage automatique site le plus proche)
- ✅ Simplification administration (1 chemin unique)

**Points** : 6/6

---

## ✔️ Question 13 : BitLocker - Chiffrement disque (7 points)

**Énoncé** :  
Un laptop contient des données sensibles. Comment activer BitLocker via PowerShell et sauvegarder la clé de récupération dans Active Directory ?

**Réponse modèle** :

**Prérequis** (2 pts) :
- TPM (Trusted Platform Module) version 1.2+ sur PC
- Windows Professionnel/Entreprise (pas Home)
- GPO "Store BitLocker recovery in AD" activée

**Activation BitLocker** (3 pts) :
```powershell
# Vérifier TPM
Get-Tpm

# Activer BitLocker sur C:
Enable-BitLocker -MountPoint "C:" `
  -EncryptionMethod XtsAes256 `
  -UsedSpaceOnly `
  -TpmProtector

# Ajouter mot de passe récupération
Add-BitLockerKeyProtector -MountPoint "C:" -RecoveryPasswordProtector
```

**Sauvegarde clé dans AD** (2 pts) :
```powershell
# Récupérer ID clé
$keyID = (Get-BitLockerVolume -MountPoint "C:").KeyProtector | Where-Object {$_.KeyProtectorType -eq "RecoveryPassword"} | Select-Object -ExpandProperty KeyProtectorId

# Sauvegarder dans AD
Backup-BitLockerKeyProtector -MountPoint "C:" -KeyProtectorId $keyID

# Vérifier sauvegarde
Get-ADComputer -Identity "LAPTOP-01" -Properties "msTPM-OwnerInformation"
```

**GPO automatisation** :
```
Computer Config → Policies → Admin Templates → Windows Components → BitLocker Drive Encryption → Operating System Drives
→ Choose how BitLocker-protected OS drives can be recovered
  ☑ Save BitLocker recovery information to AD DS
  ☑ Do not enable BitLocker until recovery information is stored
```

**Récupération** :
```powershell
# Afficher clé récupération
manage-bde -protectors -get C:
```

**Points** : 7/7

---

## ✔️ Question 14 : Audit Active Directory (8 points)

**Énoncé** :  
Le DSI demande un rapport mensuel des modifications de groupes d'administration (ajout/suppression membres). Comment configurer l'audit et extraire ces informations ?

**Réponse modèle** :

**Configuration audit** (4 pts) :

**1. Activer audit via GPO** :
```
Computer Config → Policies → Windows Settings → Security Settings → Advanced Audit Policy
  → DS Access
    ☑ Audit Directory Service Changes : Success
```

**2. Activer audit sur objet AD** :
```powershell
# Audit groupe "Domain Admins"
$group = Get-ADGroup "Domain Admins"
$acl = Get-Acl "AD:\$($group.DistinguishedName)" -Audit

# Règle audit : Toute modification
$rule = New-Object System.DirectoryServices.ActiveDirectoryAuditRule(
    [System.Security.Principal.SecurityIdentifier]"S-1-1-0",  # Everyone
    [System.DirectoryServices.ActiveDirectoryRights]::WriteProperty,
    [System.Security.AccessControl.AuditFlags]::Success
)

$acl.AddAuditRule($rule)
Set-Acl "AD:\$($group.DistinguishedName)" $acl -AuditRules $rule
```

**Extraction événements** (4 pts) :
```powershell
# Événements modifications groupes AD (Event ID 4728/4729/4732/4733)
Get-WinEvent -FilterHashtable @{
    LogName='Security'
    ID=4728,4729,4732,4733
    StartTime=(Get-Date).AddDays(-30)
} | ForEach-Object {
    [PSCustomObject]@{
        Date = $_.TimeCreated
        Utilisateur = $_.Properties[6].Value
        Groupe = $_.Properties[2].Value
        Membre = $_.Properties[0].Value
        Action = switch ($_.Id) {
            4728 {"Ajout membre groupe global"}
            4729 {"Suppression membre groupe global"}
            4732 {"Ajout membre groupe local"}
            4733 {"Suppression membre groupe local"}
        }
    }
} | Export-Csv "Rapport-Audit-Groupes.csv" -NoTypeInformation

Write-Host "Rapport genere : Rapport-Audit-Groupes.csv"
```

**Event IDs importants** :
- **4728** : Membre ajouté groupe sécurité global
- **4729** : Membre supprimé groupe sécurité global
- **4732** : Membre ajouté groupe local
- **4733** : Membre supprimé groupe local
- **4720** : Compte utilisateur créé
- **4726** : Compte utilisateur supprimé

**Points** : 8/8

---

## ✔️ Question 15 : PowerShell - Recherche comptes inactifs (10 points)

**Énoncé** :  
Créez un script PowerShell qui génère un rapport CSV des comptes utilisateurs inactifs (>180 jours sans connexion), incluant les colonnes : Nom, Login, OU, DernièreConnexion, Désactivé. Le script doit proposer de désactiver automatiquement ces comptes.

**Réponse modèle** :

**Script complet** (10 pts) :
```powershell
# Paramètres
$joursInactivite = 180
$dateLimite = (Get-Date).AddDays(-$joursInactivite)
$rapportCsv = "C:\Scripts\Comptes-Inactifs-$(Get-Date -Format 'yyyyMMdd').csv"

# Import module AD
Import-Module ActiveDirectory

Write-Host "Recherche comptes inactifs > $joursInactivite jours..." -ForegroundColor Cyan

# Récupérer utilisateurs inactifs
$utilisateursInactifs = Get-ADUser -Filter {Enabled -eq $true} `
    -Properties LastLogonDate, DistinguishedName | 
    Where-Object {
        ($_.LastLogonDate -lt $dateLimite) -or ($_.LastLogonDate -eq $null)
    } | 
    Select-Object @{Name="Nom";Expression={$_.Name}},
                  @{Name="Login";Expression={$_.SamAccountName}},
                  @{Name="OU";Expression={($_.DistinguishedName -split ',',2)[1]}},
                  @{Name="DerniereConnexion";Expression={
                      if ($_.LastLogonDate) {$_.LastLogonDate} else {"Jamais"}
                  }},
                  @{Name="Desactive";Expression={"Non"}}

# Afficher résumé
Write-Host "`n$($utilisateursInactifs.Count) comptes inactifs trouves :" -ForegroundColor Yellow
$utilisateursInactifs | Format-Table -AutoSize

# Exporter CSV
$utilisateursInactifs | Export-Csv -Path $rapportCsv -NoTypeInformation -Encoding UTF8
Write-Host "`nRapport CSV : $rapportCsv" -ForegroundColor Green

# Proposition désactivation
$desactiver = Read-Host "`nDesactiver automatiquement ces comptes ? (O/N)"

if ($desactiver -eq "O") {
    foreach ($user in $utilisateursInactifs) {
        try {
            Disable-ADAccount -Identity $user.Login
            
            # Ajouter commentaire
            Set-ADUser -Identity $user.Login `
                -Description "Desactive automatiquement le $(Get-Date -Format 'dd/MM/yyyy') - Inactivite > $joursInactivite jours"
            
            Write-Host "[OK] $($user.Login) desactive" -ForegroundColor Green
            
        } catch {
            Write-Host "[ERREUR] Impossible de desactiver $($user.Login) : $_" -ForegroundColor Red
        }
    }
    
    Write-Host "`n$($utilisateursInactifs.Count) comptes desactives" -ForegroundColor Cyan
    
} else {
    Write-Host "`nDesactivation annulee. Rapport disponible dans $rapportCsv" -ForegroundColor Yellow
}
```

**Points notation** :
- Import module + paramètres (1 pt)
- Get-ADUser avec filtre Enabled (2 pts)
- Where-Object LastLogonDate < dateLimite (2 pts)
- Select-Object avec colonnes personnalisées (2 pts)
- Export-Csv (1 pt)
- Boucle désactivation avec Try/Catch (2 pts)

**Points** : 10/10

---

## 📊 BARÈME TOTAL : 120 POINTS

**Répartition** :
- Architecture AD (Q1-Q2) : 14 pts
- GPO (Q3, Q7, Q8) : 23 pts
- Permissions (Q4) : 8 pts
- PowerShell (Q5, Q15) : 22 pts
- DNS (Q6) : 8 pts
- Sauvegarde/Sécurité (Q9, Q13) : 15 pts
- Délégation/Audit (Q10, Q11, Q14) : 23 pts
- DFS (Q12) : 6 pts

**Note sur 20** : Total points / 6

---

**ASTUCE EXAMEN** :  
Pour les questions scripts PowerShell, même si le code n'est pas parfait, détaillez la logique et les commandes clés (Get-ADUser, ForEach, Try/Catch) pour obtenir des points partiels !
