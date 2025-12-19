# 📜 FICHE SCRIPTING - BASH & POWERSHELL
## Commandes essentielles pour l'examen

> **Pour l'examen** : Tu dois savoir LIRE, COMPRENDRE et COMMENTER des scripts simples.  
> Focus sur les bases : variables, conditions, boucles, tests.

---

## 🐧 BASH SCRIPTING

### Structure de base

```bash
#!/bin/bash
# Première ligne OBLIGATOIRE = shebang

# Commentaire avec #

echo "Hello World"
```

**Rendre exécutable** :
```bash
chmod +x script.sh
./script.sh
```

---

### Variables

```bash
# Déclaration (PAS d'espace autour du =)
nom="Jean"
age=25

# Utilisation
echo "Bonjour $nom"
echo "Tu as $age ans"
echo "Ou avec accolades : ${nom}"

# Variables systèmes
echo $HOME          # Répertoire home
echo $USER          # Nom utilisateur
echo $PATH          # PATH
echo $PWD           # Répertoire courant
```

**Arguments du script** :
```bash
$0    # Nom du script
$1    # Premier argument
$2    # Deuxième argument
$@    # Tous les arguments
$#    # Nombre d'arguments
$?    # Code retour de la dernière commande (0 = succès)
```

---

### Lecture utilisateur

```bash
# Demander une saisie
read -p "Entrez votre nom: " nom
echo "Bonjour $nom"
```

---

### Tests et conditions

**Structure if** :
```bash
if [ condition ]; then
    commandes
elif [ autre_condition ]; then
    commandes
else
    commandes
fi
```

**Tests sur fichiers** :
```bash
if [ -f /etc/passwd ]; then
    echo "Le fichier existe"
fi

if [ -d /home ]; then
    echo "Le répertoire existe"
fi
```

**Opérateurs fichiers** :
- `-f fichier` : Fichier existe
- `-d répertoire` : Répertoire existe
- `-e fichier` : Existe (fichier ou répertoire)
- `-r fichier` : Lisible
- `-w fichier` : Modifiable
- `-x fichier` : Exécutable
- `-s fichier` : Non vide

**Tests sur chaînes** :
```bash
if [ "$nom" = "Jean" ]; then
    echo "C'est Jean"
fi

if [ -z "$variable" ]; then    # Chaîne vide
    echo "Variable vide"
fi

if [ -n "$variable" ]; then    # Chaîne non vide
    echo "Variable non vide"
fi
```

**Tests numériques** :
```bash
if [ $age -eq 18 ]; then    # Égal
if [ $age -ne 18 ]; then    # Différent
if [ $age -gt 18 ]; then    # Supérieur (greater than)
if [ $age -ge 18 ]; then    # Supérieur ou égal
if [ $age -lt 18 ]; then    # Inférieur (less than)
if [ $age -le 18 ]; then    # Inférieur ou égal
```

**Tests combinés** :
```bash
# ET logique
if [ $age -ge 18 ] && [ $age -le 65 ]; then
    echo "Adulte actif"
fi

# OU logique
if [ "$nom" = "Jean" ] || [ "$nom" = "Marie" ]; then
    echo "C'est Jean ou Marie"
fi
```

---

### Boucles

**Boucle for (liste)** :
```bash
for utilisateur in jean marie paul; do
    echo "Bonjour $utilisateur"
done
```

**Boucle for (fichiers)** :
```bash
for fichier in /var/log/*.log; do
    echo "Traitement de $fichier"
done
```

**Boucle for (numérique)** :
```bash
for i in {1..5}; do
    echo "Itération $i"
done
```

**Boucle while** :
```bash
compteur=1
while [ $compteur -le 5 ]; do
    echo "Compteur: $compteur"
    compteur=$((compteur + 1))
done
```

---

### Case (switch)

