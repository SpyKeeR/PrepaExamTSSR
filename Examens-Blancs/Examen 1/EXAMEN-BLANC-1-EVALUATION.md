# 📝 EXAMEN BLANC 1 - SUJET
## Titre Professionnel TSSR - Mise en Situation Professionnelle + Questionnaire

> **Durée totale Jour 1** : 3h30 (MSP 1h30 + Questionnaire 2h)  
> **Total** : 200 points - **Validation** : 140/200 (70%)

---

## ⏱️ PARTIE 1 : MSP ÉCRITE (1h30 - 100 points)

### 📋 CONTEXTE GÉNÉRAL

**Entreprise** : TechSolutions SAS  
**Activité** : Société de conseil en informatique (80 collaborateurs)  
**Sites** : Siège Paris (50 personnes) + Agence Lyon (30 personnes)  

**Infrastructure existante** :
- Domaine Active Directory : `techsolutions.local`
- Réseau Paris : 192.168.10.0/24
- Réseau Lyon : 192.168.20.0/24
- VPN site-à-site entre Paris et Lyon
- Serveurs : Windows Server 2022, Debian 12
- Virtualisation : VMware vSphere 8

---

## 🎯 INCIDENT 1 : PROBLÈME DHCP (25 minutes - 30 points)

**Date/Heure** : 12/11/2025 - 09h15  
**Ticket #1247** - Priorité : HAUTE  
**Utilisateur** : Marie Dubois (Service Commercial, Lyon)

**Description du problème** :
Depuis ce matin, les nouveaux ordinateurs portables livrés à l'agence de Lyon n'obtiennent pas d'adresse IP automatiquement. Ils affichent l'adresse APIPA 169.254.x.x. Les postes existants fonctionnent normalement.

**Informations complémentaires** :
- 5 nouveaux PC portables Dell (Windows 11 Pro)
- Réseau Lyon : 192.168.20.0/24
- Serveur DHCP : SRV-DHCP-LYON (192.168.20.5)
- Plage DHCP actuelle : 192.168.20.100 à 192.168.20.150
- Nombre de postes existants : 28
- Les anciens postes gardent leur IP (bail non expiré)

**Connexion au serveur DHCP** :
Vous vous connectez en RDP sur `SRV-DHCP-LYON` et constatez que le service `Serveur DHCP` est démarré. Vous ouvrez la console DHCP et observez la configuration de l'étendue.

### Questions :

**1.1** (5 points) Identifiez la cause probable du problème. Justifiez votre réponse en vous basant sur les informations fournies.

```
Votre réponse :
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
```

**1.2** (8 points) Proposez une solution à court terme pour permettre aux 5 nouveaux PC d'obtenir une IP immédiatement. Décrivez précisément les étapes de mise en œuvre (console DHCP Windows Server).

```
Votre réponse :
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
```

**1.3** (7 points) Proposez une solution à moyen terme pour anticiper l'arrivée de nouveaux postes (l'entreprise prévoit 10 recrutements à Lyon d'ici 3 mois). Calculez la nouvelle plage recommandée.

```
Votre réponse :
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
```

**1.4** (5 points) La Direction souhaite réserver des adresses IP fixes pour 3 imprimantes réseau (via réservation DHCP). Expliquez l'avantage de cette méthode par rapport à une IP statique configurée manuellement sur l'imprimante.

```
Votre réponse :
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
```

**1.5** (5 points) Un utilisateur signale que son PC obtient bien une IP mais ne peut pas accéder à Internet ni au serveur de fichiers. Quelles options DHCP devez-vous vérifier ? Citez 3 options essentielles avec leur code.

```
Votre réponse :
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
```

---

## 🔧 INCIDENT 2 : SCRIPT POWERSHELL (25 minutes - 30 points)

**Date/Heure** : 12/11/2025 - 10h30  
**Ticket #1251** - Priorité : NORMALE  
**Demandeur** : Responsable RH

