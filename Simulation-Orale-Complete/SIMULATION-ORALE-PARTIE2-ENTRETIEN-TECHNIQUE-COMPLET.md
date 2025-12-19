# SIMULATION ORALE TSSR - PARTIE 2 COMPLETE
## Entretien Technique (45 minutes - 60 points)

---

# 🎤 SIMULATION ORALE TSSR - PARTIE 2A : ENTRETIEN TECHNIQUE (Introduction + CCP1-CCP2)

**Durée totale Entretien Technique** : 45 minutes  
**Cette partie (2A)** : Introduction générale + Questions CCP1 (Support) + CCP2 (Linux/Maintenance)

---

## 📋 STRUCTURE COMPLÈTE ENTRETIEN TECHNIQUE

```
┌─────────────────────────────────────────────────────────┐
│  ENTRETIEN TECHNIQUE - 45 MINUTES                       │
├─────────────────────────────────────────────────────────┤
│                                                         │
│  Introduction (5 min)                                   │
│  ├─ Présentation parcours candidat                      │
│  ├─ Questions sur dossier professionnel                 │
│  └─ Mise à l'aise                                       │
│                                                         │
│  Questions techniques orales (35 min)                   │
│  ├─ CCP1 : Support utilisateur (5 questions - 7 min)    │
│  ├─ CCP2 : Maintenance systèmes (6 questions - 8 min)   │
│  ├─ CCP3 : Réseau (5 questions - 7 min)                 │
│  ├─ CCP4 : Windows/AD (5 questions - 7 min)             │
│  ├─ CCP5 : Virtualisation (4 questions - 6 min)         │
│  └─ CCP6 : Sécurité (3 questions - 5 min)               │
│                                                         │
│  Questions transversales (5 min)                        │
│  └─ Cas pratiques multi-CCPs                            │
│                                                         │
└─────────────────────────────────────────────────────────┘
```

---

## 🎯 INTRODUCTION ENTRETIEN (5 minutes)

### **Accueil et présentation**

**Jury** :  
*"Bonjour [Prénom]. Nous allons maintenant passer à l'entretien technique. Nous allons vous poser des questions sur vos compétences techniques, votre dossier professionnel, et des cas pratiques. Prenez le temps de réfléchir avant de répondre. Si vous ne connaissez pas une réponse précise, expliquez votre raisonnement. Avez-vous des questions avant de commencer ?"*

**Réponse candidat** :  
✅ *"Bonjour. Non, tout est clair, je suis prêt(e)."*

---

### **Questions préliminaires (2-3 min)**

**Q0.1** : *"Pouvez-vous nous présenter brièvement votre parcours et ce qui vous a amené à passer le titre TSSR ?"*

**Réponse type** (1 min max) :
> *"J'ai suivi une formation TSSR de [durée] mois chez [organisme]. Mon parcours antérieur en [domaine] m'a donné des bases en [compétence], et j'ai souhaité me spécialiser dans l'administration systèmes et réseaux. Durant la formation, j'ai particulièrement apprécié [CCP préféré] et réalisé plusieurs projets pratiques comme [exemple : infrastructure VMware, serveur Linux DNS/DHCP]. Mon objectif est de travailler comme technicien systèmes et réseaux dans une PME/ESN."*