```bash
read -p "Entrez votre choix (1-3): " choix

case $choix in
    1)
        echo "Option 1 sélectionnée"
        ;;
    2)
        echo "Option 2 sélectionnée"
        ;;
    3)
        echo "Option 3 sélectionnée"
        ;;
    *)
        echo "Choix invalide"
        ;;
esac
```

---

### Fonctions

```bash
# Déclaration
ma_fonction() {
    echo "Fonction appelée avec : $1"
    return 0
}

# Appel
ma_fonction "argument"
```

---

### Arithmétique

```bash
# Méthode 1 : $(( ))
resultat=$((5 + 3))
echo $resultat    # 8

# Incrémentation
compteur=1
compteur=$((compteur + 1))
((compteur++))

# Opérateurs : +  -  *  /  %  **
```

---

### EXEMPLE COMPLET - Script de backup

```bash
#!/bin/bash
# Script de sauvegarde simple

# Variables
SOURCE="/home/user/documents"
DESTINATION="/backup"
DATE=$(date +%Y%m%d_%H%M%S)
FICHIER_BACKUP="backup_$DATE.tar.gz"

# Vérification du répertoire source
if [ ! -d "$SOURCE" ]; then
    echo "ERREUR: Le répertoire source n'existe pas"
    exit 1
fi

# Création du répertoire de destination si nécessaire
if [ ! -d "$DESTINATION" ]; then
    echo "Création du répertoire de destination..."
    mkdir -p "$DESTINATION"
fi

# Sauvegarde
echo "Début de la sauvegarde..."
tar -czf "$DESTINATION/$FICHIER_BACKUP" "$SOURCE"

# Vérification
if [ $? -eq 0 ]; then
    echo "✓ Sauvegarde réussie : $FICHIER_BACKUP"
else
    echo "✗ Erreur lors de la sauvegarde"
    exit 1
fi
```

---

## 💻 POWERSHELL SCRIPTING

### Structure de base

```powershell
# Commentaire avec #

Write-Host "Hello World"

# Ou
Write-Output "Hello World"
```

**Extension** : `.ps1`

**Exécution** :
```powershell
.\script.ps1
```

---

### Variables

```powershell
# Déclaration (avec $)
$nom = "Jean"
$age = 25

# Utilisation
Write-Host "Bonjour $nom"
Write-Host "Tu as $age ans"

# Variables automatiques
$env:USERNAME       # Nom utilisateur
$env:COMPUTERNAME   # Nom de la machine
$PWD                # Répertoire courant
$?                  # Succès de la dernière commande (True/False)
```

**Arguments du script** :
```powershell
$args[0]    # Premier argument
$args[1]    # Deuxième argument
$args       # Tous les arguments
```

---

### Lecture utilisateur

```powershell
# Demander une saisie
$nom = Read-Host "Entrez votre nom"
Write-Host "Bonjour $nom"
```

---

### Tests et conditions

**Structure if** :
```powershell
if (condition) {
    commandes
} elseif (autre_condition) {
    commandes
} else {
    commandes
}
```

**Tests sur fichiers** :
```powershell
if (Test-Path "C:\fichier.txt") {
    Write-Host "Le fichier existe"
}

if (Test-Path "C:\dossier" -PathType Container) {
    Write-Host "Le répertoire existe"
}
```

**Tests sur chaînes** :
```powershell
if ($nom -eq "Jean") {
    Write-Host "C'est Jean"
}

if ([string]::IsNullOrEmpty($variable)) {
    Write-Host "Variable vide"
}
```

**Opérateurs de comparaison** :
- `-eq` : Égal
- `-ne` : Différent
- `-gt` : Supérieur
- `-ge` : Supérieur ou égal
- `-lt` : Inférieur
- `-le` : Inférieur ou égal
- `-like` : Correspondance avec wildcard (*)
- `-match` : Correspondance regex

