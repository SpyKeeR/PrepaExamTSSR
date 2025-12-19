## ✅ CORRECTIONS DÉTAILLÉES

### ✔️ Partie 1 : Support Utilisateur & Helpdesk

**Question 1 : B) Qualifier, enregistrer et résoudre les incidents simples**
- **N1** (First Level Support) : Contact initial, qualification, résolution incidents simples (30-70% résolution)
- **N2** : Expertise technique, incidents complexes
- **N3** : Experts, développeurs, éditeurs

**Question 2 : B) Vérifier les droits NTFS et l'appartenance aux groupes**
Méthodologie support :
1. Qualifier le problème (questions)
2. Reproduire si possible
3. Vérifications de base (droits, connexion réseau, état service)
4. Solution
5. Documentation

**Question 3 : B) Nouveau**
Cycle de vie ticket GLPI :
- **Nouveau** → En cours (attribué) → En attente → Résolu → Clos

**Question 4 : B) L'urgence = délai souhaité, l'impact = étendue des conséquences**
- **Urgence** : Combien de temps peut-on attendre ? (faible/moyenne/haute)
- **Impact** : Combien d'utilisateurs/services affectés ? (1 user / service / entreprise)

**Question 5 : B) Priorité = Urgence × Impact (matrice)**

Matrice de priorité :

|  | Impact faible | Impact moyen | Impact élevé |
|---|---|---|---|
| **Urgence faible** | P4 (basse) | P3 (moyenne) | P2 (haute) |
| **Urgence moyenne** | P3 (moyenne) | P2 (haute) | P1 (critique) |
| **Urgence haute** | P2 (haute) | P1 (critique) | P1 (critique) |

**Question 6 : B) Questions ouvertes QQOQCCP**
- **Qui** : Qui est affecté ?
- **Quoi** : Quel est le problème exact ?
- **Où** : Sur quel poste/service ?
- **Quand** : Depuis quand ? À quel moment ?
- **Comment** : Comment se manifeste le problème ?
- **Combien** : Combien de fois ? Combien d'utilisateurs ?
- **Pourquoi** : Contexte, changement récent ?

**Question 7 : B) Passer au niveau supérieur (N1 → N2 → N3)**
- **Verticale** : Escalade hiérarchique (niveau expertise)
- **Horizontale** : Passer à un collègue même niveau (charge, spécialité)

**Question 8 : B) Symptôme + Cause + Solution + Mots-clés**
Structure KB :
```
Titre : Impossible d'accéder au dossier partagé
Symptôme : Message "Accès refusé" à \\serveur\partage
Cause : Utilisateur pas dans le groupe AD_Partage
Solution : Ajouter l'utilisateur au groupe AD_Partage
Mots-clés : partage, accès refusé, réseau, permissions
```

**Question 9 : B) Rester calme, écouter activement, reformuler, empathie**
Techniques :
- Écoute active
- Empathie : "Je comprends votre frustration"
- Reformulation : "Si je comprends bien..."
- Rester professionnel
- Proposer des solutions

**Question 10 : A) Accord de niveau de service**
SLA définit :
- Délais de prise en compte
- Délais de résolution (par priorité)
- Disponibilité services
- Pénalités si non-respect

Exemple :
- P1 (critique) : Prise en compte 15 min, résolution 4h
- P2 (haute) : Prise en compte 1h, résolution 8h
- P3 (moyenne) : Prise en compte 4h, résolution 24h

**Question 11 : C) Objectif de résolution (cible)**
SLA = engagement de **moyens**, pas de résultat absolu
- Objectif : 95% des tickets P1 résolus en 4h
- Si cause externe (éditeur) → peut dépasser

**Question 12 : A) SLA = externe (client), OLA = interne**
- **SLA** : Contrat avec le CLIENT (entreprise)
- **OLA** : Accord entre équipes IT internes (support ↔ réseau ↔ système)

**Question 13 : B) Attente action utilisateur ou tiers**
Causes fréquentes :
- Attente retour utilisateur
- Attente pièce/matériel
- Attente éditeur/prestataire
- Attente validation

**Question 14 : C) CSAT (Customer Satisfaction Score) ou NPS**
- **CSAT** : "Êtes-vous satisfait ?" (1-5 étoiles)
- **NPS** (Net Promoter Score) : "Recommanderiez-vous ?" (0-10)
- Envoyé après fermeture ticket

**Question 15 : B) Statistiques, tendances, optimisation**
Bénéfices :
- Identifier problèmes récurrents
- Allouer ressources (beaucoup tickets réseau → renforcer équipe)
- Amélioration continue
- Reporting management

