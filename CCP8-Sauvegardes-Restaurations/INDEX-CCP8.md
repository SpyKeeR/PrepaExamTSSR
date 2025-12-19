# 📚 CCP8 - SAUVEGARDES ET RESTAURATIONS - INDEX

> **Coefficient** : 10% 🟡  
> **Temps de révision estimé** : 3-4h  
> **Contenu** : 20 QCM + 15 Questions ouvertes

---

## 🎯 Objectifs du CCP8

Maîtriser les stratégies de sauvegarde et plans de continuité :
- Types de sauvegardes (complète, incrémentielle, différentielle)
- Stratégies de sauvegarde (GFS, 3-2-1)
- Solutions de sauvegarde (Veeam, Windows Server Backup)
- PRA (Plan de Reprise d'Activité)
- PCA (Plan de Continuité d'Activité)
- RPO et RTO (objectifs de restauration)
- Tests de restauration
- Supports de sauvegarde (disque, bande, cloud)

---

## 📂 Organisation des ressources

```
CCP8-Sauvegardes-Restaurations/
│
├── Fiche-Revisions/
│   └── FICHE-03-Sauvegardes-PRA-PCA.md            → Théorie complète
│
├── QCM/
│   ├── Evaluation/
│   │   └── QCM-CCP8-Sauvegardes-20Q-EVALUATION.md
│   └── Correction/
│       └── QCM-CCP8-Sauvegardes-20Q-CORRECTION.md
│
└── Questions-Ouvertes/
    ├── Evaluation/
    │   └── Questions-Ouvertes-Sauvegardes-15Q-EVALUATION.md
    └── Correction/
        └── Questions-Ouvertes-Sauvegardes-15Q-CORRECTION.md
```

---

## 📖 1. FICHE DE RÉVISION

### FICHE-03-Sauvegardes-PRA-PCA.md
**Chemin** : `Fiche-Revisions/FICHE-03-Sauvegardes-PRA-PCA.md`

**Contenu** :
- 💾 Types de sauvegardes :
  - **Complète** : Tout sauvegarder (long, lourd)
  - **Incrémentielle** : Changements depuis dernière sauvegarde (rapide)
  - **Différentielle** : Changements depuis dernière complète (moyen)
- 📅 Stratégies :
  - **GFS** (Grand-père, Père, Fils) : Quotidienne/Hebdo/Mensuelle
  - **3-2-1** : 3 copies, 2 supports différents, 1 hors site
- 🛠️ Solutions :
  - Veeam Backup & Replication
  - Windows Server Backup
  - Acronis, Commvault
- 📋 PRA vs PCA :
  - **PRA** : Reprise après sinistre majeur
  - **PCA** : Maintien activité pendant incident
- ⏱️ RPO et RTO :
  - **RPO** (Recovery Point Objective) : Perte de données acceptable
  - **RTO** (Recovery Time Objective) : Temps de restauration max
- ☁️ Supports : Disque, bande LTO, NAS, cloud (AWS, Azure)

**Temps de lecture** : ⏱️ 45 min

**À faire** :
- [ ] Lire la fiche complète
- [ ] Comprendre différence Incrémentielle vs Différentielle
- [ ] Apprendre stratégies GFS et 3-2-1
- [ ] Mémoriser RPO vs RTO

---

## 📝 2. QCM (20 questions)

### QCM-CCP8-Sauvegardes-20Q-EVALUATION.md
**Chemin** : `QCM/Evaluation/QCM-CCP8-Sauvegardes-20Q-EVALUATION.md`

**Contenu** : 20 questions QCM

**À faire** :
- [ ] Faire le QCM sans aide
- [ ] Chronométrer (25 min)
- [ ] Noter tes réponses

**Thèmes couverts** :
- Types de sauvegardes (6 questions)
- Stratégies (5 questions)
- PRA/PCA et RPO/RTO (5 questions)
- Solutions et supports (4 questions)

---

### QCM-CCP8-Sauvegardes-20Q-CORRECTION.md
**Chemin** : `QCM/Correction/QCM-CCP8-Sauvegardes-20Q-CORRECTION.md`

**Contenu** : Corrections détaillées

**À faire** :
- [ ] Corriger : Score = ___/20
- [ ] Revoir différences entre types
- [ ] Comprendre calcul RPO/RTO

**Score cible** : 16/20 (80%) minimum

---

## 📋 3. QUESTIONS OUVERTES (15 questions)

### Questions-Ouvertes-Sauvegardes-15Q-EVALUATION.md
**Chemin** : `Questions-Ouvertes/Evaluation/Questions-Ouvertes-Sauvegardes-15Q-EVALUATION.md`

**Contenu** : 15 questions détaillées

**À faire** :
- [ ] Essayer de répondre seul
- [ ] Rédiger des plans de sauvegarde complets
- [ ] Calculer RPO/RTO

**Types de questions** :
- Conception de stratégie de sauvegarde
- Calculs RPO/RTO
- Rédaction de PRA
- Choix de solutions
- Scénarios de restauration

**Temps recommandé** : ⏱️ 60 min

---

### Questions-Ouvertes-Sauvegardes-15Q-CORRECTION.md
**Chemin** : `Questions-Ouvertes/Correction/Questions-Ouvertes-Sauvegardes-15Q-CORRECTION.md`

**Contenu** : Corrections détaillées

---

## 📊 SUIVI DE PROGRESSION

### Checklist globale

- [ ] Fiche lue
- [ ] QCM terminé : Score = ___/20 (___%)
- [ ] Questions ouvertes terminées
- [ ] Types de sauvegardes maîtrisés
- [ ] RPO/RTO compris

---

## 💡 POINTS CLÉS À RETENIR

### Types de sauvegardes - Comparaison

| Type | Sauvegarde | Restauration | Espace | Vitesse |
|------|------------|--------------|--------|---------|
| **Complète** | Tout | Simple (1 jeu) | +++ | Lent |
| **Incrémentielle** | Changements | Complexe (N jeux) | + | Rapide |
| **Différentielle** | Depuis complète | Moyenne (2 jeux) | ++ | Moyen |

### Exemple de calendrier

**GFS (Grand-père, Père, Fils)** :
- **Fils** : Sauvegarde incrémentielle quotidienne (lundi-jeudi)
- **Père** : Sauvegarde complète hebdomadaire (vendredi)
- **Grand-père** : Sauvegarde complète mensuelle (dernier vendredi)

### Stratégie 3-2-1

- **3** copies de données (originale + 2 sauvegardes)
- **2** supports différents (ex: disque + bande)
- **1** copie hors site (cloud ou site distant)

### RPO vs RTO

```
Incident
   |
   v
   [Perte de données] <---- RPO (ex: 4h de données perdues)
   |
   v
   [Restauration en cours...]
   |                    <---- RTO (ex: 2h pour restaurer)
   v
Système opérationnel
```

**Exemple** :
- RPO = 4h → Tolérance perte de 4h de données
- RTO = 2h → Système doit être restauré en 2h max

### PRA vs PCA

| Aspect | PRA | PCA |
|--------|-----|-----|
| **Objectif** | Reprendre après sinistre | Maintenir activité |
| **Déclencheur** | Sinistre majeur | Incident localisé |
| **Durée** | Long terme | Court/moyen terme |
| **Exemple** | Datacenter détruit | Serveur en panne |

---

## 🔗 NAVIGATION

- 📖 [README.md](../README.md)
- 📚 [INDEX-GENERAL.md](../INDEX-GENERAL.md)
- 📅 [PLANNING-REVISION-5-JOURS.md](../PLANNING-REVISION-5-JOURS.md)

---

**Commence maintenant** : [Fiche-Revisions/FICHE-03-Sauvegardes-PRA-PCA.md](Fiche-Revisions/FICHE-03-Sauvegardes-PRA-PCA.md)

**Bon courage ! 💾💪**
