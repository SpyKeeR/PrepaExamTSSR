# EXAMEN BLANC 1 - CORRECTIONS ET BARÈME

**Examen** : TSSR - Technicien Supérieur Systèmes et Réseaux  
**Type** : Examen blanc complet (MSP + Questionnaire technique)  
**Durée** : 3h30 (210 minutes)  
**Total** : **200 points**  

---

## 🎯 RÉPARTITION GLOBALE

| Partie | Points | Pourcentage | Durée |
|--------|--------|-------------|-------|
| **MSP Écrite (3 incidents)** | 100 | 50% | 1h30 |
| **Questionnaire Technique (6 sections)** | 100 | 50% | 2h00 |
| **TOTAL** | **200** | **100%** | **3h30** |

---

# PARTIE 1 : MSP ÉCRITE (100 points)

---

## 🔧 INCIDENT 1 : PROBLÈME DHCP (30 points)

### Question 1.1 - Cause du problème (5 points)

**Réponse attendue** :

La cause du problème est le **manque d'adresses IP disponibles dans la plage DHCP**.

**Justification** :
- Plage actuelle : 192.168.20.100 à 192.168.20.150 = **51 adresses** (150-100+1)
- Postes existants : **28** (avec baux actifs)
- Nouveaux postes : **5**
- Total besoins : **28 + 5 = 33 postes**

**Calcul** : Il reste théoriquement 51-28 = 23 adresses libres, MAIS :
- Les baux DHCP ne sont pas libérés immédiatement (durée 8 jours par défaut)
- Certains postes peuvent avoir des adresses même s'ils sont éteints
- Le pool est probablement **saturé ou proche de la saturation**

Les nouveaux PC reçoivent **169.254.x.x** (APIPA = Automatic Private IP Addressing), ce qui confirme qu'ils ne reçoivent pas de réponse du serveur DHCP.

**Barème (5 points)** :

| Critère | Points | Détails |
|---------|--------|---------||
| **Identification correcte** | 3 pts | "Manque d'adresses IP" ou "Plage DHCP saturée" |
| **Justification calcul** | 2 pts | Calcul 150-100+1=51 adresses, postes existants vs besoins |
| **Mention APIPA 169.254.x.x** | +0.5 pts | Bonus si évoqué |
| **Explication baux actifs** | +0.5 pts | Bonus si évoqué |

**Erreurs pénalisantes** :
- ❌ "Serveur DHCP en panne" (-2 pts) : Incorrect, autres PCs fonctionnent
- ❌ "Problème réseau/câble" (-1 pt) : Trop vague

---

---

### ✔️ Question 1.2 (8 points) - Solution court terme

**Réponse attendue** :

**Solution immédiate** : Étendre temporairement la plage DHCP

**Étapes dans la console DHCP Windows Server** :

1. Ouvrir **Gestionnaire DHCP** (`dhcpmgmt.msc`)
2. Naviguer : `SRV-DHCP-LYON → IPv4 → Étendue [192.168.20.0]`
3. Clic droit sur **Pool d'adresses** → **Propriétés**
4. Modifier la **plage de fin** : `192.168.20.150` → `192.168.20.180`
   - Ajoute 30 adresses (150 à 180)
5. Cliquer **OK**
6. **Optionnel** : Clic droit sur l'étendue → **Actualiser**
7. Vérifier dans **Baux d'adresses** les nouvelles attributions

**Alternative rapide** :
- Libérer des baux non utilisés : Clic droit sur baux anciens/expirés → **Supprimer**
- Réduire la durée de bail : Propriétés de l'étendue → Durée du bail : 8 jours → 1 jour (temporaire)

**Sur les nouveaux PC** :
```cmd
ipconfig /renew
```

**Barème (8 points)** :

| Critère | Points | Détails |
|---------|--------|---------||
| **Ouvrir console DHCP** | 1 pt | `dhcpmgmt.msc` ou Gestionnaire DHCP |
| **Naviguer vers l'étendue** | 1 pt | IPv4 → Étendue [192.168.20.0] → Pool |
| **Modifier plage de fin** | 4 pts | Étendre .150 → .180 ou supérieur (valeur cohérente) |
| **Appliquer changements** | 1 pt | Valider, actualiser |
| **Renouveler IP clients** | 1 pt | `ipconfig /renew` mentionné |
| **Alternative libérer baux** | +1 pt | Bonus : Supprimer baux anciens |

**Erreurs pénalisantes** :
- ❌ "Redémarrer le serveur" (-2 pts) : Inutile, ne résout pas le problème
- ❌ Valeur incohérente (.250 dans un /24) (-2 pts)
- ❌ Modification du masque de sous-réseau (-3 pts) : Change toute l'architecture

---

### ✔️ Question 1.3 (7 points) - Solution moyen terme

**Réponse attendue** :

**Solution pérenne** : Redimensionner la plage DHCP pour anticiper la croissance

**Calcul des besoins** :
- Postes actuels : 28
- Nouveaux postes : 5
- Recrutements prévus (3 mois) : 10
- **Total** : 28 + 5 + 10 = **43 postes**
- **Marge sécurité (20%)** : 43 × 1.2 = **52 postes**
- **Recommandation** : Prévoir **60 adresses**

**Nouvelle plage recommandée** :
- **Début** : `192.168.20.100`
- **Fin** : `192.168.20.159` (60 adresses : 100 + 60 - 1)

**Configuration** :
1. Console DHCP → Propriétés de l'étendue
2. Plage de début : `192.168.20.100`
3. Plage de fin : `192.168.20.159`
4. Vérifier que le masque est `/24` (255.255.255.0)
5. Appliquer

**Alternative optimale** :
- Plage : `192.168.20.100` à `192.168.20.200` (101 adresses)
- Exclure `192.168.20.180-200` pour futurs serveurs/imprimantes
- Plage effective : 100-179 = **80 adresses disponibles**

**Barème (7 points)** :

| Critère | Points | Détails |
|---------|--------|---------||
| **Calcul besoins actuels** | 1 pt | 28 + 5 + 10 = 43 postes |
| **Marge de sécurité** | 2 pts | 20-30% supplémentaires (43 × 1.2 = 52) |
| **Nouvelle plage calculée** | 3 pts | Cohérente avec besoins + marge (ex: 100-159 = 60 adresses) |
| **Justification anticipation** | 1 pt | Expliquer pourquoi prévoir plus |
| **Alternative exclusions** | +1 pt | Bonus : Exclure plage pour IP statiques |

**Erreurs pénalisantes** :
- ❌ Pas de marge de sécurité (-2 pts)
- ❌ Plage trop juste (≤ 45 adresses) (-1 pt)
- ❌ Plage dépassant le /24 sans explication (-2 pts)

---

### ✔️ Question 1.4 (5 points) - Réservation DHCP vs IP statique

**Réponse attendue** :

**Avantages des réservations DHCP** :

1. **Gestion centralisée** :
   - Toutes les config IP sur le serveur DHCP
   - Modifications globales faciles (changer DNS, passerelle)
   - Pas besoin d'accéder physiquement à l'imprimante

2. **Cohérence des options** :
   - Les imprimantes reçoivent automatiquement DNS, passerelle, etc.
   - Évite les erreurs de configuration manuelle

3. **Traçabilité** :
   - Historique des baux dans les logs DHCP
   - Mapping IP ↔ MAC documenté
   - Facilite le dépannage

4. **Flexibilité** :
   - Changement d'IP facile (modifier réservation)
   - Pas de reconfiguration sur l'appareil

5. **Conformité avec les politiques réseau** :
   - Toutes les IP gérées par DHCP (audit, sécurité)

**Configuration réservation** :
```
Console DHCP → Réservations → Nouvelle réservation
- Nom : IMP-COMPTA-01
- Adresse IP : 192.168.20.50
- Adresse MAC : 00:1A:2B:3C:4D:5E
- Description : Imprimante service comptabilité
```

**Barème (5 points)** :

| Critère | Points | Détails |
|---------|--------|---------||
| **Avantage 1 : Gestion centralisée** | 2 pts | Toutes config sur serveur DHCP |
| **Avantage 2 : Cohérence options** | 2 pts | DNS, passerelle automatiques |
| **Avantage 3 : Traçabilité/Flexibilité** | 1 pt | Logs, historique, modification facile |
| **Clarté explication** | +0.5 pt | Bonus : Explication claire et structurée |

**Erreurs pénalisantes** :
- ❌ "Plus rapide" (-1 pt) : Faux, même performance
- ❌ Confusion réservation/bail dynamique (-1 pt)

---

### ✔️ Question 1.5 (5 points) - Options DHCP à vérifier

**Réponse attendue** :

**3 options DHCP essentielles** :

1. **Option 003 - Routeur (Passerelle par défaut)** :
   - Valeur : `192.168.20.1`
   - Permet l'accès Internet et routage inter-VLAN
   - Sans cette option : Pas d'accès hors du sous-réseau local

2. **Option 006 - Serveurs DNS** :
   - Valeur : `192.168.10.10` (DNS interne) + `8.8.8.8` (DNS public)
   - Permet la résolution de noms
   - Sans cette option : Impossible d'accéder aux ressources par nom (ex: serveur-fichiers)

3. **Option 015 - Nom de domaine DNS** :
   - Valeur : `techsolutions.local`
   - Suffixe DNS automatique
   - Facilite l'accès aux ressources internes

**Vérification** :
```
Console DHCP → Étendue → Options d'étendue → Configurer les options
Cocher : 003 Routeur, 006 Serveurs DNS, 015 Nom de domaine DNS
```

**Sur le client (vérifier réception)** :
```cmd
ipconfig /all
```
Vérifier : Passerelle par défaut, Serveurs DNS, Suffixe DNS

**Barème (5 points)** :

| Critère | Points | Détails |
|---------|--------|---------||
| **Option 003 - Routeur** | 2 pts | Code + nom + valeur (192.168.20.1) |
| **Option 006 - Serveurs DNS** | 2 pts | Code + nom + valeur IP DNS |
| **Option 015 - Domaine DNS** | 1 pt | Code + nom (ou autre option pertinente) |
| **Explication utilité** | +0.5 pt | Bonus : Expliquer l'usage de chaque option |

**Alternatives acceptées** :
- Option 042 (NTP servers)
- Option 044 (WINS servers)
- Option 046 (NetBIOS node type)

**Erreurs pénalisantes** :
- ❌ Options sans numéros (-1 pt)
- ❌ Valeurs IP incohérentes (-1 pt)
- ❌ Moins de 2 options correctes (-2 pts)

---

