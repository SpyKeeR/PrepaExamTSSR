# EXAMEN BLANC 2 - CORRECTIONS ET BARÈME

**Examen** : TSSR - Technicien Supérieur Systèmes et Réseaux  
**Type** : Examen blanc complet (MSP + Questionnaire technique)  
**Durée** : 3h30 (210 minutes)  
**Total** : **200 points**

---

## PARTIE 1 : MSP ÉCRITE (100 points)

---

## 🌐 INCIDENT 1 : PROBLÈME DNS - RÉSOLUTION NOMS (30 points)

### ✔️ Question 1.1 (5 points) - Cause racine

**Réponse attendue** :

La cause racine est l'**absence de serveurs DNS** dans la configuration réseau des PC de la Comptabilité.

**Analyse** :
```cmd
ipconfig /all
   Serveurs DNS : (vide)
```

**Pourquoi ce problème** :
- Les PC ont été **redémarrés** ce week-end (mises à jour Windows)
- Probable **perte de configuration IP** (si IP statiques mal configurées)
- Ou **problème DHCP** : L'option 006 (serveurs DNS) n'est pas fournie pour le VLAN/réseau Comptabilité

**Conséquences** :
- ✅ **Ping par IP** fonctionne : Connectivité réseau OK
- ❌ **Ping par nom** échoue : Pas de résolution DNS
- ❌ Accès `\\serveur-fichiers` : Besoin de résolution NetBIOS/DNS
- ❌ Internet : Besoin de résolution DNS pour sites web
- ❌ Outlook/Exchange : Besoin de résolution DNS

**Barème** :
- Identification "Serveurs DNS absents" : **3 pts**
- Explication conséquences : **2 pts**

---

### ✔️ Question 1.2 (8 points) - Solution immédiate (temporaire)

**Réponse attendue** :

**Solution 1 : Configuration manuelle DNS sur chaque PC** (recommandée)

**Sur chaque poste Windows** :

1. **Ouvrir Propriétés TCP/IPv4** :
   - Panneau de configuration → Réseau et Internet → Centre Réseau
   - Modifier les paramètres de la carte
   - Clic droit sur "Ethernet" → Propriétés
   - Double-clic sur "Protocole Internet version 4 (TCP/IPv4)"

2. **Configurer les serveurs DNS** :
   ```
   Serveur DNS préféré : 192.168.10.10  (SRV-DNS-01 interne)
   Serveur DNS auxiliaire : 8.8.8.8      (Google DNS public)
   ```

3. **Appliquer et tester** :
   ```cmd
   ipconfig /flushdns
   ipconfig /registerdns
   nslookup serveur-fichiers
   ping serveur-fichiers
   ```

---

**Solution 2 : Script PowerShell pour automatiser (plus rapide)** :

```powershell
# Exécuter sur chaque PC (ou via GPO en démarrage)
$Adapter = Get-NetAdapter | Where-Object {$_.Status -eq "Up"}
Set-DnsClientServerAddress -InterfaceIndex $Adapter.ifIndex -ServerAddresses ("192.168.10.10","8.8.8.8")
ipconfig /flushdns
Write-Host "DNS configurés : 192.168.10.10 et 8.8.8.8" -ForegroundColor Green
```

Enregistrer en `Fix-DNS.ps1`, exécuter avec droits admin :
```powershell
powershell.exe -ExecutionPolicy Bypass -File C:\Scripts\Fix-DNS.ps1
```

---

**Solution 3 : Commande CMD rapide** :

```cmd
netsh interface ip set dns "Ethernet" static 192.168.10.10 primary
netsh interface ip add dns "Ethernet" 8.8.8.8 index=2
ipconfig /flushdns
```

---

**Barème** :
- Accès propriétés TCP/IP : **2 pts**
- Configuration DNS primaire et secondaire : **4 pts**
- Test (flushdns, nslookup) : **2 pts**

---

### ✔️ Question 1.3 (10 points) - Solution pérenne

**Réponse attendue** :

**Diagnostic** : Identifier où se situe le problème

**1) Vérifier la configuration DHCP** (cause la plus probable) :

**Sur le serveur DHCP Windows** :
```
Gestionnaire DHCP (dhcpmgmt.msc)
→ IPv4
→ Étendue [192.168.30.0] Comptabilité
→ Options d'étendue
```

**Vérifier Option 006 - Serveurs DNS** :
- Si **absente ou vide** → C'EST LE PROBLÈME
- Devrait contenir : `192.168.10.10` et `8.8.8.8`

---

**Solution pérenne : Configurer l'option DNS dans l'étendue DHCP**

**Procédure** :

1. **Ouvrir Gestionnaire DHCP** :
   ```
   dhcpmgmt.msc
   ```

2. **Naviguer vers l'étendue Comptabilité** :
   ```
   SRV-DHCP → IPv4 → Étendue [192.168.30.0]
   ```

3. **Configurer options d'étendue** :
   - Clic droit sur **"Options d'étendue"** → **"Configurer les options"**
   - ☑ **006 Serveurs DNS**
   - Ajouter :
     - `192.168.10.10` (DNS interne)
     - `8.8.8.8` (DNS public Google)
   - Cliquer **OK**

4. **Vérifier autres options essentielles** :
   - ☑ **003 Routeur** : `192.168.30.1`
   - ☑ **015 Nom de domaine DNS** : `megastore.local`

5. **Appliquer** :
   - Les nouveaux baux DHCP incluront les DNS
   - Pour les PC actuels : Renouveler bail DHCP

---

**Sur chaque PC** (forcer renouvellement) :
```cmd
ipconfig /release
ipconfig /renew
ipconfig /all
```

Vérifier :
```
Serveurs DNS : 192.168.10.10, 8.8.8.8
```

---

**Alternative : GPO (si IPs statiques)** :

Si les PC ont des **IP statiques** (pas DHCP), configurer DNS par GPO :

```
Gestion de stratégie de groupe (gpmc.msc)
→ Créer/Modifier GPO "Config DNS Comptabilité"
→ Configuration ordinateur
   → Stratégies
      → Modèles d'administration
         → Réseau
            → Client DNS
               → Serveurs DNS
                  → Activer
                  → Ajouter : 192.168.10.10, 8.8.8.8
→ Lier la GPO à l'OU Comptabilité
→ gpupdate /force sur les PC
```

---

**Barème** :
- Identification source (étendue DHCP) : **3 pts**
- Procédure configuration option 006 : **5 pts**
- Renouvellement baux : **2 pts**

---

### ✔️ Question 1.4 (4 points) - Tests de validation

**Réponse attendue** :

**3 tests pour valider la résolution DNS** :

---

**Test 1 : nslookup (résolution directe)** :

```cmd
nslookup serveur-fichiers
```

**Résultat attendu** :
```
Serveur :   SRV-DNS-01.megastore.local
Adresse :   192.168.10.10

Nom :       serveur-fichiers.megastore.local
Adresse :   192.168.10.20
```

✅ Si IP affichée → DNS interne fonctionne

---

**Test 2 : nslookup externe (Internet)** :

```cmd
nslookup www.google.com
```

**Résultat attendu** :
```
Serveur :   google-public-dns-a.google.com
Adresse :   8.8.8.8

Nom :       www.google.com
Adresses :  142.250.185.36, ...
```

✅ Si IP affichée → DNS public fonctionne (Google 8.8.8.8)

---

**Test 3 : Ping par nom** :

```cmd
ping serveur-fichiers
ping www.microsoft.com
```

**Résultat attendu** :
```
Réponse de 192.168.10.20 : octets=32 temps<1ms TTL=64
Réponse de 20.112.52.29 : octets=32 temps=15ms TTL=117
```

✅ Si réponse → Résolution + connectivité OK

---

**Test bonus : Accès ressources** :

```cmd
net use Z: \\serveur-fichiers\partages
```

✅ Si montage réussit → DNS + SMB fonctionnent

**Test Outlook** : Ouvrir Outlook → Connexion Exchange doit fonctionner

---

**Barème** :
- Test 1 (nslookup interne) : **1.5 pts**
- Test 2 (nslookup externe) : **1.5 pts**
- Test 3 (ping ou accès ressource) : **1 pt**

---

### ✔️ Question 1.5 (3 points) - Fichier hosts

**Réponse attendue** :

**Fichier hosts** : `C:\Windows\System32\drivers\etc\hosts`

**Rôle** :
- **Table de correspondance statique** : Nom d'hôte → Adresse IP
- **Résolution locale** : Priorité sur DNS (consulté en premier)
- Format : `<IP>    <nom_hôte>   [alias]`

**Exemple de contenu** :
```
# Fichier hosts Windows

# Serveurs internes
192.168.10.10    srv-dns-01          dns01
192.168.10.20    serveur-fichiers    fichiers
192.168.10.30    srv-exchange        mail

# Serveurs web
192.168.100.50   srv-web-prod        web
```

---

**Contexte d'utilisation comme contournement** :

**1) Résolution DNS indisponible** :
- Serveur DNS en panne
- Configuration DNS incorrecte
- Permet accès temporaire aux ressources critiques

**2) Tests en développement** :
- Résolution personnalisée sans modifier DNS
- Exemple : `192.168.1.100  test.megastore.local`

**3) Blocage de sites** :
- Rediriger domaines indésirables : `127.0.0.1  facebook.com`

