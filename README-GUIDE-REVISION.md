# 🎓 GUIDE DE RÉVISION COMPLET - Examen TSSR

> **Ressources complètes pour préparer l'examen TSSR**  
> Technicien Supérieur Systèmes et Réseaux

---

## 📖 À propos de ce guide

Ce guide présente l'ensemble des ressources disponibles pour ta préparation à l'examen TSSR. Toutes les ressources sont organisées de manière à faciliter une révision efficace et progressive.

### 🎯 Objectif
Réussir l'examen TSSR en maîtrisant les 9 CCPs (Certificats de Compétences Professionnelles) du référentiel.

### 📅 Durée recommandée
- **Révision intensive** : 5 jours (50h)
- **Révision normale** : 3-4 semaines (2-3h/jour)
- **Révision longue** : 2-3 mois (30 min/jour)

---

## 📚 STRUCTURE DES RESSOURCES

### Organisation par CCP

Chaque CCP dispose d'un dossier organisé en 3 sous-dossiers :

```
CCP-X-Nom-Du-CCP/
│
├── Fiche-Revisions/
│   └── FICHE-XX-Nom-Fiche.md                    → Théorie complète
│
├── QCM/
│   ├── Evaluation/
│   │   └── QCM-XXX-XX-EVALUATION.md             → Sujet sans réponses
│   └── Correction/
│       └── QCM-XXX-XX-CORRECTION.md             → Corrections détaillées
│
└── Questions-Ouvertes/
    ├── Evaluation/
    │   └── QUESTIONS-OUVERTES-XXX-EVALUATION.md  → Sujet sans réponses
    └── Correction/
        └── QUESTIONS-OUVERTES-XXX-CORRECTION.md  → Corrections détaillées
```

### Avantages de cette organisation
- ✅ **Séparation Evaluation/Correction** : Permet de s'auto-évaluer sans tricher
- ✅ **Structure uniforme** : Tous les CCPs suivent la même organisation
- ✅ **Facilité de navigation** : Chemins clairs et prévisibles
- ✅ **Progression logique** : Fiche → QCM → Questions ouvertes

---

## 📊 LES 9 CCPs EN DÉTAIL

### 🔴 PRIORITÉ ABSOLUE (60% du coefficient)

#### CCP6 - Scripts et Automatisation (25%)
**📁 Dossier** : `CCP6-Scripts-Automatisation/`

**Contenu** :
- 📖 **Fiche** : Bash + PowerShell (syntaxe, variables, boucles, fonctions)
- 📝 **QCM** : 30 questions (15 Bash + 15 PowerShell)
- 📋 **Questions ouvertes** : 10 exercices pratiques de scripting

**Thèmes clés** :
- Syntaxe Bash et PowerShell
- Variables et types
- Structures de contrôle (if, for, while, case)
- Fonctions et paramètres
- Gestion des erreurs
- Manipulation de fichiers
- Scripts d'automatisation système

**Temps de révision** : ⏱️ 5-6h

**Pourquoi c'est prioritaire** :
- Coefficient le plus élevé (25%)
- Utilisé dans toutes les MSP (Mises en Situation Professionnelles)
- Compétence transversale demandée partout

---

#### CCP2 - Windows / Active Directory (20%)
**📁 Dossier** : `CCP2-Windows-Active-Directory/`

**Contenu** :
- 📖 **Fiche** : AD, FSMO, GPO, DNS, PowerShell, Permissions
- 📝 **QCM** : 30 questions
- 📋 **Questions ouvertes** : 15 questions détaillées

**Thèmes clés** :
- Architecture Active Directory
- Rôles FSMO (5 rôles)
- Stratégies de groupe (GPO)
- DNS et zones
- Permissions NTFS et Partage
- PowerShell pour AD (cmdlets)
- Utilisateurs, groupes, OU
- Sauvegardes AD et Corbeille AD

**Temps de révision** : ⏱️ 6-7h

**Pourquoi c'est prioritaire** :
- Coefficient élevé (20%)
- Cœur du métier de technicien système Windows
- Souvent au centre des MSP

---

