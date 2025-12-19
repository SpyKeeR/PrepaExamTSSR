# 🎤 SIMULATION ORALE TSSR - PARTIE 1 : PRÉSENTATION & MSP PRATIQUE

**Examen** : TSSR - Simulation Orale Complète  
**Durée totale épreuve orale** : 1h50 (110 minutes)  
**Coefficient** : Important (complète les 200 pts écrits)

---

## 📋 TABLE DES MATIÈRES PARTIE 1

1. [Vue d'ensemble de l'épreuve orale](#vue-ensemble)
2. [Préparation et conseils généraux](#preparation)
3. [MSP PRATIQUE - 3 Incidents (45 minutes)](#msp-pratique)
4. [Incident 1 : Accès partagé réseau bloqué](#incident1)
5. [Incident 2 : VM ne démarre plus après snapshot](#incident2)
6. [Incident 3 : Service web Apache inaccessible](#incident3)

---

<a name="vue-ensemble"></a>
## 🎯 VUE D'ENSEMBLE DE L'ÉPREUVE ORALE

### **Structure complète jour 2 examen**

```
┌─────────────────────────────────────────────────────────┐
│  JOUR 2 : ÉPREUVES ORALES ET PRATIQUES                  │
├─────────────────────────────────────────────────────────┤
│                                                          │
│  🔧 MSP PRATIQUE (45 min)                               │
│     - 3 incidents techniques réels                       │
│     - Sur infrastructure virtuelle (ESXi + VMs)          │
│     - Diagnostic + résolution en live                    │
│     - Jury observe et pose questions                     │
│                                                          │
│  ☕ PAUSE (10 min)                                       │
│                                                          │
│  💬 ENTRETIEN TECHNIQUE (45 min)                        │
│     - Questions orales sur tous les CCPs                 │
│     - Approfondissement dossier professionnel            │
│     - Cas pratiques théoriques                           │
│                                                          │
│  ☕ PAUSE (10 min)                                       │
│                                                          │
│  🎓 ENTRETIEN FINAL (20 min)                            │
│     - Présentation projet personnel                      │
│     - Motivation et perspectives                         │
│     - Questions ouvertes                                 │
│                                                          │
└─────────────────────────────────────────────────────────┘
```

### **Composition du jury**

| Rôle | Fonction | Focus évaluation |
|------|----------|------------------|
| **Président** | Professionnel secteur IT (5+ ans exp) | Vue globale compétences techniques |
| **Évaluateur 1** | Formateur/Expert technique | Profondeur connaissances CCPs |
| **Évaluateur 2** (optionnel) | RH ou Manager IT | Soft skills, communication |

---

### **Critères d'évaluation globaux**

| Critère | Poids | Description |
|---------|-------|-------------|
| **Compétences techniques** | 50% | Résolution incidents, précision réponses |
| **Méthodologie** | 20% | Approche structurée, diagnostic logique |
| **Communication** | 15% | Clarté, vulgarisation, écoute active |
| **Gestion stress** | 10% | Calme, réactivité, gestion temps |
| **Professionnalisme** | 5% | Attitude, présentation, ponctualité |

---

<a name="preparation"></a>
## 📝 PRÉPARATION ET CONSEILS GÉNÉRAUX

### **Avant l'épreuve (J-1)**

**Checklist matériel** :
- ☐ Pièce d'identité + convocation
- ☐ Dossier professionnel (2 exemplaires papier + 1 version numérique USB)
- ☐ CV actualisé (3 exemplaires)
- ☐ Projet personnel (support présentation : slides PDF, vidéo démo, schémas réseau)
- ☐ Bloc-notes + stylos (pour prendre notes pendant incidents)
- ☐ Bouteille d'eau

**Révisions finales** :
- ✅ Relire fiches récapitulatives (6 fiches)
- ✅ Revoir commandes essentielles (Linux, Cisco, PowerShell)
- ✅ Préparer présentation projet (5 slides max, 3 min)
- ✅ Simulation questions pièges (voir section Entretien Technique)
- ✅ Repos suffisant (7-8h sommeil)

---

### **Le jour J - Timing**

| Horaire | Action | Durée |
|---------|--------|-------|
| **8h30** | Arrivée centre examen | - |
| **9h00** | Accueil + installation poste | 15 min |
| **9h15** | Briefing jury + présentation infrastructure | 10 min |
| **9h25** | 🔧 **MSP PRATIQUE** | **45 min** |
| **10h10** | ☕ Pause | 10 min |
| **10h20** | 💬 **ENTRETIEN TECHNIQUE** | **45 min** |
| **11h05** | ☕ Pause | 10 min |
| **11h15** | 🎓 **ENTRETIEN FINAL** | **20 min** |
| **11h35** | Délibération jury (candidat sort) | 15 min |
| **11h50** | Annonce résultat + feedback | 10 min |

---

### **Conseils communication avec le jury**

✅ **À FAIRE** :
- Vouvoyer systématiquement le jury
- Reformuler questions si doute : *"Si je comprends bien, vous me demandez..."*
- Structurer réponses : *"Je vais répondre en 3 points..."*
- Avouer ignorance si nécessaire : *"Je ne connais pas cette technologie précise, mais je peux établir un parallèle avec..."*
- Montrer raisonnement même si pas la réponse : *"Ma démarche serait..."*
- Demander précisions si énoncé flou : *"Pour mieux cibler ma réponse, pourriez-vous préciser..."*

❌ **À ÉVITER** :
- Couper la parole au jury
- Réponses sèches "Oui/Non" sans développer
- Jargon excessif sans explication : *"FGPP PSO avec precedence 10"* → Expliquer
- Longs silences (> 10 sec) sans rien dire
- Justifications par *"On m'a dit que..."* → Préférer *"D'après mes recherches..."*
- Critique de l'énoncé : *"Cette question est bizarre"*

---

### **Gestion du stress**

**Techniques immédiates** (si panique pendant incident) :

1. **Respiration 4-7-8** :
   - Inspirer 4 secondes
   - Retenir 7 secondes
   - Expirer 8 secondes
   - Répéter 3 fois

2. **Verbalisation** :
   - Dire au jury : *"Je prends 30 secondes pour réfléchir à ma méthodologie"*
   - Écrire étapes diagnostic sur papier (organise pensée)

3. **Ancrage physique** :
   - Pieds bien posés au sol
   - Mains posées sur table (éviter tortiller stylo)

**Si blocage complet sur un incident** :
- Reconnaître : *"Je n'arrive pas à identifier la cause exacte, mais je propose..."*
- Proposer plan B : *"Si cette piste ne fonctionne pas, j'escaladerais vers..."*
- Passer à l'incident suivant (ne pas perdre 20 min sur 1 incident)

---

<a name="msp-pratique"></a>
## 🔧 MSP PRATIQUE (45 MINUTES)

### **Description infrastructure**

**Environnement virtuel fourni** :

```
┌────────────────────────────────────────────────────────┐
│  INFRASTRUCTURE MEGASTORE SARL                         │
│  (Même contexte Examen Blanc 2)                        │
├────────────────────────────────────────────────────────┤
│                                                        │
│  🖥️  SRV-DC01 (Windows Server 2022)                   │
│     - Active Directory (megastore.local)               │
│     - DNS, DHCP                                        │
│     - IP : 192.168.10.10                               │
│                                                        │
│  🖥️  SRV-FILE01 (Windows Server 2022)                 │
│     - Serveur de fichiers (partages réseau)            │
│     - IP : 192.168.10.20                               │
│                                                        │
│  🐧 SRV-WEB01 (Debian 12)                              │
│     - Apache 2.4, PHP 8.2                              │
│     - Site web intranet                                │
│     - IP : 192.168.10.30                               │
│                                                        │
│  💻 PC-CLIENT01 (Windows 10 Pro)                       │
│     - Poste utilisateur test                           │
│     - Membre domaine megastore.local                   │
│     - IP : DHCP (192.168.10.100)                       │
│                                                        │
│  🔀 SW-CORE01 (Cisco Catalyst 3750)                    │
│     - Switch cœur réseau                               │
│     - VLANs 10, 20, 30                                 │
│                                                        │
│  🌐 RTR-BORDER01 (Cisco ISR 4331)                      │
│     - Routeur accès Internet                           │
│     - NAT/PAT                                          │
│                                                        │
└────────────────────────────────────────────────────────┘
```

**Accès fournis** :
- Console ESXi (accès web vSphere Client)
- Console VMs (accès direct clavier/souris virtuels)
- Identifiants administrateur :
  - Windows : `Administrateur` / `P@ssw0rd2025!`
  - Linux : `root` / `P@ssw0rd2025!`
  - Cisco : `enable` / `Cisco2025!`

---

### **Déroulement MSP Pratique**

**Phase 1 : Présentation (5 minutes)**

Le jury explique :
1. Contexte entreprise (MegaStore SARL)
2. 3 incidents à résoudre (15 min par incident max)
3. Accès infrastructure virtuelle
4. Consignes : *"Verbalisez votre démarche, posez questions si besoin"*

**Phase 2 : Résolution incidents (40 minutes)**

| Étape | Temps | Actions candidat |
|-------|-------|------------------|
| **Lecture énoncé** | 2 min | Lire ticket, identifier symptômes |
| **Questions clarification** | 2 min | Poser max 3 questions au jury (joue rôle utilisateur) |
| **Diagnostic** | 5 min | Tests, logs, commandes vérification |
| **Hypothèse** | 1 min | Annoncer cause probable au jury |
| **Résolution** | 8 min | Appliquer correction |
| **Tests validation** | 2 min | Prouver résolution (capture écran, démo) |

**⏰ Gestion temps critique** :
- Incident 1 (facile) : **12 min max**
- Incident 2 (moyen) : **15 min max**
- Incident 3 (difficile) : **18 min max**
- Si dépassement : Jury interrompt et passe incident suivant

---

### **Matériel autorisé pendant MSP**

✅ **Autorisé** :
- Bloc-notes pour schématiser réseau, noter IPs
- Commandes `man`, `--help` sur les VMs
- Accès Google/documentation **SI DEMANDÉ AU JURY** (cas rare)

❌ **Interdit** :
- Smartphone personnel
- Communication extérieure
- Fiches de révision papier (doit être en mémoire)

---

---

<a name="incident1"></a>
## 🔴 INCIDENT 1 : ACCÈS PARTAGÉ RÉSEAU BLOQUÉ

### **Ticket d'incident**

```
┌────────────────────────────────────────────────────────┐
│  TICKET #2025-1147                                     │
├────────────────────────────────────────────────────────┤
│  Date : 12/11/2025 - 9h25                              │
│  Priorité : 🔴 HAUTE (10 utilisateurs impactés    		 │
│  Utilisateur : Marie LAMBERT (RH)                      │
│  Service : Ressources Humaines                         │
│                                                        │
│  SYMPTÔMES :                                           │
│  "Depuis ce matin, impossible d'accéder au dossier     │
│   partagé \\SRV-FILE01\RH_Paies sur le serveur.        │
│   Message d'erreur : 'Accès refusé'.                   │
│   Hier soir tout fonctionnait encore."                 │
│                                                        │
│  INFORMATIONS COMPLÉMENTAIRES :                        │
│  - Utilisatrice membre du groupe AD "GRP_RH"           │
│  - Partage accessible depuis 6 mois sans problème      │
│  - Aucune modification déclarée par utilisatrice       │
│  - Autres partages (\\SRV-FILE01\Commun) fonctionnent  │
│                                                        │
└────────────────────────────────────────────────────────┘
```

---

### **Informations contextuelles**

**Environnement technique** :
- Serveur : Windows Server 2022 (SRV-FILE01)
- Domaine : megastore.local
- Partage : `\\SRV-FILE01\RH_Paies`
- Dossier physique : `E:\Partages\RH_Paies`
- Groupe AD autorisé : `GRP_RH` (10 membres)

**Changement récent** (non communiqué initialement) :
- Hier soir 23h00 : Intervention technicien junior pour "nettoyer permissions"
- Possible modification accidentelle des ACL NTFS

---

### **Démarche attendue (15 minutes max)**

#### **Étape 1 : Collecte d'informations (2 min)**

**Questions à poser au jury** (max 3) :

1. *"D'autres utilisateurs du service RH rencontrent-ils le même problème ?"*
   - **Réponse jury** : *"Oui, tous les 10 membres du groupe GRP_RH sont impactés."*

2. *"Y a-t-il eu des interventions récentes sur ce serveur ?"*
   - **Réponse jury** : *"Oui, maintenance hier soir vers 23h par un technicien."*

3. *"L'utilisatrice arrive-t-elle à accéder à d'autres partages sur SRV-FILE01 ?"*
   - **Réponse jury** : *"Oui, le partage \\SRV-FILE01\Commun fonctionne."*

---

#### **Étape 2 : Tests initiaux (3 min)**

**Test 1 : Vérifier accès depuis PC-CLIENT01**

```powershell
# Se connecter au PC-CLIENT01 en tant que Marie LAMBERT
# Domaine\User : megastore\mlambert
# MDP : (fourni par jury)

# Tester accès partage
explorer.exe \\SRV-FILE01\RH_Paies
```

**Résultat attendu** :
```
❌ Erreur : "Vous n'avez pas les autorisations pour accéder à \\SRV-FILE01\RH_Paies"
```

---

**Test 2 : Vérifier connectivité réseau**

```powershell
# Depuis PC-CLIENT01
ping SRV-FILE01
# ✅ Réponse : Réponse de 192.168.10.20 : octets=32 temps<1ms TTL=128

Test-NetConnection -ComputerName SRV-FILE01 -Port 445
# ✅ TcpTestSucceeded : True (SMB fonctionne)
```

**Conclusion Test 2** : Problème **PAS réseau**, mais permissions.

---

**Test 3 : Vérifier appartenance groupe AD**

```powershell
# Vérifier que mlambert est bien dans GRP_RH
Get-ADUser -Identity mlambert -Properties MemberOf | Select-Object -ExpandProperty MemberOf
```

**Résultat attendu** :
```
CN=GRP_RH,OU=Groupes,DC=megastore,DC=local
CN=Domain Users,CN=Users,DC=megastore,DC=local
```

**Conclusion Test 3** : Utilisatrice bien dans groupe → Problème permissions partage/NTFS.

---

#### **Étape 3 : Diagnostic approfondi (5 min)**

**Se connecter à SRV-FILE01** (en Administrateur) :

```powershell
# Ouvrir session RDP ou console vSphere sur SRV-FILE01
```

---

**Vérification 1 : Permissions partage SMB**

```powershell
# Lister permissions partage
Get-SmbShareAccess -Name "RH_Paies"
```

**Résultat affiché** :
```
Name      AccountName             AccessControlType AccessRight
----      -----------             ----------------- -----------
RH_Paies  megastore\GRP_RH        Allow             FullControl
RH_Paies  Everyone                Allow             Read
```

**Analyse** : Permissions partage SMB **OK** (GRP_RH a FullControl).

---

**Vérification 2 : Permissions NTFS**

```powershell
# Afficher ACL NTFS du dossier
Get-Acl "E:\Partages\RH_Paies" | Format-List

# OU GUI : Clic droit dossier > Propriétés > Onglet "Sécurité"
```

**Résultat affiché** (GUI Propriétés Sécurité) :

| Groupe/Utilisateur | Permissions |
|-------------------|-------------|
| **Administrateurs** | Contrôle total ✅ |
| **SYSTEM** | Contrôle total ✅ |
| **Utilisateurs authentifiés** | ❌ **ABSENT** (devrait être là) |
| **GRP_RH** | ❌ **ABSENT** (problème identifié !) |

**🔍 CAUSE IDENTIFIÉE** :
Le groupe `GRP_RH` a été **supprimé des ACL NTFS** lors de la maintenance d'hier soir.

---

#### **Étape 4 : Annoncer hypothèse au jury (1 min)**

**Verbalisation** :

> *"J'ai identifié la cause : les permissions NTFS du dossier E:\Partages\RH_Paies ne contiennent plus le groupe GRP_RH. Les permissions partage SMB sont correctes, mais les ACL NTFS sont prioritaires. Lors de la maintenance d'hier soir, le technicien a probablement supprimé accidentellement cette entrée. Je vais restaurer les permissions NTFS pour GRP_RH avec Modification + Lecture."*

**Jury note** :
- ✅ Méthodologie structurée (tests réseau → SMB → NTFS)
- ✅ Cause correctement identifiée
- ✅ Explication claire

---

#### **Étape 5 : Résolution (4 min)**

**Méthode 1 : GUI (recommandé pour rapidité)**

1. Sur SRV-FILE01, ouvrir l'Explorateur Windows
2. Naviguer vers `E:\Partages\RH_Paies`
3. Clic droit > **Propriétés**
4. Onglet **Sécurité**
5. Clic **Modifier...**
6. Clic **Ajouter...**
7. Saisir : `GRP_RH` → **Vérifier les noms** → OK
8. Cocher permissions pour GRP_RH :
   - ✅ **Modification**
   - ✅ **Lecture et exécution**
   - ✅ **Affichage du contenu du dossier**
   - ✅ **Lecture**
   - ✅ **Écriture**
9. Clic **OK** → **Appliquer** → **OK**

**Temps estimé** : 2 minutes

---

**Méthode 2 : PowerShell (alternative si jury demande script)**

```powershell
# Définir chemin dossier
$path = "E:\Partages\RH_Paies"

# Récupérer ACL actuelle
$acl = Get-Acl $path

# Créer nouvelle règle NTFS pour GRP_RH
$rule = New-Object System.Security.AccessControl.FileSystemAccessRule(
    "megastore\GRP_RH",  # Identité
    "Modify",            # Droit (Modification)
    "ContainerInherit,ObjectInherit",  # Héritage sous-dossiers + fichiers
    "None",              # Pas de propagation spéciale
    "Allow"              # Autoriser
)

# Ajouter règle à l'ACL
$acl.SetAccessRule($rule)

# Appliquer ACL modifiée
Set-Acl $path $acl

# Vérification
Get-Acl $path | Format-List
```

**Temps estimé** : 3 minutes (si script pré-connu)

---

#### **Étape 6 : Tests validation (2 min)**

**Test 1 : Accès depuis PC-CLIENT01**

```powershell
# Retour sur PC-CLIENT01 (session mlambert)
# Fermer fenêtre explorateur précédente

# Renouveler ticket Kerberos (important après modif ACL)
klist purge

# Rouvrir partage
explorer.exe \\SRV-FILE01\RH_Paies
```

**Résultat attendu** :
```
✅ Le dossier s'ouvre avec liste fichiers visible
```

---

**Test 2 : Vérifier droits écriture**

```powershell
# Dans l'Explorateur ouvert sur \\SRV-FILE01\RH_Paies
# Clic droit > Nouveau > Document texte
# Nommer : "test_acces.txt"
```

**Résultat attendu** :
```
✅ Fichier créé sans erreur (droits Modification OK)
```

---

**Test 3 : Valider pour autre membre GRP_RH**

```powershell
# Se déconnecter de mlambert
# Se connecter avec un autre compte GRP_RH (ex: pdupont)
# Tester accès \\SRV-FILE01\RH_Paies
```

**Résultat attendu** :
```
✅ Accès OK pour tous les membres GRP_RH
```

---

#### **Étape 7 : Présentation solution au jury (1 min)**

**Verbalisation finale** :

> *"L'incident est résolu. J'ai rajouté le groupe GRP_RH dans les ACL NTFS du dossier E:\Partages\RH_Paies avec les droits Modification. J'ai validé l'accès depuis le poste de Marie Lambert et d'un autre utilisateur du service RH. Les 10 utilisateurs peuvent à nouveau accéder au partage et modifier les fichiers."*

**Actions préventives** (bonus si candidat le mentionne) :

> *"Pour éviter ce type d'incident :*
> - *Documenter toutes les interventions de maintenance (tickets avec validation)*
> - *Sauvegarder ACL avant modification : `Get-Acl | Export-Clixml backup.xml`*
> - *Former les techniciens juniors aux bonnes pratiques permissions NTFS"*

---

### **Grille d'évaluation Incident 1**

| Critère | Points | Obtenu |
|---------|--------|--------|
| **Méthodologie** (questions pertinentes, tests structurés) | 4 pts | _____ |
| **Diagnostic** (identification cause NTFS) | 3 pts | _____ |
| **Résolution** (ajout GRP_RH dans ACL) | 5 pts | _____ |
| **Validation** (tests accès multiples) | 2 pts | _____ |
| **Communication** (verbalisation claire) | 2 pts | _____ |
| **Gestion temps** (< 12 min) | 2 pts | _____ |
| **Bonus préventif** (suggestions amélioration) | +1 pt | _____ |
| **TOTAL INCIDENT 1** | **/18 pts** | **_____ /18** |

---

---

<a name="incident2"></a>
## 🟠 INCIDENT 2 : VM NE DÉMARRE PLUS APRÈS SNAPSHOT

### **Ticket d'incident**

```
┌────────────────────────────────────────────────────────┐
│  TICKET #2025-1148                                     │
├────────────────────────────────────────────────────────┤
│  Date : 12/11/2025 - 9h45                              │
│  Priorité : 🟠 MOYENNE (1 VM impactée)                 │
│  Demandeur : Service informatique (auto-généré)        │
│                                                        │
│  SYMPTÔMES :                                           │
│  La machine virtuelle SRV-WEB01 (Debian 12) refuse    │
│  de démarrer. Message vCenter :                        │
│  "Snapshot file not found"                             │
│                                                        │
│  CONTEXTE :                                            │
│  - Snapshot créé hier pour test mise à jour Apache     │
│  - Mise à jour effectuée, snapshot devait être         │
│    supprimé ce matin                                   │
│  - Après suppression snapshot via GUI, VM ne démarre   │
│    plus (état "Powered Off")                           │
│                                                        │
│  URGENCE :                                             │
│  Site web intranet inaccessible (85 employés)          │
│  Service RH utilise formulaires en ligne quotidiens    │
│                                                        │
└────────────────────────────────────────────────────────┘
```

---

### **Informations contextuelles**

**Environnement VMware** :
- Hyperviseur : ESXi 8.0 U2
- Datastore : DS-PROD01 (1 To, 75% utilisé)
- VM concernée :
  - Nom : SRV-WEB01
  - OS : Debian 12 Bookworm
  - vCPU : 2, RAM : 4 Go
  - Disques : 1× 40 Go (Thick Eager Zeroed)
  - IP : 192.168.10.30

**Historique snapshots** :
- Snapshot créé : 11/11/2025 - 17h00 (*"Avant update Apache 2.4.58"*)
- Taille snapshot : 8 Go
- Suppression tentée : 12/11/2025 - 9h30 via vSphere Client

---

### **Démarche attendue (15 minutes max)**

#### **Étape 1 : Questions clarification (2 min)**

**Questions au jury** :

1. *"Le snapshot a-t-il été correctement supprimé via vCenter, ou l'opération a-t-elle échoué ?"*
   - **Réponse jury** : *"L'opération semblait réussir (tâche 100% complétée), mais au redémarrage la VM affiche l'erreur."*

2. *"Y a-t-il eu des problèmes de performance ou saturation du datastore récemment ?"*
   - **Réponse jury** : *"Non, pas d'alerte. Datastore à 75%, espace suffisant."*

3. *"Avez-vous une sauvegarde récente de cette VM ?"*
   - **Réponse jury** : *"Oui, sauvegarde Veeam de ce matin à 3h00 (avant suppression snapshot)."*

---

#### **Étape 2 : Diagnostic initial (4 min)**

**Accès vSphere Client** (console web ESXi) :

1. Se connecter à `https://192.168.10.5` (ESXi)
2. Naviguer vers **Virtual Machines** → **SRV-WEB01**
3. Observer état VM :
   - **Power State** : ❌ Powered Off
   - **Status** : ⚠️ Warning icon

---

**Vérification 1 : Tâches récentes**

Clic onglet **Tasks** (tâches) de la VM :

| Heure | Tâche | État | Détails |
|-------|-------|------|---------|
| 9h30 | Delete snapshot | ✅ Success | Completed |
| 9h32 | Power On | ❌ Failed | *"File [DS-PROD01] SRV-WEB01/SRV-WEB01-000001.vmdk not found"* |

**🔍 Observation** : Suppression snapshot réussie, mais **fichier delta disk manquant**.

---

**Vérification 2 : Fichiers VM sur datastore**

1. vSphere Client → **Storage** → **DS-PROD01**
2. Naviguer vers dossier : `/vmfs/volumes/DS-PROD01/SRV-WEB01/`
3. Lister fichiers :

| Fichier | Taille | Type |
|---------|--------|------|
| `SRV-WEB01.vmx` | 5 Ko | Configuration VM |
| `SRV-WEB01.vmdk` | 1 Ko | Descripteur disque |
| `SRV-WEB01-flat.vmdk` | 40 Go | Disque virtuel principal |
| ~~`SRV-WEB01-000001.vmdk`~~ | ❌ **MANQUANT** | Delta disk snapshot |
| `SRV-WEB01-000001-delta.vmdk` | 8 Go | ⚠️ **Orphelin** (sans descripteur) |
| `vmware.log` | 120 Ko | Logs VM |

**🔍 CAUSE IDENTIFIÉE** :
Lors de la suppression snapshot, le fichier **descripteur** `SRV-WEB01-000001.vmdk` a été supprimé, mais le fichier **delta** `-delta.vmdk` est resté. Le fichier `.vmx` pointe encore vers le snapshot manquant.

---

**Vérification 3 : Contenu fichier .vmx**

```bash
# Depuis console ESXi (SSH activé)
cat /vmfs/volumes/DS-PROD01/SRV-WEB01/SRV-WEB01.vmx | grep scsi0:0
```

**Résultat** :
```
scsi0:0.fileName = "SRV-WEB01-000001.vmdk"  ❌ Pointe vers snapshot supprimé
```

**Devrait être** :
```
scsi0:0.fileName = "SRV-WEB01.vmdk"  ✅ Disque principal
```

---

#### **Étape 3 : Annoncer hypothèse au jury (1 min)**

**Verbalisation** :

> *"Le problème vient d'une suppression incomplète du snapshot. Le fichier de configuration .vmx pointe encore vers le snapshot SRV-WEB01-000001.vmdk qui a été supprimé. Je dois modifier le fichier .vmx pour qu'il pointe à nouveau vers le disque principal SRV-WEB01.vmdk. Il faudra également vérifier l'intégrité de la chaîne de disques avec vmkfstools."*

---

#### **Étape 4 : Résolution (6 min)**

**Action 1 : Éteindre VM (si pas déjà fait)**

```bash
# Déjà Powered Off, pas d'action nécessaire
```

---

**Action 2 : Activer SSH sur ESXi (si pas déjà fait)**

1. vSphere Client → **Host** (192.168.10.5)
2. **Actions** → **Services** → **Enable Secure Shell (SSH)**
3. Connexion SSH depuis PC candidat :

```bash
ssh root@192.168.10.5
# MDP : (fourni par jury)
```

---

**Action 3 : Naviguer vers dossier VM**

```bash
cd /vmfs/volumes/DS-PROD01/SRV-WEB01/
ls -lh
```

**Output** :
```
-rw-------  1 root root   40G Nov 12 09:30 SRV-WEB01-flat.vmdk
-rw-------  1 root root    1K Nov 12 09:30 SRV-WEB01.vmdk
-rw-------  1 root root    8G Nov 11 23:00 SRV-WEB01-000001-delta.vmdk  ⚠️ Orphelin
-rw-------  1 root root    5K Nov 12 09:30 SRV-WEB01.vmx
```

---

**Action 4 : Backup fichier .vmx**

```bash
cp SRV-WEB01.vmx SRV-WEB01.vmx.bak
```

---

**Action 5 : Éditer fichier .vmx**

```bash
vi SRV-WEB01.vmx
```

**Chercher ligne** (touche `/` dans vi) :
```
/scsi0:0.fileName
```

**Ligne actuelle** :
```
scsi0:0.fileName = "SRV-WEB01-000001.vmdk"
```

**Modifier en** (touche `i` pour Insert mode) :
```
scsi0:0.fileName = "SRV-WEB01.vmdk"
```

**Sauvegarder** (touche `Esc` puis `:wq`) :
```
:wq
```

---

**Action 6 : Supprimer fichier delta orphelin**

```bash
rm SRV-WEB01-000001-delta.vmdk
```

**⚠️ Attention** : Cette suppression est sûre car :
- Le snapshot a déjà été consolidé (tâche vCenter Success)
- Le fichier delta ne contient plus de données nécessaires
- Le disque principal `-flat.vmdk` contient toutes les données

---

**Action 7 : Recharger configuration VM dans vCenter**

```bash
# Depuis ESXi, supprimer VM de l'inventaire (pas les fichiers)
vim-cmd vmsvc/getallvms  # Lister VMs (trouver VMID)
```

**Output exemple** :
```
Vmid   Name          File                                Guest OS       Version
2      SRV-WEB01     [DS-PROD01] SRV-WEB01/SRV-WEB01.vmx debian10_64Guest vmx-19
```

**VMID = 2** (noter ce numéro)

```bash
# Désenregistrer VM (ne supprime PAS les fichiers)
vim-cmd vmsvc/unregister 2

# Réenregistrer VM avec .vmx modifié
vim-cmd solo/registervm /vmfs/volumes/DS-PROD01/SRV-WEB01/SRV-WEB01.vmx
```

**Output** :
```
3  (nouveau VMID)
```

---

#### **Étape 5 : Validation (2 min)**

**Test 1 : Démarrer VM**

Via vSphere Client :
1. Refresh liste VMs (F5)
2. Clic droit **SRV-WEB01** → **Power** → **Power On**

**Résultat attendu** :
```
✅ VM démarre sans erreur
✅ Debian 12 boot sequence visible dans console
✅ Login prompt affiché après 30 secondes
```

---

**Test 2 : Vérifier accessibilité service web**

Depuis PC-CLIENT01 :

```powershell
# Tester ping
ping 192.168.10.30
# ✅ Réponses OK

# Tester service HTTP
curl http://192.168.10.30
# ✅ Page HTML retournée

# Ouvrir navigateur
Start-Process "http://192.168.10.30"
# ✅ Site intranet accessible
```

---

**Test 3 : Vérifier absence snapshots résiduels**

vSphere Client :
1. Clic droit **SRV-WEB01** → **Snapshots** → **Manage Snapshots**
2. Vérifier liste : **"You do not have any snapshots"**

---

#### **Étape 6 : Présentation solution au jury (1 min)**

**Verbalisation finale** :

> *"L'incident est résolu. La suppression du snapshot avait laissé le fichier .vmx pointant vers un disque inexistant. J'ai modifié manuellement le fichier de configuration pour rediriger vers le disque principal, supprimé le fichier delta orphelin, et rechargé la VM dans l'inventaire. La VM démarre maintenant correctement et le site web est à nouveau accessible."*

**Actions préventives** (bonus) :

> *"Pour éviter ce type d'incident :*
> - *Toujours vérifier état VM après suppression snapshot (ne pas supposer succès)*
> - *Utiliser consolidation manuelle si doute : VM → Snapshot → Consolidate*
> - *Limiter durée de vie snapshots (max 72h, automatiser suppression)*
> - *Activer alertes vCenter sur échecs tâches snapshots"*

---

### **Grille d'évaluation Incident 2**

| Critère | Points | Obtenu |
|---------|--------|--------|
| **Diagnostic** (identification .vmx corrompu) | 4 pts | _____ |
| **Manipulation fichiers** (édition .vmx, suppression delta) | 5 pts | _____ |
| **Utilisation SSH/vi** (compétences Linux/ESXi) | 3 pts | _____ |
| **Validation** (tests démarrage + accessibilité web) | 2 pts | _____ |
| **Communication** (explication claire VMware internals) | 2 pts | _____ |
| **Gestion temps** (< 15 min) | 2 pts | _____ |
| **Bonus préventif** (alertes, consolidation) | +1 pt | _____ |
| **TOTAL INCIDENT 2** | **/18 pts** | **_____ /18** |

---

---

<a name="incident3"></a>
## 🔴 INCIDENT 3 : SERVICE WEB APACHE INACCESSIBLE

### **Ticket d'incident**

```
┌────────────────────────────────────────────────────────┐
│  TICKET #2025-1149                                     │
├────────────────────────────────────────────────────────┤
│  Date : 12/11/2025 - 10h05                             │
│  Priorité : 🔴 CRITIQUE (tout le site intranet down)   │
│  Demandeur : Direction (signalement multiple)          │
│                                                        │
│  SYMPTÔMES :                                           │
│  Site intranet http://intranet.megastore.local         │
│  totalement inaccessible depuis 20 minutes.            │
│  Message navigateur : "Ce site est inaccessible"       │
│                                                        │
│  CONTEXTE :                                            │
│  - Site fonctionnait ce matin jusqu'à 9h45             │
│  - Aucune maintenance planifiée                        │
│  - Serveur SRV-WEB01 (Debian 12) semble allumé        │
│    (répond au ping)                                    │
│  - Aucun autre service impacté                         │
│                                                        │
│  IMPACT :                                              │
│  - 85 employés ne peuvent pas accéder aux documents    │
│    partagés en ligne                                   │
│  - Formulaires RH bloqués (urgence paies fin de mois)  │
│                                                        │
└────────────────────────────────────────────────────────┘
```

---

### **Informations contextuelles**

**Configuration serveur web** :
- OS : Debian 12 Bookworm
- Serveur web : Apache 2.4.57
- Document root : `/var/www/html/intranet`
- Fichier config : `/etc/apache2/sites-available/intranet.conf`
- Port : 80 (HTTP standard)
- DNS : intranet.megastore.local → 192.168.10.30

**Changement récent** (non documenté) :
- Ce matin 9h40 : Un développeur a modifié le fichier `/etc/apache2/sites-available/intranet.conf` pour tester une nouvelle règle de réécriture d'URL (mod_rewrite)
- Redémarrage Apache effectué à 9h45
- Service ne redémarre plus depuis

---

### **Démarche attendue (18 minutes max)**

#### **Étape 1 : Questions clarification (2 min)**

**Questions au jury** :

1. *"Y a-t-il eu des modifications de configuration récentes sur ce serveur ?"*
   - **Réponse jury** : *"Un développeur a modifié la config Apache ce matin vers 9h40 pour tester un réglage."*

2. *"Le serveur SRV-WEB01 est-il accessible en SSH ?"*
   - **Réponse jury** : *"Oui, accès SSH disponible (root / P@ssw0rd2025!)."*

3. *"Avez-vous les logs d'erreur Apache disponibles ?"*
   - **Réponse jury** : *"Oui, tous les logs standards Debian présents."*

---

#### **Étape 2 : Tests connectivité (3 min)**

**Test 1 : Ping serveur**

Depuis PC-CLIENT01 :

```powershell
ping 192.168.10.30
```

**Résultat** :
```
✅ Réponse de 192.168.10.30 : octets=32 temps<1ms TTL=64
```

**Conclusion** : Serveur UP, problème pas matériel.

---

**Test 2 : Test port HTTP**

```powershell
Test-NetConnection -ComputerName 192.168.10.30 -Port 80
```

**Résultat** :
```
❌ TcpTestSucceeded : False
```

**Conclusion** : Port 80 **fermé** → Service Apache probablement arrêté.

---

**Test 3 : Résolution DNS**

```powershell
nslookup intranet.megastore.local
```

**Résultat** :
```
Serveur :   srv-dc01.megastore.local
Adresse:  192.168.10.10

Nom :    intranet.megastore.local
Adresse:  192.168.10.30
```

**Conclusion** : DNS OK, problème bien sur serveur web.

---

#### **Étape 3 : Diagnostic serveur web (8 min)**

**Connexion SSH à SRV-WEB01** :

```bash
ssh root@192.168.10.30
# MDP : P@ssw0rd2025!
```

---

**Vérification 1 : État service Apache**

```bash
systemctl status apache2
```

**Résultat** :
```
● apache2.service - The Apache HTTP Server
     Loaded: loaded (/lib/systemd/system/apache2.service; enabled; preset: enabled)
     Active: ❌ failed (Result: exit-code) since Tue 2025-11-12 09:45:32 CET; 20min ago
       Docs: https://httpd.apache.org/docs/2.4/
    Process: 2847 ExecStart=/usr/sbin/apachectl start (code=exited, status=1/FAILURE)
   Main PID: 2847 (code=exited, status=1/FAILURE)
        CPU: 45ms

Nov 12 09:45:32 srv-web01 systemd[1]: apache2.service: Failed with result 'exit-code'.
Nov 12 09:45:32 srv-web01 systemd[1]: Failed to start apache2.service - The Apache HTTP Server.
```

**🔍 Observation** : Service **failed**, ne démarre pas (erreur configuration probable).

---

**Vérification 2 : Logs erreur Apache**

```bash
tail -n 30 /var/log/apache2/error.log
```

**Résultat** :
```
[Tue Nov 12 09:45:32.374215 2025] [core:alert] [pid 2847] [client 192.168.10.30:80] 
/etc/apache2/sites-enabled/intranet.conf: Syntax error on line 15: 
Invalid command 'RewriteEngine', perhaps misspelled or defined by a module 
not included in the server configuration

[Tue Nov 12 09:45:32.375891 2025] [core:alert] [pid 2847] Configuration Failed
```

**🔍 CAUSE IDENTIFIÉE** :
Erreur syntaxe ligne 15 de `/etc/apache2/sites-enabled/intranet.conf` : commande `RewriteEngine` non reconnue → **Module mod_rewrite pas activé**.

---

**Vérification 3 : Contenu fichier config**

```bash
cat /etc/apache2/sites-available/intranet.conf
```

**Contenu** :
```apache
<VirtualHost *:80>
    ServerName intranet.megastore.local
    DocumentRoot /var/www/html/intranet

    <Directory /var/www/html/intranet>
        Options Indexes FollowSymLinks
        AllowOverride All
        Require all granted
    </Directory>

    ErrorLog ${APACHE_LOG_DIR}/intranet_error.log
    CustomLog ${APACHE_LOG_DIR}/intranet_access.log combined

    # Configuration ajoutée ce matin (développeur)
    RewriteEngine On  ← Ligne 15 (problème)
    RewriteRule ^/old-page$ /new-page [R=301,L]

</VirtualHost>
```

**Analyse** :
- Ligne 15 utilise `RewriteEngine` (directive mod_rewrite)
- Module mod_rewrite **pas activé** sur ce serveur

---

**Vérification 4 : Modules Apache activés**

```bash
apache2ctl -M | grep rewrite
```

**Résultat** :
```
(vide, aucun output)
```

**Conclusion** : Module `rewrite_module` **absent** → doit être activé.

---

#### **Étape 4 : Annoncer hypothèse au jury (1 min)**

**Verbalisation** :

> *"Le service Apache ne démarre pas car le fichier de configuration utilise la directive RewriteEngine du module mod_rewrite, mais ce module n'est pas activé sur le serveur. Le développeur a ajouté une règle de réécriture d'URL ce matin sans vérifier les modules actifs. Je vais activer mod_rewrite avec a2enmod et redémarrer Apache."*

---

#### **Étape 5 : Résolution (3 min)**

**Action 1 : Activer module mod_rewrite**

```bash
a2enmod rewrite
```

**Output** :
```
Enabling module rewrite.
To activate the new configuration, you need to run:
  systemctl restart apache2
```

---

**Action 2 : Vérifier syntaxe configuration (optionnel mais recommandé)**

```bash
apache2ctl configtest
```

**Output** :
```
Syntax OK  ✅
```

---

**Action 3 : Redémarrer Apache**

```bash
systemctl restart apache2
```

**Vérifier état** :
```bash
systemctl status apache2
```

**Résultat** :
```
● apache2.service - The Apache HTTP Server
     Loaded: loaded (/lib/systemd/system/apache2.service; enabled)
     Active: ✅ active (running) since Tue 2025-11-12 10:08:14 CET; 5s ago
       Docs: https://httpd.apache.org/docs/2.4/
   Main PID: 3124 (apache2)
      Tasks: 6 (limit: 4915)
     Memory: 12.5M
        CPU: 67ms
     CGroup: /system.slice/apache2.service
             ├─3124 /usr/sbin/apache2 -k start
             ├─3125 /usr/sbin/apache2 -k start
             └─3126 /usr/sbin/apache2 -k start

Nov 12 10:08:14 srv-web01 systemd[1]: Started apache2.service - The Apache HTTP Server.
```

**✅ Service RUNNING**

---

**Action 4 : Vérifier module chargé**

```bash
apache2ctl -M | grep rewrite
```

**Output** :
```
rewrite_module (shared)  ✅
```

---

#### **Étape 6 : Validation (2 min)**

**Test 1 : Vérifier port 80 ouvert**

Depuis PC-CLIENT01 :

```powershell
Test-NetConnection -ComputerName 192.168.10.30 -Port 80
```

**Résultat** :
```
✅ TcpTestSucceeded : True
```

---

**Test 2 : Accès site web (curl)**

```powershell
curl http://intranet.megastore.local
```

**Résultat** :
```html
<!DOCTYPE html>
<html>
<head><title>Intranet MegaStore</title></head>
<body>
    <h1>Bienvenue sur l'intranet</h1>
    ...
</body>
</html>
```

**✅ Site répond**

---

**Test 3 : Accès navigateur**

```powershell
Start-Process "http://intranet.megastore.local"
```

**Résultat** :
- ✅ Page d'accueil intranet s'affiche
- ✅ Liens de navigation fonctionnels
- ✅ Formulaires accessibles

---

**Test 4 : Vérifier règle réécriture (bonus)**

Tester l'URL `/old-page` (règle ajoutée par développeur) :

```powershell
curl -I http://intranet.megastore.local/old-page
```

**Résultat** :
```
HTTP/1.1 301 Moved Permanently
Location: http://intranet.megastore.local/new-page
```

**✅ Redirection 301 fonctionne** (mod_rewrite opérationnel)

---

#### **Étape 7 : Présentation solution au jury (1 min)**

**Verbalisation finale** :

> *"L'incident est résolu. Le service Apache était arrêté car le module mod_rewrite nécessaire pour les règles de réécriture d'URL n'était pas activé. J'ai activé le module avec la commande a2enmod rewrite, vérifié la syntaxe de la configuration, puis redémarré Apache. Le site intranet est à nouveau accessible pour les 85 employés."*

**Actions préventives** (bonus) :

> *"Pour éviter ce type d'incident :*
> - *Toujours tester les modifications de config Apache dans un environnement de dev avant prod*
> - *Utiliser `apache2ctl configtest` avant chaque redémarrage*
> - *Documenter les modules requis pour chaque VirtualHost (commentaire en haut du fichier)*
> - *Mettre en place monitoring uptime (ex: Centreon check_http toutes les 2 min)*
> - *Former développeurs aux commandes Apache de base (a2enmod, a2dissite, etc.)"*

---

### **Grille d'évaluation Incident 3**

| Critère | Points | Obtenu |
|---------|--------|--------|
| **Méthodologie** (tests connectivité → logs → config) | 4 pts | _____ |
| **Analyse logs** (identification mod_rewrite manquant) | 4 pts | _____ |
| **Résolution** (a2enmod + restart Apache) | 4 pts | _____ |
| **Validation** (tests multiples : port, curl, navigateur) | 3 pts | _____ |
| **Compétences Linux** (systemctl, apache2ctl, logs) | 3 pts | _____ |
| **Communication** (explication technique claire) | 2 pts | _____ |
| **Gestion temps** (< 18 min) | 2 pts | _____ |
| **Bonus préventif** (monitoring, procédure test) | +2 pts | _____ |
| **TOTAL INCIDENT 3** | **/22 pts** | **_____ /22** |

---

---

## 📊 RÉCAPITULATIF MSP PRATIQUE

### **Barème global (60 points max)**

| Incident | Difficulté | Points max | Temps max |
|----------|-----------|-----------|-----------|
| **Incident 1** : Accès partagé bloqué (NTFS) | ⭐⭐ Facile | 18 pts | 12 min |
| **Incident 2** : VM snapshot corrompu | ⭐⭐⭐ Moyen | 18 pts | 15 min |
| **Incident 3** : Apache module manquant | ⭐⭐⭐ Moyen | 22 pts | 18 min |
| **Bonus total** (actions préventives) | - | +4 pts | - |
| **TOTAL MSP PRATIQUE** | - | **60 pts** (58 + bonus) | **45 min** |

### **Compétences évaluées (CCPs)**

| CCP | Compétence | Incidents couverts |
|-----|------------|-------------------|
| **CCP1** | Assister utilisateurs | Incident 1 (support partage réseau) |
| **CCP2** | Maintenir infrastructures | Incident 3 (Apache, logs Linux) |
| **CCP3** | Services réseau | Incident 1 (DHCP, DNS indirectement) |
| **CCP4** | Sécuriser infras | Incident 1 (permissions NTFS) |
| **CCP5** | Virtualisation | Incident 2 (VMware snapshots) |

---

## ✅ FIN PARTIE 1 - MSP PRATIQUE

**Suite dans** :
- 📄 **SIMULATION-ORALE-PARTIE2.md** : Entretien Technique (45 min, questions orales tous CCPs)
- 📄 **SIMULATION-ORALE-PARTIE3.md** : Entretien Final + Grilles d'évaluation complètes

---