**4) Urgence (incident comme aujourd'hui)** :

Ajouter temporairement dans `hosts` :
```
192.168.10.20    serveur-fichiers
192.168.10.30    srv-exchange
```

**Avantages** :
- ✅ Solution **immédiate** (quelques secondes)
- ✅ Pas besoin de redémarrage
- ✅ Fonctionne même si DNS HS

**Inconvénients** :
- ❌ **Pas scalable** : 1 fichier par PC (15 PC Compta = 15 modifications)
- ❌ **Pas dynamique** : Si IP serveur change, modifier tous les hosts
- ❌ **Maintenance lourde** : Ajouter/supprimer entrées manuellement

---

**Commandes** :

**Éditer le fichier** :
```cmd
notepad C:\Windows\System32\drivers\etc\hosts
```
(Nécessite droits admin)

**Vider le cache DNS après modification** :
```cmd
ipconfig /flushdns
```

---

**Barème** :
- Rôle fichier hosts : **2 pts**
- Contexte utilisation contournement : **1 pt**

---

---

## 🔐 INCIDENT 2 : SCRIPT BASH - SAUVEGARDE AUTOMATISÉE (30 points)

### ✔️ Question 2.1 (15 points) - Script Bash complet

**Réponse attendue** :

```bash
#!/bin/bash

################################################################################
# Script de sauvegarde automatisée des fichiers de configuration
# Auteur : Équipe IT MegaStore
# Date : 12/11/2025
# Description : Sauvegarde quotidienne configs critiques avec rotation 30j
################################################################################

# Variables
BACKUP_DIR="/srv/backups/configs"
LOG_FILE="/var/log/backup_config.log"
DATE=$(date +%Y%m%d_%H%M%S)
BACKUP_NAME="backup_config_${DATE}.tar.gz"
BACKUP_PATH="${BACKUP_DIR}/${BACKUP_NAME}"
RETENTION_DAYS=30
EMAIL_TO="admin@megastore.local"
EMAIL_SUBJECT="[BACKUP] Rapport sauvegarde config - ${DATE}"

# Fichiers à sauvegarder
FILES_TO_BACKUP=(
    "/etc/network/interfaces"
    "/etc/fstab"
    "/etc/exports"
    "/etc/apache2/sites-available/"
    "/etc/bind/"
)

# Fonction de log
log_message() {
    echo "[$(date +'%Y-%m-%d %H:%M:%S')] $1" | tee -a "$LOG_FILE"
}

# Début du script
log_message "=== DÉBUT SAUVEGARDE ==="

# Créer le dossier de backup s'il n'existe pas
if [ ! -d "$BACKUP_DIR" ]; then
    mkdir -p "$BACKUP_DIR"
    log_message "Création du dossier $BACKUP_DIR"
fi

# Création de l'archive
log_message "Création archive : $BACKUP_NAME"

tar -czf "$BACKUP_PATH" \
    /etc/network/interfaces \
    /etc/fstab \
    /etc/exports \
    /etc/apache2/sites-available/ \
    /etc/bind/ \
    2>> "$LOG_FILE"

# Vérifier succès de la création
if [ $? -eq 0 ]; then
    log_message "✓ Archive créée avec succès"
    
    # Taille de l'archive
    BACKUP_SIZE=$(du -h "$BACKUP_PATH" | cut -f1)
    log_message "Taille archive : $BACKUP_SIZE"
    
    # Nombre de fichiers sauvegardés
    FILE_COUNT=$(tar -tzf "$BACKUP_PATH" | wc -l)
    log_message "Fichiers sauvegardés : $FILE_COUNT"
    
    STATUS="SUCCÈS"
    
else
    log_message "✗ ERREUR lors de la création de l'archive"
    STATUS="ÉCHEC"
    BACKUP_SIZE="N/A"
    FILE_COUNT="0"
fi

# Rotation : Suppression archives > 30 jours
log_message "Rotation : Suppression archives > ${RETENTION_DAYS} jours"
DELETED_COUNT=$(find "$BACKUP_DIR" -name "backup_config_*.tar.gz" -type f -mtime +${RETENTION_DAYS} -delete -print | wc -l)
log_message "Archives supprimées : $DELETED_COUNT"

# Envoi email récapitulatif
EMAIL_BODY="Rapport de sauvegarde automatique

=== RÉSUMÉ ===
Statut : ${STATUS}
Archive : ${BACKUP_NAME}
Taille : ${BACKUP_SIZE}
Fichiers sauvegardés : ${FILE_COUNT}
Emplacement : ${BACKUP_PATH}

=== ROTATION ===
Archives supprimées (> ${RETENTION_DAYS}j) : ${DELETED_COUNT}

=== LOGS ===
Consultez les logs complets : ${LOG_FILE}

---
Serveur : $(hostname)
Date : $(date +'%Y-%m-%d %H:%M:%S')
"

# Envoi email (avec mail ou sendmail)
echo "$EMAIL_BODY" | mail -s "$EMAIL_SUBJECT" "$EMAIL_TO"

if [ $? -eq 0 ]; then
    log_message "Email envoyé à $EMAIL_TO"
else
    log_message "ERREUR : Échec envoi email"
fi

log_message "=== FIN SAUVEGARDE ==="
log_message ""

exit 0
```

---

**Points clés du script** :

1. **Shebang** : `#!/bin/bash`
2. **Variables** : Centralisation chemins, dates, config
3. **Fonction log** : Horodatage + écriture dans fichier
4. **Création archive** : `tar -czf` avec tous les fichiers
5. **Vérification succès** : `if [ $? -eq 0 ]`
6. **Informations** : Taille (`du -h`), nombre fichiers (`tar -tzf | wc -l`)
7. **Rotation** : `find ... -mtime +30 -delete`
8. **Email** : Corps formaté avec résumé
9. **Logs** : Toutes actions tracées

---

**Barème** :
- Structure générale (shebang, variables) : **2 pts**
- Création archive tar.gz : **4 pts**
- Nom archive avec date/heure : **2 pts**
- Rotation 30 jours (find -mtime) : **3 pts**
- Logs avec horodatage : **2 pts**
- Email récapitulatif : **2 pts**

---

### ✔️ Question 2.2 (5 points) - Commande cron

**Réponse attendue** :

**Commande crontab pour exécution quotidienne à 2h00** :

```bash
0 2 * * * /usr/local/bin/backup_config.sh >> /var/log/backup_config_cron.log 2>&1
```

---

**Explication syntaxe cron** :

```
┌─────────── Minute (0-59)
│ ┌───────── Heure (0-23)
│ │ ┌─────── Jour du mois (1-31)
│ │ │ ┌───── Mois (1-12)
│ │ │ │ ┌─── Jour de la semaine (0-7, 0 et 7 = dimanche)
│ │ │ │ │
* * * * * commande à exécuter
```

**Notre commande** :
```
0 2 * * * /usr/local/bin/backup_config.sh
```

- `0` : Minute = 0 (à l'heure pile)
- `2` : Heure = 2h du matin
- `*` : Tous les jours du mois
- `*` : Tous les mois
- `*` : Tous les jours de la semaine

**Résultat** : Exécution **tous les jours à 2h00**

---

**Redirection logs** :
```bash
>> /var/log/backup_config_cron.log 2>&1
```
- `>>` : Ajouter stdout à la fin du fichier
- `2>&1` : Rediriger stderr vers stdout

---

**Installation dans crontab** :

**Méthode 1 : Éditer crontab root** :
```bash
sudo crontab -e
```
Ajouter la ligne :
```
0 2 * * * /usr/local/bin/backup_config.sh >> /var/log/backup_config_cron.log 2>&1
```
Sauvegarder et quitter.

---

**Méthode 2 : Fichier dans /etc/cron.d/** :
```bash
sudo nano /etc/cron.d/backup_config
```
Contenu :
```
# Sauvegarde quotidienne configs
0 2 * * * root /usr/local/bin/backup_config.sh >> /var/log/backup_config_cron.log 2>&1
```

---

**Vérifier crontab active** :
```bash
sudo crontab -l
```

**Vérifier service cron** :
```bash
sudo systemctl status cron
```

---

**Exemples autres fréquences** :

```bash
# Toutes les heures
0 * * * * /script.sh

# Tous les lundis à 8h
0 8 * * 1 /script.sh

# Premier jour du mois à minuit
0 0 1 * * /script.sh

# Toutes les 30 minutes
*/30 * * * * /script.sh

# Jours ouvrés (lundi-vendredi) à 18h
0 18 * * 1-5 /script.sh
```

---

**Barème** :
- Syntaxe cron correcte (0 2 * * *) : **3 pts**
- Explication de la syntaxe : **2 pts**

---

### ✔️ Question 2.3 (5 points) - Tester le script

**Réponse attendue** :

**Étapes de validation du script** :

---

**1) Rendre le script exécutable** :

```bash
sudo chmod +x /usr/local/bin/backup_config.sh
```

Vérifier permissions :
```bash
ls -l /usr/local/bin/backup_config.sh
# Doit afficher : -rwxr-xr-x
```

---

**2) Créer dossiers nécessaires** :

```bash
sudo mkdir -p /srv/backups/configs
sudo touch /var/log/backup_config.log
sudo chown root:root /var/log/backup_config.log
sudo chmod 640 /var/log/backup_config.log
```

---

**3) Test syntaxe Bash** :

```bash
bash -n /usr/local/bin/backup_config.sh
```

- Aucune sortie = Syntaxe OK
- Erreurs affichées = Corriger avant exécution

---

**4) Exécution manuelle en mode debug** :

```bash
sudo bash -x /usr/local/bin/backup_config.sh
```

- `-x` : Mode debug (affiche chaque commande exécutée)
- Observer le déroulement étape par étape
- Identifier erreurs éventuelles

---

**5) Exécution réelle** :

```bash
sudo /usr/local/bin/backup_config.sh
```

Surveiller :
- Messages dans le terminal
- Création archive dans `/srv/backups/configs/`
- Logs dans `/var/log/backup_config.log`

---

**6) Vérifications post-exécution** :

**a) Vérifier l'archive créée** :
```bash
ls -lh /srv/backups/configs/
# Doit afficher : backup_config_YYYYMMDD_HHMMSS.tar.gz
```

**b) Vérifier le contenu de l'archive** :
```bash
tar -tzf /srv/backups/configs/backup_config_*.tar.gz | head -20
# Doit lister : etc/network/interfaces, etc/fstab, etc/bind/*, etc/apache2/*, etc/exports
```

**c) Tester l'extraction** :
```bash
mkdir /tmp/test_restore
cd /tmp/test_restore
tar -xzf /srv/backups/configs/backup_config_*.tar.gz
tree etc/
```

**d) Vérifier les logs** :
```bash
cat /var/log/backup_config.log
# Vérifier :
# - Messages horodatés
# - "Archive créée avec succès"
# - Taille archive
# - Nombre de fichiers
```

**e) Vérifier l'email** :
```bash
# Vérifier file d'attente mail
mailq

# Ou logs mail
tail -f /var/log/mail.log
```

---

**7) Tester la rotation (simulation)** :

Créer archives anciennes factices :
```bash
cd /srv/backups/configs/
touch -d "40 days ago" backup_config_20250901_020000.tar.gz
touch -d "35 days ago" backup_config_20250906_020000.tar.gz
ls -lt
```

Exécuter script :
```bash
sudo /usr/local/bin/backup_config.sh
```

Vérifier suppression :
```bash
ls -lt /srv/backups/configs/
# Les archives de > 30 jours doivent être supprimées
```

---

**8) Test final avec cron (optionnel)** :

Créer entrée cron temporaire (exécution dans 2 min) :
```bash
# Il est 14h30 actuellement
echo "32 14 * * * /usr/local/bin/backup_config.sh" | sudo crontab -
```

Attendre 2 minutes et vérifier :
```bash
ls -lt /srv/backups/configs/
tail /var/log/backup_config.log
```

Restaurer crontab normale :
```bash
echo "0 2 * * * /usr/local/bin/backup_config.sh" | sudo crontab -
```

---

**Barème** :
- Chmod +x (exécutable) : **1 pt**
- Test syntaxe (bash -n) : **1 pt**
- Exécution manuelle : **2 pts**
- Vérifications (archive, logs, contenu) : **1 pt**

---

### ✔️ Question 2.4 (3 points) - Vérification intégrité MD5

**Réponse attendue** :

**Ajouter vérification intégrité par somme de contrôle MD5** :

**Code à insérer dans le script** (après création archive) :

```bash
# Vérifier succès de la création
if [ $? -eq 0 ]; then
    log_message "✓ Archive créée avec succès"
    
    # Taille de l'archive
    BACKUP_SIZE=$(du -h "$BACKUP_PATH" | cut -f1)
    log_message "Taille archive : $BACKUP_SIZE"
    
    # Nombre de fichiers sauvegardés
    FILE_COUNT=$(tar -tzf "$BACKUP_PATH" | wc -l)
    log_message "Fichiers sauvegardés : $FILE_COUNT"
    
    # === NOUVEAU : Vérification intégrité MD5 ===
    log_message "Calcul somme de contrôle MD5..."
    
    # Générer hash MD5
    MD5SUM=$(md5sum "$BACKUP_PATH" | awk '{print $1}')
    log_message "MD5 : $MD5SUM"
    
    # Enregistrer hash dans fichier dédié
    MD5_FILE="${BACKUP_PATH}.md5"
    echo "$MD5SUM  $BACKUP_NAME" > "$MD5_FILE"
    log_message "Hash MD5 enregistré : $MD5_FILE"
    
    # Vérifier immédiatement l'intégrité
    log_message "Vérification intégrité archive..."
    cd "$BACKUP_DIR"
    md5sum -c "$MD5_FILE" >> "$LOG_FILE" 2>&1
    
    if [ $? -eq 0 ]; then
        log_message "✓ Intégrité vérifiée : Archive OK"
        INTEGRITY="OK"
    else
        log_message "✗ ERREUR : Intégrité compromise !"
        INTEGRITY="ÉCHEC"
    fi
    # === FIN VÉRIFICATION MD5 ===
    
    STATUS="SUCCÈS"
    
else
    log_message "✗ ERREUR lors de la création de l'archive"
    STATUS="ÉCHEC"
    BACKUP_SIZE="N/A"
    FILE_COUNT="0"
    INTEGRITY="N/A"
fi
```

---

**Mettre à jour l'email** pour inclure info MD5 :

```bash
EMAIL_BODY="Rapport de sauvegarde automatique

=== RÉSUMÉ ===
Statut : ${STATUS}
Archive : ${BACKUP_NAME}
Taille : ${BACKUP_SIZE}
Fichiers sauvegardés : ${FILE_COUNT}
Emplacement : ${BACKUP_PATH}

=== INTÉGRITÉ ===
Hash MD5 : ${MD5SUM}
Vérification : ${INTEGRITY}
Fichier MD5 : ${MD5_FILE}

=== ROTATION ===
Archives supprimées (> ${RETENTION_DAYS}j) : ${DELETED_COUNT}

=== LOGS ===
Consultez les logs complets : ${LOG_FILE}

---
Serveur : $(hostname)
Date : $(date +'%Y-%m-%d %H:%M:%S')
"
```

---

**Utilisation du fichier MD5 pour vérification ultérieure** :

**Vérifier intégrité d'une archive ancienne** :
```bash
cd /srv/backups/configs/
md5sum -c backup_config_20251112_020000.tar.gz.md5
```

**Sortie attendue** :
```
backup_config_20251112_020000.tar.gz: OK
```

Si corruption :
```
backup_config_20251112_020000.tar.gz: ÉCHEC
md5sum: ATTENTION: 1 somme de contrôle n'a PAS correspondu
```

---

**Alternative : SHA256 (plus sécurisé)** :

Remplacer `md5sum` par `sha256sum` :
```bash
SHA256SUM=$(sha256sum "$BACKUP_PATH" | awk '{print $1}')
echo "$SHA256SUM  $BACKUP_NAME" > "${BACKUP_PATH}.sha256"
sha256sum -c "${BACKUP_PATH}.sha256"
```

---

**Barème** :
- Calcul MD5 (md5sum) : **2 pts**
- Enregistrement hash dans fichier : **1 pt**

---

### ✔️ Question 2.5 (2 points) - Permissions script

**Réponse attendue** :

**Objectif** : Script exécutable **uniquement par root**, pas par autres utilisateurs.

**Commande complète** :

```bash
sudo chmod 700 /usr/local/bin/backup_config.sh
```

---

**Explication** :

**700** = **rwx------** :
- **Propriétaire (root)** : `7` = `rwx` = Lecture + Écriture + Exécution
- **Groupe** : `0` = `---` = Aucun droit
- **Autres** : `0` = `---` = Aucun droit

---

**Vérifier** :

```bash
ls -l /usr/local/bin/backup_config.sh
```

**Sortie attendue** :
```
-rwx------ 1 root root 3542 nov 12 14:30 /usr/local/bin/backup_config.sh
```

---

**Propriétaire** :

```bash
sudo chown root:root /usr/local/bin/backup_config.sh
```

S'assurer que le fichier appartient à `root:root`.

---

**Permissions recommandées pour les fichiers associés** :

```bash
# Dossier backups : root seul en écriture
sudo chmod 755 /srv/backups/
sudo chmod 700 /srv/backups/configs/

# Fichier log : root en écriture, groupe lecture
sudo chmod 640 /var/log/backup_config.log
sudo chown root:adm /var/log/backup_config.log

# Archives : root seul en lecture/écriture
sudo chmod 600 /srv/backups/configs/*.tar.gz
```

---

**Alternative : Groupe admin** :

Si d'autres admins doivent exécuter le script :
```bash
sudo chmod 750 /usr/local/bin/backup_config.sh
sudo chown root:admins /usr/local/bin/backup_config.sh
```

- Propriétaire (root) : rwx
- Groupe (admins) : r-x (lecture + exécution)
- Autres : ---

---

**Barème** :
- Commande chmod 700 correcte : **2 pts**

---

**FIN CORRECTIONS PARTIE 1 - Suite dans PARTIE 2...**


---

# ✅ EXAMEN BLANC 2 - CORRECTIONS DÉTAILLÉES
## Partie 1 : MSP Écrite (suite) + Questionnaire Technique

---

## 🖧 INCIDENT 3 : PROBLÈME VMWARE - VM INACCESSIBLE (40 points)

### ✔️ Question 3.1 (6 points) - Cause racine

**Réponse attendue** :

**Cause racine** : **Saturation du datastore** causée par une **chaîne de snapshots trop longue**.

**Analyse** :

**1) Croissance exponentielle des snapshots** :

Les snapshots créent des fichiers **delta** qui capturent les modifications :
```
SRV-WEB-PROD.vmdk          100 Go (disque de base)
SRV-WEB-PROD-000001.vmdk    25 Go (snapshot 1 - vendredi)
SRV-WEB-PROD-000002.vmdk    40 Go (+ modifications)
SRV-WEB-PROD-000003.vmdk    55 Go (+ modifications)
SRV-WEB-PROD-000004.vmdk    80 Go (+ modifications)
Total VM : 300 Go au lieu de 100 Go initiaux !
```

**Pourquoi cette croissance** :
- Disque en **thin provisioning** (100 Go)
- Chaque snapshot capture **toutes les modifications**
- 4 snapshots créés (probablement tests le week-end)
- Snapshots **jamais supprimés** → Accumulation

---

**2) Saturation du datastore** :

```
Datastore DS_PROD_01 : 2 To
  - Utilisés : 1.9 To (95%)
  - Libres : 100 Go (5%)

SRV-WEB-PROD occupe : 308 Go
  - Dont 200 Go de snapshots (65%)
```

**Conséquence** :
- VM ne peut plus **écrire** (swap, logs, modifications disque)
- Tentative de démarrage échoue : "Insufficient disk space"
- vSphere HA essaie de redémarrer sur autre hôte → **Même problème** (datastore partagé)

---

**3) Thin Provisioning aggrave le problème** :

**Thin** :
- Disque **alloué dynamiquement** (grossit avec l'usage)
- Initialement : Quelques Go utilisés
- Avec snapshots : **Expansion rapide**

Si c'était **Thick Eager Zeroed** :
- 100 Go réservés dès la création
- Snapshots auraient été **bloqués** plus tôt (manque d'espace)
- **Problème détecté avant saturation complète**

---

**Relation snapshots ↔ saturation** :

```
Snapshot 1 (vendredi 18h) : +25 Go
   ↓
Week-end : VM continue de tourner
   ↓
Modifications importantes (logs, BDD temporaire, cache)
   ↓
Snapshots 2, 3, 4 créés successivement pour tests
   ↓
Chaque snapshot = NOUVEAU fichier delta
   ↓
Chaîne : Base → Δ1 → Δ2 → Δ3 → Δ4
   ↓
Total : 300 Go au lieu de 100 Go
   ↓
Datastore saturé → VM ne démarre plus
```

---

**Barème** :
- Identification "snapshots trop nombreux" : **3 pts**
- Explication croissance fichiers delta : **2 pts**
- Lien avec saturation datastore : **1 pt**

---

### ✔️ Question 3.2 (12 points) - Solution d'urgence

**Réponse attendue** :

**Objectif** : Libérer de l'espace **rapidement** pour redémarrer la VM prod.

---

**OPTION 1 : Supprimer fichiers temporaires/logs sur le datastore** (RAPIDE)

**1) Se connecter à vSphere Client** :
- Ouvrir navigateur → `https://<vcenter-ip>`
- Login avec compte administrateur

**2) Naviguer vers le datastore** :
```
Inventaire → Stockage → DS_PROD_01
→ Onglet "Fichiers"
```

**3) Identifier fichiers volumineux à supprimer** :

**Fichiers candidats** :
- **Fichiers .vswp** (swap) des VMs éteintes : ~8 Go par VM
- **Fichiers .log** anciens : Peut atteindre plusieurs Go
- **ISO** non utilisés
- **Snapshots** de VMs non critiques (si existants)

**4) Supprimer prudemment** :

⚠️ **NE PAS SUPPRIMER** :
- Fichiers `.vmdk` (disques)
- Fichiers `.vmx` (configuration)
- Snapshots de la VM de prod (pour l'instant)

✅ **Supprimer** :
- `.log` volumineux (garder les plus récents)
- `.vswp` si VM éteinte
- ISO inutilisés

**Procédure** :
```
Clic droit sur fichier → Supprimer
Confirmer
```

**Gain potentiel** : 20-50 Go

---

**OPTION 2 : Déplacer une VM non critique vers autre datastore** (MOYEN)

**Si autre datastore disponible avec espace** :

**1) Identifier VM à déplacer** :
- VM **non critique** et **volumineuse**
- Exemple : `SRV-FILE-01` (600 Go)

**2) Storage vMotion** :
```
Clic droit sur VM → Migrer
→ Sélectionner "Modifier le stockage uniquement"
→ Sélectionner autre datastore (ex: DS_BACKUP_01)
→ Suivant → Terminer
```

**Durée** : 30-60 minutes (VM reste allumée)

**Gain** : 600 Go libérés sur DS_PROD_01

---

**OPTION 3 : Supprimer UN snapshot de la VM prod** (URGENT mais RISQUÉ)

⚠️ **À faire EN DERNIER RECOURS** (si options 1-2 insuffisantes)

**Stratégie** : Supprimer le **snapshot le plus récent** (moins de données à consolider)

**1) Identifier snapshot le plus récent** :
```
Clic droit sur SRV-WEB-PROD → Snapshots → Gestionnaire de snapshots
```

Affiche :
```
Snapshot 1 (vendredi 18h00) - 25 Go
  └─ Snapshot 2 (samedi 10h00) - 40 Go
      └─ Snapshot 3 (samedi 16h00) - 55 Go
          └─ Snapshot 4 (dimanche 12h00) - 80 Go ← PLUS RÉCENT
```

**2) Supprimer snapshot 4** :
```
Sélectionner "Snapshot 4"
→ Bouton "Supprimer"
→ Confirmer
```

⚠️ **ATTENTION** :
- Les modifications du snapshot 4 seront **perdues**
- Si tests importants → **Perte de données de test**
- **Acceptable** car il s'agit de tests (pas de prod)

**Gain immédiat** : ~80 Go

**Temps** : 5-10 minutes (consolidation légère)

---

**OPTION 4 : Étendre le datastore** (si possible matériellement)

**Si SAN permet extension** :

**1) Étendre LUN SAN** (côté stockage SAN) :
- Connexion interface SAN
- Étendre LUN de 2 To → 3 To

**2) Rescanner stockage dans vSphere** :
```
Inventaire → Stockage → DS_PROD_01
→ Actions → Augmenter la capacité
→ Rescanner → Étendre
```

**Gain** : +1 To

**Temps** : 10-15 minutes

---

**PROCÉDURE COMPLÈTE RECOMMANDÉE** :

```
1. Supprimer fichiers .log et .vswp inutiles : +20 Go (5 min)
   ↓
2. Vérifier espace libre : 120 Go (suffisant ?)
   ↓
3. SI INSUFFISANT : Supprimer snapshot 4 : +80 Go (10 min)
   ↓
4. Espace libre : 200 Go
   ↓
5. Redémarrer VM SRV-WEB-PROD :
   - Clic droit → Mettre sous tension
   - Surveiller : Console VM, logs
   ↓
6. Vérifier démarrage :
   - Ping IP VM
   - Accès site web : http://<IP_VM>
   ↓
7. Site e-commerce accessible → URGENCE RÉSOLUE
```

---

**Redémarrage de la VM** :

```
vSphere Client
→ Inventaire → VMs et modèles
→ Clic droit sur SRV-WEB-PROD
→ Mettre sous tension

Surveiller :
- Console : Messages de démarrage
- Tâches récentes : Statut
- Résumé VM : État "Sous tension"
```

**Tests** :
```bash
ping <IP_VM>
curl http://<IP_VM>
```

---

**Barème** :
- Identification fichiers à supprimer : **3 pts**
- Procédure suppression (navigateur datastore) : **3 pts**
- Alternative Storage vMotion ou snapshot : **3 pts**
- Redémarrage VM et vérification : **3 pts**

---

### ✔️ Question 3.3 (8 points) - Consolider snapshots

**Réponse attendue** :

**Consolidation de snapshots** : Fusionner les fichiers delta dans le disque de base.

---

**Procédure de consolidation** :

**AVANT consolidation - État actuel** :
```
Base (VMDK) ← Δ1 ← Δ2 ← Δ3 ← Δ4 (VM écrit ici)
100 Go        25   40   55   80 Go
```

**APRÈS consolidation** :
```
Base (VMDK) fusionné
300 Go (tout intégré)
```

---

**Étapes dans vSphere Client** :

**1) S'assurer que la VM est ÉTEINTE** (recommandé) :
```
Clic droit sur SRV-WEB-PROD → Mettre hors tension
```

⚠️ **IMPORTANT** : Consolidation **possible** sur VM allumée, mais plus risqué.

**Avantages VM éteinte** :
- ✅ Pas de nouvelles écritures disque
- ✅ Consolidation plus rapide
- ✅ Moins de risque de corruption

**Inconvénients** :
- ❌ Site web indisponible pendant consolidation (30-60 min)

**Décision** : Si urgence < interruption → Consolider avec VM allumée (risque acceptable)

---

**2) Supprimer TOUS les snapshots** :

**Méthode 1 : Suppression individuelle** :
```
Clic droit sur VM → Snapshots → Gestionnaire de snapshots
→ Sélectionner snapshot le + ancien (Snapshot 1)
→ Bouton "Supprimer"
→ Option : "Supprimer l'instantané seulement" (pas "Supprimer l'instantané et les enfants")
→ Confirmer
→ Répéter pour Snapshot 2, 3, 4
```

**Méthode 2 : Suppression globale (RECOMMANDÉE)** :
```
Clic droit sur VM → Snapshots → Supprimer tous les instantanés
→ Confirmer
```

---

**3) Lancer consolidation** :

La suppression lance automatiquement la consolidation. Si besoin manuel :

```
Clic droit sur VM → Snapshots → Consolider
```

**vSphere affiche** :
```
Tâche : "Consolider les disques de machine virtuelle"
État : En cours...
Progression : 15% (mise à jour toutes les minutes)
```

---

**4) Surveiller la consolidation** :

**Onglet "Tâches récentes"** :
- Vérifier progression
- **Durée estimée** : 30-90 minutes (dépend de la taille)

**Logs vCenter** :
```
[INFO] Consolidation started for SRV-WEB-PROD
[INFO] Merging delta file 000001.vmdk → base.vmdk
[INFO] Merging delta file 000002.vmdk → base.vmdk
...
[INFO] Consolidation completed successfully
```

**Espace disque datastore** :
- **Augmente temporairement** (fichiers source + destination)
- **Puis diminue** après suppression deltas

---

**5) Vérifier consolidation terminée** :

**a) Navigateur datastore** :
```
Stockage → DS_PROD_01 → Fichiers → SRV-WEB-PROD/
```

**AVANT** :
```
SRV-WEB-PROD.vmdk (100 Go)
SRV-WEB-PROD-000001.vmdk (25 Go)
SRV-WEB-PROD-000002.vmdk (40 Go)
SRV-WEB-PROD-000003.vmdk (55 Go)
SRV-WEB-PROD-000004.vmdk (80 Go)
SRV-WEB-PROD-flat.vmdk (données)
```

**APRÈS** :
```
SRV-WEB-PROD.vmdk (descripteur)
SRV-WEB-PROD-flat.vmdk (300 Go - tout consolidé)
```

✅ **Plus de fichiers -00000X.vmdk** = Consolidation réussie

**b) Propriétés VM** :
```
Modifier les paramètres → Disque dur 1
→ Vérifier : Pas de mention "(consolidation nécessaire)"
```

---

**6) Redémarrer la VM** (si éteinte) :
```
Clic droit → Mettre sous tension
```

---

**Risques et précautions** :

**Risques** :

1. **Panne pendant consolidation** :
   - Coupure électrique, crash ESXi
   - **Risque** : Corruption disque VM
   - **Mitigation** : VM sur cluster HA, UPS

2. **Manque d'espace** :
   - Consolidation nécessite **espace temporaire** (taille finale + deltas)
   - **Risque** : Échec consolidation si datastore plein
   - **Mitigation** : Libérer 30% espace avant consolidation

3. **Durée longue** :
   - 300 Go à consolider = 1-2 heures
   - **Risque** : Indisponibilité prolongée
   - **Mitigation** : Planifier en heures creuses

4. **Corruption données** :
   - Modifications simultanées (VM allumée)
   - **Risque** : Incohérences disque
   - **Mitigation** : Éteindre VM ou mode maintenance

---

**Précautions** :

✅ **AVANT consolidation** :
1. **Sauvegarder la VM** (Veeam, export OVF)
2. **Vérifier espace libre** datastore (minimum 50%)
3. **Planifier fenêtre de maintenance**
4. **Informer utilisateurs** (indisponibilité)
5. **Snapshot du vCenter** (avant opération)

✅ **PENDANT consolidation** :
1. **Ne pas éteindre ESXi**
2. **Ne pas interrompre tâche**
3. **Surveiller logs**
4. **Surveiller espace datastore**

✅ **APRÈS consolidation** :
1. **Vérifier intégrité VM** (démarrage, applications)
2. **Tester site web** (fonctionnalités)
3. **Vérifier logs VM** (erreurs disque)
4. **Documenter** (rapport incident)

---

**Barème** :
- Procédure consolidation (supprimer snapshots) : **4 pts**
- Vérification (navigateur datastore) : **2 pts**
- Risques identifiés : **1 pt**
- Précautions : **1 pt**

---

### ✔️ Question 3.4 (6 points) - Actions préventives

**Réponse attendue** :

**3 actions préventives** pour éviter récurrence :

---

**ACTION 1 : Monitoring et alertes sur les snapshots**

**a) Alertes vCenter sur snapshots anciens** :

**Configuration** :
```
vCenter → Configuration → Alarmes
→ Nouvelle alarme → Machine virtuelle
→ Déclencheurs :
   - "Instantané créé"
   - Durée > 72 heures
→ Actions :
   - Envoyer email à admin@megastore.local
   - Envoyer notification SNMP
   - Enregistrer dans logs
```

**Email envoyé** :
```
ALERTE : Snapshot ancien détecté
VM : SRV-WEB-PROD
Snapshot : Snapshot_test_20251109
Âge : 78 heures (> 72h limite)
Action : Supprimer ou justifier
```

---

**b) Script PowerCLI de monitoring quotidien** :

```powershell
# Script : Check-OldSnapshots.ps1

Connect-VIServer -Server vcenter.megastore.local

$MaxAgeDays = 3
$Snapshots = Get-VM | Get-Snapshot | Where-Object {$_.Created -lt (Get-Date).AddDays(-$MaxAgeDays)}

if ($Snapshots) {
    $Body = "ALERTE : Snapshots de plus de $MaxAgeDays jours détectés :`n`n"
    
    foreach ($Snap in $Snapshots) {
        $Age = ((Get-Date) - $Snap.Created).Days
        $Body += "VM : $($Snap.VM.Name)`n"
        $Body += "Snapshot : $($Snap.Name)`n"
        $Body += "Âge : $Age jours`n"
        $Body += "Taille : $([math]::Round($Snap.SizeGB, 2)) Go`n`n"
    }
    
    Send-MailMessage -To "admin@megastore.local" `
                     -From "vcenter@megastore.local" `
                     -Subject "[ALERTE] Snapshots anciens détectés" `
                     -Body $Body `
                     -SmtpServer "smtp.megastore.local"
}

Disconnect-VIServer -Confirm:$false
```

**Planifier dans Task Scheduler Windows** (serveur vCenter) :
- Quotidien à 8h00
- Utilisateur : Compte service vCenter

---

**c) Dashboard vCenter** :

```
vCenter → Tableau de bord
→ Ajouter widget "Instantanés"
→ Configuration :
   - Afficher VMs avec snapshots
   - Trier par âge décroissant
   - Seuil alerte : 72 heures (orange), 168 heures (rouge)
```

---

**ACTION 2 : Alertes sur saturation datastores**

**a) Alarmes vCenter sur espace disque** :

```
vCenter → Configuration → Alarmes
→ Nouvelle alarme → Datastore
→ Déclencheurs :
   - "Utilisation disque > 80%" (Avertissement)
   - "Utilisation disque > 90%" (Critique)
→ Actions :
   - Email admin
   - Notification sur mobile (vCenter Mobile Watchlist)
```

---

**b) Script monitoring datastores** :

```powershell
# Script : Check-Datastores.ps1

Connect-VIServer -Server vcenter.megastore.local

$ThresholdPercent = 85
$Datastores = Get-Datastore | Where-Object {$_.FreeSpaceGB / $_.CapacityGB * 100 -lt (100 - $ThresholdPercent)}

if ($Datastores) {
    $Body = "ALERTE : Datastores saturés (> ${ThresholdPercent}%) :`n`n"
    
    foreach ($DS in $Datastores) {
        $UsedPercent = [math]::Round((($DS.CapacityGB - $DS.FreeSpaceGB) / $DS.CapacityGB * 100), 2)
        $Body += "Datastore : $($DS.Name)`n"
        $Body += "Capacité : $([math]::Round($DS.CapacityGB, 2)) Go`n"
        $Body += "Libre : $([math]::Round($DS.FreeSpaceGB, 2)) Go`n"
        $Body += "Utilisé : $UsedPercent%`n`n"
    }
    
    Send-MailMessage -To "admin@megastore.local" `
                     -From "vcenter@megastore.local" `
                     -Subject "[ALERTE] Datastores saturés" `
                     -Body $Body `
                     -SmtpServer "smtp.megastore.local"
}

Disconnect-VIServer -Confirm:$false
```

---

**c) Intégration monitoring externe** :

**Centreon / Nagios / Zabbix** :
- Plugin `check_vmware_datastore`
- Seuil warning : 80%
- Seuil critical : 90%
- Vérification : Toutes les 15 minutes

---

**ACTION 3 : Politique de gestion des snapshots**

**a) Procédure formalisée** :

**Document : "Politique Snapshots VMware"**

```markdown
# Politique de gestion des snapshots

## Règles

1. **Durée maximale** :
   - Snapshots dev/test : 72 heures maximum
   - Snapshots prod : 24 heures maximum
   - Snapshots patch : Supprimer après validation (max 48h)

2. **Nombre maximum** :
   - Maximum 2 snapshots par VM
   - Interdit : Chaînes > 3 snapshots

3. **Justification obligatoire** :
   - Nom snapshot : "[Projet]-[Raison]-[Date]"
   - Exemple : "PATCH-Windows-20251112"
   - Description détaillée

4. **Validation** :
   - Snapshot sur VM prod : Accord responsable IT
   - Notification équipe avant création

5. **Suppression automatique** :
   - Script quotidien supprime snapshots > 7 jours
   - Sauf tag "KEEP" (justification requise)

## Procédure création

1. Vérifier espace datastore (> 30% libre)
2. Nommer correctement le snapshot
3. Planifier suppression (calendrier)
4. Documenter (ticket GLPI)

## Procédure suppression

1. Valider que tests terminés
2. Supprimer via vSphere Client
3. Vérifier consolidation terminée
4. Fermer ticket GLPI
```

---

**b) Formation équipe** :

- Session formation "Bonnes pratiques snapshots VMware"
- Documentation interne (wiki)
- Rappels trimestriels

---

**c) Automatisation suppression** :

**Script PowerCLI : Suppression automatique snapshots > 7 jours**

```powershell
# Script : Auto-Delete-OldSnapshots.ps1

Connect-VIServer -Server vcenter.megastore.local

$MaxAgeDays = 7
$Snapshots = Get-VM | Get-Snapshot | Where-Object {
    ($_.Created -lt (Get-Date).AddDays(-$MaxAgeDays)) -and
    ($_.Name -notlike "*KEEP*")  # Exception si tag KEEP
}

foreach ($Snap in $Snapshots) {
    $Age = ((Get-Date) - $Snap.Created).Days
    Write-Host "Suppression : $($Snap.VM.Name) - $($Snap.Name) - Âge : $Age jours" -ForegroundColor Yellow
    
    # Supprimer snapshot
    Remove-Snapshot -Snapshot $Snap -Confirm:$false
    
    # Log
    $LogMessage = "$(Get-Date) - Snapshot supprimé : VM=$($Snap.VM.Name), Snapshot=$($Snap.Name), Âge=$Age jours"
    Add-Content -Path "C:\Logs\Snapshots_Deletion.log" -Value $LogMessage
}

Disconnect-VIServer -Confirm:$false
```

**Planifier** : Tous les jours à 3h00 (après sauvegardes)

---

**Barème** :
- Action 1 (monitoring snapshots) : **2 pts**
- Action 2 (alertes datastores) : **2 pts**
- Action 3 (politique/automatisation) : **2 pts**

---

### ✔️ Question 3.5 (8 points) - Architecture haute disponibilité

**Réponse attendue** :

**Architecture HA pour serveur web critique** :

---

**COMPOSANTS** :

**1) Cluster vSphere avec DRS + HA** :

- **3 hôtes ESXi** minimum
- **vSphere HA** : Redémarrage automatique si panne hôte
- **vSphere DRS** : Équilibrage charge automatique

---

**2) Datastores redondants** :

- **Datastore primaire** : DS_PROD_01 (SAN principal)
- **Datastore secondaire** : DS_PROD_02 (SAN distant ou NFS)
- **Réplication** : Asynchrone entre datastores

---

**3) VM répliquée** :

- **VM principale** : SRV-WEB-PROD (sur DS_PROD_01)
- **VM réplique** : SRV-WEB-PROD-REPLICA (sur DS_PROD_02)
- **Synchronisation** : Veeam Replication ou vSphere Replication

---

**SCHÉMA ARCHITECTURE** :

```
                    INTERNET
                       │
                       ▼
                 LOAD BALANCER
              (HAProxy / F5 / Nginx)
              IP Virtuelle : 203.0.113.100
                       │
        ┌──────────────┼──────────────┐
        │              │              │
        ▼              ▼              ▼
    ESXi-01        ESXi-02        ESXi-03
    (Site A)       (Site A)       (Site B distant)
        │              │              │
        ├──────────────┤              │
        │              │              │
        ▼              ▼              ▼
   DS_PROD_01    DS_PROD_01    DS_PROD_02
   (SAN iSCSI)   (SAN iSCSI)   (SAN distant)
        │              │              │
        ▼              │              ▼
 SRV-WEB-PROD          │       SRV-WEB-PROD-REPLICA
 (VM Primaire)         │       (VM Réplique)
   192.168.100.10      │       192.168.100.11
                       │
                       │ Réplication
                       │ (Veeam / vSphere Replication)
                       ▼
```

---

**CONFIGURATION** :

**A) Cluster vSphere** :

```
vCenter → Nouveau cluster → "CLUSTER-PROD-WEB"
→ Activer vSphere DRS :
   - Automation : Entièrement automatisé
   - Migration Threshold : Aggressif
→ Activer vSphere HA :
   - Échec de l'hôte : Redémarrer les VMs
   - Priorité SRV-WEB-PROD : Haute
   - Contrôle d'admission : Réserver ressources pour 1 panne hôte
→ Ajouter hôtes : ESXi-01, ESXi-02, ESXi-03
```

---

**B) DRS - Équilibrage automatique** :

**Règles DRS** :

```
1) Règle anti-affinité VM-VM :
   - VMs : SRV-WEB-PROD, SRV-WEB-PROD-REPLICA
   - Type : Séparer les machines virtuelles
   - Objectif : Jamais sur le même hôte ESXi

2) Règle affinité VM-Datastore :
   - VM : SRV-WEB-PROD → DS_PROD_01
   - VM : SRV-WEB-PROD-REPLICA → DS_PROD_02
```

**Fonctionnement DRS** :
- **Surveillance continue** : Utilisation CPU/RAM des hôtes
- **vMotion automatique** : Si déséquilibre détecté
- **Exemple** :
  - ESXi-01 : 90% CPU (SRV-WEB-PROD + autres VMs)
  - ESXi-02 : 30% CPU
  - **DRS déclenche vMotion** : Déplace SRV-WEB-PROD vers ESXi-02
  - **Temps** : 5-10 secondes, **aucune interruption**

---

**C) HA - Haute disponibilité** :

**Configuration HA** :

```
Cluster → vSphere HA → Modifier
→ Échecs et réponses :
   - Surveillance VM : Activée
   - Surveillance application (VMware Tools) : Activée
→ Contrôle d'admission :
   - Politique : Réserver ressources pour N pannes d'hôtes
   - Tolérance : 1 hôte (sur 3)
→ Priorité redémarrage VM :
   - SRV-WEB-PROD : Haute (redémarre en premier)
   - SRV-WEB-PROD-REPLICA : Moyenne
```

**Fonctionnement HA** :

**Scénario 1 : Panne matérielle ESXi-01**
```
1. ESXi-01 tombe en panne (CPU, RAM, carte mère)
   ↓
2. vSphere HA détecte : Heartbeat réseau + datastore manquants
   ↓
3. HA déclenche redémarrage VMs sur ESXi-02 ou ESXi-03
   ↓
4. SRV-WEB-PROD redémarre sur ESXi-02 (priorité haute)
   ↓
5. Temps de reprise : 2-5 minutes (boot OS)
   ↓
6. Load Balancer détecte VM disponible → Redirige trafic
```

---

**D) Réplication vers datastore distant** :

**Option 1 : vSphere Replication** :

```
vCenter → SRV-WEB-PROD → Configurer la réplication
→ Datastore cible : DS_PROD_02
→ RPO : 15 minutes (réplication toutes les 15 min)
→ Rétention : 24 points de restauration
→ Réseau : Réseau dédié vSphere Replication (10 Gbps)
```

**Fonctionnement** :
- **Réplication asynchrone** : Toutes les 15 min
- **Deltas uniquement** : Blocs modifiés (économie bande passante)
- **Failover manuel** : En cas de panne DS_PROD_01
  - Promouvoir SRV-WEB-PROD-REPLICA en VM principale
  - Temps : 5-10 minutes

---

**Option 2 : Veeam Replication** :

```
Veeam Backup & Replication Console
→ Replication Job → Nouveau
→ VM source : SRV-WEB-PROD
→ Destination : ESXi-03, Datastore DS_PROD_02
→ Réplication : Continue (CDP - Continuous Data Protection)
→ RPO : 1 minute
→ Réseau : Réseau réplication dédié
```

**Avantages Veeam** :
- RPO très court (< 1 min)
- Failover automatisé
- Orchestration (scripts pre/post failover)

---

**E) Load Balancer** :

**HAProxy (gratuit) ou F5 BIG-IP (commercial)**

**Configuration HAProxy** :

```haproxy
frontend web_frontend
    bind 203.0.113.100:80
    bind 203.0.113.100:443 ssl crt /etc/ssl/megastore.pem
    default_backend web_backend

backend web_backend
    balance roundrobin
    option httpchk GET /health
    
    server web_prod 192.168.100.10:80 check inter 5s rise 2 fall 3
    server web_replica 192.168.100.11:80 check inter 5s rise 2 fall 3 backup
```

**Explications** :
- **IP Virtuelle** : 203.0.113.100 (DNS public pointe ici)
- **Health Check** : Requête `/health` toutes les 5 secondes
- **Primaire** : `web_prod` (192.168.100.10)
- **Backup** : `web_replica` (192.168.100.11) - Utilisée si primaire HS
- **Bascule automatique** : Si health check échoue 3 fois

---

**FONCTIONNEMENT GLOBAL** :

**Scénario 1 : Utilisation normale**
```
Client Internet → Load Balancer (203.0.113.100)
                       ↓
                 SRV-WEB-PROD (192.168.100.10) sur ESXi-01
                 (SRV-WEB-PROD-REPLICA en standby sur ESXi-03)
```

---

**Scénario 2 : Surcharge ESXi-01**
```
1. ESXi-01 : 85% CPU (autres VMs + SRV-WEB-PROD)
   ↓
2. DRS détecte déséquilibre
   ↓
3. DRS déclenche vMotion : SRV-WEB-PROD → ESXi-02
   ↓
4. Migration en 10 secondes, aucune interruption
   ↓
5. Load Balancer : Aucun changement (même IP VM)
```

---

**Scénario 3 : Panne ESXi-01**
```
1. ESXi-01 tombe en panne (matériel)
   ↓
2. vSphere HA détecte panne (30 secondes)
   ↓
3. HA redémarre SRV-WEB-PROD sur ESXi-02
   ↓
4. Temps boot : 3 minutes
   ↓
5. VM disponible (192.168.100.10)
   ↓
6. Load Balancer health check : OK → Trafic rétabli
   ↓
Interruption totale : 3-4 minutes (acceptable)
```

---

**Scénario 4 : Panne datastore DS_PROD_01**
```
1. DS_PROD_01 inaccessible (panne SAN)
   ↓
2. SRV-WEB-PROD ne peut pas démarrer (disque inaccessible)
   ↓
3. HA tente redémarrage → ÉCHEC (datastore HS)
   ↓
4. Administrateur déclenche FAILOVER MANUEL :
   - vSphere Replication : Promouvoir SRV-WEB-PROD-REPLICA
   - Ou Veeam : Failover automatique
   ↓
5. SRV-WEB-PROD-REPLICA devient primaire (192.168.100.11)
   ↓
6. Load Balancer bascule sur replica
   ↓
7. Service rétabli (RPO = 15 min max de perte données)
```

---

**RÉSUMÉ AVANTAGES** :

| Composant | Fonction | Bénéfice |
|-----------|----------|----------|
| **DRS** | Équilibrage automatique | Performances optimales en continu |
| **HA** | Redémarrage automatique | Reprise en 2-5 min si panne hôte |
| **Réplication** | Copie VM sur datastore distant | Protection panne stockage |
| **Load Balancer** | Répartition trafic | Bascule automatique si VM HS |
| **3 hôtes** | Tolérance pannes | Continue avec 2/3 hôtes actifs |

**RTO (Recovery Time Objective)** : **3-5 minutes** (panne hôte)  
**RPO (Recovery Point Objective)** : **15 minutes** (perte données max)  
**Disponibilité** : **99.95%** (environ 4h indispo/an)

---

**Barème** :
- Schéma architecture : **2 pts**
- Configuration DRS : **2 pts**
- Configuration HA : **2 pts**
- Réplication + Load Balancer : **2 pts**

---

---

# PARTIE 2 : QUESTIONNAIRE TECHNIQUE

---

## 🐧 SECTION A : LINUX (20 points)

### ✔️ Question A1 (4 points) - Commande tar

**a) Créer archive compressée gzip** (2 pts) :

```bash
tar -czf /backups/web_backup.tar.gz /var/www/html/
```

**Explications** :
- `-c` : Create (créer archive)
- `-z` : gZip (compresser avec gzip)
- `-f` : File (nom du fichier)
- `/backups/web_backup.tar.gz` : Chemin destination
- `/var/www/html/` : Dossier source

**Variante avec verbosité** :
```bash
tar -czvf /backups/web_backup.tar.gz /var/www/html/
```
(`-v` : Verbose, affiche les fichiers archivés)

---

**b) Extraire l'archive dans /tmp/restore/** (2 pts) :

```bash
tar -xzf /backups/web_backup.tar.gz -C /tmp/restore/
```

**Explications** :
- `-x` : eXtract (extraire)
- `-z` : gZip (décompresser)
- `-f` : File
- `-C` : Change directory (extraire dans ce dossier)

**Créer le dossier avant** (si n'existe pas) :
```bash
mkdir -p /tmp/restore/
tar -xzf /backups/web_backup.tar.gz -C /tmp/restore/
```

**Lister contenu sans extraire** :
```bash
tar -tzf /backups/web_backup.tar.gz
```

---

**Barème** :
- Création archive : **2 pts**
- Extraction archive : **2 pts**

---

### ✔️ Question A2 (4 points) - Cron et planification

**a) Fréquence d'exécution** (2 pts) :

```
30 2 * * 1-5 /usr/local/bin/maintenance.sh
```

**Lecture** :
- `30` : À la 30ème minute
- `2` : À 2 heures du matin
- `*` : Tous les jours du mois
- `*` : Tous les mois
- `1-5` : Du lundi (1) au vendredi (5)

**Réponse** : **Du lundi au vendredi à 2h30 du matin** (jours ouvrés uniquement, pas le week-end)

---

**b) Modification pour tous les jours à minuit** (2 pts) :

```
0 0 * * * /usr/local/bin/maintenance.sh
```

**Explications** :
- `0` : Minute 0 (minuit pile)
- `0` : Heure 0 (minuit)
- `*` : Tous les jours
- `*` : Tous les mois
- `*` : Tous les jours de la semaine (dimanche à samedi)

**Alternative** (syntaxe spéciale) :
```
@daily /usr/local/bin/maintenance.sh
```
Équivalent à `0 0 * * *`

---

**Barème** :
- Fréquence correcte : **2 pts**
- Modification minuit : **2 pts**

---

### ✔️ Question A3 (4 points) - Gestion des processus

**a) Identifier processus gourmand** (2 pts) :

**Commande 1 : top** (temps réel)
```bash
top
```
- Trier par CPU : Appuyer sur `P`
- Identifier PID, nom, %CPU
- Quitter : `q`

**Commande 2 : htop** (plus visuel)
```bash
htop
```
- Interface couleur
- F6 : Trier par CPU
- F9 : Kill processus

**Commande 3 : ps** (instantané)
```bash
ps aux --sort=-%cpu | head -10
```
- Affiche top 10 processus par CPU

**Sortie exemple** :
```
PID  USER  %CPU  %MEM  COMMAND
12345 www  90.2  15.3  /usr/bin/apache2
```

---

**b) Arrêter proprement le processus** (2 pts) :

**Méthode 1 : kill (signal TERM)** :
```bash
kill 12345
```
- Envoie signal **SIGTERM** (15) : Arrêt propre
- Le processus peut nettoyer (fermer fichiers, sauvegarder)

**Méthode 2 : killall (par nom)** :
```bash
killall apache2
```
- Tue tous les processus nommés `apache2`

**Méthode 3 : pkill (regex)** :
```bash
pkill -f "apache2"
```
- Tue processus correspondant au motif

---

**Si processus résiste** (kill forcé) :
```bash
kill -9 12345
```
- Signal **SIGKILL** (9) : Tue immédiatement
- ⚠️ Pas de nettoyage, **risque de corruption**

**Hiérarchie signaux** :
```bash
kill -15 12345  # SIGTERM (poli, recommandé)
sleep 5
kill -9 12345   # SIGKILL (brutal, si nécessaire)
```

---

**Barème** :
- Commande identification (top/ps) : **2 pts**
- 2 méthodes arrêt (kill, killall) : **2 pts**

---

### ✔️ Question A4 (4 points) - Montage NFS

**a) Ligne /etc/fstab** (3 pts) :

```
192.168.10.50:/export/data  /mnt/nfs_data  nfs  defaults,_netdev  0  0
```

**Explications** :

```
<serveur>:<partage>  <point_montage>  <type>  <options>  <dump>  <pass>
```

- **192.168.10.50:/export/data** : Serveur NFS + chemin partagé
- **/mnt/nfs_data** : Point de montage local
- **nfs** : Type de système de fichiers
- **defaults** : Options par défaut (rw, suid, dev, exec, auto, nouser, async)
- **_netdev** : Attend disponibilité réseau avant montage (important !)
- **0** : Dump (sauvegarde) : 0 = pas de backup automatique
- **0** : Pass (fsck) : 0 = pas de vérification au boot

**Options NFS recommandées** :
```
192.168.10.50:/export/data  /mnt/nfs_data  nfs  defaults,_netdev,rsize=8192,wsize=8192,timeo=14  0  0
```

- **rsize/wsize** : Taille buffer lecture/écriture (performances)
- **timeo** : Timeout avant retry

---

**b) Tester montage sans redémarrer** (1 pt) :

**Étapes** :

1. **Créer point de montage** (si n'existe pas) :
```bash
sudo mkdir -p /mnt/nfs_data
```

2. **Monter** :
```bash
sudo mount -a
```
Monte tous les systèmes dans `/etc/fstab` qui ne sont pas encore montés

**OU (montage spécifique)** :
```bash
sudo mount 192.168.10.50:/export/data /mnt/nfs_data
```

3. **Vérifier** :
```bash
df -h | grep nfs_data
```

Sortie attendue :
```
192.168.10.50:/export/data  100G  50G  50G  50% /mnt/nfs_data
```

4. **Tester accès** :
```bash
ls -la /mnt/nfs_data
touch /mnt/nfs_data/test.txt
```

---

**Démonter** (si besoin) :
```bash
sudo umount /mnt/nfs_data
```

---

**Barème** :
- Ligne fstab correcte : **3 pts**
- Commande test (mount -a) : **1 pt**

---

### ✔️ Question A5 (4 points) - Gestion des logs

**a) Emplacement logs système** (1 pt) :

**Debian 12** :
```
/var/log/syslog
```

**Autres logs importants** :
- `/var/log/auth.log` : Authentification (SSH, sudo)
- `/var/log/kern.log` : Kernel (noyau)
- `/var/log/daemon.log` : Services/daemons
- `/var/log/apache2/` : Logs Apache
- `/var/log/nginx/` : Logs Nginx

**Journald (systemd)** :
```bash
journalctl
```
Logs centralisés systemd (depuis Debian 8)

---

**b) Afficher 20 dernières lignes en temps réel** (2 pts) :

