## ✅ CORRECTIONS DÉTAILLÉES

### ✔️ Partie 1 : Active Directory - Fondamentaux

**Question 1 : B) Service d'annuaire centralisé**

Active Directory (AD DS - Active Directory Domain Services) :
- **Annuaire centralisé** : Base de données de tous les objets (users, computers, groups, OU)
- **Authentification/Autorisation** : Single Sign-On (SSO)
- **Gestion centralisée** : GPO, droits, politiques de sécurité
- **Standard** : Protocole LDAP (Lightweight Directory Access Protocol)

**Question 2 : B) Forêt → Arbre → Domaine → OU**

Structure hiérarchique :
```
Forêt (Forest) : entreprise.com
    ↓
Arbre 1 : entreprise.com
    ↓
Domaine : paris.entreprise.com
    ↓
OU : Utilisateurs
    ↓ OU : Comptabilité
    ↓ OU : IT
    ↓ OU : RH
```

**Définitions** :
- **Forêt** : Limite de sécurité, contient 1 ou plusieurs arbres, schéma commun
- **Arbre** : Hiérarchie de domaines avec namespace DNS continu
- **Domaine** : Limite de réplication, politique de sécurité commune
- **OU** : Conteneur logique pour appliquer GPO et déléguer administration

**Question 3 : B) Nom DNS (ex: entreprise.local)**

Exemples :
- `entreprise.local` (domaine privé)
- `ad.entreprise.com` (sous-domaine public)
- `corp.intranet.fr`

Format : **FQDN** (Fully Qualified Domain Name)

**Question 4 : B) 2 DC minimum (haute disponibilité)**

Bonnes pratiques :
- **Minimum production : 2 DC**
  - Redondance (si 1 tombe, l'autre prend le relais)
  - Répartition de charge
- **Recommandé : 2 DC + 1 RODC (Read-Only DC) en site distant**

Avantages multi-DC :
- Haute disponibilité
- Réplication automatique
- Répartition géographique

**Question 5 : B) Conteneur logique pour organiser et appliquer GPO**

OU (Organizational Unit) :
- **Organisation** : Structure logique (par service, site, fonction)
- **Application GPO** : Chaque OU peut avoir ses propres GPO
- **Délégation** : Confier l'administration d'une OU à un utilisateur

Exemple structure :
```
Domaine : entreprise.local
├─ OU: Paris
│  ├─ OU: Utilisateurs_Paris
│  ├─ OU: Ordinateurs_Paris
│  └─ OU: Serveurs_Paris
├─ OU: Lyon
│  ├─ OU: Utilisateurs_Lyon
│  └─ OU: Ordinateurs_Lyon
└─ OU: Groupes
```

**Question 6 : B) Global = membres du même domaine, Local = membres partout**

Types de groupes AD :

| Type | Membres | Utilisable où | Usage typique |
|------|---------|---------------|---------------|
| **Global** | Même domaine | Partout dans la forêt | Regrouper utilisateurs par fonction |
| **Universel** | Toute la forêt | Partout | Groupes inter-domaines |
| **Local de domaine** (Domain Local) | N'importe où | Domaine local uniquement | Donner permissions sur ressources |

**Règle AGDLP** (recommandée) :
- **A**ccounts (comptes) → **G**lobal groups → **D**omain **L**ocal groups → **P**ermissions

Exemple :
```
Users: Alice, Bob, Charlie
    ↓ membres de
Global Group: GG_Comptabilite
    ↓ membre de
Domain Local Group: DL_Partage_Compta_RW
    ↓ a les permissions
Partage: \\serveur\Compta (Read/Write)
```

**Question 7 : B) `New-ADUser`**

PowerShell Active Directory :

```powershell
# Créer un utilisateur
New-ADUser -Name "Jean Dupont" `
           -GivenName "Jean" `
           -Surname "Dupont" `
           -SamAccountName "jdupont" `
           -UserPrincipalName "jdupont@entreprise.local" `
           -Path "OU=Utilisateurs,DC=entreprise,DC=local" `
           -AccountPassword (ConvertTo-SecureString "P@ssw0rd123!" -AsPlainText -Force) `
           -Enabled $true

# Ajouter à un groupe
Add-ADGroupMember -Identity "GG_Comptabilite" -Members "jdupont"

