# CORRECTIONS - CCP6 SCRIPTING BASH & POWERSHELL

**Certification TSSR** - CCP6 : Scripts et automatisation  
**10 questions - Corrections détaillées**

---

## ✔️ Question 1 : Script Bash - Sauvegarde automatique (10 pts)

**Énoncé** :  
Créez un script Bash `backup.sh` qui :
- Sauvegarde `/home/user/data` vers `/backup/data_DATE.tar.gz`
- Supprime sauvegardes >7 jours
- Envoie email si échec

**Réponse modèle** :

```bash
#!/bin/bash

# Variables
SOURCE="/home/user/data"
DEST="/backup"
DATE=$(date +%Y%m%d_%H%M%S)
ARCHIVE="data_${DATE}.tar.gz"
EMAIL="admin@entreprise.local"

# Créer sauvegarde
tar -czf "$DEST/$ARCHIVE" "$SOURCE" 2>/dev/null

# Vérifier succès
if [ $? -eq 0 ]; then
    echo "[OK] Sauvegarde créée : $ARCHIVE"
    
    # Supprimer anciennes (>7 jours)
    find "$DEST" -name "data_*.tar.gz" -mtime +7 -delete
    echo "[OK] Anciennes sauvegardes supprimées"
else
    echo "[ERREUR] Échec sauvegarde" | mail -s "Backup FAILED" "$EMAIL"
    exit 1
fi
```

**Points clés** (10 pts) :
- Shebang + variables (2 pts)
- `tar -czf` avec redirection erreurs (2 pts)
- Test `$?` (2 pts)
- `find -mtime +7 -delete` (2 pts)
- Email erreur (1 pt)
- Exit codes appropriés (1 pt)

**Points** : 10/10

---

## ✔️ Question 2 : PowerShell - Rapport AD utilisateurs (12 pts)

**Énoncé** :  
Script PowerShell qui exporte CSV des utilisateurs AD inactifs (>90 jours) avec colonnes : Nom, Login, OU, DernièreConnexion.

**Réponse modèle** :

```powershell
# Import module
Import-Module ActiveDirectory

# Paramètres
$joursinactif = 90
$dateLimite = (Get-Date).AddDays(-$joursinactif)
$csvPath = "C:\Rapports\Inactifs_$(Get-Date -Format 'yyyyMMdd').csv"

# Récupération utilisateurs
Get-ADUser -Filter {Enabled -eq $true} -Properties LastLogonDate, DistinguishedName |
    Where-Object {$_.LastLogonDate -lt $dateLimite -or $_.LastLogonDate -eq $null} |
    Select-Object @{N="Nom";E={$_.Name}},
                  @{N="Login";E={$_.SamAccountName}},
                  @{N="OU";E={($_.DistinguishedName -split ',',2)[1]}},
                  @{N="DerniereConnexion";E={
                      if ($_.LastLogonDate) {$_.LastLogonDate} else {"Jamais"}
                  }} |
    Export-Csv -Path $csvPath -NoTypeInformation -Encoding UTF8

Write-Host "Rapport généré : $csvPath" -ForegroundColor Green
```

**Points clés** (12 pts) :
- Import-Module (1 pt)
- Get-ADUser avec -Filter et -Properties (3 pts)
- Where-Object condition OR (2 pts)
- Select-Object avec propriétés calculées `@{N=;E=}` (3 pts)
- Export-Csv avec options (2 pts)
- Gestion date null (1 pt)

**Points** : 12/12

---

## ✔️ Question 3 : Bash - Analyse logs (8 pts)

**Énoncé** :  
Script qui compte occurrences erreurs dans `/var/log/syslog` et affiche top 5 types erreurs.

**Réponse modèle** :

```bash
#!/bin/bash

LOG="/var/log/syslog"

echo "=== Top 5 erreurs dans $LOG ==="

# Extraire lignes avec "error" (insensible casse)
# Compter occurrences uniques, trier, afficher top 5
grep -i "error" "$LOG" | \
    awk '{print $5,$6,$7,$8}' | \
    sort | uniq -c | \
    sort -rn | \
    head -5

# Total erreurs
total=$(grep -ic "error" "$LOG")
echo ""
echo "TOTAL: $total erreurs trouvées"
```

**Explication** (8 pts) :
- `grep -i "error"` : Recherche insensible casse (2 pts)
- `awk` : Extrait champs pertinents (2 pts)
- `sort | uniq -c` : Compte occurrences (2 pts)
- `sort -rn | head -5` : Top 5 décroissant (1 pt)
- `grep -ic` : Compte total (1 pt)