```bash
tail -f -n 20 /var/log/syslog
```

**Explications** :
- `tail` : Affiche fin de fichier
- `-f` : Follow (suit les nouvelles lignes en temps réel)
- `-n 20` : 20 dernières lignes

**Variantes** :

**Sans spécifier nombre** (10 par défaut) :
```bash
tail -f /var/log/syslog
```

**Avec journalctl** :
```bash
journalctl -f -n 20
```

**Filtrer par service** :
```bash
journalctl -u apache2 -f -n 20
```

---

**c) Rechercher "error" (insensible casse)** (1 pt) :

```bash
grep -i "error" /var/log/syslog
```

**Explications** :
- `-i` : Insensible à la casse (ignore case)
- Trouve : "error", "Error", "ERROR", "ErRoR"

**Variantes** :

**Avec numéros de ligne** :
```bash
grep -in "error" /var/log/syslog
```

**Dernières occurrences** :
```bash
grep -i "error" /var/log/syslog | tail -20
```

**Plusieurs termes** :
```bash
grep -iE "error|warning|critical" /var/log/syslog
```

**Avec contexte** (lignes avant/après) :
```bash
grep -i -C 3 "error" /var/log/syslog
```
(`-C 3` : 3 lignes de contexte avant et après)

**Compter occurrences** :
```bash
grep -ic "error" /var/log/syslog
```

---

**Barème** :
- Emplacement logs : **1 pt**
- tail -f : **2 pts**
- grep -i : **1 pt**

---

---

## 🌐 SECTION B : RÉSEAU (25 points)

### ✔️ Question B1 (6 points) - Subnetting CIDR

**Réseau** : `10.20.0.0/22`

**Objectif** : Diviser en **4 sous-réseaux égaux**

---

**a) Nouveau préfixe CIDR** (2 pts) :

**Calcul** :
- Réseau initial : `/22` = 22 bits réseau
- Diviser en 4 = 2^2 = **2 bits supplémentaires**
- Nouveau préfixe : 22 + 2 = **/24**

**Réponse** : `/24`

---

**b) 4 réseaux avec masques** (4 pts) :

**Réseau 1** :
- Réseau : `10.20.0.0/24`
- Masque : `255.255.255.0`
- Plage : `10.20.0.1` à `10.20.0.254`
- Broadcast : `10.20.0.255`
- Hôtes : 254

**Réseau 2** :
- Réseau : `10.20.1.0/24`
- Masque : `255.255.255.0`
- Plage : `10.20.1.1` à `10.20.1.254`
- Broadcast : `10.20.1.255`
- Hôtes : 254

**Réseau 3** :
- Réseau : `10.20.2.0/24`
- Masque : `255.255.255.0`
- Plage : `10.20.2.1` à `10.20.2.254`
- Broadcast : `10.20.2.255`
- Hôtes : 254

**Réseau 4** :
- Réseau : `10.20.3.0/24`
- Masque : `255.255.255.0`
- Plage : `10.20.3.1` à `10.20.3.254`
- Broadcast : `10.20.3.255`
- Hôtes : 254

---

**Vérification** :
- Réseau original : `10.20.0.0/22` = 10.20.0.0 à 10.20.3.255 (1024 IP)
- 4 sous-réseaux /24 : 4 × 256 = 1024 IP ✅
- **Pas de chevauchement** ✅

---

**Barème** :
- Préfixe /24 : **2 pts**
- 4 réseaux corrects : **4 pts** (1 pt par réseau)

---

### ✔️ Question B2 (5 points) - ACL Cisco étendue

**Réponse attendue** :

**Configuration ACL étendue** :

```cisco
!  Connexion au routeur/switch
enable
configure terminal

! Création ACL étendue nommée
ip access-list extended ACL_WEB_SERVER

 ! Autoriser HTTP (port 80)
 permit tcp any host 192.168.100.50 eq 80
 
 ! Autoriser HTTPS (port 443)
 permit tcp any host 192.168.100.50 eq 443
 
 ! Bloquer tout le reste (implicite, mais on peut l'écrire)
 deny ip any any log

! Sortir du mode ACL
exit

! Application sur interface (exemple : interface entrante)
interface GigabitEthernet0/1
 ip access-group ACL_WEB_SERVER in

! Sauvegarde
end
copy running-config startup-config
```

---

**Explications** :

**Ligne 1** : `permit tcp any host 192.168.100.50 eq 80`
- **permit** : Autoriser
- **tcp** : Protocole TCP
- **any** : Depuis n'importe quelle source
- **host 192.168.100.50** : Vers le serveur web (destination)
- **eq 80** : Port 80 (HTTP)

**Ligne 2** : `permit tcp any host 192.168.100.50 eq 443`
- Même principe pour HTTPS (port 443)

**Ligne 3** : `deny ip any any log`
- **deny** : Bloquer
- **ip** : Tout protocole IP
- **any any** : De partout vers partout
- **log** : Enregistrer dans logs (détection tentatives)

---

**Vérification** :

```cisco
show access-lists ACL_WEB_SERVER
```

**Sortie** :
```
Extended IP access list ACL_WEB_SERVER
    10 permit tcp any host 192.168.100.50 eq www (5 matches)
    20 permit tcp any host 192.168.100.50 eq 443 (3 matches)
    30 deny ip any any log
```

---

**Variante avec plage source** :

Si on veut autoriser seulement depuis réseau interne `192.168.0.0/16` :

```cisco
ip access-list extended ACL_WEB_SERVER
 permit tcp 192.168.0.0 0.0.255.255 host 192.168.100.50 eq 80
 permit tcp 192.168.0.0 0.0.255.255 host 192.168.100.50 eq 443
 deny ip any any log
```

---

**Barème** :
- ACL étendue nommée : **1 pt**
- Permit TCP 80 : **2 pts**
- Permit TCP 443 : **1 pt**
- Application sur interface : **1 pt**

---

**FIN CORRECTIONS PARTIE 2 - Suite dans PARTIE 3...**


---

# ✅ EXAMEN BLANC 2 - CORRECTIONS DÉTAILLÉES
## Partie 3 : Questionnaire Technique - Section B (fin)

---

## 🌐 SECTION B : RÉSEAU (suite et fin - 14 points)

### ✔️ Question B3 (5 points) - Routage inter-VLAN

**a) Créer interfaces SVI des 3 VLANs** (4 pts) :

```cisco
! Connexion au switch L3
enable
configure terminal

! Créer VLAN 10 (s'ils n'existent pas)
vlan 10
 name Gestion
exit

vlan 20
 name Commercial
exit

vlan 30
 name Comptabilite
exit

! Créer interface SVI VLAN 10 (passerelle)
interface vlan 10
 description "Gateway VLAN 10 - Gestion"
 ip address 192.168.10.1 255.255.255.0
 no shutdown
exit

! Créer interface SVI VLAN 20
interface vlan 20
 description "Gateway VLAN 20 - Commercial"
 ip address 192.168.20.1 255.255.255.0
 no shutdown
exit

! Créer interface SVI VLAN 30
interface vlan 30
 description "Gateway VLAN 30 - Comptabilite"
 ip address 192.168.30.1 255.255.255.0
 no shutdown
exit

! Sauvegarder
end
copy running-config startup-config
```