#### CCP5 - Virtualisation VMware (15%)
**📁 Dossier** : `CCP5-Virtualisation/`

**Contenu** :
- 📖 **Fiche** : VMware vSphere, ESXi, vCenter
- 📝 **QCM** : 40 questions
- 📋 **Questions ouvertes** : 10 questions

**Thèmes clés** :
- Architecture vSphere
- vMotion et Storage vMotion
- vSphere HA (High Availability)
- vSphere DRS (Distributed Resource Scheduler)
- Snapshots (risques et bonnes pratiques)
- Datastores et stockage
- Réseau virtuel (vSwitch, dvSwitch)
- Resource Pools

**Temps de révision** : ⏱️ 4-5h

**Pourquoi c'est prioritaire** :
- Coefficient important (15%)
- Virtualisation omniprésente en entreprise
- Concepts techniques pointus

---

### 🟡 PRIORITÉ ÉLEVÉE (25% du coefficient)

#### CCP3 - Maintenance Linux (10%)
**📁 Dossier** : `CCP3-Maintenance-Linux/`

**Contenu** :
- 📖 **Fiche** : Commandes Linux essentielles
- 📝 **QCM** : 50 questions
- 📋 **Questions ouvertes** : 20 questions
- 🔧 **MSP** : Pratique services Linux

**Thèmes clés** :
- Navigation système de fichiers
- Gestion utilisateurs et permissions (chmod, chown, umask)
- Processus et services (systemctl, ps, top, kill)
- Réseau Linux (ip, netstat, ss, iptables)
- Logs et journaux (journalctl, /var/log)
- Gestion de paquets (apt, yum, dpkg, rpm)
- Cron et tâches planifiées
- Configuration services (Apache, SSH, NFS, Samba)

**Temps de révision** : ⏱️ 5-6h

**Pourquoi c'est important** :
- Linux de plus en plus présent en entreprise
- Commandes à connaître par cœur
- Souvent dans les MSP

---

#### CCP1 - Support Utilisateur (10%)
**📁 Dossier** : `CCP1-Support-Utilisateur/`

**Contenu** :
- 📖 **Fiche** : Méthodologie support, communication, ITIL
- 📝 **QCM** : 40 questions
- 📋 **Questions ouvertes** : 10 questions

**Thèmes clés** :
- Méthodologie de résolution d'incidents
- Communication avec les utilisateurs
- ITIL v4 (Incident, Problème, Changement)
- Gestion des tickets
- Dépannage réseau (ipconfig, ping, tracert, nslookup)
- Active Directory (utilisateurs, réinitialisation MDP)
- Problèmes courants (imprimantes, Outlook, VPN, RDP)
- Documentation et escalade

**Temps de révision** : ⏱️ 4-5h

**Pourquoi c'est important** :
- Base du métier de technicien
- Communication cruciale à l'oral
- ITIL souvent demandé

---

#### CCP8 - Sauvegardes & Restaurations (5%)
**📁 Dossier** : `CCP8-Sauvegardes-Restaurations/`

**Contenu** :
- 📖 **Fiche** : Sauvegardes, PRA, PCA, RTO, RPO
- 📝 **QCM** : 20 questions
- 📋 **Questions ouvertes** : 15 questions

