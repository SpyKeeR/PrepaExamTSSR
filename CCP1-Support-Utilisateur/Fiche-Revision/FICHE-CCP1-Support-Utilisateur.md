# FICHE REVISION CCP1 - SUPPORT UTILISATEUR

**Certification TSSR** - CCP1 : Assister utilisateurs centre de services

---

## 1. MÉTHODOLOGIE TICKETS

### Cycle de vie ticket
1. **Ouverture** : Identification utilisateur, description problème
2. **Qualification** : Catégorie (matériel/logiciel/réseau), criticité
3. **Priorisation** : P1 (bloquant) → P4 (confort)
4. **Diagnostic** : Analyse, reproduction, logs
5. **Résolution** : Application solution, test
6. **Clôture** : Validation utilisateur, documentation

### Niveaux priorité
- **P1 (Critique)** : Production bloquée, >50 users (4h max)
- **P2 (Urgent)** : Fonctionnalité majeure KO (8h max)
- **P3 (Normal)** : Gêne partielle (24h max)
- **P4 (Mineur)** : Demande amélioration (72h)

---

## 2. COMMUNICATION EFFICACE

### Règles d'or
- ✅ Langage simple (pas jargon technique)
- ✅ Écoute active (reformuler problème)
- ✅ Empathie ("Je comprends votre situation")
- ✅ Questions fermées (oui/non pour diagnostic)
- ❌ Éviter "c'est évident", "normalement ça marche"

### Exemple transformation
**Mauvais** : "Votre résolveur DNS ne répond pas, vérifiez la config DHCP"  
**Bon** : "Je vais vérifier votre connexion réseau. Pouvez-vous essayer d'ouvrir Google ?"

---

## 3. DÉPANNAGE RÉSEAU

### Commandes essentielles
```powershell
# Test connectivité
ping 8.8.8.8                          # Internet Google DNS
ping google.com                       # Résolution DNS
Test-NetConnection google.com -Port 443

# Configuration IP
ipconfig /all                         # Config complète
ipconfig /release                     # Libérer IP DHCP
ipconfig /renew                       # Renouveler IP
ipconfig /flushdns                    # Vider cache DNS

# Traçage route
tracert google.com                    # Chemin paquets
pathping google.com                   # Trace + stats pertes
```

### Diagnostic connexion
1. **Câble physique** : Voyant carte réseau allumé ?
2. **IP valide** : `ipconfig` → Si 169.254.x.x = APIPA (DHCP KO)
3. **Passerelle** : `ping <gateway>` → Routeur accessible ?
4. **DNS** : `nslookup google.com` → Résolution OK ?
5. **Internet** : `ping 8.8.8.8` → Connexion externe OK ?

---

## 4. ACTIVE DIRECTORY - GESTION COMPTES

### Réinitialisation mot de passe
**Sécurité** :
- Vérifier identité (badge, validation manager)
- Pas de réinit par téléphone sans double authentification
- Email sécurisé séparé pour communiquer nouveau MDP

**PowerShell** :
```powershell
# Réinitialiser MDP
Set-ADAccountPassword -Identity jdupont -Reset `
  -NewPassword (ConvertTo-SecureString "TempPass2025!" -AsPlainText -Force)

# Forcer changement prochaine connexion
Set-ADUser -Identity jdupont -ChangePasswordAtLogon $true

# Déverrouiller compte
Unlock-ADAccount -Identity jdupont
```

### Déblocage compte
**Causes** :
- 3-5 tentatives MDP incorrectes (GPO)
- Expiration MDP
- Compte désactivé admin

---

## 5. ACCÈS DISTANT (RDP)

### Configuration RDP
```powershell
# Activer RDP
Set-ItemProperty -Path 'HKLM:\System\CurrentControlSet\Control\Terminal Server' `
  -Name "fDenyTSConnections" -Value 0

# Pare-feu
Enable-NetFirewallRule -DisplayGroup "Remote Desktop"

# Ajouter utilisateur
Add-LocalGroupMember -Group "Remote Desktop Users" -Member "DOMAIN\jdupont"
```