---

**Explications** :

**SVI (Switch Virtual Interface)** :
- Interface **virtuelle** (pas physique)
- Représente le **VLAN au niveau IP**
- Sert de **passerelle** pour les PC du VLAN
- Permet le **routage inter-VLAN**

**Configuration** :
```cisco
interface vlan 10
 ip address 192.168.10.1 255.255.255.0
```
- IP `.1` = Convention passerelle par défaut
- Masque `/24` (255.255.255.0)
- `no shutdown` : Activer l'interface

---

**Vérification** :

```cisco
show ip interface brief | include Vlan
```

**Sortie** :
```
Vlan10    192.168.10.1    YES manual up    up
Vlan20    192.168.20.1    YES manual up    up
Vlan30    192.168.30.1    YES manual up    up
```

```cisco
show vlan brief
```

**Sortie** :
```
VLAN Name         Status    Ports
---- ------------ --------- --------------------------
10   Gestion      active    Fa0/1, Fa0/2
20   Commercial   active    Fa0/3, Fa0/4, Fa0/5
30   Comptabilite active    Fa0/6, Fa0/7
```

---

**b) Activer le routage IP** (1 pt) :

```cisco
! Mode configuration
configure terminal

! Activer le routage IP
ip routing

! Sortir et sauvegarder
end
copy running-config startup-config
```

---

**Explications** :

**`ip routing`** :
- Commande **globale** (pas dans une interface)
- **Active la fonction de routeur** du switch L3
- Sans cette commande : Switch fait uniquement du **switching** (pas de routage entre VLANs)
- Avec cette commande : Switch devient **routeur inter-VLAN**

---

**Vérification routage activé** :

```cisco
show ip route
```

**Sortie** :
```
Codes: C - connected, S - static, ...

Gateway of last resort is not set

C    192.168.10.0/24 is directly connected, Vlan10
C    192.168.20.0/24 is directly connected, Vlan20
C    192.168.30.0/24 is directly connected, Vlan30
```

**Routes connectées** (`C`) : Les 3 réseaux sont automatiquement ajoutés à la table de routage.

---

**Test connectivité inter-VLAN** :

**Depuis un PC du VLAN 10** (192.168.10.100) :
```cmd
ping 192.168.20.50
ping 192.168.30.80
```

**Résultat attendu** : ✅ Réponses (les VLANs communiquent)

---

**Configuration ports d'accès** (rappel) :

**Affecter ports aux VLANs** :
```cisco
! Ports VLAN 10 (Gestion)
interface range FastEthernet0/1 - 2
 switchport mode access
 switchport access vlan 10
 spanning-tree portfast
exit

! Ports VLAN 20 (Commercial)
interface range FastEthernet0/3 - 5
 switchport mode access
 switchport access vlan 20
 spanning-tree portfast
exit

! Ports VLAN 30 (Comptabilité)
interface range FastEthernet0/6 - 7
 switchport mode access
 switchport access vlan 30
 spanning-tree portfast
exit
```

---

**Barème** :
- 3 interfaces SVI avec IP correctes : **4 pts** (1 pt si 1 seule, 2-3 pts si 2-3 interfaces)
- Commande ip routing : **1 pt**

---

### ✔️ Question B4 (5 points) - Diagnostic réseau

**a) 3 commandes de diagnostic** (3 pts) :

---

**Commande 1 : ping**

**Windows** :
```cmd
ping 10.50.1.100
```

**Linux** :
```bash
ping -c 4 10.50.1.100
```

**Utilité** :
- Teste la **connectivité réseau** (couche 3 - IP)
- Envoie paquets **ICMP Echo Request**
- Attend **ICMP Echo Reply**
- Indique : **RTT** (temps aller-retour), **perte de paquets**

**Interprétation** :
- ✅ **Réponse** : Serveur accessible, réseau OK
- ❌ **Timeout** : Serveur injoignable (panne, firewall bloque ICMP, route manquante)
- ❌ **Destination host unreachable** : Pas de route vers destination

---

**Commande 2 : tracert (Windows) / traceroute (Linux)**

**Windows** :
```cmd
tracert 10.50.1.100
```

**Linux** :
```bash
traceroute 10.50.1.100
```

**Utilité** :
- Affiche le **chemin complet** (saut par saut) vers la destination
- Identifie **où le trafic est bloqué** (quel routeur)
- Utilise **TTL** (Time To Live) incrémental

**Sortie exemple** :
```
1    <1 ms    192.168.10.1     (passerelle locale)
2     5 ms    10.0.0.1         (routeur WAN)
3    15 ms    10.50.0.1        (routeur distant)
4     *       *       *        (timeout - problème ici)
```

**Interprétation** :
- Hop 4 = **Timeout** → Problème au **4ème routeur**
- Permet de localiser **précisément** la panne

---

**Commande 3 : nslookup (résolution DNS)**

**Windows et Linux** :
```cmd
nslookup serveur.example.com
```

**Utilité** :
- Teste la **résolution de noms DNS**
- Vérifie si le **serveur DNS répond**
- Affiche **IP correspondante** au nom

**Sortie exemple** :
```
Serveur :   dns.megastore.local
Adresse :   192.168.10.10

Nom :       serveur.example.com
Adresse :   10.50.1.100
```

**Interprétation** :
- ✅ **IP affichée** : DNS fonctionne
- ❌ **Erreur "serveur introuvable"** : DNS inaccessible ou mal configuré
- ❌ **Erreur "nom introuvable"** : Enregistrement DNS manquant

---

**Autres commandes utiles** :

**ipconfig /all** (Windows) :
```cmd
ipconfig /all
```
Affiche config réseau complète (IP, masque, passerelle, DNS, MAC)

**ip addr** (Linux) :
```bash
ip addr show
```
Affiche interfaces réseau et adresses IP

**netstat -rn** (table de routage) :
```cmd
netstat -rn
```
Affiche routes configurées

**telnet** (test port TCP) :
```cmd
telnet 10.50.1.100 80
```
Teste si port TCP 80 est ouvert sur le serveur

---

**Barème** :
- Commande 1 (ping) avec utilité : **1 pt**
- Commande 2 (tracert) avec utilité : **1 pt**
- Commande 3 (nslookup ou autre) avec utilité : **1 pt**

---

**b) Différence tracert vs pathping** (2 pts) :

**tracert (Trace Route)** :

```cmd
tracert 10.50.1.100
```

**Fonctionnement** :
- Envoie **3 paquets ICMP** par saut (TTL incrémental)
- Affiche **RTT instantané** pour chaque saut
- **Rapide** : Quelques secondes

**Sortie** :
```
1    <1 ms   <1 ms   <1 ms   192.168.10.1
2     5 ms    4 ms    6 ms   10.0.0.1
3    15 ms   16 ms   14 ms   10.50.0.1
4    20 ms   22 ms   21 ms   10.50.1.100
```

**Utilité** :
- Identifier **chemin réseau**
- Détecter **où se situe la panne** (quel saut timeout)

---

**pathping (Path Ping - Windows uniquement)**

```cmd
pathping 10.50.1.100
```

**Fonctionnement** :
- **Combine tracert + ping**
- Envoie **100 paquets** par saut (sur plusieurs minutes)
- Calcule **statistiques détaillées** : Perte de paquets, latence moyenne

**Sortie** :
```
Tracé de l'itinéraire vers 10.50.1.100 sur 4 sauts maximum :
  0  PC-LOCAL [192.168.10.100]
  1  192.168.10.1
  2  10.0.0.1
  3  10.50.0.1
  4  10.50.1.100

Calcul des statistiques pendant 300 secondes...

            Source vers Ici      Ce Nœud/Lien
Saut  RTT    Perdu/Envoyé = Pct  Perdu/Envoyé = Pct  Adresse
  0                                                   PC-LOCAL
                                0/ 100 =  0%   |
  1    1ms     0/ 100 =  0%     0/ 100 =  0%   192.168.10.1
                                0/ 100 =  0%   |
  2    5ms     0/ 100 =  0%     0/ 100 =  0%   10.0.0.1
                                5/ 100 =  5%   | ← Perte ici !
  3   18ms     5/ 100 =  5%     0/ 100 =  0%   10.50.0.1
                                0/ 100 =  0%   |
  4   22ms     5/ 100 =  5%     0/ 100 =  0%   10.50.1.100
```

**Utilité** :
- Détecter **perte de paquets intermittente** (problème de qualité ligne)
- Identifier **lien réseau dégradé**
- **Plus précis** que tracert (mais plus long : 5-10 minutes)

---

**Tableau comparatif** :

| Critère | tracert | pathping |
|---------|---------|----------|
| **Durée** | 5-10 secondes | 5-10 minutes |
| **Paquets par saut** | 3 | 100 |
| **Statistiques** | RTT instantané | Perte paquets + RTT moyen |
| **Usage** | Diagnostic rapide | Analyse qualité lien |
| **OS** | Windows, Linux, Mac | Windows uniquement |

---

**Barème** :
- tracert expliqué : **1 pt**
- pathping expliqué : **1 pt**

---

### ✔️ Question B5 (4 points) - NAT et PAT

**a) Différence NAT vs PAT** (2 pts) :

---

**NAT (Network Address Translation)** :

**Définition** :
- Traduction **1 IP privée ↔ 1 IP publique**
- Mapping **statique** ou **dynamique**

**Type 1 : NAT statique (1:1)** :
```
192.168.10.10 (privé) ↔ 203.0.113.10 (public) [permanent]
192.168.10.20 (privé) ↔ 203.0.113.11 (public) [permanent]
```
- **1 IP privée** → **1 IP publique dédiée**
- Utilisé pour serveurs accessibles depuis Internet

**Type 2 : NAT dynamique (pool)** :
```
Réseau privé : 192.168.10.0/24 (254 hôtes)
Pool public : 203.0.113.10 - 203.0.113.30 (20 IPs)
```
- **IP publique assignée dynamiquement** depuis un pool
- Limite : **20 utilisateurs** simultanés maximum

---

**PAT (Port Address Translation) - aussi appelé NAT overload** :

**Définition** :
- **Plusieurs IP privées ↔ 1 seule IP publique**
- Utilise les **numéros de ports** pour différencier les connexions

**Fonctionnement** :
```
PC1 (192.168.10.10:52000) → Routeur (203.0.113.1:10001) → Internet
PC2 (192.168.10.20:52000) → Routeur (203.0.113.1:10002) → Internet
PC3 (192.168.10.30:54321) → Routeur (203.0.113.1:10003) → Internet
```

**Table PAT** :
| IP Privée | Port Privé | IP Publique | Port Public |
|-----------|-----------|-------------|-------------|
| 192.168.10.10 | 52000 | 203.0.113.1 | 10001 |
| 192.168.10.20 | 52000 | 203.0.113.1 | 10002 |
| 192.168.10.30 | 54321 | 203.0.113.1 | 10003 |

**Avantages** :
- ✅ **Économie d'IP publiques** : 1 seule IP pour 1000+ utilisateurs
- ✅ **Solution la plus courante** (box Internet, routeurs entreprise)

---

**Tableau comparatif** :

| Critère | NAT (classique) | PAT (NAT overload) |
|---------|-----------------|---------------------|
| **Mapping** | 1 privée → 1 publique | Plusieurs privées → 1 publique |
| **Identifiant unique** | IP | IP + Port |
| **IP publiques nécessaires** | Autant que d'utilisateurs | 1 seule |
| **Coût** | Élevé (1 IP/utilisateur) | Faible (1 IP pour tous) |
| **Usage** | Serveurs accessibles | Accès Internet entreprise |

---

**Barème** :
- NAT défini : **1 pt**
- PAT défini : **1 pt**

---

**b) Exemple PAT en entreprise** (2 pts) :

**Cas d'usage typique** :

**Entreprise MegaStore** :
- **85 employés** répartis sur 4 sites
- **Réseau interne privé** : 192.168.0.0/16
- **1 seule IP publique Internet** : 203.0.113.50

---

**Architecture** :

```
┌─────────────────────────────────────┐
│   RÉSEAU INTERNE (Privé)            │
│                                     │
│  PC1 : 192.168.10.10                │
│  PC2 : 192.168.10.20                │
│  PC3 : 192.168.20.30                │
│  ... (85 postes au total)           │
│                                     │
└───────────┬─────────────────────────┘
            │
            ▼
    ┌───────────────┐
    │  ROUTEUR PAT  │
    │  (NAT Overload)│
    └───────┬───────┘
            │
            │ IP Publique : 203.0.113.50
            │
            ▼
        INTERNET
```

---

**Scénario : 3 employés accèdent à un site web** :

**Requête sortante (vers Internet)** :

| Utilisateur | IP Privée | Port Privé | → | IP Publique | Port NAT | Destination |
|-------------|-----------|-----------|---|-------------|----------|-------------|
| Sophie | 192.168.10.10 | 54320 | → | 203.0.113.50 | 20001 | Google (142.250.x.x:80) |
| Marc | 192.168.10.20 | 54321 | → | 203.0.113.50 | 20002 | Google (142.250.x.x:80) |
| Julie | 192.168.20.30 | 54322 | → | 203.0.113.50 | 20003 | Amazon (176.32.x.x:443) |

**Ce que voit le serveur Google** :
```
Requête 1 : depuis 203.0.113.50:20001
Requête 2 : depuis 203.0.113.50:20002
```
→ **2 connexions différentes**, mais **même IP source** (203.0.113.50)

---

**Configuration PAT (Cisco)** :

```cisco
! Définir interface interne (LAN)
interface GigabitEthernet0/0
 ip address 192.168.10.1 255.255.255.0
 ip nat inside
 no shutdown

! Définir interface externe (WAN/Internet)
interface GigabitEthernet0/1
 ip address 203.0.113.50 255.255.255.252
 ip nat outside
 no shutdown

! Créer ACL : Réseau interne à NATer
access-list 1 permit 192.168.0.0 0.0.255.255

! Configurer PAT (overload)
ip nat inside source list 1 interface GigabitEthernet0/1 overload

! Route par défaut vers Internet
ip route 0.0.0.0 0.0.0.0 203.0.113.49
```

**Mot-clé important** : **`overload`** = Active PAT (sinon NAT classique)

---

**Vérification** :

```cisco
show ip nat translations
```

**Sortie** :
```
Pro Inside global      Inside local       Outside local      Outside global
tcp 203.0.113.50:20001 192.168.10.10:54320 142.250.185.36:80 142.250.185.36:80
tcp 203.0.113.50:20002 192.168.10.20:54321 142.250.185.36:80 142.250.185.36:80
tcp 203.0.113.50:20003 192.168.20.30:54322 176.32.103.205:443 176.32.103.205:443
```

---

**Avantages pour l'entreprise** :

✅ **Économie** : 1 seule IP publique au lieu de 85 (coût réduit)  
✅ **Sécurité** : Masque réseau interne (IPs privées invisibles)  
✅ **Flexibilité** : Ajout/suppression postes sans changer IP publique  
✅ **Scalabilité** : Supporte jusqu'à 65000 connexions simultanées  

---

**Barème** :
- Exemple concret d'entreprise : **1 pt**
- Explication fonctionnement : **1 pt**

---

---

## ✅ RÉCAPITULATIF SECTION B (Réseau - 25 points)

| Question | Sujet | Points |
|----------|-------|--------|
| B1 | Subnetting 10.20.0.0/22 → 4 sous-réseaux /24 | 5 pts |
| B2 | ACL Cisco HTTP/HTTPS | 5 pts |
| B3 | Routage inter-VLAN (SVI) | 5 pts |
| B4 | Diagnostic réseau (ping/tracert/pathping) | 5 pts |
| B5 | NAT vs PAT | 4 pts |
| **TOTAL** | **Section B** | **25 pts** |

---

**✅ FIN CORRECTIONS PARTIE 3**

**Suite dans PARTIE 4** : Section C complète (Windows/Active Directory - 25 points)

---


---

# ✅ EXAMEN BLANC 2 - CORRECTIONS DÉTAILLÉES
## Partie 4 : Questionnaire Technique - Section C (Windows/Active Directory)

---

## 🪟 SECTION C : WINDOWS / ACTIVE DIRECTORY (25 points)

### ✔️ Question C1 (5 points) - Commandes PowerShell AD

**a) Lister utilisateurs désactivés dans OU** (3 pts) :

```powershell
Get-ADUser -Filter {Enabled -eq $false} -SearchBase "OU=Personnel,DC=megastore,DC=local" | Select-Object Name, SamAccountName, DistinguishedName
```

**Explications** :
- `Get-ADUser` : Cmdlet pour interroger AD
- `-Filter {Enabled -eq $false}` : Filtre utilisateurs **désactivés**
- `-SearchBase` : Limite recherche à l'OU spécifiée
- `Select-Object` : Affiche colonnes choisies

**Variante avec tableau** :
```powershell
Get-ADUser -Filter {Enabled -eq $false} -SearchBase "OU=Personnel,DC=megastore,DC=local" -Properties DisplayName, EmailAddress | Format-Table Name, SamAccountName, Enabled, EmailAddress -AutoSize
```

**Sortie exemple** :
```
Name        SamAccountName Enabled EmailAddress
----        -------------- ------- ------------
Jean Dupont jdupont        False   jdupont@megastore.local
Marie Martin mmartin       False   mmartin@megastore.local
Pierre Durand pdurand      False   pdurand@megastore.local
```

**Exporter en CSV** :
```powershell
Get-ADUser -Filter {Enabled -eq $false} -SearchBase "OU=Personnel,DC=megastore,DC=local" | Export-Csv C:\Temp\Utilisateurs_Desactives.csv -NoTypeInformation -Encoding UTF8
```

**Compter le nombre** :
```powershell
(Get-ADUser -Filter {Enabled -eq $false} -SearchBase "OU=Personnel,DC=megastore,DC=local").Count
```

---

**b) Réactiver compte utilisateur** (2 pts) :

```powershell
Enable-ADAccount -Identity jdupont
```

**Explications** :
- `Enable-ADAccount` : Cmdlet pour réactiver compte
- `-Identity jdupont` : SamAccountName de l'utilisateur

**Variantes** :

**Par DistinguishedName** :
```powershell
Enable-ADAccount -Identity "CN=Jean Dupont,OU=Personnel,DC=megastore,DC=local"
```

**Par GUID** :
```powershell
Enable-ADAccount -Identity "a1b2c3d4-e5f6-7890-abcd-ef1234567890"
```

**Avec confirmation** :
```powershell
Enable-ADAccount -Identity jdupont -Confirm
```

**Réactiver plusieurs comptes** :
```powershell
Get-ADUser -Filter {Enabled -eq $false} -SearchBase "OU=Personnel,DC=megastore,DC=local" | Enable-ADAccount
```

---

**Vérifier statut** :

```powershell
Get-ADUser -Identity jdupont | Select-Object Name, Enabled
```

**Sortie** :
```
Name        Enabled
----        -------
Jean Dupont True
```

---

**Barème** :
- Commande Get-ADUser avec filtre : **3 pts** (2 pts si filtre incomplet)
- Commande Enable-ADAccount : **2 pts**

---

### ✔️ Question C2 (5 points) - GPO de sécurité

**a) Nœuds GPO pour chaque paramètre** (3 pts) :

**GPO** : `GPO_Securite_Comptabilite`

---

**Paramètre 1 : Désactiver l'USB**

**Chemin** :
```
Configuration ordinateur
→ Stratégies
   → Modèles d'administration
      → Système
         → Accès au stockage amovible
            → Toutes les classes de stockage amovible : Refuser tout accès
               → Activé
```

**Alternative (Registre)** :
```
Configuration ordinateur
→ Préférences
   → Paramètres Windows
      → Registre
         → Nouveau → Élément de Registre
         
Ruche : HKEY_LOCAL_MACHINE
Chemin : SYSTEM\CurrentControlSet\Services\USBSTOR
Nom valeur : Start
Type : REG_DWORD
Données : 4 (désactivé)
Action : Mettre à jour
```

**Valeurs Start** :
- `0` = Boot (démarrage au boot)
- `1` = System (démarrage système)
- `2` = Automatic (automatique)
- `3` = Manual (manuel)
- `4` = **Disabled** (désactivé) ✅

---

**Paramètre 2 : Verrouillage automatique après 5 minutes**

**Chemin** :
```
Configuration ordinateur
→ Stratégies
   → Paramètres Windows
      → Paramètres de sécurité
         → Stratégies locales
            → Options de sécurité
               → Sécurité interactive : Limite d'inactivité machine
                  → Activé
                  → 300 secondes (5 minutes)
```

**Alternative (Écran de veille)** :
```
Configuration utilisateur
→ Stratégies
   → Modèles d'administration
      → Panneau de configuration
         → Personnalisation
            → Activer l'écran de veille
               → Activé
            → Délai d'expiration de l'écran de veille
               → 300 secondes
            → Protection par mot de passe de l'écran de veille
               → Activé
```

---

**Paramètre 3 : Complexité mots de passe (longueur min 12)**

**Chemin** :
```
Configuration ordinateur
→ Stratégies
   → Paramètres Windows
      → Paramètres de sécurité
         → Stratégies de compte
            → Stratégie de mot de passe
               → Longueur minimale du mot de passe
                  → 12 caractères
               → Le mot de passe doit respecter des exigences de complexité
                  → Activé
```

**Exigences complexité** (si activé) :
- Minimum **3 des 4 catégories** :
  - Majuscules (A-Z)
  - Minuscules (a-z)
  - Chiffres (0-9)
  - Caractères spéciaux (!@#$%...)
- Ne contient pas le **nom du compte** utilisateur
- Longueur minimum : 12 caractères (dans ce cas)

**Exemples MDP valides** :
- ✅ `Megastore2025!` (Maj + Min + Chiffres + Spéciaux)
- ✅ `P@ssw0rd1234` (4 catégories)
- ❌ `jdupont123!` (contient le nom du compte)
- ❌ `Password` (pas de chiffres ni spéciaux)

---

**Barème** :
- USB désactivé (chemin correct) : **1 pt**
- Verrouillage 5 min (chemin correct) : **1 pt**
- Complexité MDP (chemin correct) : **1 pt**

---

**b) Forcer application immédiate GPO** (2 pts) :

**Sur le PC** :

```cmd
gpupdate /force
```

**Explications** :
- `gpupdate` : Met à jour les stratégies de groupe
- `/force` : Force la réapplication (même si GPO non modifiée)

**Sortie** :
```
Mise à jour de la stratégie...

Traitement de la stratégie ordinateur terminé.
Traitement de la stratégie utilisateur terminé.

La mise à jour de la stratégie a réussi.
```

---

**Options supplémentaires** :

**Avec redémarrage** (si nécessaire pour certaines GPO) :
```cmd
gpupdate /force /boot
```

**Avec déconnexion** (pour stratégies utilisateur) :
```cmd
gpupdate /force /logoff
```

**Attendre fin** :
```cmd
gpupdate /force /wait:0
```
(`/wait:0` = Attend indéfiniment la fin)

**Cibler ordinateur OU utilisateur** :
```cmd
gpupdate /target:computer /force
gpupdate /target:user /force
```

---

**Vérifier application** :

**Résumé des GPO** :
```cmd
gpresult /r
```

**Rapport HTML détaillé** :
```cmd
gpresult /h C:\Temp\GPResult.html
```

**Voir GPO appliquées** :
```cmd
gpresult /scope:computer /r
gpresult /scope:user /r
```

---

**Délai d'application** :

- **Immédiatement** après `gpupdate /force`
- Sinon rafraîchissement automatique :
  - **Ordinateurs** : Toutes les 90 minutes (±30 min aléatoire)
  - **Contrôleurs de domaine** : Toutes les 5 minutes
  - **Au démarrage** (GPO ordinateur) et **à la connexion** (GPO utilisateur)