**Thèmes clés** :
- Types de sauvegardes (complète, incrémentielle, différentielle)
- Stratégies de sauvegarde (règle 3-2-1)
- RTO et RPO (définitions et calculs)
- PRA (Plan de Reprise d'Activité)
- PCA (Plan de Continuité d'Activité)
- Snapshots VM
- Outils de sauvegarde (Veeam, Windows Server Backup)
- Tests de restauration

**Temps de révision** : ⏱️ 3-4h

**Pourquoi c'est important** :
- Concepts souvent testés (RTO/RPO)
- Distinction PRA/PCA à maîtriser
- Essentiel pour la continuité d'activité

---

### 🟢 PRIORITÉ MOYENNE (15% du coefficient)

#### CCP4 - Services Réseau (10%)
**📁 Dossier** : `CCP4-Services-Reseau/`

**Contenu** :
- 📖 **Fiches** (3) :
  - Ports et Protocoles
  - Subnetting et CIDR
  - Commandes Cisco IOS
- 📝 **QCM** : 60 questions (3 parties de 20)
- 📋 **Questions ouvertes** : 20 questions (2 parties de 10)
- 🔧 **Exercices** : 20 exercices de subnetting

**Thèmes clés** :
- Modèle OSI et TCP/IP
- Ports et protocoles (TCP/UDP)
- Subnetting et CIDR
- VLAN (Access, Trunk, 802.1Q)
- Routage (statique, dynamique)
- DHCP et DNS
- ACL Cisco
- Spanning Tree Protocol (STP)
- Commandes Cisco IOS (show, config)

**Temps de révision** : ⏱️ 6-7h

**Pourquoi c'est utile** :
- Fondamentaux réseau essentiels
- Ports à connaître par cœur
- Subnetting souvent testé

---

#### CCP7 - Sécurisation Accès Internet (10%)
**📁 Dossier** : `CCP7-Securisation-Acces-Internet/`

**Contenu** :
- 📖 **Fiche** : Firewall, VPN, Proxy, IDS/IPS, DMZ
- 📝 **QCM** : 25 questions
- 📋 **Questions ouvertes** : 8 questions

**Thèmes clés** :
- Firewall (pfSense, Fortinet)
- VPN (IPsec, SSL/TLS, OpenVPN)
- Proxy (Squid, filtrage URL)
- IDS/IPS (Suricata, Snort)
- DMZ (Zone démilitarisée)
- SSL/TLS et certificats
- Authentification et AAA
- NAT et PAT

**Temps de révision** : ⏱️ 3-4h

**Pourquoi c'est utile** :
- Sécurité de plus en plus importante
- Configuration DMZ souvent demandée
- Concepts VPN à maîtriser

---

#### CCP9 - Déploiement Postes (10%)
**📁 Dossier** : `CCP9-Deploiement-Postes/`

**Contenu** :
- 📖 **Fiche** : WDS, MDT, SCCM, Sysprep, DISM
- 📝 **QCM** : 20 questions
- 📋 **Questions ouvertes** : 6 questions

**Thèmes clés** :
- Windows Deployment Services (WDS)
- Microsoft Deployment Toolkit (MDT)
- Sysprep (généralisation d'image)
- DISM (gestion d'images)
- PXE Boot
- Format WIM
- Activation Windows (KMS, MAK)
- Gestion de pilotes
- Task Sequences

**Temps de révision** : ⏱️ 3-4h

**Pourquoi c'est utile** :
- Déploiement massif en entreprise
- Sysprep et DISM souvent testés
- Concepts d'automatisation

---

## 📝 EXAMENS BLANCS

### Examen Blanc 1 & 2
**📁 Dossier** : `Examens-Blancs/Examen 1/` et `Examens-Blancs/Examen 2/`

**Format** :
- **Durée totale** : 3h30
- **Partie 1 - MSP** : 1h30 (100 points)
  - 3 incidents à résoudre (mise en situation professionnelle)
- **Partie 2 - Questionnaire** : 2h00 (100 points)
  - 6 sections techniques (Linux, Réseau, Windows, Virtualisation, Sécurité, Anglais)

**Contenu de chaque examen** :
- 📝 **EVALUATION.md** : Sujet complet (à faire sans aide)
- ✅ **CORRECTION.md** : Corrections détaillées avec barème intégré

**Comment utiliser** :
1. Réserver un créneau de 3h30 sans interruption
2. Faire l'examen en conditions réelles (chronomètre, sans aide)
3. Se corriger avec le fichier CORRECTION
4. Noter toutes les erreurs
5. Réviser les notions mal comprises

**Objectif** :
- S'habituer au format de l'examen
- Gérer son temps efficacement
- Identifier ses points faibles
- Prendre confiance avant le jour J

---

## 🎤 SIMULATION ORALE

**📁 Dossier** : `Simulation-Orale-Complete/`

**Contenu** :
- 📖 **INDEX-SIMULATION-ORALE.md** : Guide complet de préparation
- 📝 **PARTIE1-MSP-PRATIQUE.md** : Mise en situation professionnelle (30 min)
- 📝 **PARTIE2-ENTRETIEN-TECHNIQUE.md** : Questions techniques approfondies (20 min)
- 📝 **PARTIE3-ENTRETIEN-FINAL.md** : Questions sur le dossier professionnel (10 min)

**Format de l'épreuve orale** :
- **Durée totale** : 1h
- **Préparation** : 30 min (MSP écrite)
- **Passage** : 30 min (présentation + questions)

**Conseils pour l'oral** :
- 🗣️ **Parler à voix haute** pendant la préparation
- 🎯 **Montrer sa démarche** même si on ne trouve pas tout
- 📚 **Vocabulaire technique** mais adaptable
- 🤝 **Être pédagogue** : expliquer comme à un utilisateur
- ❌ **Ne jamais dire "je ne sais pas"** → proposer une méthode de recherche
- 💼 **Parler de son expérience** : stages, projets persos

---

## 🎯 MÉTHODOLOGIE DE RÉVISION

### Approche recommandée par CCP

Pour chaque CCP, suis cette séquence :

#### 1. Lecture de la fiche (30-60 min)
- 📖 Ouvre le fichier dans `Fiche-Revisions/`
- ✍️ Prends des notes manuscrites
- 🖍️ Surligne les points clés
- 🤔 Comprends les concepts (ne pas apprendre par cœur bêtement)

#### 2. QCM en conditions réelles (30-60 min)
- 📝 Ouvre le fichier dans `QCM/Evaluation/`
- ⏱️ Chronomètre-toi
- 🚫 **NE PAS regarder la correction !**
- 📄 Note tes réponses sur papier ou dans un document séparé

#### 3. Correction du QCM (30-60 min)
- ✅ Ouvre le fichier dans `QCM/Correction/`
- 🔢 Compte ton score
- 🔍 Lis TOUTES les corrections (même celles des bonnes réponses)
- 📓 Note tes erreurs dans un carnet dédié
- 📚 Retourne à la fiche si une notion n'est pas claire

#### 4. Questions ouvertes (30-90 min)
- 📝 Ouvre le fichier dans `Questions-Ouvertes/Evaluation/`
- ⌨️ Essaie de répondre seul (au clavier ou manuscrit)
- 🤔 Prends le temps de rédiger (pas juste des mots-clés)
- 🚫 Résiste à la tentation d'ouvrir la correction

#### 5. Correction des questions ouvertes (30-60 min)
- ✅ Ouvre le fichier dans `Questions-Ouvertes/Correction/`
- 📊 Compare tes réponses avec les corrections
- ➕ Complète tes notes avec les détails manquants
- 🎯 Identifie les points faibles persistants

#### 6. Révision ciblée (variable)
- 🔄 Refais les questions ratées quelques jours après
- 📚 Consulte d'autres ressources si besoin (docs officielles)
- 💻 Pratique en environnement virtuel si possible

---

### Planning de révision

#### Option 1 : Révision intensive (5 jours)
👉 **Suis le [PLANNING-REVISION-5-JOURS.md](PLANNING-REVISION-5-JOURS.md)**

- **Jour 1** : Linux + Support Utilisateur
- **Jour 2** : Sauvegardes + Réseau + Windows (début)
- **Jour 3** : Virtualisation + Scripts + Sécurité + Déploiement
- **Jour 4** : Windows (fin) + Révision + Examen Blanc 1
- **Jour 5** : Examen Blanc 2 + Simulation orale + Révision finale

**Avantages** :
- ✅ Révision complète en peu de temps
- ✅ Planning détaillé heure par heure
- ✅ Tous les fichiers sont listés

**Inconvénients** :
- ⚠️ Très intensif (8-10h/jour)
- ⚠️ Fatiguant mentalement
- ⚠️ Nécessite disponibilité complète

---

#### Option 2 : Révision progressive (3-4 semaines)

**Semaine 1** : CCPs prioritaires
- Lundi/Mardi : CCP6 - Scripts
- Mercredi/Jeudi : CCP2 - Windows/AD
- Vendredi/Samedi : CCP5 - Virtualisation
- Dimanche : Révision de la semaine

**Semaine 2** : CCPs complémentaires
- Lundi/Mardi : CCP3 - Linux
- Mercredi/Jeudi : CCP1 - Support Utilisateur
- Vendredi : CCP8 - Sauvegardes
- Samedi/Dimanche : Révision de la semaine

**Semaine 3** : CCPs secondaires
- Lundi/Mardi : CCP4 - Réseau
- Mercredi : CCP7 - Sécurité
- Jeudi : CCP9 - Déploiement
- Vendredi : Révision générale
- Samedi/Dimanche : Examen Blanc 1

**Semaine 4** : Finalisation
- Lundi/Mardi : Révision des points faibles
- Mercredi : Examen Blanc 2
- Jeudi : Analyse des erreurs + révision ciblée
- Vendredi : Simulation orale
- Samedi : Révision fiches prioritaires
- Dimanche : Repos

---

#### Option 3 : Révision longue (2-3 mois)

**Rythme** : 30-60 min/jour

- **Mois 1** : 1 CCP par semaine (4 CCPs)
- **Mois 2** : 1 CCP par semaine (4 CCPs)
- **Semaines 9-10** : Dernier CCP + Révision générale
- **Semaine 11** : Examens blancs (1 par week-end)
- **Semaine 12** : Simulation orale + Révision finale

**Avantages** :
- ✅ Apprentissage durable
- ✅ Moins de stress
- ✅ Temps pour pratiquer en environnement virtuel

**Inconvénients** :
- ⚠️ Risque de procrastination
- ⚠️ Besoin de discipline quotidienne
- ⚠️ Peut sembler long

---

## 📊 SUIVI DE PROGRESSION

Utilise le fichier **[SUIVI-SCORES-PROGRESSION.md](SUIVI-SCORES-PROGRESSION.md)** pour :

- 📈 Noter tes scores de QCM
- 🎯 Identifier tes points faibles
- 📊 Mesurer ta progression dans le temps
- ✅ Cocher les CCPs terminés
- 📝 Noter tes observations

**Exemple de tableau** :
| CCP | QCM Score | Questions Score | État | À revoir |
|-----|-----------|-----------------|------|----------|
| CCP6 | 24/30 (80%) | 7/10 (70%) | ✅ Terminé | Fonctions Bash |
| CCP2 | 22/30 (73%) | 12/15 (80%) | ✅ Terminé | Rôles FSMO |

---

## 💡 CONSEILS POUR RÉUSSIR

### Pendant la révision

1. **📝 Prends des notes manuscrites**
   - L'écriture manuscrite améliore la mémorisation
   - Crée des fiches synthétiques personnelles
   - Utilise des couleurs pour hiérarchiser

2. **🗣️ Parle à voix haute**
   - Explique les concepts comme si tu enseignais
   - Prépare-toi à l'épreuve orale
   - Aide à détecter les incompréhensions

3. **⏱️ Chronométre-toi**
   - Habitue-toi à la pression du temps
   - Gère mieux ton stress
   - Apprends à prioriser

4. **🔄 Révise régulièrement**
   - Refais les questions ratées après quelques jours
   - Espacé les révisions (répétition espacée)
   - Ne laisse pas de lacunes

5. **💻 Pratique en environnement virtuel**
   - Installe VirtualBox ou VMware Workstation
   - Crée des VMs Windows Server, Linux, pfSense
   - Teste les commandes et configurations

6. **📚 Complète avec des ressources officielles**
   - Documentation Microsoft Learn
   - Man pages Linux
   - VMware Docs
   - Cisco Learning Network

7. **🛌 Dors suffisamment**
   - 7-8h de sommeil par nuit
   - Le sommeil consolide la mémorisation
   - Évite le bachotage de dernière minute

8. **🧘 Gère ton stress**
   - Fais des pauses régulières (10 min/90 min)
   - Respire profondément
   - Visualise ta réussite

---

### Le jour de l'examen

#### Épreuve écrite

1. **📖 Lis ENTIÈREMENT chaque question**
   - Ne te précipite pas
   - Souvent des indices dans l'énoncé

2. **⏰ Gère ton temps**
   - Ne reste pas bloqué sur une question
   - Passe à la suivante et reviens après
   - Réserve 15 min pour la relecture

3. **🎯 Commence par les questions faciles**
   - Prends confiance
   - Accumule des points rapidement
   - Gère mieux ton stress

4. **✍️ Rédige proprement**
   - Soigne ton écriture
   - Structure tes réponses (listes, tableaux)
   - Justifie tes choix

5. **🔢 Vérifie tes réponses**
   - Relis tout si tu as du temps
   - Vérifie les calculs (subnetting)
   - Corrige les fautes d'orthographe

---

#### Épreuve orale

1. **🗣️ Parle à voix haute de ta démarche**
   - Montre ta logique de résolution
   - Le jury veut comprendre ton raisonnement
   - Même si tu te trompes, la méthode compte

2. **🤝 Sois pédagogue**
   - Explique comme à un utilisateur non-technique
   - Utilise des métaphores si besoin
   - Adapte ton vocabulaire

3. **❌ Ne dis JAMAIS "je ne sais pas"**
   - Propose une démarche de recherche
   - Mentionne les sources que tu consulterais
   - Parle d'escalade vers niveau 2 si besoin

4. **💼 Parle de ton expérience**
   - Fais le lien avec tes stages
   - Mentionne tes projets personnels
   - Montre ta passion pour l'informatique

5. **😌 Reste calme et confiant**
   - Respire profondément si tu stresses
   - Prends ton temps avant de répondre
   - Le jury n'est pas là pour te piéger

6. **🎯 Écoute bien les questions**
   - N'hésite pas à faire répéter
   - Reformule la question pour vérifier
   - Réponds précisément (ne pars pas en hors-sujet)

---

## 🔗 RESSOURCES COMPLÉMENTAIRES

### Documentation officielle

- **Microsoft Learn** : https://learn.microsoft.com/
  - Windows Server, Active Directory, PowerShell, Hyper-V
  
- **VMware Docs** : https://docs.vmware.com/
  - vSphere, ESXi, vCenter
  
- **Linux man pages** : `man <commande>` dans le terminal
  - Documentation complète de toutes les commandes
  
- **Cisco IOS** : https://www.cisco.com/c/en/us/support/index.html
  - Configuration switch et routeur

### Outils pratiques

- **Subnet Calculator** : https://www.subnet-calculator.com/
  - Calculs de sous-réseaux CIDR
  
- **RegEx Tester** : https://regex101.com/
  - Tests d'expressions régulières
  
- **PowerShell ISE** : Inclus dans Windows
  - Tests de scripts PowerShell
  
- **VirtualBox** : https://www.virtualbox.org/
  - Environnement de virtualisation gratuit

---

## ✨ CONCLUSION

Tu disposes maintenant de **TOUTES les ressources nécessaires** pour réussir ton examen TSSR :

- ✅ **9 CCPs complets** avec fiches, QCM et questions ouvertes
- ✅ **~400 questions** pour t'entraîner
- ✅ **2 examens blancs** pour te tester
- ✅ **1 simulation orale** pour te préparer
- ✅ **Planning détaillé** pour t'organiser
- ✅ **Méthodologie** pour réviser efficacement

**Il ne te reste plus qu'à suivre ce guide et à réviser ! 💪**

---

## 🚀 Prochaines étapes

1. 📖 Lis le [README.md](README.md) pour comprendre l'organisation générale
2. 🚀 Ouvre le [START-HERE.md](START-HERE.md) pour démarrer rapidement
3. 📅 Consulte le [PLANNING-REVISION-5-JOURS.md](PLANNING-REVISION-5-JOURS.md) pour un planning détaillé
4. 📚 Explore l'[INDEX-GENERAL.md](INDEX-GENERAL.md) pour naviguer par CCP

---

**Bon courage pour tes révisions ! Tu vas réussir ! 🎓✨**
