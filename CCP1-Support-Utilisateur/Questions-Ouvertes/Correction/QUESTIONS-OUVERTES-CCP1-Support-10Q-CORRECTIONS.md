# CORRECTIONS - CCP1 SUPPORT UTILISATEUR

**Certification TSSR** - CCP1 : Assister utilisateurs centre de services  
**10 questions ouvertes - Corrections détaillées**

---

## Q1 : Méthodologie traitement ticket (6 pts)

**Énoncé** : Listez les 6 étapes traitement ticket incident.

**Réponse modèle** :
1. **Qualification** : Identifier utilisateur, problème, criticité
2. **Catégorisation** : Type incident (matériel/logiciel/réseau)
3. **Priorisation** : Urgent/Important selon impact
4. **Diagnostic** : Reproduire, analyser logs
5. **Résolution** : Appliquer solution, tester
6. **Clôture** : Valider avec utilisateur, documenter

**Criticité** : P1 (bloquant) → P4 (mineur)

---

## Q2 : Communication utilisateur (5 pts)

**Énoncé** : Utilisateur non-technique signale "internet ne marche pas". Comment répondre ?

**Réponse modèle** :

❌ **Mauvais** : "Ton DNS is down"  
✅ **Bon** : "Je comprends, je vais vérifier votre connexion. Pouvez-vous ouvrir un site web pour tester ?"

**Principes** :
- Langage simple (pas jargon)
- Empathie ("Je comprends")
- Questions fermées (oui/non)
- Reformuler problème

---

## Q3 : Remote Desktop dépannage (8 pts)

**Énoncé** : RDP vers PC-USER01 échoue. Listez 5 vérifications.

**Réponse modèle** :
1. **Ping** : `ping PC-USER01` (connectivité réseau)
2. **Port RDP** : `Test-NetConnection PC-USER01 -Port 3389`
3. **Service** : `Get-Service TermService` actif ?
4. **Pare-feu** : Règle RDP autorisée
5. **Compte** : Membre "Remote Desktop Users"

**Commandes** :
```powershell
# Activer RDP
Set-ItemProperty -Path 'HKLM:\System\CurrentControlSet\Control\Terminal Server' -Name "fDenyTSConnections" -Value 0
Enable-NetFirewallRule -DisplayGroup "Remote Desktop"
```

---

## Q4 : Gestion mot de passe (7 pts)

**Énoncé** : Utilisateur oublié mot de passe AD. Procédure réinitialisation sécurisée ?

**Réponse modèle** :

**Vérifications** (3 pts) :
1. Vérifier identité (badge, manager)
2. Ticket validé responsable
3. Pas de réinitialisation par téléphone sans double vérif

**Réinitialisation** (4 pts) :
```powershell
# AD Users and Computers → Reset Password
# Ou PowerShell :
Set-ADAccountPassword -Identity jdupont -Reset -NewPassword (ConvertTo-SecureString "TempPass2025!" -AsPlainText -Force)
Set-ADUser -Identity jdupont -ChangePasswordAtLogon $true
Unlock-ADAccount -Identity jdupont
```

**Communication** : Email sécurisé séparé (pas SMS)

---

## Q5 : Outlook problème profil (8 pts)

**Énoncé** : Outlook démarre lentement, erreurs aléatoires. Dépannage ?

**Réponse modèle** :

**Diagnostics** (4 pts) :
1. Taille PST/OST : `>10GB = problème`
2. Add-ins : Démarrer mode sans échec `outlook.exe /safe`
3. Profil corrompu : Créer nouveau profil
4. Indexation : Reconstruire index Windows Search

**Solutions** (4 pts) :
```powershell
# Réparer profil Outlook
outlook.exe /resetnavpane

# Reconstruire OST
# 1. Fermer Outlook
# 2. Renommer C:\Users\<user>\AppData\Local\Microsoft\Outlook\*.ost
# 3. Redémarrer Outlook (recrée OST)
```

**Mode sans échec** : `Win+R` → `outlook.exe /safe`

---

## Q6 : Imprimante réseau (7 pts)

**Énoncé** : Déployer imprimante \\SRV-PRINT01\HP-RH-01 via GPO.

**Réponse modèle** :

**GPO** (5 pts) :
```
User Config → Preferences → Control Panel Settings → Printers
→ New → Shared Printer
   Action: Update
   Share path: \\SRV-PRINT01\HP-RH-01
   ☑ Set this printer as default
```