**Points** : 8/8

---

## ✔️ Question 4 : PowerShell - Gestion services (10 pts)

**Énoncé** :  
Fonction PowerShell `Restart-ServiceSafely` qui redémarre service SEULEMENT s'il est déjà en cours d'exécution.

**Réponse modèle** :

```powershell
function Restart-ServiceSafely {
    param(
        [Parameter(Mandatory=$true)]
        [string]$ServiceName
    )
    
    try {
        $service = Get-Service -Name $ServiceName -ErrorAction Stop
        
        if ($service.Status -eq 'Running') {
            Write-Host "Redémarrage $ServiceName..." -ForegroundColor Yellow
            Restart-Service -Name $ServiceName -Force
            Write-Host "[OK] $ServiceName redémarré" -ForegroundColor Green
        }
        else {
            Write-Host "[INFO] $ServiceName n'est pas actif (statut: $($service.Status))" -ForegroundColor Cyan
        }
    }
    catch {
        Write-Host "[ERREUR] Service $ServiceName introuvable : $_" -ForegroundColor Red
    }
}

# Utilisation
Restart-ServiceSafely -ServiceName "Spooler"
```

**Points clés** (10 pts) :
- Fonction avec `param()` Mandatory (3 pts)
- Try/Catch gestion erreurs (2 pts)
- Get-Service avec -ErrorAction (2 pts)
- Test `if ($service.Status -eq 'Running')` (2 pts)
- Messages colorés informatifs (1 pt)

**Points** : 10/10

---

## ✔️ Question 5 : Bash - Monitoring disque (8 pts)

**Énoncé** :  
Script qui alerte si partition `/` dépasse 85% utilisation.

**Réponse modèle** :

```bash
#!/bin/bash

# Seuil alerte
SEUIL=85

# Récupérer utilisation / (sans le %)
USAGE=$(df -h / | awk 'NR==2 {print $5}' | sed 's/%//')

echo "Utilisation disque / : ${USAGE}%"

if [ "$USAGE" -gt "$SEUIL" ]; then
    echo "[ALERTE] Disque plein à ${USAGE}% (seuil: ${SEUIL}%)" | \
        mail -s "ALERTE Disque Serveur" admin@entreprise.local
    
    # Logger
    logger -p user.warn "Disque / à ${USAGE}%"
    
    exit 1
else
    echo "[OK] Espace disque suffisant"
    exit 0
fi
```

**Points clés** (8 pts) :
- `df -h` extraction utilisation (2 pts)
- `awk 'NR==2 {print $5}'` + `sed 's/%//'` (2 pts)
- Condition `-gt` comparaison numérique (2 pts)
- Email + logger (1 pt)
- Exit codes (1 pt)

**Points** : 8/8

---

## ✔️ Question 6 : PowerShell - Nettoyage fichiers (10 pts)

**Énoncé** :  
Script qui supprime fichiers `.log` et `.tmp` >30 jours dans `C:\Temp`, avec confirmation et rapport.

**Réponse modèle** :

```powershell
# Paramètres
$path = "C:\Temp"
$extensions = @("*.log", "*.tmp")
$jours = 30
$dateLimite = (Get-Date).AddDays(-$jours)

Write-Host "Recherche fichiers >$jours jours dans $path..." -ForegroundColor Cyan

# Trouver fichiers
$fichiers = Get-ChildItem -Path $path -Include $extensions -Recurse -File |
    Where-Object {$_.LastWriteTime -lt $dateLimite}

if ($fichiers) {
    Write-Host "`n$($fichiers.Count) fichiers trouvés :" -ForegroundColor Yellow
    $fichiers | Format-Table Name, Length, LastWriteTime -AutoSize
    
    # Calcul taille totale
    $tailleTotal = ($fichiers | Measure-Object -Property Length -Sum).Sum / 1MB
    Write-Host "`nTaille totale : $([math]::Round($tailleTotal, 2)) MB" -ForegroundColor Yellow
    
    # Confirmation
    $confirm = Read-Host "`nSupprimer ces fichiers ? (O/N)"
    
    if ($confirm -eq "O") {
        $fichiers | Remove-Item -Force
        Write-Host "[OK] $($fichiers.Count) fichiers supprimés ($([math]::Round($tailleTotal, 2)) MB libérés)" -ForegroundColor Green
    }
    else {
        Write-Host "[ANNULE] Aucun fichier supprimé" -ForegroundColor Red
    }
}
else {
    Write-Host "[INFO] Aucun fichier à supprimer" -ForegroundColor Green
}
```

**Points clés** (10 pts) :
- Get-ChildItem -Include avec array (2 pts)
- Where-Object comparaison dates (2 pts)
- Measure-Object calcul taille (2 pts)
- Format-Table affichage (1 pt)
- Read-Host confirmation (2 pts)
- Gestion cas aucun fichier (1 pt)

**Points** : 10/10

---

## ✔️ Question 7 : Bash - Création utilisateurs CSV (10 pts)

**Énoncé** :  
Script qui lit `users.csv` (Prenom,Nom,Groupe) et crée comptes Linux avec mot de passe temporaire.

**Fichier users.csv** :
```
Prenom,Nom,Groupe
Jean,Dupont,rh
Marie,Martin,comptabilite
```

**Réponse modèle** :

```bash
#!/bin/bash