---

### ✔️ Partie 2 : ITIL v3 Fondamentaux

**Question 16 : A) Information Technology Infrastructure Library**
- Créé par le gouvernement britannique (années 1980)
- Framework de bonnes pratiques IT
- Standard mondial

**Question 17 : B) Incident = interruption, Problème = cause inconnue**

| Critère | Incident | Problème |
|---------|----------|----------|
| **Définition** | Interruption/dégradation service | Cause inconnue d'incidents (récurrents) |
| **Objectif** | Restaurer le service VITE | Identifier cause racine |
| **Durée** | Court terme | Moyen/long terme |
| **Solution** | Workaround acceptable | Résolution définitive |

**Question 18 : A) Incident**
Car : interruption du service d'impression pour 1 utilisateur

**Question 19 : B) Un problème**
- 5 incidents similaires → Investigation
- Cause identifiée (driver corrompu) = **Problème**
- Correction définitive (réinstaller bon driver) = Résolution du problème

**Question 20 : B) Restaurer le service rapidement**
Gestion incidents :
1. Restaurer le service (même temporairement)
2. Contournement acceptable
3. Documentation
4. Si récurrent → Créer un "Problème"

**Question 21 : B) Identifier et éliminer la cause racine**
Gestion problèmes :
- Analyse approfondie (5 Pourquoi, Ishikawa)
- Trouver cause racine
- Corriger définitivement
- Éviter récurrence

**Question 22 : B) Solution temporaire sans corriger la cause**
Exemples :
- Serveur mail lent → Redémarrer (workaround) vs Ajouter RAM (résolution)
- Imprimante HS → Utiliser autre imprimante (workaround)

**Question 23 : A) Toute modification de l'infrastructure IT**
Exemples :
- Installation serveur
- Mise à jour logiciel
- Changement configuration réseau
- Ajout utilisateur (si processus formalisé)

**Question 24 : B) Évaluer et approuver les changements**
CAB (Change Advisory Board) :
- Réunion régulière
- Membres : Responsables IT, métiers concernés
- Évalue risques/impacts
- Approuve/reporte/rejette

**Question 25 : A) Standard, Normal, Urgent**
- **Standard** : Pré-approuvé, faible risque (ajout compte user) → Pas de CAB
- **Normal** : Planifié, évaluation CAB (maj serveur)
- **Urgent** : Emergency, pour résoudre incident critique (pas le temps d'attendre CAB)

---

### ✔️ Partie 3 : ITIL 4 & Évolutions

**Question 26 : B) La chaîne de valeur des services (SVC)**
ITIL v3 : 5 phases (Stratégie → Conception → Transition → Exploitation → Amélioration)
ITIL 4 : Service Value System (SVS) avec chaîne de valeur

**Question 27 : B) Incident Management et Problem Management**
ITIL 4 : 34 pratiques (dont ces 2)
Évolutions : Plus Agile, intégration DevOps, focus valeur client

**Question 28 : A) Système complet de création de valeur**
SVS contient :
- Principes directeurs (7)
- Gouvernance
- Chaîne de valeur (6 activités)
- Pratiques (34)
- Amélioration continue

**Question 29 : B) 7 principes directeurs**
1. Focus sur la valeur
2. Commencer là où vous êtes
3. Progresser itérativement avec feedback
4. Collaborer et promouvoir la visibilité
5. Penser et travailler de manière holistique
6. Rester simple et pratique
7. Optimiser et automatiser

**Question 30 : B) Analyser l'existant, réutiliser ce qui fonctionne**
Évite de :
- Tout reconstruire de zéro
- Ignorer l'existant
Encourage :
- Audit de l'existant
- Réutilisation (assets, processus qui marchent)
- Amélioration incrémentale

**Question 31 : B) Créer de la valeur pour le client**
Toute activité IT doit :
- Répondre à un besoin métier
- Créer de la valeur mesurable
- Être justifiée (ROI)

**Question 32 : B) Plan, Improve, Engage, Design & Transition, Obtain/Build, Deliver & Support**
Chaîne de valeur SVC (6 activités) :
1. **Plan** : Planification stratégique
2. **Improve** : Amélioration continue
3. **Engage** : Engagement parties prenantes
4. **Design & Transition** : Conception et transition
5. **Obtain/Build** : Obtenir/Construire
6. **Deliver & Support** : Livrer et supporter

**Question 33 : B) Collaboration Dev + Ops**
DevOps :
- Casser les silos Dev/Ops
- Automatisation (CI/CD)
- Livraison continue
- Feedback rapide
ITIL 4 intègre ces concepts