---

**Barème** :
- Commande gpupdate /force : **2 pts**

---

### ✔️ Question C3 (5 points) - DNS Windows Server

**a) Zone principale vs zone secondaire** (2 pts) :

**Zone principale (Primary Zone)** :

**Définition** :
- **Zone maître** : Contient la **copie modifiable** des enregistrements DNS
- **Lecture/Écriture** : Administrateur peut ajouter/modifier/supprimer enregistrements
- **Fichier zone** : Stocké dans `C:\Windows\System32\dns\` (ex: `megastore.local.dns`)
- **Autorité** : Serveur **autoritaire** (SOA = Start of Authority)

**Usage** :
- **Serveur DNS principal** de l'entreprise
- **1 seul serveur** héberge la zone principale (ou plusieurs si Active Directory Integrated)

**Avantages** :
- ✅ Contrôle total sur les enregistrements
- ✅ Source de vérité pour la zone
- ✅ Gestion centralisée

**Inconvénients** :
- ❌ Point de défaillance unique (si pas de secondaires)
- ❌ Charge importante si nombreux clients

---

**Zone secondaire (Secondary Zone)** :

**Définition** :
- **Zone esclave** : Copie **en lecture seule** de la zone principale
- **Réplication** : Synchronisée depuis la zone principale (transfert de zone AXFR/IXFR)
- **Pas de modification locale** : Tout changement doit être fait sur la principale
- **Redondance** : Si serveur principal HS, secondaire répond aux requêtes

**Usage** :
- **Haute disponibilité** DNS
- **Répartition de charge** (plusieurs serveurs DNS)
- **Sites distants** (copie locale pour réduire latence WAN)

**Avantages** :
- ✅ Redondance (continuité service si principal HS)
- ✅ Répartition charge (load balancing)
- ✅ Réduction latence (serveur local site distant)

**Inconvénients** :
- ❌ Lecture seule (pas de modifications directes)
- ❌ Dépendant du transfert de zone

---

**Tableau comparatif** :

| Critère | Zone Principale | Zone Secondaire |
|---------|-----------------|-----------------|
| **Modification** | Oui (R/W) | Non (R/O) |
| **Autorité** | Oui (SOA) | Non (copie) |
| **Fichier local** | Oui | Oui (répliqué) |
| **Transfert zone** | Source | Destination |
| **Nombre** | 1 seul | Plusieurs possibles |
| **Usage** | Gestion zones | Redondance/performance |

---

**Exemple architecture** :

```
SRV-DNS-01 (192.168.10.10) - SITE PRINCIPAL
   ↓
Zone PRINCIPALE : megastore.local (R/W)
   - Enregistrement A : web.megastore.local → 192.168.100.50
   - Enregistrement A : mail.megastore.local → 192.168.100.60
   - Enregistrement CNAME : www → web
   ↓
   [Transfert de zone AXFR/IXFR]
   ↓
SRV-DNS-02 (192.168.10.11) - SITE PRINCIPAL
SRV-DNS-03 (192.168.20.10) - SITE DISTANT
   ↓
Zone SECONDAIRE : megastore.local (R/O - réplique)
```

**Configuration transfert** :
```
SRV-DNS-01 → Propriétés zone megastore.local
→ Onglet "Transferts de zone"
→ ☑ Autoriser les transferts de zone
→ ⦿ Uniquement vers les serveurs suivants : 
     192.168.10.11 (SRV-DNS-02)
     192.168.20.10 (SRV-DNS-03)
```

---

**Barème** :
- Zone principale définie : **1 pt**
- Zone secondaire définie : **1 pt**

---

**b) Créer enregistrement A** (3 pts) :

**Méthode 1 : GUI (Gestionnaire DNS)**

**Étapes** :

1. **Ouvrir Gestionnaire DNS** :
   ```
   Démarrer → Outils d'administration Windows → DNS
   ```

2. **Naviguer vers la zone** :
   ```
   SRV-DNS-01 → Zones de recherche directes → megastore.local
   ```

3. **Créer enregistrement** :
   ```
   Clic droit sur "megastore.local" → Nouvel hôte (A ou AAAA)...
   ```

4. **Renseigner** :
   ```
   Nom : web
   Nom de domaine complet (FQDN) : web.megastore.local
   Adresse IP : 192.168.100.50
   ☑ Créer un enregistrement PTR associé (reverse lookup)
   ```

5. **Cliquer sur "Ajouter un hôte"**

6. **Message confirmation** :
   ```
   "L'hôte web.megastore.local a été créé avec succès"
   ```

7. **Vérifier** :
   ```
   Enregistrement "web" apparaît dans la liste avec type A et IP 192.168.100.50
   ```

---

**Méthode 2 : PowerShell**

```powershell
Add-DnsServerResourceRecordA -Name "web" -ZoneName "megastore.local" -IPv4Address "192.168.100.50"
```

**Explications** :
- `Add-DnsServerResourceRecordA` : Cmdlet pour ajouter enregistrement A
- `-Name "web"` : Nom de l'hôte (sans domaine)
- `-ZoneName "megastore.local"` : Zone DNS
- `-IPv4Address` : Adresse IP associée

**Avec TTL personnalisé** :
```powershell
Add-DnsServerResourceRecordA -Name "web" -ZoneName "megastore.local" -IPv4Address "192.168.100.50" -TimeToLive 01:00:00
```
(TTL = 1 heure)

**Avec PTR automatique** :
```powershell
Add-DnsServerResourceRecordA -Name "web" -ZoneName "megastore.local" -IPv4Address "192.168.100.50" -CreatePtr
```

---

**Vérification** :

**PowerShell** :
```powershell
Get-DnsServerResourceRecord -ZoneName "megastore.local" -Name "web"
```

**Sortie** :
```
HostName                  RecordType Type       Timestamp            TimeToLive      RecordData
--------                  ---------- ----       ---------            ----------      ----------
web                       A          1          0                    01:00:00        192.168.100.50
```

**nslookup depuis un PC** :
```cmd
nslookup web.megastore.local 192.168.10.10
```

**Sortie** :
```
Serveur :   SRV-DNS-01.megastore.local
Adresse :   192.168.10.10

Nom :       web.megastore.local
Adresse :   192.168.100.50
```

---

**Créer également PTR** (reverse lookup) :

**Zone inverse** : `100.168.192.in-addr.arpa`

**PowerShell** :
```powershell
Add-DnsServerResourceRecordPtr -Name "50" -ZoneName "100.168.192.in-addr.arpa" -PtrDomainName "web.megastore.local"
```

**Vérifier PTR** :
```cmd
nslookup 192.168.100.50 192.168.10.10
```

**Sortie** :
```
Serveur :   SRV-DNS-01.megastore.local
Adresse :   192.168.10.10

Nom :       web.megastore.local
Adresse :   192.168.100.50
```

---

**Autres types d'enregistrements** :

**CNAME (alias)** :
```powershell
Add-DnsServerResourceRecordCName -Name "www" -ZoneName "megastore.local" -HostNameAlias "web.megastore.local"
```

**MX (mail)** :
```powershell
Add-DnsServerResourceRecordMX -Name "." -ZoneName "megastore.local" -MailExchange "mail.megastore.local" -Preference 10
```

---

**Barème** :
- Procédure GUI complète et correcte : **3 pts** OU
- Commande PowerShell correcte avec paramètres : **3 pts**
- (2 pts si procédure incomplète ou paramètres manquants)

---

### ✔️ Question C4 (5 points) - Sauvegarde Windows Server

**a) 2 méthodes natives** (2 pts) :

**Méthode 1 : Windows Server Backup**

**Description** :
- Outil **GUI et ligne de commande** (wbadmin.exe)
- Inclus dans Windows Server (fonctionnalité à installer)
- Sauvegarde complète, incrémentielle, sélective
- Support disques locaux, externes, partages réseau

**Installation** :
```powershell
Install-WindowsFeature Windows-Server-Backup -IncludeManagementTools
```

**Utilisation GUI** :
```
Gestionnaire de serveur → Outils → Sauvegarde Windows Server
```

**Types sauvegarde** :
- **Sauvegarde complète du serveur** : Tous les volumes
- **Sauvegarde personnalisée** : Volumes sélectionnés
- **État du système** (System State) : Registre, AD, Boot files
- **Sauvegarde bare-metal** (BMR) : Restauration complète sur nouveau matériel

---

**Méthode 2 : System State Backup (wbadmin)**

**Description** :
- Sauvegarde **état du système** uniquement
- Contient :
  - Registre Windows
  - Base Active Directory (si contrôleur de domaine)
  - Fichiers de démarrage (boot files)
  - Base de données de certificats (si serveur CA)
  - Base IIS (si serveur Web)
  - SYSVOL (si contrôleur de domaine)
  - COM+ et WMI

**Commande** :
```cmd
wbadmin start systemstatebackup -backupTarget:E:
```

**Usage** :
- ✅ Restauration AD après panne
- ✅ Récupération configuration système
- ✅ Plus rapide que sauvegarde complète

---

**Méthode 3 (bonus) : Clichés instantanés (VSS)**

**Description** :
- **Snapshots** de volumes (Volume Shadow Copy Service)
- Permet restauration fichiers individuels
- Intégré à l'Explorateur Windows ("Versions précédentes")
- Sauvegarde incrémentielle automatique

**Activation** :
```
Explorateur → Clic droit sur volume C:
→ Configurer les clichés instantanés
→ Activer
→ Définir planification (quotidien 7h00, 12h00)
```

**PowerShell** :
```powershell
vssadmin create shadow /for=C:
```

**Restauration fichier** :
```
Clic droit sur fichier → Restaurer les versions précédentes
→ Sélectionner version → Restaurer
```

---

**Barème** :
- Méthode 1 (Windows Server Backup) définie : **1 pt**
- Méthode 2 (System State ou VSS ou autre) définie : **1 pt**

---

**b) Commande wbadmin sauvegarde complète** (3 pts) :

```cmd
wbadmin start backup -backupTarget:E: -include:C: -allCritical -quiet
```

**Explications** :

- `wbadmin start backup` : Lance une sauvegarde
- `-backupTarget:E:` : **Destination** = Disque E: (peut être lecteur externe, NAS)
- `-include:C:` : **Inclure volume C:** (système d'exploitation)
- `-allCritical` : Inclut **tous les volumes critiques** (boot, système, récupération EFI)
- `-quiet` : Mode silencieux (pas de confirmation interactive)

---

**Variantes** :

**Sauvegarde complète du serveur** (tous volumes) :
```cmd
wbadmin start backup -backupTarget:E: -allCritical -quiet
```

**Sauvegarde de plusieurs volumes** :
```cmd
wbadmin start backup -backupTarget:E: -include:C:,D:,F: -allCritical -quiet
```

**Sauvegarde vers partage réseau** (UNC) :
```cmd
wbadmin start backup -backupTarget:\\SERVEUR-NAS\Backup -include:C: -allCritical -quiet
```

**Sauvegarde avec VSS complet** :
```cmd
wbadmin start backup -backupTarget:E: -include:C: -allCritical -vssFull -quiet
```
(`-vssFull` : Sauvegarde complète VSS, efface historique logs applicatifs SQL/Exchange)

**Sauvegarde planifiée récurrente** :
```cmd
wbadmin enable backup -addtarget:E: -schedule:02:00 -include:C: -allCritical -quiet
```
(Quotidienne à 2h00 du matin)

---

**Autres commandes wbadmin utiles** :

**Lister sauvegardes disponibles** :
```cmd
wbadmin get versions
```

**Sortie** :
```
Version de sauvegarde : 12/11/2025-08:00
Cible de sauvegarde : Disque nommé "Sauvegarde" (E:)
État : Succès
```

**Obtenir détails d'une sauvegarde** :
```cmd
wbadmin get items -version:12/11/2025-08:00
```

**Restaurer fichier/dossier** :
```cmd
wbadmin start recovery -version:12/11/2025-08:00 -itemType:File -items:C:\Data\fichier.txt -recoverToAlternateLocation -alternateTargetLocation:C:\Restore\
```

**Restaurer volume complet** :
```cmd
wbadmin start recovery -version:12/11/2025-08:00 -itemType:Volume -items:C: -recoverToAlternateLocation -alternateTargetLocation:F:
```

**Restaurer System State** :
```cmd
wbadmin start systemstaterecovery -version:12/11/2025-08:00
```

**Désactiver sauvegarde planifiée** :
```cmd
wbadmin disable backup
```

---

**Sortie commande backup** :

```
wbadmin 1.0 - Outil de ligne de commande de sauvegarde
(C) Copyright Microsoft Corporation. Tous droits réservés.

Récupération du volume à sauvegarder...
Création du cliché instantané...
Sauvegarde en cours : 35 % terminée.
Sauvegarde en cours : 73 % terminée.
Sauvegarde en cours : 100 % terminée.

Résumé de la sauvegarde :
Sauvegarde de C: terminée avec succès.
Durée : 00:15:32
Taille : 45 Go
```

---

**Barème** :
- Commande wbadmin start backup : **2 pts**
- Options correctes (-backupTarget, -include, -allCritical) : **1 pt**
- (1-2 pts si commande incomplète ou erreur syntaxe)

---

### ✔️ Question C5 (5 points) - Stratégies de mot de passe

**a) Configurer stratégie de mot de passe fine (FGPP)** (3 pts) :

**FGPP (Fine-Grained Password Policy)** :
- Permet des **stratégies de mot de passe différentes** par groupe d'utilisateurs
- Appelée aussi **PSO** (Password Settings Object)
- Exemple : Admins → MDP 16 caractères, Utilisateurs standard → 12 caractères

---

**Prérequis** :
- Niveau fonctionnel domaine : **Windows Server 2008+**
- Droits : **Admins du domaine**
- Outil : **Centre d'administration Active Directory** ou **PowerShell**

---

**Méthode 1 : Centre d'administration AD (GUI)**

**Étapes** :

1. **Ouvrir Centre d'administration AD** :
   ```
   Démarrer → Outils d'administration Windows → Centre d'administration Active Directory
   ```

2. **Naviguer** :
   ```
   megastore (local) → System → Password Settings Container
   ```

3. **Créer PSO** (Password Settings Object) :
   ```
   Clic droit dans panneau droit → Nouveau → Paramètres de mot de passe
   ```

4. **Configurer PSO** :
   ```
   Nom : PSO_Admins_Strict
   Précédence : 10 (plus faible = prioritaire)
   
   Stratégies mot de passe :
   ☑ Imposer l'historique des mots de passe : 24 mots de passe
   ☑ Durée de vie maximale du mot de passe : 60 jours
   ☑ Durée de vie minimale du mot de passe : 1 jour
   ☑ Longueur minimale du mot de passe : 16 caractères
   ☑ Le mot de passe doit respecter des exigences de complexité : Activé
   ☑ Chiffrer les mots de passe à l'aide d'un chiffrement réversible : Désactivé
   
   Options de verrouillage de compte :
   ☑ Appliquer le verrouillage du compte : Activé
   ☑ Durée de verrouillage du compte : 30 minutes
   ☑ Nombre de tentatives d'ouverture de session ayant échoué autorisé : 3
   ☑ Réinitialiser le compteur de tentatives d'ouverture de session ayant échoué après : 30 minutes
   ```

5. **Appliquer au groupe** :
   ```
   Section "S'applique directement à"
   → Ajouter...
   → Entrer noms d'objets : Admins du domaine
   → Vérifier les noms
   → OK
   ```

6. **Enregistrer** : Cliquer sur OK

---

**Méthode 2 : PowerShell**

```powershell
# Créer PSO
New-ADFineGrainedPasswordPolicy `
    -Name "PSO_Admins_Strict" `
    -Precedence 10 `
    -ComplexityEnabled $true `
    -MinPasswordLength 16 `
    -MaxPasswordAge "60.00:00:00" `
    -MinPasswordAge "1.00:00:00" `
    -PasswordHistoryCount 24 `
    -LockoutThreshold 3 `
    -LockoutDuration "0.00:30:00" `
    -LockoutObservationWindow "0.00:30:00" `
    -ReversibleEncryptionEnabled $false

# Appliquer au groupe
Add-ADFineGrainedPasswordPolicySubject `
    -Identity "PSO_Admins_Strict" `
    -Subjects "Admins du domaine"
```

**Explications paramètres** :
- `-Precedence 10` : Priorité (10 < 20 < 30...)
- `-MaxPasswordAge "60.00:00:00"` : Format jours.heures:minutes:secondes
- `-LockoutDuration "0.00:30:00"` : 30 minutes de verrouillage
- `-LockoutObservationWindow` : Fenêtre observation tentatives

---

**Créer PSO pour utilisateurs standard** :

```powershell
New-ADFineGrainedPasswordPolicy `
    -Name "PSO_Users_Standard" `
    -Precedence 20 `
    -ComplexityEnabled $true `
    -MinPasswordLength 12 `
    -MaxPasswordAge "90.00:00:00" `
    -MinPasswordAge "1.00:00:00" `
    -PasswordHistoryCount 12 `
    -LockoutThreshold 5 `
    -LockoutDuration "0.00:15:00" `
    -LockoutObservationWindow "0.00:15:00"

Add-ADFineGrainedPasswordPolicySubject -Identity "PSO_Users_Standard" -Subjects "Utilisateurs du domaine"
```

---

**Vérification** :

**Lister toutes les PSO** :
```powershell
Get-ADFineGrainedPasswordPolicy -Filter * | Select-Object Name, Precedence, MinPasswordLength, MaxPasswordAge
```

**Sortie** :
```
Name                Precedence MinPasswordLength MaxPasswordAge
----                ---------- ----------------- --------------
PSO_Admins_Strict   10         16                60.00:00:00
PSO_Users_Standard  20         12                90.00:00:00
```

**Voir PSO appliquée à un utilisateur** :
```powershell
Get-ADUserResultantPasswordPolicy -Identity "admin1"
```

**Sortie** :
```
Name             : PSO_Admins_Strict
Precedence       : 10
MinPasswordLength: 16
MaxPasswordAge   : 60.00:00:00
```

**Voir utilisateurs/groupes liés à une PSO** :
```powershell
Get-ADFineGrainedPasswordPolicySubject -Identity "PSO_Admins_Strict"
```

---

**Barème** :
- Création PSO (GUI OU PowerShell) avec paramètres corrects : **2 pts**
- Application à un groupe : **1 pt**
- (1-2 pts si procédure incomplète ou paramètres manquants)

---

**b) Différence avec stratégie domaine par défaut** (2 pts) :

**Stratégie de mot de passe de domaine par défaut** :

**Caractéristiques** :
- **Unique** pour tout le domaine
- Définie dans **Default Domain Policy** GPO (liée à la racine du domaine)
- **Tous les utilisateurs** ont les mêmes règles
- **Simplicité** : Configuration centralisée
- **Limite** : Pas de granularité

**Chemin GPO** :
```
GPO : Default Domain Policy
→ Configuration ordinateur
   → Stratégies
      → Paramètres Windows
         → Paramètres de sécurité
            → Stratégies de compte
               → Stratégie de mot de passe
```

**Exemple** :
```
Longueur minimale du mot de passe : 8 caractères
Le mot de passe doit respecter des exigences de complexité : Activé
Durée de vie maximale du mot de passe : 42 jours
Durée de vie minimale du mot de passe : 1 jour
Conserver l'historique des mots de passe : 24
```
→ S'applique à **TOUS** les utilisateurs du domaine (sauf si PSO appliquée)

---

**Stratégie de mot de passe fine (FGPP/PSO)** :

**Caractéristiques** :
- **Multiples stratégies** possibles (nombre illimité)
- **Granulaire** : Par groupe OU par utilisateur individuel
- **Priorité** : Numéro de précédence (10, 20, 30...)
- **Flexibilité** : Admins = règles strictes, Utilisateurs = règles souples
- **Active Directory natif** : Pas de GPO (objets AD dans conteneur System)

**Exemple** :
```
PSO_Admins (précédence 10) :
  - Longueur : 16 caractères
  - Durée : 60 jours
  - Verrouillage : 3 tentatives
  → Appliqué au groupe "Admins du domaine"

PSO_Users (précédence 20) :
  - Longueur : 12 caractères
  - Durée : 90 jours
  - Verrouillage : 5 tentatives
  → Appliqué au groupe "Utilisateurs"

PSO_ServiceAccounts (précédence 30) :
  - Longueur : 20 caractères (comptes de service)
  - Durée : 365 jours (pas de changement fréquent)
  → Appliqué à utilisateur "svc_backup"
```

---

**Tableau comparatif** :

| Critère | Stratégie domaine | FGPP/PSO |
|---------|-------------------|----------|
| **Nombre** | 1 seule | Plusieurs (illimité) |
| **Granularité** | Tout le domaine | Par groupe/utilisateur |
| **Priorité** | N/A | Précédence (10, 20, ...) |
| **Flexibilité** | Faible | Élevée |
| **Outil config** | GPO (Default Domain Policy) | Centre admin AD / PowerShell |
| **Stockage** | SYSVOL (GPO) | Active Directory (conteneur System) |
| **Niveau fonctionnel** | Tous | Windows Server 2008+ |
| **Application** | Automatique (tous) | Ciblée (groupes/users) |

---

**Ordre de priorité (résolution PSO)** :

1. **PSO appliquée directement à l'utilisateur** (priorité absolue)
   - Exemple : PSO liée à "jdupont" → S'applique même si membre de groupe avec autre PSO

2. **PSO avec précédence la plus faible** (si utilisateur dans plusieurs groupes avec PSO)
   - Exemple : User1 dans "Admins" (PSO précédence **10**) ET "Compta" (PSO précédence **20**)
   - → PSO "Admins" (**10**) s'applique (10 < 20)

3. **Stratégie domaine par défaut** (Default Domain Policy)
   - Si aucune PSO applicable à l'utilisateur

---

**Exemple concret** :

**Utilisateur** : `mmartin`  
**Groupes** : 
- Admins du domaine (PSO_Admins - précédence 10)
- Comptabilité (PSO_Compta - précédence 15)

**PSO appliquée** : PSO_Admins (10 < 15)

**Vérification** :
```powershell
Get-ADUserResultantPasswordPolicy -Identity mmartin
```

**Sortie** :
```
Name             : PSO_Admins
Precedence       : 10
MinPasswordLength: 16
```

---

**Barème** :
- Stratégie domaine définie (Default Domain Policy) : **1 pt**
- Différence avec FGPP expliquée (granularité, priorité) : **1 pt**

---

---

## ✅ RÉCAPITULATIF SECTION C (Windows/AD - 25 points)

| Question | Sujet | Points |
|----------|-------|--------|
| C1 | PowerShell AD (Get-ADUser, Enable-ADAccount) | 5 pts |
| C2 | GPO sécurité (USB, verrouillage, MDP) + gpupdate | 5 pts |
| C3 | DNS (zones principale/secondaire, enregistrement A) | 5 pts |
| C4 | Sauvegarde Windows Server (wbadmin) | 5 pts |
| C5 | FGPP/PSO (stratégies MDP fines) | 5 pts |
| **TOTAL** | **Section C** | **25 pts** |

---

**✅ FIN CORRECTIONS PARTIE 4**

**Suite dans PARTIE 5** : Sections D (Virtualisation) et E (Sécurité) - 30 points

---


---

# ✅ EXAMEN BLANC 2 - CORRECTIONS DÉTAILLÉES
## Partie 5 : Questionnaire Technique - Sections D et E

---

## 💾 SECTION D : VIRTUALISATION VMWARE (15 points)

### ✔️ Question D1 (5 points) - Types de disques virtuels

**a) Expliquer les 3 types** (3 pts) :

---

**Type 1 : Thick Provision Lazy Zeroed**

**Définition** :
- **Espace réservé immédiatement** lors de la création de la VM
- Les **blocs ne sont pas effacés** (pas de remplissage par zéros)
- **Écriture à la demande** : Les zéros sont écrits au moment de l'utilisation