**Tests combinés** :
```powershell
# ET logique
if ($age -ge 18 -and $age -le 65) {
    Write-Host "Adulte actif"
}

# OU logique
if ($nom -eq "Jean" -or $nom -eq "Marie") {
    Write-Host "C'est Jean ou Marie"
}
```

---

### Boucles

**Boucle foreach (objets)** :
```powershell
$utilisateurs = @("Jean", "Marie", "Paul")

foreach ($user in $utilisateurs) {
    Write-Host "Bonjour $user"
}
```

**Boucle foreach (fichiers)** :
```powershell
foreach ($fichier in Get-ChildItem C:\Logs\*.log) {
    Write-Host "Traitement de $($fichier.Name)"
}
```

**Boucle for (numérique)** :
```powershell
for ($i = 1; $i -le 5; $i++) {
    Write-Host "Itération $i"
}
```

**Boucle while** :
```powershell
$compteur = 1
while ($compteur -le 5) {
    Write-Host "Compteur: $compteur"
    $compteur++
}
```

---

### Switch (case)

```powershell
$choix = Read-Host "Entrez votre choix (1-3)"

switch ($choix) {
    1 { Write-Host "Option 1 sélectionnée" }
    2 { Write-Host "Option 2 sélectionnée" }
    3 { Write-Host "Option 3 sélectionnée" }
    default { Write-Host "Choix invalide" }
}
```

---

### Fonctions

```powershell
# Déclaration
function Ma-Fonction {
    param($parametre)
    Write-Host "Fonction appelée avec : $parametre"
    return 0
}

# Appel
Ma-Fonction "argument"
```

---

### Arithmétique

```powershell
# Calculs
$resultat = 5 + 3
Write-Host $resultat    # 8

# Incrémentation
$compteur = 1
$compteur++
$compteur += 1

# Opérateurs : +  -  *  /  %
```

---

### EXEMPLE COMPLET - Script de gestion d'utilisateurs AD

```powershell
# Script de création d'utilisateurs AD

# Import du module Active Directory
Import-Module ActiveDirectory

# Variables
$prenoms = @("Jean", "Marie", "Paul")
$nom = "Dupont"
$ou = "OU=Utilisateurs,DC=entreprise,DC=local"

# Boucle de création
foreach ($prenom in $prenoms) {
    $username = "$prenom.$nom".ToLower()
    $displayName = "$prenom $nom"
    
    # Vérification si l'utilisateur existe
    if (Get-ADUser -Filter "SamAccountName -eq '$username'" -ErrorAction SilentlyContinue) {
        Write-Host "L'utilisateur $username existe déjà" -ForegroundColor Yellow
    } else {
        # Création de l'utilisateur
        try {
            New-ADUser -Name $displayName `
                       -SamAccountName $username `
                       -UserPrincipalName "$username@entreprise.local" `
                       -Path $ou `
                       -AccountPassword (ConvertTo-SecureString "P@ssw0rd123!" -AsPlainText -Force) `
                       -Enabled $true
            
            Write-Host "✓ Utilisateur $username créé avec succès" -ForegroundColor Green
        } catch {
            Write-Host "✗ Erreur lors de la création de $username : $_" -ForegroundColor Red
        }
    }
}