### Dépannage connexion RDP
1. **Port 3389** : `Test-NetConnection PC-01 -Port 3389`
2. **Service** : `Get-Service TermService` (doit être "Running")
3. **Pare-feu** : Règle "Remote Desktop" activée
4. **Groupe** : Utilisateur membre "Remote Desktop Users"
5. **Licence** : Serveur RDS licences valides

---

## 6. OUTLOOK - PROBLÈMES COURANTS

### Outlook lent/erreurs
**Diagnostics** :
```powershell
# Taille OST/PST
Get-ChildItem "$env:LOCALAPPDATA\Microsoft\Outlook\*.ost" | Select Name, Length

# Mode sans échec (désactive add-ins)
outlook.exe /safe

# Réparer profil
outlook.exe /resetnavpane
```

**Solutions** :
- **OST >10GB** : Archiver anciens emails
- **Add-ins** : Désactiver modules tiers (File → Options → Add-ins)
- **Profil corrompu** : Créer nouveau profil (Control Panel → Mail)
- **Index** : Reconstruire Windows Search

### Recréer OST
```
1. Fermer Outlook
2. Renommer C:\Users\<user>\AppData\Local\Microsoft\Outlook\*.ost
3. Redémarrer Outlook (OST recréé automatiquement)
```

---

## 7. IMPRIMANTES RÉSEAU

### Déploiement GPO
```
User Config → Preferences → Control Panel Settings → Printers
→ New → Shared Printer
   Action: Update
   Share path: \\SRV-PRINT01\HP-RH-01
   Default: Yes
```

### Dépannage impression
```powershell
# Lister imprimantes
Get-Printer

# Ajouter imprimante
Add-Printer -ConnectionName "\\SRV-PRINT01\HP-RH-01"

# Redémarrer spooler
Restart-Service Spooler

# Vider file d'attente
Stop-Service Spooler
Remove-Item C:\Windows\System32\spool\PRINTERS\* -Force
Start-Service Spooler
```

---

## 8. MAPPAGE LECTEURS RÉSEAU

### GPO Drive Mapping
```
User Config → Preferences → Windows Settings → Drive Maps
→ New Mapped Drive
   Location: \\SRV-FILE01\Partages
   Drive Letter: P:
   Reconnect: ☑
   Label: Partages Communs
```

### Commandes manuelles
```cmd
# Mapper
net use P: \\SRV-FILE01\Partages /persistent:yes

# Lister
net use

# Supprimer
net use P: /delete
```

**PowerShell** :
```powershell
New-PSDrive -Name "P" -PSProvider FileSystem `
  -Root "\\SRV-FILE01\Partages" -Persist
```

---

## 9. VPN UTILISATEUR NOMADE

### Configuration Windows
```
Paramètres → Réseau → VPN → Ajouter connexion VPN
  Fournisseur: Windows (intégré)
  Nom: VPN Entreprise
  Serveur: vpn.entreprise.local
  Type VPN: IKEv2 (recommandé) ou L2TP/IPsec
  Type connexion: Nom d'utilisateur et mot de passe
```

**PowerShell** :
```powershell
Add-VpnConnection -Name "VPN Entreprise" `
  -ServerAddress "vpn.entreprise.local" `
  -TunnelType L2tp `
  -EncryptionLevel Required `
  -AuthenticationMethod MSChapv2 `
  -RememberCredential
```

### Dépannage VPN
- **Erreur 806** : Pare-feu bloque (port 1723 L2TP)
- **Erreur 809** : NAT-T bloqué (UDP 4500)
- **Erreur 619** : Credentials incorrects

---

## 10. PROFILS UTILISATEURS

### Profil corrompu
**Message** : "Service de profil utilisateur a échoué l'ouverture de session"