**Fonctionnement** :
```
Création VM avec disque 100 Go Thick Lazy Zeroed :
→ Datastore : 100 Go réservés immédiatement
→ Blocs : Non effacés (contiennent données anciennes)
→ Première écriture : Bloc rempli de zéros puis données écrites
```

**Avantages** :
- ✅ **Création rapide** (pas d'effacement)
- ✅ **Performances bonnes** (espace garanti)
- ✅ **Pas de surallocation** (espace réservé)

**Inconvénients** :
- ❌ **Légère latence** première écriture (zeroing à la volée)
- ❌ **Moins sécurisé** (traces données anciennes)

**Usage** :
- Environnements **non critiques**
- VMs de **développement/test**
- Quand la **rapidité de déploiement** prime

---

**Type 2 : Thick Provision Eager Zeroed**

**Définition** :
- **Espace réservé immédiatement** lors de la création
- Les **blocs sont effacés** (remplis de zéros) dès la création
- **Performances maximales** dès la première écriture

**Fonctionnement** :
```
Création VM avec disque 100 Go Thick Eager Zeroed :
→ Datastore : 100 Go réservés immédiatement
→ Blocs : Tous remplis de zéros à la création (prend du temps)
→ Première écriture : Directe (pas de zeroing)
```

**Avantages** :
- ✅ **Performances maximales** (pas de latence écriture)
- ✅ **Sécurité** (données anciennes effacées)
- ✅ **Requis pour Fault Tolerance** (FT nécessite Eager Zeroed)
- ✅ **Compatible clustering** (MSCS, Oracle RAC)

**Inconvénients** :
- ❌ **Création très lente** (effacement complet : plusieurs minutes pour 100 Go)
- ❌ **Espace consommé immédiatement** (pas d'économie)

**Usage** :
- Serveurs de **production critiques**
- **Bases de données** (SQL Server, Oracle)
- VMs avec **Fault Tolerance** activée
- Applications nécessitant **performances maximales**

---

**Type 3 : Thin Provisioning**

**Définition** :
- **Espace alloué à la demande** (pas de réservation)
- Le disque **grandit dynamiquement** selon l'utilisation
- **Optimisation de l'espace** datastore

**Fonctionnement** :
```
Création VM avec disque 100 Go Thin :
→ Datastore : 1 Go utilisé initialement (OS uniquement)
→ VM écrit 20 Go de données : Datastore = 21 Go
→ VM écrit 50 Go de plus : Datastore = 71 Go
→ Maximum : 100 Go (taille configurée)
```

**Avantages** :
- ✅ **Économie d'espace** (20-50% gain)
- ✅ **Déploiement rapide** (création instantanée)
- ✅ **Surallocation possible** (provisionner 10 VMs de 100 Go sur datastore 500 Go)
- ✅ **Flexibilité** (migration Storage vMotion plus rapide)

**Inconvénients** :
- ❌ **Risque saturation datastore** (si surallocation excessive)
- ❌ **Performances légèrement inférieures** (allocation dynamique)
- ❌ **Fragmentation** (blocs dispersés)
- ❌ **Monitoring requis** (surveiller espace datastore)

**Usage** :
- **Environnements de développement/test**
- VDI (Virtual Desktop Infrastructure)
- VMs avec **utilisation faible** (serveurs fichiers, DNS)
- Quand **économie d'espace** est prioritaire

---

**Tableau comparatif** :

| Critère | Thick Lazy Zeroed | Thick Eager Zeroed | Thin Provisioning |
|---------|-------------------|---------------------|-------------------|
| **Allocation espace** | Immédiate | Immédiate | À la demande |
| **Effacement blocs** | À la demande | À la création | À la demande |
| **Création VM** | Rapide (secondes) | Lente (minutes) | Très rapide |
| **Performances** | Bonnes | Maximales | Bonnes (légère latence) |
| **Économie espace** | Non | Non | Oui (20-50%) |
| **Sécurité** | Faible | Élevée | Faible |
| **Fault Tolerance** | Non compatible | Compatible | Non compatible |
| **Usage** | Dev/Test | Production critique | Dev/VDI/Économie |

---

**Barème** :
- Thick Lazy Zeroed défini : **1 pt**
- Thick Eager Zeroed défini : **1 pt**
- Thin Provisioning défini : **1 pt**

---

**b) Meilleur type pour base de données production** (2 pts) :

**Réponse** : **Thick Provision Eager Zeroed** ✅

---

**Justification** :

**Raisons techniques** :

1. **Performances maximales** :
   - Pas de latence lors des écritures (blocs déjà effacés)
   - I/O prévisibles et constants
   - Critique pour bases de données (SQL Server, Oracle, PostgreSQL)

2. **Espace garanti** :
   - Pas de risque saturation datastore en pleine transaction
   - Opérations CRUD stables (Create, Read, Update, Delete)

3. **Compatibilité clustering** :
   - Requis pour **MSCS** (Microsoft Cluster Service)
   - Supporté par **Oracle RAC**
   - Nécessaire pour **SQL Server Always On FCI**

4. **Sécurité données** :
   - Blocs effacés (pas de traces données anciennes)
   - Conforme réglementations (RGPD, PCI-DSS)

5. **Compatibilité Fault Tolerance** :
   - Si besoin activer FT (haute disponibilité instantanée)
   - Protection panne matérielle ESXi

---

**Configuration recommandée pour BDD production** :

```
VM SQL Server Production :
- vCPU : 8
- RAM : 32 Go
- Disque OS (C:) : 100 Go - Thick Eager Zeroed
- Disque Data (D:) : 500 Go - Thick Eager Zeroed (fichiers .mdf)
- Disque Logs (L:) : 200 Go - Thick Eager Zeroed (fichiers .ldf)
- Disque TempDB (T:) : 100 Go - Thick Eager Zeroed
- Réseau : VMXNET3 (10 Gbps)
- Datastore : SSD/Flash (faible latence)
```

---

**Alternatives selon contexte** :

**Si contrainte espace** :
- **Thick Lazy Zeroed** acceptable
- Performances légèrement inférieures (acceptable si BDD < 500 Go)
- Ne pas utiliser Thin (risque saturation)

**Si base de données de développement/test** :
- **Thin Provisioning** acceptable
- Économie d'espace (plusieurs environnements sur même datastore)
- Performances suffisantes pour tests

---

**Commandes conversion (si nécessaire)** :

**Convertir Thin → Eager Zeroed** :
```bash
vmkfstools -j zeroedthick /vmfs/volumes/DATASTORE/VM/disk.vmdk
```

**Convertir via Storage vMotion** :
- Migrer VM vers autre datastore
- Sélectionner format disque : "Thick Provision Eager Zeroed"
- Avantage : Pas d'arrêt VM (si Storage vMotion disponible)

---

**Barème** :
- Choix correct (Thick Eager Zeroed) : **1 pt**
- Justification (performances, sécurité, clustering) : **1 pt**

---

### ✔️ Question D2 (5 points) - vMotion et Storage vMotion

**a) Différence vMotion vs Storage vMotion** (3 pts) :

---

**vMotion (Live Migration)**

**Définition** :
- Migration **à chaud** d'une VM d'un **hôte ESXi vers un autre**
- VM reste **allumée** pendant la migration
- **Downtime** : < 1 seconde (imperceptible)
- Les **fichiers VM restent sur le même datastore**

**Fonctionnement** :
```
1. Pré-migration :
   - Copie de la mémoire RAM vers hôte cible
   - Pages mémoire modifiées copiées itérativement

2. Switchover (< 1 seconde) :
   - VM gelée sur hôte source
   - Transfert état CPU et pages mémoire finales
   - VM redémarre sur hôte cible

3. Post-migration :
   - Connexions réseau redirigées (ARP gratuit)
   - VM continue fonctionnement
```

**Prérequis** :
- ✅ vCenter Server
- ✅ Même version CPU (Intel → Intel, AMD → AMD) ou EVC activé
- ✅ Réseau vMotion configuré (VMkernel port)
- ✅ Accès au **même datastore** (source et cible)
- ✅ Mêmes réseaux virtuels (portgroups)

**Usage** :
- **Maintenance hôte** (mode maintenance ESXi)
- **Répartition charge** (DRS automatique)
- **Éviter downtime** lors d'interventions

---

**Storage vMotion**

**Définition** :
- Migration **à chaud** des **fichiers VM** d'un **datastore vers un autre**
- VM reste **allumée** pendant la migration
- **L'hôte ESXi reste le même** (pas de changement hôte)
- Peut **changer le format disque** (Thin ↔ Thick)

**Fonctionnement** :
```
1. Pré-migration :
   - Copie fichiers disque (.vmdk) vers datastore cible
   - VM continue fonctionnement sur datastore source

2. Migration active :
   - Shadow VM sur datastore cible
   - Synchronisation blocs modifiés (mirror)

3. Switchover (< 1 seconde) :
   - Basculement vers datastore cible
   - Suppression fichiers source

4. Post-migration :
   - VM fonctionne sur datastore cible
```

**Prérequis** :
- ✅ vCenter Server
- ✅ Datastore cible accessible par l'hôte ESXi
- ✅ Espace suffisant sur datastore cible
- ✅ Performances réseau correctes (SAN/NAS)

**Usage** :
- **Maintenance datastore** (upgrade stockage)
- **Répartition charge stockage** (SDRS automatique)
- **Migration vers nouveau SAN**
- **Conversion format disque** (Thin → Thick)

---

**Tableau comparatif** :

| Critère | vMotion | Storage vMotion |
|---------|---------|-----------------|
| **Migration** | Hôte ESXi | Datastore |
| **Fichiers VM** | Restent en place | Déplacés |
| **Hôte ESXi** | Change | Reste le même |
| **Datastore** | Reste le même | Change |
| **Downtime** | < 1 seconde | < 1 seconde |
| **Prérequis réseau** | vMotion VMkernel | Accès datastores |
| **Conversion disque** | Non | Oui (Thin/Thick) |
| **Usage DRS** | Oui (automatique) | Oui (SDRS automatique) |

---

**vMotion + Storage vMotion combinés** :

Possibilité de migrer **simultanément** :
- **Hôte ESXi** (vMotion)
- **Datastore** (Storage vMotion)

**Commande vSphere Client** :
```
VM → Migrate...
→ Sélectionner : "Change both compute resource and storage"
→ Choisir hôte cible + datastore cible
```

**Usage** :
- Migration **datacenter → datacenter**
- Évacuation **cluster complet**
- Migration **vers nouveau cluster vSphere**

---

**Barème** :
- vMotion défini (migration hôte) : **1.5 pt**
- Storage vMotion défini (migration datastore) : **1.5 pt**

---

**b) 2 cas d'usage** (2 pts) :

---

**Cas d'usage 1 : Maintenance matérielle hôte ESXi**

**Scénario** :
- Serveur **ESXi-HOST-02** nécessite **upgrade RAM** (64 Go → 128 Go)
- 15 VMs en production (dont 5 critiques)
- Intervention prévue : **Samedi 2h00**

**Solution avec vMotion** :

**Étape 1 : Passer hôte en mode maintenance**
```
vSphere Client → ESXi-HOST-02
→ Clic droit → Maintenance Mode → Enter Maintenance Mode
→ ☑ Move powered off and suspended virtual machines
```

**Résultat** :
- **DRS migre automatiquement** les 15 VMs vers ESXi-HOST-01 et ESXi-HOST-03
- Migration **sans downtime** (vMotion)
- Durée : 5-10 minutes (selon taille RAM VMs)

**Étape 2 : Intervention physique**
- Hôte ESXi-HOST-02 : **0 VM**, prêt intervention
- Ajout barrettes RAM 128 Go
- Redémarrage serveur

**Étape 3 : Sortie mode maintenance**
```
ESXi-HOST-02 → Exit Maintenance Mode
```

**Résultat** :
- Hôte disponible pour héberger VMs
- DRS rééquilibre automatiquement (si activé)

**Avantages** :
- ✅ **Aucun downtime** pour les utilisateurs
- ✅ Intervention **transparente**
- ✅ Flexibilité planning (pas de contrainte horaire critique)

---

**Cas d'usage 2 : Migration vers nouveau SAN (Storage vMotion)**

**Scénario** :
- **Ancien SAN** : EMC VNX (5 ans, espace saturé 90%)
- **Nouveau SAN** : Pure Storage FlashArray (SSD full-flash)
- 50 VMs à migrer (total 15 To)
- Migration **sans arrêt service**

**Solution avec Storage vMotion** :

**Étape 1 : Ajouter nouveau datastore**
```
vSphere Client → Storage
→ New Datastore → VMFS
→ Sélectionner LUN Pure Storage
→ Nom : PURE_DATASTORE_01 (2 To)
```

**Étape 2 : Migrer VMs par lot**

**Lot 1 - VMs critiques** (pendant heures creuses) :
```
Sélectionner 10 VMs
→ Migrate... → Change storage only
→ Datastore cible : PURE_DATASTORE_01
→ Format disque : Thick Eager Zeroed (production)
→ Start Migration
```

**Durée** : 30-60 min par VM (selon taille disque et bande passante SAN)

**Lot 2 - VMs non critiques** (pendant journée) :
```
40 VMs restantes → Storage vMotion
→ Format : Thin Provisioning (économie espace)
```

**Étape 3 : Vérification**
```
VMs fonctionnent sur nouveau SAN Pure Storage
→ Performances améliorées (SSD vs HDD)
→ Latence divisée par 10 (de 15 ms → 1 ms)
```

**Étape 4 : Désactiver ancien SAN**
- Tous les fichiers VM migrés
- Datastore ancien SAN vide
- Démontage LUN

**Avantages** :
- ✅ **Migration sans downtime**
- ✅ **Conversion format disque** (optimisation)
- ✅ **Gain performances** (SSD)
- ✅ **Pas de fenêtre maintenance** requise

---

**Autres cas d'usage** :

**vMotion** :
- Load balancing automatique (DRS)
- Évacuation cluster pour mise à jour vSphere
- Migration vers nouveau datacenter

**Storage vMotion** :
- Résoudre saturation datastore
- Répartir charge I/O (SDRS)
- Migration LUN VMFS5 → VMFS6

---

**Barème** :
- Cas 1 (maintenance hôte avec vMotion) expliqué : **1 pt**
- Cas 2 (migration SAN avec Storage vMotion) expliqué : **1 pt**

---

### ✔️ Question D3 (5 points) - VMFS datastore

**a) Taille bloc VMFS6 + nombre hôtes** (2 pts) :

---

**Taille de bloc VMFS6**

**Réponse** : **1 Mo (1 mégaoctet)** ✅

**Explications** :

**Évolution versions VMFS** :

| Version | Taille bloc | Configurable ? | Taille max fichier | Année |
|---------|-------------|----------------|-------------------|-------|
| VMFS3 | 1/2/4/8 Mo | Oui | 2 To (bloc 8 Mo) | 2006 |
| VMFS5 | 1 Mo | **Non** (unifié) | 2 To | 2009 |
| VMFS6 | 1 Mo | **Non** (unifié) | 62 To | 2016 |

**VMFS6 - Améliorations** :
- Taille bloc **unifiée** : 1 Mo (simplicité)
- Taille max VM : **62 To** (vs 2 To VMFS5)
- Support **4K native** (Advanced Format disks)
- **Automatic space reclamation** (UNMAP automatique pour thin provisioning)
- **Moins de fragmentation**

**Pourquoi 1 Mo ?** :
- Compromis **performances / gaspillage espace**
- Fichiers < 1 Mo : Pas de perte espace (bloc partiel utilisé)
- Fichiers > 1 Mo : Allocation par blocs 1 Mo

---

**Nombre d'hôtes ESXi par datastore VMFS6**

**Réponse** : **64 hôtes maximum** ✅

**Évolution** :

| Version | Hôtes max par datastore | VMs max par datastore |
|---------|------------------------|----------------------|
| VMFS3 | 32 | Illimité (pratique ~100) |
| VMFS5 | 64 | Illimité (pratique ~200) |
| VMFS6 | **64** | Illimité (pratique ~500) |

**Recommandations** :
- Production : **8-16 hôtes** par datastore (répartir charge)
- Ne pas partager datastore entre **trop d'hôtes** (goulot I/O)
- Utiliser **plusieurs datastores** pour scalabilité

---

**Barème** :
- Taille bloc 1 Mo : **1 pt**
- 64 hôtes max : **1 pt**

---

**b) 2 méthodes d'expansion datastore** (3 pts) :

---

**Méthode 1 : Extend (Étendre le datastore existant)**

**Description** :
- **Augmenter la taille** d'un datastore VMFS existant
- Ajouter espace libre **sur le même LUN** OU **ajouter nouveau LUN**

**Prérequis** :
- LUN SAN avec **espace disponible** (agrandi côté baie SAN)
- Datastore **VMFS5 ou VMFS6**

---

**Scénario 1 : Étendre LUN existant (Expand)**

**Situation** :
- Datastore : **PROD_DS01** (1 To)
- LUN SAN : **LUN10** (1 To → agrandi à 2 To côté baie)
- Besoin : Utiliser le 1 To supplémentaire

**Étapes** :

**1. Rescan stockage** (détecter nouvelle taille LUN) :
```
vSphere Client → ESXi Host → Storage
→ Storage Adapters → iSCSI/FC Adapter
→ Clic droit → Rescan Storage
→ ☑ Scan for new storage devices
→ ☑ Scan for new VMFS volumes
→ OK
```

**2. Étendre datastore** :
```
Storage → PROD_DS01
→ Clic droit → Increase Datastore Capacity
→ Sélectionner option : "Expand an existing VMFS datastore extent"
→ Sélectionner : LUN10 (nouvelle taille détectée : 2 To)
→ Next → Finish
```

**Résultat** :
```
Datastore PROD_DS01 :
- Avant : 1 To (1 extent)
- Après : 2 To (1 extent agrandi)
```

**Avantages** :
- ✅ **Pas de downtime** (VMs continuent fonctionnement)
- ✅ **Transparent** (même datastore, même nom)
- ✅ **Simple** (1 seul extent)

---

**Scénario 2 : Ajouter nouveau LUN (Add Extent)**

**Situation** :
- Datastore : **PROD_DS01** (1 To - LUN10)
- Nouveau LUN : **LUN20** (1 To supplémentaire)
- Besoin : Agréger 2 LUNs dans 1 datastore

**Étapes** :

**1. Présenter nouveau LUN aux hôtes** :
```
Baie SAN → Créer LUN20 (1 To)
→ Masquer vers tous ESXi du cluster
```

**2. Rescan stockage** :
```
ESXi Hosts → Rescan Storage
```

**3. Ajouter extent** :
```
Storage → PROD_DS01
→ Increase Datastore Capacity
→ "Add an extent to existing VMFS datastore"
→ Sélectionner : LUN20 (1 To)
→ Finish
```

**Résultat** :
```
Datastore PROD_DS01 :
- Avant : 1 To (1 extent - LUN10)
- Après : 2 To (2 extents - LUN10 + LUN20)
```

**Avantages** :
- ✅ Utiliser **LUNs séparés** (flexibilité SAN)
- ✅ **Jusqu'à 64 extents** par datastore (64 To max théorique)

**Inconvénients** :
- ❌ **Striping** (données réparties sur extents)
- ❌ Si 1 LUN échoue → **Tout le datastore perdu**
- ❌ Complexité (plusieurs dépendances matérielles)

---

**Méthode 2 : Créer nouveau datastore**

**Description** :
- Créer un **datastore VMFS supplémentaire** sur nouveau LUN
- **Ne pas toucher** au datastore existant

**Situation** :
- Datastore : **PROD_DS01** (1 To - saturé)
- Nouveau LUN : **LUN30** (2 To)
- Besoin : Espace supplémentaire **sans toucher PROD_DS01**

**Étapes** :

**1. Présenter LUN30** :
```
Baie SAN → Créer LUN30 (2 To)
→ Masquer vers cluster ESXi
```

**2. Rescan** :
```
ESXi → Rescan Storage
```

**3. Créer datastore** :
```
vSphere Client → Storage
→ New Datastore...
→ Type : VMFS
→ Name : PROD_DS02
→ Sélectionner : LUN30 (2 To)
→ VMFS version : VMFS6
→ Partition configuration : Use full disk
→ Finish
```

**Résultat** :
```
2 datastores indépendants :
- PROD_DS01 : 1 To (ancien)
- PROD_DS02 : 2 To (nouveau)
```

**Avantages** :
- ✅ **Isolation** (panne LUN n'affecte qu'1 datastore)
- ✅ **Flexibilité** (migration sélective VMs avec Storage vMotion)
- ✅ **Simplicité** (1 datastore = 1 LUN)
- ✅ **Meilleure pratique VMware**

**Inconvénients** :
- ❌ Gestion **multiple datastores** (+ complexe)
- ❌ Nécessite **Storage vMotion** pour migrer VMs

**Actions post-création** :
```
Migrer VMs de PROD_DS01 → PROD_DS02 (Storage vMotion)
→ Libérer espace sur ancien datastore
→ Ou désactiver PROD_DS01 (si migration complète)
```

---

**Tableau comparatif** :

| Critère | Extend (Étendre) | Nouveau Datastore |
|---------|------------------|-------------------|
| **Complexité** | Simple | Simple |
| **Downtime** | Aucun | Aucun |
| **Extents** | 1-64 | 1 seul |
| **Isolation** | Faible (1 DS) | Élevée (DS séparés) |
| **Migration VMs** | Non requise | Storage vMotion |
| **Meilleure pratique** | Acceptable | **Recommandé** ✅ |

---

**Recommandations VMware** :

✅ **À faire** :
- **Nouveau datastore** si possible (isolation)
- Limiter à **2-3 extents** par datastore (si extend)
- Utiliser **LUNs dédiés** (pas de partage RAID groups)

❌ **À éviter** :
- Dépasser **32 extents** (performances dégradées)
- Mixer **LUNs lents + rapides** (HDD + SSD) sur même datastore

---

**Barème** :
- Méthode 1 (Extend) expliquée : **1.5 pt**
- Méthode 2 (Nouveau datastore) expliquée : **1.5 pt**

---

---

## 🔒 SECTION E : SÉCURITÉ & SAUVEGARDES (15 points)

### ✔️ Question E1 (5 points) - Stratégie GFS

**a) Expliquer stratégie GFS** (3 pts) :

**GFS (Grandfather-Father-Son)** :

**Définition** :
- Méthode de **rotation des sauvegardes** sur 3 niveaux
- **Optimise** le nombre de supports (bandes, disques)
- **Équilibre** entre rétention longue et coût stockage

---

**Les 3 niveaux** :

**Niveau 1 : Son (Fils) - Quotidien** :

- Sauvegarde **quotidienne** (lundi à vendredi)
- **5-7 supports** (1 par jour ouvré)
- **Rotation hebdomadaire** : Réutilisation après 7 jours

**Exemple** :
```
Lundi    → Bande "Lun"
Mardi    → Bande "Mar"
Mercredi → Bande "Mer"
Jeudi    → Bande "Jeu"
Vendredi → Bande "Ven"
(Samedi/Dimanche : Pas de sauvegarde ou bande "Weekend")

Lundi suivant → Réutilisation bande "Lun" (écrasement)
```

**Type sauvegarde** :
- **Incrémentielle** (uniquement modifications depuis veille)
- Rapide (30 min - 2h selon volume)

---

**Niveau 2 : Father (Père) - Hebdomadaire** :

- Sauvegarde **hebdomadaire** (vendredi soir ou samedi)
- **4-5 supports** (1 par semaine du mois)
- **Rotation mensuelle** : Réutilisation après 4-5 semaines

**Exemple** :
```
Semaine 1 (Ven) → Bande "W1" (Week 1)
Semaine 2 (Ven) → Bande "W2"
Semaine 3 (Ven) → Bande "W3"
Semaine 4 (Ven) → Bande "W4"
Semaine 5 (Ven) → Bande "W5" (si mois 5 semaines)

Semaine 6 → Réutilisation bande "W1"
```