CSV="users.csv"
PASSWORD_DEFAULT="TempPass2025!"

# Vérifier droits root
if [ "$(id -u)" -ne 0 ]; then
    echo "[ERREUR] Script doit être exécuté en root"
    exit 1
fi

# Vérifier fichier existe
if [ ! -f "$CSV" ]; then
    echo "[ERREUR] Fichier $CSV introuvable"
    exit 1
fi

# Lire CSV (skip header)
tail -n +2 "$CSV" | while IFS=',' read -r prenom nom groupe; do
    # Construction login
    login=$(echo "${prenom}.${nom}" | tr '[:upper:]' '[:lower:]')
    
    # Vérifier si groupe existe
    if ! getent group "$groupe" > /dev/null 2>&1; then
        echo "[INFO] Création groupe $groupe"
        groupadd "$groupe"
    fi
    
    # Créer utilisateur
    if ! id "$login" > /dev/null 2>&1; then
        useradd -m -g "$groupe" -s /bin/bash "$login"
        echo "$login:$PASSWORD_DEFAULT" | chpasswd
        chage -d 0 "$login"  # Forcer changement MDP 1ère connexion
        
        echo "[OK] Utilisateur $login créé (groupe: $groupe)"
    else
        echo "[SKIP] Utilisateur $login existe déjà"
    fi
done

echo ""
echo "=== Création terminée ==="
```

**Points clés** (10 pts) :
- Vérif root `id -u` (1 pt)
- Lecture CSV avec `IFS=','` (2 pts)
- `tail -n +2` skip header (1 pt)
- Construction login avec `tr` (2 pts)
- Test existence groupe/user (2 pts)
- `useradd` + `chpasswd` + `chage -d 0` (2 pts)

**Points** : 10/10

---

## ✔️ Question 8 : PowerShell - Comparaison fichiers (8 pts)

**Énoncé** :  
Script qui compare 2 fichiers texte et liste différences (lignes présentes dans fichier1 mais pas fichier2).

**Réponse modèle** :

```powershell
param(
    [Parameter(Mandatory=$true)]
    [string]$File1,
    
    [Parameter(Mandatory=$true)]
    [string]$File2
)

# Vérifier fichiers existent
if (-not (Test-Path $File1)) {
    Write-Error "Fichier $File1 introuvable"
    exit 1
}
if (-not (Test-Path $File2)) {
    Write-Error "Fichier $File2 introuvable"
    exit 1
}

# Lire contenu
$contenu1 = Get-Content $File1
$contenu2 = Get-Content $File2

# Différences (dans fichier1 mais pas fichier2)
$differences = Compare-Object -ReferenceObject $contenu1 -DifferenceObject $contenu2 |
    Where-Object {$_.SideIndicator -eq '<='} |
    Select-Object -ExpandProperty InputObject

if ($differences) {
    Write-Host "=== Lignes dans $File1 mais absentes de $File2 ===" -ForegroundColor Yellow
    $differences | ForEach-Object { Write-Host $_ }
    Write-Host "`nTotal: $($differences.Count) différence(s)" -ForegroundColor Cyan
}
else {
    Write-Host "[OK] Aucune différence (fichier2 contient toutes lignes fichier1)" -ForegroundColor Green
}
```

**Points clés** (8 pts) :
- Paramètres Mandatory (1 pt)
- Test-Path vérification (2 pts)
- Compare-Object (2 pts)
- Where-Object SideIndicator '<=' (2 pts)
- Gestion cas aucune différence (1 pt)

**Points** : 8/8

---

## ✔️ Question 9 : Bash - Parsing arguments (8 pts)

**Énoncé** :  
Script `backup.sh` acceptant options : `-s <source>`, `-d <destination>`, `-v` (verbose). Exemple : `./backup.sh -s /data -d /backup -v`

**Réponse modèle** :

```bash
#!/bin/bash