## 🔧 INCIDENT 2 : SCRIPT POWERSHELL - CORRECTIONS (30 points)

### ✔️ Question 2.1 (15 points) - Script complet

**Réponse attendue** :

```powershell
# Import du module Active Directory
Import-Module ActiveDirectory

# Chemin du fichier CSV
$CSVPath = "C:\Scripts\nouveaux_users.csv"

# Mot de passe initial (à changer à la première connexion)
$Password = ConvertTo-SecureString "P@ssw0rd2025!" -AsPlainText -Force

# Import des utilisateurs depuis le CSV
$Users = Import-Csv -Path $CSVPath -Delimiter ","

# Boucle sur chaque utilisateur
foreach ($User in $Users) {
    
    # Récupération des champs
    $Prenom = $User.Prenom
    $Nom = $User.Nom
    $Service = $User.Service
    $Fonction = $User.Fonction
    
    # Génération du SamAccountName (prenom.nom en minuscules)
    $Sam = "$($Prenom).$($Nom)".ToLower()
    
    # Génération de l'UPN
    $UPN = "$Sam@techsolutions.local"
    
    # Nom complet
    $DisplayName = "$Prenom $Nom"
    
    # Chemin de l'OU
    $OUPath = "OU=$Service,OU=Utilisateurs,DC=techsolutions,DC=local"
    
    # Création de l'utilisateur
    New-ADUser -Name $DisplayName `
               -GivenName $Prenom `
               -Surname $Nom `
               -SamAccountName $Sam `
               -UserPrincipalName $UPN `
               -Path $OUPath `
               -AccountPassword $Password `
               -Enabled $true `
               -ChangePasswordAtLogon $true `
               -Title $Fonction `
               -Description "Créé automatiquement le $(Get-Date -Format 'dd/MM/yyyy')"
    
    Write-Host "Utilisateur créé : $DisplayName ($Sam)" -ForegroundColor Green
}

Write-Host "`nCréation terminée ! Total : $($Users.Count) utilisateurs" -ForegroundColor Cyan
```

**Points clés du script** :
- Import module AD
- Lecture CSV
- Boucle foreach
- Génération Sam/UPN
- New-ADUser avec tous les paramètres
- ChangePasswordAtLogon = $true
- Enabled = $true

**Barème (15 points)** :

| Critère | Points | Détails |
|---------|--------|---------||
| **Import-Module ActiveDirectory** | 1 pt | Ou vérification module disponible |
| **Import-Csv correct** | 2 pts | Chemin, délimiteur, variable |
| **Boucle foreach structurée** | 2 pts | Syntaxe correcte, itération sur $Users |
| **Génération SamAccountName** | 2 pts | Prenom.Nom en minuscules (`.ToLower()`) |
| **Génération UPN** | 1 pt | sam@techsolutions.local |
| **Construction OUPath dynamique** | 2 pts | "OU=$Service,OU=Utilisateurs,DC=..." |
| **New-ADUser complet** | 5 pts | Tous paramètres essentiels (Name, GivenName, Surname, Sam, UPN, Path, Password, Enabled) |
| **ChangePasswordAtLogon** | 1 pt | -ChangePasswordAtLogon $true |
| **Affichage/Log basique** | 1 pt | Write-Host ou Out-File |
| **Syntaxe PowerShell** | -2 pts | Pénalité si erreurs syntaxe graves |

**Bonus** :
- +0.5 : SecureString pour mot de passe
- +0.5 : Description avec date création

**Erreurs pénalisantes** :
- ❌ Pas de boucle (-5 pts) : Script ne traite qu'un utilisateur
- ❌ SamAccountName avec majuscules/accents (-2 pts) : Non conforme
- ❌ Utilisateurs créés désactivés (-2 pts) : -Enabled $false ou manquant
- ❌ Mot de passe en clair (-1 pt) : Faille de sécurité
- ❌ OUPath en dur (pas dynamique) (-2 pts)

---

### ✔️ Question 2.2 (5 points) - Gestion erreurs et log

**Réponse attendue** :

```powershell
# Fichier de log
$LogFile = "C:\Scripts\creation_users.log"

# Initialisation du compteur
$SuccessCount = 0
$ErrorCount = 0

# En-tête du log
"=== Création utilisateurs - $(Get-Date -Format 'dd/MM/yyyy HH:mm:ss') ===" | Out-File -FilePath $LogFile

foreach ($User in $Users) {
    
    $Prenom = $User.Prenom
    $Nom = $User.Nom
    $Service = $User.Service
    $Sam = "$($Prenom).$($Nom)".ToLower()
    $UPN = "$Sam@techsolutions.local"
    $DisplayName = "$Prenom $Nom"
    $OUPath = "OU=$Service,OU=Utilisateurs,DC=techsolutions,DC=local"
    
    Try {
        # Vérifier si l'utilisateur existe déjà
        $ExistingUser = Get-ADUser -Filter "SamAccountName -eq '$Sam'" -ErrorAction SilentlyContinue
        
        if ($ExistingUser) {
            throw "L'utilisateur $Sam existe déjà"
        }
        
        # Vérifier si l'OU existe
        $OUExists = Get-ADOrganizationalUnit -Filter "DistinguishedName -eq '$OUPath'" -ErrorAction SilentlyContinue
        
        if (-not $OUExists) {
            throw "L'OU $OUPath n'existe pas"
        }
        
        # Création de l'utilisateur
        New-ADUser -Name $DisplayName `
                   -GivenName $Prenom `
                   -Surname $Nom `
                   -SamAccountName $Sam `
                   -UserPrincipalName $UPN `
                   -Path $OUPath `
                   -AccountPassword $Password `
                   -Enabled $true `
                   -ChangePasswordAtLogon $true `
                   -ErrorAction Stop
        
        # Log succès
        $Message = "[SUCCÈS] $(Get-Date -Format 'HH:mm:ss') - Utilisateur créé : $DisplayName ($Sam)"
        Write-Host $Message -ForegroundColor Green
        $Message | Out-File -FilePath $LogFile -Append
        $SuccessCount++
        
    } Catch {
        # Log erreur
        $ErrorMessage = "[ERREUR] $(Get-Date -Format 'HH:mm:ss') - $DisplayName ($Sam) : $($_.Exception.Message)"
        Write-Host $ErrorMessage -ForegroundColor Red
        $ErrorMessage | Out-File -FilePath $LogFile -Append
        $ErrorCount++
    }
}

# Résumé dans le log
$Summary = "`n=== RÉSUMÉ ===`nSuccès : $SuccessCount`nErreurs : $ErrorCount`nTotal : $($Users.Count)"
Write-Host $Summary -ForegroundColor Cyan
$Summary | Out-File -FilePath $LogFile -Append
```

**Barème** :
- Try/Catch correctement placé : **2 pts**
- Vérifications (user existe, OU existe) : **2 pts**
- Log avec horodatage : **1 pt**

---

### ✔️ Question 2.3 (5 points) - Envoi email récapitulatif

**Réponse attendue** :

```powershell
# À ajouter à la fin du script

# Paramètres email
$From = "admin@techsolutions.local"
$To = "rh@techsolutions.local"
$Subject = "Rapport création utilisateurs - $(Get-Date -Format 'dd/MM/yyyy')"
$SMTPServer = "smtp.techsolutions.local"  # ou "mail.techsolutions.local"
$SMTPPort = 25

# Corps de l'email
$Body = @"
Bonjour,

Le script de création d'utilisateurs s'est terminé.

=== RÉSUMÉ ===
- Utilisateurs créés avec succès : $SuccessCount
- Erreurs rencontrées : $ErrorCount
- Total traité : $($Users.Count)

Le fichier de log complet est disponible ici : $LogFile

Cordialement,
Système automatisé
"@

# Envoi de l'email
Try {
    Send-MailMessage -From $From `
                     -To $To `
                     -Subject $Subject `
                     -Body $Body `
                     -SmtpServer $SMTPServer `
                     -Port $SMTPPort `
                     -Encoding UTF8
    
    Write-Host "`nEmail envoyé à $To" -ForegroundColor Green
    
} Catch {
    Write-Host "`nErreur envoi email : $($_.Exception.Message)" -ForegroundColor Red
}
```

**Avec pièce jointe (log)** :
```powershell
Send-MailMessage -From $From `
                 -To $To `
                 -Subject $Subject `
                 -Body $Body `
                 -Attachments $LogFile `
                 -SmtpServer $SMTPServer `
                 -Port $SMTPPort `
                 -Encoding UTF8
```

**Barème** :
- Send-MailMessage correcte : **3 pts**
- Corps d'email avec résumé : **2 pts**

---

### ✔️ Question 2.4 (3 points) - Remplacement accents

**Réponse attendue** :

**Méthode 1 : Fonction personnalisée**
```powershell
function Remove-Accents {
    param([string]$Text)
    
    $Text = $Text.Replace('é','e').Replace('è','e').Replace('ê','e')
    $Text = $Text.Replace('à','a').Replace('â','a')
    $Text = $Text.Replace('ù','u').Replace('û','u')
    $Text = $Text.Replace('ô','o').Replace('ö','o')
    $Text = $Text.Replace('î','i').Replace('ï','i')
    $Text = $Text.Replace('ç','c')
    $Text = $Text.Replace('É','E').Replace('È','E')
    $Text = $Text.Replace('À','A').Replace('Ç','C')
    
    return $Text
}

# Utilisation
$Sam = Remove-Accents "$($Prenom).$($Nom)".ToLower()
```

**Méthode 2 : Regex avec normalisation**
```powershell
function Remove-Accents {
    param([string]$Text)
    
    $Normalized = $Text.Normalize([Text.NormalizationForm]::FormD)
    $Result = $Normalized -replace '\p{M}', ''
    
    return $Result
}
```

**Méthode 3 : Table de correspondance**
```powershell
$AccentMap = @{
    'é' = 'e'; 'è' = 'e'; 'ê' = 'e'; 'ë' = 'e'
    'à' = 'a'; 'â' = 'a'; 'ä' = 'a'
    'ù' = 'u'; 'û' = 'u'; 'ü' = 'u'
    'ô' = 'o'; 'ö' = 'o'
    'î' = 'i'; 'ï' = 'i'
    'ç' = 'c'
}

function Remove-Accents {
    param([string]$Text)
    
    foreach ($Key in $AccentMap.Keys) {
        $Text = $Text.Replace($Key, $AccentMap[$Key])
    }
    
    return $Text
}
```

**Barème** :
- Fonction correcte avec .Replace() : **2 pts**
- Gère plusieurs accents : **1 pt**

---

### ✔️ Question 2.5 (2 points) - Tester existence OU

**Réponse attendue** :

**Cmdlet** : `Get-ADOrganizationalUnit`

**Exemple d'utilisation** :
```powershell
# Tester si l'OU existe
$OUPath = "OU=Commercial,OU=Utilisateurs,DC=techsolutions,DC=local"

$OUExists = Get-ADOrganizationalUnit -Filter "DistinguishedName -eq '$OUPath'" -ErrorAction SilentlyContinue

if ($OUExists) {
    Write-Host "L'OU existe" -ForegroundColor Green
} else {
    Write-Host "L'OU n'existe pas" -ForegroundColor Red
}
```

**Alternative avec Try/Catch** :
```powershell
Try {
    Get-ADOrganizationalUnit -Identity $OUPath -ErrorAction Stop
    # L'OU existe
} Catch {
    # L'OU n'existe pas
    Write-Error "L'OU $OUPath n'existe pas"
}
```

**Barème** :
- Cmdlet correcte : **2 pts**

---

**Suite dans le fichier EXAMEN-BLANC-1-CORRECTIONS-PARTIE2.md...**


---

# ✅ EXAMEN BLANC 1 - CORRECTIONS DÉTAILLÉES
## Partie 1 : MSP Écrite (suite)

---

## 🌐 INCIDENT 3 : PROBLÈME RÉSEAU/VLAN - CORRECTIONS (40 points)

### ✔️ Question 3.1 (5 points) - Cause racine du problème

**Réponse attendue** :

La cause racine est une **mauvaise configuration des ports switchs** : les postes du service Comptabilité sont connectés à des ports configurés en **VLAN 20** (Commercial) au lieu du **VLAN 30** (Comptabilité).

**Conséquence** :
- Les postes reçoivent des adresses IP du range du VLAN 20 : `192.168.20.x`
- Ils ne peuvent pas atteindre le serveur de fichiers `192.168.30.50` (VLAN 30)
- Le serveur et les postes sont dans des **sous-réseaux différents**
- La communication nécessite un routage, qui n'est pas configuré ou autorisé

**Diagnostic** :
```
Sur le PC : ipconfig → IP 192.168.20.105 (VLAN 20)
Ping 192.168.30.50 → Échec (pas de route)
```

**Barème** :
- Identification "ports en mauvais VLAN" : **3 pts**
- Explication isolation réseau : **2 pts**

---

### ✔️ Question 3.2 (10 points) - Configuration switch Cisco

**Réponse attendue** :

**Connexion au switch** :
```cisco
enable
configure terminal
```

**1) Identifier les ports concernés** :
```cisco
show vlan brief
! Repérer les ports Fa0/10 à Fa0/15 (exemple)
```

