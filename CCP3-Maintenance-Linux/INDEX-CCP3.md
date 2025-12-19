# 📚 CCP3 - MAINTENANCE LINUX - INDEX

> **Coefficient** : 10%  
> **Temps de révision estimé** : 5-6h  
> **Contenu** : 50 QCM + 20 Questions ouvertes + 1 MSP pratique

---

## 🎯 Objectifs du CCP3

Maîtriser l'administration système Linux :
- Navigation et gestion du système de fichiers
- Gestion des utilisateurs et permissions
- Gestion des processus et services
- Configuration réseau Linux
- Analyse des logs
- Gestion des paquets
- Configuration de services (Apache, SSH, NFS, Samba, Cron)

---

## 📂 Organisation des ressources

```
CCP3-Maintenance-Linux/
│
├── Fiche-Revision/
│   └── FICHE-01-Linux-Commandes-Essentielles.md    → Théorie complète
│
├── QCM/
│   ├── Evaluation/
│   │   └── QCM-Linux-Complet-50Q-EVALUATION.md     → 50 questions (sujet)
│   └── Correction/
│       └── QCM-Linux-CORRECTION.md                  → Corrections détaillées
│
├── Questions-Ouvertes/
│   ├── Evaluation/
│   │   └── Questions-Ouvertes-Linux-20Q-EVALUATION.md
│   └── Correction/
│       └── Questions-Ouvertes-Linux-20Q-CORRECTION.md
│
└── MSP/
    └── MSP-Pratique-Services-Linux.md               → Mise en situation pratique
```

---

## 📖 1. FICHE DE RÉVISION

### FICHE-01-Linux-Commandes-Essentielles.md
**Chemin** : `Fiche-Revision/FICHE-01-Linux-Commandes-Essentielles.md`

**Contenu** :
- 🗂️ Système de fichiers Linux (arborescence FHS)
- 📁 Commandes de navigation (cd, ls, pwd, find, locate)
- 👤 Gestion des utilisateurs (useradd, usermod, userdel, passwd)
- 🔐 Permissions (chmod, chown, chgrp, umask, ACL)
- ⚙️ Processus (ps, top, htop, kill, nice, renice)
- 🔧 Services (systemctl, service, journalctl)
- 🌐 Réseau (ip, ifconfig, netstat, ss, iptables, firewalld)
- 📦 Gestion de paquets (apt, yum, dpkg, rpm)
- 📄 Logs (/var/log, journalctl)
- ⏰ Cron et tâches planifiées

**Temps de lecture** : ⏱️ 60-90 min

**À faire** :
- [ ] Lire la fiche complète
- [ ] Prendre des notes manuscrites
- [ ] Surligner les commandes essentielles
- [ ] Tester les commandes dans une VM Linux

---

## 📝 2. QCM (50 questions)

### QCM-Linux-Complet-50Q-EVALUATION.md
**Chemin** : `QCM/Evaluation/QCM-Linux-Complet-50Q-EVALUATION.md`

**Contenu** : 50 questions QCM sans les réponses

**À faire** :
- [ ] Faire le QCM **sans regarder la correction**
- [ ] Chronométrer (60 min recommandées)
- [ ] Noter tes réponses sur papier

**Thèmes couverts** :
- Commandes de base (15 questions)
- Permissions et utilisateurs (10 questions)
- Processus et services (10 questions)
- Réseau (8 questions)
- Logs et sécurité (7 questions)

---

### QCM-Linux-CORRECTION.md
**Chemin** : `QCM/Correction/QCM-Linux-CORRECTION.md`

**Contenu** : Corrections détaillées de toutes les 50 questions

**À faire** :
- [ ] Corriger ton QCM
- [ ] Calculer ton score : ___/50
- [ ] Lire toutes les explications
- [ ] Noter tes erreurs dans un carnet
- [ ] Réviser les notions mal comprises

**Score cible** : 40/50 (80%) minimum

---

## 📋 3. QUESTIONS OUVERTES (20 questions)

### Questions-Ouvertes-Linux-20Q-EVALUATION.md
**Chemin** : `Questions-Ouvertes/Evaluation/Questions-Ouvertes-Linux-20Q-EVALUATION.md`

**Contenu** : 20 questions de rédaction

**À faire** :
- [ ] Essayer de répondre seul (au clavier)
- [ ] Prendre le temps de rédiger (pas juste des mots-clés)
- [ ] Ne pas regarder la correction

**Types de questions** :
- Expliquer des concepts (permissions, processus, services)
- Rédiger des commandes complètes
- Résoudre des problèmes (dépannage)
- Proposer des architectures

**Temps recommandé** : ⏱️ 90 min

---

### Questions-Ouvertes-Linux-20Q-CORRECTION.md
**Chemin** : `Questions-Ouvertes/Correction/Questions-Ouvertes-Linux-20Q-CORRECTION.md`

**Contenu** : Corrections détaillées avec explications

**À faire** :
- [ ] Comparer tes réponses avec les corrections
- [ ] Compléter tes notes
- [ ] Identifier les concepts à revoir

---

## 🔧 4. MSP PRATIQUE