Write-Host "Traitement terminé"
```

---

## 🔥 COMMANDES ESSENTIELLES POUR L'EXAMEN

### BASH - Top 15

| Commande | Usage |
|----------|-------|
| `echo` | Afficher du texte |
| `read` | Lire une entrée utilisateur |
| `if [ ]` | Condition |
| `for` / `while` | Boucles |
| `case` | Switch |
| `$?` | Code retour dernière commande |
| `test` ou `[ ]` | Tests |
| `grep` | Filtrer du texte |
| `sed` | Édition de flux |
| `awk` | Traitement de texte |
| `date` | Date/heure |
| `exit` | Sortir du script (avec code) |
| `$(commande)` | Substitution de commande |
| `&&` / `||` | ET / OU logique |
| `>` / `>>` | Redirection |

### POWERSHELL - Top 15

| Commande | Usage |
|----------|-------|
| `Write-Host` | Afficher du texte |
| `Read-Host` | Lire une entrée |
| `if` | Condition |
| `foreach` / `while` | Boucles |
| `switch` | Switch |
| `Test-Path` | Tester existence fichier/dossier |
| `Get-ChildItem` | Lister fichiers (comme `ls`) |
| `Get-Content` | Lire un fichier |
| `Set-Content` | Écrire dans un fichier |
| `Get-ADUser` | Utilisateur AD |
| `New-ADUser` | Créer utilisateur AD |
| `Get-Service` | Lister les services |
| `Start-Service` | Démarrer un service |
| `$_` | Objet courant dans pipeline |
| `Where-Object` | Filtrer des objets |

---

## 📌 COMPARAISON BASH vs POWERSHELL

| Action | BASH | POWERSHELL |
|--------|------|------------|
| Variable | `nom="Jean"` | `$nom = "Jean"` |
| Affichage | `echo $nom` | `Write-Host $nom` |
| Condition | `if [ $age -eq 18 ]` | `if ($age -eq 18)` |
| Égalité | `-eq` | `-eq` |
| Différence | `-ne` | `-ne` |
| ET logique | `&&` ou `-a` | `-and` |
| OU logique | `||` ou `-o` | `-or` |
| Fichier existe | `[ -f fichier ]` | `Test-Path fichier` |
| Code retour | `$?` (0=succès) | `$?` (True=succès) |
| Commentaire | `#` | `#` |

---

## 🎯 SCÉNARIOS D'EXAMEN FRÉQUENTS

### Scénario 1 : Comprendre ce script BASH

```bash
#!/bin/bash
for utilisateur in $(cat /tmp/users.txt); do
    if id "$utilisateur" &> /dev/null; then
        echo "$utilisateur existe déjà"
    else
        useradd -m "$utilisateur"
        echo "Utilisateur $utilisateur créé"
    fi
done
```

**Que fait ce script ?**
- Lit une liste d'utilisateurs depuis `/tmp/users.txt`
- Pour chaque utilisateur, vérifie s'il existe (`id`)
- Si existe : message
- Si n'existe pas : création avec `useradd -m`

---

### Scénario 2 : Commenter ce script POWERSHELL

```powershell
$services = Get-Service | Where-Object {$_.Status -eq "Stopped"}
foreach ($service in $services) {
    Write-Host "Service arrêté : $($service.Name)"
}
```

**Que fait ce script ?**
- Récupère tous les services
- Filtre uniquement ceux qui sont arrêtés (Status = Stopped)
- Affiche le nom de chaque service arrêté

---

### Scénario 3 : Corriger ce script BASH

```bash
#!/bin/bash
# Script avec erreur
nom = "Jean"       # ❌ Espaces autour du =
if [ $age -eq 18 ] # ❌ Variable $age non définie
then
    echo "Majeur"
fi
```

**Corrections** :
```bash
#!/bin/bash
nom="Jean"         # ✓ Pas d'espace
age=18             # ✓ Définir la variable
if [ $age -eq 18 ]; then
    echo "Majeur"
fi
```

---

## ✅ CHECKLIST AVANT L'EXAMEN

- [ ] Je sais déclarer et utiliser une variable (BASH et PowerShell)
- [ ] Je sais faire une condition `if`
- [ ] Je connais les opérateurs de test `-eq`, `-ne`, `-gt`, `-lt`
- [ ] Je sais tester l'existence d'un fichier
- [ ] Je sais faire une boucle `for` et `while`
- [ ] Je sais lire une entrée utilisateur (`read` / `Read-Host`)
- [ ] Je comprends `$?` (code retour)
- [ ] Je sais commenter un script simple
- [ ] Je peux expliquer ce que fait un script de 10-15 lignes

---