**2) Retirer les ports du VLAN 20** :
```cisco
interface range FastEthernet0/10 - 15
no switchport access vlan 20
```

**3) Affecter les ports au VLAN 30** :
```cisco
interface range FastEthernet0/10 - 15
switchport mode access
switchport access vlan 30
spanning-tree portfast
description "Postes Comptabilite"
```

**4) Vérifier la configuration** :
```cisco
end
show vlan id 30
show running-config interface Fa0/10
```

**5) Sauvegarder** :
```cisco
copy running-config startup-config
! ou
write memory
```

**Sur les postes** (après reconfiguration) :
```cmd
ipconfig /release
ipconfig /renew
ipconfig
ping 192.168.30.50
```

**Résultat attendu** :
- Les postes reçoivent des IP `192.168.30.x`
- Le ping vers `192.168.30.50` fonctionne

**Barème** :
- Interface range : **2 pts**
- Switchport access vlan 30 : **4 pts**
- Vérification (show vlan) : **2 pts**
- Sauvegarde (copy run start) : **2 pts**

---

### ✔️ Question 3.3 (8 points) - Configuration relais DHCP

**Réponse attendue** :

**Problème** :
Les requêtes DHCP sont des **broadcasts** (255.255.255.255), qui ne traversent **pas** les routeurs/VLANs. Les postes du VLAN 30 ne peuvent pas atteindre le serveur DHCP situé dans un autre VLAN.

**Solution** : Configurer un **DHCP Relay Agent** (ip helper-address) sur le routeur ou switch L3.

**Configuration sur le routeur/switch L3** :

```cisco
enable
configure terminal

! Configuration sur l'interface SVI du VLAN 30
interface vlan 30
ip helper-address 192.168.10.10
! (adresse IP du serveur DHCP)

exit
write memory
```

**Explication** :
- `ip helper-address` transforme les broadcasts DHCP en **unicasts** dirigés vers le serveur DHCP
- Le routeur relaie les requêtes DHCP du VLAN 30 vers le serveur DHCP (VLAN 10)
- Le serveur répond au routeur, qui retransmet aux clients

**Vérification** :
```cisco
show running-config interface vlan 30
show ip interface vlan 30
```

**Sur les postes** :
```cmd
ipconfig /release
ipconfig /renew
ipconfig /all
```
Vérifier : Serveur DHCP = `192.168.10.10`

**Configuration DHCP côté serveur** :
Il faut créer une **étendue pour le VLAN 30** :
- Plage : `192.168.30.100 - 192.168.30.150`
- Masque : `255.255.255.0`
- Passerelle : `192.168.30.1`
- DNS : `192.168.10.10`

**Barème** :
- Explication broadcast + relais : **3 pts**
- Commande ip helper-address : **4 pts**
- Création étendue DHCP : **1 pt**

---

### ✔️ Question 3.4 (12 points) - ACL pour sécuriser

**Réponse attendue** :

**Objectif** : Autoriser uniquement :
- **SMB** (partages fichiers) : TCP 445
- **DNS** : UDP/TCP 53
- Bloquer tout le reste

**Configuration ACL étendue** :

```cisco
enable
configure terminal

! Création de l'ACL
ip access-list extended ACL_VLAN30_TO_SERVER

 ! Autoriser SMB (partage fichiers)
 permit tcp 192.168.30.0 0.0.0.255 host 192.168.30.50 eq 445
 
 ! Autoriser DNS
 permit udp 192.168.30.0 0.0.0.255 host 192.168.30.50 eq 53
 permit tcp 192.168.30.0 0.0.0.255 host 192.168.30.50 eq 53
 
 ! Autoriser ICMP (ping pour diagnostic)
 permit icmp 192.168.30.0 0.0.0.255 host 192.168.30.50
 
 ! Bloquer tout le reste (implicite, mais on peut l'écrire)
 deny ip any any log

exit

! Application sur l'interface SVI du VLAN 30 (en sortie)
interface vlan 30
ip access-group ACL_VLAN30_TO_SERVER out

exit
write memory
```

**Explication des lignes** :

1. **permit tcp ... eq 445** : Autorise SMB/CIFS (partages Windows)
2. **permit udp/tcp ... eq 53** : Autorise DNS (requêtes)
3. **permit icmp** : Autorise ping (diagnostic)
4. **deny ip any any log** : Bloque tout le reste et logue les tentatives

**Wildcard mask** :
- `0.0.0.255` = inverse du masque `/24`
- `0` = doit correspondre exactement
- `255` = n'importe quelle valeur

**Vérification** :
```cisco
show access-lists ACL_VLAN30_TO_SERVER
show ip interface vlan 30
```

**Test** :
- ✅ Depuis un poste VLAN 30 : Accès `\\192.168.30.50\partage` → OK
- ✅ Ping `192.168.30.50` → OK
- ❌ Tentative SSH vers le serveur (port 22) → Bloqué
- ❌ Tentative HTTP vers le serveur (port 80) → Bloqué

**Barème** :
- Création ACL étendue : **2 pts**
- Permit SMB (tcp 445) : **3 pts**
- Permit DNS (udp/tcp 53) : **2 pts**
- Application sur interface : **3 pts**
- Vérification/test : **2 pts**

---

### ✔️ Question 3.5 (5 points) - Documentation

**Réponse attendue** :

**Éléments à documenter** :

1. **Topologie réseau** :
   - Schéma des VLANs (10, 20, 30)
   - Plans d'adressage par VLAN
   - Équipements (switch, routeur, serveurs)

2. **Configuration switch** :
   - Ports affectés à chaque VLAN
   - Ports trunk entre équipements
   - STP (Spanning Tree Protocol)

3. **Configuration DHCP** :
   - Étendues par VLAN
   - Options (passerelle, DNS)
   - Relais DHCP (ip helper-address)

4. **Règles ACL** :
   - Nom de l'ACL
   - Règles détaillées (permit/deny)
   - Interfaces d'application (in/out)
   - Justification de chaque règle

5. **Procédures de changement** :
   - Process pour changer un poste de VLAN
   - Validation des configurations
   - Rollback en cas de problème

**Format documentation** :

```markdown
# DOCUMENTATION RÉSEAU - TechSolutions SAS

## 1. Plan d'adressage

| VLAN | Nom          | Réseau          | Plage DHCP       | Passerelle   |
|------|--------------|-----------------|------------------|--------------|
| 10   | Infra        | 192.168.10.0/24 | .50-.100         | 192.168.10.1 |
| 20   | Commercial   | 192.168.20.0/24 | .100-.150        | 192.168.20.1 |
| 30   | Comptabilité | 192.168.30.0/24 | .100-.150        | 192.168.30.1 |

## 2. Affectation des ports switch

### SW-LYON-01

| Ports          | VLAN | Service      | Description        |
|----------------|------|--------------|--------------------|
| Fa0/1-9        | 20   | Commercial   | Postes commerciaux |
| Fa0/10-15      | 30   | Comptabilité | Postes comptables  |
| Fa0/24         | 10   | Infra        | Vers serveur DHCP  |
| Gi0/1          | Trunk| -            | Vers routeur       |

## 3. Configuration DHCP Relay

```cisco
interface vlan 30
 ip helper-address 192.168.10.10
```

## 4. ACL de sécurité

### ACL_VLAN30_TO_SERVER
- **Objectif** : Restreindre accès serveur fichiers
- **Autorisé** : SMB (445), DNS (53), ICMP
- **Application** : Interface VLAN 30 (out)
```

**Barème** :
- Topologie/adressage : **2 pts**
- Configuration switch/ports : **1 pt**
- ACL et justifications : **1 pt**
- Format clair et complet : **1 pt**

---

---

# PARTIE 2 : QUESTIONNAIRE TECHNIQUE (100 points)

---

## 📋 SECTION A : LINUX (20 points)

### ✔️ Question A1 (4 points) - Permissions fichier

**Réponse attendue** :

```
-rwxr-x---
```

