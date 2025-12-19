# 💾 FICHE DE RÉVISION EXPRESS - SAUVEGARDES/PRA/PCA
## CCP8 - Sauvegardes et restaurations

> **⚠️ CRITIQUE** : Sujet TRÈS fréquent à l'examen ! RTO/RPO, types de sauvegardes, snapshots

---

## 📊 LES 3 TYPES DE SAUVEGARDES - À CONNAÎTRE PAR CŒUR

### 1. Sauvegarde COMPLÈTE (Full Backup)

**Définition** : Copie intégrale de toutes les données

✅ **Avantages** :
- Restauration simple et rapide (1 seule sauvegarde)
- Indépendante (pas besoin des autres sauvegardes)
- Fichiers complets facilement accessibles

❌ **Inconvénients** :
- Très longue à effectuer
- Consomme beaucoup d'espace disque
- Utilise beaucoup de bande passante réseau

📅 **Fréquence recommandée** : Hebdomadaire (ex: tous les dimanches)

**Exemple** :
```
Lundi : Complète (100 Go)
```

---

### 2. Sauvegarde DIFFÉRENTIELLE (Differential Backup)

**Définition** : Sauvegarde tous les fichiers modifiés depuis la DERNIÈRE sauvegarde COMPLÈTE

✅ **Avantages** :
- Plus rapide qu'une complète
- Restauration simple (complète + dernière différentielle)
- Moins d'espace que les complètes quotidiennes

❌ **Inconvénients** :
- Taille augmente chaque jour jusqu'à la prochaine complète
- Plus lente que l'incrémentielle
- Plus d'espace que l'incrémentielle

📅 **Fréquence recommandée** : Quotidienne + complète hebdomadaire

**Exemple** :
```
Dimanche : Complète (100 Go)
Lundi :    Différentielle (10 Go) - Fichiers changés depuis dimanche
Mardi :    Différentielle (15 Go) - Fichiers changés depuis dimanche
Mercredi : Différentielle (20 Go) - Fichiers changés depuis dimanche
...
```

**Restauration** : Complète de dimanche + Différentielle du jour voulu

---

### 3. Sauvegarde INCRÉMENTIELLE (Incremental Backup)