# Lister les utilisateurs d'une OU
Get-ADUser -Filter * -SearchBase "OU=Utilisateurs,DC=entreprise,DC=local"
```

**Question 8 : B) Réinitialiser le mot de passe**

Procédures :

**Via GUI (Active Directory Users and Computers)** :
1. Ouvrir "Utilisateurs et ordinateurs Active Directory"
2. Clic droit sur l'utilisateur → "Réinitialiser le mot de passe"
3. Cocher "L'utilisateur doit changer le mot de passe à la prochaine ouverture de session"

**Via PowerShell** :
```powershell
Set-ADAccountPassword -Identity jdupont `
    -Reset `
    -NewPassword (ConvertTo-SecureString "NouveauP@ss123!" -AsPlainText -Force)

Set-ADUser -Identity jdupont -ChangePasswordAtLogon $true
```

**Question 9 : B) Nom de connexion format email (user@domaine.local)**

**UPN** (User Principal Name) :
- Format : `utilisateur@domaine.local`
- Utilisé pour la connexion (plus simple que DOMAINE\utilisateur)
- Exemple : `jdupont@entreprise.local`

**Vs SamAccountName** :
- SamAccountName : `jdupont` (nom court, rétro-compatibilité)
- UPN : `jdupont@entreprise.local` (moderne, recommandé)

**Question 10 : B) Base de données NTDS.dit**

Emplacements clés :
- **Base de données** : `C:\Windows\NTDS\ntds.dit` (tous les objets AD)
- **Logs** : `C:\Windows\NTDS\*.log` (transactions)
- **SYSVOL** : `C:\Windows\SYSVOL\` (GPO, scripts de connexion, réplication)

**Sauvegarde AD** :
- Windows Server Backup (System State)
- Sauvegarde `C:\Windows\NTDS\` (arrêt service AD requis)

**Question 11 : B) Synchronisation entre DC**

Réplication AD :
- **Automatique** : Modifications répliquées entre tous les DC
- **Multi-maître** : Modifications possibles sur n'importe quel DC
- **Convergence** : Tous les DC finissent par avoir les mêmes données

**Types** :
- **Intra-site** : Rapide (quelques minutes), compression désactivée
- **Inter-site** : Planifiée, compression activée

**Commandes** :
```powershell
# Forcer réplication
repadmin /replsummary
repadmin /syncall

# Vérifier réplication
dcdiag /test:replications
```

**Question 12 : C) Kerberos (port 88)**

Protocoles AD :

| Protocole | Port | Rôle |
|-----------|------|------|
| **Kerberos** | 88 TCP/UDP | Authentification (moderne) |
| **LDAP** | 389 TCP | Requêtes annuaire |
| **LDAPS** | 636 TCP | LDAP sécurisé (SSL) |
| **Global Catalog** | 3268 TCP | Catalogue global |
| **SMB** | 445 TCP | Partages, SYSVOL |
| **DNS** | 53 TCP/UDP | Résolution noms |
| **NTLM** | - | Authentification legacy (fallback) |

**Kerberos** :
- Standard depuis Windows 2000
- Authentification par tickets
- Plus sécurisé que NTLM

---

### ✔️ Partie 2 : GPO (Group Policy Objects)

**Question 13 : B) Ensemble de paramètres appliqués aux users/computers**

GPO permet de configurer :

**Configuration ordinateur** :
- Politiques de sécurité (mots de passe, pare-feu)
- Installation logiciels
- Scripts de démarrage
- Paramètres réseau

**Configuration utilisateur** :
- Fonds d'écran
- Mappage lecteurs réseau
- Restrictions (Panneau de configuration, CMD)
- Scripts d'ouverture de session

**Question 14 : C) Local → Site → Domaine → OU (LSDOU)**

Ordre d'application (acronyme **LSDOU**) :
1. **L**ocal (GPO locale de la machine)
2. **S**ite (GPO liée au site AD)
3. **D**omain (GPO liée au domaine)
4. **O**U (GPO liée à l'OU) ← **Plus prioritaire**

**Règle** : La dernière appliquée gagne (OU > Domaine > Site > Local)

**Question 15 : B) OU prioritaire (plus proche de l'objet)**

Exemple conflit :
- GPO Domaine : Fond d'écran = Bleu
- GPO OU Comptabilité : Fond d'écran = Rouge
- **Résultat** : Les users de l'OU Comptabilité ont fond d'écran **Rouge** (OU gagne)

**Exception** : Si GPO Domaine a option **"Enforced"** → Domaine gagne même face à OU

**Question 16 : B) `gpupdate /force`**

Commandes GPO :

```powershell
# Forcer mise à jour GPO immédiate
gpupdate /force