**Décomposition** :
- `-` : Fichier régulier (pas un dossier `d`)
- `rwx` : Propriétaire (user) → Lecture, Écriture, Exécution
- `r-x` : Groupe (group) → Lecture, Exécution (pas d'écriture)
- `---` : Autres (others) → Aucun droit

**Notation octale** : `750`
- Propriétaire : 7 (4+2+1 = rwx)
- Groupe : 5 (4+0+1 = r-x)
- Autres : 0 (0+0+0 = ---)

**Barème** :
- Propriétaire (rwx) : **2 pts**
- Groupe (r-x) : **1 pt**
- Autres (---) : **1 pt**

---

### ✔️ Question A2 (4 points) - Trouver fichiers volumineux

**Réponse attendue** :

**Commande** :
```bash
find /var/log -type f -size +100M
```

**Explication** :
- `find` : Recherche de fichiers
- `/var/log` : Répertoire de départ
- `-type f` : Fichiers uniquement (pas les dossiers)
- `-size +100M` : Taille supérieure à 100 Mo

**Variantes** :

**Avec affichage taille** :
```bash
find /var/log -type f -size +100M -exec ls -lh {} \;
```

**Trier par taille** :
```bash
find /var/log -type f -size +100M -exec ls -lh {} \; | sort -k5 -hr
```

**Avec du** :
```bash
find /var/log -type f -size +100M -exec du -h {} \; | sort -hr
```

**Alternative avec du seul** :
```bash
du -ah /var/log | awk '$1 ~ /M$/ && $1+0 > 100'
```

**Barème** :
- Commande find : **2 pts**
- -type f : **1 pt**
- -size +100M : **1 pt**

---

### ✔️ Question A3 (4 points) - Redirections Linux

**Réponse attendue** :

**1) `>` : Redirection standard output (stdout)** :
- Redirige la **sortie standard** vers un fichier
- **Écrase** le contenu du fichier s'il existe
- Équivalent à `1>`

**Exemple** :
```bash
ls -l > liste.txt
# La sortie de ls est écrite dans liste.txt (écrasement)
```

**2) `>>` : Redirection en mode ajout (append)** :
- Redirige la **sortie standard** vers un fichier
- **Ajoute** à la fin du fichier (sans écraser)

**Exemple** :
```bash
echo "Ligne 1" > fichier.txt   # Crée ou écrase
echo "Ligne 2" >> fichier.txt  # Ajoute à la fin
```

**3) `2>` : Redirection erreur standard (stderr)** :
- Redirige les **erreurs** (stderr) vers un fichier
- Les messages d'erreur ne s'affichent plus à l'écran

**Exemple** :
```bash
find / -name "test" 2> /dev/null
# Les erreurs "Permission denied" sont supprimées
```

**Exemples combinés** :

```bash
# Rediriger stdout ET stderr dans le même fichier
commande > output.txt 2>&1

# Rediriger stdout et stderr séparément
commande > output.txt 2> errors.txt

# Ignorer les erreurs
commande 2> /dev/null

# Tout capturer
commande &> all.txt  # Bash 4+
```

**Barème** :
- `>` : **1 pt**
- `>>` : **1 pt**
- `2>` : **2 pts**

---

### ✔️ Question A4 (4 points) - Processus en arrière-plan

**Réponse attendue** :

**1) Lancer en arrière-plan dès le départ** :
```bash
./script.sh &
```
Le `&` à la fin lance le processus en background.

**2) Mettre un processus en arrière-plan (déjà lancé)** :
```bash
# Suspendre le processus en cours
Ctrl + Z

# Reprendre en arrière-plan
bg
```

**3) Lister les processus en arrière-plan** :
```bash
jobs
```

**Sortie exemple** :
```
[1]+  Running   ./script.sh &
[2]-  Stopped   vim fichier.txt
```

**4) Ramener en premier plan** :
```bash
fg %1
# Ou simplement
fg
```

**5) Détacher complètement (survit à la fermeture du terminal)** :
```bash
nohup ./script.sh &
# Les sorties sont dans nohup.out
```

**Barème** :
- Lancer avec & : **2 pts**
- Ctrl+Z puis bg : **1 pt**
- jobs pour lister : **1 pt**

---

### ✔️ Question A5 (4 points) - Grep avec regex

**Réponse attendue** :

**Commande** :
```bash
grep -E '^Failed password' /var/log/auth.log | wc -l
```

**Explication** :
- `grep` : Recherche de motif
- `-E` : Regex étendue (ou `egrep`)
- `^Failed password` : Lignes commençant par "Failed password"
- `/var/log/auth.log` : Fichier des logs d'authentification
- `wc -l` : Compte le nombre de lignes

**Variantes** :

**Avec affichage des IP sources** :
```bash
grep -E '^Failed password' /var/log/auth.log | awk '{print $(NF-3)}' | sort | uniq -c | sort -rn
```

**Avec date** :
```bash
grep -E '^[A-Z][a-z]{2} [ 0-9]{2}.*Failed password' /var/log/auth.log
```

**Filtrer par utilisateur** :
```bash
grep -E '^Failed password for (root|admin)' /var/log/auth.log
```

**Dernières 24h (avec journalctl)** :
```bash
journalctl -u ssh --since "24 hours ago" | grep "Failed password" | wc -l
```

**Barème** :
- grep avec motif : **2 pts**
- wc -l pour compter : **1 pt**
- Fichier /var/log/auth.log : **1 pt**

---

## 🌐 SECTION B : RÉSEAU (25 points)

### ✔️ Question B1 (5 points) - Subnetting

**Réseau** : `172.16.50.0/23`

**Réponse attendue** :

**1) Masque de sous-réseau** :
- `/23` = **255.255.254.0**
- Binaire : `11111111.11111111.11111110.00000000`

**2) Nombre d'hôtes** :
- Bits hôtes : 32 - 23 = **9 bits**
- Formule : 2^9 - 2 = 512 - 2 = **510 hôtes**
  - `-2` : Exclure adresse réseau et broadcast

**3) Plage d'adresses IP utilisables** :
- Réseau : `172.16.50.0` (non utilisable)
- Première IP : **172.16.50.1**
- Dernière IP : **172.16.51.254**
- Broadcast : `172.16.51.255` (non utilisable)

**Calcul détaillé** :
- `/23` = **2 réseaux /24** fusionnés
- 172.16.50.0 à 172.16.50.255 (256 IP)
- 172.16.51.0 à 172.16.51.255 (256 IP)
- Total : 512 IP (dont 510 utilisables)

**Barème** :
- Masque correct : **2 pts**
- Nombre d'hôtes : **2 pts**
- Plage IP : **1 pt**

---

### ✔️ Question B2 (5 points) - VLSM

**Réponse attendue** :

**Objectif** : Sous-diviser `10.10.0.0/16` pour :
- Siège (500 hôtes)
- Agence A (120 hôtes)
- Agence B (30 hôtes)

**Méthode** : Allouer du **plus grand au plus petit**

**1) Siège - 500 hôtes** :
- Besoin : 500 hôtes
- 2^9 = 512 → Besoin de **9 bits hôtes**
- Préfixe : 32 - 9 = `/23`
- **Réseau** : `10.10.0.0/23`
- Plage : `10.10.0.1` à `10.10.1.254`
- Hôtes : 510

**2) Agence A - 120 hôtes** :
- Besoin : 120 hôtes
- 2^7 = 128 → Besoin de **7 bits hôtes**
- Préfixe : 32 - 7 = `/25`
- **Réseau** : `10.10.2.0/25`
- Plage : `10.10.2.1` à `10.10.2.126`
- Hôtes : 126

**3) Agence B - 30 hôtes** :
- Besoin : 30 hôtes
- 2^5 = 32 → Besoin de **5 bits hôtes**
- Préfixe : 32 - 5 = `/27`
- **Réseau** : `10.10.2.128/27`
- Plage : `10.10.2.129` à `10.10.2.158`
- Hôtes : 30

**Tableau récapitulatif** :

| Site     | Hôtes | Réseau           | Masque          | Plage IP              | Broadcast      |
|----------|-------|------------------|-----------------|-----------------------|----------------|
| Siège    | 500   | 10.10.0.0/23     | 255.255.254.0   | 10.10.0.1-10.10.1.254 | 10.10.1.255    |
| Agence A | 120   | 10.10.2.0/25     | 255.255.255.128 | 10.10.2.1-10.10.2.126 | 10.10.2.127    |
| Agence B | 30    | 10.10.2.128/27   | 255.255.255.224 | 10.10.2.129-.158      | 10.10.2.159    |

**Barème** :
- Siège /23 : **2 pts**
- Agence A /25 : **2 pts**
- Agence B /27 : **1 pt**

---

### ✔️ Question B3 (5 points) - VLAN Trunk vs Access

**Réponse attendue** :

**1) Port ACCESS** :
- **Définition** : Port configuré pour un **seul VLAN**
- **Usage** : Connexion des équipements terminaux (PC, imprimantes, serveurs)
- **Trames** : Non taguées (untagged)
- L'équipement connecté n'a pas connaissance du VLAN

**Configuration Cisco** :
```cisco
interface FastEthernet0/10
 switchport mode access
 switchport access vlan 20
```

**2) Port TRUNK** :
- **Définition** : Port transportant **plusieurs VLANs** simultanément
- **Usage** : Connexion entre switch, ou switch ↔ routeur
- **Trames** : Taguées avec identifiant VLAN (802.1Q)
- Transporte tous les VLANs autorisés

**Configuration Cisco** :
```cisco
interface GigabitEthernet0/1
 switchport mode trunk
 switchport trunk allowed vlan 10,20,30
 switchport trunk native vlan 1
```

**Tableau comparatif** :

| Caractéristique      | Port ACCESS            | Port TRUNK                  |
|----------------------|------------------------|-----------------------------|
| **VLANs**            | 1 seul                 | Plusieurs                   |
| **Équipements**      | PC, serveurs, imprim.  | Switch, routeur, hyperv.    |
| **Trames**           | Non taguées            | Taguées (802.1Q)            |
| **Protocole**        | -                      | 802.1Q ou ISL (legacy)      |
| **Configuration**    | `switchport access`    | `switchport trunk`          |

**Native VLAN** :
- Sur un trunk, le **Native VLAN** transporte des trames **non taguées**
- Par défaut : VLAN 1
- **Recommandation** : Changer le Native VLAN (sécurité)

**Barème** :
- Port Access défini : **2 pts**
- Port Trunk défini : **2 pts**
- Explication tagging : **1 pt**

---

### ✔️ Question B4 (5 points) - Routage statique

**Réponse attendue** :

