# SIMULATION ORALE TSSR - PARTIE 3 COMPLETE
## Entretien Final + Grilles Evaluation + Recapitulatif (20 minutes - 30 points)

---

# 🎯 SIMULATION ORALE TSSR - PARTIE 3A
## Introduction à l'Entretien Final

---

## 📋 INFORMATIONS GÉNÉRALES

**Durée totale Entretien Final** : 20 minutes  
**Composition** : 3 phases distinctes  
**Coefficient** : 30 points sur 150 (Jour 2 oral)  
**Seuil validation** : 12/30 points minimum (40%)

---

## 🎬 OBJECTIFS DE L'ENTRETIEN FINAL

### 1. **Évaluation Projet Personnel**
- Capacité à mener un projet technique de bout en bout
- Compétences transversales mobilisées
- Autonomie et initiative
- Documentation et présentation

### 2. **Évaluation Motivation Professionnelle**
- Cohérence du parcours et du projet professionnel
- Connaissance du métier TSSR
- Perspectives d'évolution
- Adaptabilité et curiosité

### 3. **Synthèse Globale du Candidat**
- Vue d'ensemble compétences techniques + soft skills
- Capacité à prendre du recul
- Maturité professionnelle
- Adéquation profil/certification

---

## ⏱️ DÉROULEMENT DÉTAILLÉ (20 MINUTES)

### **Phase 1 : Présentation Projet Personnel (5-7 minutes)**

#### 📊 Structure Attendue

**Minute 1-2 : Introduction**
- Contexte du projet (cadre : stage, formation, perso)
- Problématique identifiée
- Objectif du projet

**Minute 3-4 : Réalisation Technique**
- Technologies utilisées (en lien avec les CCPs)
- Architecture mise en place
- Méthodologie suivie
- Difficultés rencontrées et solutions apportées

**Minute 5-6 : Résultats et Bilan**
- Livrables produits
- Tests et validation
- Retour utilisateurs
- Compétences développées

**Minute 7 : Ouverture**
- Améliorations possibles
- Évolutions envisagées
- Leçons apprises

---

#### 💡 CONSEILS POUR LA PRÉSENTATION

**À FAIRE** ✅
- **Structurer** : Introduction → Développement → Conclusion
- **Illustrer** : Support visuel (slides PowerPoint/PDF) recommandé mais pas obligatoire
- **Valoriser** : Mettre en avant les CCPs couverts
- **Démontrer** : Si possible, montrer une capture d'écran ou un schéma d'architecture
- **Quantifier** : Donner des chiffres (X utilisateurs, Y serveurs, Z Go sauvegardés)
- **Problématiser** : Montrer que vous avez résolu un vrai besoin

**À ÉVITER** ❌
- Lire ses notes mot à mot
- Projet trop simple (installer Windows 10)
- Projet hors périmètre TSSR (développement web pur)
- Pas de lien avec les CCPs
- Discours trop technique sans vulgarisation
- Dépassement temps (7 min max)

---

#### 📂 EXEMPLES DE PROJETS VALORISABLES

