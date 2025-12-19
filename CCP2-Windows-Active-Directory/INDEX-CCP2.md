# 📚 CCP2 - WINDOWS / ACTIVE DIRECTORY - INDEX

> **Coefficient** : 20% 🔥 **PRIORITÉ ABSOLUE**  
> **Temps de révision estimé** : 6-7h  
> **Contenu** : 30 QCM + 15 Questions ouvertes

---

## 🎯 Objectifs du CCP2

Maîtriser l'administration Windows Server et Active Directory :
- Architecture Active Directory (forêt, arbre, domaine, OU)
- Rôles FSMO (5 rôles critiques)
- Stratégies de groupe (GPO)
- DNS et zones (principale, secondaire, stub)
- Permissions NTFS et partages SMB
- PowerShell pour Active Directory
- Gestion des utilisateurs, groupes, OU
- Sauvegarde et restauration AD

---

## 📂 Organisation des ressources

```
CCP2-Windows-Active-Directory/
│
├── Fiche-Revisions/
│   └── FICHE-CCP4-Windows-AD-PowerShell.md        → Théorie complète
│
├── QCM/
│   ├── Evaluation/
│   │   └── QCM-Windows-ActiveDirectory-30Q-EVALUATION.md
│   └── Correction/
│       └── QCM-Windows-ActiveDirectory-30Q-CORRECTION.md
│
└── Questions-Ouvertes/
    ├── Evaluation/
    │   └── QUESTIONS-OUVERTES-CCP4-Windows-AD-15Q-EVALUATION.md
    └── Correction/
        └── QUESTIONS-OUVERTES-CCP4-Windows-AD-15Q-CORRECTION.md
```

---

## 📖 1. FICHE DE RÉVISION

### FICHE-CCP4-Windows-AD-PowerShell.md
**Chemin** : `Fiche-Revisions/FICHE-CCP4-Windows-AD-PowerShell.md`

**Contenu** :
- 🏛️ Architecture Active Directory (forêt, domaine, OU, objets)
- ⚙️ Rôles FSMO (5 rôles : Schema Master, Domain Naming, PDC, RID, Infrastructure)
- 📋 GPO (création, liaison, héritage LSDOU, Enforced/Block)
- 🌐 DNS (zones, enregistrements A/AAAA/CNAME/MX/PTR/SRV)
- 🔐 Permissions NTFS (lecture, écriture, contrôle total, héritage)
- 📁 Partages SMB (permissions réseau vs NTFS)
- 💻 PowerShell AD (New-ADUser, Get-ADUser, Set-ADUser, etc.)
- 👥 Groupes (Domain Local, Global, Universal - stratégie AGDLP)
- 💾 Sauvegarde AD (System State, Corbeille AD, restauration autoritaire)

**Temps de lecture** : ⏱️ 90-120 min

**À faire** :
- [ ] Lire la fiche complète (la plus importante !)
- [ ] Apprendre les 5 rôles FSMO par cœur
- [ ] Comprendre LSDOU (Local, Site, Domain, OU)
- [ ] Maîtriser les cmdlets PowerShell AD
- [ ] Comprendre la différence Domain Local vs Global

---

## 📝 2. QCM (30 questions)

### QCM-Windows-ActiveDirectory-30Q-EVALUATION.md
**Chemin** : `QCM/Evaluation/QCM-Windows-ActiveDirectory-30Q-EVALUATION.md`

**Contenu** : 30 questions QCM sans les réponses

**À faire** :
- [ ] Faire le QCM **sans regarder la correction**
- [ ] Chronométrer (40 min recommandées)
- [ ] Noter tes réponses

**Thèmes couverts** :
- Architecture AD (8 questions)
- FSMO et réplication (7 questions)
- GPO (6 questions)
- PowerShell AD (5 questions)
- DNS et permissions (4 questions)

---

### QCM-Windows-ActiveDirectory-30Q-CORRECTION.md
**Chemin** : `QCM/Correction/QCM-Windows-ActiveDirectory-30Q-CORRECTION.md`