**Points clés** :
- Structuré (origine → formation → objectif)
- Précis (noms technologies, pas vague)
- Positif (envie d'apprendre)

---

**Q0.2** : *"Dans votre dossier professionnel, vous mentionnez avoir travaillé sur [projet X]. Pouvez-vous détailler votre rôle et les technologies utilisées ?"*

**Réponse type** :
> *"Sur ce projet, j'ai été chargé de [rôle]. J'ai utilisé [technologies précises : Windows Server 2022, VMware ESXi 8, Debian 12]. Les principales difficultés rencontrées étaient [problème technique], que j'ai résolues en [solution]. J'ai appris notamment [compétence acquise : gestion snapshots VMware, configuration DNS Bind9]."*

**Points clés** :
- Rôle clair (pas "on a fait" mais "j'ai fait")
- Technologies précises (versions, noms exacts)
- Problème/Solution (montre résolution incidents)

---

**Q0.3** : *"Quelles sont vos sources de veille technologique ?"*

**Réponse type** :
> *"Je suis plusieurs sources :*
> - *Blogs techniques (ex : IT-Connect, Le Monde Informatique)*
> - *Chaînes YouTube (ex : NetworkChuck, PowerCert)*
> - *Forums (ex : Reddit r/sysadmin, Stack Overflow)*
> - *Documentation officielle (Microsoft Docs, Debian Wiki)*
> - *Réseaux sociaux pros (LinkedIn groupes IT)*
> 
> *Je consacre environ 2-3h/semaine à la veille, notamment sur les CVE critiques et nouvelles versions logiciels (ex : Windows Server updates, VMware releases)."*

**Points clés** :
- Sources variées (pas que YouTube)
- Fréquence régulière
- Exemples concrets récents

---

---

## 🛠️ CCP1 : ASSISTER LES UTILISATEURS EN ENVIRONNEMENT INFORMATIQUE (7 minutes)

### **Question 1.1 : Méthodologie support**

**Jury** :  
*"Un utilisateur vous appelle et dit : 'Mon ordinateur est lent'. Comment procédez-vous ?"*

**Réponse attendue** (2 min) :

**Étape 1 : Qualification incident**
- *"Je commence par qualifier l'incident avec des questions ciblées :*
  - *Depuis quand la lenteur ? (ce matin, après redémarrage, progressive)*
  - *Sur quelles applications ? (toutes, ou seulement Word/Excel/navigateur)*
  - *D'autres symptômes ? (écran bleu, messages erreur, ventilateur bruyant)*"

**Étape 2 : Tests rapides à distance**
- *"Si accès distant possible (TeamViewer/AnyDesk) :*
  - *Gestionnaire des tâches (Ctrl+Shift+Esc) → Vérifier CPU/RAM/Disque*
  - *Identifier processus consommant ressources (ex : Chrome 50% RAM)*
  - *Vérifier espace disque libre (min 15% recommandé)*"

**Étape 3 : Actions correctives**
- *"Selon diagnostic :*
  - *Si processus bloqué → Terminer processus*
  - *Si disque plein → Nettoyer fichiers temporaires (cleanmgr)*
  - *Si RAM saturée → Fermer applications inutiles, vérifier malware*
  - *Si disque 100% → Désactiver Windows Search temporairement*"

**Étape 4 : Suivi**
- *"Je documente l'incident dans GLPI/ticket :*
  - *Symptômes, cause, solution*
  - *Si problème persiste > 15 min → Escalade niveau 2*"

**Critères évaluation** (points jury) :
- ✅ Méthodologie structurée (questions avant actions)
- ✅ Outils nommés (Gestionnaire tâches, cleanmgr, GLPI)
- ✅ Escalade mentionnée (conscience limites)

---

### **Question 1.2 : Communication non-technique**

**Jury** :  
*"Comment expliquez-vous à un utilisateur non-technique ce qu'est une adresse IP ?"*

**Réponse attendue** (1 min) :

> *"Une adresse IP, c'est comme une adresse postale pour votre ordinateur sur le réseau. De la même manière qu'on a besoin d'une adresse pour recevoir du courrier, votre PC a besoin d'une adresse IP pour recevoir et envoyer des données sur Internet ou sur le réseau de l'entreprise.*
> 
> *Par exemple, l'adresse IP 192.168.1.10, c'est l'identifiant unique de votre ordinateur. Le routeur l'utilise pour savoir où envoyer les pages web que vous demandez."*

**Analogies complémentaires** (si jury demande d'approfondir) :
- **Masque de sous-réseau** = Code postal (définit le quartier/réseau local)
- **Passerelle** = Bureau de poste (point de sortie vers Internet)
- **DNS** = Annuaire téléphonique (traduit www.google.com en adresse IP)

**Critères évaluation** :
- ✅ Analogie claire (adresse postale pertinente)
- ✅ Pas de jargon technique
- ✅ Exemple concret (192.168.1.10)

---

### **Question 1.3 : Priorisation incidents**

**Jury** :  
*"Vous recevez simultanément 3 tickets :*
- *Ticket A : Le PDG ne peut pas imprimer (urgent)*
- *Ticket B : 20 utilisateurs n'ont pas accès au partage réseau (bloquant)*
- *Ticket C : Demande installation logiciel (non urgent)*

*Comment priorisez-vous ?"*

**Réponse attendue** (2 min) :

**Analyse matrice impact/urgence** :

| Ticket | Impact | Urgence | Priorité |
|--------|--------|---------|----------|
| **B** | 🔴 Élevé (20 users) | 🔴 Critique (travail bloqué) | **P1 - Immédiate** |
| **A** | 🟠 Moyen (1 user VIP) | 🟠 Haute (PDG) | **P2 - Haute** |
| **C** | 🟢 Faible (confort) | 🟢 Basse (pas bloquant) | **P3 - Normale** |

**Verbalisation réponse** :

> *"Je traite d'abord le **Ticket B** (partage réseau) car il impacte 20 personnes et bloque leur travail. C'est une panne critique avec impact business fort.*
> 
> *Ensuite le **Ticket A** (impression PDG) : bien que 1 seul utilisateur, c'est un VIP et souvent urgent pour réunions. Je peux le traiter rapidement (5 min : vérifier file d'attente imprimante, redémarrer spooler).*
> 
> *Enfin le **Ticket C** (installation logiciel) : non bloquant, je le planifie pour plus tard dans la journée.*
> 
> *J'informe les utilisateurs des délais estimés via réponse ticket (transparence)."*

**Si jury challenge** : *"Mais le PDG insiste pour passer en priorité ?"*

**Réponse** :
> *"Je lui explique diplomatiquement qu'une panne impacte 20 collaborateurs (productivité entreprise), et que je résous son problème d'impression juste après (ETA 15 min). Si vraiment urgent, je peux demander à un collègue de prendre le Ticket A en parallèle."*

**Critères évaluation** :
- ✅ Matrice impact/urgence maîtrisée
- ✅ Justification logique (business > individuel)
- ✅ Communication proactive (informer utilisateurs)

---

### **Question 1.4 : ITIL - Incident vs Demande**

**Jury** :  
*"Quelle est la différence entre un incident et une demande de service en ITIL ?"*

**Réponse attendue** (1 min) :

**Tableau comparatif** :

| Critère | Incident | Demande de service |
|---------|----------|-------------------|
| **Définition** | Interruption ou dégradation service | Demande standard utilisateur |
| **Objectif** | Restaurer service normal rapidement | Fournir nouveau service/accès |
| **Exemple** | Imprimante en panne, serveur down | Créer compte AD, installer logiciel |
| **Processus ITIL** | Gestion des incidents | Gestion des demandes (Service Request) |
| **SLA** | Temps résolution court (urgence) | Temps exécution planifié (pas urgent) |

**Verbalisation** :

> *"Un **incident**, c'est une interruption non planifiée d'un service (ex : serveur de fichiers inaccessible). L'objectif est de restaurer le service au plus vite.*
> 
> *Une **demande de service**, c'est une requête standard de l'utilisateur (ex : créer un compte, installer Office). Ce n'est pas une panne, mais une prestation normale planifiable."*

**Critères évaluation** :
- ✅ Définitions ITIL correctes
- ✅ Exemples pertinents
- ✅ Distinction claire urgence/planification

---

### **Question 1.5 : Documentation**

**Jury** :  
*"Pourquoi est-il important de documenter chaque intervention dans un ticket ?"*

**Réponse attendue** (1 min) :

**5 raisons principales** :

1. **Traçabilité** : Historique complet interventions (audit, conformité ISO)
2. **Base de connaissances** : Réutiliser solutions pour incidents similaires (gain temps)
3. **Statistiques** : Identifier incidents récurrents (amélioration continue)
4. **Transfert compétences** : Collègues/équipe suivante peut reprendre dossier
5. **Reporting** : Indicateurs SLA (temps résolution, taux résolution niveau 1)

**Verbalisation** :

> *"Documenter chaque ticket permet de :*
> - *Constituer une base de connaissances (si même problème revient, solution disponible)*
> - *Assurer la traçabilité (audit, preuves interventions)*
> - *Générer des statistiques pour identifier problèmes récurrents*
> - *Faciliter le transfert entre techniciens (continuité service)*
> 
> *Dans GLPI, je renseigne systématiquement : symptômes, diagnostic, solution, temps passé."*

**Critères évaluation** :
- ✅ Au moins 3 raisons citées
- ✅ Mention outil (GLPI, ServiceNow)
- ✅ Exemples concrets (base connaissances, SLA)

---

---

## 🐧 CCP2 : MAINTENIR LES INFRASTRUCTURES SYSTÈMES (8 minutes)

### **Question 2.1 : Commandes Linux essentielles**

**Jury** :  
*"Citez 5 commandes Linux que vous utilisez régulièrement et leur utilité."*

**Réponse attendue** (2 min) :

| Commande | Utilité | Exemple |
|----------|---------|---------|
| **ls -lh** | Lister fichiers avec détails (taille, droits) | `ls -lh /var/log/` |
| **grep** | Rechercher texte dans fichiers/logs | `grep "error" /var/log/syslog` |
| **systemctl** | Gérer services (start/stop/status) | `systemctl status apache2` |
| **df -h** | Vérifier espace disque partitions | `df -h` (affiche usage /) |
| **tail -f** | Suivre logs en temps réel | `tail -f /var/log/auth.log` |

**Bonus (si temps)** :
- **chmod** : Modifier droits fichiers (`chmod 755 script.sh`)
- **ps aux** : Lister processus en cours (`ps aux | grep apache`)
- **top** : Monitoring CPU/RAM temps réel

**Verbalisation** :

> *"Les 5 commandes que j'utilise quotidiennement :*
> 1. *`ls -lh` pour lister fichiers avec tailles et permissions*
> 2. *`grep` pour filtrer logs, par exemple chercher erreurs dans syslog*
> 3. *`systemctl status` pour vérifier état services (Apache, SSH)*
> 4. *`df -h` pour surveiller espace disque, éviter saturation*
> 5. *`tail -f` pour suivre logs en temps réel pendant diagnostic"*

**Critères évaluation** :
- ✅ 5 commandes nommées correctement
- ✅ Utilité expliquée (pas juste le nom)
- ✅ Exemples concrets (pas que théorie)

---

### **Question 2.2 : Gestion logs Linux**

**Jury** :  
*"Un serveur Debian semble ralenti. Comment utilisez-vous les logs pour diagnostiquer ?"*

**Réponse attendue** (2 min) :

**Étape 1 : Identifier logs pertinents**

| Fichier log | Contenu | Commande analyse |
|-------------|---------|------------------|
| `/var/log/syslog` | Logs système généraux | `tail -n 100 /var/log/syslog` |
| `/var/log/auth.log` | Connexions SSH, authentifications | `grep "Failed password" /var/log/auth.log` |
| `/var/log/kern.log` | Erreurs matérielles (disque, RAM) | `grep -i "error" /var/log/kern.log` |
| `/var/log/messages` (RedHat) | Équivalent syslog sur CentOS | `less /var/log/messages` |

**Étape 2 : Recherche erreurs**

```bash
# Chercher erreurs récentes (dernières 50 lignes)
tail -n 50 /var/log/syslog | grep -i "error\|fail\|critical"

# Suivre logs en temps réel
tail -f /var/log/syslog

# Compter occurrences erreur spécifique
grep "out of memory" /var/log/syslog | wc -l
```

**Étape 3 : Analyse spécifique selon symptômes**

- **Si lenteur disque** :
  ```bash
  dmesg | grep -i "I/O error"  # Erreurs disque matériel
  iostat -x 5  # Stats I/O disque
  ```

- **Si lenteur CPU** :
  ```bash
  grep "CPU" /var/log/syslog  # Surcharge CPU
  top  # Processus consommant CPU
  ```

- **Si lenteur réseau** :
  ```bash
  grep -i "timeout\|unreachable" /var/log/syslog
  ```

**Verbalisation** :

> *"Pour diagnostiquer une lenteur via les logs :*
> 1. *Je consulte `/var/log/syslog` pour vue d'ensemble*
> 2. *Je filtre avec grep pour trouver erreurs : `grep -i "error" /var/log/syslog`*
> 3. *Si suspicion disque, je vérifie `/var/log/kern.log` pour erreurs matérielles*
> 4. *J'utilise `tail -f` pour suivre logs en temps réel pendant tests*
> 5. *Je corrèle avec `top` et `df -h` pour confirmer cause (CPU/RAM/disque)"*

**Critères évaluation** :
- ✅ Connaissance fichiers logs Debian
- ✅ Commandes grep/tail maîtrisées
- ✅ Méthodologie (pas juste 1 log, mais corrélation)

---

### **Question 2.3 : Permissions fichiers Linux**

**Jury** :  
*"Expliquez ce que signifie `chmod 755 fichier.sh`."*

**Réponse attendue** (1 min) :

**Décomposition 755** :

| Chiffre | Utilisateur | Binaire | Droits | Signification |
|---------|-------------|---------|--------|---------------|
| **7** | Propriétaire (owner) | 111 | rwx | Lecture + Écriture + Exécution |
| **5** | Groupe (group) | 101 | r-x | Lecture + Exécution (pas écriture) |
| **5** | Autres (others) | 101 | r-x | Lecture + Exécution (pas écriture) |

**Calcul** :
- 7 = 4 (r) + 2 (w) + 1 (x) = **rwx**
- 5 = 4 (r) + 0 (w) + 1 (x) = **r-x**

**Verbalisation** :

> *"`chmod 755 fichier.sh` donne :*
> - *Au **propriétaire** : tous les droits (lecture, écriture, exécution)*
> - *Au **groupe** et **autres** : lecture et exécution uniquement*
> 
> *C'est typique pour un script shell : le créateur peut le modifier, les autres peuvent l'exécuter mais pas le modifier. C'est plus sécurisé que 777 qui donnerait écriture à tout le monde."*

**Cas d'usage** :
- **755** : Scripts exécutables publics (`/usr/local/bin/backup.sh`)
- **700** : Scripts privés admin (`/root/scripts/sensible.sh`)
- **644** : Fichiers config lecture seule (`/etc/apache2/sites-available/site.conf`)

**Critères évaluation** :
- ✅ Décomposition 7-5-5 expliquée
- ✅ Distinction propriétaire/groupe/autres
- ✅ Cas d'usage pertinent (script)

---

### **Question 2.4 : Sauvegarde tar**

**Jury** :  
*"Quelle commande utilisez-vous pour créer une sauvegarde compressée de /home avec tar ?"*

**Réponse attendue** (1 min) :

**Commande de base** :
```bash
tar -czf /backup/home_$(date +%Y%m%d).tar.gz /home
```

**Décomposition options** :

| Option | Signification | Utilité |
|--------|--------------|---------|
| **-c** | Create | Créer une archive |
| **-z** | gZip | Compresser avec gzip |
| **-f** | File | Spécifier nom fichier archive |
| **-v** | Verbose (optionnel) | Afficher fichiers archivés |

**Commande complète avec options avancées** :
```bash
tar -czvf /backup/home_$(date +%Y%m%d_%H%M).tar.gz \
    --exclude=/home/*/.cache \
    --exclude=/home/*/Downloads \
    /home
```

**Explications** :
- `$(date +%Y%m%d)` : Horodatage (ex : 20251112)
- `--exclude` : Exclure dossiers cache/Downloads (gain espace)

**Extraction** (si jury demande) :
```bash
tar -xzf /backup/home_20251112.tar.gz -C /restore/
```

**Verbalisation** :

> *"Je crée une sauvegarde compressée avec :*
> ```bash
> tar -czf /backup/home_$(date +%Y%m%d).tar.gz /home
> ```
> *Options :*
> - *`-c` : créer archive*
> - *`-z` : compresser gzip*
> - *`-f` : nom fichier*
> - *`$(date +%Y%m%d)` : horodatage automatique*
> 
> *J'exclus les dossiers cache avec `--exclude` pour économiser espace."*

**Critères évaluation** :
- ✅ Options -czf présentes
- ✅ Horodatage mentionné (bonne pratique)
- ✅ Option --exclude (montre expertise)

---

### **Question 2.5 : Gestion processus**

**Jury** :  
*"Un processus ne répond plus. Quelle est la différence entre `kill -15` et `kill -9` ?"*

**Réponse attendue** (1 min) :

**Tableau comparatif** :

| Signal | Nom | Comportement | Usage |
|--------|-----|--------------|-------|
| **-15** | SIGTERM (Terminate) | Arrêt **gracieux** (processus peut cleanup) | **Toujours essayer en premier** |
| **-9** | SIGKILL (Kill) | Arrêt **forcé immédiat** (pas de cleanup) | **Dernier recours** si -15 échoue |

**Verbalisation** :

> *"`kill -15 PID` envoie un signal **SIGTERM** : le processus reçoit une demande d'arrêt propre. Il peut fermer fichiers ouverts, libérer ressources, terminer proprement. C'est comme dire "s'il vous plaît, arrêtez-vous".*
> 
> *`kill -9 PID` envoie **SIGKILL** : le système tue immédiatement le processus, sans lui laisser temps de cleanup. Risque de corruption données si fichiers ouverts. C'est le dernier recours si -15 ne fonctionne pas.*
> 
> *Bonne pratique : Toujours `kill -15` d'abord, attendre 10 secondes, puis `kill -9` si nécessaire."*

**Exemple pratique** :
```bash
# Étape 1 : Identifier PID processus bloqué
ps aux | grep apache2
# Output : root  1234  50.0  5.2 ...

# Étape 2 : Arrêt gracieux
kill -15 1234

# Attendre 10 sec, vérifier si processus existe encore
ps -p 1234

# Étape 3 : Si toujours là, forcer
kill -9 1234
```

**Critères évaluation** :
- ✅ Différence gracieux/forcé expliquée
- ✅ Ordre priorité (-15 avant -9)
- ✅ Analogie claire (si mentionnée)

---

### **Question 2.6 : Automatisation cron**

**Jury** :  
*"Vous devez exécuter un script de sauvegarde tous les jours à 3h du matin. Comment configurez-vous cron ?"*

**Réponse attendue** (2 min) :

**Étape 1 : Éditer crontab**

```bash
crontab -e  # Éditer crontab utilisateur courant
# OU
sudo crontab -e  # Éditer crontab root (pour tâches système)
```

**Étape 2 : Ajouter ligne cron**

**Syntaxe générale** :
```
┌───────────── minute (0-59)
│ ┌─────────── heure (0-23)
│ │ ┌───────── jour du mois (1-31)
│ │ │ ┌─────── mois (1-12)
│ │ │ │ ┌───── jour de la semaine (0-7, 0 et 7 = dimanche)
│ │ │ │ │
* * * * * commande
```

**Solution question** :
```bash
0 3 * * * /usr/local/bin/backup.sh >> /var/log/backup.log 2>&1
```

**Décomposition** :
- `0` = minute 0 (début de l'heure)
- `3` = 3h du matin
- `* * *` = tous les jours, tous les mois, tous les jours de la semaine
- `>> /var/log/backup.log` = Rediriger output vers log
- `2>&1` = Rediriger erreurs (stderr) vers output (stdout)

**Exemples complémentaires** :

| Fréquence | Syntaxe cron | Description |
|-----------|--------------|-------------|
| Toutes les heures | `0 * * * *` | Minute 0 de chaque heure |
| Tous les lundis 9h | `0 9 * * 1` | Lundi = 1 |
| 1er du mois 00h | `0 0 1 * *` | Jour 1 |
| Toutes les 5 min | `*/5 * * * *` | Diviseur */5 |

**Vérification crontab** :
```bash
crontab -l  # Lister crontab actuel
```

**Verbalisation** :

> *"Je configure cron avec :*
> ```bash
> sudo crontab -e  # Éditer crontab root
> ```
> *J'ajoute la ligne :*
> ```bash
> 0 3 * * * /usr/local/bin/backup.sh >> /var/log/backup.log 2>&1
> ```
> *Cela signifie : exécuter le script tous les jours à 3h00, logger output et erreurs dans backup.log.*
> 
> *Je vérifie avec `crontab -l` que la tâche est bien enregistrée."*

**Critères évaluation** :
- ✅ Syntaxe cron correcte (5 champs)
- ✅ Redirection logs mentionnée
- ✅ Commande crontab -e nommée

---

---

## 📊 RÉCAPITULATIF PARTIE 2A

### **Questions couvertes (12 questions)**

| CCP | Nombre questions | Durée |
|-----|-----------------|-------|
| **Introduction** | 3 questions préliminaires | 5 min |
| **CCP1 (Support)** | 5 questions | 7 min |
| **CCP2 (Linux/Maintenance)** | 6 questions | 8 min |
| **TOTAL PARTIE 2A** | **14 questions** | **20 min** |

### **Compétences évaluées**

✅ Méthodologie support utilisateur (qualification incidents, priorisation)  
✅ Communication non-technique (vulgarisation concepts)  
✅ ITIL (incident vs demande, documentation)  
✅ Commandes Linux (ls, grep, systemctl, df, tail)  
✅ Gestion logs Linux (/var/log/syslog, auth.log, kern.log)  
✅ Permissions fichiers (chmod 755, calcul droits)  
✅ Sauvegardes tar (compression, exclusions, horodatage)  
✅ Gestion processus (kill -15 vs -9, SIGTERM/SIGKILL)  
✅ Automatisation cron (syntaxe, redirection logs)  

---


---

# 🎤 SIMULATION ORALE TSSR - PARTIE 2B : ENTRETIEN TECHNIQUE (CCP3 - RÉSEAU)

**Durée cette partie** : 7 minutes  
**Questions** : 5 questions sur déploiement services réseau

---

## 🌐 CCP3 : DÉPLOYER DES SERVICES RÉSEAUX (7 minutes)

### **Question 3.1 : Subnetting CIDR**

**Jury** :  
*"Vous avez le réseau 172.16.0.0/16 et devez créer 4 sous-réseaux de tailles égales. Quels sont les sous-réseaux résultants et leur masque ?"*

**Réponse attendue** (2 min) :

**Étape 1 : Calcul nouveau masque**

- Réseau initial : `172.16.0.0/16` (masque 255.255.0.0)
- Besoin : **4 sous-réseaux** = 2² = **2 bits supplémentaires**
- Nouveau masque : /16 + 2 = **/18**
- Masque décimal : **255.255.192.0**

**Étape 2 : Calcul taille bloc**

- /18 = 2^(32-18) = 2^14 = **16384 hôtes** par sous-réseau
- Incrément : 256 - 192 = **64** (3ᵉ octet)

**Étape 3 : Sous-réseaux**

| Sous-réseau | Plage réseau | Plage hôtes | Broadcast | Masque |
|-------------|--------------|-------------|-----------|--------|
| **SR1** | 172.16.0.0/18 | 172.16.0.1 - 172.16.63.254 | 172.16.63.255 | /18 |
| **SR2** | 172.16.64.0/18 | 172.16.64.1 - 172.16.127.254 | 172.16.127.255 | /18 |
| **SR3** | 172.16.128.0/18 | 172.16.128.1 - 172.16.191.254 | 172.16.191.255 | /18 |
| **SR4** | 172.16.192.0/18 | 172.16.192.1 - 172.16.255.254 | 172.16.255.255 | /18 |

**Vérification** :
- 4 sous-réseaux ✅
- Pas de chevauchement ✅
- Tailles égales (16384 hôtes chacun) ✅

**Verbalisation** :

> *"Pour créer 4 sous-réseaux égaux depuis 172.16.0.0/16 :*
> 1. *4 sous-réseaux = 2² bits → Nouveau masque /18 (255.255.192.0)*
> 2. *Incrément 64 sur 3ᵉ octet (256-192)*
> 3. *Sous-réseaux :*
>    - *172.16.0.0/18*
>    - *172.16.64.0/18*
>    - *172.16.128.0/18*
>    - *172.16.192.0/18*
> 4. *Chaque sous-réseau : 16382 hôtes utilisables (2^14 - 2)"*

**Critères évaluation** :
- ✅ Calcul masque /18 correct
- ✅ 4 sous-réseaux sans chevauchement
- ✅ Méthodologie claire (incrément 64)

---

### **Question 3.2 : DNS - Zones et enregistrements**

**Jury** :  
*"Expliquez la différence entre une zone DNS principale et secondaire. Quel est l'intérêt d'avoir une zone secondaire ?"*

**Réponse attendue** (1.5 min) :

**Tableau comparatif** :

| Critère | Zone Principale (Primary) | Zone Secondaire (Secondary) |
|---------|--------------------------|----------------------------|
| **Rôle** | Maître (Master) | Esclave (Slave) |
| **Modifications** | ✅ Lecture/Écriture | ❌ Lecture seule |
| **Données** | Fichier local éditable | Copie depuis zone principale |
| **Mise à jour** | Manuelle (admin modifie) | Automatique (transfert zone AXFR) |
| **Réplication** | Envoie vers secondaires | Reçoit depuis principale |

**Avantages zone secondaire** :

1. **Haute disponibilité** :
   - Si serveur DNS principal tombe, le secondaire répond
   - Pas d'interruption service résolution noms

2. **Répartition charge** :
   - Plusieurs serveurs DNS partagent requêtes
   - Réduit charge serveur principal

3. **Redondance géographique** :
   - Zone secondaire sur site distant
   - Si panne réseau, site distant garde résolution DNS locale

**Exemple pratique** :

```
Entreprise MegaStore :
- DNS Principal : 192.168.10.10 (site Paris)
- DNS Secondaire : 192.168.20.10 (site Lyon)

Configuration DHCP clients :
- DNS préféré : 192.168.10.10
- DNS auxiliaire : 192.168.20.10

→ Si Paris HS, clients basculent automatiquement sur Lyon
```

**Transfert de zone (AXFR)** :

```bash
# Serveur DNS principal (Bind9) : /etc/bind/named.conf.local
zone "megastore.local" {
    type master;
    file "/etc/bind/db.megastore.local";
    allow-transfer { 192.168.20.10; };  # Autoriser secondaire
};

# Serveur DNS secondaire
zone "megastore.local" {
    type slave;
    file "/var/cache/bind/db.megastore.local";
    masters { 192.168.10.10; };  # Depuis principal
};
```

**Verbalisation** :

> *"Une **zone principale** contient les données DNS originales et modifiables. L'admin peut ajouter/supprimer enregistrements.*
> 
> *Une **zone secondaire** est une copie en lecture seule, synchronisée automatiquement depuis la zone principale via transfert AXFR.*
> 
> *Intérêts zone secondaire :*
> - *Haute disponibilité : si DNS principal tombe, le secondaire prend le relais*
> - *Répartition charge : plusieurs serveurs répondent aux requêtes*
> - *Redondance géographique : un secondaire sur site distant protège contre pannes réseau*
> 
> *Exemple : DNS principal Paris (192.168.10.10), secondaire Lyon (192.168.20.10)."*

**Critères évaluation** :
- ✅ Différences principale/secondaire claires
- ✅ Au moins 2 avantages secondaire cités
- ✅ Mention transfert zone AXFR

---

### **Question 3.3 : DHCP - Scope et options**

**Jury** :  
*"Quelles sont les options DHCP essentielles à configurer pour qu'un client obtienne une configuration réseau complète ?"*

**Réponse attendue** (1.5 min) :

**Options DHCP obligatoires** :

| Option | Code | Nom | Valeur exemple | Utilité |
|--------|------|-----|----------------|---------|
| **Adresse IP** | - | IP Address | 192.168.10.100 | Adresse attribuée au client |
| **Masque** | 001 | Subnet Mask | 255.255.255.0 | Définit le réseau local |
| **Passerelle** | 003 | Router | 192.168.10.1 | Accès Internet/autres réseaux |
| **Serveurs DNS** | 006 | DNS Servers | 192.168.10.10, 8.8.8.8 | Résolution noms domaines |
| **Durée bail** | 051 | Lease Time | 86400 sec (1 jour) | Validité configuration |

**Options complémentaires** (bonus) :

| Option | Code | Nom | Utilité |
|--------|------|-----|---------|
| **Nom domaine** | 015 | Domain Name | megastore.local | Suffixe DNS automatique |
| **Serveur WINS** | 044 | WINS Servers | (NetBIOS, obsolète) | Résolution noms NetBIOS |
| **Serveur NTP** | 042 | NTP Servers | 192.168.10.10 | Synchronisation horaire |

**Configuration Windows Server DHCP** :

```powershell
# Créer étendue DHCP
Add-DhcpServerv4Scope -Name "VLAN10-Bureaux" `
    -StartRange 192.168.10.100 `
    -EndRange 192.168.10.200 `
    -SubnetMask 255.255.255.0 `
    -LeaseDuration 1.00:00:00  # 1 jour

# Configurer options
Set-DhcpServerv4OptionValue -ScopeId 192.168.10.0 `
    -Router 192.168.10.1 `  # Option 003
    -DnsServer 192.168.10.10,8.8.8.8 `  # Option 006
    -DnsDomain "megastore.local"  # Option 015
```

**Processus DORA (rappel)** :

```
Client                           Serveur DHCP
  |                                    |
  |-- DISCOVER (broadcast) ----------->|
  |                                    |
  |<-- OFFER (proposition IP) ---------|
  |                                    |
  |-- REQUEST (acceptation) ---------->|
  |                                    |
  |<-- ACK (confirmation) -------------|
  |                                    |
Configuration active
```

**Verbalisation** :

> *"Les options DHCP essentielles pour une config complète :*
> 1. **Adresse IP** : attribuée automatiquement depuis plage (ex : 192.168.10.100)*
> 2. **Masque de sous-réseau** (option 001) : définit le réseau local (ex : 255.255.255.0)*
> 3. **Passerelle par défaut** (option 003) : routeur pour accès Internet (ex : 192.168.10.1)*
> 4. **Serveurs DNS** (option 006) : résolution noms (ex : 192.168.10.10, 8.8.8.8)*
> 5. **Durée du bail** (option 051) : temps validité config (ex : 24h)*
> 
> *Options complémentaires utiles : nom de domaine (015), serveur NTP (042).*
> 
> *Sans ces options, le client ne pourrait pas accéder à Internet ni résoudre noms de domaines."*

**Critères évaluation** :
- ✅ 4 options essentielles citées (IP, masque, passerelle, DNS)
- ✅ Numéros options connus (003, 006)
- ✅ Exemple valeurs concrètes

---

### **Question 3.4 : VLANs - Segmentation réseau**

**Jury** :  
*"Pourquoi utiliser des VLANs dans un réseau d'entreprise ? Donnez 2 avantages."*

**Réponse attendue** (1 min) :

**Définition VLAN** :

> *"Un VLAN (Virtual LAN) est un réseau local virtuel qui segmente logiquement un réseau physique. Plusieurs VLANs peuvent coexister sur un même switch."*

**Avantages principaux** :

**1. Segmentation sécurité** :
- Isoler départements (VLAN10 RH, VLAN20 IT, VLAN30 Invités)
- Trafic VLAN10 ne voit pas VLAN20 (sans routage inter-VLAN)
- Limiter propagation malware/attaques

**Exemple** :
```
VLAN 10 (RH) : PC comptabilité → données confidentielles
VLAN 30 (Invités) : WiFi visiteurs → accès limité Internet

→ Visiteurs ne peuvent PAS accéder serveurs RH
```

**2. Optimisation performances** :
- Réduire domaine de broadcast (broadcast confiné au VLAN)
- Améliorer bande passante (moins de trafic inutile)
- Priorisation trafic (QoS par VLAN : VoIP prioritaire)

**Exemple** :
```
VLAN 100 (VoIP) : Téléphones IP → QoS prioritaire
VLAN 200 (DATA) : PCs bureautique → Best effort

→ Qualité appels préservée même si DATA saturé
```

**3. Flexibilité gestion** (bonus) :
- Déplacer utilisateur = changer port VLAN (pas câblage physique)
- Topologie logique indépendante de physique

**Configuration Cisco** :

```cisco
! Créer VLANs
Switch(config)# vlan 10
Switch(config-vlan)# name RH
Switch(config-vlan)# exit

Switch(config)# vlan 20
Switch(config-vlan)# name IT

! Assigner port à VLAN (access mode)
Switch(config)# interface Fa0/1
Switch(config-if)# switchport mode access
Switch(config-if)# switchport access vlan 10

! Port trunk (entre switches, transporte tous VLANs)
Switch(config)# interface Gi0/1
Switch(config-if)# switchport mode trunk
Switch(config-if)# switchport trunk allowed vlan 10,20
```

**Verbalisation** :

> *"Les VLANs permettent :*
> 
> **1. Segmentation sécurité** : Isoler départements. Par exemple, VLAN10 pour RH (données sensibles), VLAN30 pour invités WiFi. Les invités ne peuvent pas accéder aux serveurs RH.*
> 
> **2. Optimisation performances** : Réduire broadcasts (chaque VLAN = domaine broadcast séparé). On peut aussi prioriser trafic (QoS) : VLAN VoIP prioritaire sur VLAN DATA.*
> 
> *Exemple concret : Entreprise avec 3 VLANs (RH/IT/Invités), tous sur même switch physique, mais trafic isolé."*

**Critères évaluation** :
- ✅ 2 avantages distincts cités
- ✅ Exemples concrets (RH/Invités, VoIP/DATA)
- ✅ Mention isolation trafic

---

### **Question 3.5 : Routage inter-VLAN**

**Jury** :  
*"Vous avez 3 VLANs (10, 20, 30) sur un switch. Comment permettre la communication entre VLANs ?"*

**Réponse attendue** (2 min) :

**Principe** :

> *"Les VLANs sont isolés par défaut. Pour communiquer entre VLANs, il faut un **routeur** (physique ou intégré au switch Layer 3)."*

**2 méthodes principales** :

---

**Méthode 1 : Router-on-a-stick (1 routeur, 1 lien trunk)**

**Schéma** :
```
        [ROUTEUR]
           | Gi0/0 (trunk)
           |
        [SWITCH]
         /  |  \
      VLAN VLAN VLAN
       10   20   30
```

**Configuration Cisco** :

```cisco
! === SWITCH ===
! Port trunk vers routeur
Switch(config)# interface Gi0/1
Switch(config-if)# switchport mode trunk
Switch(config-if)# switchport trunk allowed vlan 10,20,30

! === ROUTEUR ===
! Sous-interfaces (1 par VLAN)
Router(config)# interface Gi0/0
Router(config-if)# no shutdown

Router(config)# interface Gi0/0.10
Router(config-subif)# encapsulation dot1Q 10
Router(config-subif)# ip address 192.168.10.1 255.255.255.0

Router(config)# interface Gi0/0.20
Router(config-subif)# encapsulation dot1Q 20
Router(config-subif)# ip address 192.168.20.1 255.255.255.0

Router(config)# interface Gi0/0.30
Router(config-subif)# encapsulation dot1Q 30
Router(config-subif)# ip address 192.168.30.1 255.255.255.0
```

**Avantages** :
- ✅ 1 seul lien physique routeur-switch
- ✅ Fonctionne avec switch Layer 2 standard

**Inconvénient** :
- ❌ Lien trunk peut devenir bottleneck (tout trafic inter-VLAN passe par là)

---

**Méthode 2 : Switch Layer 3 avec SVI (interfaces VLAN virtuelles)**

**Schéma** :
```
    [SWITCH LAYER 3]
    (routing activé)
     /     |     \
  VLAN   VLAN   VLAN
   10     20     30
```

**Configuration Cisco (Switch 3560/3750)** :

```cisco
! Activer routage IP
Switch(config)# ip routing

! Créer interfaces SVI (1 par VLAN)
Switch(config)# interface vlan 10
Switch(config-if)# ip address 192.168.10.1 255.255.255.0
Switch(config-if)# no shutdown

Switch(config)# interface vlan 20
Switch(config-if)# ip address 192.168.20.1 255.255.255.0
Switch(config-if)# no shutdown

Switch(config)# interface vlan 30
Switch(config-if)# ip address 192.168.30.1 255.255.255.0
Switch(config-if)# no shutdown
```

**Avantages** :
- ✅ Routage à vitesse wire-speed (matériel, pas software)
- ✅ Pas de lien externe vers routeur
- ✅ Scalable (support 100+ VLANs)

**Inconvénient** :
- ❌ Nécessite switch Layer 3 (plus cher)

---

**Vérification** (commune aux 2 méthodes) :

```cisco
! Vérifier table routage
Switch# show ip route
C    192.168.10.0/24 is directly connected, Vlan10
C    192.168.20.0/24 is directly connected, Vlan20
C    192.168.30.0/24 is directly connected, Vlan30

! Test ping depuis VLAN10 vers VLAN20
PC-VLAN10> ping 192.168.20.5
✅ Reply from 192.168.20.5
```

**Verbalisation** :

> *"Pour faire communiquer VLANs 10, 20, 30 :*
> 
> **Méthode 1 (Router-on-a-stick)** : Créer sous-interfaces sur routeur (Gi0/0.10, .20, .30) avec encapsulation dot1Q. 1 lien trunk vers switch.*
> 
> **Méthode 2 (Switch Layer 3)** : Activer `ip routing` sur switch, créer interfaces SVI (interface vlan 10, 20, 30) avec adresses IP. Routage direct sans routeur externe.*
> 
> *Je préfère Méthode 2 si switch Layer 3 disponible (performances meilleures).*
> 
> *Chaque VLAN a sa passerelle (ex : VLAN10 → 192.168.10.1). Les PCs configurent cette IP comme default gateway."*

**Critères évaluation** :
- ✅ 2 méthodes citées (router-on-a-stick, SVI)
- ✅ Encapsulation dot1Q mentionnée (méthode 1)
- ✅ Commande `ip routing` connue (méthode 2)

---

---

## 📊 RÉCAPITULATIF PARTIE 2B

### **Questions couvertes (5 questions CCP3)**

| Question | Sujet | Durée |
|----------|-------|-------|
| 3.1 | Subnetting CIDR (172.16.0.0/16 → 4 SR) | 2 min |
| 3.2 | DNS (zones principale/secondaire) | 1.5 min |
| 3.3 | DHCP (options essentielles) | 1.5 min |
| 3.4 | VLANs (avantages segmentation) | 1 min |
| 3.5 | Routage inter-VLAN (2 méthodes) | 2 min |
| **TOTAL PARTIE 2B** | **CCP3 - Réseau** | **7 min** |

### **Compétences évaluées**

✅ **Subnetting** : Calcul masques CIDR, découpage réseaux égaux  
✅ **DNS** : Zones principale/secondaire, transfert AXFR, haute disponibilité  
✅ **DHCP** : Options 001/003/006, configuration Windows Server, processus DORA  
✅ **VLANs** : Segmentation sécurité, isolation broadcasts, QoS  
✅ **Routage inter-VLAN** : Router-on-a-stick vs Switch L3 SVI, dot1Q, ip routing  

---


---

# 🎤 SIMULATION ORALE TSSR - PARTIE 2C : ENTRETIEN TECHNIQUE (CCP4 - WINDOWS/AD)

**Durée cette partie** : 7 minutes  
**Questions** : 5 questions sur Windows Server et Active Directory

---

## 🪟 CCP4 : SÉCURISER ET ADMINISTRER INFRASTRUCTURES WINDOWS (7 minutes)

### **Question 4.1 : Active Directory - Structure**

**Jury** :  
*"Expliquez la hiérarchie d'objets dans Active Directory : domaine, OU, groupes, utilisateurs."*

**Réponse attendue** (1.5 min) :

**Hiérarchie AD (du plus grand au plus petit)** :

```
FORÊT (megastore.forest)
 └── DOMAINE (megastore.local)
      ├── OU (Organizational Unit) = Conteneur logique
      │    ├── OU_Paris
      │    │    ├── OU_RH
      │    │    │    ├── 👤 Utilisateurs (jdupont, mlambert)
      │    │    │    └── 👥 Groupes (GRP_RH, GRP_Paies)
      │    │    └── OU_IT
      │    │         ├── 👤 Utilisateurs (admin, technicien)
      │    │         └── 💻 Ordinateurs (SRV-DC01, PC-IT01)
      │    └── OU_Lyon
      │         └── ...
      └── CN (Containers par défaut)
           ├── Users
           ├── Computers
           └── Domain Controllers
```

**Définitions** :

| Objet | Description | Utilité |
|-------|-------------|---------|
| **Forêt** | Ensemble de domaines partageant schéma AD commun | Niveau le plus haut (trust entre domaines) |
| **Domaine** | Limite de réplication, de sécurité, d'administration | Ex : megastore.local |
| **OU** | Conteneur logique personnalisable | Organiser objets, appliquer GPO |
| **Utilisateur** | Compte personne (login/MDP) | Authentification, permissions |
| **Groupe** | Collection utilisateurs/ordinateurs | Simplifier gestion permissions |
| **Ordinateur** | Objet machine membre du domaine | Authentification machine, GPO ordinateur |

**Bonnes pratiques structure OU** :

✅ **Par géographie + département** :
```
megastore.local
 ├── Paris
 │    ├── RH
 │    ├── IT
 │    └── Compta
 └── Lyon
      ├── RH
      └── Production
```

✅ **Séparer utilisateurs, groupes, ordinateurs** :
```
megastore.local
 ├── _Users
 │    ├── RH
 │    └── IT
 ├── _Groups
 │    ├── GRP_RH
 │    └── GRP_IT
 └── _Computers
      ├── Workstations
      └── Servers
```

**Avantage OU vs Containers par défaut** :
- OU = Peut recevoir GPO ✅
- Container "Users" = Pas de GPO ❌ (raison de créer OU personnalisées)

**Exemple commandes PowerShell** :

```powershell
# Créer OU
New-ADOrganizationalUnit -Name "RH" -Path "OU=Paris,DC=megastore,DC=local"

# Créer utilisateur dans OU
New-ADUser -Name "Jean Dupont" -SamAccountName "jdupont" `
    -Path "OU=RH,OU=Paris,DC=megastore,DC=local" `
    -AccountPassword (ConvertTo-SecureString "P@ssw0rd!" -AsPlainText -Force) `
    -Enabled $true

# Créer groupe dans OU
New-ADGroup -Name "GRP_RH" -GroupScope Global `
    -Path "OU=RH,OU=Paris,DC=megastore,DC=local"
```

**Verbalisation** :

> *"La hiérarchie AD est :*
> 1. **Forêt** : Niveau le plus élevé, peut contenir plusieurs domaines*
> 2. **Domaine** : Limite administrative (ex : megastore.local)*
> 3. **OU (Organizational Unit)** : Conteneurs personnalisables pour organiser objets. Utilisés pour appliquer GPO.*
> 4. **Objets** : Utilisateurs, groupes, ordinateurs à l'intérieur des OU*
> 
> *Exemple : OU_Paris contient OU_RH, qui contient utilisateurs jdupont, mlambert et groupe GRP_RH.*
> 
> *Avantage OU : On peut appliquer GPO spécifiques (ex : GPO sécurité uniquement sur OU_RH)."*

**Critères évaluation** :
- ✅ Hiérarchie Forêt → Domaine → OU → Objets
- ✅ Distinction OU vs Container par défaut (GPO)
- ✅ Exemple structure pertinente

---

### **Question 4.2 : GPO - Stratégies de groupe**

**Jury** :  
*"Comment bloquer l'accès aux clés USB via GPO pour les utilisateurs du service RH ?"*

**Réponse attendue** (2 min) :

**Étape 1 : Créer GPO**

1. Ouvrir **Group Policy Management** (gpmc.msc)
2. Clic droit sur OU **RH** → **Create a GPO in this domain, and Link it here**
3. Nommer : **GPO_Blocage_USB_RH**

**Étape 2 : Configurer restriction USB**

**Chemin GPO** :
```
Computer Configuration
 └── Policies
      └── Administrative Templates
           └── System
                └── Removable Storage Access
```

**Paramètres à activer** :

| Paramètre GPO | Action | Effet |
|---------------|--------|-------|
| **Removable Disks: Deny read access** | ✅ Enabled | Empêche lecture USB |
| **Removable Disks: Deny write access** | ✅ Enabled | Empêche écriture USB |
| **All Removable Storage classes: Deny all access** | ✅ Enabled | Bloque tout (USB, CD, etc.) |

**Configuration écran** :
```
[x] Enabled
    ○ Not Configured
    ○ Disabled
```

**Étape 3 : Lier GPO et appliquer**

1. GPO automatiquement liée à OU **RH** (créée dessus)
2. Vérifier liaison : OU RH → onglet **Linked Group Policy Objects**
3. Forcer application immédiate sur clients :

```powershell
# Sur poste utilisateur RH
gpupdate /force

# Vérifier GPO appliquées
gpresult /r
```

**Étape 4 : Tester**

- Brancher clé USB sur PC utilisateur RH
- **Résultat attendu** :
  - Windows détecte périphérique
  - Message : *"Accès refusé par stratégie de groupe"*
  - Clé USB non accessible dans Explorateur

**Alternative : Blocage via Registre** (si GPO pas disponible)

```powershell
# Désactiver USB Storage (nécessite redémarrage)
Set-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Services\USBSTOR" `
    -Name "Start" -Value 4  # 4 = Disabled
```

**Exceptions possibles** :

Si certains utilisateurs RH ont besoin USB (ex : manager) :

**Méthode 1 : Filtrage sécurité GPO**

1. GPO_Blocage_USB_RH → onglet **Delegation**
2. **Advanced** → Ajouter utilisateur exception (ex : manager_rh)
3. **Deny** sur permission **Apply Group Policy**

**Méthode 2 : Groupe AD avec droits**

```powershell
# Créer groupe exception
New-ADGroup -Name "GRP_RH_USB_Autorisés" -GroupScope Global

# Ajouter manager
Add-ADGroupMember -Identity "GRP_RH_USB_Autorisés" -Members "manager_rh"

# Dans GPO : Computer Config → Policies → Windows Settings → Security Settings
# → Local Policies → User Rights Assignment
# → "Deny access from network" : Ajouter groupe SAUF GRP_RH_USB_Autorisés
```

**Verbalisation** :

> *"Pour bloquer USB sur service RH via GPO :*
> 1. *Créer GPO **GPO_Blocage_USB_RH** liée à OU_RH*
> 2. *Configurer : Computer Configuration → Administrative Templates → System → Removable Storage Access*
> 3. *Activer **Removable Disks: Deny all access** (bloque lecture/écriture)*
> 4. *Appliquer : `gpupdate /force` sur postes RH*
> 5. *Tester : Brancher USB → Accès refusé*
> 
> *Si exceptions nécessaires (ex : manager), utiliser filtrage sécurité GPO pour exclure utilisateur."*

**Critères évaluation** :
- ✅ Chemin GPO correct (Removable Storage Access)
- ✅ Liaison OU mentionnée
- ✅ Commande gpupdate /force connue
- ✅ Bonus exceptions (filtrage sécurité)

---

### **Question 4.3 : DNS Windows Server**

**Jury** :  
*"Comment créer un enregistrement DNS de type A pour le serveur web intranet.megastore.local pointant vers 192.168.10.30 ?"*

**Réponse attendue** (1.5 min) :

**Méthode 1 : Interface graphique (DNS Manager)**

**Étapes** :

1. Ouvrir **DNS Manager** (dnsmgmt.msc)
2. Développer **SRV-DC01** → **Forward Lookup Zones** → **megastore.local**
3. Clic droit zone **megastore.local** → **New Host (A or AAAA)...**
4. Renseigner :
   - **Name** : `intranet`
   - **IP address** : `192.168.10.30`
   - ☑ **Create associated pointer (PTR) record** (optionnel, pour résolution inverse)
5. Clic **Add Host**
6. Message confirmation : *"The host record intranet.megastore.local was successfully created"*

**Résultat** :
```
intranet.megastore.local → 192.168.10.30 (Type A)
```

**Méthode 2 : PowerShell**

```powershell
# Ajouter enregistrement A
Add-DnsServerResourceRecordA -Name "intranet" `
    -ZoneName "megastore.local" `
    -IPv4Address "192.168.10.30" `
    -TimeToLive 01:00:00  # TTL 1 heure

# Vérifier enregistrement créé
Get-DnsServerResourceRecord -ZoneName "megastore.local" -Name "intranet"
```

**Output** :
```
HostName     RecordType  Timestamp            TimeToLive    RecordData
--------     ----------  ---------            ----------    ----------
intranet     A           0                    01:00:00      192.168.10.30
```

**Méthode 3 : Fichier zone (manuel, déconseillé)**

Fichier : `C:\Windows\System32\dns\megastore.local.dns`

```dns
; Ajouter ligne
intranet    IN    A    192.168.10.30
```

**⚠️ Attention** : Nécessite redémarrage service DNS, risque erreurs syntaxe.

**Tests validation** :

```powershell
# Depuis client Windows
nslookup intranet.megastore.local
# Output :
# Name:    intranet.megastore.local
# Address:  192.168.10.30

# OU
Resolve-DnsName intranet.megastore.local
```

**Types enregistrements DNS (rappel)** :

| Type | Nom complet | Utilité | Exemple |
|------|-------------|---------|---------|
| **A** | Address | IPv4 → Nom | intranet → 192.168.10.30 |
| **AAAA** | IPv6 Address | IPv6 → Nom | intranet → 2001:db8::1 |
| **CNAME** | Canonical Name | Alias vers autre nom | www → intranet |
| **MX** | Mail Exchange | Serveur mail | mail → 192.168.10.40 |
| **PTR** | Pointer | IP → Nom (inverse) | 30.10.168.192.in-addr.arpa → intranet |
| **SRV** | Service | Service AD (_ldap, _kerberos) | _ldap._tcp.megastore.local |

**Verbalisation** :

> *"Pour créer enregistrement A :*
> 
> **Méthode GUI** :*
> 1. *DNS Manager → Forward Lookup Zones → megastore.local*
> 2. *Clic droit → New Host (A)*
> 3. *Name : intranet, IP : 192.168.10.30*
> 4. *Add Host*
> 
> **Méthode PowerShell** :*
> ```powershell
> Add-DnsServerResourceRecordA -Name "intranet" `
>     -ZoneName "megastore.local" -IPv4Address "192.168.10.30"
> ```
> 
> *Résultat : intranet.megastore.local résout vers 192.168.10.30*
> 
> *Je teste avec `nslookup intranet.megastore.local` pour valider."*

**Critères évaluation** :
- ✅ 2 méthodes citées (GUI + PowerShell)
- ✅ Syntaxe Add-DnsServerResourceRecordA correcte
- ✅ Test nslookup mentionné

---

### **Question 4.4 : Permissions NTFS**

**Jury** :  
*"Quelle est la différence entre permissions NTFS et permissions de partage ? Lesquelles sont prioritaires ?"*

**Réponse attendue** (1.5 min) :

**Tableau comparatif** :

| Critère | Permissions NTFS | Permissions Partage (SMB) |
|---------|------------------|--------------------------|
| **Emplacement** | Fichiers/dossiers locaux (E:\, C:\) | Partages réseau (\\serveur\partage) |
| **Accès** | Local ET réseau | Réseau uniquement |
| **Granularité** | Très fine (Lecture, Écriture, Modification, Contrôle total, Exécution) | Simple (Lecture, Modification, Contrôle total) |
| **Héritage** | ✅ Héritage dossiers parents | ❌ Pas d'héritage |
| **Persistance** | ✅ Même après déplacement fichier (NTFS vers NTFS) | ❌ Perdu si copie locale |

**Règle de priorité** :

> **Les permissions les plus restrictives s'appliquent.**

**Scénarios** :

| NTFS | Partage | Accès réseau effectif | Explication |
|------|---------|----------------------|-------------|
| **Contrôle total** | **Lecture** | **Lecture** ✅ | Partage plus restrictif → Lecture |
| **Lecture** | **Contrôle total** | **Lecture** ✅ | NTFS plus restrictif → Lecture |
| **Modification** | **Modification** | **Modification** ✅ | Égales → Modification |
| **Refuser Lecture** | **Contrôle total** | **Aucun accès** ❌ | Refuser prioritaire |

**Exemple pratique** :

```
Partage réseau : \\SRV-FILE01\RH_Paies
Dossier local : E:\Partages\RH_Paies

Utilisateur : Marie Lambert (membre GRP_RH)

Permissions Partage SMB :
- GRP_RH : Contrôle total

Permissions NTFS :
- GRP_RH : Lecture seule

→ Accès réseau : Lecture seule (NTFS plus restrictif)
→ Accès local (depuis SRV-FILE01) : Lecture seule (NTFS uniquement)
```

**Commandes vérification** :

```powershell
# Vérifier permissions partage
Get-SmbShareAccess -Name "RH_Paies"

# Vérifier permissions NTFS
Get-Acl "E:\Partages\RH_Paies" | Format-List
```

**Bonnes pratiques** :

✅ **Permissions partage** : Laisser **"Tout le monde : Contrôle total"**  
✅ **Permissions NTFS** : Gérer finement (groupes AD, droits précis)  
→ Simplification gestion (tout contrôlé par NTFS)

**Exceptions** : Environnements hétérogènes (Linux NFS + Windows SMB)

**Verbalisation** :

> *"Différences :*
> - **NTFS** : Appliqué localement ET réseau. Très granulaire (Lecture, Écriture, Modification, etc.).*
> - **Partage SMB** : Uniquement accès réseau. Moins granulaire (Lecture, Modification, Contrôle total).*
> 
> **Priorité : Les plus restrictives gagnent.**
> 
> *Exemple : Si NTFS = Lecture et Partage = Contrôle total → Accès réseau limité à Lecture (NTFS plus restrictif).*
> 
> *Bonne pratique : Partage = "Tout le monde Contrôle total", gérer sécurité via NTFS uniquement."*

**Critères évaluation** :
- ✅ Différence local/réseau expliquée
- ✅ Règle "plus restrictives gagnent"
- ✅ Exemple scénario pertinent
- ✅ Bonne pratique citée (NTFS seul)

---

### **Question 4.5 : PowerShell Active Directory**

**Jury** :  
*"Écrivez une commande PowerShell pour lister tous les utilisateurs désactivés dans l'OU RH."*

**Réponse attendue** (1.5 min) :

**Commande de base** :

```powershell
Get-ADUser -Filter {Enabled -eq $false} -SearchBase "OU=RH,OU=Paris,DC=megastore,DC=local"
```

**Commande améliorée avec propriétés** :

```powershell
Get-ADUser -Filter {Enabled -eq $false} `
    -SearchBase "OU=RH,OU=Paris,DC=megastore,DC=local" `
    -Properties DisplayName, LastLogonDate, Description |
    Select-Object Name, SamAccountName, Enabled, LastLogonDate, Description |
    Sort-Object Name
```

**Décomposition** :

| Paramètre | Description | Valeur exemple |
|-----------|-------------|----------------|
| **-Filter** | Critère filtrage (syntaxe script block) | `{Enabled -eq $false}` |
| **-SearchBase** | OU de recherche (DN complet) | `"OU=RH,OU=Paris,DC=megastore,DC=local"` |
| **-Properties** | Attributs supplémentaires (pas retournés par défaut) | `LastLogonDate, Description` |
| **Select-Object** | Colonnes à afficher | `Name, SamAccountName, Enabled` |
| **Sort-Object** | Tri résultats | `Name` (alphabétique) |

**Output exemple** :

```
Name           SamAccountName  Enabled  LastLogonDate        Description
----           --------------  -------  -------------        -----------
Dupont Jean    jdupont         False    12/10/2025 14:32:12  Congé parental
Lambert Marie  mlambert        False    05/09/2025 09:15:43  Démission
```

**Variantes utiles** :

**1. Exporter vers CSV** :

```powershell
Get-ADUser -Filter {Enabled -eq $false} `
    -SearchBase "OU=RH,OU=Paris,DC=megastore,DC=local" `
    -Properties * |
    Export-Csv -Path "C:\Temp\Utilisateurs_Desactives_RH.csv" -NoTypeInformation -Encoding UTF8
```

**2. Compter nombre utilisateurs désactivés** :

```powershell
(Get-ADUser -Filter {Enabled -eq $false} -SearchBase "OU=RH,OU=Paris,DC=megastore,DC=local").Count
# Output : 2
```

**3. Désactiver utilisateur** (bonus) :

```powershell
# Désactiver compte
Disable-ADAccount -Identity "jdupont"

# Vérifier
Get-ADUser -Identity "jdupont" | Select-Object Name, Enabled
# Output : Name = Jean Dupont, Enabled = False
```

**4. Activer utilisateur** (bonus) :

```powershell
Enable-ADAccount -Identity "jdupont"
```

**Syntaxe -Filter importantes** :

| Filtre | Signification |
|--------|--------------|
| `{Enabled -eq $false}` | Comptes désactivés |
| `{Enabled -eq $true}` | Comptes actifs |
| `{Name -like "*Dupont*"}` | Nom contient "Dupont" |
| `{LastLogonDate -lt (Get-Date).AddDays(-90)}` | Pas de connexion depuis 90 jours |
| `{Department -eq "RH"}` | Département = RH |

**Verbalisation** :

> *"Commande PowerShell pour lister utilisateurs désactivés dans OU RH :*
> ```powershell
> Get-ADUser -Filter {Enabled -eq $false} `
>     -SearchBase "OU=RH,OU=Paris,DC=megastore,DC=local" `
>     -Properties LastLogonDate | Select-Object Name, SamAccountName, Enabled
> ```
> 
> *Explications :*
> - *`-Filter {Enabled -eq $false}` : Filtrer comptes désactivés*
> - *`-SearchBase` : Limiter recherche à OU_RH*
> - *`-Properties LastLogonDate` : Ajouter date dernière connexion*
> 
> *Je peux exporter résultats CSV avec `Export-Csv` pour rapport."*

**Critères évaluation** :
- ✅ Syntaxe Get-ADUser -Filter correcte (accolades {})
- ✅ SearchBase avec DN complet
- ✅ Bonus : Properties, Select-Object, Export-Csv

---

---

## 📊 RÉCAPITULATIF PARTIE 2C

### **Questions couvertes (5 questions CCP4)**

| Question | Sujet | Durée |
|----------|-------|-------|
| 4.1 | Active Directory (hiérarchie Forêt/Domaine/OU) | 1.5 min |
| 4.2 | GPO (blocage USB par OU) | 2 min |
| 4.3 | DNS Windows (enregistrement A) | 1.5 min |
| 4.4 | Permissions NTFS vs Partage | 1.5 min |
| 4.5 | PowerShell AD (Get-ADUser -Filter) | 1.5 min |
| **TOTAL PARTIE 2C** | **CCP4 - Windows/AD** | **7 min** |

### **Compétences évaluées**

✅ **Active Directory** : Structure OU, bonnes pratiques organisation  
✅ **GPO** : Restriction périphériques USB, liaison OU, gpupdate  
✅ **DNS Windows** : Enregistrements A, Add-DnsServerResourceRecordA, nslookup  
✅ **Permissions** : NTFS vs SMB, règle restrictive, Get-Acl/Get-SmbShareAccess  
✅ **PowerShell** : Get-ADUser, filtres script block, SearchBase, Export-Csv  

---

# 🎤 SIMULATION ORALE TSSR - PARTIE 2D : ENTRETIEN TECHNIQUE (CCP5 - VIRTUALISATION)

**Durée cette partie** : 6 minutes  
**Questions** : 4 questions sur VMware vSphere et virtualisation

---

## ☁️ CCP5 : VIRTUALISATION ET INFRASTRUCTURES CLOUD (6 minutes)

### **Question 5.1 : Snapshots VMware**

**Jury** :  
*"Qu'est-ce qu'un snapshot VMware ? Quelles sont les bonnes pratiques et limitations ?"*

**Réponse attendue** (1.5 min) :

**Définition** :

> *"Un snapshot est une photo instantanée de l'état complet d'une VM à un instant T : disques, RAM, configuration. Il permet de revenir à cet état en cas de problème."*

**Fonctionnement technique** :

```
VM au repos (disque VMDK)
    ↓
Création snapshot
    ↓
┌──────────────────────────────┐
│  Disque original (VMDK)      │ ← Read-only (figé)
│  [État avant snapshot]       │
└──────────────────────────────┘
           ↓
┌──────────────────────────────┐
│  Delta disk (-000001.vmdk)   │ ← Read/Write (modifications)
│ [Changements depuis snapshot]│
└──────────────────────────────┘
```

**Cas d'usage** :

✅ **Avant mise à jour** : Snapshot → Update logiciel → Si KO, revert snapshot  
✅ **Tests configuration** : Snapshot → Test firewall → Si KO, revert  
✅ **Formation/démo** : Snapshot état propre, revert après chaque session  

**Bonnes pratiques** :

| Pratique | Recommandation | Raison |
|----------|----------------|--------|
| **Durée max** | ⏱️ 24-72h max | Croissance delta disk (performances ⬇️) |
| **Nombre max** | 🔢 2-3 snapshots max par VM | Chaîne longue = lenteur |
| **Taille VM** | 📦 Éviter sur VM > 500 Go | Consolidation très longue |
| **Production** | ⚠️ Pas de snapshot long terme | Utiliser backups (Veeam) |
| **Mémoire incluse** | 💾 Décocher "Snapshot VM memory" si pas besoin | Gain espace + rapidité |

**Limitations** :

❌ **Pas un backup** : Snapshot sur même datastore (si datastore fail, snapshot perdu)  
❌ **Dégradation performances** : Lecture/écriture sur delta disk plus lent  
❌ **Risque saturation** : Delta disk grandit (peut remplir datastore)  
❌ **Corruption possible** : Si chaîne snapshot trop longue (> 32 snapshots = limite VMware)  

**Commandes essentielles** :

```bash
# Lister snapshots VM
vim-cmd vmsvc/snapshot.get <VMID>

# Supprimer tous snapshots (consolidation)
vim-cmd vmsvc/snapshot.removeall <VMID>
```

**Verbalisation** :

> *"Un snapshot capture l'état VM à un instant T. Les modifications après snapshot sont écrites dans un delta disk (-000001.vmdk).*
> 
> *Bonnes pratiques :*
> - *Durée max 72h (performances dégradées si trop long)*
> - *Max 2-3 snapshots par VM*
> - *Pas en production (utiliser Veeam pour backups)*
> 
> *Limitations : Pas un backup (même datastore), performances ralenties, risque saturation."*

**Critères évaluation** :
- ✅ Définition correcte (état instantané)
- ✅ Mention delta disk
- ✅ Au moins 2 bonnes pratiques citées
- ✅ Différence snapshot/backup

---

### **Question 5.2 : vMotion et HA**

**Jury** :  
*"Expliquez vMotion et vSphere HA. Quelles sont leurs utilités ?"*

**Réponse attendue** (2 min) :

**vMotion - Migration à chaud**

**Définition** :
> *"vMotion permet de déplacer une VM en fonctionnement d'un hôte ESXi vers un autre, sans interruption service (downtime = 0)."*

**Prérequis** :
- vCenter Server (orchestration)
- Réseau vMotion dédié (Gigabit min, 10 Gb recommandé)
- Stockage partagé (SAN, NAS, vSAN)
- Processeurs compatibles (même famille Intel/AMD)

**Cas d'usage** :

| Scénario | Action | Bénéfice |
|----------|--------|----------|
| **Maintenance hôte** | vMotion VMs → autre hôte → Redémarrer hôte | Pas d'arrêt VMs |
| **Équilibrage charge** | DRS vMotion auto VMs surchargées | Performances optimales |
| **Panne matériel** | vMotion avant crash (alerte RAID) | Proactif (éviter downtime) |

**Variantes** :

- **Storage vMotion** : Déplacer disques VM vers autre datastore (VM reste sur même hôte)
- **Cross vCenter vMotion** : Migrer entre 2 vCenters (infrastructures différentes)

---

**vSphere HA (High Availability) - Haute disponibilité**

**Définition** :
> *"vSphere HA redémarre automatiquement les VMs sur autre hôte ESXi si un hôte tombe en panne."*

**Fonctionnement** :

```
Cluster HA (3 hôtes ESXi)
  ├── ESXi-01 (Master) ← Monitoring heartbeats
  ├── ESXi-02 (Slave)  ← 10 VMs
  └── ESXi-03 (Slave)

PANNE ESXi-02
     ↓
HA détecte absence heartbeat (15 sec)
     ↓
Redémarrage automatique 10 VMs sur ESXi-01 et ESXi-03
     ↓
Downtime = Temps boot VMs (2-5 min)
```

**Configuration HA** :

| Paramètre | Valeur | Description |
|-----------|--------|-------------|
| **Admission Control** | ✅ Enabled | Réserve ressources pour failover |
| **Host Failures** | 1-2 hôtes | Nombre pannes tolérées |
| **VM Restart Priority** | High/Medium/Low | Ordre redémarrage VMs |
| **Isolation Response** | Power Off / Leave Powered On | Action si hôte isolé réseau |

**Prérequis** :
- Cluster ESXi (min 2 hôtes)
- Stockage partagé (VMs accessibles depuis tous hôtes)
- Réseau management fiable (heartbeats)

**Cas d'usage** :

✅ **Panne matérielle hôte** : PSU, carte mère → HA redémarre VMs  
✅ **Crash ESXi** : Purple Screen of Death → HA prend relais  
✅ **Coupure électrique** : 1 hôte sans UPS → HA redistribue VMs  

**Différence HA vs FT (Fault Tolerance)** :

| Critère | HA | FT |
|---------|----|----|
| **Downtime** | 2-5 min (reboot) | 0 sec (instantané) |
| **Utilisation** | Production standard | Applications critiques (BDD transactionnelles) |
| **Overhead** | Faible | Élevé (VM miroir temps réel) |
| **Limite** | N/A | Max 4 vCPUs par VM |

**Verbalisation** :

> *"**vMotion** : Déplace VM en live d'un hôte vers un autre (downtime 0). Utilité : maintenance hôtes sans arrêter VMs, équilibrage charge.*
> 
> *Prérequis : vCenter, stockage partagé (SAN), réseau vMotion.*
> 
> ***vSphere HA** : Redémarre automatiquement VMs sur autre hôte si panne. Downtime = temps boot (2-5 min).*
> 
> *Prérequis : Cluster ESXi (min 2 hôtes), stockage partagé.*
> 
> *Exemple : Serveur ESXi crash → HA détecte absence heartbeat → Redémarre VMs sur hôtes restants."*

**Critères évaluation** :
- ✅ vMotion = migration à chaud (0 downtime)
- ✅ HA = redémarrage auto après panne (2-5 min downtime)
- ✅ Prérequis commun : stockage partagé
- ✅ Cas d'usage pertinents

---

### **Question 5.3 : Types de disques virtuels**

**Jury** :  
*"Quels sont les 3 types de disques virtuels VMware et lequel recommandez-vous pour une base de données en production ?"*

**Réponse attendue** (1.5 min) :

**Tableau comparatif** :

| Type disque | Allocation | Zeroing | Performances | Usage recommandé |
|-------------|-----------|---------|--------------|------------------|
| **Thick Lazy Zeroed** | ✅ Immédiate (espace réservé) | ⏱️ À la demande | ⭐⭐ Moyennes | Dev/Test |
| **Thick Eager Zeroed** | ✅ Immédiate + Zeroing complet | ✅ Création (long) | ⭐⭐⭐ Excellentes | **Production BDD** |
| **Thin Provisioning** | ❌ À la demande | ⏱️ À la demande | ⭐ Faibles (write penalty) | Économie espace, templates |

**Détails techniques** :

**1. Thick Lazy Zeroed** :
- Espace réservé immédiatement (ex : disque 100 Go → 100 Go datastore)
- Blocs écrits à zéro lors 1ère écriture (lazy = paresseux)
- **Avantage** : Création rapide
- **Inconvénient** : 1ère écriture légèrement plus lente (zeroing)

**2. Thick Eager Zeroed** :
- Espace réservé + blocs écrits à zéro immédiatement
- Création longue (zeroing complet 100 Go peut prendre 10-30 min)
- **Avantage** : Performances optimales, compatible Fault Tolerance
- **Inconvénient** : Temps création

**3. Thin Provisioning** :
- Espace alloué à la demande (disque 100 Go peut n'occuper que 20 Go)
- **Avantage** : Économie espace (surallocation possible : 10 VMs × 100 Go = 1 To sur datastore 500 Go)
- **Inconvénient** : Fragmentation, risque saturation datastore, performances moindres

**Recommandation BDD production** :

✅ **Thick Eager Zeroed**

**Raisons** :
1. **Performances constantes** : Pas de write penalty, zeroing déjà fait
2. **Fault Tolerance** : Seul type compatible FT (si BDD critique)
3. **Prévisibilité espace** : Pas de risque saturation surprise
4. **Moins de fragmentation** : Blocs contigus

**Conversion types disques** :

```powershell
# Thin → Thick Eager Zeroed (Storage vMotion)
$vm = Get-VM "SRV-BDD01"
$harddisk = Get-HardDisk -VM $vm | Where {$_.Name -eq "Hard disk 1"}
Set-HardDisk -HardDisk $harddisk -Datastore "DS-PROD-SSD" -StorageFormat EagerZeroedThick -Confirm:$false
```

**Autre facteur : Type datastore** :

| Datastore | Disque recommandé | Raison |
|-----------|------------------|--------|
| **SSD/NVMe** | Eager Zeroed | Exploiter performances |
| **HDD SATA** | Lazy Zeroed | Économie temps création |
| **vSAN** | Thin (géré par vSAN) | Optimisation automatique |

**Verbalisation** :

> *"3 types disques VMware :*
> 
> 1. **Thick Lazy Zeroed** : Espace réservé, zeroing à la demande. Usage : Dev/Test*
> 2. **Thick Eager Zeroed** : Espace réservé + zeroing immédiat. Usage : Production (BDD)*
> 3. **Thin Provisioning** : Allocation demande. Usage : Économie espace, templates*
> 
> *Pour BDD production, je recommande **Eager Zeroed** :*
> - *Performances constantes (pas de write penalty)*
> - *Compatible Fault Tolerance*
> - *Prévisibilité espace (pas de saturation surprise)*
> 
> *Inconvénient : Création plus longue (zeroing complet), mais compensé par performances."*

**Critères évaluation** :
- ✅ 3 types nommés correctement
- ✅ Eager Zeroed recommandé pour BDD
- ✅ Justification performances/FT
- ✅ Mention write penalty Thin

---

### **Question 5.4 : Datastore et VMFS**

**Jury** :  
*"Qu'est-ce qu'un datastore VMware ? Quelle est la différence entre VMFS et NFS ?"*

**Réponse attendue** (1.5 min) :

**Définition datastore** :

> *"Un datastore est un conteneur de stockage logique pour fichiers VMs (VMDK, VMX, logs). Il abstrait le stockage physique (SAN, NAS, disques locaux)."*

**Tableau comparatif VMFS vs NFS** :

| Critère | VMFS (Virtual Machine File System) | NFS (Network File System) |
|---------|-----------------------------------|--------------------------|
| **Type** | Système fichiers bloc (SAN, iSCSI, FC) | Système fichiers réseau (NAS) |
| **Protocole** | iSCSI, Fibre Channel, FCoE | NFS v3 ou v4.1 |
| **Format disque** | Thick/Thin VMDK | Thin VMDK uniquement |
| **Performances** | ⭐⭐⭐ Excellentes (accès bloc direct) | ⭐⭐ Bonnes (overhead réseau) |
| **Clustering** | ✅ Oui (SCSI locking) | ⚠️ Limité (NFS locking) |
| **Snapshots** | ✅ VMFS snapshots | ✅ NAS snapshots (côté stockage) |
| **Taille max** | 64 To (VMFS6) | Illimité (dépend NAS) |
| **Usage** | Production haute perf (BDD, Exchange) | Fichiers, développement, ISO |

**VMFS versions** :

| Version | Taille bloc | Nombre hôtes max | Taille max datastore | Taille max fichier |
|---------|------------|------------------|---------------------|-------------------|
| **VMFS5** | 1 Mo | 64 | 64 To | 62 To |
| **VMFS6** | 1 Mo (unifié) | 64 | 64 To | 62 To |

**VMFS6 nouveautés** :
- Automatic space reclamation (UNMAP automatique)
- Support 4K native sectors
- Meilleure gestion snapshots

**Création datastore** :

**VMFS (iSCSI)** :
1. ESXi → Storage → Adapters → iSCSI → Add Target
2. Storage → Datastores → New Datastore → VMFS
3. Sélectionner LUN iSCSI → Format VMFS6 → Nom datastore

**NFS** :
1. ESXi → Storage → Datastores → New Datastore → NFS
2. Renseigner :
   - **NFS Server** : 192.168.10.50
   - **NFS Share** : /mnt/nfs_datastore
   - **NFS version** : 3 ou 4.1
   - **Datastore name** : DS-NFS-01

**Commandes esxcli** :

```bash
# Lister datastores
esxcli storage filesystem list

# Informations VMFS
vmkfstools -P /vmfs/volumes/DS-PROD-01
```

**Verbalisation** :

> *"Un datastore stocke fichiers VMs (disques, configuration). C'est l'abstraction du stockage physique.*
> 
> **VMFS** :*
> - *Système fichiers bloc (SAN iSCSI/FC)*
> - *Excellentes performances*
> - *Usage : Production (BDD, Exchange)*
> - *Taille max 64 To, 64 hôtes max*
> 
> **NFS** :*
> - *Stockage réseau (NAS)*
> - *Bonnes performances mais overhead réseau*
> - *Usage : Fichiers, ISO, développement*
> - *Thin provisioning uniquement*
> 
> *Choix selon usage : VMFS pour production critique, NFS pour flexibilité/économie."*

**Critères évaluation** :
- ✅ Définition datastore correcte
- ✅ VMFS = bloc (SAN), NFS = réseau (NAS)
- ✅ Différence performances expliquée
- ✅ Cas d'usage pertinents

---

---

## 📊 RÉCAPITULATIF PARTIE 2D

### **Questions couvertes (4 questions CCP5)**

| Question | Sujet | Durée |
|----------|-------|-------|
| 5.1 | Snapshots VMware (bonnes pratiques, limitations) | 1.5 min |
| 5.2 | vMotion et vSphere HA (différences, utilités) | 2 min |
| 5.3 | Types disques virtuels (Thick/Thin, BDD) | 1.5 min |
| 5.4 | Datastore (VMFS vs NFS) | 1.5 min |
| **TOTAL PARTIE 2D** | **CCP5 - Virtualisation** | **6 min** |

### **Compétences évaluées**

✅ **Snapshots** : Delta disk, bonnes pratiques (72h max), différence vs backup  
✅ **vMotion** : Migration à chaud, Storage vMotion, prérequis stockage partagé  
✅ **vSphere HA** : Redémarrage auto, heartbeats, admission control  
✅ **Disques virtuels** : Thick Lazy/Eager, Thin provisioning, choix BDD production  
✅ **Datastores** : VMFS6 (bloc/SAN) vs NFS (réseau/NAS), performances  


---


---

# 🎤 SIMULATION ORALE TSSR - PARTIE 2E : ENTRETIEN TECHNIQUE (CCP6 - SÉCURITÉ)

**Durée cette partie** : 5 minutes  
**Questions** : 3 questions sur sécurité, sauvegardes et PRA/PCA

---

## 🔒 CCP6 : SÉCURISER INFRASTRUCTURES ET SAUVEGARDES (5 minutes)

### **Question 6.1 : Stratégies de sauvegarde**

**Jury** :  
*"Expliquez la stratégie de sauvegarde GFS (Grandfather-Father-Son) et calculez le nombre de supports nécessaires."*

**Réponse attendue** (2 min) :

**Principe GFS** :

```
GRANDFATHER (Mensuel)
    ├── FATHER (Hebdomadaire)
    │    └── SON (Quotidien)
```

**Schéma rotation** :

| Niveau | Fréquence | Rétention | Nombre supports |
|--------|-----------|-----------|----------------|
| **Son** (Fils) | Quotidien (lun-ven) | 1 semaine | **5 supports** |
| **Father** (Père) | Hebdomadaire (vendredi) | 1 mois | **4 supports** |
| **Grandfather** (Grand-père) | Mensuel (dernier vendredi) | 1 an | **12 supports** |

**Total** : **5 + 4 + 12 = 21 supports minimum**

**Exemple calendrier Novembre 2025** :

```
Lun 3  → SON-1  (quotidien)
Mar 4  → SON-2  (quotidien)
Mer 5  → SON-3  (quotidien)
Jeu 6  → SON-4  (quotidien)
Ven 7  → SON-5 + FATHER-1  (quotidien + hebdo)

Lun 10 → SON-1  (écrase sauvegarde 3/11)
...
Ven 28 → SON-5 + FATHER-4 + GRANDFATHER-11  (dernier vendredi du mois)
```

**Avantages GFS** :

✅ **Granularité récupération** :
- Fichier supprimé hier → SON (quotidien)
- Erreur il y a 2 semaines → FATHER (hebdomadaire)
- Besoin données janvier → GRANDFATHER (mensuel)

✅ **Économie supports** : Rotation automatique (21 supports vs 365 quotidiens)

✅ **Conformité légale** : Archivage annuel (obligations comptables, RGPD)

**Variantes GFS** :

**1. GFS+** (avec niveau Yearly) :

| Niveau | Fréquence | Rétention | Supports |
|--------|-----------|-----------|----------|
| Son | Quotidien | 1 semaine | 5 |
| Father | Hebdomadaire | 1 mois | 4 |
| Grandfather | Mensuel | 1 an | 12 |
| **Yearly** | Annuel | 7-10 ans | 7-10 |
| **TOTAL** | - | - | **28-31** |

**2. Tower of Hanoi** (alternative) :
- Plus complexe, rotation non linéaire
- Meilleure protection contre corruption

**Règle 3-2-1-1-0** (moderne) :

```
3 copies données
2 supports différents (disque + bande)
1 copie off-site (site distant)
1 copie offline/immutable (protection ransomware)
0 erreur vérification (intégrité testée)
```

**Outils automatisation** :
- Veeam Backup & Replication (GFS intégré)
- Windows Server Backup (planification)
- Bacula/Amanda (open-source)

**Verbalisation** :

> *"GFS = 3 niveaux rotation :*
> - **Son (quotidien)** : 5 sauvegardes lun-ven (réutilisées chaque semaine)*
> - **Father (hebdomadaire)** : 4 sauvegardes (1 par semaine, réutilisées chaque mois)*
> - **Grandfather (mensuel)** : 12 sauvegardes (1 par mois, conservées 1 an)*
> 
> *Calcul : 5 + 4 + 12 = **21 supports minimum***
> 
> *Avantages :*
> - *Granularité : restaurer données récentes (Son) ou anciennes (Grandfather)*
> - *Économie supports : rotation vs 365 sauvegardes quotidiennes*
> - *Conformité légale : archivage annuel*
> 
> *Exemple : Fichier supprimé hier → restaurer Son. Données janvier → restaurer Grandfather."*

**Critères évaluation** :
- ✅ 3 niveaux expliqués (Son/Father/Grandfather)
- ✅ Calcul 21 supports correct
- ✅ Avantages cités (granularité, économie)
- ✅ Bonus : Règle 3-2-1-1-0

---

### **Question 6.2 : RTO et RPO**

**Jury** :  
*"Définissez RTO et RPO. Donnez un exemple concret pour un serveur web e-commerce."*

**Réponse attendue** (1.5 min) :

**Définitions** :

**RTO (Recovery Time Objective)** :
> *"Durée maximale d'interruption acceptable. Temps pour restaurer service après incident."*

**RPO (Recovery Point Objective)** :
> *"Perte de données maximale acceptable. Âge dernière sauvegarde restaurable."*

**Schéma temporel** :

```
Incident                    Service restauré
   ↓                              ↓
   |<--------- RTO ------------>|
   |  (temps indisponibilité)   |
   
Dernière sauvegarde          Incident
   ↓                           ↓
   |<---- RPO ----->|
   | (perte données)|
```

**Tableau comparatif** :

| Critère | RTO | RPO |
|---------|-----|-----|
| **Question** | "Combien de temps offline ?" | "Combien de données perdues ?" |
| **Unité** | Minutes/Heures | Minutes/Heures |
| **Impact** | Disponibilité service | Intégrité données |
| **Mesure** | Temps restauration | Fréquence sauvegardes |

**Exemple serveur web e-commerce** :

**Contexte** :
- Site e-commerce MegaStore.com
- 1000 commandes/jour (moyenne 100 €)
- Chiffre affaires : 100 000 €/jour

**Scénario incident** :
- Serveur web crash à 14h00
- Dernière sauvegarde : 13h00 (sauvegardes horaires)

**Exigences métier** :

| Métrique | Valeur | Justification |
|----------|--------|---------------|
| **RTO** | **15 minutes** | Chaque minute offline = 70 € perdus (100k€/jour ÷ 1440 min) |
| **RPO** | **1 heure** | Commandes 13h00-14h00 perdues (acceptable si < 100 commandes) |

**Solution technique pour respecter RTO/RPO** :

**Pour RTO 15 min** :

| Solution | RTO réel | Coût |
|----------|----------|------|
| **Cluster HA** (2 serveurs web + load balancer) | < 1 min | 💰💰 Moyen |
| **VM snapshot avant MAJ** | 5 min | 💰 Faible |
| **Backup image complète (Veeam)** | 10 min | 💰💰 Moyen |
| **PRA site distant** (bascule DNS) | 15 min | 💰💰💰 Élevé |

**Pour RPO 1h** :

| Solution | RPO réel | Coût |
|----------|----------|------|
| **Sauvegarde BDD horaire** | 1h | 💰 Faible |
| **Réplication BDD temps réel** (SQL AlwaysOn) | < 1 min | 💰💰💰 Élevé |
| **Snapshot storage SAN** (toutes les 15 min) | 15 min | 💰💰 Moyen |

**Compromis coût/exigence** :

```
RTO court + RPO court = Coût élevé
    ↓
Cluster HA + Réplication BDD = 20k€/an
    VS
RTO long + RPO long = Coût faible
    ↓
Sauvegarde quotidienne + Restore manuel = 2k€/an
```

**Calcul perte financière** :

```
RTO 15 min × 70 €/min = 1 050 € perte CA
RPO 1h × 4 commandes/h × 100 € = 400 € commandes perdues
---
Perte totale incident : 1 450 €

Si incident 1×/mois = 17 400 €/an
→ Budget PRA 20k€/an justifié
```

**Verbalisation** :

> *"**RTO** = Temps max indisponibilité. **RPO** = Perte données max acceptable.*
> 
> *Exemple e-commerce :*
> - *RTO 15 min : Site doit être restauré en max 15 min (chaque minute = 70 € perdus)*
> - *RPO 1h : On accepte perdre commandes dernière heure (sauvegardes horaires)*
> 
> *Solutions :*
> - *RTO 15 min → Cluster HA (bascule auto) ou Veeam Instant Recovery*
> - *RPO 1h → Sauvegarde BDD horaire ou Snapshot SAN*
> 
> *Plus RTO/RPO courts = Plus cher (cluster HA + réplication temps réel = 20k€/an vs backup quotidien = 2k€/an)."*

**Critères évaluation** :
- ✅ RTO = disponibilité, RPO = intégrité
- ✅ Exemple chiffré pertinent (temps/perte)
- ✅ Solutions techniques citées (HA, réplication)
- ✅ Lien coût/exigence

---

### **Question 6.3 : Sécurité - Protection ransomware**

**Jury** :  
*"Quelles mesures techniques mettriez-vous en place pour protéger une infrastructure contre les ransomwares ?"*

**Réponse attendue** (1.5 min) :

**3 piliers protection ransomware** :

```
┌─────────────────────────────────────────┐
│  PRÉVENTION (empêcher infection)        │
│  ↓                                      │
│  DÉTECTION (identifie activité suspecte)│
│  ↓                                      │
│  RÉCUPÉRATION (restaurer sans payer)    │
└─────────────────────────────────────────┘
```

---

**1. PRÉVENTION (6 mesures)**

| Mesure | Description | Outil/Technique |
|--------|-------------|-----------------|
| **A. Filtrage emails** | Bloquer pièces jointes suspectes (.exe, .js, macros) | Exchange Mail Flow Rules, Proofpoint |
| **B. Antivirus/EDR** | Détection comportementale (pas seulement signatures) | Windows Defender ATP, CrowdStrike |
| **C. Segmentation réseau** | VLANs isolés (VLAN Serveurs ≠ VLAN Utilisateurs) | ACL firewall, micro-segmentation |
| **D. Principe moindre privilège** | Utilisateurs = pas admin local | GPO "Restricted Groups" |
| **E. MAJ régulières** | Patcher Windows/logiciels (fermer failles) | WSUS, SCCM |
| **F. Formation utilisateurs** | Ne pas ouvrir pièces jointes inconnues | Phishing tests, sensibilisation |

---

**2. DÉTECTION (3 mesures)**

| Mesure | Indicateur alerte | Outil |
|--------|-------------------|-------|
| **A. Monitoring comportemental** | Chiffrement massif fichiers | Sysmon, SIEM (Splunk) |
| **B. Honeypot files** | Fichiers appâts (modif = alerte) | Canary tokens |
| **C. Alertes backup** | Suppression/chiffrement sauvegardes | Veeam alertes, logs Event Viewer |

---

**3. RÉCUPÉRATION (4 mesures - CRUCIAL)**

| Mesure | Description | Implémentation |
|--------|-------------|----------------|
| **A. Backups immutables** | Sauvegardes non modifiables (pas chiffrement) | Veeam Immutability, S3 Object Lock |
| **B. Backups offline/air-gap** | Copies hors ligne (bandes, disques déconnectés) | Rotation bandes LTO-8 off-site |
| **C. Règle 3-2-1-1-0** | 3 copies, 2 supports, 1 off-site, 1 offline, 0 erreur | Architecture backup |
| **D. Tests restauration** | Valider backups fonctionnent (DR drill) | Mensuel : restaurer VM test |

---

**Exemple architecture anti-ransomware** :

```
┌──────────────────────────────────────────┐
│  INTERNET                                │
└────────┬─────────────────────────────────┘
         ↓
    [FIREWALL + IPS]  ← Filtrage trafic malveillant
         ↓
    [EMAIL GATEWAY]  ← Proofpoint (anti-phishing)
         ↓
┌────────────────────────────────────┐
│  VLAN 10 : Utilisateurs            │
│  - Antivirus Defender ATP          │  Segmentation
│  - Pas droits admin                │  ↕
│  - Accès serveurs limité (ACL)     │
└────────────────────────────────────┘
         ↓ (ACL restrictive)
┌────────────────────────────────────┐
│  VLAN 20 : Serveurs                │
│  - Windows Server 2022             │
│  - Sauvegardes Veeam quotidiennes  │
│  - Réplication off-site            │
└────────────────────────────────────┘
         ↓
┌────────────────────────────────────┐
│  STOCKAGE BACKUP                   │
│  - Veeam Immutability (14 jours)   │  Air-gap
│  - Bandes LTO-8 off-site (mensuel) │  ←─────
│  - Tests restauration mensuels     │
└────────────────────────────────────┘
```

**Scénario incident ransomware** :

```
Jour J :
- Utilisateur ouvre email phishing → Infection

J+1 heure :
- Ransomware chiffre C:\Users, tente accès serveurs
- ❌ Bloqué par ACL firewall (pas accès VLAN Serveurs)
- ✅ Alerte SIEM : chiffrement massif détecté

J+2 heures :
- IT isole PC infecté (désactiver port switch)
- Wipe PC, réinstallation depuis image propre

J+3 heures :
- Restauration données utilisateur depuis backup Veeam
- RPO : 12h (sauvegarde quotidienne 3h00)
- RTO : 3h (temps diagnostic + restauration)

Résultat : Pas de paiement rançon, données récupérées
```

**Coûts estimés** (PME 50 utilisateurs) :

| Solution | Coût annuel |
|----------|-------------|
| Email Gateway (Proofpoint) | 3 000 € |
| Antivirus EDR (Defender ATP) | 2 500 € |
| Veeam Backup | 5 000 € |
| Bandes LTO + lecteur | 2 000 € |
| Formation utilisateurs | 1 500 € |
| **TOTAL** | **14 000 €/an** |

**VS Coût ransomware** :
- Rançon moyenne : 50 000 € (sans garantie récupération)
- Perte CA (downtime 1 semaine) : 100 000 €
- Perte image/clients : Inestimable

→ **ROI protection : 10:1**

**Verbalisation** :

> *"3 axes protection ransomware :*
> 
> **1. Prévention** :*
> - *Filtrage emails (bloquer .exe, macros)*
> - *Antivirus comportemental (Defender ATP)*
> - *Segmentation VLAN (isoler serveurs)*
> - *Pas droits admin utilisateurs*
> 
> **2. Détection** :*
> - *SIEM alertes chiffrement massif*
> - *Honeypot files (fichiers appâts)*
> 
> **3. Récupération (CRUCIAL)** :*
> - *Backups immutables (Veeam Immutability = pas modifiable/chiffrable)*
> - *Backups offline (bandes déconnectées)*
> - *Règle 3-2-1-1-0 (3 copies, 1 offline)*
> - *Tests restauration mensuels (DR drill)*
> 
> *Essentiel : Backups offline/immutables → Ne jamais payer rançon si backups OK."*

**Critères évaluation** :
- ✅ Au moins 2 mesures prévention citées
- ✅ Backups immutables/offline mentionnés (critique)
- ✅ Règle 3-2-1-1-0 connue
- ✅ Segmentation réseau (VLAN)
- ✅ Bonus : Coût vs rançon, tests restauration

---

---

## 📊 RÉCAPITULATIF PARTIE 2E

### **Questions couvertes (3 questions CCP6)**

| Question | Sujet | Durée |
|----------|-------|-------|
| 6.1 | Stratégie GFS (calcul 21 supports, niveaux) | 2 min |
| 6.2 | RTO/RPO (définitions, exemple e-commerce) | 1.5 min |
| 6.3 | Protection ransomware (3 axes, backups immutables) | 1.5 min |
| **TOTAL PARTIE 2E** | **CCP6 - Sécurité** | **5 min** |

### **Compétences évaluées**

✅ **GFS** : Rotation Son/Father/Grandfather, calcul 21 supports, règle 3-2-1-1-0  
✅ **RTO/RPO** : Définitions, calcul perte financière, solutions HA/réplication  
✅ **Anti-ransomware** : Prévention (segmentation, filtrage), Backups immutables/offline, Tests DR  

---

## 📋 RÉCAPITULATIF GLOBAL ENTRETIEN TECHNIQUE (PARTIES 2A à 2E)

### **Temps total : 45 minutes**

| Partie | CCPs couverts | Questions | Durée |
|--------|--------------|-----------|-------|
| **2A** | Introduction + CCP1 (Support) + CCP2 (Linux) | 14 Q | 20 min |
| **2B** | CCP3 (Réseau) | 5 Q | 7 min |
| **2C** | CCP4 (Windows/AD) | 5 Q | 7 min |
| **2D** | CCP5 (Virtualisation) | 4 Q | 6 min |
| **2E** | CCP6 (Sécurité) | 3 Q | 5 min |
| **TOTAL** | **6 CCPs** | **31 questions** | **45 min** |

### **Répartition par CCP**

| CCP | Compétence | Questions | % Temps |
|-----|------------|-----------|---------|
| **CCP1** | Support utilisateur | 5 Q | 15% |
| **CCP2** | Maintenance systèmes (Linux) | 6 Q | 18% |
| **CCP3** | Services réseau | 5 Q | 15% |
| **CCP4** | Windows/Active Directory | 5 Q | 15% |
| **CCP5** | Virtualisation | 4 Q | 13% |
| **CCP6** | Sécurité/Sauvegardes | 3 Q | 11% |
| **Intro** | Parcours/Dossier pro | 3 Q | 11% |

---


---

# 🎤 SIMULATION ORALE TSSR - PARTIE 2F : QUESTIONS PIÈGES & CONSEILS

**Contenu** : Questions difficiles/pièges + Stratégies réponses + Grille évaluation Entretien Technique

---

## ⚠️ TOP 10 QUESTIONS PIÈGES ET COMMENT Y RÉPONDRE

### **Piège 1 : "Vous ne savez pas ?"**

**Question jury** :  
*"Quelle est la différence entre un conteneur Docker et une VM ?"*

**Si vous ne connaissez PAS Docker** :

❌ **Mauvaise réponse** : *"Euh... je ne sais pas."* (silence gêné)

✅ **Bonne réponse** :

> *"Je n'ai pas encore eu l'occasion de travailler avec Docker dans ma formation, mais d'après ma compréhension théorique :*
> - *Une **VM** virtualise hardware complet (OS invité complet)*
> - *Un **conteneur** partage kernel OS hôte (plus léger, démarrage rapide)*
> 
> *Je sais que Docker est utilisé pour déploiement applications microservices. C'est une technologie que je souhaite apprendre car très demandée en DevOps.*
> 
> *Puis-je établir un parallèle avec VMware que je maîtrise ? (rebondir sur terrain connu)"*

**Principe** :
- Reconnaître lacune honnêtement
- Montrer connaissance théorique (veille techno)
- Exprimer envie d'apprendre
- Rediriger vers compétence maîtrisée

---

### **Piège 2 : Question volontairement vague**

**Question jury** :  
*"Parlez-moi de la sécurité réseau."* (trop large, 1000 réponses possibles)

❌ **Mauvaise réponse** : Réponse brouillon qui part dans tous les sens

✅ **Bonne réponse** : **Structurer et demander précision**

> *"La sécurité réseau est un vaste sujet. Pour cibler ma réponse, souhaitez-vous que je parle :*
> - *De la **segmentation** (VLANs, firewalls) ?*
> - *Des **protocoles sécurisés** (SSH vs Telnet, HTTPS) ?*
> - *Ou de la **protection périmètre** (DMZ, IDS/IPS) ?*
> 
> *Je peux détailler l'aspect qui vous intéresse."*

**Principe** : Reformuler question trop large en sous-questions précises

---

### **Piège 3 : Contradiction dans votre dossier pro**

**Question jury** :  
*"Dans votre dossier, vous dites avoir configuré DHCP sur Windows Server, mais ensuite vous parlez de dnsmasq sur Linux. Lequel avez-vous réellement utilisé ?"*

❌ **Mauvaise réponse** : S'embrouiller, changer version des faits

✅ **Bonne réponse** : **Clarifier chronologie**

> *"J'ai effectivement travaillé sur les deux, mais dans des projets différents :*
> - *Projet 1 (stage) : DHCP Windows Server 2022 pour infrastructure entreprise (200 postes)*
> - *Projet 2 (formation) : dnsmasq sur Debian pour lab personnel (10 VMs)*
> 
> *Je maîtrise mieux DHCP Windows (3 mois en prod), dnsmasq était un exercice pratique."*

**Principe** : Contexte clair (quand, où, combien de temps)

---

### **Piège 4 : Question hors programme TSSR**

**Question jury** :  
*"Comment configureriez-vous un cluster Kubernetes avec load balancing Ingress ?"* (niveau DevOps, pas TSSR)

❌ **Mauvaise réponse** : Inventer réponse incohérente

✅ **Bonne réponse** : **Admettre limite et établir parallèle**

> *"Kubernetes dépasse le périmètre de ma formation TSSR, je n'ai pas pratiqué cette technologie.*
> 
> *Cependant, je peux faire un parallèle avec VMware DRS que je connais :*
> - *DRS équilibre charge VMs entre hôtes ESXi*
> - *Je suppose que Kubernetes fait similaire avec pods sur nodes*
> 
> *Si je devais apprendre Kubernetes, je commencerais par documenter architecture (Master/Worker nodes), puis pratiquer avec Minikube en local.*
> 
> *Est-ce que cette compétence est requise pour le poste ?"* (retourner question)

**Principe** : Honnêteté + Capacité apprentissage + Parallèle pertinent

---

### **Piège 5 : "Que feriez-vous si... ?" (situation impossible)**

**Question jury** :  
*"Un serveur de production crash et vous avez perdu toutes les sauvegardes. Que faites-vous ?"*

❌ **Mauvaise réponse** : *"Euh... je ne sais pas, c'est catastrophique."*

✅ **Bonne réponse** : **Méthodologie même en situation extrême**

> *"Si toutes les sauvegardes sont perdues, c'est une situation critique qui révèle un problème de PRA. Voici ma démarche :*
> 
> **Immédiat** (30 min) :*
> - *Informer direction/clients (transparence)*
> - *Isoler serveur crashé (éviter aggravation)*
> - *Vérifier si données récupérables (disque RAID dégradé mais pas mort, RAM dump, logs)*
> 
> **Court terme** (2-4h) :*
> - *Contacter éditeur/prestataire (support urgence)*
> - *Tenter récupération données (ddrescue, forensics)*
> - *Reconstruire serveur from scratch si nécessaire*
> 
> **Moyen terme** (post-incident) :*
> - *Post-mortem : Pourquoi sauvegardes perdues ?*
> - *Mettre en place règle 3-2-1-1-0 (copies off-site)*
> - *Tests restauration mensuels*
> 
> *Dans tous les cas, j'escaladerais immédiatement vers N+2 et prestataires externes."*

**Principe** : Structure (immédiat/court/moyen terme) + Conscience gravité + Amélioration continue

---

### **Piège 6 : Question technique ultra-précise**

**Question jury** :  
*"Quelle est la taille par défaut de la fenêtre TCP en octets sur Windows 10 ?"*

❌ **Mauvaise réponse** : Inventer chiffre au hasard *"Euh... 2048 ?"*

✅ **Bonne réponse** : **Admettre + Montrer où trouver info**

> *"Je ne connais pas la valeur exacte par cœur, mais je sais comment la trouver :*
> 
> *Commande PowerShell :*
> ```powershell
> Get-NetTCPSetting | Select-Object SettingName, AutoTuningLevelLocal
> netsh interface tcp show global
> ```
> 
> *La fenêtre TCP est dynamique sur Windows 10 (auto-tuning). Elle commence généralement autour de 64 Ko et peut aller jusqu'à plusieurs Mo selon conditions réseau.*
> 
> *Si nécessaire pour dépannage, je consulterais Microsoft Docs ou utiliserais Wireshark pour observer valeur réelle dans handshake TCP."*

**Principe** : Ne pas inventer → Montrer démarche recherche info

---

### **Piège 7 : "Critique une technologie que vous utilisez"**

**Question jury** :  
*"Quels sont les défauts de VMware vSphere ?"*

❌ **Mauvaise réponse** : *"Aucun, c'est parfait."* (pas crédible)

✅ **Bonne réponse** : **Critique constructive + Alternative**

> *"VMware vSphere est excellent, mais a quelques inconvénients :*
> 
> **1. Coût élevé** :*
> - *Licences vSphere + vCenter = 5-10k€ pour PME*
> - *Alternative : Proxmox (open-source, gratuit)*
> 
> **2. Complexité** :*
> - *Courbe d'apprentissage steep pour débutants*
> - *Nécessite certifications (VCP) pour expertise*
> 
> **3. Dépendance éditeur** :*
> - *Enfermement (vendor lock-in)*
> - *Changements politique licences (rachat Broadcom 2024)*
> 
> *Malgré ça, VMware reste leader marché pour fiabilité et fonctionnalités (vMotion, HA, DRS). En entreprise, le coût est justifié par stabilité."*

**Principe** : Honnêteté (reconnaître défauts) + Nuance (malgré tout, leader)

---

### **Piège 8 : Comparaison Windows vs Linux**

**Question jury** :  
*"Lequel préférez-vous : Windows ou Linux ?"*

❌ **Mauvaise réponse** : Choisir camp et dénigrer l'autre

✅ **Bonne réponse** : **Pragmatique, selon contexte**

> *"Les deux ont leur place selon usage :*
> 
> **Windows Server** :*
> - *Meilleur pour : Active Directory, Exchange, environnement Microsoft (Office 365)*
> - *GUI plus accessible pour administrateurs débutants*
> - *Support commercial (tickets Microsoft)*
> 
> **Linux (Debian/Ubuntu Server)** :*
> - *Meilleur pour : Serveurs web (Apache, Nginx), conteneurs (Docker), coût (gratuit)*
> - *Performances supérieures (moins de ressources)*
> - *Flexibilité scripting (Bash, cron)*
> 
> *Personnellement, j'utilise les deux :*
> - *Windows pour infra AD, GPO, postes utilisateurs*
> - *Linux pour services web, BDD MySQL, routeurs (pfSense)*
> 
> *L'idéal en entreprise : infrastructure hybride."*

**Principe** : Pas de dogmatisme, choisir outil adapté au besoin

---

### **Piège 9 : "Racontez un échec"**

**Question jury** :  
*"Parlez-moi d'une fois où vous avez fait une erreur technique grave."*

❌ **Mauvaise réponse** : *"Je ne fais jamais d'erreur"* (pas crédible) OU raconter catastrophe sans leçon

✅ **Bonne réponse** : **Méthode STAR (Situation, Tâche, Action, Résultat) + Leçon apprise**

> *"Lors d'un projet formation, j'ai fait une erreur de configuration GPO qui a bloqué 20 postes :*
> 
> **Situation** : Devais bloquer USB sur OU Test (5 PCs)*
> 
> **Tâche** : Créer GPO restriction périphériques*
> 
> **Action (erreur)** : Lié GPO à mauvaise OU (OU Production au lieu de Test)*
> 
> **Résultat** : 20 PCs production bloqués, impossible travailler*
> 
> **Résolution** :*
> - *Détecté erreur en 10 min (tests utilisateurs)*
> - *Délié GPO immédiatement*
> - *Forcé mise à jour : `gpupdate /force` sur tous PCs*
> - *Retour normal en 20 min*
> 
> **Leçon apprise** :*
> - *Toujours tester GPO sur OU Test avant Production*
> - *Utiliser filtrage sécurité (limiter à 1-2 PCs pilotes)*
> - *Documenter changements dans ticket avant application*
> 
> *Depuis, je crée systématiquement OU Test et jamais appliqué GPO directement en prod."*

**Principe** : Reconnaître erreur + Résolution rapide + Amélioration pratiques

---

### **Piège 10 : Question politique/salaire**

**Question jury** :  
*"Combien voulez-vous gagner ?"* (en plein entretien technique)

❌ **Mauvaise réponse** : Se lancer dans négociation salaire maintenant

✅ **Bonne réponse** : **Reporter poliment**

> *"Je préfère discuter rémunération une fois que nous aurons validé l'adéquation technique. Pour l'instant, je me concentre sur démontrer mes compétences.*
> 
> *Si mon profil vous convient, nous pourrons aborder ce sujet en fin d'entretien ou lors d'un second RDV. Mes attentes sont en ligne avec le marché TSSR junior (25-30k€ selon Région Île-de-France/Province)."*

**Principe** : Rester focus sur technique, pas polluer entretien avec négociation

---

---

## 💡 CONSEILS STRATÉGIQUES RÉPONSES ORALES

### **1. Structure réponse type (méthode DESC)**

```
D = Définition (quoi ?)
E = Exemple concret (où, quand ?)
S = Spécificités techniques (comment ?)
C = Conclusion / Cas d'usage (pourquoi ?)
```

**Exemple** : *"Expliquez vMotion"*

> **D** : *"vMotion migre VM en live entre hôtes ESXi sans downtime."*
> **E** : *"En formation, j'ai migré 5 VMs pendant maintenance hôte ESXi-01 vers ESXi-02."*
> **S** : *"Nécessite vCenter, réseau vMotion dédié Gigabit, stockage partagé (SAN iSCSI)."*
> **C** : *"Utilité : maintenance hardware sans arrêter services, équilibrage charge automatique (DRS)."*

---

### **2. Technique de l'entonnoir (du général au spécifique)**

```
Niveau 1 : Vue 10 000 pieds (contexte global)
    ↓
Niveau 2 : Focus spécifique (votre expérience)
    ↓
Niveau 3 : Détail technique (commandes, config)
```

**Exemple** : *"Parlez-moi de votre projet Active Directory"*

> **Niveau 1** : *"J'ai déployé une infrastructure AD pour entreprise simulée de 50 utilisateurs."*
> **Niveau 2** : *"Rôles : DC principal, DNS intégré, DHCP avec options. Structure OU par département (RH, IT, Compta)."*
> **Niveau 3** : *"Commandes PowerShell : `New-ADOrganizationalUnit -Name "RH"`, GPO blocage USB sur OU_RH, script automatisation création comptes CSV."*

---

### **3. Utiliser analogies (vulgarisation)**

| Concept technique | Analogie compréhensible |
|-------------------|------------------------|
| **VLAN** | Immeubles dans même ville (même switch) mais pas voisins (isolés) |
| **Snapshot VM** | Photo Polaroid (instant T), peut revenir en arrière |
| **DHCP** | Distributeur automatique adresses IP (vs attribution manuelle) |
| **DNS** | Annuaire téléphonique (nom → numéro/IP) |
| **Firewall** | Videur boîte de nuit (autorise/refuse selon règles) |
| **Backup GFS** | Albums photos : quotidiens (semaine), hebdos (mois), annuels (archives) |

---

### **4. Gestion temps réponse**

| Type question | Temps idéal réponse | Structure |
|---------------|---------------------|-----------|
| **Définition simple** | 30 sec - 1 min | 1 phrase définition + 1 exemple |
| **Comparaison (A vs B)** | 1-2 min | Tableau 2 colonnes mental, 2 différences clés |
| **Procédure (Comment faire X ?)** | 2-3 min | 4-6 étapes numérotées, pas plus |
| **Cas pratique** | 3-5 min | Situation → Analyse → Solution → Validation |

**Si vous dépassez 3 min** : Jury peut vous interrompre (pas grave, c'est normal)

**Si jury silent après réponse** : Demander *"Souhaitez-vous que je développe un point précis ?"*

---

### **5. Langage corporel (communication non-verbale)**

✅ **À FAIRE** :
- Contact visuel avec les 3 jurys (balayer regard, pas fixer un seul)
- Posture droite (pas avachi), mains posées table (ou gesticulation modérée)
- Sourire léger (montre confiance, pas stress)
- Hochements tête quand jury parle (écoute active)

❌ **ÉVITER** :
- Croiser bras (attitude défensive)
- Gigoter jambes sous table (stress visible)
- Triturer stylo/téléphone
- Regarder plafond/sol (perte contact)

---

### **6. Mots de transition (relier idées)**

| Situation | Transition | Exemple |
|-----------|------------|---------|
| **Ajouter info** | *"De plus..."*, *"Par ailleurs..."* | *"Le VLAN isole trafic. **De plus**, il améliore sécurité."* |
| **Opposer** | *"En revanche..."*, *"Cependant..."* | *"DHCP automatise. **En revanche**, IP fixe pour serveurs."* |
| **Illustrer** | *"Par exemple..."*, *"Concrètement..."* | *"GPO bloque USB. **Par exemple**, OU_RH = pas de clés USB."* |
| **Conclure** | *"En résumé..."*, *"Pour conclure..."* | *"**En résumé**, vMotion = 0 downtime, HA = 2-5 min."* |

---

---

## 📊 GRILLE D'ÉVALUATION ENTRETIEN TECHNIQUE

### **Barème jury (60 points)**

| Critère | Points max | Détails |
|---------|-----------|---------|
| **1. Connaissances techniques** | **25 pts** | |
| CCP1 (Support) | 4 pts | Méthodologie, ITIL, priorisation |
| CCP2 (Linux) | 5 pts | Commandes, logs, cron, tar |
| CCP3 (Réseau) | 4 pts | Subnetting, DNS, DHCP, VLAN |
| CCP4 (Windows/AD) | 5 pts | AD, GPO, PowerShell |
| CCP5 (Virtualisation) | 4 pts | VMware, snapshots, vMotion, HA |
| CCP6 (Sécurité) | 3 pts | GFS, RTO/RPO, ransomware |
| **2. Méthodologie** | **10 pts** | |
| Structuration réponses | 4 pts | Étapes logiques, pas brouillon |
| Exemples concrets | 3 pts | Pas que théorie, cas pratiques |
| Gestion erreurs | 3 pts | Admettre lacunes, montrer démarche |
| **3. Communication** | **10 pts** | |
| Clarté expression | 4 pts | Phrases complètes, vocabulaire précis |
| Écoute active | 3 pts | Reformule questions, pas coupe parole |
| Vulgarisation | 3 pts | Explique concepts simplement |
| **4. Professionnalisme** | **10 pts** | |
| Attitude (calme, confiance) | 4 pts | Pas stress visible, souriant |
| Gestion temps | 3 pts | Réponses concises (pas trop longues) |
| Motivation | 3 pts | Enthousiasme, curiosité techno |
| **5. Dossier professionnel** | **5 pts** | |
| Cohérence DP/réponses | 3 pts | Pas contradiction projet décrit/oral |
| Profondeur projets | 2 pts | Détails techniques, pas superficiel |
| **TOTAL** | **/60 pts** | |

### **Échelle notation**

| Tranche | Points | Appréciation | Résultat |
|---------|--------|--------------|----------|
| **Excellent** | 50-60 | ⭐⭐⭐ Maîtrise complète | Validé |
| **Très bien** | 40-49 | ⭐⭐ Solide, quelques imprécisions | Validé |
| **Bien** | 30-39 | ⭐ Acceptable, lacunes mineures | Validé |
| **Passable** | 24-29 | ⚠️ Juste (seuil 40%) | Validé limite |
| **Insuffisant** | < 24 | ❌ Lacunes importantes | Non validé |

**Seuil validation** : **24/60 (40%)**

---

---

## ✅ CHECKLIST FINALE AVANT ORAL

### **Veille (J-1)**

☐ Relire fiches récapitulatives (6 fiches)  
☐ Réviser top 20 commandes (Linux, PowerShell, Cisco)  
☐ Préparer présentation projet (3-5 slides max)  
☐ Préparer questions à poser au jury (2-3 questions)  
☐ Tenue professionnelle préparée (chemise, pas t-shirt)  
☐ Repos suffisant (7-8h sommeil)  

### **Jour J - 1h avant**

☐ Arrivée 15 min en avance (pas 1h, pas 2 min)  
☐ Téléphone éteint (pas silencieux, éteint)  
☐ Dossier professionnel (2 exemplaires papier)  
☐ CV (3 exemplaires)  
☐ Bloc-notes + stylo  
☐ Bouteille d'eau  

### **Pendant entretien**

☐ Vouvoyer jury systématiquement  
☐ Reformuler questions si doute  
☐ Structurer réponses (étapes numérotées)  
☐ Exemples concrets (pas que théorie)  
☐ Admettre ignorance si nécessaire  
☐ Demander précisions si question vague  
☐ Sourire, contact visuel  
☐ Respirer profondément si stress  

### **Fin entretien**

☐ Remercier jury pour temps accordé  
☐ Poser questions préparées (montrer intérêt)  
☐ Demander délai résultat  
☐ Serrer main (si proposé)  

---

---

## 🎯 QUESTIONS À POSER AU JURY (fin entretien)

**Si jury demande** : *"Avez-vous des questions ?"*

✅ **Bonnes questions** (montrent intérêt) :

1. *"Quels sont les retours que vous donneriez pour améliorer ma prestation aujourd'hui ?"* (amélioration continue)

2. *"Quelles technologies émergentes recommanderiez-vous d'approfondir pour un profil TSSR ?"* (veille techno)

3. *"Quel est le profil type des candidats qui réussissent le mieux en poste TSSR ?"* (benchmark)

4. *"Quelle est la prochaine étape du processus de recrutement ?"* (timeline)

❌ **Mauvaises questions** :

- *"C'est bon, je suis pris ?"* (trop direct)
- *"Combien de jours de congés ?"* (pas le moment)
- *"Quand je commence ?"* (présomptueux)

---

---

## 📞 APRÈS L'ENTRETIEN

### **Débriefing personnel (dans les 2h)**

1. **Noter questions posées** : Rédiger liste questions jury (mémorisation fraîche)
2. **Auto-évaluation** : Quelles réponses bien passées ? Lesquelles améliorables ?
3. **Points à retravailler** : Identifier 2-3 sujets techniques à réviser

---

---

## 🏆 RÉCAPITULATIF FINAL : CLÉ DU SUCCÈS

```
┌──────────────────────────────────────────────┐
│  FORMULE RÉUSSITE ENTRETIEN TECHNIQUE        │
├──────────────────────────────────────────────┤
│                                              │
│  60% Connaissances techniques solides        │
│      ↳ Réviser fiches, pratiquer commandes   │
│                                              │
│  20% Méthodologie structurée                 │
│      ↳ DESC, étapes numérotées, exemples     │
│                                              │
│  15% Communication efficace                  │
│      ↳ Clarté, écoute, vulgarisation         │
│                                              │
│  5% Attitude professionnelle                 │
│      ↳ Calme, sourire, motivation            │
│                                              │
└──────────────────────────────────────────────┘
```

**Message final** :

> *Le jury évalue **3 dimensions** :*
> 1. **Savoirs** (connaissances techniques)
> 2. **Savoir-faire** (résolution incidents, méthodologie)
> 3. **Savoir-être** (communication, gestion stress, professionnalisme)
> 
> *Ne cherchez pas la perfection (impossible). Le jury cherche :*
> - *Un technicien **compétent** (pas expert)*
> - *Capable d'**apprendre** (pas tout savoir)*
> - *Avec **bonne attitude** (teamwork, calme)*
> 
> ***Vous êtes prêt(e) ! 💪***

---