**Projet 1 : Mise en place infrastructure virtualisée PME**
- **Contexte** : Stage chez ABC SARL (25 utilisateurs)
- **Problématique** : Serveurs physiques vieillissants, pas de sauvegarde
- **Réalisation** :
  - Installation ESXi 8.0 sur 2 hôtes HP
  - Création 5 VMs (DC, File Server, GLPI, PfSense, Veeam)
  - Configuration AD avec 3 OUs (Direction, Compta, Commercial)
  - GPOs (fond d'écran, mappage lecteurs, restrictions USB)
  - Sauvegardes Veeam quotidiennes avec réplication off-site
- **Résultats** : RTO réduit de 24h à 2h, RPO de 1 semaine à 1 jour
- **CCPs couverts** : CCP3 (réseau), CCP4 (Windows/AD), CCP5 (virtualisation), CCP6 (sauvegardes)
- **Durée projet** : 3 semaines
- **Livrables** : Documentation technique (30 pages), procédures restauration

**Projet 2 : Migration serveur Linux Debian 10 → 12**
- **Contexte** : Formation admin Linux, serveur web production
- **Problématique** : Debian 10 fin de vie, vulnérabilités CVE critiques
- **Réalisation** :
  - Audit paquets installés (`dpkg -l > audit.txt`)
  - Sauvegarde complète (`tar -czf` + `rsync`)
  - Tests migration sur VM clone
  - Mise à jour Debian 10 → 11 → 12 (`apt dist-upgrade`)
  - Reconfiguration Apache 2.4 (modules, VirtualHosts)
  - Tests validation (curl, ab benchmarks)
- **Résultats** : Serveur sécurisé, performances +15% (PHP 8.2), 0 downtime utilisateurs
- **CCPs couverts** : CCP2 (maintenance Linux), CCP3 (services web), CCP6 (sauvegardes)
- **Durée projet** : 2 semaines (week-end maintenance)
- **Livrables** : Plan de migration, rapport de tests

**Projet 3 : Automatisation déploiement poste utilisateur**
- **Contexte** : Stage PME, 15 nouveaux PCs à déployer
- **Problématique** : Installation manuelle = 2h/poste = 30h total
- **Réalisation** :
  - Création image Windows 10 avec Sysprep
  - Script PowerShell post-déploiement :
    - Renommage PC (`Rename-Computer`)
    - Jonction domaine (`Add-Computer`)
    - Installation logiciels silencieux (7-Zip, Adobe Reader, Chrome)
    - Configuration imprimantes réseau
  - Documentation procédure pour service IT
- **Résultats** : Temps réduit à 30 min/poste = gain 22,5h, standardisation configuration
- **CCPs couverts** : CCP4 (Windows/AD), CCP7 (scripting PowerShell)
- **Durée projet** : 1 semaine
- **Livrables** : Script PowerShell commenté (200 lignes), guide déploiement

---

### **Phase 2 : Questions sur le Projet (3-5 minutes)**

Le jury pose 3 à 5 questions pour approfondir votre projet.

#### 🎯 QUESTIONS TYPES ET RÉPONSES

**Q1 : "Pourquoi avoir choisi VMware plutôt que Hyper-V ou Proxmox ?"**

**Réponse structurée** :
```
CONTEXTE :
- Entreprise déjà licences VMware Essentials
- Tuteur de stage compétent VMware (support interne)

COMPARAISON :
- Hyper-V : Gratuit avec Windows Server, mais moins mature clustering
- Proxmox : Open source, excellent mais pas de support commercial
- VMware : Leader marché, vMotion/HA robustes, écosystème riche

DÉCISION :
→ VMware pour cohérence infrastructure existante
→ Si nouveau projet : j'aurais évalué Proxmox (coûts)
```

**Q2 : "Vous mentionnez des GPOs, lesquelles avez-vous configurées ?"**

**Réponse détaillée** :
```
GPO 1 : "Mappage lecteurs réseau"
- User Configuration → Preferences → Drive Maps
- Lecteur P: → \\SRV-FILE01\Partage_Public (Everyone)
- Lecteur H: → \\SRV-FILE01\Users\%USERNAME% (personnel)

GPO 2 : "Restrictions USB"
- Computer Configuration → Administrative Templates → Removable Storage
- Deny all removable storage → Enabled
- Exception : Filtrage sécurité sur groupe "GRP_Admins"

GPO 3 : "Fond d'écran entreprise"
- User Configuration → Administrative Templates → Desktop → Wallpaper
- Chemin UNC : \\SRV-FILE01\Wallpapers\logo_company.jpg

GPO 4 : "Stratégie mots de passe"
- Computer Configuration → Policies → Account Policies → Password Policy
- Longueur min : 12 caractères
- Complexité : Enabled
- Durée max : 90 jours
```

**Q3 : "Comment avez-vous validé que les sauvegardes Veeam fonctionnaient ?"**

**Réponse méthodique** :
```
TESTS RÉALISÉS :

1. BACKUP SUCCESS (quotidien)
   - Vérification console Veeam : Job "Daily Backup" → Success
   - Email notification configuré (alertes échec)

2. RESTAURATION FICHIER
   - Test : Suppression volontaire fichier Word sur FILE01
   - Restauration : Veeam Backup Browser → recherche fichier → Restore
   - Durée : 2 minutes
   - Validation : Fichier restauré identique (hash MD5)

3. RESTAURATION VM COMPLÈTE
   - Test : Corruption VM "SRV-GLPI" (shutdown brutal)
   - Restauration : Instant VM Recovery (boot depuis backup)
   - Durée : 5 minutes (démarrage depuis datastore backup)
   - Migration Storage vMotion vers datastore production
   - Validation : Application GLPI accessible, tickets OK

4. DR DRILL (1×/mois)
   - Restauration complète infrastructure sur environnement isolé
   - Validation : Services démarrent, AD répliqué, utilisateurs connectent
   - Documentation temps restauration (RTO réel : 1h45)
```

**Q4 : "Quelles difficultés avez-vous rencontrées et comment les avez-vous résolues ?"**

**Réponse STAR (Situation/Tâche/Action/Résultat)** :

```
DIFFICULTÉ 1 : Réplication Veeam vers site distant échoue

Situation :
- WAN 10 Mbps entre siège et agence
- Backup FILE01 = 500 Go
- Réplication échoue après 18h (timeout)

Tâche :
- Assurer copie off-site pour PRA

Action :
1. Analyse : Calcul bande passante nécessaire
   - 500 Go = 4 000 000 Mbits
   - 10 Mbps théorique = 7 Mbps réel (overhead)
   - Temps requis : 4 000 000 / 7 / 3600 = 159 heures (6,6 jours) ❌
2. Solution alternative :
   - Backup local quotidien Veeam (repository NAS)
   - Copie manuelle mensuelle disque externe USB 2 To
   - Rotation GFS : disque stocké coffre agence
3. Implémentation :
   - Script PowerShell copie différentielle (`robocopy /MIR`)
   - Chiffrement BitLocker disque externe
   - Procédure documentée pour responsable agence

Résultat :
- PRA fonctionnel avec RPO 1 mois (acceptable PME)
- Coût 150€ (disque) vs 5k€ (upgrade WAN fibre)
- Compromis validé direction
```

```
DIFFICULTÉ 2 : GPO "Mappage lecteurs" ne s'applique pas

Situation :
- GPO créée et liée OU "OU=Utilisateurs,DC=abc,DC=local"
- Utilisateurs ne voient pas lecteurs P: et H:

Tâche :
- Diagnostiquer pourquoi GPO non appliquée

Action :
1. Tests initiaux :
   - `gpupdate /force` sur poste client → Aucun effet
   - `gpresult /r` → GPO "Mappage lecteurs" pas listée
2. Vérification GPO :
   - Filtre sécurité : "Authenticated Users" → ✅ OK
   - Liens GPO : Héritage bloqué ? → ❌ Non
   - État GPO : Enabled → ✅ OK
3. Découverte problème :
   - GPO appliquée sur Computer Configuration au lieu User Configuration ❌
4. Correction :
   - Déplacement paramètres vers User Configuration → Preferences → Drive Maps
   - `gpupdate /force` + redémarrage session
5. Validation :
   - `gpresult /r` → GPO appliquée ✅
   - Lecteurs P: et H: visibles ✅

Résultat :
- Problème résolu en 30 minutes
- Leçon apprise : Toujours distinguer Computer vs User Configuration
- Documentation procédure test GPO pour éviter erreur future
```

**Q5 : "Si vous deviez refaire ce projet, que changeriez-vous ?"**

**Réponse avec recul** :
```
AMÉLIORATION 1 : PLANNING PLUS RÉALISTE
- Initial : 2 semaines prévues
- Réel : 3 semaines nécessaires
- Cause : Sous-estimation temps configuration GPOs et tests
- À refaire : Ajouter 30% marge sécurité planning

AMÉLIORATION 2 : TESTS PLUS RIGOUREUX
- Effectué : Tests fonctionnels basiques (services démarrent)
- Manquant : Tests charge (combien d'utilisateurs simultanés ?)
- À refaire : Utiliser outils benchmarking (Apache Bench, IOMeter)

AMÉLIORATION 3 : DOCUMENTATION EN CONTINU
- Effectué : Documentation rédigée à la fin (1 semaine)
- Problème : Oubli détails configurations intermédiaires
- À refaire : Documentation au fur et à mesure (1h/jour)

AMÉLIORATION 4 : IMPLICATION UTILISATEURS
- Effectué : Déploiement "surprise" week-end
- Problème : Utilisateurs perdus lundi matin (lecteurs réseau changés)
- À refaire : Formation 30 min utilisateurs avant déploiement

AMÉLIORATION 5 : MONITORING DÈS LE DÉPART
- Effectué : Infrastructure déployée sans monitoring
- Problème : Panne disque RAID détectée 2 semaines après (downtime 4h)
- À refaire : Installer Centreon/Zabbix dès J1 (alertes proactives)

SYNTHÈSE :
→ Le projet a atteint ses objectifs techniques
→ Mais j'ai appris l'importance de la gestion de projet (planning, communication, documentation)
→ Ces soft skills sont aussi essentiels que les compétences techniques
```

---

### **Phase 3 : Questions Motivation et Perspectives (5-7 minutes)**

Le jury évalue votre motivation et votre projet professionnel.

#### 🎯 QUESTIONS MOTIVATION TYPES

**Q1 : "Pourquoi avez-vous choisi le métier de Technicien Supérieur Systèmes et Réseaux ?"**

**Réponse structurée** :

```
1. PASSION POUR L'INFORMATIQUE (depuis quand ?)
"Depuis adolescent, j'étais le 'dépanneur informatique' de la famille. 
J'ai commencé par démonter mon premier PC à 14 ans, puis j'ai créé mon 
premier réseau domestique avec un vieux routeur Linksys."

2. ATTRAIT POUR LE TECHNIQUE ET LE CONCRET
"Ce qui me plaît dans le métier TSSR, c'est la dimension technique : 
on ne fait pas que de la théorie, on met les mains dans le cambouis. 
Résoudre un incident réseau ou restaurer un serveur, c'est concret et gratifiant."

3. DIVERSITÉ DES MISSIONS
"Le métier TSSR couvre un large spectre : support utilisateur, administration 
systèmes Windows/Linux, réseau Cisco, virtualisation, sécurité. 
Cette polyvalence évite la routine."

4. ÉVOLUTION RAPIDE DU DOMAINE
"L'IT évolue constamment (cloud, conteneurs, IA). C'est un secteur où 
on apprend en permanence, ce qui me motive."

5. IMPACT MÉTIER
"Sans infrastructure IT fonctionnelle, une entreprise ne peut pas travailler. 
Le TSSR a un rôle central, c'est valorisant."

CONCLUSION :
"Le métier TSSR combine mes trois passions : technique, résolution de problèmes, 
et apprentissage continu."
```

**Q2 : "Quels sont vos objectifs professionnels à court terme (1-2 ans) et moyen terme (3-5 ans) ?"**

**Réponse avec progression logique** :

```
COURT TERME (1-2 ANS) : CONSOLIDER LES BASES

Objectif 1 : Intégrer une équipe infrastructure IT (PME/ETI)
- Poste : Technicien systèmes et réseaux junior
- Environnement : Infrastructure hybride Windows/Linux
- Missions : Support N2, administration serveurs, projets

Objectif 2 : Obtenir certifications éditeurs
- Microsoft : AZ-900 (Azure Fundamentals) → AZ-104 (Administrator)
- Cisco : CCNA (Routing & Switching)
- Linux : LPIC-1 ou Red Hat RHCSA
- Raison : Validation compétences reconnues marché

Objectif 3 : Développer expertise scripting
- PowerShell avancé (modules, API REST)
- Bash scripting production
- Automatisation tâches récurrentes

MOYEN TERME (3-5 ANS) : SPÉCIALISATION ET ÉVOLUTION

Objectif 1 : Évoluer vers Administrateur Systèmes et Réseaux
- Autonomie complète infrastructure
- Gestion projets techniques (migrations, déploiements)
- Pilotage prestataires externes

Objectif 2 : Spécialisation domaine (2 options selon opportunités)

Option A : VIRTUALISATION / CLOUD
- Expertise VMware vSphere (VCP certification)
- Ou expertise Microsoft Azure (architecte solutions)
- Pourquoi : Marché porteur, évolution vers cloud architect

Option B : SÉCURITÉ IT
- Expertise cybersécurité (firewall, EDR, SIEM)
- Certifications : CompTIA Security+, CEH
- Pourquoi : Enjeu majeur entreprises, métier d'avenir

Objectif 3 : Élargir compétences gestion
- Gestion budgets IT (CAPEX/OPEX)
- Management équipe (1-2 techniciens)
- Relation fournisseurs (négociation licences)

LONG TERME (5+ ANS) : RESPONSABLE INFRASTRUCTURE (optionnel)

Si opportunité :
- Responsable Infrastructure IT (PME/ETI)
- Management équipe 3-5 personnes
- Stratégie IT (schéma directeur, roadmap)

SYNTHÈSE :
"À court terme, je veux consolider mes bases techniques et certifications.
À moyen terme, évoluer vers admin sys et me spécialiser (cloud ou sécurité).
Le long terme dépendra des opportunités, mais pourquoi pas évoluer vers 
un poste à responsabilités managériales si affinités."
```

**Q3 : "Pourquoi cette certification TSSR plutôt qu'un autre diplôme (BTS SIO, BUT Réseaux) ?"**

**Réponse argumentée** :

```
AVANTAGES TITRE PROFESSIONNEL TSSR :

1. ORIENTATION PRATIQUE
- TP 60% du temps vs cours théoriques
- Projets réels (infrastructure complète)
- Compétences immédiatement opérationnelles
- VS BTS SIO : Plus théorique, moins de pratique systèmes/réseaux

2. DURÉE ADAPTÉE (8-12 mois)
- Reconversion rapide (j'ai 28 ans, pas le temps pour 2-3 ans d'études)
- Formation intensive
- VS BUT Réseaux : 3 ans, plus adapté sortie bac

3. RECONNAISSANCE MARCHÉ
- Certification RNCP Niveau 5 (Bac+2)
- Reconnue entreprises (équivalent BTS)
- CCPs séparables (validation progressive)

4. ACCOMPAGNEMENT RENFORCÉ
- Formateurs professionnels IT (15+ ans expérience)
- Stage long en entreprise (3-6 mois)
- Réseau alumni actif

5. COÛT (si autofinancement)
- CPF éligible (financement)
- Ou Pôle Emploi (reconversion)
- VS BTS/BUT : Frais universitaires si formation initiale

MON CAS PERSONNEL :
"J'ai 28 ans, en reconversion professionnelle (ancien comptable).
Le titre TSSR était idéal : formation courte (10 mois), pratique intensive,
et reconnaissance marché. Si j'avais 18 ans sortant du bac, j'aurais 
peut-être opté pour un BUT Réseaux (diplôme universitaire), mais 
dans ma situation, le TSSR était le meilleur choix."
```

**Q4 : "Quelle est votre veille technologique ? Comment vous tenez-vous informé des évolutions IT ?"**

**Réponse concrète avec exemples** :

```
MA ROUTINE VEILLE TECHNO :

1. QUOTIDIEN (15 min/jour)

Sites actualités IT :
- Next INpact / Clubic (actus générales)
- Silicon.fr (actualités entreprises)
- LeMagIT (infrastructure, cloud)

Réseaux sociaux :
- Reddit : r/sysadmin, r/networking, r/homelab
- Twitter : Comptes experts (@arstechnica, @thehackernews)

2. HEBDOMADAIRE (1h/semaine)

Newsletters :
- TLDR Tech (résumé quotidien)
- Hacker Newsletter (sécurité)
- Microsoft 365 Roadmap (nouveautés M365)

YouTube :
- NetworkChuck (tutoriels réseau fun)
- TechWorld with Nana (DevOps, Docker, K8s)
- Linus Tech Tips (hardware)

Podcasts (trajets) :
- "Darknet Diaries" (cybersécurité, investigations)
- "La Méthode Scientifique" (France Culture, technologie)

3. MENSUEL (1 demi-journée/mois)

Documentation officielle :
- Microsoft Learn (parcours Azure, Windows Server)
- Cisco Learning Network (CCNA updates)
- VMware Docs (nouvelles features vSphere)

Webinars éditeurs :
- Veeam Webinars (stratégies backup)
- Palo Alto Networks (cybersécurité)

Certifications :
- Préparation AZ-900 (Azure Fundamentals) en cours
- Labs pratiques Microsoft Learn

4. ANNUEL (conférences)

Événements :
- Microsoft Ignite (novembre, conférences cloud)
- Cisco Live (conférences réseau)
- SSTIC (Symposium sécurité, Rennes) si budget

5. PRATIQUE (HOMELAB)

Mon HomeLab personnel :
- 1 serveur Proxmox (ancien PC Dell)
- 5 VMs : Debian 12, Windows Server 2022, pfSense, GLPI, Zabbix
- Tests technologies avant utilisation pro

EXEMPLE RÉCENT :
"Février 2024 : Microsoft annonce Copilot pour Windows 11.
→ J'ai lu articles Next INpact + documentation Microsoft
→ Testé sur VM Windows 11 (HomeLab)
→ Conclusion : Pas encore mature entreprise, mais à surveiller"

SYNTHÈSE :
"Je consacre 2-3h/semaine à la veille techno (quotidien 15 min + week-end).
C'est essentiel dans l'IT : les technologies évoluent vite, il faut 
rester à jour pour être efficace."
```

---

### **Phase 4 : Questions Transversales et Cas Pratiques Complexes (5-8 minutes)**

Le jury peut poser des questions ouvertes ou des cas pratiques mobilisant plusieurs CCPs.

#### 🎯 QUESTIONS TRANSVERSALES TYPES

**Q1 : "Un utilisateur se plaint que 'tout est lent'. Comment diagnostiquez-vous ?"**

**Réponse méthodologique (multi-CCPs)** :

```
ÉTAPE 1 : QUALIFICATION (CCP1 Support)

Questions utilisateur :
- "Lent depuis quand ?" (installation récente logiciel ?)
- "Lent comment ?" (démarrage PC, applications, navigation web ?)
- "Lent toujours ou par intermittence ?" (pic charge matin 9h ?)
- "Lent pour vous seul ou plusieurs collègues ?" (problème local vs réseau)

ÉTAPE 2 : DIAGNOSTIC LOCAL (CCP2/CCP3)

Tests poste utilisateur :

1. Performances système (CCP2 Windows)
   - Gestionnaire des tâches → CPU/RAM/Disque
   - Processus suspect ? (ex: Chrome.exe 90% CPU)
   - RAM saturée ? (ex: 15,8 Go / 16 Go utilisés)

2. Santé disque (CCP2)
   - CrystalDiskInfo (SMART)
   - Espace libre < 10% ? (fragmentation)
   - Erreurs disque ?

3. Réseau local (CCP3)
   - `ping 192.168.1.1` (passerelle) → latence ?
   - `ping 8.8.8.8` (Internet) → perte paquets ?
   - Test débit : speedtest.net

ÉTAPE 3 : DIAGNOSTIC RÉSEAU (CCP3)

Si plusieurs utilisateurs affectés :

1. Tests serveurs
   - `ping SRV-DC01` → serveur répond ?
   - `Test-NetConnection SRV-FILE01 -Port 445` → SMB accessible ?

2. Tests switch
   - Vérifier switch (port saturé ? erreurs CRC ?)
   - `show interfaces GigabitEthernet 0/1` (Cisco)
   - Input errors ? Collisions ?

3. Tests WAN
   - `ping` fournisseur Internet → latence élevée ?
   - Bande passante saturée ? (QoS configurée ?)

ÉTAPE 4 : DIAGNOSTIC SERVEUR (CCP4/CCP5)

Si problème serveur :

1. Windows Server (CCP4)
   - Event Viewer : erreurs disque ? RAM ?
   - Performance Monitor : CPU sustained 90%+ ?
   - Services stoppés ? (ex: DNS Server arrêté)

2. VM (CCP5)
   - Ressources VM suffisantes ? (4 vCPU / 8 Go RAM)
   - Datastore saturé ? (latence disque VMDK)
   - Snapshots anciens ? (delta disk dégradé)

ÉTAPE 5 : ACTIONS CORRECTIVES

Selon diagnostic :

| CAUSE | ACTION |
|-------|--------|
| Processus gourmand (Chrome 90% CPU) | Fermer onglets, redémarrer navigateur |
| RAM saturée (16 Go insuffisant) | Upgrade RAM 32 Go |
| Disque plein (<10% libre) | Nettoyage (CCleaner, `Disk Cleanup`) |
| Malware (processus suspect) | Scan Malwarebytes, nettoyage |
| Switch port saturé | Vérifier boucle réseau, STP |
| Serveur CPU élevé | Identifier processus (SQL Server ?), optimisation |
| Datastore saturé | Storage vMotion vers datastore avec espace |

ÉTAPE 6 : VALIDATION ET SUIVI (CCP1)

- Test avec utilisateur : "C'est mieux ?"
- Monitoring 24-48h : problème récurrent ?
- Ticket : Clôture avec résolution documentée
- Prévention : Action récurrente ? (ex: nettoyage mensuel)

SYNTHÈSE :
"Diagnostic méthodique en entonnoir : Local → Réseau → Serveur.
Mobilise CCPs 1-5 selon origine problème."
```

---

**Q2 : "Vous devez mettre en place un nouveau site distant connecté au siège. Quelle architecture proposez-vous ?"**

**Réponse architecturale complète** :

```
CONTEXTE HYPOTHÉTIQUE :
- Siège : Paris (50 utilisateurs)
- Site distant : Lyon (15 utilisateurs)
- Besoin : Accès serveurs siège (AD, File Server, ERP)

ARCHITECTURE PROPOSÉE :

┌─────────────────────────────────────────┐
│           SITE SIÈGE (PARIS)            │
├─────────────────────────────────────────┤
│ Internet (Fibre 1 Gbps)                 │
│    ↓                                    │
│ Firewall pfSense (VPN IPsec)            │
│    ↓                                    │
│ Switch Core Cisco SG350                 │
│    ↓                                    │
│ VLAN 10 : Serveurs                      │
│   - SRV-DC01 (AD, DNS, DHCP)            │
│   - SRV-FILE01 (Fichiers partagés)      │
│   - SRV-ERP01 (Application métier)      │
│ VLAN 20 : Utilisateurs (50 PCs)         │
└─────────────────────────────────────────┘
                  ↕ VPN IPsec
┌─────────────────────────────────────────┐
│         SITE DISTANT (LYON)             │
├─────────────────────────────────────────┤
│ Internet (Fibre 100 Mbps)               │
│    ↓                                    │
│ Firewall pfSense (VPN IPsec)            │
│    ↓                                    │
│ Switch Cisco SG200                      │
│    ↓                                    │
│ VLAN 30 : Utilisateurs (15 PCs)         │
│ VLAN 40 : Imprimantes (2 imprimantes)   │
│                                         │
│ OPTION : 1 serveur local (RODC)         │
│   - Réplication AD lecture seule        │
│   - Cache authentification              │
└─────────────────────────────────────────┘

COMPOSANTS TECHNIQUES :

1. CONNECTIVITÉ (CCP3 Réseau)

VPN Site-à-Site IPsec :
- Protocole : IPsec (IKEv2)
- Chiffrement : AES-256-GCM
- Authentification : Pre-Shared Key (ou certificats)
- Tunnel permanent (pas besoin initiation manuelle)

Configuration pfSense siège :
```
Phase 1 :
- Remote Gateway : IP publique Lyon (ex: 203.0.113.50)
- Encryption : AES 256
- Hash : SHA256
- DH Group : 14 (2048 bits)

Phase 2 :
- Local Network : 192.168.10.0/24 (VLAN Serveurs siège)
- Remote Network : 192.168.30.0/24 (VLAN Utilisateurs Lyon)
- Encryption : AES 256 GCM
```

Bande passante :
- WAN siège : 1 Gbps (suffisant)
- WAN Lyon : 100 Mbps (suffisant 15 users)
- QoS : Prioriser trafic VPN (VoIP si téléphonie IP)

2. ACTIVE DIRECTORY (CCP4 Windows)

Option A : Pas de serveur local Lyon (économique)
- Authentifications via VPN vers SRV-DC01 siège
- Avantage : Pas de serveur à maintenir
- Inconvénient : Si VPN coupé, impossibilité connexion

Option B : RODC (Read-Only Domain Controller) Lyon
- VM Windows Server 2022 local Lyon
- Réplication AD unidirectionnelle (lecture seule)
- Cache authentification (fonctionne si VPN coupé temporairement)
- Configuration :
```powershell
# Siège Paris : Promouvoir RODC
Install-ADDSDomainController `
  -DomainName "entreprise.local" `
  -SiteName "Site-Lyon" `
  -ReadOnlyReplica `
  -Credential (Get-Credential)
```

Choix recommandé : **Option B (RODC)** si budget OK
- Coût : ~1500€ (serveur Dell T140 + licences)
- Gain : Résilience si VPN coupé

3. FICHIERS PARTAGÉS (CCP4 Windows + CCP6 Sauvegardes)

Option A : Accès direct serveur siège via VPN
- \\SRV-FILE01\Partages accessible depuis Lyon
- Avantage : Centralisation
- Inconvénient : Lenteur (tout via WAN 100 Mbps)

Option B : DFS Replication (recommandé)
- Serveur local Lyon avec réplication fichiers
- DFS Namespace : \\entreprise.local\Partages
  - Cible 1 : \\SRV-FILE01\Partages (siège)
  - Cible 2 : \\SRV-LYON01\Partages (local)
- Réplication bidirectionnelle (modifications siège ↔ Lyon)
- Configuration :
```powershell
# Créer groupe réplication
New-DfsReplicationGroup -GroupName "Replication-Lyon"
New-DfsReplicatedFolder -GroupName "Replication-Lyon" -FolderName "Partages"

# Ajouter membres
Add-DfsrMember -GroupName "Replication-Lyon" -ComputerName "SRV-FILE01","SRV-LYON01"

# Configurer connexion
Add-DfsrConnection -GroupName "Replication-Lyon" `
  -SourceComputerName "SRV-FILE01" `
  -DestinationComputerName "SRV-LYON01"
```

4. ADRESSAGE IP (CCP3 Réseau)

Plan adressage :
- Siège VLAN 10 Serveurs : 192.168.10.0/24
- Siège VLAN 20 Utilisateurs : 192.168.20.0/24
- Lyon VLAN 30 Utilisateurs : 192.168.30.0/24
- Lyon VLAN 40 Imprimantes : 192.168.40.0/24
- VPN tunnel : 10.0.0.0/30 (point-à-point)

DHCP :
- Siège : SRV-DC01 (scopes VLAN 10, 20)
- Lyon : SRV-LYON01 ou pfSense local (scope VLAN 30, 40)

DNS :
- Tous les clients : DNS primaire = SRV-DC01 (192.168.10.10)
- Lyon : DNS secondaire = SRV-LYON01 (si RODC déployé)

5. SÉCURITÉ (CCP6)

Firewall pfSense :
- Règles strict allow VPN tunnel
- Blocage accès Internet direct serveurs
- IDS/IPS activé (Snort/Suricata)

Accès distant utilisateurs (télétravail) :
- VPN client OpenVPN (certificats)
- MFA avec Duo Security (optionnel)

6. MONITORING ET SAUVEGARDES (CCP6)

Monitoring :
- Centreon ou Zabbix : Superviser VPN (tunnel up/down)
- Alertes : Email si VPN coupé > 5 min

Sauvegardes :
- Veeam Backup : Sauvegarde VMs siège + Lyon
- Réplication Veeam : Copie backups Lyon → Siège (PRA)

COÛTS ESTIMÉS :

| ÉLÉMENT | COÛT |
|---------|------|
| Firewall pfSense Lyon (Netgate SG-2100) | 500€ |
| Switch Cisco SG200-26 Lyon | 300€ |
| Serveur Dell T140 (RODC + File) Lyon | 1500€ |
| Licences Windows Server 2022 | 800€ |
| Licences CAL utilisateurs (15×) | 750€ |
| Câblage réseau Lyon (patch panels) | 400€ |
| **TOTAL** | **4250€** |

PLANNING DÉPLOIEMENT (2 semaines) :

| JOUR | TÂCHE |
|------|-------|
| J1-J2 | Installation switches et câblage Lyon |
| J3 | Configuration VPN IPsec pfSense |
| J4 | Tests connectivité VPN |
| J5-J6 | Installation serveur RODC Lyon |
| J7 | Réplication AD + tests auth |
| J8-J9 | Configuration DFS Replication |
| J10 | Tests fichiers partagés |
| J11 | Configuration DHCP/DNS local |
| J12 | Déploiement postes utilisateurs (15 PCs) |
| J13 | Formation utilisateurs Lyon |
| J14 | Mise en production + monitoring |

SYNTHÈSE :
"Architecture VPN site-à-site avec RODC et DFS Replication.
Mobilise CCPs 3 (réseau), 4 (AD), 6 (sécurité/monitoring).
Coût 4250€, déploiement 2 semaines."


---

## 📊 POSTURE ET ATTITUDE ATTENDUES

### ✅ COMPORTEMENTS VALORISÉS

**1. Confiance et assurance**
- Parler clairement, voix posée
- Assumer ses choix techniques
- Ne pas s'excuser excessivement ("Désolé, mon projet est simple...")

**2. Recul et maturité**
- Identifier limites de son projet
- Proposer améliorations
- Reconnaître erreurs et leçons apprises

**3. Curiosité et veille**
- Mentionner technologies récentes (même si non maîtrisées)
- Poser questions au jury ("Quel retour avez-vous sur Azure vs AWS en PME ?")
- Montrer intérêt pour évolution métier

**4. Communication adaptée**
- Vulgariser si nécessaire ("DFS Replication, c'est comme une synchronisation Dropbox mais pour serveurs")
- Donner exemples concrets
- Éviter jargon inutile

**5. Enthousiasme mesuré**
- Montrer passion pour l'IT
- Sans tomber dans l'excès ("VMware c'est TROP COOL !" ❌)

---

### ❌ COMPORTEMENTS À ÉVITER

**1. Arrogance**
- "Moi je sais tout faire, j'ai rien à apprendre"
- "Mon projet est le meilleur de la promo"
- Critiquer le jury ou la formation

**2. Dévalorisation excessive**
- "Mon projet est nul, j'ai rien fait de bien"
- "Je suis pas sûr d'avoir le niveau"
- Se comparer négativement aux autres

**3. Langue de bois**
- Réponses évasives génériques
- "Ça dépend..." sans développer
- Éviter les questions

**4. Rigidité**
- Refuser d'envisager autres solutions
- "Il n'y a qu'une seule bonne méthode : la mienne"

**5. Désintérêt**
- Réponses monosyllabiques ("Oui", "Non", "Je sais pas")
- Pas de questions au jury
- Regarder sa montre

---

## ⏱️ GESTION DU TEMPS

| PHASE | DURÉE | CHRONO |
|-------|-------|--------|
| Présentation projet | 5-7 min | 00:00 - 00:07 |
| Questions projet | 3-5 min | 00:07 - 00:12 |
| Questions motivation | 5-7 min | 00:12 - 00:19 |
| Questions transversales | 0-3 min | 00:19 - 00:20 |
| **TOTAL** | **20 min** | |

**💡 CONSEILS TIMING** :
- Jury peut interrompre si dépassement (normal)
- Si question longue du jury → Réponse concise
- Garder 2-3 min pour vos questions au jury

---

## 🎯 RÉSUMÉ PARTIE 3A

**Ce fichier a couvert** :
✅ Objectifs entretien final (3 axes évaluation)  
✅ Déroulement détaillé 20 minutes (4 phases)  
✅ Structure présentation projet (7 min avec slides)  
✅ Exemples projets valorisables (3 exemples concrets)  
✅ Questions projet types avec réponses détaillées  
✅ Questions motivation avec réponses argumentées  
✅ Questions transversales multi-CCPs avec méthodologie  
✅ Posture et attitude (comportements à valoriser/éviter)  
✅ Gestion du temps

---

**📅 Date création** : 12 novembre 2025  
**🎓 Certification** : Titre Professionnel TSSR (Niveau 5)  


---

*Vous êtes prêt pour l'entretien final ! La partie 3A pose les fondations : présentation projet structurée, réponses motivation solides, et cas pratiques transversaux. Les parties 3B et 3C détailleront les grilles d'évaluation et le récapitulatif complet.* 💪

**Bon courage ! Vous allez réussir ! 🚀**

---

# 🎯 SIMULATION ORALE TSSR - PARTIE 3B
## Grilles d'Évaluation Détaillées

---

## 📋 SOMMAIRE DES GRILLES

Ce document présente les **3 grilles d'évaluation détaillées** du Jour 2 (Oral) :

1. **Grille MSP Pratique** (60 points) - 45 minutes
2. **Grille Entretien Technique** (60 points) - 45 minutes  
3. **Grille Entretien Final** (30 points) - 20 minutes

**Total Jour 2** : 150 points  
**Seuil validation Jour 2** : 60/150 points (40%)

---

## 🔧 GRILLE 1 : MSP PRATIQUE (60 POINTS)

### 📊 RÉPARTITION GÉNÉRALE

| CRITÈRE | POINTS | POIDS |
|---------|--------|-------|
| **Incident 1** | 18 pts | 30% |
| **Incident 2** | 18 pts | 30% |
| **Incident 3** | 22 pts | 37% |
| **Bonus Actions préventives** | +2 pts | 3% |
| **TOTAL** | **60 pts** | **100%** |

---

### 🚨 INCIDENT 1 : ACCÈS PARTAGÉ RÉSEAU BLOQUÉ (18 POINTS)

**Contexte** :  
Les utilisateurs du groupe "GRP_RH" ne peuvent plus accéder au partage réseau `\\SRV-FILE01\RH_Partage`. Message d'erreur : "Accès refusé".

**Temps imparti** : 12 minutes maximum

---

#### 📋 GRILLE DÉTAILLÉE INCIDENT 1

| CRITÈRE | DÉTAIL | BARÈME | TOTAL |
|---------|--------|--------|-------|
| **1. MÉTHODOLOGIE DIAGNOSTIC** | | | **4 pts** |
| 1.1 Qualification incident | Pose questions pertinentes (depuis quand ? qui est affecté ?) | 1 pt | |
| 1.2 Tests accès local | Teste accès depuis SRV-FILE01 en local (OK) | 1 pt | |
| 1.3 Tests accès réseau | Teste depuis PC utilisateur `\\SRV-FILE01\RH_Partage` | 1 pt | |
| 1.4 Vérification permissions | Identifie besoin vérifier NTFS + SMB | 1 pt | |
| **2. DIAGNOSTIC PRÉCIS** | | | **6 pts** |
| 2.1 Outils utilisés | Utilise outils appropriés (Propriétés dossier, icacls, Get-Acl) | 2 pts | |
| 2.2 Identification cause | Identifie permissions NTFS manquantes pour GRP_RH | 2 pts | |
| 2.3 Vérification SMB | Vérifie permissions partage SMB (OK : "Tout le monde" Contrôle total) | 1 pt | |
| 2.4 Explication cause racine | Explique : "Membre équipe IT a modifié NTFS hier, supprimé GRP_RH par erreur" | 1 pt | |
| **3. RÉSOLUTION TECHNIQUE** | | | **5 pts** |
| 3.1 Commande PowerShell | Utilise `$acl = Get-Acl`, `$rule = New-Object`, `$acl.SetAccessRule()`, `Set-Acl` | 2 pts | |
| 3.2 OU Interface graphique | OU Propriétés → Sécurité → Modifier → Ajouter "GRP_RH" → Lecture/Écriture | 2 pts | |
| 3.3 Application permissions | Applique correctement (case "Remplacer autorisations enfants" cochée) | 1 pt | |
| **4. VALIDATION** | | | **2 pts** |
| 4.1 Test accès utilisateur | Se connecte PC utilisateur RH, teste `\\SRV-FILE01\RH_Partage` → Accès OK | 1 pt | |
| 4.2 Test lecture/écriture | Crée fichier test.txt, enregistre, supprime → Permissions OK | 1 pt | |
| **5. COMMUNICATION** | | | **1 pt** |
| 5.1 Explications claires | Explique au jury chaque étape réalisée | 0,5 pt | |
| 5.2 Vocabulaire technique | Utilise termes corrects (NTFS, ACL, héritages) | 0,5 pt | |

---

#### 🎯 EXEMPLE RÉSOLUTION INCIDENT 1 (18/18)

**Candidat démontre méthodologie complète** :

```powershell
# Étape 1 : Diagnostic - Vérification permissions actuelles
Get-Acl "\\SRV-FILE01\RH_Partage" | Format-List

# Résultat : Groupe GRP_RH absent des ACL NTFS

# Étape 2 : Correction - Ajout permissions NTFS
$acl = Get-Acl "E:\Partages\RH_Partage"
$rule = New-Object System.Security.AccessControl.FileSystemAccessRule(
    "ENTREPRISE\GRP_RH",
    "Modify",
    "ContainerInherit,ObjectInherit",
    "None",
    "Allow"
)
$acl.SetAccessRule($rule)
Set-Acl "E:\Partages\RH_Partage" $acl

# Étape 3 : Validation
Test-Path "\\SRV-FILE01\RH_Partage" -PathType Container
# Résultat : True

# Test création fichier
New-Item "\\SRV-FILE01\RH_Partage\test.txt" -ItemType File
# Résultat : Fichier créé avec succès
```

**Commentaire jury** :  
"Excellent. Méthodologie rigoureuse, diagnostic rapide (3 min), résolution technique maîtrisée (PowerShell), validation complète. 18/18."

---

### 🚨 INCIDENT 2 : VM NE DÉMARRE PLUS APRÈS SNAPSHOT (18 POINTS)

**Contexte** :  
La VM "SRV-GLPI" ne démarre plus après suppression d'un snapshot ancien. Message vSphere : "Fichier .vmdk introuvable".

**Temps imparti** : 15 minutes maximum

---

#### 📋 GRILLE DÉTAILLÉE INCIDENT 2

| CRITÈRE | DÉTAIL | BARÈME | TOTAL |
|---------|--------|--------|-------|
| **1. MÉTHODOLOGIE DIAGNOSTIC** | | | **4 pts** |
| 1.1 Analyse message erreur | Lit message vSphere, identifie fichier .vmdk manquant | 1 pt | |
| 1.2 Vérification datastore | Browse Datastore, liste fichiers VM (trouve .vmx, .vmdk, -000001.vmdk) | 1 pt | |
| 1.3 Hypothèse snapshot | Émet hypothèse : fichier .vmx pointe vers snapshot supprimé | 1 pt | |
| 1.4 Consultation logs | (Optionnel) Consulte /var/log/vmware.log pour détails erreur | 1 pt | |
| **2. DIAGNOSTIC PRÉCIS** | | | **5 pts** |
| 2.1 Connexion SSH ESXi | Se connecte en SSH à l'hôte ESXi hébergeant VM | 2 pts | |
| 2.2 Lecture fichier .vmx | Utilise `cat` ou `vi` pour lire SRV-GLPI.vmx | 1 pt | |
| 2.3 Identification ligne fautive | Identifie : `scsi0:0.fileName = "SRV-GLPI-000001.vmdk"` (snapshot supprimé) | 1 pt | |
| 2.4 Identification fichier correct | Identifie fichier correct : `SRV-GLPI.vmdk` (fichier base présent) | 1 pt | |
| **3. RÉSOLUTION TECHNIQUE** | | | **6 pts** |
| 3.1 Édition fichier .vmx | Utilise `vi SRV-GLPI.vmx` pour éditer | 2 pts | |
| 3.2 Modification ligne | Modifie `scsi0:0.fileName = "SRV-GLPI.vmdk"` (supprime référence snapshot) | 2 pts | |
| 3.3 Sauvegarde fichier | Enregistre (`:wq` dans vi) | 1 pt | |
| 3.4 Réenregistrement VM | Réenregistre VM dans inventaire vCenter (Remove from Inventory → Register VM) | 1 pt | |
| **4. VALIDATION** | | | **2 pts** |
| 4.1 Démarrage VM | Power On VM → Démarre avec succès | 1 pt | |
| 4.2 Vérification services | Se connecte VM, vérifie service GLPI fonctionne (navigateur) | 1 pt | |
| **5. COMMUNICATION** | | | **1 pt** |
| 5.1 Explications SSH | Explique pourquoi SSH nécessaire (accès bas niveau fichiers) | 0,5 pt | |
| 5.2 Prévention future | Mentionne bonne pratique : ne pas garder snapshots >72h | 0,5 pt | |

---

#### 🎯 EXEMPLE RÉSOLUTION INCIDENT 2 (18/18)

**Candidat démontre compétences VMware avancées** :

```bash
# Étape 1 : Connexion SSH ESXi
ssh root@192.168.10.50

# Étape 2 : Navigation dossier VM
cd /vmfs/volumes/DATASTORE01/SRV-GLPI/
ls -lh
# Résultat : SRV-GLPI.vmx, SRV-GLPI.vmdk, SRV-GLPI-000001-delta.vmdk (orphelin)

# Étape 3 : Lecture fichier .vmx
cat SRV-GLPI.vmx | grep vmdk
# Résultat : scsi0:0.fileName = "SRV-GLPI-000001.vmdk"  ← PROBLÈME

# Étape 4 : Édition fichier .vmx
vi SRV-GLPI.vmx
# Modification :
# AVANT : scsi0:0.fileName = "SRV-GLPI-000001.vmdk"
# APRÈS : scsi0:0.fileName = "SRV-GLPI.vmdk"
# Sauvegarde : Échap, :wq, Entrée

# Étape 5 : Réenregistrement VM (depuis vSphere Client)
# Clic droit datastore → Browse Files → SRV-GLPI.vmx → Register VM

# Étape 6 : Démarrage VM
# Power On VM → Succès

# Étape 7 : Validation
# Navigateur : http://192.168.10.100/glpi → Page GLPI accessible
```

**Commentaire jury** :  
"Très bien. Diagnostic précis (5 min), compétences SSH/vi démontrées, explication claire delta disk. Mention prévention snapshots : excellent réflexe. 18/18."

---

### 🚨 INCIDENT 3 : SERVICE WEB APACHE INACCESSIBLE (22 POINTS)

**Contexte** :  
Le site web intranet (`http://intranet.entreprise.local`) retourne erreur 500. Apache est démarré mais site inaccessible.

**Temps imparti** : 18 minutes maximum

---

#### 📋 GRILLE DÉTAILLÉE INCIDENT 3

| CRITÈRE | DÉTAIL | BARÈME | TOTAL |
|---------|--------|--------|-------|
| **1. MÉTHODOLOGIE DIAGNOSTIC** | | | **5 pts** |
| 1.1 Vérification service Apache | `systemctl status apache2` → Active (running) mais erreurs logs | 1 pt | |
| 1.2 Test accès local | `curl http://localhost` depuis serveur → Erreur 500 | 1 pt | |
| 1.3 Test accès réseau | Depuis PC client, teste `http://192.168.10.30` → Erreur 500 | 1 pt | |
| 1.4 Consultation logs Apache | `tail -f /var/log/apache2/error.log` → Messages erreur | 1 pt | |
| 1.5 Identification message clé | Repère message : "Invalid command 'RewriteEngine', module mod_rewrite not enabled" | 1 pt | |
| **2. DIAGNOSTIC PRÉCIS** | | | **6 pts** |
| 2.1 Compréhension erreur | Explique : Module mod_rewrite requis mais désactivé | 2 pts | |
| 2.2 Vérification config VirtualHost | `cat /etc/apache2/sites-available/intranet.conf` → Contient directives RewriteEngine | 1 pt | |
| 2.3 Vérification modules actifs | `apache2ctl -M | grep rewrite` → Module absent | 2 pts | |
| 2.4 Explication cause racine | Explique : "Mise à jour Apache hier a désactivé module, ou migration config sans activation module" | 1 pt | |
| **3. RÉSOLUTION TECHNIQUE** | | | **7 pts** |
| 3.1 Activation module | `a2enmod rewrite` → Module enabled | 2 pts | |
| 3.2 Vérification syntaxe config | `apache2ctl configtest` → Syntax OK | 1 pt | |
| 3.3 Redémarrage Apache | `systemctl restart apache2` → Service redémarre sans erreurs | 2 pts | |
| 3.4 Vérification état service | `systemctl status apache2` → Active, pas d'erreurs logs | 1 pt | |
| 3.5 (Optionnel) Ajout permanent | Comprend que `a2enmod` crée symlink `/etc/apache2/mods-enabled/` (permanent) | 1 pt | |
| **4. VALIDATION** | | | **3 pts** |
| 4.1 Test accès local | `curl http://localhost` → Code 200, HTML retourné | 1 pt | |
| 4.2 Test accès réseau | Depuis PC client, navigateur `http://intranet.entreprise.local` → Site accessible | 1 pt | |
| 4.3 Test fonctionnalités | Teste liens internes site (authentification, formulaires) → Fonctionnels | 1 pt | |
| **5. COMMUNICATION** | | | **1 pt** |
| 5.1 Explications logs | Explique importance lecture logs pour diagnostic | 0,5 pt | |
| 5.2 Vocabulaire Apache | Utilise termes corrects (VirtualHost, module, a2enmod) | 0,5 pt | |

---

#### 🎯 EXEMPLE RÉSOLUTION INCIDENT 3 (22/22)

**Candidat démontre expertise Linux/Apache** :

```bash
# Étape 1 : Vérification service
systemctl status apache2
# Résultat : Active (running) mais "Invalid command 'RewriteEngine'" dans logs

# Étape 2 : Consultation logs détaillés
tail -n 50 /var/log/apache2/error.log
# Message clé : "Invalid command 'RewriteEngine', perhaps misspelled or 
# defined by a module not included in the server configuration"

# Étape 3 : Vérification module rewrite
apache2ctl -M | grep rewrite
# Résultat : (vide) → Module pas chargé

# Étape 4 : Analyse config site
cat /etc/apache2/sites-available/intranet.conf
# Contenu :
# <VirtualHost *:80>
#     ServerName intranet.entreprise.local
#     DocumentRoot /var/www/intranet
#     <Directory /var/www/intranet>
#         RewriteEngine On          ← NÉCESSITE mod_rewrite
#         RewriteRule ^/old-page$ /new-page [R=301,L]
#     </Directory>
# </VirtualHost>

# Étape 5 : Activation module
a2enmod rewrite
# Résultat : Enabling module rewrite. To activate, run: systemctl restart apache2

# Étape 6 : Vérification syntaxe
apache2ctl configtest
# Résultat : Syntax OK

# Étape 7 : Redémarrage service
systemctl restart apache2

# Étape 8 : Vérification état
systemctl status apache2
# Résultat : Active (running), pas d'erreurs

# Étape 9 : Validation accès
curl -I http://localhost
# Résultat : HTTP/1.1 200 OK

# Test depuis client
curl http://intranet.entreprise.local
# Résultat : HTML retourné, site fonctionnel
```

**Commentaire jury** :  
"Parfait. Lecture logs efficace (2 min), diagnostic précis (module manquant), résolution rapide (5 min), tests validation complets. Explique symlinks a2enmod (bonus). 22/22."

---

### 🎁 BONUS : ACTIONS PRÉVENTIVES (2 POINTS)

Le jury peut attribuer jusqu'à **2 points bonus** si le candidat propose spontanément des **actions préventives** pour éviter récurrence incidents.

| ACTION PRÉVENTIVE | POINTS |
|-------------------|--------|
| Propose documentation procédure restauration permissions NTFS | +0,5 pt |
| Mentionne importance rotation snapshots VMware (<72h) | +0,5 pt |
| Suggère monitoring Apache (Centreon, Nagios) pour alertes proactives | +0,5 pt |
| Propose mise en place sauvegarde config Apache (`/etc/apache2/`) | +0,5 pt |
| **TOTAL BONUS** | **+2 pts max** |

---

### 📊 RÉSUMÉ GRILLE MSP PRATIQUE

| INCIDENT | POINTS | TEMPS | FOCUS ÉVALUATION |
|----------|--------|-------|------------------|
| Incident 1 (NTFS) | 18 pts | 12 min | Méthodologie diagnostic, commandes PowerShell/GUI, validation |
| Incident 2 (VMware) | 18 pts | 15 min | SSH ESXi, édition .vmx avec vi, compréhension snapshots |
| Incident 3 (Apache) | 22 pts | 18 min | Lecture logs Linux, activation modules Apache, tests validation |
| **Bonus** | +2 pts | - | Actions préventives spontanées |
| **TOTAL** | **60 pts** | **45 min** | |

**Seuil validation MSP Pratique** : 24/60 points (40%)

---

## 💬 GRILLE 2 : ENTRETIEN TECHNIQUE (60 POINTS)

### 📊 RÉPARTITION GÉNÉRALE

| CCP | QUESTIONS | POINTS | POIDS |
|-----|-----------|--------|-------|
| **CCP1 Support utilisateur** | 5 questions | 8 pts | 13% |
| **CCP2 Maintenance Linux** | 6 questions | 10 pts | 17% |
| **CCP3 Services réseau** | 5 questions | 10 pts | 17% |
| **CCP4 Windows/AD** | 5 questions | 10 pts | 17% |
| **CCP5 Virtualisation** | 4 questions | 8 pts | 13% |
| **CCP6 Sécurité/Sauvegardes** | 3 questions | 6 pts | 10% |
| **Méthodologie** | Transversal | 4 pts | 7% |
| **Communication** | Transversal | 4 pts | 7% |
| **TOTAL** | **31 questions** | **60 pts** | **100%** |

---

### 🎯 GRILLE DÉTAILLÉE PAR CCP

---

#### **CCP1 : SUPPORT UTILISATEUR (8 POINTS)**

| QUESTION | CRITÈRES ÉVALUATION | BARÈME |
|----------|---------------------|--------|
| **Q1.1 : Méthodologie "ordinateur lent"** | | **2 pts** |
| - Qualification incident | Pose 4 questions qualifiantes (quand, quoi, qui, fréquence) | 0,5 pt |
| - Tests diagnostic | Mentionne 3 tests (Gestionnaire tâches, RAM, réseau) | 0,5 pt |
| - Actions correctives | Propose 2-3 actions selon diagnostic (fermer processus, upgrade RAM, nettoyage) | 0,5 pt |
| - Suivi | Mentionne validation avec utilisateur + ticket clôture | 0,5 pt |
| **Q1.2 : Communication non-technique "Qu'est-ce qu'une adresse IP ?"** | | **1,5 pts** |
| - Analogie simple | Utilise analogie (adresse postale, numéro téléphone) | 0,5 pt |
| - Explications claires | Évite jargon (pas de "couche 3 OSI"), langage accessible | 0,5 pt |
| - Exemple concret | Donne exemple (192.168.1.10 = votre PC, 192.168.1.1 = box Internet) | 0,5 pt |
| **Q1.3 : Priorisation 3 tickets simultanés** | | **2 pts** |
| - Utilisation matrice | Mentionne matrice impact/urgence | 0,5 pt |
| - Priorisation correcte | P1 : Serveur ERP down (impact élevé, urgence haute) | 0,5 pt |
| | P2 : PC directeur lent (impact moyen, urgence haute) | 0,5 pt |
| | P3 : Imprimante couleur (impact faible, urgence basse) | 0,5 pt |
| **Q1.4 : ITIL incident vs demande de service** | | **1,5 pts** |
| - Définition incident | "Interruption non planifiée service" (ex: serveur down) | 0,5 pt |
| - Définition demande | "Besoin utilisateur standard" (ex: nouvel accès, logiciel) | 0,5 pt |
| - Différence traitement | Incident = résolution urgente, Demande = workflow approbation | 0,5 pt |
| **Q1.5 : Importance documentation tickets** | | **1 pt** |
| - Traçabilité | Historique actions, responsabilité, audit | 0,5 pt |
| - Base connaissances | Réutilisation solutions incidents similaires | 0,5 pt |

**TOTAL CCP1** : **8 points**

---

#### **CCP2 : MAINTENANCE SYSTÈMES LINUX (10 POINTS)**

| QUESTION | CRITÈRES ÉVALUATION | BARÈME |
|----------|---------------------|--------|
| **Q2.1 : 5 commandes Linux essentielles** | | **2 pts** |
| - Commande 1 | `ls -lh` (lister fichiers détails) | 0,4 pt |
| - Commande 2 | `grep` (rechercher texte fichiers) | 0,4 pt |
| - Commande 3 | `systemctl status/restart` (gérer services) | 0,4 pt |
| - Commande 4 | `df -h` ou `du -sh` (espace disque) | 0,4 pt |
| - Commande 5 | `tail -f /var/log/syslog` (consulter logs temps réel) | 0,4 pt |
| **Q2.2 : Gestion logs diagnostic problème réseau** | | **2 pts** |
| - Fichiers logs pertinents | `/var/log/syslog`, `/var/log/kern.log`, `/var/log/auth.log` | 0,5 pt |
| - Commandes consultation | `tail`, `less`, `grep` avec exemples | 0,5 pt |
| - Exemple recherche | `grep "error" /var/log/syslog` ou `journalctl -xe` | 0,5 pt |
| - Interprétation | Explique comment identifier cause (messages erreur, timestamps) | 0,5 pt |
| **Q2.3 : Permissions chmod 755** | | **2 pts** |
| - Décomposition notation octale | 7 (propriétaire), 5 (groupe), 5 (autres) | 0,5 pt |
| - Conversion binaire/symbolique | 7 = rwx (4+2+1), 5 = r-x (4+0+1) | 1 pt |
| - Résultat final | Propriétaire : lecture/écriture/exécution, Groupe/Autres : lecture/exécution | 0,5 pt |
| **Q2.4 : Sauvegarde tar avec rotation** | | **2 pts** |
| - Commande tar création | `tar -czf backup-$(date +%Y%m%d).tar.gz /etc --exclude=/etc/backup` | 1 pt |
| - Options expliquées | `-c` create, `-z` gzip, `-f` file, `--exclude` exclusions | 0,5 pt |
| - Rotation automatique | Script supprime backups >7 jours : `find /backup -mtime +7 -delete` | 0,5 pt |
| **Q2.5 : Processus kill -15 vs -9** | | **1 pt** |
| - kill -15 (SIGTERM) | Signal gracieux, processus peut nettoyer avant arrêt | 0,5 pt |
| - kill -9 (SIGKILL) | Signal forcé immédiat, pas de nettoyage (dernier recours) | 0,5 pt |
| **Q2.6 : Automatisation cron** | | **1 pt** |
| - Syntaxe 5 champs | `minute heure jour mois jour_semaine commande` | 0,5 pt |
| - Exemple concret | `0 3 * * * /usr/local/bin/backup.sh` (tous les jours 3h) | 0,5 pt |

**TOTAL CCP2** : **10 points**

---

#### **CCP3 : SERVICES RÉSEAU (10 POINTS)**

| QUESTION | CRITÈRES ÉVALUATION | BARÈME |
|----------|---------------------|--------|
| **Q3.1 : Subnetting CIDR 172.16.0.0/16 en 4 sous-réseaux** | | **3 pts** |
| - Calcul nouveau masque | /16 → /18 (2 bits empruntés : 2² = 4 sous-réseaux) | 1 pt |
| - Incrément | Incrément = 256 - 192 = 64 (3ème octet) | 0,5 pt |
| - 4 plages correctes | 172.16.0.0/18, 172.16.64.0/18, 172.16.128.0/18, 172.16.192.0/18 | 1 pt |
| - Plage hôtes | Ex: 172.16.0.0/18 = 172.16.0.1 à 172.16.63.254 (16382 hôtes) | 0,5 pt |
| **Q3.2 : DNS zones principale vs secondaire** | | **2 pts** |
| - Zone principale | Master, lecture/écriture, modifications directes | 0,5 pt |
| - Zone secondaire | Slave, lecture seule, transfert AXFR depuis principale | 0,5 pt |
| - Avantages secondaire | Haute disponibilité, répartition charge, redondance géographique | 0,5 pt |
| - Configuration | Mentionne transfert zone AXFR, intervalle refresh/retry | 0,5 pt |
| **Q3.3 : DHCP options essentielles** | | **2 pts** |
| - Option 001 | Masque sous-réseau (255.255.255.0) | 0,5 pt |
| - Option 003 | Passerelle par défaut (192.168.1.1) | 0,5 pt |
| - Option 006 | Serveurs DNS (ex: 8.8.8.8, 1.1.1.1) | 0,5 pt |
| - Configuration Windows | Exemple PowerShell `Set-DhcpServerv4OptionValue` ou GUI | 0,5 pt |
| **Q3.4 : VLANs avantages** | | **1,5 pts** |
| - Segmentation sécurité | Isolation départements (VLAN10 RH, VLAN30 Invités) | 0,5 pt |
| - Optimisation performances | Réduction broadcasts, QoS par VLAN (VoIP prioritaire) | 0,5 pt |
| - Exemple concret | Architecture avec 3-4 VLANs (Serveurs, Users, DMZ, Invités) | 0,5 pt |
| **Q3.5 : Routage inter-VLAN** | | **1,5 pts** |
| - Méthode 1 : Router-on-a-stick | Sous-interfaces, encapsulation dot1Q, 1 câble physique | 0,5 pt |
| - Méthode 2 : Switch Layer 3 SVI | `ip routing`, interfaces VLAN, performance supérieure | 0,5 pt |
| - Comparaison | Router = simple mais goulot, Switch L3 = rapide mais coûteux | 0,5 pt |

**TOTAL CCP3** : **10 points**

---

#### **CCP4 : WINDOWS / ACTIVE DIRECTORY (10 POINTS)**

| QUESTION | CRITÈRES ÉVALUATION | BARÈME |
|----------|---------------------|--------|
| **Q4.1 : Structure AD hiérarchie** | | **2 pts** |
| - Forêt | Conteneur racine, limite sécurité, schéma commun | 0,5 pt |
| - Domaine | Limite administrative, réplication, politiques | 0,5 pt |
| - OU (Unités Organisationnelles) | Conteneurs personnalisables, GPO applicables | 0,5 pt |
| - Commandes PowerShell | `New-ADOrganizationalUnit`, `New-ADUser`, `New-ADGroup` | 0,5 pt |
| **Q4.2 : GPO blocage USB** | | **2,5 pts** |
| - Chemin GPO | Computer Config → Admin Templates → System → Removable Storage Access | 1 pt |
| - Paramètres | "Deny read/write access" activé | 0,5 pt |
| - Application | `gpupdate /force`, redémarrage poste | 0,5 pt |
| - Exceptions | Filtrage sécurité GPO, groupe "GRP_Admins" autorisé | 0,5 pt |
| **Q4.3 : DNS Windows enregistrement A** | | **2 pts** |
| - Méthode GUI | DNS Manager → New Host (A or AAAA) | 0,5 pt |
| - PowerShell | `Add-DnsServerResourceRecordA -Name "intranet" -IPv4Address "192.168.10.30"` | 1 pt |
| - Validation | `nslookup intranet` ou `Resolve-DnsName` | 0,5 pt |
| **Q4.4 : Permissions NTFS vs Partage** | | **2 pts** |
| - NTFS | Local + réseau, granulaire (Lecture/Écriture/Modification/Contrôle total) | 0,5 pt |
| - Partage SMB | Réseau uniquement, simple (Lecture/Modification/Contrôle total) | 0,5 pt |
| - Règle cumulative | Plus restrictives gagnent (NTFS Lecture + SMB Contrôle = Lecture finale) | 0,5 pt |
| - Bonne pratique | Partage "Tout le monde Contrôle total", gérer via NTFS uniquement | 0,5 pt |
| **Q4.5 : PowerShell AD Get-ADUser** | | **1,5 pts** |
| - Commande filtre | `Get-ADUser -Filter {Enabled -eq $false}` (comptes désactivés) | 0,5 pt |
| - SearchBase | `-SearchBase "OU=Utilisateurs,DC=entreprise,DC=local"` | 0,5 pt |
| - Export CSV | `Export-Csv "comptes_desactives.csv"` | 0,5 pt |

**TOTAL CCP4** : **10 points**

---

#### **CCP5 : VIRTUALISATION VMWARE (8 POINTS)**

| QUESTION | CRITÈRES ÉVALUATION | BARÈME |
|----------|---------------------|--------|
| **Q5.1 : Snapshots VMware** | | **2 pts** |
| - Définition | État instantané VM, delta disk (-000001.vmdk) | 0,5 pt |
| - Bonnes pratiques | Durée max 72h, max 2-3 snapshots/VM, pas production | 0,5 pt |
| - Limitations | Pas un backup (même datastore), dégradation performances | 0,5 pt |
| - Différence backup | Snapshot = dépannage court terme, Backup = protection long terme | 0,5 pt |
| **Q5.2 : vMotion et vSphere HA** | | **2,5 pts** |
| - vMotion définition | Migration à chaud VM entre hôtes, 0 downtime | 0,5 pt |
| - vMotion prérequis | vCenter, réseau dédié Gigabit+, stockage partagé SAN | 0,5 pt |
| - HA définition | Redémarrage auto VMs après panne hôte, 2-5 min downtime | 0,5 pt |
| - HA fonctionnement | Heartbeats, détection 15 sec, redémarrage hôtes survivants | 0,5 pt |
| - Différence | vMotion = migration planifiée, HA = récupération automatique panne | 0,5 pt |
| **Q5.3 : Types disques virtuels** | | **2 pts** |
| - Thick Lazy Zeroed | Espace réservé, zeroing à la demande, Dev/Test | 0,5 pt |
| - Thick Eager Zeroed | Espace réservé + zeroing immédiat, production BDD (performances) | 0,5 pt |
| - Thin Provisioning | Allocation à la demande, économie espace, write penalty | 0,5 pt |
| - Recommandation BDD | Eager Zeroed (performances constantes, prévisibilité, FT compatible) | 0,5 pt |
| **Q5.4 : Datastores VMFS vs NFS** | | **1,5 pts** |
| - VMFS | Système fichiers bloc (SAN iSCSI/FC), performances excellentes | 0,5 pt |
| - NFS | Système fichiers réseau (NAS), overhead réseau, flexibilité | 0,5 pt |
| - VMFS6 nouveautés | 1 Mo bloc, 64 hôtes, 64 To datastore, UNMAP auto | 0,5 pt |

**TOTAL CCP5** : **8 points**

---

#### **CCP6 : SÉCURITÉ / SAUVEGARDES (6 POINTS)**

| QUESTION | CRITÈRES ÉVALUATION | BARÈME |
|----------|---------------------|--------|
| **Q6.1 : Stratégie GFS** | | **2 pts** |
| - 3 niveaux | Son (quotidien 5), Father (hebdo 4), Grandfather (mensuel 12) | 0,5 pt |
| - Calcul supports | 5 + 4 + 12 = 21 supports minimum | 0,5 pt |
| - Avantages | Granularité récupération, économie supports, conformité | 0,5 pt |
| - Règle 3-2-1-1-0 | 3 copies, 2 supports, 1 off-site, 1 offline/immutable, 0 erreur | 0,5 pt |
| **Q6.2 : RTO / RPO** | | **2 pts** |
| - RTO définition | Recovery Time Objective = durée max indisponibilité | 0,5 pt |
| - RPO définition | Recovery Point Objective = perte données max acceptable | 0,5 pt |
| - Exemple e-commerce | RTO 15 min (70€/min CA), RPO 1h (sauvegardes horaires) | 0,5 pt |
| - Solutions techniques | Cluster HA (<1 min), Veeam Instant Recovery (10 min), Réplication BDD (<1 min) | 0,5 pt |
| **Q6.3 : Protection ransomware** | | **2 pts** |
| - Prévention | Filtrage emails, Antivirus EDR, Segmentation VLAN, Pas admin local, MAJ | 0,5 pt |
| - Détection | Monitoring comportemental (SIEM), Honeypot files, Alertes backup | 0,5 pt |
| - Récupération (crucial) | Backups immutables (Veeam, S3 Object Lock), Backups offline (bandes LTO) | 0,5 pt |
| - Règle 3-2-1-1-0 | Tests restauration mensuels, architecture air-gap | 0,5 pt |

**TOTAL CCP6** : **6 points**

---

### 📊 CRITÈRES TRANSVERSAUX (8 POINTS)

| CRITÈRE | DÉTAIL | BARÈME |
|---------|--------|--------|
| **MÉTHODOLOGIE (4 pts)** | | |
| Structuration réponses | Utilise méthode DESC (Définition/Exemple/Spécificités/Conclusion) | 1,5 pt |
| Exemples concrets | Illustre réponses avec expériences personnelles (projets, stages) | 1,5 pt |
| Gestion erreurs | Admet lacunes honnêtement, montre capacité apprentissage | 1 pt |
| **COMMUNICATION (4 pts)** | | |
| Clarté expression | Vocabulaire technique correct, phrases structurées | 1,5 pt |
| Écoute active | Reformule questions si besoin, demande précisions | 1 pt |
| Vulgarisation | Utilise analogies pour concepts complexes | 1,5 pt |

**TOTAL TRANSVERSAL** : **8 points**

---

### 📊 RÉSUMÉ GRILLE ENTRETIEN TECHNIQUE

| CATÉGORIE | POINTS | POIDS |
|-----------|--------|-------|
| Connaissances CCP1-6 | 52 pts | 87% |
| Méthodologie | 4 pts | 7% |
| Communication | 4 pts | 7% |
| **TOTAL** | **60 pts** | **100%** |

**Seuil validation Entretien Technique** : 24/60 points (40%)

---

## 🎤 GRILLE 3 : ENTRETIEN FINAL (30 POINTS)

### 📊 RÉPARTITION GÉNÉRALE

| CRITÈRE | POINTS | POIDS |
|---------|--------|-------|
| **1. Projet Personnel** | 10 pts | 33% |
| **2. Motivation / Perspectives** | 10 pts | 33% |
| **3. Communication / Professionnalisme** | 10 pts | 33% |
| **TOTAL** | **30 pts** | **100%** |

---

### 📋 GRILLE DÉTAILLÉE ENTRETIEN FINAL

---

#### **1. PROJET PERSONNEL (10 POINTS)**

| CRITÈRE | DÉTAIL | BARÈME |
|---------|--------|--------|
| **1.1 Contexte et problématique** | | **2 pts** |
| - Cadre projet | Contexte clair (stage, formation, personnel) | 0,5 pt |
| - Problématique identifiée | Besoin métier réel, pas projet "pour faire un projet" | 0,5 pt |
| - Objectifs définis | Objectifs SMART (Spécifiques, Mesurables, Atteignables) | 0,5 pt |
| - Périmètre | Envergure adaptée (ni trop simple, ni irréaliste) | 0,5 pt |
| **1.2 Réalisation technique** | | **3 pts** |
| - Technologies utilisées | Au moins 3 CCPs couverts (ex: Windows/AD + Réseau + Virtualisation) | 1 pt |
| - Architecture | Schéma réseau ou architecture présenté (visuel ou oral) | 0,5 pt |
| - Méthodologie | Approche structurée (phases : étude, tests, production) | 0,5 pt |
| - Difficultés rencontrées | Identifie 2-3 difficultés concrètes (techniques, organisationnelles) | 0,5 pt |
| - Solutions apportées | Explique comment difficultés résolues (méthode STAR) | 0,5 pt |
| **1.3 Résultats et livrables** | | **2 pts** |
| - Livrables concrets | Documentation technique, scripts, procédures | 0,5 pt |
| - Tests validation | Tests fonctionnels réalisés, résultats mesurables | 0,5 pt |
| - Retours utilisateurs | (Si applicable) Feedback positif utilisateurs finaux | 0,5 pt |
| - Quantification bénéfices | Chiffres (RTO réduit 24h→2h, 15h temps gagné, 50% coûts économisés) | 0,5 pt |
| **1.4 Recul et perspectives** | | **2 pts** |
| - Compétences développées | Identifie 3-5 compétences acquises/renforcées | 0,5 pt |
| - Améliorations possibles | Critique constructive, propose 2-3 améliorations | 0,5 pt |
| - Leçons apprises | Identifie leçons (techniques + soft skills : gestion temps, communication) | 0,5 pt |
| - Évolutions futures | Envisage évolutions projet (monitoring, HA, migration cloud) | 0,5 pt |
| **1.5 Support visuel (optionnel)** | | **1 pt bonus** |
| - Slides professionnelles | PowerPoint/PDF clair, 5-8 slides, pas de fautes | 0,5 pt |
| - Schémas/captures | Architecture réseau, captures écran, tableaux comparatifs | 0,5 pt |

**TOTAL PROJET** : **10 points** (+ 1 pt bonus max)

---

#### **2. MOTIVATION ET PERSPECTIVES (10 POINTS)**

| CRITÈRE | DÉTAIL | BARÈME |
|---------|--------|--------|
| **2.1 Motivation métier TSSR** | | **3 pts** |
| - Passion informatique | Explique origine intérêt IT (anecdote personnelle convaincante) | 1 pt |
| - Attrait technique | Montre intérêt pour dimension technique/concrète (pas juste théorique) | 0,5 pt |
| - Diversité missions | Valorise polyvalence TSSR (systèmes, réseau, support, projets) | 0,5 pt |
| - Cohérence parcours | Lien logique entre parcours antérieur et choix TSSR | 0,5 pt |
| - Impact métier | Comprend importance rôle TSSR dans entreprise | 0,5 pt |
| **2.2 Objectifs professionnels** | | **3 pts** |
| - Court terme (1-2 ans) | Objectifs réalistes : poste technicien, certifications (AZ-900, CCNA) | 1 pt |
| - Moyen terme (3-5 ans) | Évolution cohérente : admin sys, spécialisation (cloud, sécurité) | 1 pt |
| - Long terme (5+ ans) | (Optionnel) Perspectives management ou expertise technique | 0,5 pt |
| - Plan d'action | Actions concrètes identifiées (formations, certifications, expériences) | 0,5 pt |
| **2.3 Veille technologique** | | **2 pts** |
| - Sources veille | Cite 3-5 sources (sites, newsletters, YouTube, podcasts) | 0,5 pt |
| - Exemples récents | Mentionne évolution techno récente suivie (ex: Copilot Windows 11) | 0,5 pt |
| - Pratique HomeLab | (Bonus) HomeLab personnel pour tests (Proxmox, VMs) | 0,5 pt |
| - Routine veille | Fréquence régulière (15 min/jour, 2h/semaine) | 0,5 pt |
| **2.4 Connaissance secteur IT** | | **2 pts** |
| - Tendances marché | Identifie 2-3 tendances (cloud, cybersécurité, IA, automatisation) | 0,5 pt |
| - Certifications éditeurs | Connaît certifications valorisées (Microsoft, Cisco, VMware, Linux) | 0,5 pt |
| - Types entreprises | Distingue PME/ETI/Grands groupes, ESN vs client final | 0,5 pt |
| - Salaires indicatifs | Connaît fourchettes salaires TSSR (25-32k€ junior, 35-45k€ confirmé) | 0,5 pt |

**TOTAL MOTIVATION** : **10 points**

---

#### **3. COMMUNICATION ET PROFESSIONNALISME (10 POINTS)**

| CRITÈRE | DÉTAIL | BARÈME |
|---------|--------|--------|
| **3.1 Qualité communication orale** | | **3 pts** |
| - Clarté expression | Phrases structurées, vocabulaire adapté, pas de tics verbaux excessifs | 1 pt |
| - Rythme débit | Parle posément (ni trop rapide, ni trop lent) | 0,5 pt |
| - Volume voix | Voix audible, assurée (pas trop faible) | 0,5 pt |
| - Gestion silence | Accepte silences réflexion (ne comble pas par "euh" incessants) | 0,5 pt |
| - Reformulation | Reformule questions jury si besoin clarification | 0,5 pt |
| **3.2 Langage corporel** | | **2 pts** |
| - Contact visuel | Regarde les 3 jurys équitablement | 0,5 pt |
| - Posture | Posture droite, mains posées (pas croisées bras) | 0,5 pt |
| - Gestuelle | Gestes modérés pour illustrer propos (pas figé, pas excessif) | 0,5 pt |
| - Sourire | Sourire naturel, attitude avenante | 0,5 pt |
| **3.3 Attitude professionnelle** | | **3 pts** |
| - Ponctualité | Arrivée 15 min avance, prêt à l'heure convoqué | 0,5 pt |
| - Présentation | Tenue adaptée (smart casual minimum), soignée | 0,5 pt |
| - Politesse | Vouvoiement, "Bonjour", "Merci", "Au revoir" | 0,5 pt |
| - Confiance mesurée | Assurance sans arrogance, humilité sans dévalorisation | 0,5 pt |
| - Écoute active | Écoute questions complètement avant répondre, hochements tête | 0,5 pt |
| - Gestion stress | Respire, prend temps réfléchir, gère erreurs calmement | 0,5 pt |
| **3.4 Questions au jury** | | **1 pt** |
| - Pose questions pertinentes | 1-2 questions intelligentes en fin entretien (pas salaire) | 0,5 pt |
| - Curiosité | Questions montrent intérêt métier (retours amélioration, technologies) | 0,5 pt |
| **3.5 Cohérence dossier professionnel** | | **1 pt** |
| - Cohérence DP/oral | Réponses orales cohérentes avec dossier professionnel écrit | 0,5 pt |
| - Exemples détaillés | Approfondit projets mentionnés dans DP | 0,5 pt |

**TOTAL COMMUNICATION** : **10 points**

---

### 📊 RÉSUMÉ GRILLE ENTRETIEN FINAL

| CATÉGORIE | POINTS | POIDS |
|-----------|--------|-------|
| Projet personnel | 10 pts | 33% |
| Motivation / Perspectives | 10 pts | 33% |
| Communication / Professionnalisme | 10 pts | 33% |
| **TOTAL** | **30 pts** | **100%** |

**Seuil validation Entretien Final** : 12/30 points (40%)

---

## 📊 ÉCHELLES DE NOTATION

### 🎯 ÉCHELLE GLOBALE (Toutes grilles)

| NOTE | APPRÉCIATION | SIGNIFICATION |
|------|--------------|---------------|
| **90-100%** | Excellent | Maîtrise exceptionnelle, aucune lacune, autonomie totale |
| **75-89%** | Très bien | Maîtrise solide, lacunes mineures, autonomie forte |
| **60-74%** | Bien | Compétences acquises, quelques imprécisions, autonomie correcte |
| **40-59%** | Passable | Niveau minimum, lacunes significatives, besoin accompagnement |
| **<40%** | Insuffisant | Compétences non acquises, nombreuses lacunes |

---

### 🎯 SEUILS VALIDATION PAR ÉPREUVE

| ÉPREUVE | POINTS TOTAL | SEUIL 40% | SEUIL 60% | SEUIL 80% |
|---------|--------------|-----------|-----------|-----------|
| **MSP Pratique** | 60 pts | 24 pts | 36 pts | 48 pts |
| **Entretien Technique** | 60 pts | 24 pts | 36 pts | 48 pts |
| **Entretien Final** | 30 pts | 12 pts | 18 pts | 24 pts |
| **TOTAL JOUR 2** | **150 pts** | **60 pts** | **90 pts** | **120 pts** |

---

### 🎯 PROFILS CANDIDATS TYPES

**Profil A : "Technicien autonome"** ✅ EXCELLENT
- MSP Pratique : 55/60 (92%)
- Entretien Technique : 52/60 (87%)
- Entretien Final : 27/30 (90%)
- **Total** : 134/150 (89%) → Certification avec mention

**Profil B : "Technicien confirmé"** ✅ TRÈS BIEN
- MSP Pratique : 48/60 (80%)
- Entretien Technique : 45/60 (75%)
- Entretien Final : 24/30 (80%)
- **Total** : 117/150 (78%) → Certification solide

**Profil C : "Technicien débutant"** ✅ BIEN
- MSP Pratique : 38/60 (63%)
- Entretien Technique : 38/60 (63%)
- Entretien Final : 20/30 (67%)
- **Total** : 96/150 (64%) → Certification acquise

**Profil D : "Technicien juste"** ⚠️ PASSABLE
- MSP Pratique : 26/60 (43%)
- Entretien Technique : 26/60 (43%)
- Entretien Final : 14/30 (47%)
- **Total** : 66/150 (44%) → Certification limite (besoin 60/150)

**Profil E : "Technicien insuffisant"** ❌ ÉCHEC
- MSP Pratique : 18/60 (30%)
- Entretien Technique : 20/60 (33%)
- Entretien Final : 10/30 (33%)
- **Total** : 48/150 (32%) → Certification non obtenue

---

## 🎯 CONSEILS POUR MAXIMISER VOTRE SCORE

### ✅ MSP PRATIQUE (60 pts)

**Pour obtenir 24/60 (Passable)** :
- Résoudre au moins 1 incident complètement (18 pts)
- Diagnostic partiel sur 2 autres incidents (6 pts)

**Pour obtenir 48/60 (Excellent)** :
- Résoudre 2 incidents complètement (36 pts)
- Diagnostic + début résolution 3ème incident (10 pts)
- Bonus actions préventives (2 pts)

---

### ✅ ENTRETIEN TECHNIQUE (60 pts)

**Pour obtenir 24/60 (Passable)** :
- Répondre correctement à 40% des questions (12/31 questions)
- Focus CCPs forts (prioriser 2-3 CCPs maîtrisés)

**Pour obtenir 48/60 (Excellent)** :
- Répondre correctement à 80% des questions (25/31 questions)
- Méthodologie structurée (DESC)
- Exemples concrets systématiques

---

### ✅ ENTRETIEN FINAL (30 pts)

**Pour obtenir 12/30 (Passable)** :
- Projet personnel cohérent avec CCPs (4/10 pts)
- Motivation sincère (4/10 pts)
- Communication acceptable (4/10 pts)

**Pour obtenir 24/30 (Excellent)** :
- Projet technique solide avec livrables (8/10 pts)
- Motivation argumentée + veille active (8/10 pts)
- Communication professionnelle irréprochable (8/10 pts)

---

## 📊 RÉCAPITULATIF GRILLES PARTIE 3B

**Ce fichier a couvert** :
✅ Grille MSP Pratique détaillée (60 pts : 3 incidents + bonus)  
✅ Grille Entretien Technique détaillée (60 pts : 31 questions réparties 6 CCPs)  
✅ Grille Entretien Final détaillée (30 pts : Projet + Motivation + Communication)  
✅ Échelles notation (Excellent → Insuffisant)  
✅ Seuils validation par épreuve (40%, 60%, 80%)  
✅ Profils candidats types (5 exemples A à E)  
✅ Conseils maximisation score par épreuve

---

**📅 Date création** : 12 novembre 2025  
**🎓 Certification** : Titre Professionnel TSSR (Niveau 5)  

---

*Vous comprenez maintenant comment le jury vous évalue ! La partie 3C regroupera les barèmes globaux (écrit + oral), le timing complet du Jour 2, et les derniers conseils pour maximiser vos chances.* 💪

**Continuez comme ça, c'est excellent ! 🚀**

---

# 🎯 SIMULATION ORALE TSSR - PARTIE 3C
## Barèmes Globaux + Récapitulatif Complet + Conseils Finaux

---

## 📊 BARÈME GLOBAL CERTIFICATION TSSR (350 POINTS)

### 🎓 STRUCTURE COMPLÈTE DE LA CERTIFICATION

| JOUR | ÉPREUVE | DURÉE | POINTS | POIDS |
|------|---------|-------|--------|-------|
| **JOUR 1 : ÉCRIT** | | | | |
| MSP Écrite | 1h30 | 100 pts | 29% |
| Questionnaire Technique | 2h00 | 100 pts | 29% |
| **SOUS-TOTAL JOUR 1** | **3h30** | **200 pts** | **57%** |
| **JOUR 2 : ORAL** | | | | |
| MSP Pratique | 45 min | 60 pts | 17% |
| Entretien Technique | 45 min | 60 pts | 17% |
| Entretien Final | 20 min | 30 pts | 9% |
| **SOUS-TOTAL JOUR 2** | **1h50** | **150 pts** | **43%** |
| **TOTAL CERTIFICATION** | **5h20** | **350 pts** | **100%** |

---

### 🎯 SEUILS DE VALIDATION

| NIVEAU | POINTS | POURCENTAGE | STATUT |
|--------|--------|-------------|--------|
| **Seuil minimal certification** | 210/350 | 60% | ✅ Certification obtenue |
| **Seuil échec** | <210/350 | <60% | ❌ Certification non obtenue |
| **Seuil excellence** | ≥280/350 | ≥80% | 🏆 Certification avec mention |

---

### 📊 RÉPARTITION DÉTAILLÉE PAR CCP

| CCP | JOUR 1 ÉCRIT | JOUR 2 ORAL | TOTAL | POIDS |
|-----|--------------|-------------|-------|-------|
| **CCP1 Support utilisateur** | 25 pts | 8 pts | 33 pts | 9% |
| **CCP2 Maintenance Linux** | 30 pts | 10 pts | 40 pts | 11% |
| **CCP3 Services réseau** | 40 pts | 10 pts | 50 pts | 14% |
| **CCP4 Windows/AD** | 35 pts | 10 pts | 45 pts | 13% |
| **CCP5 Virtualisation** | 25 pts | 8 pts | 33 pts | 9% |
| **CCP6 Sécurité/Sauvegardes** | 20 pts | 6 pts | 26 pts | 7% |
| **CCP7 Scripting** | 15 pts | 0 pt | 15 pts | 4% |
| **CCP8 Supervision** | 10 pts | 0 pt | 10 pts | 3% |
| **Méthodologie/Communication** | 0 pt | 8 pts | 8 pts | 2% |
| **MSP Pratique (incidents)** | 0 pt | 60 pts | 60 pts | 17% |
| **Entretien Final (projet)** | 0 pt | 30 pts | 30 pts | 9% |
| **TOTAL** | **200 pts** | **150 pts** | **350 pts** | **100%** |

---

### 🎯 VALIDATION PAR CCP (CCPs SÉPARABLES)

Le titre TSSR permet la **validation par CCP** (Certificat de Compétences Professionnelles). Chaque CCP peut être obtenu indépendamment.

| CCP | POINTS TOTAL | SEUIL 60% | VALIDATION |
|-----|--------------|-----------|------------|
| **CCP1 Support utilisateur** | 33 pts | 20 pts | ✅ Séparable |
| **CCP2 Maintenance Linux** | 40 pts | 24 pts | ✅ Séparable |
| **CCP3 Services réseau** | 50 pts | 30 pts | ✅ Séparable |
| **CCP4 Windows/AD** | 45 pts | 27 pts | ✅ Séparable |
| **CCP5 Virtualisation** | 33 pts | 20 pts | ✅ Séparable |
| **CCP6 Sécurité/Sauvegardes** | 26 pts | 16 pts | ✅ Séparable |
| **CCP7 Scripting** | 15 pts | 9 pts | ✅ Séparable |
| **CCP8 Supervision** | 10 pts | 6 pts | ✅ Séparable |

**Modalité validation partielle** :
- Si échec global (<210/350) mais ≥60% sur certains CCPs → CCPs validés conservés 5 ans
- Possibilité repasser uniquement CCPs manquants (examen adapté)
- Titre obtenu quand tous CCPs validés

---

## 📅 RÉCAPITULATIF COMPLET JOUR 2 (ORAL)

### ⏱️ TIMING DÉTAILLÉ JOUR 2

| HORAIRE | ACTIVITÉ | DURÉE | LIEU |
|---------|----------|-------|------|
| **08h00** | Arrivée candidats | - | Salle attente |
| **08h15** | Accueil, émargement | 15 min | Accueil |
| **08h30** | Briefing collectif | 15 min | Salle attente |
| **08h45** | **Début passages individuels** | | |
| | ↓ Candidat 1 convoqué | | |
| **08h45 - 09h30** | **MSP Pratique** (Candidat 1) | 45 min | Salle technique (lab) |
| | - Incident 1 : 12 min | | |
| | - Incident 2 : 15 min | | |
| | - Incident 3 : 18 min | | |
| **09h30 - 09h35** | Pause transition | 5 min | Couloir |
| **09h35 - 10h20** | **Entretien Technique** (Candidat 1) | 45 min | Salle jury |
| | - 31 questions CCPs | | |
| **10h20 - 10h25** | Pause transition | 5 min | Couloir |
| **10h25 - 10h45** | **Entretien Final** (Candidat 1) | 20 min | Salle jury |
| | - Présentation projet : 7 min | | |
| | - Questions projet : 5 min | | |
| | - Questions motivation : 8 min | | |
| **10h45 - 10h50** | Clôture, sortie candidat | 5 min | Accueil |
| **10h50 - 11h00** | Délibération jury Candidat 1 | 10 min | Salle jury |
| **11h00** | ↓ Candidat 2 convoqué | | |
| ... | (Répétition pour candidats suivants) | | |
| **18h00** | Fin passages tous candidats | | |

**Durée totale par candidat** : 1h50 (hors délibération jury)

---

### 🎒 CHECKLIST MATÉRIEL JOUR 2

#### 📋 DOCUMENTS OBLIGATOIRES

✅ **Pièce d'identité** (CNI, passeport, permis conduire)  
✅ **Convocation examen** (imprimée + version numérique smartphone)  
✅ **Dossier Professionnel** (2 exemplaires reliés)  
✅ **CV** (3 exemplaires pour jury 3 personnes)

#### 📋 MATÉRIEL RECOMMANDÉ

✅ **Bloc-notes + stylo** (prise notes jury, questions à poser)  
✅ **Bouteille d'eau** (petite, discret)  
✅ **Support visuel projet** (clé USB avec slides PowerPoint/PDF, si applicable)  
✅ **Montre** (gestion temps, téléphone éteint)  
✅ **Mouchoirs** (en cas stress, mains moites)

#### 📋 MATÉRIEL INTERDIT

❌ Téléphone allumé (mode avion ou éteint obligatoire)  
❌ Smartwatch connectée (si notification active)  
❌ Écouteurs Bluetooth  
❌ Documents non autorisés (pas de notes personnelles pendant MSP)

---

### 👔 TENUE VESTIMENTAIRE RECOMMANDÉE

**Homme** :
- Pantalon costume sombre (bleu marine, gris, noir)
- Chemise claire (blanc, bleu clair) **ou** polo propre
- Chaussures de ville propres (pas baskets)
- (Optionnel) Cravate (pas obligatoire, smart casual accepté)

**Femme** :
- Pantalon/jupe professionnel (sobre)
- Chemisier/blouse/pull (éviter décolleté plongeant)
- Chaussures fermées (éviter talons très hauts si pas habitué)

**Règle d'or** : **Smart casual professionnel** (entre décontracté et costume strict)

---

## 🎯 FLUX CANDIDAT DÉTAILLÉ JOUR 2

### 📍 ÉTAPE 1 : ARRIVÉE ET ACCUEIL (08h00 - 08h45)

**08h00 : Arrivée centre examen**
- Arriver **15 minutes avant convocation** (marge sécurité)
- Repérer salle attente, toilettes, distributeurs

**08h15 : Émargement**
- Présenter pièce identité + convocation
- Signature feuille émargement
- Remise badge nominatif (si applicable)

**08h30 : Briefing collectif**
- Présentation jury (3 personnes : président jury + 2 évaluateurs)
- Rappel règles :
  - Téléphones éteints
  - Pas de communication entre candidats pendant pauses
  - Ordre passages (tirage au sort ou alphabétique)
- Rappel déroulement :
  - MSP Pratique 45 min (salle lab)
  - Entretien Technique 45 min (salle jury)
  - Entretien Final 20 min (même salle)
- Questions candidats

**08h45 : Début passages**
- Candidats appelés un par un
- Attente en salle (lecture, relaxation)

---

### 📍 ÉTAPE 2 : MSP PRATIQUE (45 MINUTES)

**00h00 : Entrée salle technique**
- Jury vous accueille : "Bonjour [Nom], installez-vous"
- Vous vous installez devant poste lab (PC + accès serveurs)

**00h02 : Consignes jury**
- Jury explique :
  - 3 incidents à résoudre
  - 45 minutes total (gestion temps libre)
  - Infrastructure disponible (schéma réseau affiché)
  - Vous pouvez parler à voix haute (expliquer démarche)
  - Jury prend notes, n'intervient pas sauf question administrative

**00h05 : Distribution énoncés**
- Vous recevez **feuille A4 avec 3 incidents** :

```
INCIDENT 1 : Accès partagé réseau bloqué (Temps suggéré : 12 min)
Les utilisateurs GRP_RH ne peuvent plus accéder \\SRV-FILE01\RH_Partage.
Message : "Accès refusé".
→ Diagnostiquer et résoudre.

INCIDENT 2 : VM ne démarre plus (Temps suggéré : 15 min)
VM "SRV-GLPI" ne démarre plus après suppression snapshot.
Message vSphere : "Fichier .vmdk introuvable".
→ Diagnostiquer et résoudre.

INCIDENT 3 : Site web inaccessible (Temps suggéré : 18 min)
Site http://intranet.entreprise.local retourne erreur 500.
Service Apache actif mais site non fonctionnel.
→ Diagnostiquer et résoudre.
```

**00h05 - 00h45 : Résolution incidents**

**Conseils pendant MSP** :
1. **Lire les 3 incidents (2 min)** : Prioriser (commencer par le plus facile pour vous)
2. **Verbaliser démarche** : "Je vais d'abord vérifier les permissions NTFS..."
3. **Gérer temps** : Montre discrète, si bloqué >5 min sur incident → Passer au suivant
4. **Tests validation** : Toujours valider résolution (test accès utilisateur, VM démarre, site accessible)
5. **Documenter actions** : Jury note vos commandes, méthodologie

**00h45 : Fin MSP**
- Jury : "Temps écoulé, merci. Vous pouvez sortir, pause 5 minutes."
- Vous sortez, pause couloir/toilettes

---

### 📍 ÉTAPE 3 : ENTRETIEN TECHNIQUE (45 MINUTES)

**00h00 : Entrée salle jury**
- Même jury (3 personnes)
- Vous vous asseyez face au jury (table triangulaire ou U)

**00h02 : Consignes jury**
- Jury explique :
  - 45 minutes questions techniques
  - Questions orales (pas de QCM écrit)
  - Couvre tous les CCPs
  - Vous pouvez demander reformulation si question pas claire
  - Pas de bonne réponse unique, on évalue méthodologie aussi

**00h05 : Début questions**

**Exemple déroulement** :

```
JURY : "Nous allons commencer par le CCP1, Support utilisateur. 
        Un utilisateur vous appelle : 'Mon ordinateur est lent'. 
        Comment procédez-vous ?"

VOUS : [Réponse structurée 2 minutes]

JURY : "Très bien. Question suivante : Expliquez à un utilisateur 
        non technique ce qu'est une adresse IP."

VOUS : [Réponse avec analogie 1 minute]

... (29 questions suivantes, rythme ~1min20 par question)

JURY : "Dernière question, CCP6 Sécurité : Comment protéger 
        votre infrastructure contre les ransomwares ?"

VOUS : [Réponse 3 axes : Prévention/Détection/Récupération]

JURY : "Parfait, merci. Nous passons maintenant à l'entretien final."
```

**Conseils pendant Entretien Technique** :
1. **Structure réponses** : Méthode DESC (Définition → Exemple → Spécificités → Conclusion)
2. **Exemples concrets** : "Dans mon stage, j'ai configuré..."
3. **Si vous ne savez pas** : "Je ne maîtrise pas ce point précisément, mais je sais que..."
4. **Demander précisions** : Si question vague, demandez clarification
5. **Respirer** : Prenez 5 secondes réflexion avant répondre

---

### 📍 ÉTAPE 4 : ENTRETIEN FINAL (20 MINUTES)

**00h00 : Transition directe**
- Pas de pause entre Entretien Technique et Final
- Jury : "Nous passons à la dernière partie, l'entretien final."

**00h02 : Présentation projet (7 minutes)**

```
JURY : "Pouvez-vous nous présenter votre projet personnel ?"

VOUS : [Présentation structurée avec slides optionnel]
       - Contexte et problématique (2 min)
       - Réalisation technique (3 min)
       - Résultats et bilan (2 min)
```

**00h09 : Questions projet (5 minutes)**

```
JURY : "Pourquoi avoir choisi VMware plutôt que Proxmox ?"
VOUS : [Réponse argumentée]

JURY : "Quelles difficultés avez-vous rencontrées ?"
VOUS : [Méthode STAR : Situation/Tâche/Action/Résultat]

JURY : "Si vous deviez refaire ce projet, que changeriez-vous ?"
VOUS : [Recul, améliorations possibles]
```

**00h14 : Questions motivation (6 minutes)**

```
JURY : "Pourquoi avez-vous choisi le métier TSSR ?"
VOUS : [Passion, attrait technique, diversité, évolution]

JURY : "Quels sont vos objectifs professionnels à 3-5 ans ?"
VOUS : [Court terme : certifications, Moyen terme : admin sys/spécialisation]

JURY : "Comment faites-vous votre veille technologique ?"
VOUS : [Sources concrètes : sites, newsletters, HomeLab]
```

**00h18 : Vos questions au jury (2 minutes)**

```
JURY : "Avez-vous des questions pour nous ?"

VOUS : (Posez 1-2 questions intelligentes)
       - "Quels retours me donneriez-vous pour améliorer mes compétences ?"
       - "Quelles technologies émergentes recommanderiez-vous d'approfondir ?"
       - "Quel profil de candidat réussit particulièrement dans ce métier ?"

       (ÉVITEZ : "C'est bon, je suis pris ?", "Quand commence le poste ?", "Salaire ?")
```

**00h20 : Clôture entretien**

```
JURY : "Merci [Nom], nous avons terminé. Vous allez recevoir 
        les résultats par courrier sous 3-4 semaines. 
        Avez-vous une dernière question ?"

VOUS : "Non, merci beaucoup pour votre attention. Au revoir !"
       [Vous serrez la main des 3 jurys (optionnel selon contexte COVID)]

JURY : "Au revoir et bonne continuation."
```

---

### 📍 ÉTAPE 5 : SORTIE ET ATTENTE RÉSULTATS

**10h50 : Sortie centre examen**
- Vous récupérez vos affaires (vestiaire)
- Vous pouvez partir immédiatement (pas d'obligation rester)

**10h50 - 11h00 : Délibération jury (vous n'êtes pas présent)**
- Jury remplit grilles évaluation :
  - MSP Pratique /60
  - Entretien Technique /60
  - Entretien Final /30
- Calcul note Jour 2 /150
- Ajout note Jour 1 /200 → Note globale /350
- Décision : Certification obtenue si ≥210/350

**J+20 à J+30 : Réception résultats**
- Courrier postal **ou** email (selon centre)
- Contenu :
  - Note globale /350
  - Détail notes par épreuve
  - CCPs validés (si validation partielle)
  - Décision : **Titre obtenu** ou **Titre non obtenu**

**Si titre obtenu** :
- Diplôme envoyé sous 2-3 mois
- Inscription RNCP (Répertoire National Certifications Professionnelles)
- Titre Niveau 5 (Bac+2) reconnu État

**Si titre non obtenu** :
- CCPs validés conservés 5 ans
- Possibilité repasser CCPs manquants (session suivante)
- Retour formation complémentaire recommandé (si nombreuses lacunes)

---

## 🎯 CONSEILS FINAUX AVANT ORAL

### ✅ LA VEILLE (J-1)

**16h00 - 18h00 : Révisions légères**
- Relire fiches récapitulatives (pas d'apprentissage nouveau)
- Relire dossier professionnel (cohérence avec oral)
- Lister 3-5 questions possibles jury + réponses

**18h00 - 19h00 : Préparation matérielle**
- Vérifier documents (CNI, convocation, CV, DP)
- Préparer tenue vestimentaire (repasser si besoin)
- Charger clé USB slides projet (si applicable)
- Vérifier itinéraire centre examen (Google Maps, temps trajet)

**19h00 - 21h00 : Détente**
- Dîner léger (éviter alcool)
- Activité relaxante (série, musique, lecture)
- **PAS de révisions intensives** (risque saturation)

**21h00 : Coucher tôt**
- Coucher 21h-22h pour 8h sommeil
- Éviter écrans 1h avant dormir (lumière bleue)
- Relaxation (respiration, méditation si pratiqué)

---

### ✅ LE JOUR J (MATIN)

**06h30 : Réveil**
- Réveil 6h30 (2h avant départ)
- Douche (éveil, fraîcheur)

**07h00 : Petit-déjeuner**
- Petit-déjeuner complet (éviter ventre vide = stress)
- Féculents (pain, céréales) + protéines (œuf, fromage)
- Éviter caféine excessive (max 1-2 cafés, sinon nervosité)

**07h15 : Relecture rapide (15 min)**
- Relire projet personnel (rafraîchir mémoire)
- Relire top 5 commandes par CCP
- **PAS de révisions marathon** (trop tard)

**07h30 : Habillage**
- Mettre tenue professionnelle
- Vérifier aucune tache, boutons fermés
- Parfum/déodorant léger (éviter excès)

**07h45 : Départ**
- Partir avec **30 min marge** (anticipation retard transports)
- Vérifier sac (documents, matériel, eau)

**08h00 : Arrivée centre**
- Arrivée 15 min avant convocation (8h15)
- Repérage lieux (toilettes, distributeurs)
- **Respiration profonde 5 min** (calmer stress)

---

### ✅ PENDANT L'EXAMEN

#### 🧘 GESTION DU STRESS

**Technique 1 : Respiration 4-7-8**
- Inspirer 4 secondes (nez)
- Retenir 7 secondes
- Expirer 8 secondes (bouche)
- Répéter 3× (calme rythme cardiaque)

**Technique 2 : Ancrage corporel**
- Sentir pieds sur le sol
- Sentir dos sur chaise
- Ramène attention présent (évite rumination)

**Technique 3 : Recadrage positif**
- Pensée négative : "Je vais échouer" ❌
- Recadrage : "J'ai préparé, je fais de mon mieux" ✅

#### 💬 GESTION QUESTION DIFFICILE

**Si vous ne savez pas répondre** :

```
MAUVAIS ❌ :
"Euh... je sais pas... désolé... je suis nul..."

BON ✅ :
"Je ne maîtrise pas ce point précisément. En revanche, je sais que 
[concept connexe]. Si je devais l'apprendre, je consulterais 
[documentation officielle / cours / collègue expert]."
```

**Exemple concret** :

```
JURY : "Expliquez le fonctionnement du protocole BGP."

VOUS (si vous ne savez pas) :
"Je ne connais pas BGP en détail, je n'ai pas encore travaillé 
sur des infrastructures multi-sites nécessitant ce protocole. 
Je sais que BGP est un protocole de routage externe utilisé 
entre AS (Autonomous Systems), notamment par les FAI. 
Pour l'apprendre, je consulterais la documentation Cisco CCNP 
ou suivrais une formation spécifique routage avancé."

→ Le jury apprécie : honnêteté + contexte + volonté apprendre
```

#### 🕐 GESTION DU TEMPS

**MSP Pratique (45 min)** :
- Incident 1 : 12 min max (chrono mental ou montre)
- Incident 2 : 15 min max
- Incident 3 : 18 min max
- **Si bloqué >5 min** : Passer au suivant, revenir à la fin

**Entretien Technique (45 min)** :
- Réponses courtes : 1 min (définitions)
- Réponses moyennes : 2 min (comparaisons)
- Réponses longues : 3 min max (cas pratiques)
- **Si jury interrompt** : Normal (pas un échec, gestion temps jury)

**Entretien Final (20 min)** :
- Présentation projet : 5-7 min (**pratiquer chez vous avec chrono**)
- Si dépassement : Jury interrompt poliment ("Merci, nous allons passer aux questions")

---

### ✅ APRÈS L'EXAMEN

**Immédiatement après sortie** :
- **NE PAS discuter avec autres candidats** (comparaison = stress inutile)
- Rentrer chez vous directement
- Respirer, décompresser

**Après-midi J0** :
- Activité relaxante (sport, balade, ciné)
- **PAS de rumination** ("J'aurais dû dire...", "J'ai raté...")
- Ce qui est fait est fait ✅

**J+1 (optionnel)** :
- **Email remerciement jury** (optionnel mais apprécié) :

```
Objet : Remerciement suite examen TSSR - [Votre Nom]

Bonjour,

Je tenais à vous remercier pour le temps que vous avez consacré 
à mon examen TSSR hier. J'ai apprécié la qualité de l'échange 
durant les entretiens et les retours constructifs que vous avez pu 
me faire durant les épreuves pratiques.

Dans l'attente des résultats, je reste à votre disposition pour 
toute information complémentaire.

Cordialement,
[Votre Nom]
[Téléphone]
[Email]
```

**J+1 à J+30 : Attente résultats**
- Continuer veille techno (routine habituelle)
- Préparer projets professionnels (candidatures, certifications)
- **Ne pas appeler centre tous les jours** (résultats envoyés sous 3-4 semaines)

---

## 🏆 STRATÉGIES MAXIMISATION SCORE

### 🎯 STRATÉGIE JOUR 2 : PRIORISATION INTELLIGENTE

**Principe** : Vous n'avez pas besoin de 100% pour réussir. **60% suffisent** (210/350).

**Répartition optimale effort** :

| ÉPREUVE | POINTS | EFFORT RECOMMANDÉ | SCORE CIBLE |
|---------|--------|-------------------|-------------|
| MSP Pratique | 60 pts | 🔥🔥🔥 Élevé (résoudre 2/3 incidents) | 40/60 (67%) |
| Entretien Technique | 60 pts | 🔥🔥🔥 Élevé (répondre 25/31 questions) | 45/60 (75%) |
| Entretien Final | 30 pts | 🔥🔥 Modéré (projet + motivation) | 20/30 (67%) |
| **TOTAL JOUR 2** | **150 pts** | | **105/150 (70%)** ✅ |

**Combiné avec Jour 1** (hypothèse 105/200 = 52%) :
- Jour 1 : 105/200
- Jour 2 : 105/150
- **Total : 210/350 (60%)** → **Certification obtenue** ✅

**Message clé** : Même si Jour 1 moyen (52%), un bon Jour 2 (70%) compense !

---

### 🎯 FOCUS PAR PROFIL CANDIDAT

**Profil A : "Fort en pratique, faible en théorie"**
- **Forces** : MSP Pratique, commandes techniques
- **Faiblesses** : Questions théoriques (ex: différence incident/demande ITIL)
- **Stratégie** :
  - MSP Pratique : Viser 50/60 (résoudre les 3 incidents)
  - Entretien Technique : Focus CCPs techniques (CCP2 Linux, CCP3 Réseau, CCP4 Windows)
  - Entretien Final : Valoriser projet technique solide

**Profil B : "Fort en théorie, faible en pratique"**
- **Forces** : Réponses orales structurées, connaissances conceptuelles
- **Faiblesses** : Commandes pratiques, diagnostic incidents
- **Stratégie** :
  - MSP Pratique : Viser 30/60 (résoudre 1-2 incidents, montrer méthodologie sur 3ème)
  - Entretien Technique : Viser 50/60 (répondre 28/31 questions, structurer avec DESC)
  - Entretien Final : Viser 25/30 (communication excellente, projet bien présenté)

**Profil C : "Généraliste équilibré"**
- **Forces** : Polyvalence, pas de grosse lacune
- **Faiblesses** : Pas d'expertise pointue
- **Stratégie** :
  - MSP Pratique : Viser 40/60 (résoudre 2 incidents complètement)
  - Entretien Technique : Viser 40/60 (répondre 24/31 questions, admettre lacunes honnêtement)
  - Entretien Final : Viser 22/30 (projet cohérent, motivation sincère)

---

## 📊 RÉCAPITULATIF SIMULATION ORALE COMPLÈTE


### 🎯 LES 3 PILIERS DE LA RÉUSSITE

```
              🏆 CERTIFICATION TSSR
                     /   |   \
                    /    |    \
                   /     |     \
                  /      |      \
                 /       |       \
         ┌──────────┬────────┬──────────┐
         │  SAVOIR  │ SAVOIR-│ SAVOIR-  │
         │          │  FAIRE │   ÊTRE   │
         └──────────┴────────┴──────────┘
             60%       20%       20%

SAVOIR (Connaissances techniques) :
→ CCPs maîtrisés, commandes, protocoles, architectures
→ Vous l'avez acquis durant la formation ✅

SAVOIR-FAIRE (Méthodologie) :
→ Diagnostic méthodique, résolution incidents, structuration
→ Vous l'avez pratiqué sur labs et projets ✅

SAVOIR-ÊTRE (Communication, attitude) :
→ Professionnalisme, écoute, gestion stress, motivation
→ Vous allez le démontrer le jour J ✅
```

---

### 🔥 AFFIRMATIONS POSITIVES (À RÉPÉTER)

**Avant l'examen** :
- "Je suis prêt(e), j'ai travaillé sérieusement."
- "Je connais mon métier, je vais le démontrer."
- "Le stress est normal, je le gère avec respiration."

**Pendant MSP Pratique** :
- "Je diagnostique méthodiquement, étape par étape."
- "Si je bloque, je passe au suivant et je reviens."
- "Je valide chaque résolution avec des tests."

**Pendant Entretien Technique** :
- "Je structure mes réponses (DESC)."
- "Je donne des exemples concrets de mon expérience."
- "Si je ne sais pas, je l'admets honnêtement."

**Pendant Entretien Final** :
- "Mon projet est solide, je le présente avec assurance."
- "Ma motivation est sincère, je la communique clairement."
- "Je souris, je respire, je suis professionnel(le)."

---

### 🎊 APRÈS LA CERTIFICATION

**Quand vous aurez réussi** (car vous allez réussir !) :

1. **Célébrez** 🎉
   - Vous avez mérité : dîner, sortie, repos
   - Partagez avec proches (famille, amis, formateurs)

2. **Mettez à jour vos profils** 📝
   - LinkedIn : "Titre Professionnel TSSR (Niveau 5 - Bac+2) obtenu"
   - CV : Ajout certification + date obtention
   - Signature email : "Prénom NOM, Technicien Supérieur Systèmes et Réseaux"

3. **Lancez-vous sur le marché** 🚀
   - Candidatures ciblées (PME/ETI, ESN)
   - Réseau alumni formation
   - Salons emploi IT (Forum jobs IT, Paris IT Forum)

4. **Continuez à apprendre** 📚
   - Certification éditeur 1 : AZ-900 (Azure) ou CCNA (Cisco)
   - HomeLab personnel (améliorer lab existant)
   - Veille techno quotidienne (routine maintenue)

5. **Donnez au suivant** 🤝
   - Aidez futurs candidats TSSR (mentorat)
   - Partagez retour expérience examen (forum, réseaux)
   - Contribuez communauté IT (StackOverflow, Reddit r/sysadmin)

---

## 🎯 DERNIERS MOTS

**Cher(e) futur(e) Technicien(ne) Supérieur(e) Systèmes et Réseaux,**

Vous êtes arrivé(e) au terme de cette simulation orale complète. Vous avez maintenant entre les mains **tous les outils** pour réussir brillamment votre certification TSSR.

**Rappelez-vous** :
- Le jury **veut** vous voir réussir (pas un piège)
- Vous avez **le niveau** (sinon pas convoqué)
- Le stress est **normal** (tous les candidats le ressentent)
- **60% suffisent** (pas besoin de perfection)

**Le jour J** :
- Respirez profondément 🌬️
- Souriez naturellement 😊
- Soyez vous-même (authenticité prime sur performance)
- Faites de votre mieux (c'est suffisant)

**Nous croyons en vous.** Vous allez décrocher ce titre. Dans quelques semaines, vous lirez ce message avec votre diplôme en main, souriant de voir à quel point le stress était inutile.

---

## 📞 DERNIÈRES RESSOURCES

### 🌐 LIENS UTILES

**Documentation officielle** :
- Titre TSSR : https://www.francecompetences.fr/recherche/rncp/37680/
- Référentiel activités : REAC TSSR (fourni par centre formation)
- Référentiel certification : RC TSSR (fourni par centre formation)

**Communautés IT** :
- Reddit r/sysadmin : https://reddit.com/r/sysadmin
- Reddit r/homelab : https://reddit.com/r/homelab

**Veille techno** :
- Next INpact : https://next.ink
- LeMagIT : https://www.lemagit.fr
- NetworkChuck YouTube : https://youtube.com/@NetworkChuck

---

## ✅ CHECKLIST FINALE AVANT ORAL

**3 JOURS AVANT (J-3)** :
- [ ] Relire Simulation Orale complète (PARTIES 1 à 3C)
- [ ] Pratiquer MSP Pratique sur lab (3 incidents)
- [ ] Préparer présentation projet (slides)

**VEILLE EXAMEN (J-1)** :
- [ ] Révisions légères (fiches, top commandes)
- [ ] Vérifier documents (CNI, convocation, CV, DP)
- [ ] Préparer tenue vestimentaire
- [ ] Vérifier itinéraire centre examen
- [ ] Coucher tôt (21h-22h)

**MATIN EXAMEN (J0)** :
- [ ] Réveil 2h avant départ (6h30)
- [ ] Petit-déjeuner complet
- [ ] Relecture rapide projet personnel
- [ ] Habillage professionnel
- [ ] Départ avec 30 min marge
- [ ] Arrivée 15 min avant convocation

**DANS LE SAC** :
- [ ] Pièce identité (CNI/passeport)
- [ ] Convocation examen
- [ ] Dossier Professionnel (2 exemplaires)
- [ ] CV (3 exemplaires)
- [ ] Bloc-notes + stylo
- [ ] Bouteille d'eau
- [ ] Clé USB slides projet
- [ ] Montre (téléphone éteint)

---

**🎓 Certification** : Titre Professionnel TSSR (Niveau 5)  
**⏰ Durée Jour 2** : 1h50 (MSP 45min + Technique 45min + Final 20min)  
**🎯 Objectif** : ≥60/150 points Jour 2 (40%) + ≥210/350 global (60%)

---

# 🏆 BONNE CHANCE ! VOUS ALLEZ RÉUSSIR ! 🚀

**Croyez en vous. Respirez. Souriez. Montrez qui vous êtes.**

**Rendez-vous de l'autre côté de la certification, avec votre titre en poche.** 💪

---

*Fin de la Simulation Orale TSSR - Vous avez maintenant tous les outils pour réussir brillamment !* ✅