**Définition** : Sauvegarde uniquement les fichiers modifiés depuis la DERNIÈRE sauvegarde (quelle qu'elle soit)

✅ **Avantages** :
- Très rapide à effectuer
- Utilise très peu d'espace
- Bande passante minimale

❌ **Inconvénients** :
- Restauration plus complexe (complète + TOUTES les incrémentielles)
- Si une incrémentielle est corrompue, perte de données

📅 **Fréquence recommandée** : Quotidienne + complète hebdomadaire

**Exemple** :
```
Dimanche : Complète (100 Go)
Lundi :    Incrémentielle (5 Go) - Fichiers changés depuis dimanche
Mardi :    Incrémentielle (3 Go) - Fichiers changés depuis lundi
Mercredi : Incrémentielle (4 Go) - Fichiers changés depuis mardi
...
```

**Restauration** : Complète de dimanche + TOUTES les incrémentielles (lundi, mardi, mercredi...)

---

## 📈 TABLEAU COMPARATIF (QUESTION D'EXAMEN FRÉQUENTE)

| Critère | Complète | Différentielle | Incrémentielle |
|---------|----------|----------------|----------------|
| **Vitesse sauvegarde** | 🐢🐢🐢 Très lent | 🐢🐢 Moyen | 🐇 Très rapide |
| **Espace disque** | 💾💾💾 Énorme | 💾💾 Moyen (croissant) | 💾 Minimal |
| **Vitesse restauration** | 🚀🚀🚀 Très rapide | 🚀🚀 Rapide | 🚀 Lente |
| **Complexité restauration** | ⭐ Simple | ⭐⭐ Moyenne | ⭐⭐⭐ Complexe |
| **Éléments nécessaires** | 1 sauvegarde | 2 sauvegardes | Toutes les sauv. |
| **Sécurité** | ✅ Excellente | ✅ Bonne | ⚠️ Risque si perte |

---

## 🎯 RTO vs RPO - DIFFÉRENCE CRUCIALE

### RPO (Recovery Point Objective) - "Perte de données acceptable"

**Définition** : Quantité maximale de données qu'on peut se permettre de perdre (en temps)

**Question** : "Combien de temps de données peut-on perdre ?"

**Exemples** :
- RPO = 24h → Sauvegarde quotidienne (perte max = 1 jour de données)
- RPO = 1h → Sauvegarde toutes les heures
- RPO = 0 → Réplication en temps réel

**📌 Retenir** : RPO = **P**oint = **P**erte de données

```
Timeline :
|----------|----------|----------|----------|
Dernière   Incident   Point de   Restauration
sauvegarde            récup      souhaitée
           <--------->
              RPO
```

---

### RTO (Recovery Time Objective) - "Temps de reprise acceptable"

**Définition** : Durée maximale d'interruption de service tolérable

**Question** : "Combien de temps peut-on rester hors service ?"

**Exemples** :
- RTO = 4h → Service doit être rétabli en 4h maximum
- RTO = 24h → Service peut être down 1 jour
- RTO = 0 → Service doit être disponible en continu (HA)

**📌 Retenir** : RTO = **T**emps = **T**emps de reprise

```
Timeline :
|----------|----------|----------|----------|
Incident   Début      Fin        Service
           restaur.   restaur.   rétabli
           <--------------------->
                    RTO
```

---

### 🎓 Différence RTO vs RPO - Mémo

| Aspect | RPO | RTO |
|--------|-----|-----|
| **Signification** | Recovery **Point** Objective | Recovery **Time** Objective |
| **Question** | Combien de **données** perdues ? | Combien de **temps** down ? |
| **Unité** | Temps (perte de données) | Temps (interruption service) |
| **Impact** | Fréquence des sauvegardes | Rapidité de restauration |
| **Coût** | Plus le RPO est bas, plus les sauv. sont fréquentes | Plus le RTO est bas, plus l'infra est coûteuse |

**Exemple concret** :
- **RPO = 1h** : On sauvegarde toutes les heures
- **RTO = 4h** : On doit restaurer en moins de 4h

---

## 📸 SNAPSHOT vs SAUVEGARDE - DIFFÉRENCE IMPORTANTE

### Snapshot (Instantané)

**Définition** : Photo de l'état d'un système à un instant T

✅ **Avantages** :
- ⚡ Création ultra-rapide (secondes)
- 💾 Peu d'espace initial (delta uniquement)
- 🔄 Retour arrière instantané
- 🧪 Idéal pour tests/mises à jour

❌ **Inconvénients** :
- ⚠️ Dépend du système source (pas indépendant)
- 📈 Taille augmente avec les modifications
- 🔥 Si le disque crash, snapshot perdu
- ⏱️ Performances dégradées si trop de snapshots

**Usage** : Test de mise à jour, avant modification critique

**❌ CE N'EST PAS UNE SAUVEGARDE !**

---

### Sauvegarde (Backup)

**Définition** : Copie des données sur un support séparé

✅ **Avantages** :
- ✅ Totalement indépendante du système source
- 🔒 Protection contre panne matérielle
- 💾 Stockage sur support externe/distant
- 📦 Conservation long terme

❌ **Inconvénients** :
- 🐢 Plus lent à créer
- 💰 Coût stockage externe
- 🔄 Restauration plus longue

**Usage** : Protection des données, archivage, conformité

---

### 📊 Comparaison Snapshot vs Sauvegarde

| Critère | Snapshot | Sauvegarde |
|---------|----------|------------|
| **Vitesse création** | ⚡ Secondes | 🐢 Minutes à heures |
| **Indépendance** | ❌ Non (même support) | ✅ Oui (support séparé) |
| **Protection panne** | ❌ Faible | ✅ Forte |
| **Espace initial** | 💾 Minimal | 💾💾 Important |
| **Usage** | Tests, rollback rapide | Protection, DR |
| **Durée de vie** | ⏱️ Court terme | 📅 Long terme |

**Règle d'or** : 
```
SNAPSHOT ≠ SAUVEGARDE
Snapshot = Photo locale temporaire
Sauvegarde = Copie externe durable
```

---

## 🏢 PRA vs PCA - PLANS DE CONTINUITÉ

### PCA (Plan de Continuité d'Activité)

**Définition** : Ensemble des mesures pour maintenir ou reprendre les activités essentielles en cas de sinistre

**Objectif** : CONTINUER l'activité (continuité)

**Périmètre** : TOUTE l'entreprise (pas que l'IT)

**Contient** :
- Procédures de crise
- Cellule de crise
- Modes dégradés
- Communication
- Locaux de repli
- PRI (Plan de Reprise Informatique)

**Exemple** : Incendie du bâtiment → travailler depuis un site de secours