**Question 34 : A) Knowledge Management**
Pratique gérant :
- Base de connaissances
- Articles de résolution
- Documentation
- Partage d'expertise

**Question 35 : B) Pratique permanente d'optimisation**
Amélioration continue (Kaizen) :
- Cycle PDCA (Plan-Do-Check-Act)
- Itérations courtes
- Mesure KPIs
- Ajustements réguliers

---

### ✔️ Partie 4 : Pratiques & Outils

**Question 36 : A) GLPI**
Outils ticketing courants :
- **GLPI** (open-source, français)
- ServiceNow (propriétaire, leader)
- Jira Service Management
- FreshService
- Zendesk

**Question 37 : B) Lier des tickets**
Exemple :
- Ticket parent : "Serveur web indisponible"
  - Ticket enfant 1 : User A ne peut pas accéder
  - Ticket enfant 2 : User B ne peut pas accéder
  - Ticket enfant 3 : User C ne peut pas accéder

Fermer parent → Ferme automatiquement enfants

**Question 38 : A) Portail self-service utilisateur**
Fonctions :
- Créer ticket
- Suivre statut tickets
- Consulter KB
- Demandes de service (nouveau PC, logiciel)
- FAQ

Bénéfices :
- Autonomie utilisateurs
- Réduction charge support N1

**Question 39 : A) MTTR (Mean Time To Resolve)**
Indicateurs clés :
- **MTTR** : Temps moyen de résolution
- **MTBF** : Temps moyen entre pannes (fiabilité)
- **FRT** : First Response Time (temps première réponse)
- **FCR** : First Call Resolution (résolution au premier contact)

**Question 40 : B) Base de données des CI (Configuration Items)**
CMDB contient :
- Serveurs
- Postes de travail
- Applications
- Licences
- Relations/dépendances

Exemple relation :
```
Application Web
    ↓ dépend de
Serveur Web Apache
    ↓ hébergé sur
VM Linux
    ↓ exécutée sur
Serveur physique ESXi
```

Bénéfices :
- Analyse d'impact (avant changement)
- Cartographie SI
- Gestion incidents (identifier dépendances)

---

## 📊 BARÈME ET ÉVALUATION

**Nombre de bonnes réponses** : _____ / 40

| Score | Niveau | Commentaire |
|-------|--------|-------------|
| 36-40 | ⭐⭐⭐ Expert | Excellent ! Maîtrise ITIL et support |
| 30-35 | ⭐⭐ Très bien | Bonnes bases, révise détails |
| 24-29 | ⭐ Bien | Relis FICHE Support & ITIL |
| 18-23 | 🟡 Moyen | Révision approfondie nécessaire |
| 0-17 | 🔴 Insuffisant | Revoir tous les cours ITIL et GLPI |

---

## 🎯 POINTS CLÉS À RETENIR

### Niveaux de support
```
N1 (First Level)
├─ Qualification tickets
├─ Résolution incidents simples (30-70%)
└─ Escalade N2 si besoin

N2 (Second Level)
├─ Expertise technique
├─ Incidents complexes
└─ Escalade N3 si besoin

N3 (Third Level)
├─ Experts / Développeurs / Éditeurs
└─ Problèmes très complexes
```

### Matrice Priorité
```
Urgence × Impact = Priorité

Exemple :
- 1 VIP bloqué (Impact faible × Urgence haute) = P2
- 500 users bloqués (Impact élevé × Urgence haute) = P1
```

### ITIL : Incident vs Problème
```
[Incident 1: User A imprimante HS]
[Incident 2: User B imprimante HS]  → Récurrent ?
[Incident 3: User C imprimante HS]     ↓
                                   [Problème créé]
                                        ↓
                              Analyse cause racine
                                        ↓
                              Driver corrompu identifié
                                        ↓
                              Résolution définitive
```

### Cycle de vie changement
```
1. RFC (Request For Change) créée
2. Évaluation CAB
3. Approbation / Rejet
4. Planification
5. Implémentation
6. Revue post-implémentation
```

### ITIL 4 : 7 Principes directeurs
1. 🎯 Focus sur la valeur
2. 🏁 Commencer là où vous êtes
3. 🔄 Progresser itérativement
4. 🤝 Collaborer et promouvoir visibilité
5. 🌐 Penser holistique
6. ✨ Rester simple et pratique
7. ⚙️ Optimiser et automatiser

---

**🎓 Bon courage pour la révision ! Ces concepts sont ESSENTIELS pour le TSSR !**
