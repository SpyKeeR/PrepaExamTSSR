# 📚 CCP9 - DÉPLOIEMENT DE POSTES - INDEX

> **Coefficient** : 10% 🟢  
> **Temps de révision estimé** : 3-4h  
> **Contenu** : 20 QCM + 6 Questions ouvertes

---

## 🎯 Objectifs du CCP9

Maîtriser le déploiement et la gestion de postes de travail :
- Méthodes de déploiement (WDS, MDT, SCCM, Autopilot)
- Images système (capture, personnalisation, déploiement)
- Fichiers de réponse (unattend.xml, Sysprep)
- PXE Boot et réseau de déploiement
- Gestion des pilotes
- Déploiement d'applications
- WSUS (Windows Server Update Services)
- Intune et gestion moderne (MDM)

---

## 📂 Organisation des ressources

```
CCP9-Deploiement-Postes/
│
├── Fiche-Revisions/
│   └── FICHE-CCP9-Deploiement-Postes.md           → Théorie complète
│
├── QCM/
│   ├── Evaluation/
│   │   └── QCM-CCP9-Deploiement-20Q-EVALUATION.md
│   └── Correction/
│       └── QCM-CCP9-Deploiement-20Q-CORRECTION.md
│
└── Questions-Ouvertes/
    ├── Evaluation/
    │   └── QUESTIONS-OUVERTES-CCP9-Deploiement-6Q-EVALUATION.md
    └── Correction/
        └── QUESTIONS-OUVERTES-CCP9-Deploiement-6Q-CORRECTION.md
```

---

## 📖 1. FICHE DE RÉVISION

### FICHE-CCP9-Deploiement-Postes.md
**Chemin** : `Fiche-Revisions/FICHE-CCP9-Deploiement-Postes.md`

**Contenu** :
- 🖥️ Méthodes de déploiement :
  - **WDS** (Windows Deployment Services) : Déploiement réseau PXE
  - **MDT** (Microsoft Deployment Toolkit) : Déploiement Zero Touch
  - **SCCM/ConfigMgr** : Gestion centralisée entreprise
  - **Autopilot** : Déploiement cloud Windows 10/11
- 💿 Images système :
  - Capture avec Sysprep
  - Formats : WIM (Windows Imaging), VHD
  - Personnalisation images
- 📄 Fichiers de réponse (unattend.xml) :
  - Configuration automatique installation
  - Composants (windowsPE, specialize, oobeSystem)
- 🌐 PXE Boot :
  - Démarrage réseau
  - DHCP et options 66/67
- 🔧 Gestion pilotes et applications
- 📦 WSUS : Gestion des mises à jour Windows
- 📱 Intune : MDM (Mobile Device Management) cloud

**Temps de lecture** : ⏱️ 45 min

**À faire** :
- [ ] Lire la fiche complète
- [ ] Comprendre différence WDS vs MDT vs SCCM
- [ ] Apprendre étapes Sysprep
- [ ] Mémoriser options DHCP pour PXE

---

## 📝 2. QCM (20 questions)

### QCM-CCP9-Deploiement-20Q-EVALUATION.md
**Chemin** : `QCM/Evaluation/QCM-CCP9-Deploiement-20Q-EVALUATION.md`

**Contenu** : 20 questions QCM

**À faire** :
- [ ] Faire le QCM sans aide
- [ ] Chronométrer (25 min)
- [ ] Noter tes réponses

**Thèmes couverts** :
- WDS et PXE (6 questions)
- Images et Sysprep (5 questions)
- MDT et SCCM (5 questions)
- WSUS et Autopilot (4 questions)

---

### QCM-CCP9-Deploiement-20Q-CORRECTION.md
**Chemin** : `QCM/Correction/QCM-CCP9-Deploiement-20Q-CORRECTION.md`

**Contenu** : Corrections détaillées