**Contexte** :
Le service RH vous demande de créer un script PowerShell pour automatiser la création de comptes utilisateurs lors des arrivées. Actuellement, la création est manuelle et génère des erreurs (oublis, fautes de frappe).

**Spécifications** :
- Les utilisateurs sont dans un fichier CSV : `C:\Scripts\nouveaux_users.csv`
- Structure CSV : `Prenom,Nom,Service,Fonction`
- Les comptes doivent être créés dans l'OU correspondant au service : `OU=<Service>,OU=Utilisateurs,DC=techsolutions,DC=local`
- Nom de connexion (SamAccountName) : `prenom.nom` (minuscules, remplacer espaces/accents)
- UPN : `prenom.nom@techsolutions.local`
- Mot de passe initial : `P@ssw0rd2025!` (à changer à la première connexion)
- Les comptes doivent être activés

**Exemple de fichier CSV** :
```
Prenom,Nom,Service,Fonction
Jean,Dupont,Commercial,Technico-commercial
Marie,Martin,IT,Administrateur système
Pierre,Lefèvre,Comptabilite,Assistant comptable
```

### Questions :

**2.1** (15 points) Écrivez le script PowerShell complet répondant aux spécifications. Commentez les lignes importantes.

```powershell
# Votre script ici :
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
```

**2.2** (5 points) Le script doit gérer les erreurs (utilisateur déjà existant, OU inexistante). Ajoutez un bloc `Try/Catch` et un fichier de log `C:\Scripts\creation_users.log` avec horodatage.

```powershell
# Ajout gestion erreurs :
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
```

**2.3** (5 points) Ajoutez une fonctionnalité pour envoyer un email récapitulatif au responsable RH (`rh@techsolutions.local`) avec le nombre d'utilisateurs créés et les éventuelles erreurs.

```powershell
# Code d'envoi email :
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
```

**2.4** (3 points) La fonction doit remplacer les caractères accentués (é → e, è → e, à → a) dans le SamAccountName. Proposez une méthode PowerShell.

```powershell
# Fonction de nettoyage :
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
```

**2.5** (2 points) Quelle cmdlet PowerShell utiliseriez-vous pour tester si une OU existe avant de créer l'utilisateur ?

```
Votre réponse :
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
```

---

## 🌐 INCIDENT 3 : RÉSEAU & VLAN (40 minutes - 40 points)

**Date/Heure** : 12/11/2025 - 14h00  
**Ticket #1255** - Priorité : CRITIQUE  
**Utilisateurs** : Tout le service Comptabilité (10 personnes)

**Description** :
Suite à une réorganisation des bureaux, le service Comptabilité a été déplacé dans un nouveau local. Les 10 postes ont été reconnectés sur le switch SW-COMPTA-01 (Cisco Catalyst 2960). Depuis, ils ne peuvent plus accéder au serveur comptable `SRV-COMPTA` (192.168.10.50) ni aux autres ressources réseau.

**Informations réseau** :

**Plan d'adressage actuel** :
- VLAN 10 (Management) : 192.168.10.0/24 (Serveurs, admin)
- VLAN 20 (Utilisateurs) : 192.168.20.0/24 (Postes de travail)
- VLAN 30 (Comptabilité) : 192.168.30.0/24 (Service comptable)
- VLAN 40 (Invités) : 192.168.40.0/24 (WiFi visiteurs)

**Topologie** :
```
Core Switch (SW-CORE-01) - Cisco 3750
    |
    | Trunk (Gi0/1)
    |
SW-COMPTA-01 (Switch service Comptabilité)
    |
    └─ Ports Fa0/1 à Fa0/10 : Postes comptabilité
```

**Configuration actuelle SW-COMPTA-01** :
```cisco
interface GigabitEthernet0/1
 description TRUNK vers SW-CORE-01
 switchport mode trunk
 switchport trunk allowed vlan 10,20,30,40

interface range FastEthernet0/1-10
 description Postes Comptabilite
 switchport mode access
 switchport access vlan 20
```

### Questions :