**💡 ASTUCE EXAMEN** : Tu n'as PAS à écrire un script complet de zéro.  
**Tu dois** :
- **LIRE** et **COMPRENDRE** un script
- **COMMENTER** les lignes
- **CORRIGER** une erreur simple
- **COMPLÉTER** un script existant

---

---

## 🔥 EXEMPLES AVANCÉS

### BASH - Script de surveillance système

```bash
#!/bin/bash
# Surveillance des ressources système

# Fonction d'alerte
envoyer_alerte() {
    local message=$1
    echo "[ALERTE] $message" | tee -a /var/log/monitoring.log
    # mail -s "Alerte Système" admin@entreprise.com <<< "$message"
}

# Vérification CPU
cpu_usage=$(top -bn1 | grep "Cpu(s)" | awk '{print $2}' | cut -d'%' -f1)
if (( $(echo "$cpu_usage > 80" | bc -l) )); then
    envoyer_alerte "CPU élevé : ${cpu_usage}%"
fi

# Vérification RAM
mem_free=$(free | awk '/Mem:/ {printf "%.0f", $7/$2 * 100}')
if [ $mem_free -lt 10 ]; then
    envoyer_alerte "Mémoire faible : ${mem_free}% disponible"
fi

# Vérification disque
df -H | grep -vE '^Filesystem|tmpfs|cdrom' | awk '{print $5 " " $1}' | while read usage partition; do
    usage_num=$(echo $usage | sed 's/%//')
    if [ $usage_num -ge 90 ]; then
        envoyer_alerte "Disque plein : $partition à ${usage}"
    fi
done

echo "Surveillance terminée à $(date)"
```

### BASH - Gestion d'utilisateurs en masse

```bash
#!/bin/bash
# Création d'utilisateurs depuis un fichier CSV
# Format CSV : prenom,nom,service

FICHIER_CSV="utilisateurs.csv"
MOT_DE_PASSE_DEFAUT="TempPass123!"

if [ ! -f "$FICHIER_CSV" ]; then
    echo "Erreur : fichier $FICHIER_CSV introuvable"
    exit 1
fi

while IFS=',' read -r prenom nom service; do
    # Ignorer la première ligne (en-têtes)
    if [ "$prenom" = "prenom" ]; then
        continue
    fi
    
    # Créer le login
    login=$(echo "${prenom}.${nom}" | tr '[:upper:]' '[:lower:]')
    
    # Vérifier si l'utilisateur existe
    if id "$login" &>/dev/null; then
        echo "⚠️  $login existe déjà, ignoré"
        continue
    fi
    
    # Créer l'utilisateur
    useradd -m -c "$prenom $nom" -s /bin/bash "$login"
    
    # Définir le mot de passe
    echo "$login:$MOT_DE_PASSE_DEFAUT" | chpasswd
    
    # Forcer le changement au prochain login
    chage -d 0 "$login"
    
    # Ajouter au groupe du service
    if [ -n "$service" ]; then
        if ! getent group "$service" &>/dev/null; then
            groupadd "$service"
        fi
        usermod -aG "$service" "$login"
    fi
    
    echo "✓ Utilisateur $login créé (service: $service)"
    
done < "$FICHIER_CSV"

echo "Traitement terminé"
```

### POWERSHELL - Audit Active Directory