**À faire** :
- [ ] Corriger : Score = ___/20
- [ ] Revoir différences entre solutions
- [ ] Comprendre Sysprep

**Score cible** : 16/20 (80%) minimum

---

## 📋 3. QUESTIONS OUVERTES (6 questions)

### QUESTIONS-OUVERTES-CCP9-Deploiement-6Q-EVALUATION.md
**Chemin** : `Questions-Ouvertes/Evaluation/QUESTIONS-OUVERTES-CCP9-Deploiement-6Q-EVALUATION.md`

**Contenu** : 6 questions détaillées

**À faire** :
- [ ] Essayer de répondre seul
- [ ] Rédiger procédures complètes
- [ ] Expliquer choix de solutions

**Types de questions** :
- Configuration WDS
- Création image avec Sysprep
- Fichier unattend.xml
- Choix de solution de déploiement
- Architecture de déploiement
- WSUS et gestion updates

**Temps recommandé** : ⏱️ 45 min

---

### QUESTIONS-OUVERTES-CCP9-Deploiement-6Q-CORRECTION.md
**Chemin** : `Questions-Ouvertes/Correction/QUESTIONS-OUVERTES-CCP9-Deploiement-6Q-CORRECTION.md`

**Contenu** : Corrections détaillées

---

## 📊 SUIVI DE PROGRESSION

### Checklist globale

- [ ] Fiche lue
- [ ] QCM terminé : Score = ___/20 (___%)
- [ ] Questions ouvertes terminées
- [ ] WDS/MDT/SCCM compris
- [ ] Sysprep maîtrisé

---

## 💡 POINTS CLÉS À RETENIR

### Solutions de déploiement - Comparaison

| Solution | Complexité | Coût | Usage | Cloud |
|----------|------------|------|-------|-------|
| **WDS** | Faible | Gratuit | PME | Non |
| **MDT** | Moyenne | Gratuit | PME/ETI | Non |
| **SCCM** | Élevée | Payant | Entreprise | Hybride |
| **Autopilot** | Faible | Inclus M365 | Moderne | Oui |

### Sysprep - Étapes

1. **Installer** Windows sur machine de référence
2. **Personnaliser** (logiciels, paramètres)
3. **Exécuter Sysprep** :
   ```
   C:\Windows\System32\Sysprep\sysprep.exe /oobe /generalize /shutdown
   ```
   - `/oobe` : Mode Out-of-Box Experience
   - `/generalize` : Supprime infos spécifiques (SID, etc.)
   - `/shutdown` : Éteint après Sysprep
4. **Capturer image** (avec DISM ou WDS)
5. **Déployer** sur postes cibles

### PXE Boot - Configuration DHCP

**Options DHCP nécessaires** :
- **Option 66** : Adresse IP du serveur WDS
- **Option 67** : Nom du fichier de boot (`boot\x64\wdsnbp.com`)

### unattend.xml - Passes principales

| Pass | Phase | Utilisation |
|------|-------|-------------|
| **windowsPE** | Installation | Langue, clavier, partition disque |
| **specialize** | Configuration | Nom ordinateur, domaine, pilotes |
| **oobeSystem** | Première ouverture | Création utilisateur, paramètres |

### WSUS - Groupes de déploiement

```
WSUS
 ├── Test (10% machines) → Tester updates 1 semaine
 ├── Pilote (30%) → Déployer si OK
 └── Production (60%) → Déployer en dernier
```

---

## 🔗 NAVIGATION

- 📖 [README.md](../README.md)
- 📚 [INDEX-GENERAL.md](../INDEX-GENERAL.md)
- 📅 [PLANNING-REVISION-5-JOURS.md](../PLANNING-REVISION-5-JOURS.md)

---

**Commence maintenant** : [Fiche-Revisions/FICHE-CCP9-Deploiement-Postes.md](Fiche-Revisions/FICHE-CCP9-Deploiement-Postes.md)

**Bon courage ! 💻💪**