**Topologie** :
```
R1 (192.168.1.1/24) ---- Internet ---- R2 (10.0.0.1/24)
         |                                      |
    LAN A (192.168.1.0/24)              LAN B (10.0.0.0/24)
```

**Sur R1 (pour atteindre 10.0.0.0/24)** :
```cisco
ip route 10.0.0.0 255.255.255.0 <IP_Interface_R2>
```

**Exemple** : Si R2 est accessible via `203.0.113.2` :
```cisco
ip route 10.0.0.0 255.255.255.0 203.0.113.2
```

**Sur R2 (pour atteindre 192.168.1.0/24)** :
```cisco
ip route 192.168.1.0 255.255.255.0 <IP_Interface_R1>
```

**Exemple** :
```cisco
ip route 192.168.1.0 255.255.255.0 203.0.113.1
```

**Vérification** :
```cisco
show ip route
show ip route static
```

**Route par défaut** (si nécessaire) :
```cisco
ip route 0.0.0.0 0.0.0.0 <Gateway_Internet>
```

**Barème** :
- Commande ip route : **3 pts**
- Syntaxe correcte (réseau masque gateway) : **2 pts**

---

### ✔️ Question B5 (5 points) - DNS vs DHCP

**Réponse attendue** :

**1) DNS (Domain Name System)** :

**Rôle** :
- **Résolution de noms** : Convertit noms de domaine ↔ adresses IP
- Exemple : `www.google.com` → `142.250.185.78`

**Fonctionnement** :
1. Client envoie requête DNS
2. Serveur DNS interroge sa base (zone) ou serveurs racines
3. Réponse avec IP correspondante

**Enregistrements principaux** :
- **A** : Nom → IPv4
- **AAAA** : Nom → IPv6
- **CNAME** : Alias (redirection)
- **MX** : Serveur mail
- **PTR** : IP → Nom (reverse)
- **NS** : Serveur DNS autoritaire
- **SOA** : Autorité de zone

**Port** : UDP/TCP **53**

---

**2) DHCP (Dynamic Host Configuration Protocol)** :

**Rôle** :
- **Attribution automatique des paramètres réseau** :
  - Adresse IP
  - Masque de sous-réseau
  - Passerelle par défaut
  - Serveurs DNS
  - Nom de domaine

**Fonctionnement (DORA)** :
1. **Discover** : Client cherche serveur DHCP (broadcast)
2. **Offer** : Serveur propose une IP
3. **Request** : Client demande cette IP
4. **Acknowledge** : Serveur confirme

**Port** : UDP **67** (serveur) et **68** (client)

---

**Tableau comparatif** :

| Caractéristique    | DNS                      | DHCP                        |
|--------------------|--------------------------|------------------------------|
| **Rôle**           | Résolution noms → IP     | Attribution config réseau    |
| **Protocole**      | UDP/TCP                  | UDP                          |
| **Port**           | 53                       | 67/68                        |
| **Type requête**   | Unicast                  | Broadcast puis Unicast       |
| **Durée**          | Réponse instantanée      | Bail (lease) 1-8 jours       |
| **Exemples**       | Bind9, Microsoft DNS     | ISC DHCP, Microsoft DHCP     |

**Complémentarité** :
- DHCP attribue **l'IP** et l'adresse du **serveur DNS**
- DNS permet ensuite de résoudre les noms de domaine

**Barème** :
- Rôle DNS : **2 pts**
- Rôle DHCP : **2 pts**
- Différence claire : **1 pt**

---

## 🪟 SECTION C : WINDOWS / ACTIVE DIRECTORY (25 points)

### ✔️ Question C1 (5 points) - GPO et héritage LSDOU

**Réponse attendue** :

**Ordre d'application des GPO : LSDOU**

1. **L = Local** : GPO locales (sur la machine)
2. **S = Site** : GPO liées au site AD
3. **D = Domain** : GPO liées au domaine
4. **OU = Organizational Unit** : GPO liées aux OU

**Principe** : Les GPO appliquées **en dernier** ont la **priorité** (écrasent les précédentes).

---

**Exemple pratique** :

**Structure AD** :
```
Domaine : techsolutions.local
  └─ OU=Paris
      └─ OU=Commercial
```

**GPO configurées** :
1. **GPO Locale** : Fond d'écran bleu
2. **GPO Domaine** : Fond d'écran rouge
3. **GPO OU Paris** : Fond d'écran vert
4. **GPO OU Commercial** : Fond d'écran jaune

**Résultat pour un utilisateur dans OU Commercial** :
- Fond d'écran = **Jaune** (dernière GPO appliquée)

---

**Options spéciales** :

**1) Enforced (Application forcée)** :
- La GPO **ne peut pas être bloquée** par les enfants
- Priorité maximale
- Icône cadenas 🔒

**2) Block Inheritance (Bloquer l'héritage)** :
- Les GPO parentes **ne s'appliquent pas**
- Sauf si elles sont **Enforced**

**Ordre final avec Enforced** :
1. Local
2. Site
3. Domain
4. OU (du + haut au + bas)
5. **GPO Enforced** (priorité absolue)

---

**Commande pour voir l'ordre** :
```cmd
gpresult /r
gpresult /h rapport.html
```

**Barème** :
- LSDOU expliqué : **3 pts**
- Exemple ou ordre priorité : **2 pts**

---

### ✔️ Question C2 (5 points) - Rôles FSMO

**Réponse attendue** :

**FSMO = Flexible Single Master Operations**

5 rôles de contrôleur de domaine **uniques** (1 seul DC par rôle) :

---

**1) Schema Master (Maître de schéma)** :
- **Scope** : Forêt
- **Rôle** : Modifications du schéma AD
- **Exemple** : Ajouter nouvelle classe d'objets, attributs

**2) Domain Naming Master (Maître d'attribution de noms)** :
- **Scope** : Forêt
- **Rôle** : Ajout/suppression de domaines dans la forêt
- **Exemple** : Créer un nouveau domaine enfant

---

**3) PDC Emulator (Émulateur PDC)** :
- **Scope** : Domaine
- **Rôle** :
  - Synchronisation de l'heure (time source)
  - Gestion des changements de mots de passe
  - Compatibilité avec anciens clients (NT4)
  - Verrouillage de comptes
- **Critique** : Rôle le plus sollicité

**4) RID Master (Maître RID)** :
- **Scope** : Domaine
- **Rôle** : Attribution des pools de RID (Relative ID)
- **Explication** : Chaque objet AD a un SID unique = Domain SID + RID
- Le RID Master distribue des blocs de 500 RID aux DC

**5) Infrastructure Master (Maître d'infrastructure)** :
- **Scope** : Domaine
- **Rôle** : Maintien des références entre domaines
- **Exemple** : Utilisateur domaine A dans groupe domaine B
- **Contrainte** : Ne doit **PAS** être sur un serveur Global Catalog (sauf si tous les DC sont GC)

---

**Commandes pour identifier les rôles** :

**En PowerShell** :
```powershell
Get-ADDomain | Select-Object PDCEmulator, RIDMaster, InfrastructureMaster
Get-ADForest | Select-Object SchemaMaster, DomainNamingMaster
```

**En cmd** :
```cmd
netdom query fsmo
```

**Transférer un rôle** :
```powershell
Move-ADDirectoryServerOperationMasterRole -Identity "DC02" -OperationMasterRole PDCEmulator
```

**Barème** :
- 5 rôles nommés : **3 pts**
- Explication 2-3 rôles : **2 pts**

---

### ✔️ Question C3 (5 points) - Groupes globaux vs locaux

**Réponse attendue** :

**1) Groupe Local de domaine (Domain Local)** :

**Portée** : **Un seul domaine**

**Contenu** :
- Utilisateurs de **n'importe quel domaine** (de la forêt)
- Groupes globaux de **n'importe quel domaine**
- Groupes universels

**Usage** :
- Assigner des **permissions** sur des ressources du domaine
- Exemple : Accès à un partage de fichiers

**Symbole** : 🔵 (cercle avec flèche vers le bas)

---

**2) Groupe Global** :

**Portée** : **Toute la forêt**

**Contenu** :
- Utilisateurs du **même domaine uniquement**
- Groupes globaux du **même domaine**

**Usage** :
- **Regrouper des utilisateurs** par rôle/service
- Peut être membre de groupes locaux d'autres domaines
- Exemple : "Commerciaux", "RH"

**Symbole** : 🌍 (globe)

---

**3) Groupe Universel** :

**Portée** : **Toute la forêt**

**Contenu** :
- Utilisateurs de **n'importe quel domaine**
- Groupes globaux/universels de **n'importe quel domaine**

**Usage** :
- Multi-domaines
- **Attention** : Répliqué sur tous les Global Catalog (impact perf)

---

**Stratégie A-G-DL-P** :

**A** = **A**ccounts (Comptes utilisateurs)  
**G** = **G**lobal groups (Groupes globaux)  
**DL** = **D**omain **L**ocal groups (Groupes locaux de domaine)  
**P** = **P**ermissions (sur ressources)

**Exemple** :
1. Créer utilisateurs : `jdupont`, `mmartin`
2. Les mettre dans groupe **Global** : `G_Commerciaux`
3. Mettre `G_Commerciaux` dans groupe **Local** : `DL_Acces_Partage_Commercial`
4. Donner permissions NTFS au groupe `DL_Acces_Partage_Commercial`

**Avantages** :
- Gestion centralisée
- Réutilisabilité
- Scalabilité multi-domaines

**Barème** :
- Groupe Local défini : **2 pts**
- Groupe Global défini : **2 pts**
- Différence usage : **1 pt**

---

### ✔️ Question C4 (5 points) - Profils itinérants

**Réponse attendue** :

**Profil itinérant (Roaming Profile)** :
- Profil utilisateur **stocké sur un serveur de fichiers**
- **Synchronisé** à chaque ouverture/fermeture de session
- L'utilisateur retrouve **son environnement** sur n'importe quel PC

**Avantages** :
1. **Mobilité** : Même environnement sur tous les PC
2. **Sauvegarde centralisée** : Profils sur le serveur (sauvegardés)
3. **Gestion IT** : Redirection facile, quotas

**Inconvénients** :
1. **Temps de connexion/déconnexion** : Synchronisation lente (gros profils)
2. **Trafic réseau** : Upload/download à chaque session
3. **Espace disque serveur** : 1 profil complet par utilisateur
4. **Problèmes de corruption** : Si coupure réseau pendant sync

---

**Configuration** :

**1) Créer le partage sur le serveur** :
```
Partage : \\SRV-FICHIERS\Profils$
Permissions NTFS : Utilisateurs = Contrôle total sur leur sous-dossier
```