# Mise à jour seulement user
gpupdate /target:user

# Mise à jour seulement ordinateur
gpupdate /target:computer

# Voir les GPO appliquées
gpresult /r

# Rapport HTML détaillé
gpresult /h rapport.html
```

**Timing normal** :
- GPO ordinateur : Au démarrage + toutes les 90-120 min
- GPO utilisateur : À la connexion + toutes les 90-120 min

**Question 17 : B) Configuration utilisateur → Préférences → Mappage de lecteur**

Navigation GPO :
```
Configuration utilisateur
└─ Préférences
   └─ Paramètres Windows
      └─ Mappages de lecteurs
```

**Configuration** :
- Action : Créer
- Lettre : Z:
- Emplacement : `\\serveur\partage$`
- Se connecter en tant que : (laisser vide = user actuel)
- Étiquette : "Partage Comptabilité"

**Ciblage** : Possibilité de filtrer par groupe de sécurité

**Question 18 : B) Priorité absolue (non bloquable par OU enfants)**

Options GPO :

**Enforced (Appliqué)** :
- Force l'application, même si OU enfant a "Block Inheritance"
- Utilisé pour politiques de sécurité critiques
- Icône cadenas dans GPMC

**Block Inheritance (Bloquer l'héritage)** :
- Empêche les GPO parents de s'appliquer
- Mais **Enforced** passe quand même !

**Disabled (Désactivé)** :
- Totalement inactif

**Exemple** :
```
Domaine : GPO_Securite (Enforced) → Fond écran bleu
    ↓
OU IT : Block Inheritance + GPO_IT → Fond écran rouge
    ↓
Résultat : Fond écran BLEU (Enforced gagne)
```

---

### ✔️ Partie 3 : DNS Windows Server

**Question 19 : B) Résolution noms + Localisation DC (SRV records)**

DNS dans AD :
1. **Résolution de noms** : `serveur1.entreprise.local` → `192.168.1.10`
2. **Localisation DC** : Enregistrements SRV indiquent où sont les contrôleurs
3. **Localisation services** : LDAP, Kerberos, etc.

**Sans DNS fonctionnel = AD ne fonctionne pas !**

**Question 20 : A) Résolution Nom → IP**

Zone de recherche directe (Forward) :
- Type : Principale / Secondaire / Intégrée AD
- Contient : Enregistrements A, AAAA, CNAME, MX, SRV

Exemple zone `entreprise.local` :
```
serveur1    A       192.168.1.10
serveur2    A       192.168.1.11
dc01        A       192.168.1.5
www         CNAME   serveur1
mail        A       192.168.1.20
@           MX 10   mail
```

**Question 21 : B) Résolution IP → Nom (reverse)**

Zone de recherche inversée (Reverse) :
- Format : `1.168.192.in-addr.arpa` (pour 192.168.1.0/24)
- Contient : Enregistrements PTR

Exemple :
```
10.1.168.192.in-addr.arpa  PTR  serveur1.entreprise.local
11.1.168.192.in-addr.arpa  PTR  serveur2.entreprise.local
```

Usage :
- Logs (IP → nom lisible)
- Sécurité (vérification reverse DNS)
- Diagnostics

**Question 22 : C) A (IPv4) ou AAAA (IPv6)**

Types d'enregistrements DNS :

| Type | Rôle | Exemple |
|------|------|---------|
| **A** | Nom → IPv4 | `serveur1 A 192.168.1.10` |
| **AAAA** | Nom → IPv6 | `serveur1 AAAA 2001:db8::1` |
| **CNAME** | Alias | `www CNAME serveur1` |
| **MX** | Serveur mail | `@ MX 10 mail.entreprise.local` |
| **PTR** | IP → Nom (reverse) | `10 PTR serveur1.entreprise.local` |
| **SRV** | Localisation service | `_ldap._tcp SRV 0 100 389 dc01` |
| **NS** | Serveur DNS autoritaire | `@ NS dns1.entreprise.local` |
| **SOA** | Start of Authority | Infos zone (serial, refresh, etc.) |

**Question 23 : A) Alias (nom alternatif)**

CNAME (Canonical Name) :
- Pointe un nom vers un **autre nom** (pas une IP)
- Utile pour plusieurs noms vers même serveur

Exemple :
```
serveur1    A       192.168.1.10
www         CNAME   serveur1
intranet    CNAME   serveur1
```

Résolution :
```
www.entreprise.local
    ↓ CNAME