**3.1** (8 points) Identifiez la cause du problème. Expliquez pourquoi les postes comptabilité ne peuvent pas communiquer avec le serveur SRV-COMPTA.

```
Votre réponse :
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
```

**3.2** (10 points) Proposez la configuration Cisco IOS à appliquer sur `SW-COMPTA-01` pour résoudre le problème. Écrivez les commandes exactes avec les modes de configuration.

```cisco
! Votre configuration :
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
```

**3.3** (7 points) Après correction, les postes obtiennent une IP via DHCP mais sont dans le mauvais sous-réseau (192.168.20.x au lieu de 192.168.30.x). Identifiez la cause et proposez une solution au niveau du serveur DHCP Windows.


```
Votre réponse :
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
```

**3.4** (5 points) Un poste comptable (PC-COMPTA-05, IP 192.168.30.105) ne peut pas atteindre le serveur `SRV-COMPTA` (192.168.10.50) alors que les autres postes comptabilité y accèdent. Proposez une méthodologie de dépannage en 5 étapes avec les commandes.

```
Votre réponse :
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
```

**3.5** (10 points) La Direction souhaite que le VLAN Comptabilité (30) ne puisse communiquer qu'avec le VLAN Serveurs (10) et pas avec les autres VLAN. Expliquez le principe des ACL (Access Control List) et proposez une ACL étendue sur le routeur/switch L3.

```
Votre réponse :
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
```

---

## ⏱️ PARTIE 2 : QUESTIONNAIRE TECHNIQUE (2h - 100 points)

### 📋 SECTION A : LINUX (20 points)

**Question A1** (3 points)  
Quelle commande permet de lister tous les processus en cours, incluant ceux des autres utilisateurs, avec le maximum de détails ?
- [ ] A) `ps`
- [ ] B) `ps aux`
- [ ] C) `top`
- [ ] D) `ls -la`

**Question A2** (3 points)  
Vous devez donner à l'utilisateur `jdupont` les droits d'exécuter la commande `systemctl restart apache2` sans mot de passe via `sudo`. Quelle ligne ajouter dans `/etc/sudoers` ?
- [ ] A) `jdupont ALL=(ALL) NOPASSWD: /bin/systemctl restart apache2`
- [ ] B) `jdupont sudo systemctl restart apache2`
- [ ] C) `jdupont ALL systemctl`
- [ ] D) `ALL jdupont systemctl`

**Question A3** (4 points)  
Expliquez la différence entre les permissions `chmod 755` et `chmod 644` sur un fichier. Donnez un exemple d'usage pour chaque.

```
Votre réponse :
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
```

**Question A4** (5 points)  
Vous devez monter automatiquement au démarrage un partage NFS `192.168.1.50:/exports/data` vers `/mnt/data`. Quelle ligne ajouter dans `/etc/fstab` ? Expliquez chaque option.

```
Votre réponse :
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
```

**Question A5** (5 points)  
Un serveur Linux est lent. Décrivez 5 commandes pour diagnostiquer la charge CPU, RAM, disque et réseau.

```
Votre réponse :
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
```

---

### 📋 SECTION B : RÉSEAU (25 points)

**Question B1** (5 points)  
Calculez le nombre d'hôtes utilisables dans le réseau `172.16.50.0/21`. Donnez également l'adresse de broadcast et le masque en décimal.

```
Votre réponse :
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
```

**Question B2** (5 points)  
Vous devez segmenter le réseau `10.0.0.0/24` en 6 sous-réseaux avec VLSM. Proposez un plan d'adressage si :
- VLAN 10 : 50 hôtes
- VLAN 20 : 30 hôtes
- VLAN 30 : 20 hôtes
- VLAN 40 : 10 hôtes
- VLAN 50 : 10 hôtes
- VLAN 60 : 5 hôtes

```
Votre réponse :
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
```

**Question B3** (5 points)  
Expliquez la différence entre un switch Layer 2 et un switch Layer 3. Donnez 2 avantages du L3.

```
Votre réponse :
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
```