**2) Configurer dans AD** :
```
Propriétés utilisateur → Profil
Chemin du profil : \\SRV-FICHIERS\Profils$\%username%
```
`%username%` = Variable remplacée automatiquement

---

**Alternative moderne : Redirection de dossiers** :

Au lieu de tout synchroniser, rediriger **seulement** :
- Documents
- Bureau
- Favoris
- AppData

**Avantage** :
- Accès direct aux fichiers (pas de sync)
- Connexion/déconnexion rapides

**Configuration par GPO** :
```
Configuration utilisateur → Stratégies → Paramètres Windows
→ Redirection de dossiers → Documents
→ Paramètres : De base - Rediriger les dossiers de tous les utilisateurs
→ Emplacement : \\SRV-FICHIERS\Redirections$\%username%\Documents
```

**Barème** :
- Définition profil itinérant : **2 pts**
- 1 avantage : **1 pt**
- 1 inconvénient : **2 pts**

---

### ✔️ Question C5 (5 points) - Tombstone lifetime

**Réponse attendue** :

**Tombstone** :
- Objet AD **supprimé** mais pas encore définitivement effacé
- Marqué comme "deleted" pendant une période
- Permet la **réplication de la suppression** sur tous les DC

**Tombstone Lifetime** :
- **Durée de vie** d'un objet tombstone
- Par défaut : **180 jours** (Windows Server 2003 SP1+)
- Anciennement : **60 jours** (Windows 2000/2003)

---

**Fonctionnement** :

1. Objet supprimé → Passe en état **tombstone**
2. Réplication de la suppression sur tous les DC
3. Après **180 jours** : Suppression définitive (garbage collection)
4. **Impossible de restaurer** après expiration

---

**Importance pour la restauration** :

**Restauration possible uniquement pendant le Tombstone Lifetime** :

1. **Restauration autoritaire** (avant expiration) :
```cmd
ntdsutil
activate instance ntds
authoritative restore
restore object "CN=JDupont,OU=Users,DC=techsolutions,DC=local"
```

2. **Après expiration** :
- Restauration **impossible**
- Nécessite sauvegarde complète AD

---

**Vérifier le Tombstone Lifetime** :

**PowerShell** :
```powershell
Get-ADObject -Identity "CN=Directory Service,CN=Windows NT,CN=Services,CN=Configuration,DC=techsolutions,DC=local" -Properties tombstoneLifetime

# Modifier (exemple : 365 jours)
Set-ADObject -Identity "CN=Directory Service,CN=Windows NT,CN=Services,CN=Configuration,DC=techsolutions,DC=local" -Replace @{tombstoneLifetime=365}
```

**Recommandations** :
- **Ne jamais laisser un DC déconnecté > 180 jours**
- Sinon : Le DC devient **non-réintégrable** (risque d'objets "Lingering Objects")
- Solution : Réinstallation complète du DC

---

**Corbeille AD (Recycle Bin)** :

Depuis **Windows Server 2008 R2** :
- Permet restauration **complète** (avec tous les attributs)
- Sans passer par sauvegarde

**Activation** :
```powershell
Enable-ADOptionalFeature -Identity "Recycle Bin Feature" -Scope ForestOrConfigurationSet -Target "techsolutions.local"
```

**Barème** :
- Définition tombstone : **2 pts**
- Durée 180 jours : **1 pt**
- Impact restauration : **2 pts**

---

**Suite dans les sections suivantes...**

*(Continuer avec Virtualisation, Sécurité/Sauvegardes, Anglais)*


---

# ✅ EXAMEN BLANC 1 - CORRECTIONS DÉTAILLÉES
## Partie 2 : Questionnaire Technique (suite et fin)

---

## 💻 SECTION D : VIRTUALISATION (15 points)

### ✔️ Question D1 (5 points) - Snapshots risques

**Réponse attendue** :

**Définition snapshot** :
- **Image instantanée** de l'état d'une VM à un instant T
- Capture : Disque, mémoire, configuration
- Permet de **revenir en arrière** (rollback)

---

**❌ Risques d'utilisation prolongée** :

**1) Dégradation des performances** :
- Les snapshots créent des fichiers **delta** (différences)
- Chaque lecture/écriture disque passe par **tous les deltas** (chaîne)
- Plus il y a de snapshots → Plus c'est lent

**Exemple** :
```
VM.vmdk (disque de base)
  └─ VM-000001.vmdk (snapshot 1)
      └─ VM-000002.vmdk (snapshot 2)
          └─ VM-000003.vmdk (snapshot 3) ← VM écrit ici
```
Chaque I/O doit parcourir **4 fichiers** !

---

**2) Consommation d'espace disque** :
- Chaque snapshot **grandit** avec le temps
- **Peut remplir le datastore** si non surveillé
- Blocage de la VM si plus d'espace

**Exemple** :
- VM de 100 Go
- Snapshot de 5 jours : **+50 Go**
- Snapshot de 30 jours : **+200 Go**

---

**3) Risque de perte de données** :
- Snapshot **n'est pas une sauvegarde**
- Si corruption du snapshot → **Perte des modifications**
- Si suppression accidentelle du snapshot → Commit forcé (risque)

---

**4) Complexité de gestion** :
- Chaînes de snapshots complexes
- Dépendances entre snapshots
- **Suppression longue** (consolidation)

---

**5) Problèmes de réplication/backup** :
- **vMotion plus lent** (plus de fichiers à déplacer)
- **Backup impossible** tant que snapshot actif (selon solution)

---

**Bonnes pratiques** :

1. **Ne pas conserver > 72 heures**
2. **Maximum 3-4 snapshots** par VM
3. **Supprimer après tests** (ne pas oublier)
4. **Surveiller l'espace disque** des datastores
5. **Utiliser des sauvegardes** pour la rétention longue
6. **Pas de snapshot sur VM de production critiques** (BDD, Exchange)

---

**Supprimer un snapshot (consolidation)** :

**vSphere Client** :
```
Clic droit VM → Snapshots → Snapshot Manager → Supprimer
OU
Clic droit VM → Snapshots → Consolider
```

**Durée** : Peut prendre **plusieurs heures** selon taille

**Barème** :
- Risque 1 (performances) : **2 pts**
- Risque 2 (espace disque) : **2 pts**
- Autre risque ou bonne pratique : **1 pt**

---

### ✔️ Question D2 (5 points) - HA vs DRS

**Réponse attendue** :

---

## **1) vSphere HA (High Availability)**

**Objectif** : **Haute disponibilité** - Redémarrage automatique des VMs en cas de panne

**Fonctionnement** :
1. Un hôte ESXi **tombe en panne**
2. vSphere HA **détecte** la panne (heartbeat réseau + datastore)
3. Les VMs de cet hôte sont **redémarrées automatiquement** sur d'autres hôtes du cluster

**Conditions** :
- **Cluster HA activé**
- **Ressources disponibles** sur les autres hôtes (CPU, RAM)
- **VMs sur datastores partagés** (SAN, NFS)

**Paramètres clés** :
- **Admission Control** : Réserver ressources pour tolérer N pannes d'hôtes
- **VM Restart Priority** : Ordre de redémarrage (haute/moyenne/basse)
- **Host Isolation Response** : Action si hôte isolé (éteindre VMs ou laisser)

**Temps de reprise** : 2-5 minutes (redémarrage complet de l'OS)

**Commande vérification** :
```
Cluster → Configuration → vSphere HA
```

---

## **2) vSphere DRS (Distributed Resource Scheduler)**

**Objectif** : **Équilibrage de charge** - Répartition automatique des VMs selon ressources

**Fonctionnement** :
1. DRS **analyse** l'utilisation CPU/RAM des hôtes
2. Si déséquilibre détecté → **vMotion automatique** des VMs
3. Les VMs sont **déplacées à chaud** (sans interruption)

**Niveaux d'automatisation** :
- **Manuel** : Recommandations uniquement (admin décide)
- **Partiellement automatisé** : Demande confirmation pour vMotion
- **Entièrement automatisé** : vMotion automatique sans intervention

**Paramètres clés** :
- **Migration Threshold** : Agressivité (conservative ↔ aggressive)
- **VM/Host Rules** : Affinités (garder VMs ensemble) ou anti-affinités
- **Resource Pools** : Allouer ressources par service/équipe

**Temps de migration** : Quelques secondes (aucune interruption)

**Commande vérification** :
```
Cluster → Configuration → vSphere DRS
```

---

## **Tableau comparatif**

| Caractéristique       | vSphere HA                        | vSphere DRS                       |
|-----------------------|-----------------------------------|-----------------------------------|
| **Objectif**          | Haute disponibilité               | Équilibrage de charge             |
| **Action**            | Redémarrage VM (en cas de panne)  | vMotion VM (à chaud)              |
| **Déclencheur**       | Panne d'hôte                      | Déséquilibre ressources           |
| **Interruption VM**   | **Oui** (redémarrage)             | **Non** (migration à chaud)       |
| **Temps reprise**     | 2-5 minutes                       | Quelques secondes                 |
| **Prérequis**         | Datastores partagés               | vMotion network, datastores partagés |
| **Type**              | **Réactif** (réagit aux pannes)   | **Proactif** (optimise en continu)|

---

**Complémentarité** :
- **HA** : Protège contre pannes matérielles
- **DRS** : Optimise les performances au quotidien
- **Recommandation** : Activer **les deux** dans un cluster de production

---

**Configuration minimale cluster** :
- Minimum **2 hôtes** pour HA
- Minimum **3 hôtes** recommandé (tolérer 1 panne + maintenir équilibrage)
- Datastores partagés (SAN/NFS)
- vMotion network configuré

**Barème** :
- HA défini (redémarrage) : **2 pts**
- DRS défini (équilibrage) : **2 pts**
- Différence claire : **1 pt**

---

### ✔️ Question D3 (5 points) - vMotion prérequis

**Réponse attendue** :

**vMotion** : Migration **à chaud** d'une VM d'un hôte ESXi à un autre **sans interruption**

**Prérequis techniques** :

---

**1) Réseau vMotion** :
- **Réseau dédié** configuré sur chaque hôte ESXi
- VMkernel port avec **vMotion activé**
- **Même VLAN** sur tous les hôtes
- Bande passante recommandée : **1 Gbps minimum** (10 Gbps idéal)

**Configuration** :
```
ESXi → Networking → VMkernel adapters → Add
Cocher : vMotion
IP : 192.168.100.10/24 (réseau dédié vMotion)
```

---