# Valeurs par défaut
SOURCE=""
DEST=""
VERBOSE=false

# Parsing arguments
while getopts "s:d:vh" opt; do
    case $opt in
        s) SOURCE="$OPTARG" ;;
        d) DEST="$OPTARG" ;;
        v) VERBOSE=true ;;
        h)
            echo "Usage: $0 -s <source> -d <destination> [-v]"
            echo "  -s  Répertoire source"
            echo "  -d  Répertoire destination"
            echo "  -v  Mode verbose"
            exit 0
            ;;
        \?)
            echo "Option invalide: -$OPTARG" >&2
            exit 1
            ;;
    esac
done

# Vérifier paramètres obligatoires
if [ -z "$SOURCE" ] || [ -z "$DEST" ]; then
    echo "[ERREUR] Options -s et -d obligatoires"
    echo "Usage: $0 -s <source> -d <destination> [-v]"
    exit 1
fi

# Exécution sauvegarde
[ "$VERBOSE" = true ] && echo "[INFO] Source: $SOURCE, Destination: $DEST"

tar -czf "$DEST/backup_$(date +%Y%m%d).tar.gz" "$SOURCE"

[ "$VERBOSE" = true ] && echo "[OK] Sauvegarde terminée"
```

**Points clés** (8 pts) :
- `getopts "s:d:vh"` (`:` = argument requis) (3 pts)
- `case $opt in` traitement options (2 pts)
- `$OPTARG` récupération valeur (1 pt)
- Vérification obligatoires `-z` (1 pt)
- Usage/aide (1 pt)

**Points** : 8/8

---

## ✔️ Question 10 : PowerShell - Planification tâche (10 pts)

**Énoncé** :  
Créez tâche planifiée qui exécute `C:\Scripts\Rapport.ps1` tous les lundis à 8h avec compte SYSTEM.

**Réponse modèle** :

```powershell
# Paramètres tâche
$taskName = "Rapport_Hebdomadaire"
$scriptPath = "C:\Scripts\Rapport.ps1"
$trigger = New-ScheduledTaskTrigger -Weekly -DaysOfWeek Monday -At "08:00"
$action = New-ScheduledTaskAction -Execute "powershell.exe" `
    -Argument "-NoProfile -ExecutionPolicy Bypass -File `"$scriptPath`""
$principal = New-ScheduledTaskPrincipal -UserId "SYSTEM" -LogonType ServiceAccount -RunLevel Highest

# Créer tâche
Register-ScheduledTask -TaskName $taskName `
    -Trigger $trigger `
    -Action $action `
    -Principal $principal `
    -Description "Génération rapport hebdomadaire automatique"

Write-Host "[OK] Tâche planifiée '$taskName' créée" -ForegroundColor Green

# Vérification
Get-ScheduledTask -TaskName $taskName | Select-Object TaskName, State, Triggers
```

**Points clés** (10 pts) :
- New-ScheduledTaskTrigger -Weekly (2 pts)
- New-ScheduledTaskAction avec arguments PowerShell (3 pts)
- New-ScheduledTaskPrincipal SYSTEM (2 pts)
- Register-ScheduledTask (2 pts)
- Vérification Get-ScheduledTask (1 pt)

**Points** : 10/10

---

## 📊 BARÈME TOTAL : 94 POINTS

**Répartition** :
- Scripts Bash (Q1, 3, 5, 7, 9) : 44 pts
- Scripts PowerShell (Q2, 4, 6, 8, 10) : 50 pts

**Note sur 20** : Total points / 4.7

---

**CONSEILS EXAMEN** :
- **Structure** : Shebang/paramètres → Variables → Logique → Gestion erreurs
- **Bash** : Tester conditions `[ ]`, redirections `2>&1`, exit codes `$?`
- **PowerShell** : Try/Catch, pipeline `|`, propriétés calculées `@{N=;E=}`
- **Lisibilité** : Commentaires, indentation, messages informatifs