**Vérif client** (2 pts) :
```powershell
gpupdate /force
Get-Printer | Where-Object {$_.Name -like "*HP-RH*"}
```

**Alternative PowerShell** :
```powershell
Add-Printer -ConnectionName "\\SRV-PRINT01\HP-RH-01"
```

---

## Q7 : Mappage lecteur réseau (6 pts)

**Énoncé** : Mapper P: vers \\SRV-FILE01\Partages persistant après reboot.

**Réponse modèle** :

**Méthode 1 - GPO** (3 pts) :
```
User Config → Preferences → Windows Settings → Drive Maps
→ New Mapped Drive
   Action: Create
   Location: \\SRV-FILE01\Partages
   Drive Letter: P:
   Reconnect: ☑
```

**Méthode 2 - Manuel** (3 pts) :
```cmd
net use P: \\SRV-FILE01\Partages /persistent:yes
```

**PowerShell** :
```powershell
New-PSDrive -Name "P" -PSProvider FileSystem -Root "\\SRV-FILE01\Partages" -Persist
```

---

## Q8 : VPN utilisateur nomade (8 pts)

**Énoncé** : Configurer VPN Windows 10 vers vpn.entreprise.local.

**Réponse modèle** :

**Manuel** (4 pts) :
```
Paramètres → Réseau et Internet → VPN → Ajouter une connexion VPN
   Fournisseur VPN: Windows (intégré)
   Nom connexion: VPN Entreprise
   Nom/adresse serveur: vpn.entreprise.local
   Type VPN: IKEv2 ou L2TP/IPsec
   Type infos connexion: Nom d'utilisateur et mot de passe
```

**PowerShell** (4 pts) :
```powershell
Add-VpnConnection -Name "VPN Entreprise" `
  -ServerAddress "vpn.entreprise.local" `
  -TunnelType L2tp `
  -EncryptionLevel Required `
  -AuthenticationMethod MSChapv2 `
  -RememberCredential
```

**Test** : `Test-NetConnection vpn.entreprise.local -Port 1723`

---

## Q9 : Mise à jour Windows bloquée (7 pts)

**Énoncé** : Windows Update erreur 0x80070005. Dépannage ?

**Réponse modèle** :

**Causes** (3 pts) :
- Permissions insuffisantes
- Service Windows Update arrêté
- Cache corrompu

**Solutions** (4 pts) :
```powershell
# Redémarrer services
Stop-Service wuauserv, bits, cryptsvc
Remove-Item C:\Windows\SoftwareDistribution\Download\* -Recurse -Force
Start-Service wuauserv, bits, cryptsvc

# Vider cache
Dism /Online /Cleanup-Image /RestoreHealth
sfc /scannow

# Outil Microsoft
# Télécharger Windows Update Troubleshooter
```

**Vérif finale** : `Get-WindowsUpdateLog`

---

## Q10 : Profil utilisateur corrompu (8 pts)

**Énoncé** : Utilisateur message "Service de profil utilisateur a échoué". Solution ?

**Réponse modèle** :

**Cause** (2 pts) : Profil Windows corrompu (registre verrouillé)

**Solution** (6 pts) :
```
1. Connexion compte admin local
2. Regedit → HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\ProfileList
3. Chercher SID utilisateur (S-1-5-21-xxx)
4. Si existe SID.bak → Renommer :
   - SID → SID.old
   - SID.bak → SID
5. Redémarrer PC
6. Connexion utilisateur (profil reconstruit)
```

**Prévention** :
- Redirection dossiers (Documents, Bureau) via GPO
- Profils itinérants (roaming profiles)
- Sauvegardes régulières

---

## BARÈME : 70 POINTS

**Note sur 20** : Total / 3.5

---

## 💡 CONSEILS PRATIQUES

**TICKETS TYPES À CONNAÎTRE** :
- Mot de passe oublié (P2)
- Imprimante ne fonctionne pas (P3)
- Accès réseau perdu (P1)
- Email ne s'envoie pas (P2)
- Application plante (P2-P3)

**OUTILS ESSENTIELS** :
- `gpupdate /force`, `gpresult /r`
- `Test-NetConnection`, `ping`, `tracert`
- `Get-Service`, `Restart-Service`
- `eventvwr.msc` (Event Viewer)