**2) Compatibilité CPU** :
- **CPUs compatibles** entre hôtes source et destination
- Même famille (Intel ↔ Intel, AMD ↔ AMD)
- Si différence → Activer **EVC** (Enhanced vMotion Compatibility)

**EVC** :
```
Cluster → Configuration → vSphere EVC
Activer EVC : Mode "Intel Sandy Bridge" (exemple)
```
Masque les instructions CPU avancées pour uniformiser

---

**3) Stockage partagé** :
- **Datastores partagés** accessibles par tous les hôtes
- SAN (FC, iSCSI) ou NFS
- Les fichiers VM (VMDK, VMX) doivent être sur datastore partagé

**Exception** : Storage vMotion permet migration du stockage (mais plus lent)

---

**4) Réseau VM** :
- **Même nom de port-group** sur tous les hôtes
- Exemple : Si VM connectée au port-group "VLAN20_Production"
  → Ce port-group doit exister sur hôte destination

---

**5) Configuration réseau ESXi** :
- **Pas de stockage local** pour les VMs (sauf Storage vMotion)
- **vSwitch ou dvSwitch** cohérent
- **Même version ESXi** (ou compatible, max 1 version d'écart)

---

**6) Licences** :
- **Licence vSphere Standard minimum** (ou sup.)
- vMotion **non disponible** en édition Essentials

---

**Limites vMotion** :
- **Simultané** : 8 vMotion par hôte en même temps (ESXi 6.x+)
- **Latence réseau** : < 5 ms recommandé
- **Périphériques physiques** : Les VMs avec périphériques USB/PCI passthrough nécessitent vMotion étendu (ou impossible)

---

**Vérifier compatibilité** :

**vSphere Client** :
```
Clic droit VM → Migrate → Change compute resource only
→ Affiche compatibilité des hôtes de destination
```

**Barème** :
- Réseau vMotion : **2 pts**
- Stockage partagé : **2 pts**
- Autre prérequis (CPU, port-group) : **1 pt**

---

---

## 🔒 SECTION E : SÉCURITÉ & SAUVEGARDES (15 points)

### ✔️ Question E1 (5 points) - RTO vs RPO

**Réponse attendue** :

---

**1) RPO (Recovery Point Objective)** :

**Définition** :
- **Perte de données maximale acceptable**
- "Combien de données peut-on perdre ?"
- Intervalle entre la **dernière sauvegarde** et l'**incident**

**Exemple** :
- RPO = **4 heures**
- Signifie : Sauvegarde toutes les 4 heures
- Incident à 14h → Dernière sauvegarde à 10h → **Perte de 4h de données**

**Impact** :
- RPO court (1h) → Sauvegardes **fréquentes** (coût élevé)
- RPO long (24h) → Sauvegardes **quotidiennes** (coût faible)

**Déterminé par** :
- Criticité des données
- Fréquence de modification
- Budget

---

**2) RTO (Recovery Time Objective)** :

**Définition** :
- **Temps de reprise maximal acceptable**
- "Combien de temps peut durer l'interruption ?"
- Délai entre l'**incident** et le **retour à la normale**

**Exemple** :
- RTO = **2 heures**
- Signifie : Le service doit être restauré en **maximum 2 heures**
- Incident à 10h → Service doit être opérationnel à **12h maximum**

**Impact** :
- RTO court (30 min) → Infrastructure **redondante**, haute dispo (coût élevé)
- RTO long (48h) → Restauration **standard** (coût faible)

**Déterminé par** :
- Impact métier de l'indisponibilité
- SLA clients
- Budget

---

**Tableau comparatif** :

| Critère               | RPO                           | RTO                              |
|-----------------------|-------------------------------|----------------------------------|
| **Définition**        | Perte de données acceptable   | Temps d'interruption acceptable  |
| **Question**          | "Combien de données ?"        | "Combien de temps ?"             |
| **Unité**             | Temps (1h, 4h, 24h)           | Temps (30 min, 2h, 24h)          |
| **Impact**            | Fréquence de sauvegarde       | Architecture de reprise          |
| **Technologies**      | Snapshots, réplication        | Clustering, réplication, cloud   |

---

**Exemples par criticité** :

**Application critique (ERP)** :
- RPO = **15 minutes** (réplication continue)
- RTO = **30 minutes** (cluster HA)

**Serveur fichiers** :
- RPO = **4 heures** (sauvegardes incrémentielles)
- RTO = **4 heures** (restauration sur nouveau serveur)

**Serveur de test** :
- RPO = **24 heures** (sauvegarde quotidienne)
- RTO = **48 heures** (restauration basse priorité)

---

**Mise en œuvre** :

**RPO court** :
- Réplication synchrone/asynchrone
- Snapshots fréquents
- Journalisation en continu

**RTO court** :
- Cluster haute disponibilité
- VM de secours (hot standby)
- Restauration automatisée

**Barème** :
- RPO défini : **2 pts**
- RTO défini : **2 pts**
- Différence claire : **1 pt**

---

### ✔️ Question E2 (5 points) - Règle 3-2-1

**Réponse attendue** :

**Règle 3-2-1 : Bonne pratique de sauvegarde**

---