**Question B4** (5 points)  
Décrivez le processus DORA du protocole DHCP. Précisez le type de trame (broadcast/unicast) pour chaque étape.

```
Votre réponse :
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
```

**Question B5** (5 points)  
Un utilisateur ne peut pas atteindre le site `www.google.com` mais peut pinger `8.8.8.8`. Identifiez le problème et proposez 2 solutions.

```
Votre réponse :
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
```

---

### 📋 SECTION C : WINDOWS SERVER & ACTIVE DIRECTORY (25 points)

**Question C1** (5 points)  
Expliquez l'ordre d'application des GPO avec l'acronyme LSDOU. Donnez un exemple de conflit et la GPO qui s'applique.

```
Votre réponse :
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
```

**Question C2** (5 points)  
Vous devez créer 50 utilisateurs depuis un fichier CSV avec PowerShell. Écrivez la commande `New-ADUser` avec les paramètres minimum (nom, prénom, SamAccountName, OU, mot de passe, activation).

```powershell
Votre réponse :
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
```

**Question C3** (5 points)  
Listez les 5 rôles FSMO d'Active Directory. Précisez ceux au niveau forêt et ceux au niveau domaine.

```
Votre réponse :
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
```

**Question C4** (5 points)  
Un utilisateur a oublié son mot de passe. Décrivez 2 méthodes pour le réinitialiser (1 en GUI, 1 en PowerShell).

```
Votre réponse :
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
```

**Question C5** (5 points)  
Expliquez la différence entre un groupe Global et un groupe Domain Local. Donnez la règle AGDLP.

```
Votre réponse :
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
```

---

### 📋 SECTION D : VIRTUALISATION (15 points)

**Question D1** (5 points)  
Quelle est la différence entre un snapshot et un backup de VM ? Peut-on garder des snapshots longtemps ? Justifiez.

```
Votre réponse :
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
```

**Question D2** (5 points)  
Expliquez le fonctionnement de vSphere HA. Quel est le temps d'indisponibilité lors d'une panne d'hôte ?

```
Votre réponse :
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
```

**Question D3** (5 points)  
Quelles sont les 3 prérequis pour effectuer un vMotion (migration à chaud) ?

```
Votre réponse :
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
```

---

### 📋 SECTION E : SÉCURITÉ & SAUVEGARDE (15 points)

**Question E1** (5 points)  
Une entreprise définit RTO = 2h et RPO = 30 min. Expliquez ces termes et proposez une stratégie de sauvegarde adaptée.

```
Votre réponse :
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
```

**Question E2** (5 points)  
Expliquez la règle 3-2-1 des sauvegardes avec un exemple concret d'application en entreprise.

```
Votre réponse :
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
```

**Question E3** (5 points)  
Citez 3 différences entre un pare-feu stateful et stateless. Lequel est recommandé et pourquoi ?

```
Votre réponse :
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
```

---

### 📋 SECTION F : ANGLAIS TECHNIQUE (5 points)

**Texte** :

*"A critical vulnerability has been discovered in the Apache Log4j library (CVE-2021-44228), allowing remote code execution. System administrators must immediately update all affected servers and apply the security patches provided by the vendor. The exploit, known as Log4Shell, enables attackers to execute arbitrary code on vulnerable systems by sending specially crafted requests. Organizations should audit their infrastructure to identify all instances of Log4j and prioritize patching based on internet exposure."*

**Question F1** (5 points)  
Résumez en français les points clés de cette alerte de sécurité (vulnérabilité, impact, actions requises).

```
Votre réponse :
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
```

---

## 📝 CONSIGNES

1. **Gestion du temps** : Surveillez votre temps (1h30 MSP, 2h Questionnaire)
2. **Lecture attentive** : Lisez bien chaque question avant de répondre
3. **Clarté** : Réponses lisibles, code indenté correctement
4. **Syntaxe** : Attention aux commandes (majuscules, tirets, chemins)
5. **Justification** : Expliquez vos choix quand demandé

**Bon courage ! 🚀**