---

### PRA (Plan de Reprise d'Activité)

**Définition** : Ensemble des procédures pour restaurer le système d'information après un sinistre

**Objectif** : REPRENDRE l'activité (reprise)

**Périmètre** : Système informatique uniquement

**Contient** :
- Inventaire des systèmes critiques
- Procédures de restauration
- Sites de secours (hot/warm/cold)
- Ordre de restauration des services
- Tests réguliers

**Exemple** : Panne du serveur principal → basculer sur serveur de secours

---

### PRI (Plan de Reprise Informatique)

**Définition** : Composante du PCA focalisée sur l'IT

**Rôle** : Restaurer les systèmes informatiques critiques

---

### 📊 Différence PCA vs PRA

| Aspect | PCA | PRA |
|--------|-----|-----|
| **Périmètre** | Toute l'entreprise | IT uniquement |
| **Objectif** | Continuer l'activité | Reprendre les systèmes |
| **Inclut** | Humain, logistique, IT | Infrastructure IT |
| **Pilote** | Direction générale | DSI |
| **Contient** | PRI/PRA + procédures métier | Procédures techniques |

**Relation** : PCA ⊃ PRI ⊃ PRA (PCA contient PRA)

---

## 📦 TYPES DE SUPPORTS DE SAUVEGARDE

### Supports physiques

| Support | Avantages | Inconvénients | Usage |
|---------|-----------|---------------|-------|
| **Bandes (LTO)** | Capacité énorme, coût faible, longue durée | Lent, séquentiel | Archives long terme |
| **Disques externes** | Rapide, facile | Fragile, coût moyen | Sauvegarde locale |
| **NAS** | Centralisé, réseau | Coût, complexité | Sauvegarde d'entreprise |
| **SAN** | Performance, fiabilité | Très coûteux | Entreprises critiques |

### Cloud

✅ **Avantages** :
- Accessibilité mondiale
- Réplication géographique
- Scalabilité automatique
- Pas de maintenance matérielle

❌ **Inconvénients** :
- Dépendance Internet
- Coûts récurrents
- Confidentialité des données
- Vitesse de restauration (selon bande passante)

**Exemples** : AWS S3, Azure Backup, Google Cloud Storage

---

## 📐 RÈGLE 3-2-1 (RÈGLE D'OR DES SAUVEGARDES)

### Principe de base

**3** copies des données :
- 1 copie de production (données originales)
- 2 copies de sauvegarde

**2** supports différents :
- Ex: Disque + Bande
- Ex: NAS + Cloud

**1** copie hors site (off-site) :
- Protection contre incendie, inondation
- Ex: Cloud, datacenter distant

**Exemple d'application** :
```
✅ Données en production sur serveur (1)
✅ Sauvegarde sur NAS local (2) - Support 1
✅ Sauvegarde dans le Cloud (3) - Support 2 + Off-site
```

---

## 🔄 STRATÉGIES DE SAUVEGARDE

### Stratégie Grand-Père-Père-Fils (GFS)

**Principe** : 3 niveaux de rotation

- **Fils (Quotidien)** : 1 sauvegarde par jour, conservée 7 jours
- **Père (Hebdomadaire)** : 1 sauvegarde par semaine, conservée 4 semaines
- **Grand-Père (Mensuel)** : 1 sauvegarde par mois, conservée 12 mois

**Exemple** :
```
Lundi-Samedi : Incrémentielle (Fils)
Dimanche :     Différentielle (Père)
1er du mois :  Complète (Grand-Père)
```

---

### Sites de secours (Disaster Recovery)

| Type | Description | RTO | Coût |
|------|-------------|-----|------|
| **Hot Site** | Site actif en temps réel, prêt immédiatement | ~0 minutes | 💰💰💰 Très élevé |
| **Warm Site** | Site partiellement configuré, activation rapide | ~Heures | 💰💰 Élevé |
| **Cold Site** | Site vide, matériel à installer | ~Jours | 💰 Faible |

---

## 🛠️ OUTILS DE SAUVEGARDE

### Windows
- **Windows Server Backup** : Natif Windows Server
- **Veeam Backup & Replication** : Virtualisation (leader)
- **Acronis** : Serveurs et postes
- **Shadowprotect** : Images disque

### Linux
- **rsync** : Synchronisation fichiers
- **tar** : Archives
- **dd** : Images disque bit-à-bit
- **Bacula** : Solution entreprise (open source)
- **Amanda** : Advanced Maryland Automatic Network Disk Archiver