**Type sauvegarde** :
- **Différentielle** (modifications depuis dernière complète)
- OU **Complète hebdomadaire** (recommandé)

---

**Niveau 3 : Grandfather (Grand-Père) - Mensuel** :

- Sauvegarde **mensuelle** (dernier vendredi du mois)
- **12 supports** (1 par mois de l'année)
- **Rotation annuelle** : Réutilisation après 12 mois

**Exemple** :
```
Janvier   → Bande "M01"
Février   → Bande "M02"
Mars      → Bande "M03"
...
Décembre  → Bande "M12"

Janvier suivant → Réutilisation bande "M01"
```

**Type sauvegarde** :
- **Complète** (full backup)
- Conservée **1 an minimum** (conformité légale)

---

**Niveau bonus : Annuel (optionnel)** :

- Sauvegarde **annuelle** (31 décembre)
- **7-10 supports** (conservation pluriannuelle)
- **Archivage** : Bandes stockées hors site (bunker)

**Exemple** :
```
2018 → Bande "Y2018"
2019 → Bande "Y2019"
2020 → Bande "Y2020"
...

Conservation : 7 ans (obligation légale comptabilité)
```

---

**Schéma rotation GFS** :

```
QUOTIDIEN (Son) :
Lun Mar Mer Jeu Ven [Sam Dim]
 ↓   ↓   ↓   ↓   ↓    (7 bandes)
Rotation 1 semaine

HEBDOMADAIRE (Father) :
W1  W2  W3  W4  [W5]
 ↓   ↓   ↓   ↓    (5 bandes)
Rotation 1 mois

MENSUEL (Grandfather) :
M01 M02 M03 ... M12
 ↓   ↓   ↓       ↓    (12 bandes)
Rotation 1 an

ANNUEL (optionnel) :
Y2018 Y2019 Y2020 ...
  ↓     ↓     ↓       (7-10 bandes)
Conservation pluriannuelle
```

---

**Total supports nécessaires** :
```
Quotidien : 5-7 bandes
Hebdomadaire : 4-5 bandes
Mensuel : 12 bandes
Annuel (opt) : 7-10 bandes

Total minimum : 21 bandes (5+4+12)
Total complet : 34 bandes (7+5+12+10)
```

---

**Avantages GFS** :

✅ **Rétention longue** (12 mois minimum)  
✅ **Optimisation coût** (réutilisation supports)  
✅ **Conformité légale** (archivage pluriannuel)  
✅ **Flexibilité restauration** (plusieurs points dans le temps)  
✅ **Standard industrie** (largement adopté)  

**Inconvénients** :

❌ **Gestion complexe** (rotation manuelle si pas d'automatisation)  
❌ **Nombreux supports** (stockage physique)  
❌ **Restauration** peut nécessiter plusieurs bandes (si incrémentielle)  

---

**Barème** :
- Niveaux Son/Father/Grandfather expliqués : **3 pts** (1 pt par niveau)

---

**b) Nombre de supports nécessaires** (2 pts) :

**Calcul minimal** :

**Quotidien (Son)** :
- 5 jours ouvrés (lundi-vendredi)
- **5 bandes minimum**

**Hebdomadaire (Father)** :
- 4 semaines par mois (moyenne)
- **4 bandes minimum**

**Mensuel (Grandfather)** :
- 12 mois par an
- **12 bandes**

---

**Total minimum** :
```
5 (quotidien) + 4 (hebdomadaire) + 12 (mensuel) = 21 bandes
```

**Réponse** : **21 supports minimum** ✅

---

**Calcul optimal** :

**Quotidien** : **7 bandes** (inclure weekend)  
**Hebdomadaire** : **5 bandes** (mois 5 semaines)  
**Mensuel** : **12 bandes**  
**Annuel** : **7 bandes** (archivage pluriannuel)  

**Total optimal** :
```
7 + 5 + 12 + 7 = 31 bandes
```

---

**Exemple réel entreprise** :

**MegaStore SARL** (85 employés) :
```
Données à sauvegarder : 5 To
Supports : Bandes LTO-7 (6 To chacune)

Quotidien : 7 bandes LTO-7
Hebdomadaire : 5 bandes LTO-7
Mensuel : 12 bandes LTO-7
Annuel : 7 bandes LTO-7

Total : 31 bandes × 200€ = 6200€ (investissement initial)

Durée vie bande : 30 ans (si bien stockée)
Réutilisations : ~200 cycles (quotidien), ~50 cycles (mensuel)
```

---

**Recommandations supplémentaires** :

**Bandes de secours** :
- Ajouter **2-3 bandes spare** (en cas de défaut)
- Total : **24-34 bandes** selon configuration

**Stockage hors site** :
- **Grandfather + Annuel** : Coffre banque ou datacenter distant
- Protection contre **sinistre site principal** (incendie, inondation)

**Règle 3-2-1** (combinée avec GFS) :
- **3 copies** des données (production + 2 backups)
- **2 supports différents** (disque + bande)
- **1 copie hors site** (bunker, cloud)

---

**Barème** :
- Calcul correct (21-31 bandes selon détail) : **2 pts**
- (1 pt si ordre de grandeur correct mais calcul inexact)

---

### ✔️ Question E2 (5 points) - Protection ransomware

**a) 3 mesures préventives** (3 pts) :

---

**Mesure 1 : Sauvegardes immutables hors ligne (Air-Gap)**

**Description** :
- Sauvegardes **déconnectées du réseau** après création
- **Impossible à chiffrer** par ransomware (pas d'accès)
- Support **physique isolé** ou **cloud avec verrouillage objet**

**Solutions** :

**Option 1 : Bandes LTO hors ligne** :
```
1. Sauvegarde quotidienne sur bande LTO
2. Éjection automatique bande
3. Stockage bande dans coffre-fort physique
4. Rotation GFS (21 bandes hors site)
```

**Avantages** :
- ✅ **Air-gap physique** (déconnecté)
- ✅ **Impossible à atteindre** par ransomware réseau
- ✅ **Conformité légale** (archivage longue durée)

**Option 2 : Sauvegarde cloud immutable** :
```
Veeam Backup & Replication → AWS S3
→ Activer "Object Lock" (WORM - Write Once Read Many)
→ Rétention : 30 jours immutables
```

**Avantages** :
- ✅ **Verrouillage objet** (impossible suppression/modification)
- ✅ **Hors site** (protection sinistre local)
- ✅ **Automatisé** (pas de manipulation bandes)

**Option 3 : Disque USB/NAS déconnecté** :
```
Script PowerShell :
1. Monter disque USB (robocopy sauvegarde)
2. Démontage automatique après backup
3. Déconnexion physique quotidienne
```

**Avantages** :
- ✅ **Simple et économique**
- ✅ **Restauration rapide** (pas de bandes à charger)

---

**Mesure 2 : Segmentation réseau et principe du moindre privilège**

**Description** :
- **Isoler serveurs critiques** dans VLANs séparés
- **Restreindre accès admin** (comptes privilégiés)
- **Désactiver protocoles inutiles** (SMBv1, RDP ouvert)

**Actions** :

**Segmentation réseau** :
```
VLAN 10 : Utilisateurs (192.168.10.0/24)
VLAN 20 : Serveurs applicatifs (192.168.20.0/24)
VLAN 30 : Serveurs backup (192.168.30.0/24) ← ISOLÉ
VLAN 40 : DMZ (10.0.0.0/24)

Firewall inter-VLAN :
- VLAN 10 → VLAN 30 : DENY (utilisateurs ne peuvent pas atteindre backup)
- VLAN 20 → VLAN 30 : ALLOW (serveurs peuvent sauvegarder)
```

**Avantages** :
- ✅ Ransomware sur PC utilisateur **ne peut pas chiffrer** serveurs backup
- ✅ **Limitation propagation** latérale

**Comptes privilégiés** :
```
1. Supprimer droits admin locaux utilisateurs
2. Comptes admin : Stations dédiées (PAW - Privileged Access Workstation)
3. MFA (Multi-Factor Authentication) obligatoire admins
4. Rotation mots de passe 60 jours (FGPP)
```

**Désactiver SMBv1** (vulnérable WannaCry) :
```powershell
# Windows Server
Disable-WindowsOptionalFeature -Online -FeatureName SMB1Protocol

# Vérifier
Get-SmbServerConfiguration | Select EnableSMB1Protocol
```

---

**Mesure 3 : Filtrage email et formation utilisateurs**

**Description** :
- **Bloquer pièces jointes dangereuses** (macros Office, .exe, .js)
- **Formation anti-phishing** (sensibilisation utilisateurs)
- **Sandboxing** (analyse pièces jointes en environnement isolé)

**Solutions** :

**Filtrage Exchange/Office 365** :
```
Exchange Admin Center → Protection
→ Stratégies anti-programme malveillant
→ Bloquer types fichiers :
   .exe, .bat, .cmd, .js, .vbs, .scr, .ps1, .docm, .xlsm
→ Quarantaine automatique (pas de remise)
```

**Sandbox ATP (Advanced Threat Protection)** :
```
Microsoft Defender for Office 365
→ Pièces jointes analysées dans VM isolée
→ Si malveillant : Bloqué
→ Si sain : Remis à destinataire (délai +5 min)
```

**Formation utilisateurs** :
```
Campagnes phishing simulées (KnowBe4, Proofpoint) :
- Email piège 1x/mois
- Utilisateurs cliquant → Formation obligatoire
- Réduction taux clic : 30% → 3% (en 6 mois)
```

**Règles détection comportementale** :
```
Veeam Backup → Alertes :
- Plus de 1000 fichiers modifiés en < 5 min → ALERTE
- Extensions suspectes (.encrypted, .locked) → ALERTE
- Suppression shadow copies → ALERTE
```

---

**Barème** :
- Mesure 1 (sauvegarde immutable) : **1 pt**
- Mesure 2 (segmentation réseau) : **1 pt**
- Mesure 3 (filtrage email/formation) : **1 pt**

---

**b) Utilité sauvegarde offline** (2 pts) :

**Réponse** : **Protection ultime contre chiffrement ransomware** ✅

---

**Justification** :

**Principe** :
- Sauvegarde **déconnectée physiquement** du réseau
- **Air-gap** (fossé numérique) = Isolation totale
- Ransomware **ne peut pas l'atteindre** (pas de chemin réseau)

---

**Pourquoi crucial ?** :

**Ransomwares modernes ciblent les sauvegardes** :
```
1. Infection initiale (phishing, RDP exposé)
2. Élévation privilèges (exploitation vulnérabilités)
3. Reconnaissance réseau (scan partages SMB, serveurs backup)
4. Chiffrement données production
5. PUIS chiffrement/suppression sauvegardes en ligne ← CRITIQUE !
6. Demande rançon (souvent 50-500 k€)
```

**Exemple réel - Ransomware Ryuk** :
- Cible **spécifiquement** :
  - Partages réseau backup (\\SERVEUR-NAS\Backup)
  - Snapshots VMware (suppression via API)
  - Veeam repositories en ligne
  - Shadow Copies Windows (`vssadmin delete shadows /all`)

**Sans backup offline** :
- ❌ Production chiffrée
- ❌ Sauvegardes en ligne chiffrées/supprimées
- ❌ **Aucune restauration possible**
- ❌ **Obligation payer rançon** (ou perte totale données)

**Avec backup offline** :
- ✅ Production chiffrée (mais restaurable)
- ✅ **Bandes/disques offline intacts** (ransomware ne peut pas les atteindre)
- ✅ **Restauration complète** possible (RTO 4-24h selon volume)
- ✅ **Aucune rançon payée**

---

**Mise en œuvre** :

**Solution 1 : Bandes LTO avec rotation manuelle** :
```
Script backup quotidien (2h00) :
1. Sauvegarde Veeam vers bande LTO
2. Éjection automatique bande (commande `mt offline`)
3. Technicien récupère bande le matin
4. Stockage coffre-fort ignifugé (bâtiment B)
5. Rotation GFS (21 bandes)
```

**Solution 2 : Disque USB déconnecté automatiquement** :
```powershell
# Script PowerShell (planifié 3h00)
$disk = Get-Disk | Where-Object {$_.FriendlyName -like "*Backup USB*"}
# Sauvegarde robocopy
robocopy D:\Production E:\ /MIR /Z
# Démontage et éjection logique
$disk | Set-Disk -IsOffline $true
# Message technicien : "Déconnectez disque USB E:"
```

**Solution 3 : Cloud avec Object Lock (AWS S3 Glacier)** :
```
Veeam → AWS S3 Bucket
→ Glacier Deep Archive (0.002$/Go/mois)
→ Object Lock WORM : 30 jours
→ Même admin backup ne peut PAS supprimer pendant 30 jours
```

---

**Règle 3-2-1-1-0** (évolution règle 3-2-1) :

```
3 : 3 copies des données (production + 2 backups)
2 : 2 supports différents (disque + bande)
1 : 1 copie hors site (datacenter distant)
1 : 1 copie OFFLINE (air-gap) ← CRUCIAL !
0 : 0 erreur (tests restauration réguliers)
```

---

**Tests réguliers** (indispensables) :

```
Exercice trimestriel :
1. Simuler attaque ransomware (VM test)
2. Restaurer depuis bande offline
3. Vérifier intégrité données
4. Mesurer RTO réel (temps restauration)

Objectif :
- RTO < 24h (données critiques)
- RPO < 24h (perte max 1 jour)
```

---

**Barème** :
- Utilité définie (protection contre chiffrement) : **1 pt**
- Justification détaillée (air-gap, impossibilité atteinte) : **1 pt**

---

### ✔️ Question E3 (5 points) - DMZ (Zone Démilitarisée)

**a) Définition et serveurs DMZ** (3 pts) :

**Définition DMZ** :

**DMZ (DeMilitarized Zone)** :
- Zone réseau **semi-sécurisée** entre Internet et réseau interne
- **Isolation** des serveurs accessibles depuis Internet
- **Double firewall** : Protection entrée (Internet) ET sortie (LAN interne)

**Objectif** :
- **Protéger réseau interne** (empêcher accès direct depuis Internet)
- **Exposer services publics** (web, mail) de manière contrôlée
- **Limitation dégâts** si compromission serveur DMZ

---

**Principe de fonctionnement** :

```
INTERNET
   ↓
[FIREWALL 1 - Externe]
   ↓
DMZ (Zone tampon)
- Serveurs publics exposés
- Isolation réseau interne
   ↓
[FIREWALL 2 - Interne]
   ↓
LAN INTERNE
- Serveurs critiques
- Postes utilisateurs
- Active Directory
```

**Règles firewall** :

**Firewall 1 (Externe - vers DMZ)** :
```
Internet → DMZ :
- Port 80/443 (HTTP/HTTPS) → Serveur Web → ALLOW
- Port 25 (SMTP) → Serveur Mail → ALLOW
- Tout le reste → DENY

DMZ → Internet :
- Ports 80/443 (mises à jour) → ALLOW
- DNS (port 53) → ALLOW
- Tout le reste → DENY
```

**Firewall 2 (Interne - DMZ ↔ LAN)** :
```
DMZ → LAN Interne :
- DENY par défaut (sauf flux autorisés spécifiques)
- Ex: Serveur Web DMZ → SQL Server LAN (port 1433) → ALLOW

LAN Interne → DMZ :
- Administration SSH/RDP depuis jump server → ALLOW
- Tout le reste → DENY
```

---

**Serveurs typiques en DMZ** :

**1. Serveur Web public (Apache/IIS/Nginx)**
- **Fonction** : Héberger site web entreprise accessible depuis Internet
- **Exposition** : Ports 80 (HTTP) et 443 (HTTPS)
- **Exemple** : www.megastore.fr

**2. Serveur Mail (SMTP/IMAP)**
- **Fonction** : Réception/envoi emails depuis/vers Internet
- **Exposition** : Ports 25 (SMTP), 587 (SMTP submit), 993 (IMAPS)
- **Exemple** : mail.megastore.fr

**3. Serveur FTP/SFTP**
- **Fonction** : Transfert fichiers avec partenaires externes
- **Exposition** : Ports 21 (FTP), 22 (SFTP), 989-990 (FTPS)
- **Exemple** : ftp.megastore.fr

**4. Serveur VPN (accès distant)**
- **Fonction** : Tunnel sécurisé pour employés nomades
- **Exposition** : Ports 1194 (OpenVPN), 500/4500 (IPsec), 443 (SSL VPN)
- **Exemple** : vpn.megastore.fr

**5. Reverse Proxy (nginx, HAProxy)**
- **Fonction** : Frontal qui redirige vers serveurs internes
- **Exposition** : Ports 80/443
- **Exemple** : proxy.megastore.fr

**6. Serveur DNS public (autoritaire)**
- **Fonction** : Résolution noms de domaine entreprise depuis Internet
- **Exposition** : Port 53 (UDP/TCP)
- **Exemple** : ns1.megastore.fr

---

**Barème** :
- Définition DMZ (zone isolation) : **1 pt**
- 2-3 serveurs typiques nommés : **2 pts** (0.5-1 pt par serveur)

---

**b) Schéma réseau DMZ** (2 pts) :

**Schéma architecture complète** :

```
┌─────────────────────────────────────────────────────────────┐
│                        INTERNET                             │
│                    (Réseau non fiable)                      │
└───────────────────────┬─────────────────────────────────────┘
                        │ IP Publique : 203.0.113.50
                        │
            ┌───────────▼───────────┐
            │   FIREWALL 1          │
            │   (Pare-feu externe)  │
            │   Cisco ASA / pfSense │
            │                       │
            │ Règles :              │
            │ - HTTP/HTTPS → DMZ OK │
            │ - SMTP → DMZ OK       │
            │ - SSH/RDP → DENY      │
            │ - Tout reste → DENY   │
            └───────────┬───────────┘
                        │
┌───────────────────────▼─────────────────────────────────────┐
│                      DMZ (Zone Démilitarisée)               │
│                   Réseau : 10.0.0.0/24                      │
│                                                             │
│  ┌──────────────────┐  ┌──────────────────┐                │
│  │ Serveur Web      │  │ Serveur Mail     │                │
│  │ Apache/Nginx     │  │ Postfix          │                │
│  │ 10.0.0.10        │  │ 10.0.0.20        │                │
│  │ www.megastore.fr │  │ mail.megastore.fr│                │
│  │ Ports : 80, 443  │  │ Ports : 25, 587  │                │
│  └──────────────────┘  └──────────────────┘                │
│                                                             │
│  ┌──────────────────┐  ┌──────────────────┐                │
│  │ Serveur VPN      │  │ Reverse Proxy    │                │
│  │ OpenVPN          │  │ HAProxy          │                │
│  │ 10.0.0.30        │  │ 10.0.0.40        │                │
│  │ vpn.megastore.fr │  │ proxy.megastore.fr│               │
│  │ Port : 1194      │  │ Ports : 80, 443  │                │
│  └──────────────────┘  └──────────────────┘                │
│                                                             │
└───────────────────────┬─────────────────────────────────────┘
                        │
            ┌───────────▼───────────┐
            │   FIREWALL 2          │
            │   (Pare-feu interne)  │
            │   Fortinet FortiGate  │
            │                       │
            │ Règles :              │
            │ - DMZ → LAN : DENY    │
            │   (sauf flux autorisés)│
            │ - Web DMZ → SQL LAN OK│
            │ - LAN → DMZ Admin OK  │
            └───────────┬───────────┘
                        │
┌───────────────────────▼─────────────────────────────────────┐
│                   LAN INTERNE (Sécurisé)                    │
│                  Réseau : 192.168.0.0/16                    │
│                                                             │
│  ┌──────────────────┐  ┌──────────────────┐                │
│  │ Active Directory │  │ SQL Server       │                │
│  │ SRV-DC01         │  │ SRV-SQL01        │                │
│  │ 192.168.10.10    │  │ 192.168.20.10    │                │
│  └──────────────────┘  └──────────────────┘                │
│                                                             │
│  ┌──────────────────┐  ┌──────────────────┐                │
│  │ Serveur Fichiers │  │ Serveur Backup   │                │
│  │ SRV-FILE01       │  │ SRV-BACKUP01     │                │
│  │ 192.168.30.10    │  │ 192.168.40.10    │                │
│  └──────────────────┘  └──────────────────┘                │
│                                                             │
│  ┌─────────────────────────────────────────┐               │
│  │ Postes Utilisateurs (85 PCs)            │               │
│  │ VLAN 10 : 192.168.10.0/24               │               │
│  └─────────────────────────────────────────┘               │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

---

**Flux réseau typiques** :

**1. Client Internet → Site Web** :
```
Client (Internet)
   ↓ HTTPS (443)
Firewall 1 (inspection, autorisation)
   ↓
Serveur Web DMZ (10.0.0.10)
   ↓ SQL (1433) - Si besoin données
Firewall 2 (autorisation spécifique)
   ↓
SQL Server LAN (192.168.20.10)
   ↓ Réponse données
Firewall 2
   ↓
Serveur Web DMZ
   ↓ Page HTML générée
Firewall 1
   ↓
Client (Internet)
```

**2. Email entrant depuis Internet** :
```
Serveur Email externe (Internet)
   ↓ SMTP (25)
Firewall 1
   ↓
Serveur Mail DMZ (10.0.0.20)
   ↓ Relay vers Exchange interne (si applicable)
Firewall 2 (flux autorisé)
   ↓
Exchange Server LAN (192.168.25.10)
   ↓ Distribution boîtes aux lettres
```

**3. Administrateur gère serveur DMZ** :
```
Poste Admin LAN (192.168.10.100)
   ↓ SSH (22) via jump server
Firewall 2 (autorisation depuis jump server uniquement)
   ↓
Serveur Web DMZ (10.0.0.10)
```

---

**Variantes architecture** :

**DMZ simple (1 firewall avec 3 zones)** :
```
       Internet
           ↓
    ┌──────┴──────┐
    │  FIREWALL   │
    │  3 interfaces:│
    │  - WAN (Internet)
    │  - DMZ
    │  - LAN
    └──────┬──────┘
       ┌───┴───┐
      DMZ     LAN
```

**Avantage** : Économique (1 seul firewall)  
**Inconvénient** : Moins sécurisé (1 point de défaillance)

---

**DMZ double (2 firewalls - recommandé)** :
```
Internet → Firewall 1 → DMZ → Firewall 2 → LAN
```

**Avantage** : Sécurité maximale (défense en profondeur)  
**Inconvénient** : Coût élevé (2 firewalls)

---

**Barème** :
- Schéma avec 3 zones (Internet, DMZ, LAN) : **1 pt**
- 2 firewalls positionnés correctement : **0.5 pt**
- Serveurs DMZ + flux indiqués : **0.5 pt**

---

---

## ✅ RÉCAPITULATIF SECTIONS D & E

**Section D - Virtualisation (15 points)** :
| Question | Sujet | Points |
|----------|-------|--------|
| D1 | Types disques (Thick Lazy/Eager, Thin) | 5 pts |
| D2 | vMotion vs Storage vMotion | 5 pts |
| D3 | VMFS6 (bloc 1Mo, 64 hôtes, expansion) | 5 pts |

**Section E - Sécurité (15 points)** :
| Question | Sujet | Points |
|----------|-------|--------|
| E1 | Stratégie GFS (21 bandes) | 5 pts |
| E2 | Ransomware (3 mesures, backup offline) | 5 pts |
| E3 | DMZ (définition, serveurs, schéma) | 5 pts |

**Total Partie 5 : 30 points**

---

**✅ FIN CORRECTIONS PARTIE 5**

**Suite dans PARTIE 6** : Section F (Anglais) + Récapitulatif final complet

---


---

# ✅ EXAMEN BLANC 2 - CORRECTIONS DÉTAILLÉES
## Partie 6 : Section F (Anglais) + Récapitulatif Final

---

## 🇬🇧 SECTION F : ANGLAIS TECHNIQUE (5 points)

### ✔️ Question F1 (5 points) - Post-Incident Report

**Texte anglais** :

```
POST-INCIDENT REPORT - Network Outage