**Solution registre** :
```
1. Connexion admin local
2. regedit → HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\ProfileList
3. Chercher SID utilisateur (S-1-5-21-xxx)
4. Si .bak existe :
   - Renommer SID → SID.old
   - Renommer SID.bak → SID
5. Redémarrer, connexion utilisateur
```

### Redirection dossiers (GPO)
```
User Config → Policies → Windows Settings → Folder Redirection
  Documents → \\SRV-FILE01\Users$\%USERNAME%\Documents
  Desktop → \\SRV-FILE01\Users$\%USERNAME%\Desktop
```

---

## 11. WINDOWS UPDATE

### Erreurs courantes
**0x80070005** (accès refusé) :
```powershell
Stop-Service wuauserv, bits, cryptsvc
Remove-Item C:\Windows\SoftwareDistribution\Download\* -Recurse -Force
Start-Service wuauserv, bits, cryptsvc
```

**0x80240034** (échec téléchargement) :
```powershell
Dism /Online /Cleanup-Image /RestoreHealth
sfc /scannow
```

### Forcer mises à jour
```powershell
# Module PSWindowsUpdate
Install-Module PSWindowsUpdate -Force
Get-WindowsUpdate
Install-WindowsUpdate -AcceptAll -AutoReboot
```

---

## 12. GPO DÉPANNAGE

### Vérifier application GPO
```powershell
# Résumé GPO utilisateur
gpresult /r /scope:user

# Rapport HTML complet
gpresult /h C:\gporeport.html /user DOMAIN\jdupont

# Forcer application
gpupdate /force

# Logs GPO
eventvwr.msc → Applications and Services → Microsoft → Windows → GroupPolicy
```

### Problèmes fréquents
- Ordre GPO (priorité OU > Domaine)
- Filtrage sécurité (utilisateur pas dans groupe autorisé)
- Blocage héritage activé
- Cache GPO obsolète (`gpupdate /force`)

---

## 13. OUTILS DIAGNOSTICS

### Event Viewer
```
eventvwr.msc
  Windows Logs → Application (erreurs logiciels)
  Windows Logs → System (matériel, services)
  Windows Logs → Security (authentification)
```

**PowerShell** :
```powershell
# 50 dernières erreurs système
Get-EventLog -LogName System -EntryType Error -Newest 50

# Erreurs Application dernières 24h
Get-EventLog -LogName Application -After (Get-Date).AddDays(-1) -EntryType Error
```

### Outils réseau
- **ping** : Connectivité basique
- **tracert** : Chemin routage
- **nslookup** : Résolution DNS
- **netstat** : Connexions actives
- **Test-NetConnection** : Port TCP spécifique

---

## 14. TICKETS TYPES

| Type | Priorité | Délai | Actions |
|------|----------|-------|---------|
| MDP oublié | P2 | 1h | Vérif identité, réinit AD |
| Imprimante KO | P3 | 4h | Spooler, pilotes, réseau |
| Pas d'accès réseau | P1 | 30min | IP, DNS, câble |
| Email ne part pas | P2 | 2h | Outlook config, Exchange |
| Application plante | P2-P3 | 4h | Logs, réinstall, MAJ |

---

## 15. BONNES PRATIQUES

### Documentation
- ✅ Détailler actions réalisées
- ✅ Noter solutions pour KB (base connaissances)
- ✅ Temps passé précis

### Escalade
- **Niveau 1** : Support basique, réinitialisation
- **Niveau 2** : Expertise technique, scripts
- **Niveau 3** : Architecte, problèmes complexes

### SLA (Service Level Agreement)
- Respecter délais contractuels
- Communiquer si dépassement prévu
- Prioriser selon impact business

---

**COMMANDES MÉMO** :
```powershell
gpupdate /force              # Forcer GPO
ipconfig /renew              # Renouveler IP
Test-NetConnection -Port 80  # Test port
Restart-Service <nom>        # Redémarrer service
Set-ADAccountPassword        # Reset MDP AD
Get-EventLog -Newest 50      # Logs système
```

**Date révision** : 12 novembre 2025