```powershell
# Script d'audit des comptes utilisateurs AD

# Import du module
Import-Module ActiveDirectory

# Variables
$rapport = "C:\Rapports\Audit_AD_$(Get-Date -Format 'yyyyMMdd_HHmmss').html"
$domaine = (Get-ADDomain).DNSRoot

# Début du rapport HTML
$html = @"
<html>
<head>
    <title>Rapport d'Audit Active Directory</title>
    <style>
        body { font-family: Arial; }
        table { border-collapse: collapse; width: 100%; }
        th, td { border: 1px solid #ddd; padding: 8px; text-align: left; }
        th { background-color: #4CAF50; color: white; }
        .warning { background-color: #ffcccc; }
        .ok { background-color: #ccffcc; }
    </style>
</head>
<body>
    <h1>Rapport d'Audit Active Directory</h1>
    <p>Domaine : $domaine</p>
    <p>Date : $(Get-Date -Format 'dd/MM/yyyy HH:mm')</p>
"@

# 1. Comptes désactivés
$html += "<h2>Comptes désactivés</h2>"
$comptesDesactives = Get-ADUser -Filter {Enabled -eq $false} -Properties LastLogonDate
$html += "<p>Total : $($comptesDesactives.Count) comptes</p>"
$html += "<table><tr><th>Nom</th><th>Login</th><th>Dernière connexion</th></tr>"
foreach ($user in $comptesDesactives) {
    $html += "<tr class='warning'>"
    $html += "<td>$($user.Name)</td>"
    $html += "<td>$($user.SamAccountName)</td>"
    $html += "<td>$($user.LastLogonDate)</td>"
    $html += "</tr>"
}
$html += "</table>"

# 2. Comptes sans expiration de mot de passe
$html += "<h2>Comptes sans expiration de mot de passe</h2>"
$comptesNoExpire = Get-ADUser -Filter {PasswordNeverExpires -eq $true -and Enabled -eq $true}
$html += "<p>Total : $($comptesNoExpire.Count) comptes</p>"
$html += "<table><tr><th>Nom</th><th>Login</th></tr>"
foreach ($user in $comptesNoExpire) {
    $html += "<tr class='warning'>"
    $html += "<td>$($user.Name)</td>"
    $html += "<td>$($user.SamAccountName)</td>"
    $html += "</tr>"
}
$html += "</table>"

# 3. Comptes inactifs (>90 jours)
$html += "<h2>Comptes inactifs (>90 jours)</h2>"
$dateLimit = (Get-Date).AddDays(-90)
$comptesInactifs = Get-ADUser -Filter {LastLogonDate -lt $dateLimit -and Enabled -eq $true} -Properties LastLogonDate
$html += "<p>Total : $($comptesInactifs.Count) comptes</p>"
$html += "<table><tr><th>Nom</th><th>Login</th><th>Dernière connexion</th><th>Jours</th></tr>"
foreach ($user in $comptesInactifs) {
    $jours = ((Get-Date) - $user.LastLogonDate).Days
    $html += "<tr class='warning'>"
    $html += "<td>$($user.Name)</td>"
    $html += "<td>$($user.SamAccountName)</td>"
    $html += "<td>$($user.LastLogonDate)</td>"
    $html += "<td>$jours</td>"
    $html += "</tr>"
}
$html += "</table>"

# 4. Membres du groupe Admins
$html += "<h2>Membres du groupe Administrateurs du domaine</h2>"
$admins = Get-ADGroupMember "Domain Admins"
$html += "<p>Total : $($admins.Count) membres</p>"
$html += "<table><tr><th>Nom</th><th>Login</th><th>Type</th></tr>"
foreach ($admin in $admins) {
    $html += "<tr>"
    $html += "<td>$($admin.Name)</td>"
    $html += "<td>$($admin.SamAccountName)</td>"
    $html += "<td>$($admin.objectClass)</td>"
    $html += "</tr>"
}
$html += "</table>"

# Fin du rapport
$html += "</body></html>"

# Enregistrer le rapport
$html | Out-File -FilePath $rapport -Encoding UTF8

Write-Host "✓ Rapport généré : $rapport" -ForegroundColor Green

# Ouvrir le rapport dans le navigateur
Start-Process $rapport
```

### POWERSHELL - Gestion automatique des services