Incident Date: November 10, 2025
Duration: 2 hours 15 minutes (14:30 - 16:45 UTC)
Severity: Critical
Affected Services: Internet connectivity, VPN access, email services

SUMMARY:
On November 10th at 14:30 UTC, our network operations center detected a complete
loss of Internet connectivity affecting all sites. The incident was caused by
a misconfigured BGP routing policy that was inadvertently pushed to our core
routers during a planned maintenance window.

ROOT CAUSE:
A junior network engineer applied an incorrect BGP route filter that resulted
in our network announcing a default route to our ISP, creating a routing loop.
This caused all outbound traffic to be redirected back into our internal
network, effectively cutting off all external communication.

RESOLUTION:
1. Emergency rollback of the BGP configuration (15 minutes)
2. Verification of routing tables across all edge routers (30 minutes)
3. Gradual restoration of services by priority (90 minutes)
4. Full network connectivity restored at 16:45 UTC

PREVENTIVE MEASURES:
- Implement mandatory peer review for all routing changes
- Deploy configuration validation tools before production deployment
- Enhanced monitoring alerts for BGP anomalies
- Comprehensive training for junior staff on BGP best practices

LESSONS LEARNED:
Change management procedures must be strictly enforced, especially during
maintenance windows. Automated testing environments should mirror production
configurations to catch errors before deployment.
```

---

**a) Traduction en français** (3 pts) :

**RAPPORT POST-INCIDENT - Panne Réseau**

**Date de l'incident** : 10 novembre 2025  
**Durée** : 2 heures 15 minutes (14h30 - 16h45 UTC)  
**Gravité** : Critique  
**Services affectés** : Connectivité Internet, accès VPN, services email  

**RÉSUMÉ** :
Le 10 novembre à 14h30 UTC, notre centre d'opérations réseau a détecté une perte complète de la connectivité Internet affectant tous les sites. L'incident a été causé par une politique de routage BGP mal configurée qui a été déployée par inadvertance sur nos routeurs cœur de réseau durant une fenêtre de maintenance planifiée.

**CAUSE RACINE** :
Un ingénieur réseau junior a appliqué un filtre de route BGP incorrect qui a eu pour résultat l'annonce d'une route par défaut par notre réseau vers notre fournisseur d'accès Internet (FAI), créant ainsi une boucle de routage. Cela a causé la redirection de tout le trafic sortant vers notre réseau interne, coupant effectivement toute communication externe.

**RÉSOLUTION** :
1. Retour arrière d'urgence de la configuration BGP (15 minutes)
2. Vérification des tables de routage sur tous les routeurs de bordure (30 minutes)
3. Restauration progressive des services par ordre de priorité (90 minutes)
4. Connectivité réseau complète restaurée à 16h45 UTC

**MESURES PRÉVENTIVES** :
- Mettre en place une revue par les pairs obligatoire pour tous les changements de routage
- Déployer des outils de validation de configuration avant déploiement en production
- Renforcer les alertes de surveillance pour les anomalies BGP
- Formation approfondie du personnel junior sur les meilleures pratiques BGP

**LEÇONS APPRISES** :
Les procédures de gestion du changement doivent être strictement appliquées, spécialement durant les fenêtres de maintenance. Les environnements de test automatisés doivent refléter les configurations de production pour détecter les erreurs avant le déploiement.

---

**Barème traduction** :
- Traduction fidèle et compréhensible : **3 pts**
- (2 pts si quelques erreurs mineures, 1 pt si traduction approximative)

---

**b) Résumé technique en français** (2 pts) :

**Résumé** :

**Incident** :
Panne réseau majeure le 10 novembre 2025 durant **2h15** (14h30-16h45 UTC), ayant coupé l'accès Internet, VPN et emails sur tous les sites.

**Cause** :
Erreur humaine lors d'une maintenance : un ingénieur junior a déployé un **filtre BGP incorrect** qui a créé une **boucle de routage**. Le réseau interne annonçait une route par défaut vers le FAI, redirigeant tout le trafic sortant vers l'intérieur, empêchant toute communication externe.

**Résolution** :
1. **Rollback immédiat** de la configuration BGP (15 min)
2. **Vérification** des tables de routage (30 min)
3. **Restauration progressive** des services prioritaires (90 min)
4. Retour à la normale après 2h15

**Mesures correctives** :
- **Validation obligatoire** (peer review) avant tout changement routage
- **Outils de test automatisés** avant production
- **Monitoring renforcé** BGP (alertes anomalies)
- **Formation approfondie** personnel junior sur BGP

**Leçon** :
Importance de la **gestion rigoureuse des changements** (change management) et de disposer d'**environnements de test** identiques à la production pour éviter ce type d'erreur critique.

---

**Points clés techniques** :

**BGP (Border Gateway Protocol)** :
- Protocole de routage Internet (AS - Autonomous Systems)
- Utilisé entre routeurs de différents opérateurs
- Erreur critique : **Route par défaut annoncée** = Tout l'Internet redirigé vers réseau interne !

**Boucle de routage** :
```
Internet → FAI → Routeur MegaStore (route par défaut annoncée)
                        ↓
                  Trafic renvoyé vers FAI
                        ↓
                    [BOUCLE INFINIE]
                        ↓
                  Saturation réseau
```

**Impact** :
- ❌ Plus d'accès Internet (navigation impossible)
- ❌ VPN inaccessible (télétravail bloqué)
- ❌ Emails en panne (Exchange Online injoignable)
- ❌ **85 employés** impactés pendant 2h15

**Coût estimé** :
```
85 employés × 2.25 heures × 35 €/heure (coût moyen) = 6 693 €
+ Perte image de marque
+ Clients e-commerce : Ventes perdues (si site web impacté)
```

---

**Barème résumé** :
- Résumé clair avec cause et résolution : **2 pts**
- (1 pt si résumé incomplet ou peu précis)

---

---

## 📊 RÉCAPITULATIF FINAL - EXAMEN BLANC 2

### 🎯 BARÈME DÉTAILLÉ PAR SECTION

#### **MSP ÉCRITE (100 points - 1h30)**

**Incident 1 : Problème résolution DNS** (30 points)
| Question | Sujet | Points |
|----------|-------|--------|
| 1.1 | Cause panne DNS (option DHCP 006 manquante) | 6 pts |
| 1.2 | Solution temporaire (config manuelle DNS) | 6 pts |
| 1.3 | Solution définitive (ajouter option 006 DHCP) | 8 pts |
| 1.4 | Tests validation (nslookup, ping FQDN) | 5 pts |
| 1.5 | Fichier hosts (rôle, limites) | 5 pts |

**Incident 2 : Script sauvegarde Bash** (30 points)
| Question | Sujet | Points |
|----------|-------|--------|
| 2.1 | Script complet tar.gz avec rotation 30 jours | 10 pts |
| 2.2 | Logging (fichier log avec date/résultat) | 5 pts |
| 2.3 | Notification email (mail/sendmail) | 5 pts |
| 2.4 | Vérification intégrité (MD5/SHA256) | 5 pts |
| 2.5 | Automatisation cron + permissions | 5 pts |

**Incident 3 : VM inaccessible - Snapshots VMware** (40 points)
| Question | Sujet | Points |
|----------|-------|--------|
| 3.1 | Analyse problème (chaîne 4 snapshots, 95% full) | 8 pts |
| 3.2 | Solutions d'urgence (suppression logs, Storage vMotion) | 8 pts |
| 3.3 | Consolidation snapshots (procédure + risques) | 10 pts |
| 3.4 | Actions préventives (monitoring, alertes, politique) | 8 pts |
| 3.5 | Architecture HA (DRS, Replication, Load Balancer) | 6 pts |

---

#### **QUESTIONNAIRE TECHNIQUE (100 points - 2h00)**

**Section A : Linux** (20 points)
| Question | Sujet | Points |
|----------|-------|--------|
| A1 | Commandes tar (compression/extraction) | 4 pts |
| A2 | Cron (analyse syntaxe 30 2 * * 1-5) | 4 pts |
| A3 | Gestion processus (top, ps, kill) | 4 pts |
| A4 | NFS montage (/etc/fstab) | 4 pts |
| A5 | Logs système (tail -f, grep -i) | 4 pts |

**Section B : Réseau** (25 points)
| Question | Sujet | Points |
|----------|-------|--------|
| B1 | Subnetting 10.20.0.0/22 → 4 sous-réseaux /24 | 5 pts |
| B2 | ACL Cisco HTTP/HTTPS serveur web | 5 pts |
| B3 | Routage inter-VLAN (SVI, ip routing) | 5 pts |
| B4 | Diagnostic réseau (ping/tracert/pathping) | 5 pts |
| B5 | NAT vs PAT (différence + exemple) | 4 pts |

**Section C : Windows/Active Directory** (25 points)
| Question | Sujet | Points |
|----------|-------|--------|
| C1 | PowerShell AD (Get-ADUser, Enable-ADAccount) | 5 pts |
| C2 | GPO sécurité (USB, verrouillage, MDP) + gpupdate | 5 pts |
| C3 | DNS (zones principale/secondaire, enregistrement A) | 5 pts |
| C4 | Sauvegarde (Windows Server Backup, wbadmin) | 5 pts |
| C5 | FGPP/PSO (stratégies MDP fines) | 5 pts |

**Section D : Virtualisation VMware** (15 points)
| Question | Sujet | Points |
|----------|-------|--------|
| D1 | Types disques (Thick Lazy/Eager, Thin) | 5 pts |
| D2 | vMotion vs Storage vMotion (différence + cas usage) | 5 pts |
| D3 | VMFS6 (bloc 1Mo, 64 hôtes, expansion datastore) | 5 pts |

**Section E : Sécurité & Sauvegardes** (15 points)
| Question | Sujet | Points |
|----------|-------|--------|
| E1 | Stratégie GFS (niveaux, 21 bandes) | 5 pts |
| E2 | Ransomware (3 mesures, backup offline) | 5 pts |
| E3 | DMZ (définition, serveurs, schéma) | 5 pts |

**Section F : Anglais Technique** (5 points)
| Question | Sujet | Points |
|----------|-------|--------|
| F1 | Post-Incident Report (traduction + résumé) | 5 pts |

---

### 📈 GRILLE DE NOTATION GLOBALE

**Total Examen Blanc 2 : 200 points**

| Tranche | Points | Résultat | Commentaire |
|---------|--------|----------|-------------|
| **180-200** | 90-100% | ⭐⭐⭐ Excellent | Maîtrise complète, prêt pour l'examen |
| **160-179** | 80-89% | ⭐⭐ Très bien | Solide, revoir quelques points spécifiques |
| **140-159** | 70-79% | ⭐ Bien | Niveau correct, approfondir certains domaines |
| **120-139** | 60-69% | ✅ Passable | **Seuil minimum**, renforcer faiblesses |
| **< 120** | < 60% | ❌ Insuffisant | Révisions approfondies nécessaires |

**Seuil de réussite examen officiel** : **120/200 (60%)**

---

### 🎯 RÉPARTITION PAR CCP

**Analyse couverture CCPs** :

| CCP | Intitulé | Questions | Points | % Examen |
|-----|----------|-----------|--------|----------|
| **CCP1** | Assister utilisateurs | Inc 1, A5 | 34 pts | 17% |
| **CCP2** | Maintenir infrastructures | Inc 1, 2, 3, A1-A5, B4 | 104 pts | 52% |
| **CCP3** | Déployer services réseau | B1-B5, C1-C5 | 54 pts | 27% |
| **CCP4** | Sécuriser infrastructures | E1-E3, C2 | 20 pts | 10% |
| **CCP5** | Virtualisation (transverse) | Inc 3, D1-D3 | 55 pts | 27.5% |

**Note** : Certaines questions couvrent plusieurs CCPs (scores cumulés > 200)

---

### ⏱️ GESTION DU TEMPS RECOMMANDÉE

**MSP Écrite (1h30 = 90 minutes)** :

| Incident | Temps recommandé | Points | Ratio temps/pts |
|----------|-----------------|--------|-----------------|
| Incident 1 (DNS) | 25 minutes | 30 pts | 50 sec/point |
| Incident 2 (Bash) | 30 minutes | 30 pts | 60 sec/point |
| Incident 3 (VMware) | 35 minutes | 40 pts | 52 sec/point |

**Questionnaire (2h00 = 120 minutes)** :

| Section | Temps recommandé | Points | Ratio temps/pts |
|---------|-----------------|--------|-----------------|
| A (Linux) | 20 minutes | 20 pts | 60 sec/point |
| B (Réseau) | 25 minutes | 25 pts | 60 sec/point |
| C (Windows/AD) | 25 minutes | 25 pts | 60 sec/point |
| D (Virtualisation) | 20 minutes | 15 pts | 80 sec/point |
| E (Sécurité) | 20 minutes | 15 pts | 80 sec/point |
| F (Anglais) | 10 minutes | 5 pts | 120 sec/point |

---

### 🔍 POINTS DE VIGILANCE PAR SECTION

**MSP - Incident 1 (DNS)** :
- ❗ Ne pas confondre **serveur DNS** et **option DHCP 006**
- ❗ Penser à tester avec **nslookup** et **ping FQDN** (pas juste IP)
- ❗ Expliquer **pourquoi** fichier hosts n'est pas scalable

**MSP - Incident 2 (Bash)** :
- ❗ Script doit être **exécutable** (chmod +x)
- ❗ Rotation : Utiliser `find` avec `-mtime +30` (pas `-mdate`)
- ❗ Cron : Format correct (5 champs, pas d'erreur syntaxe)
- ❗ Email : Vérifier MTA configuré (postfix/sendmail)

**MSP - Incident 3 (VMware)** :
- ❗ **NE JAMAIS** supprimer snapshots depuis datastore (corruption VM)
- ❗ Consolidation = **Risque downtime** (prévoir fenêtre maintenance)
- ❗ Monitoring : Script vérifier chaîne snapshots (pas juste espace)

**Section A (Linux)** :
- ❗ Tar : `-c` (create) vs `-x` (extract), ne pas confondre
- ❗ Cron : Astérisque `*` = "tous", pas "aucun"
- ❗ Kill : Envoyer `-15` (SIGTERM) avant `-9` (SIGKILL)

**Section B (Réseau)** :
- ❗ Subnetting : Vérifier bits empruntés (masque cohérent)
- ❗ ACL Cisco : Ordre important (permit AVANT deny)
- ❗ SVI : Ne pas oublier `ip routing` (sinon pas de routage inter-VLAN)

**Section C (Windows/AD)** :
- ❗ PowerShell : `-Filter` utilise **accolades** `{}`, pas parenthèses
- ❗ GPO : `gpupdate /force` pas `/f` (erreur courante)
- ❗ FGPP : Précédence **inverse** (10 < 20, donc 10 prioritaire)

**Section D (Virtualisation)** :
- ❗ Thick Eager = **Production** (pas Lazy)
- ❗ vMotion = **Hôte**, Storage vMotion = **Datastore** (ne pas confondre)
- ❗ VMFS6 = **1 Mo** (pas 1 Go)

**Section E (Sécurité)** :
- ❗ GFS : Calcul **21 bandes minimum** (5+4+12)
- ❗ Ransomware : **Air-gap** (offline) indispensable
- ❗ DMZ : **2 firewalls** (pas 1)

**Section F (Anglais)** :
- ❗ Traduire **fidèlement** (pas paraphraser excessivement)
- ❗ Vocabulaire technique : BGP, routing loop, rollback

---

### 📚 CONCEPTS CLÉS À RETENIR

**Top 20 commandes/notions incontournables** :

**Linux** :
1. `tar -czf` / `tar -xzf` (sauvegarde/restauration)
2. `crontab -e` (automatisation)
3. `chmod 700` / `chown` (permissions)
4. `/etc/fstab` (montages permanents)
5. `grep -i` / `tail -f` (analyse logs)

**Réseau** :
6. Subnetting CIDR (calcul masque, hôtes, réseau)
7. ACL Cisco (permit/deny, numéros 100-199)
8. `ip routing` (routage inter-VLAN)
9. NAT vs PAT (overload)
10. `nslookup` / `tracert` (diagnostic)

**Windows/AD** :
11. `Get-ADUser -Filter {}` (requête AD)
12. `gpupdate /force` (application GPO)
13. `Add-DnsServerResourceRecordA` (DNS)
14. `wbadmin start backup` (sauvegarde)
15. FGPP/PSO (précédence)

**Virtualisation** :
16. Thick Eager Zeroed (production)
17. vMotion (hôte) / Storage vMotion (datastore)
18. Snapshot consolidation (delete ≠ consolidate)
19. VMFS6 (1 Mo, 64 hôtes)
20. DRS/HA/vSphere Replication

**Sécurité** :
21. GFS (Son/Father/Grandfather)
22. Backup offline (air-gap anti-ransomware)
23. DMZ (double firewall)
24. Règle 3-2-1-1-0

---

### 🎓 CONSEILS STRATÉGIQUES EXAMEN

**Avant l'examen** :
1. ✅ Relire **toutes les corrections** Examen Blanc 1 et 2
2. ✅ Refaire **exercices subnetting** (vitesse)
3. ✅ Mémoriser **syntaxes commandes** (tar, cron, PowerShell, Cisco)
4. ✅ Réviser **vocabulaire anglais technique** (50 mots clés)
5. ✅ Dormir **8h** la veille (concentration maximale)

**Pendant MSP** :
1. ✅ Lire **3 incidents en entier** d'abord (5 min)
2. ✅ Commencer par **incident le plus facile** (confiance)
3. ✅ **Structurer réponses** (pas de pavés illisibles)
4. ✅ Donner **commandes exactes** (syntaxe correcte)
5. ✅ Si bloqué sur question : **Passer**, revenir après

**Pendant Questionnaire** :
1. ✅ **Lire consignes attentivement** (combien d'éléments demandés ?)
2. ✅ Répondre **précisément** (pas de hors-sujet)
3. ✅ Schémas : **Propres et légendés** (pas de gribouillis)
4. ✅ Gestion temps : **Vérifier toutes les 30 min**
5. ✅ Garder **10 min relecture** finale (fautes, oublis)

**Gestion stress** :
- 🧘 Respiration profonde si blocage (30 secondes)
- 💧 Boire eau régulièrement (hydratation cerveau)
- ⏰ Ne pas paniquer sur temps (respecter planning)
- ✍️ Écrire au brouillon si idées confuses (clarifier)

---

### 🏆 OBJECTIFS DE PERFORMANCE

**Score cible Examen Blanc 2** :

| Niveau | Score visé | Actions |
|--------|-----------|---------|
| **Débutant** | 120-139 pts | Valider bases, revoir sections < 60% |
| **Intermédiaire** | 140-159 pts | Approfondir points faibles, viser 70%+ partout |
| **Avancé** | 160-179 pts | Perfectionnement, cibler 85%+ |
| **Expert** | 180-200 pts | Maîtrise complète, mentorer autres apprenants |

---

### 📝 CHECKLIST RÉVISION POST-EXAMEN BLANC

**Après avoir passé l'Examen Blanc 2** :

**Étape 1 : Auto-correction** (1h)
- ☐ Comparer réponses avec corrections détaillées
- ☐ Noter score par section (tableur)
- ☐ Identifier **3 sections les plus faibles**

**Étape 2 : Analyse erreurs** (30 min)
- ☐ Classer erreurs :
  - ❌ **Connaissance manquante** (relire cours)
  - ❌ **Erreur d'inattention** (mieux lire consignes)
  - ❌ **Manque de temps** (améliorer vitesse)
  - ❌ **Syntaxe incorrecte** (mémoriser commandes)

**Étape 3 : Plan d'action** (15 min)
- ☐ Créer fiche **3 notions à réviser en priorité**
- ☐ Planifier **2h révision ciblée** (dans les 48h)
- ☐ Refaire **questions ratées** (dans 1 semaine)

**Étape 4 : Validation** (1 semaine après)
- ☐ Refaire **Examen Blanc 2** (complet ou sections faibles)
- ☐ Objectif : **+10-20 points** vs première tentative
- ☐ Si score > 160 pts : ✅ **Prêt pour examen officiel**

---

---

## ✅ CONCLUSION EXAMEN BLANC 2

**Objectifs pédagogiques atteints** :

✅ **Évaluation complète** des 9 CCPs TSSR  
✅ **Scénarios réalistes** (DNS, Bash, VMware, ransomware)  
✅ **Complexité progressive** (questions simples → complexes)  
✅ **Corrections détaillées** avec explications approfondies  
✅ **Barème transparent** (préparation notation officielle)  
✅ **Gestion du temps** (entraînement 3h30 chrono)  

---

**Différences Examen Blanc 1 vs 2** :

| Critère | Examen Blanc 1 | Examen Blanc 2 |
|---------|----------------|----------------|
| **MSP Incident 1** | DHCP (Windows) | DNS (DHCP option 006) |
| **MSP Incident 2** | PowerShell (AD) | Bash (sauvegarde tar) |
| **MSP Incident 3** | VLAN Cisco | VMware snapshots |
| **Section A** | 5Q Linux | 5Q Linux (différentes) |
| **Section B** | 5Q Réseau | 5Q Réseau (routage inter-VLAN) |
| **Section C** | 5Q Windows/AD | 5Q Windows/AD (FGPP) |
| **Section D** | 3Q Virtualisation | 3Q Virtualisation (disques, vMotion) |
| **Section E** | 3Q Sécurité | 3Q Sécurité (GFS, ransomware, DMZ) |
| **Section F** | 1Q Anglais | 1Q Anglais (incident BGP) |

**Complémentarité** : Les 2 examens blancs couvrent **100% du référentiel TSSR**

---

**Prochaines étapes préparation** :

1. ✅ **Corriger Examen Blanc 2** (comparer avec corrections)
2. ✅ **Identifier lacunes** (sections < 70%)
3. ✅ **Réviser fiches** concernées (6 fiches essentielles)
4. ✅ **Refaire QCMs** (250 questions disponibles)
5. ✅ **Simulation orale** (MSP pratique 45 min + Entretien 65 min)
6. ✅ **Examen officiel** : 17 novembre 2025 🎯

---

**Ressources disponibles** :

📁 **Dossier RevisionStructuree/** :
- ✅ Planning 5 jours
- ✅ 6 Fiches récapitulatives
- ✅ 6 QCMs (250 questions)
- ✅ 55 Questions ouvertes
- ✅ 20 Exercices subnetting
- ✅ MSP Pratique Linux
- ✅ **Examen Blanc 1** (5 fichiers)
- ✅ **Examen Blanc 2** (6 fichiers) ← VOUS ÊTES ICI

📌 **Manquant** :
- ⏳ Barème détaillé Examen Blanc 2 (fichier séparé)
- ⏳ Simulation orale complète

---

**Message de motivation** 💪 :

🎯 **Vous disposez maintenant de 2 examens blancs complets** avec corrections détaillées.  
📚 **400+ pages de contenu** pour réviser efficacement.  
⏰ **5 jours avant l'examen** : Utilisez ce temps à bon escient !  

**Stratégie gagnante** :
1. **Jour -5** : Examen Blanc 2 (ce fichier) + Corrections
2. **Jour -4** : Révision fiches + QCMs sections faibles
3. **Jour -3** : Refaire Examen Blanc 1 (validation progression)
4. **Jour -2** : Simulation orale + Exercices subnetting
5. **Jour -1** : Relecture fiches + Repos (pas de surcharge !)
6. **JOUR J** : **Confiance, méthode, réussite !** 🏆

---

**Vous êtes prêt(e) ! Bonne chance pour l'examen officiel ! 🍀**

---

**✅ FIN EXAMEN BLANC 2 - CORRECTIONS COMPLÈTES**

---