### MSP-Pratique-Services-Linux.md
**Chemin** : `MSP/MSP-Pratique-Services-Linux.md`

**Contenu** : Mise en situation professionnelle

**Scénarios pratiques** :
- Configuration Apache2 (virtual hosts)
- Configuration SSH (sécurisation)
- Configuration NFS (partage de fichiers)
- Configuration Samba (intégration Windows)
- Tâches planifiées avec Cron
- Analyse de logs et dépannage

**Temps recommandé** : ⏱️ 60-90 min

**À faire** :
- [ ] Lire les scénarios
- [ ] Essayer de résoudre seul
- [ ] Tester dans une VM Linux (optionnel)
- [ ] Comparer avec les solutions proposées

---

## 📊 SUIVI DE PROGRESSION

### Checklist globale

- [ ] Fiche de révision lue et comprise
- [ ] QCM terminé : Score = ___/50 (___%)
- [ ] QCM corrigé et erreurs analysées
- [ ] Questions ouvertes terminées
- [ ] Questions ouvertes corrigées
- [ ] MSP pratique effectuée
- [ ] Points faibles identifiés : _______________
- [ ] Révision ciblée effectuée

### Objectifs de score

| Niveau | Score QCM | Score Questions | État |
|--------|-----------|-----------------|------|
| **Excellent** | 45-50/50 (90%+) | 18-20/20 (90%+) | 🟢 Prêt ! |
| **Très bien** | 40-44/50 (80-89%) | 16-17/20 (80-89%) | 🟡 Réviser un peu |
| **Bien** | 35-39/50 (70-79%) | 14-15/20 (70-79%) | 🟠 Réviser davantage |
| **Insuffisant** | < 35/50 (< 70%) | < 14/20 (< 70%) | 🔴 Réviser en priorité |

---

## 💡 CONSEILS SPÉCIFIQUES LINUX

### Commandes à connaître PAR CŒUR

**Navigation** :
```bash
cd, ls, pwd, find, locate, which, whereis
```

**Fichiers** :
```bash
cat, less, more, head, tail, grep, sed, awk
cp, mv, rm, mkdir, rmdir, touch
```

**Permissions** :
```bash
chmod, chown, chgrp, umask
chmod 755, chmod 644, chmod 777
```

**Utilisateurs** :
```bash
useradd, usermod, userdel, passwd
su, sudo, id, whoami, who, w
```

**Processus** :
```bash
ps aux, top, htop, kill, killall, pkill
nice, renice, bg, fg, jobs, nohup
```

**Services** :
```bash
systemctl start/stop/restart/status nom_service
systemctl enable/disable nom_service
systemctl list-units --type=service
```

**Réseau** :
```bash
ip addr, ip route, ip link
ping, traceroute, netstat, ss, nmap
iptables -L, firewall-cmd
```

**Logs** :
```bash
journalctl -u nom_service
journalctl --since "2023-01-01"
tail -f /var/log/syslog
```

### Pratique recommandée

**Installe une VM Linux** :
- Ubuntu Server 22.04 LTS (plus facile)
- Rocky Linux 9 (équivalent RHEL)
- Debian 12 (stable)

**Entraîne-toi sur** :
- Créer des utilisateurs et groupes
- Gérer les permissions (chmod/chown)
- Installer et configurer Apache2
- Configurer SSH (désactiver root, changer port)
- Créer des tâches cron
- Analyser les logs avec journalctl

---

## 🔗 NAVIGATION

### Retour aux documents principaux
- 📖 [README.md](../README.md) - Vue d'ensemble
- 🚀 [START-HERE.md](../START-HERE.md) - Démarrage rapide
- 📚 [INDEX-GENERAL.md](../INDEX-GENERAL.md) - Tous les CCPs
- 📅 [PLANNING-REVISION-5-JOURS.md](../PLANNING-REVISION-5-JOURS.md) - Planning détaillé

### Autres CCPs
- [CCP1 - Support Utilisateur](../CCP1-Support-Utilisateur/)
- [CCP2 - Windows / Active Directory](../CCP2-Windows-Active-Directory/)
- [CCP4 - Services Réseau](../CCP4-Services-Reseau/)
- [CCP5 - Virtualisation](../CCP5-Virtualisation/)
- [CCP6 - Scripts et Automatisation](../CCP6-Scripts-Automatisation/)
- [CCP7 - Sécurisation Internet](../CCP7-Securisation-Acces-Internet/)
- [CCP8 - Sauvegardes](../CCP8-Sauvegardes-Restaurations/)
- [CCP9 - Déploiement Postes](../CCP9-Deploiement-Postes/)

---

## 🚀 Commence maintenant !

**Étape 1** : Ouvre [Fiche-Revision/FICHE-01-Linux-Commandes-Essentielles.md](Fiche-Revision/FICHE-01-Linux-Commandes-Essentielles.md)  
**Étape 2** : Lis attentivement en prenant des notes  
**Étape 3** : Fais le [QCM/Evaluation/QCM-Linux-Complet-50Q-EVALUATION.md](QCM/Evaluation/QCM-Linux-Complet-50Q-EVALUATION.md)

**Bon courage ! 🐧💪**