serveur1.entreprise.local
    ↓ A
192.168.1.10
```

**Limitation** : Ne peut pas être utilisé pour l'enregistrement racine (@)

**Question 24 : C) SRV (Service records)**

Enregistrements SRV AD :
```
_ldap._tcp.dc._msdcs.entreprise.local  SRV  0 100 389 dc01.entreprise.local
_kerberos._tcp.entreprise.local        SRV  0 100 88  dc01.entreprise.local
_gc._tcp.entreprise.local              SRV  0 100 3268 dc01.entreprise.local
```

Format SRV :
```
_service._protocole.domaine  SRV  priorité poids port cible
```

**Usage** : Les clients trouvent automatiquement les DC via ces enregistrements

**Commande test** :
```powershell
nslookup -type=srv _ldap._tcp.dc._msdcs.entreprise.local
```

---

### ✔️ Partie 4 : DHCP Windows Server

**Question 25 : B) Distribuer automatiquement les adresses IP**

DHCP (Dynamic Host Configuration Protocol) :
- **Automatisation** : Plus besoin de configurer IP manuellement
- **Gestion centralisée** : Toutes les configs depuis le serveur
- **Évite conflits IP** : Le serveur gère les attributions

**Question 26 : B) Plage d'adresses IP disponibles**

Scope (Étendue) DHCP :
- **Réseau** : 192.168.1.0/24
- **Plage de début** : 192.168.1.100
- **Plage de fin** : 192.168.1.200
- **Masque** : 255.255.255.0
- **Passerelle** : 192.168.1.1
- **DNS** : 192.168.1.5, 8.8.8.8
- **Durée bail** : 8 jours

**Question 27 : B) IP fixe via adresse MAC**

Réservation DHCP :
- **Principe** : Lier une adresse MAC à une IP spécifique
- **Usage** : Serveurs, imprimantes, caméras IP (besoin IP fixe mais gérée par DHCP)

Exemple :
```
Nom : Imprimante_Compta
Adresse IP : 192.168.1.150
Adresse MAC : 00-1A-2B-3C-4D-5E
```

**Avantages vs IP statique manuelle** :
- Gestion centralisée
- Options DHCP automatiques (DNS, passerelle)
- Changement facile

**Question 28 : A) Adresses exclues de la distribution**

Exclusion DHCP :
- **Scope** : 192.168.1.100 - 192.168.1.200
- **Exclusion** : 192.168.1.150 - 192.168.1.160
- **Résultat** : DHCP distribue 100-149 et 161-200 uniquement

**Usage** :
- Réserver plage pour IP statiques manuelles
- Éviter conflits avec serveurs configurés manuellement

**Question 29 : B) 8 jours (Windows standard)**

Durée de bail (Lease Duration) :
- **Par défaut** : 8 jours
- **Renouvellement** : Automatique à 50% du bail (4 jours)
- **Rébindage** : À 87.5% si renouvellement échoue

**Ajuster selon contexte** :
- **Réseau stable** (bureaux) : 8 jours OK
- **Réseau visiteurs** (WiFi public) : 1-4 heures
- **Réseau datacenter** : 1 jour

**Question 30 : B) IP + Masque + Passerelle + DNS + etc.**

Options DHCP standards :

| Code | Option | Exemple |
|------|--------|---------|
| 1 | Masque de sous-réseau | 255.255.255.0 |
| 3 | Passerelle par défaut | 192.168.1.1 |
| 6 | Serveurs DNS | 192.168.1.5, 8.8.8.8 |
| 15 | Nom de domaine | entreprise.local |
| 42 | Serveurs NTP | 192.168.1.5 |
| 44 | Serveurs WINS | 192.168.1.5 |
| 51 | Durée du bail | 691200 sec (8 jours) |
| 66 | Serveur TFTP | 192.168.1.50 (PXE boot) |

**Options personnalisées** possibles (déploiement, VoIP, etc.)

---

## 📊 BARÈME ET ÉVALUATION

**Nombre de bonnes réponses** : _____ / 30

| Score | Niveau | Commentaire |
|-------|--------|-------------|
| 27-30 | ⭐⭐⭐ Expert | Parfait ! Maîtrise AD/GPO/DNS/DHCP |
| 23-26 | ⭐⭐ Très bien | Bonnes connaissances, révise détails |
| 18-22 | ⭐ Bien | Relis cours Windows Server |
| 13-17 | 🟡 Moyen | Révision approfondie nécessaire |
| 0-12 | 🔴 Insuffisant | Revoir intégralement le module |

---

## 🎯 POINTS CLÉS À RETENIR

### Structure Active Directory
```
Forêt (limite sécurité, schéma commun)
  ↓
