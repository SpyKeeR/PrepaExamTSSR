# 📚 CCP5 - VIRTUALISATION VMWARE - INDEX

> **Coefficient** : 15% 🔥 **PRIORITÉ ABSOLUE**  
> **Temps de révision estimé** : 4-5h  
> **Contenu** : 40 QCM + 10 Questions ouvertes

---

## 🎯 Objectifs du CCP5

Maîtriser VMware vSphere et la virtualisation :
- Architecture vSphere (ESXi, vCenter, Datacenter, Cluster)
- vMotion et Storage vMotion
- vSphere HA (High Availability)
- vSphere DRS (Distributed Resource Scheduler)
- Snapshots (risques et bonnes pratiques)
- Datastores et stockage (SAN, NAS, VMFS, NFS)
- Réseau virtuel (vSwitch, dvSwitch, Port Groups)
- Resource Pools et réservations

---

## 📂 Organisation des ressources

```
CCP5-Virtualisation/
│
├── Fiche-Revisions/
│   └── FICHE-CCP5-VMware-vSphere.md               → Théorie complète
│
├── QCM/
│   ├── Evaluation/
│   │   └── QCM-Virtualisation-40Q-EVALUATION.md
│   └── Correction/
│       └── QCM-Virtualisation-40Q-CORRECTION.md
│
└── Questions-Ouvertes/
    ├── Evaluation/
    │   └── QUESTIONS-OUVERTES-CCP5-VMware-10Q-EVALUATION.md
    └── Correction/
        └── QUESTIONS-OUVERTES-CCP5-VMware-10Q-CORRECTION.md
```

---

## 📖 1. FICHE DE RÉVISION

### FICHE-CCP5-VMware-vSphere.md
**Chemin** : `Fiche-Revisions/FICHE-CCP5-VMware-vSphere.md`

**Contenu** :
- 🏗️ Architecture vSphere (ESXi, vCenter, Datacenter, Cluster)
- 🚀 vMotion (migration à chaud de VMs sans interruption)
- 💾 Storage vMotion (migration du stockage)
- 🔄 vSphere HA (redémarrage automatique en cas de panne)
- ⚖️ vSphere DRS (équilibrage de charge automatique)
- 📸 Snapshots (capture d'état, risques, consolidation)
- 💿 Datastores (VMFS, NFS, types de stockage)
- 🌐 Réseau virtuel (vSwitch standard, dvSwitch, port groups)
- 📊 Resource Pools (allocation CPU/RAM, réservations, limites)
- 🔧 EVC (Enhanced vMotion Compatibility)

**Temps de lecture** : ⏱️ 60-90 min

**À faire** :
- [ ] Lire la fiche complète
- [ ] Comprendre différence HA vs DRS
- [ ] Mémoriser prérequis vMotion
- [ ] Comprendre risques des snapshots
- [ ] Apprendre types de datastores

---

## 📝 2. QCM (40 questions)

### QCM-Virtualisation-40Q-EVALUATION.md
**Chemin** : `QCM/Evaluation/QCM-Virtualisation-40Q-EVALUATION.md`

**Contenu** : 40 questions QCM

**À faire** :
- [ ] Faire le QCM sans aide
- [ ] Chronométrer (50 min)
- [ ] Noter tes réponses

**Thèmes couverts** :
- Architecture vSphere (10 questions)
- vMotion et HA (10 questions)
- DRS et Resource Pools (8 questions)
- Snapshots et stockage (7 questions)
- Réseau virtuel (5 questions)

---

### QCM-Virtualisation-40Q-CORRECTION.md
**Chemin** : `QCM/Correction/QCM-Virtualisation-40Q-CORRECTION.md`

**Contenu** : Corrections détaillées

**À faire** :
- [ ] Corriger : Score = ___/40
- [ ] Revoir concepts HA vs DRS
- [ ] Comprendre prérequis vMotion

**Score cible** : 32/40 (80%) minimum

---

## 📋 3. QUESTIONS OUVERTES (10 questions)

### QUESTIONS-OUVERTES-CCP5-VMware-10Q-EVALUATION.md
**Chemin** : `Questions-Ouvertes/Evaluation/QUESTIONS-OUVERTES-CCP5-VMware-10Q-EVALUATION.md`

**Contenu** : 10 questions détaillées

**À faire** :
- [ ] Essayer de répondre seul
- [ ] Expliquer les concepts avec tes mots
- [ ] Dessiner des schémas si nécessaire

**Types de questions** :
- Configuration de clusters HA/DRS
- Résolution de problèmes vMotion
- Stratégies de snapshots
- Dimensionnement de datastores
- Architecture réseau virtuel

**Temps recommandé** : ⏱️ 60 min

---

### QUESTIONS-OUVERTES-CCP5-VMware-10Q-CORRECTION.md
**Chemin** : `Questions-Ouvertes/Correction/QUESTIONS-OUVERTES-CCP5-VMware-10Q-CORRECTION.md`

**Contenu** : Corrections détaillées

---

## 📊 SUIVI DE PROGRESSION

### Checklist globale

- [ ] Fiche lue
- [ ] QCM terminé : Score = ___/40 (___%)
- [ ] Questions ouvertes terminées
- [ ] HA vs DRS bien compris
- [ ] Prérequis vMotion mémorisés

---

## 💡 POINTS CLÉS À RETENIR

### vSphere HA vs DRS

| Aspect | HA (High Availability) | DRS (Distributed Resource Scheduler) |
|--------|------------------------|--------------------------------------|
| **Objectif** | Haute disponibilité | Équilibrage de charge |
| **Action** | Redémarre VMs en cas de panne | Migration vMotion automatique |
| **Déclencheur** | Panne d'hôte ESXi | Déséquilibre ressources |
| **Interruption** | Oui (redémarrage) | Non (migration à chaud) |
| **Type** | Réactif | Proactif |

### Prérequis vMotion

1. ✅ **Réseau vMotion** configuré sur tous les hôtes ESXi
2. ✅ **Datastores partagés** (SAN, NFS) accessibles par tous
3. ✅ **Compatibilité CPU** entre hôtes (ou EVC activé)
4. ✅ **Même nom de port group** sur tous les hôtes
5. ✅ **Licences** vSphere Standard minimum

### Risques des snapshots

- ⚠️ **Dégradation performances** : Chaîne de fichiers delta
- ⚠️ **Espace disque** : Peut remplir le datastore
- ⚠️ **Pas une sauvegarde** : Risque de corruption
- ⚠️ **Consolidation longue** : Peut prendre des heures

**Bonne pratique** : Ne jamais garder un snapshot > 72h

### Types de datastores

| Type | Protocole | Usage | Avantages |
|------|-----------|-------|-----------|
| **VMFS** | FC, iSCSI | Local/SAN | Performances élevées |
| **NFS** | NFS v3/v4 | NAS | Simple, flexible |
| **vSAN** | Interne | Hyperconvergé | Évolutif, local |

---

## 🔗 NAVIGATION

- 📖 [README.md](../README.md)
- 📚 [INDEX-GENERAL.md](../INDEX-GENERAL.md)
- 📅 [PLANNING-REVISION-5-JOURS.md](../PLANNING-REVISION-5-JOURS.md)

---

**Commence maintenant** : [Fiche-Revisions/FICHE-CCP5-VMware-vSphere.md](Fiche-Revisions/FICHE-CCP5-VMware-vSphere.md)

**Bon courage ! ☁️💪**