**3** = **3 copies des données** :
- **1 original** (données de production)
- **2 sauvegardes** (copies de l'original)
- En cas de corruption de l'original → 2 copies disponibles

**Exemple** :
- Données sur serveur de prod
- Sauvegarde 1 sur NAS local
- Sauvegarde 2 sur bandes LTO

---

**2** = **2 types de supports différents** :
- **Ne pas mettre tous ses œufs dans le même panier**
- Diversifier les technologies de stockage
- Protection contre défaillance d'un type de média

**Exemples de supports** :
- Disques durs (NAS, SAN)
- Bandes magnétiques (LTO)
- Cloud (AWS S3, Azure Blob)
- Disques SSD

**Exemple** :
- Sauvegarde 1 : **Disque NAS**
- Sauvegarde 2 : **Bande LTO** stockée hors site

---

**1** = **1 copie hors site** (off-site) :
- **Copie externalisée**, dans un autre lieu
- Protection contre sinistres locaux (incendie, inondation, vol)
- Distance recommandée : **> 100 km**

**Exemples** :
- Bandes stockées dans un **coffre-fort bancaire**
- Réplication vers **datacenter distant**
- **Cloud public** (AWS, Azure, OVH)

---

**Mise en pratique** :

**Exemple 1 : PME** :
- **3** : Données prod + NAS local + Cloud
- **2** : Disques (NAS) + Cloud (S3)
- **1** : Cloud = hors site

**Exemple 2 : Grande entreprise** :
- **3** : Données prod + NAS datacenter principal + Bandes
- **2** : Disques (NAS) + Bandes magnétiques (LTO-8)
- **1** : Bandes stockées sur site distant (coffre)

**Exemple 3 : Configuration recommandée** :
```
┌─────────────┐
│  SERVEUR    │ ← Original
│  PRODUCTION │
└──────┬──────┘
       │
       ├──────► NAS Local (Sauvegarde quotidienne)
       │
       ├──────► Bandes LTO (Sauvegarde hebdomadaire) → Coffre hors site
       │
       └──────► Cloud Azure (Réplication en continu)
```

---

**Extension : Règle 3-2-1-1-0** (moderne) :

- **3** copies
- **2** médias différents
- **1** copie hors site
- **1** copie **offline** (air gap, protection ransomware)
- **0** erreur de restauration (tester régulièrement)

---

**Outils de sauvegarde** :
- **Veeam Backup & Replication** (VMs)
- **Windows Server Backup** (serveurs physiques)
- **Acronis Backup**
- **Bacula** (open source)
- **Borg Backup** (dédupliqué, chiffré)

**Barème** :
- 3 copies : **2 pts**
- 2 médias différents : **2 pts**
- 1 hors site : **1 pt**

---

### ✔️ Question E3 (5 points) - PRA vs PCA

**Réponse attendue** :

---

## **1) PRA (Plan de Reprise d'Activité)**

**Définition** :
- Ensemble de **procédures** pour rétablir le SI après un **sinistre majeur**
- Objectif : **Reprendre l'activité** après interruption

**Déclenchement** :
- **Après un incident** (incendie, inondation, cyberattaque)
- Sinistre ayant causé une **interruption complète**

**Contenu** :
1. **Inventaire des ressources** (matériel, logiciels, données)
2. **Procédures de restauration** :
   - Ordre de restauration des serveurs
   - Étapes détaillées (installation, config, données)
3. **Contacts d'urgence** (équipe IT, prestataires, fournisseurs)
4. **Sites de secours** (datacenter de backup)
5. **RTO/RPO par application**
6. **Tests réguliers** (2 fois/an recommandé)

**Phases du PRA** :
1. **Détection** du sinistre
2. **Activation** du PRA (décision direction)
3. **Bascule** vers site de secours
4. **Reprise** de l'activité (mode dégradé)
5. **Retour à la normale**

**Exemple** :
- Incendie du datacenter principal
- Activation du PRA
- Bascule vers datacenter de secours (distant 200 km)
- Restauration des VMs depuis backups
- Reprise en 4 heures (RTO respecté)

---

## **2) PCA (Plan de Continuité d'Activité)**

**Définition** :
- Stratégie **proactive** pour maintenir l'activité **pendant** un incident
- Objectif : **Éviter l'interruption** (ou minimiser)

**Déclenchement** :
- **Avant/Pendant** l'incident (prévention)
- Mesures **permanentes** (redondance, haute dispo)

**Contenu** :
1. **Analyse d'impact métier** (BIA - Business Impact Analysis)
   - Identifier processus critiques
   - Évaluer pertes financières si arrêt
2. **Mesures de prévention** :
   - Redondance matérielle (cluster, RAID, double alimentation)
   - Réplication de données en temps réel
   - Sites géographiquement distants
3. **Organisation** :
   - Télétravail (si locaux inaccessibles)
   - Répartition des équipes sur plusieurs sites
4. **Communication de crise**
5. **Formation du personnel**

**Exemple** :
- Datacenter avec **2 salles serveurs** (bâtiments distincts)
- Cluster HA sur les 2 sites
- Réplication synchrone des données
- Si panne site A → **Bascule automatique** vers site B (0 interruption)

---

## **Tableau comparatif**

| Critère               | PRA                                  | PCA                                    |
|-----------------------|--------------------------------------|----------------------------------------|
| **Objectif**          | **Reprendre** après sinistre         | **Continuer** pendant sinistre         |
| **Moment**            | **Après** l'incident (réactif)       | **Avant/Pendant** (proactif)           |
| **Focus**             | Restauration du SI                   | Continuité métier globale              |
| **Interruption**      | Accepte interruption (RTO)           | Vise 0 interruption                    |
| **Coût**              | Modéré (site de secours, backups)    | Élevé (redondance totale)              |
| **Exemple**           | Restauration depuis backup           | Cluster actif/actif, réplication       |
| **Périmètre**         | SI et infrastructure IT              | Toute l'organisation (RH, métiers, IT) |

---

## **Complémentarité**

**PCA** = Prévention (empêche l'arrêt)  
**PRA** = Guérison (répare après l'arrêt)

**Stratégie idéale** :
1. **PCA** pour les services **critiques** (ERP, production)
   - Cluster HA
   - Réplication synchrone
   - RTO < 1 minute
2. **PRA** pour les services **secondaires**
   - Sauvegarde quotidienne
   - Restauration manuelle
   - RTO 4-24 heures

---

**Exemple d'architecture PCA + PRA** :

```
SITE A (Paris)                    SITE B (Lyon - 500 km)
┌──────────────┐                  ┌──────────────┐
│  Cluster ESXi│◄────Réplication──┤  Cluster ESXi│  ← PCA (actif/actif)
│  + VMs       │    synchrone     │  + VMs       │
└──────┬───────┘                  └──────┬───────┘
       │                                  │
       ▼                                  ▼
  Backup quotidien                  Backup quotidien
       │                                  │
       ▼                                  ▼
  BANDES LTO ──► Coffre hors site    BANDES LTO         ← PRA (si échec PCA)
```

**Barème** :
- PRA défini (après sinistre) : **2 pts**
- PCA défini (continuité) : **2 pts**
- Différence claire : **1 pt**

---

---

## 🇬🇧 SECTION F : ANGLAIS TECHNIQUE (5 points)

### ✔️ Question F1 (5 points) - Traduction et résumé

**Texte anglais** :

> **Security Alert**
> 
> A critical vulnerability (CVE-2024-12345) has been discovered in OpenSSL 3.x versions. This flaw allows remote attackers to execute arbitrary code on affected systems. All administrators must immediately update to OpenSSL 3.0.15 or later. Additionally, review system logs for suspicious activity during the past 30 days.

---

**Traduction française attendue** :

> **Alerte de sécurité**
> 
> Une vulnérabilité critique (CVE-2024-12345) a été découverte dans les versions OpenSSL 3.x. Cette faille permet aux attaquants distants d'exécuter du code arbitraire sur les systèmes affectés. Tous les administrateurs doivent immédiatement mettre à jour vers OpenSSL 3.0.15 ou ultérieur. De plus, examinez les journaux système pour détecter toute activité suspecte au cours des 30 derniers jours.

---

**Résumé technique (français)** :

**Vulnérabilité** : CVE-2024-12345 dans OpenSSL 3.x

**Gravité** : **Critique** (permet l'exécution de code à distance - RCE)

**Impact** :
- Compromission totale du système
- Exécution de commandes par attaquant distant
- Aucune authentification requise pour exploitation

**Action immédiate** :
1. **Patcher** : Mettre à jour vers OpenSSL **3.0.15+**
2. **Audit** : Vérifier logs des 30 derniers jours
3. **Détection** : Rechercher indicateurs de compromission (IOC)

**Commandes de vérification** :

**Identifier version OpenSSL** :
```bash
openssl version
# Si < 3.0.15 → VULNÉRABLE
```

**Mettre à jour (Debian/Ubuntu)** :
```bash
sudo apt update
sudo apt install --only-upgrade openssl
openssl version  # Vérifier
```

**Vérifier logs** :
```bash
sudo grep -i "suspicious\|attack\|unauthorized" /var/log/auth.log
sudo journalctl --since "30 days ago" | grep -i "openssl\|ssl\|tls"
```

**Redémarrer services** :
```bash
sudo systemctl restart apache2
sudo systemctl restart nginx
```

---

**Vocabulaire technique** :

| Anglais                    | Français                        |
|----------------------------|---------------------------------|
| Vulnerability              | Vulnérabilité                   |
| Flaw                       | Faille                          |
| Remote attacker            | Attaquant distant               |
| Execute arbitrary code     | Exécuter du code arbitraire     |
| Affected systems           | Systèmes affectés               |
| Update                     | Mettre à jour                   |
| Review                     | Examiner / Passer en revue      |
| System logs                | Journaux système                |
| Suspicious activity        | Activité suspecte               |

---

**Contexte CVE** :

**CVE** = Common Vulnerabilities and Exposures
- **Identifiant unique** des vulnérabilités
- Format : `CVE-ANNÉE-NUMÉRO`
- Exemple : `CVE-2024-12345`

**Sources d'info** :
- **NIST NVD** : https://nvd.nist.gov/
- **MITRE** : https://cve.mitre.org/
- **Cert-FR** : https://www.cert.ssi.gouv.fr/

---

**Scoring CVSS** :

| Score        | Gravité       |
|--------------|---------------|
| 0.0          | None          |
| 0.1 - 3.9    | Low           |
| 4.0 - 6.9    | Medium        |
| 7.0 - 8.9    | High          |
| 9.0 - 10.0   | **Critical**  |

RCE (Remote Code Execution) = Généralement **9.0+** (Critical)

---

**Barème** :
- Traduction correcte : **2 pts**
- Résumé complet : **2 pts**
- Vocabulaire technique précis : **1 pt**

---

---

# 🎓 RÉCAPITULATIF BARÈME TOTAL

## MSP Écrite (100 points)

| Incident | Question | Points |
|----------|----------|--------|
| **1 - DHCP** | 1.1 Cause | 5 |
| | 1.2 Solution court terme | 8 |
| | 1.3 Solution moyen terme | 7 |
| | 1.4 Réservation vs statique | 5 |
| | 1.5 Options DHCP | 5 |
| **Sous-total Incident 1** | | **30** |
| **2 - PowerShell** | 2.1 Script complet | 15 |
| | 2.2 Gestion erreurs | 5 |
| | 2.3 Email récap | 5 |
| | 2.4 Accents | 3 |
| | 2.5 Test OU | 2 |
| **Sous-total Incident 2** | | **30** |
| **3 - Réseau/VLAN** | 3.1 Cause racine | 5 |
| | 3.2 Config switch | 10 |
| | 3.3 Relais DHCP | 8 |
| | 3.4 ACL sécurité | 12 |
| | 3.5 Documentation | 5 |
| **Sous-total Incident 3** | | **40** |

---

## Questionnaire Technique (100 points)

| Section | Question | Points |
|---------|----------|--------|
| **A - Linux** | A1 Permissions | 4 |
| | A2 Find fichiers | 4 |
| | A3 Redirections | 4 |
| | A4 Processus bg | 4 |
| | A5 Grep regex | 4 |
| **Sous-total Linux** | | **20** |
| **B - Réseau** | B1 Subnetting /23 | 5 |
| | B2 VLSM | 5 |
| | B3 VLAN Trunk/Access | 5 |
| | B4 Routage statique | 5 |
| | B5 DNS vs DHCP | 5 |
| **Sous-total Réseau** | | **25** |
| **C - Windows/AD** | C1 GPO LSDOU | 5 |
| | C2 Rôles FSMO | 5 |
| | C3 Groupes Global/Local | 5 |
| | C4 Profils itinérants | 5 |
| | C5 Tombstone | 5 |
| **Sous-total Windows/AD** | | **25** |
| **D - Virtualisation** | D1 Snapshots risques | 5 |
| | D2 HA vs DRS | 5 |
| | D3 vMotion prérequis | 5 |
| **Sous-total Virtualisation** | | **15** |
| **E - Sécurité/Backup** | E1 RTO vs RPO | 5 |
| | E2 Règle 3-2-1 | 5 |
| | E3 PRA vs PCA | 5 |
| **Sous-total Sécurité** | | **15** |
| **F - Anglais** | F1 Traduction/résumé | 5 |
| **Sous-total Anglais** | | **5** |

---

## **TOTAL GÉNÉRAL : 200 points**

- **MSP** : 100 points
- **Questionnaire** : 100 points

---

## 📊 **Évaluation**

| Note | Appréciation |
|------|--------------|
| 180-200 | Excellent - Maîtrise complète |
| 160-179 | Très bien - Solides compétences |
| 140-159 | Bien - Niveau correct |
| 120-139 | Passable - Lacunes à combler |
| < 120 | Insuffisant - Révisions nécessaires |

**Seuil de réussite recommandé** : **120/200 (60%)**

---

## ⏱️ **Gestion du temps recommandée**

**Total : 3h30 (210 minutes)**

### MSP (1h30 = 90 minutes)
- Incident 1 DHCP : **25 min**
- Incident 2 PowerShell : **35 min**
- Incident 3 Réseau/VLAN : **30 min**

### Questionnaire (2h00 = 120 minutes)
- Section A Linux : **20 min**
- Section B Réseau : **30 min**
- Section C Windows/AD : **30 min**
- Section D Virtualisation : **15 min**
- Section E Sécurité/Backup : **20 min**
- Section F Anglais : **5 min**

**Relecture** : Garder **10 minutes** à la fin

---

## ✅ **Conseils de correction**

**Pour l'examinateur** :

1. **Accepter variantes** : Plusieurs solutions possibles (exemple : `egrep` = `grep -E`)
2. **Syntaxe vs concept** : Privilégier la compréhension du concept (petite erreur syntaxe = -0.5 pt)
3. **Points partiels** : Attribuer points partiels si démarche correcte
4. **Anglais** : Tolérer petites erreurs de traduction si sens respecté

**Fautes éliminatoires** :
- Commande dangereuse (exemple : `rm -rf /` sans contexte)
- Faille de sécurité grave (ACL trop permissive sans justification)
- Incompréhension totale du concept

---

**FIN DES CORRECTIONS - EXAMEN BLANC 1**

---

## 📚 **Ressources complémentaires pour révisions**

**Linux** :
- Man pages : `man chmod`, `man find`, `man grep`
- Cheat sheet : https://cheatography.com/davechild/cheat-sheets/linux-command-line/

**Réseau** :
- Calculateur subnetting : https://www.subnet-calculator.com/
- RFC 1918 (adresses privées), RFC 791 (IP)

**Windows/AD** :
- TechNet Microsoft : https://learn.microsoft.com/
- GPO Reference : https://gpsearch.azurewebsites.net/

**Virtualisation** :
- VMware docs : https://docs.vmware.com/
- vSphere 8 guide d'administration

**Sécurité** :
- ANSSI guides : https://www.ssi.gouv.fr/
- NIST Cybersecurity Framework

---

**Bonne chance pour votre examen ! 🎯**


---