Arbre (namespace DNS continu)
  ↓
Domaine (limite réplication)
  ↓
OU (organisation logique, application GPO)
  ↓
Objets (Users, Computers, Groups)
```

### Types de groupes (règle AGDLP)
```
Accounts (Users)
  ↓
Global Groups (même domaine)
  ↓
Domain Local Groups (permissions)
  ↓
Permissions (sur ressources)
```

### Ordre application GPO : LSDOU
```
1. Local (machine)
2. Site
3. Domain
4. OU ← PRIORITAIRE
```

### FSMO Roles (5 rôles)
**Au niveau forêt (1 seul par forêt)** :
- **Schema Master** : Modifications du schéma AD
- **Domain Naming Master** : Ajout/suppression domaines

**Au niveau domaine (1 par domaine)** :
- **PDC Emulator** : Maître des horloges, changements de MDP prioritaires
- **RID Master** : Attribution des pools de SID (identifiants uniques)
- **Infrastructure Master** : Références entre domaines

**Commandes** :
```powershell
# Voir les FSMO
netdom query fsmo

# Transférer FSMO
Move-ADDirectoryServerOperationMasterRole -Identity "DC02" -OperationMasterRole PDCEmulator
```

### DNS - Types d'enregistrements
```
A      : Nom → IPv4
AAAA   : Nom → IPv6
CNAME  : Alias (nom → nom)
MX     : Serveur mail
PTR    : IP → Nom (reverse)
SRV    : Localisation services (DC, LDAP)
```

### DHCP - Composants
```
Scope (Étendue)
├─ Plage IP : 192.168.1.100-200
├─ Exclusions : 150-160 (non distribués)
├─ Réservations : MAC → IP fixe
└─ Options : DNS, Passerelle, etc.
```

---

## 📚 COMMANDES ESSENTIELLES

### PowerShell Active Directory
```powershell
# Utilisateurs
New-ADUser -Name "User" -SamAccountName "user" -Enabled $true
Set-ADAccountPassword -Identity "user" -Reset
Remove-ADUser -Identity "user"
Get-ADUser -Filter * -SearchBase "OU=Users,DC=dom,DC=local"

# Groupes
New-ADGroup -Name "GG_IT" -GroupScope Global
Add-ADGroupMember -Identity "GG_IT" -Members "user1","user2"
Get-ADGroupMember -Identity "GG_IT"

# Ordinateurs
New-ADComputer -Name "PC01" -Path "OU=Computers,DC=dom,DC=local"
Get-ADComputer -Filter * | Select Name

# GPO
Get-GPO -All
gpupdate /force
gpresult /r
```

### Outils graphiques
- **Active Directory Users and Computers** : `dsa.msc`
- **Active Directory Sites and Services** : `dssite.msc`
- **DNS Manager** : `dnsmgmt.msc`
- **DHCP Manager** : `dhcpmgmt.msc`
- **Group Policy Management** : `gpmc.msc`

---

**🎓 Maîtrise ces concepts pour l'examen TSSR ! Windows Server & AD sont au cœur du métier !**