```powershell
# Surveillance et redémarrage automatique de services critiques

# Liste des services à surveiller
$servicesCritiques = @(
    "W3SVC",        # IIS
    "MSSQLSERVER",  # SQL Server
    "Spooler"       # Imprimante
)

# Fichier de log
$logFile = "C:\Logs\Services_$(Get-Date -Format 'yyyyMMdd').log"

# Fonction de log
function Write-Log {
    param($message)
    $timestamp = Get-Date -Format "yyyy-MM-dd HH:mm:ss"
    "$timestamp - $message" | Out-File -FilePath $logFile -Append
    Write-Host $message
}

Write-Log "=== Début de la surveillance ==="

foreach ($serviceName in $servicesCritiques) {
    try {
        $service = Get-Service -Name $serviceName -ErrorAction Stop
        
        if ($service.Status -ne "Running") {
            Write-Log "⚠️  Service $serviceName est $($service.Status)"
            
            # Tentative de redémarrage
            Write-Log "Tentative de redémarrage de $serviceName..."
            Start-Service -Name $serviceName
            
            # Vérification
            Start-Sleep -Seconds 5
            $service = Get-Service -Name $serviceName
            
            if ($service.Status -eq "Running") {
                Write-Log "✓ Service $serviceName redémarré avec succès"
                
                # Envoyer une notification email (à configurer)
                # Send-MailMessage -To "admin@entreprise.com" `
                #                  -Subject "Service $serviceName redémarré" `
                #                  -Body "Le service a été redémarré automatiquement" `
                #                  -SmtpServer "smtp.entreprise.com"
            } else {
                Write-Log "✗ Échec du redémarrage de $serviceName"
            }
        } else {
            Write-Log "✓ Service $serviceName fonctionne correctement"
        }
    }
    catch {
        Write-Log "✗ Erreur avec le service $serviceName : $_"
    }
}

Write-Log "=== Fin de la surveillance ==="
```

---

## 🎓 EXERCICES PRATIQUES

### Exercice 1 - BASH : Script de nettoyage

**Énoncé** : Créez un script qui :
1. Supprime les fichiers `.tmp` de plus de 7 jours dans `/tmp`
2. Vide les logs de plus de 30 jours dans `/var/log`
3. Affiche l'espace disque libéré


```bash
#!/bin/bash

echo "Début du nettoyage..."

# Espace initial
espace_avant=$(df -h / | awk 'NR==2 {print $4}')

# Nettoyage fichiers .tmp
echo "Suppression des fichiers .tmp..."
find /tmp -name "*.tmp" -type f -mtime +7 -delete
echo "✓ Fichiers .tmp supprimés"

# Nettoyage logs
echo "Suppression des vieux logs..."
find /var/log -name "*.log" -type f -mtime +30 -delete
echo "✓ Logs supprimés"

# Espace final
espace_apres=$(df -h / | awk 'NR==2 {print $4}')

echo "Espace avant : $espace_avant"
echo "Espace après : $espace_apres"
echo "Nettoyage terminé"
```

### Exercice 2 - POWERSHELL : Rapport d'espace disque

**Énoncé** : Créez un script qui affiche l'espace disque de tous les disques et alerte si <10% disponible.


```powershell
$disques = Get-PSDrive -PSProvider FileSystem | Where-Object {$_.Used -ne $null}

foreach ($disque in $disques) {
    $totalGo = [math]::Round($disque.Used / 1GB + $disque.Free / 1GB, 2)
    $libreGo = [math]::Round($disque.Free / 1GB, 2)
    $utilisePourcent = [math]::Round(($disque.Used / ($disque.Used + $disque.Free)) * 100, 1)
    
    Write-Host "`nDisque $($disque.Name):"
    Write-Host "Total : $totalGo Go"
    Write-Host "Libre : $libreGo Go"
    Write-Host "Utilisé : $utilisePourcent%"
    
    if ($utilisePourcent -gt 90) {
        Write-Host "⚠️  ALERTE : Disque presque plein !" -ForegroundColor Red
    }
}
```

---

**🚀 Maintenant tu peux analyser n'importe quel script simple !**