### Virtualisation
- **Veeam** : Leader pour VMware/Hyper-V
- **VMware Data Protection** : Intégré vSphere
- **Snapshot vSphere** : Instantanés VM

### Bases de données
- **mysqldump** : Export MySQL
- **pg_dump** : Export PostgreSQL
- **SQL Server Management Studio** : SQL Server
- **Oracle RMAN** : Recovery Manager Oracle

---

## ✅ BONNES PRATIQUES

### 1. **Tester régulièrement les restaurations**
- ⚠️ Une sauvegarde non testée = pas de sauvegarde !
- Planifier tests trimestriels minimum
- Documenter les procédures de restauration

### 2. **Chiffrer les sauvegardes**
- Protection des données sensibles
- Obligatoire si transport hors site

### 3. **Automatiser les sauvegardes**
- Éliminer l'erreur humaine
- Utiliser des scripts/outils
- Planifier avec cron (Linux) ou Task Scheduler (Windows)

### 4. **Surveiller et alerter**
- Recevoir des notifications d'échec
- Vérifier les logs quotidiennement
- Monitorer l'espace disque

### 5. **Documenter**
- Procédures de sauvegarde
- Procédures de restauration
- Planning de rétention
- Contacts en cas de problème

### 6. **Rotation des supports**
- Ne pas saturer un seul support
- Alterner les bandes/disques
- Externaliser régulièrement

### 7. **Respecter les obligations légales**
- Durée de conservation (logs: 1 an minimum en France)
- RGPD (droit à l'oubli, chiffrement)
- Archivage comptable (10 ans)

---

## 🎯 QUESTIONS TYPES D'EXAMEN

### Question 1 : Types de sauvegardes
**Q** : "Expliquez les 3 types de sauvegardes : Totale, Différentielle, Incrémentielle"

**R** :
- **Totale** : Copie complète de toutes les données
- **Différentielle** : Fichiers modifiés depuis la dernière sauvegarde COMPLÈTE
- **Incrémentielle** : Fichiers modifiés depuis la DERNIÈRE sauvegarde (quelle qu'elle soit)

---

### Question 2 : RTO vs RPO
**Q** : "Quelle est la différence entre RTO et RPO ?"

**R** :
- **RPO** (Recovery Point Objective) : Quantité de données qu'on accepte de perdre (en temps)
- **RTO** (Recovery Time Objective) : Temps maximum pour restaurer le service

---

### Question 3 : Avantages/Inconvénients snapshots
**Q** : "Quels sont les avantages et inconvénients des snapshots ?"

**R** :
✅ **Avantages** : Rapides, peu d'espace, retour arrière facile
❌ **Inconvénients** : Pas indépendants, pas de protection panne matérielle, performances dégradées

---

### Question 4 : Histogramme de sauvegardes
**Q** : "On vous montre un histogramme de sauvegardes. Identifiez les types et la fréquence."

**R** : Analyser les tailles :
- Grande barre = Complète (hebdomadaire généralement)
- Barres moyennes croissantes = Différentielle (quotidienne)
- Petites barres régulières = Incrémentielle (quotidienne)

---

### Question 5 : PRA vs PCA
**Q** : "Quelle est la différence entre PRA et PCA ?"

**R** :
- **PCA** : Plan de Continuité d'Activité → TOUTE l'entreprise
- **PRA** : Plan de Reprise d'Activité → Systèmes IT uniquement
- Le PCA contient le PRA

---

## 💡 MÉMO ULTRA-RAPIDE (1 minute chrono)

```
TYPES DE SAUVEGARDES :
✅ Complète = Tout
✅ Différentielle = Modifs depuis dernière COMPLÈTE (taille croissante)
✅ Incrémentielle = Modifs depuis DERNIÈRE sauvegarde (taille stable)

RPO vs RTO :
✅ RPO = Perte de données acceptable (Point)
✅ RTO = Temps de reprise acceptable (Time)

SNAPSHOT ≠ SAUVEGARDE :
✅ Snapshot = Photo temporaire locale
✅ Sauvegarde = Copie externe durable

PRA vs PCA :
✅ PCA = Toute l'entreprise (Continuité)
✅ PRA = IT seulement (Reprise)

RÈGLE 3-2-1 :
✅ 3 copies
✅ 2 supports
✅ 1 hors site
```

---

**🎓 Cette fiche = Points faciles à l'examen ! Apprends-la par cœur ! 💪**