**Contenu** : Corrections détaillées

**À faire** :
- [ ] Corriger ton QCM : Score = ___/30
- [ ] Revoir les rôles FSMO si erreurs
- [ ] Comprendre chaque cmdlet PowerShell

**Score cible** : 24/30 (80%) minimum

---

## 📋 3. QUESTIONS OUVERTES (15 questions)

### QUESTIONS-OUVERTES-CCP4-Windows-AD-15Q-EVALUATION.md
**Chemin** : `Questions-Ouvertes/Evaluation/QUESTIONS-OUVERTES-CCP4-Windows-AD-15Q-EVALUATION.md`

**Contenu** : 15 questions détaillées

**À faire** :
- [ ] Essayer de répondre seul
- [ ] Rédiger les scripts PowerShell complets
- [ ] Expliquer les concepts (FSMO, GPO, etc.)

**Types de questions** :
- Scripts PowerShell (création utilisateurs, groupes)
- Configuration GPO
- Architecture AD
- Résolution de problèmes FSMO
- DNS et réplication

**Temps recommandé** : ⏱️ 90 min

---

### QUESTIONS-OUVERTES-CCP4-Windows-AD-15Q-CORRECTION.md
**Chemin** : `Questions-Ouvertes/Correction/QUESTIONS-OUVERTES-CCP4-Windows-AD-15Q-CORRECTION.md`

**Contenu** : Corrections détaillées avec scripts complets

---

## 📊 SUIVI DE PROGRESSION

### Checklist globale

- [ ] Fiche lue et 5 rôles FSMO mémorisés
- [ ] QCM terminé : Score = ___/30 (___%)
- [ ] Questions ouvertes terminées
- [ ] PowerShell AD maîtrisé

---

## 💡 POINTS CLÉS À RETENIR PAR CŒUR

### 5 Rôles FSMO

| Rôle | Scope | Fonction |
|------|-------|----------|
| **Schema Master** | Forêt | Modifications du schéma AD |
| **Domain Naming Master** | Forêt | Ajout/suppression de domaines |
| **PDC Emulator** | Domaine | Synchronisation temps, MDP, compatibilité NT4 |
| **RID Master** | Domaine | Distribution des pools de RID (SID) |
| **Infrastructure Master** | Domaine | Références entre domaines |

### Cmdlets PowerShell essentielles

```powershell
# Utilisateurs
New-ADUser -Name "Jean Dupont" -SamAccountName "jdupont"
Get-ADUser -Filter * | Select Name, SamAccountName
Set-ADUser -Identity "jdupont" -Title "Directeur"
Remove-ADUser -Identity "jdupont"

# Groupes
New-ADGroup -Name "IT_Support" -GroupScope Global
Add-ADGroupMember -Identity "IT_Support" -Members "jdupont"

# OU
New-ADOrganizationalUnit -Name "Paris" -Path "DC=entreprise,DC=local"
```

### Ordre d'application des GPO : LSDOU

1. **L**ocal (GPO locale sur la machine)
2. **S**ite (GPO liée au site AD)
3. **D**omain (GPO liée au domaine)
4. **OU** (GPO liées aux OU, du parent vers l'enfant)

**Priorité** : La dernière appliquée (OU) a la priorité !

### Groupes : Stratégie AGDLP

- **A**ccounts (utilisateurs) → **G**lobal groups → **D**omain **L**ocal groups → **P**ermissions

---

## 🔗 NAVIGATION

- 📖 [README.md](../README.md)
- 📚 [INDEX-GENERAL.md](../INDEX-GENERAL.md)
- 📅 [PLANNING-REVISION-5-JOURS.md](../PLANNING-REVISION-5-JOURS.md)

---

**Commence maintenant** : [Fiche-Revisions/FICHE-CCP4-Windows-AD-PowerShell.md](Fiche-Revisions/FICHE-CCP4-Windows-AD-PowerShell.md)

**Bon courage ! 🪟💪**
