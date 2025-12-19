# 🪟 QCM WINDOWS SERVER & ACTIVE DIRECTORY - 30 QUESTIONS
## AD, GPO, DNS, DHCP, Utilisateurs/Groupes, FSMO

> **Pour l'examen** : Structure AD, GPO (priorité LSDOU), DNS zones, DHCP scopes, FSMO roles, réplication

---

## 📋 PARTIE 1 : ACTIVE DIRECTORY - FONDAMENTAUX (Questions 1-12)

### Question 1
Qu'est-ce que l'**Active Directory** (AD) ?
- [ ] A) Un annuaire de fichiers
- [ ] B) Un service d'annuaire centralisé pour gérer utilisateurs, ordinateurs, ressources
- [ ] C) Un système d'exploitation
- [ ] D) Un antivirus

### Question 2
Quelle est la structure hiérarchique de base d'Active Directory ?
- [ ] A) Domaine → Arbre → Forêt → OU
- [ ] B) Forêt → Arbre → Domaine → OU (Unités d'Organisation)
- [ ] C) OU → Domaine → Forêt
- [ ] D) Workgroup → Domaine → Forest

### Question 3
Un **domaine Active Directory** est identifié par :
- [ ] A) Une adresse IP
- [ ] B) Un nom DNS (ex: entreprise.local)
- [ ] C) Un nom NetBIOS uniquement
- [ ] D) Un GUID

### Question 4
Combien de **contrôleurs de domaine** (DC) minimum sont recommandés pour la redondance ?
- [ ] A) 1
- [ ] B) 2
- [ ] C) 5
- [ ] D) 10

### Question 5
Qu'est-ce qu'une **OU** (Organizational Unit) ?
- [ ] A) Un utilisateur
- [ ] B) Un conteneur logique pour organiser objets AD et appliquer GPO
- [ ] C) Un serveur
- [ ] D) Un type de groupe

### Question 6
Quelle est la différence entre un **groupe global** et un **groupe local de domaine** ?
- [ ] A) Aucune différence
- [ ] B) Global = membres du même domaine, utilisable partout / Local = membres de n'importe où, utilisable localement
- [ ] C) Global = pour les fichiers, Local = pour les imprimantes
- [ ] D) Global = Windows, Local = Linux

### Question 7
Quelle commande PowerShell permet de créer un utilisateur AD ?
- [ ] A) `Create-ADUser`
- [ ] B) `New-ADUser`
- [ ] C) `Add-ADUser`
- [ ] D) `Make-ADUser`

### Question 8
Un utilisateur a oublié son mot de passe. Que faire ?
- [ ] A) Supprimer et recréer le compte
- [ ] B) Réinitialiser le mot de passe (Reset Password)
- [ ] C) Réinstaller Active Directory
- [ ] D) Attendre 24h (délai légal)

### Question 9
Que signifie **UPN** (User Principal Name) ?
- [ ] A) Le nom de l'ordinateur
- [ ] B) Le nom de connexion au format email (user@domaine.local)
- [ ] C) Le mot de passe
- [ ] D) L'adresse IP

### Question 10
Où sont stockées les informations Active Directory ?
- [ ] A) Dans le registre Windows
- [ ] B) Dans la base de données **NTDS.dit** (C:\Windows\NTDS\)
- [ ] C) Dans un fichier Excel
- [ ] D) Sur le cloud Azure uniquement

### Question 11
Qu'est-ce que la **réplication Active Directory** ?
- [ ] A) La copie de fichiers
- [ ] B) La synchronisation des modifications entre contrôleurs de domaine
- [ ] C) La sauvegarde automatique
- [ ] D) Un virus

### Question 12
Quel protocole est utilisé pour l'authentification dans Active Directory ?
- [ ] A) HTTP
- [ ] B) FTP
- [ ] C) **Kerberos** (port 88)
- [ ] D) SMTP

---

## 🔧 PARTIE 2 : GPO (GROUP POLICY OBJECTS) (Questions 13-18)

### Question 13
Qu'est-ce qu'une **GPO** (Group Policy Object) ?
- [ ] A) Un utilisateur
- [ ] B) Un ensemble de paramètres de configuration appliqués à des utilisateurs/ordinateurs
- [ ] C) Un serveur
- [ ] D) Un antivirus

### Question 14
Quel est l'ordre d'application des GPO (du moins prioritaire au plus prioritaire) ?
- [ ] A) Site → Domaine → OU
- [ ] B) OU → Domaine → Site
- [ ] C) Local → Site → Domaine → OU (**LSDOU**)
- [ ] D) Aléatoire

### Question 15
Une GPO au niveau **OU** entre en conflit avec une GPO au niveau **Domaine**. Laquelle s'applique ?
- [ ] A) Domaine (prioritaire)
- [ ] B) OU (prioritaire, plus proche de l'objet)
- [ ] C) Aucune
- [ ] D) Les deux en même temps

### Question 16
Quelle commande force la mise à jour immédiate des GPO sur un poste client ?
- [ ] A) `ipconfig /renew`
- [ ] B) `gpupdate /force`
- [ ] C) `net use`
- [ ] D) `shutdown /r`

### Question 17
Où configure-t-on une GPO pour mapper un lecteur réseau ?
- [ ] A) Configuration ordinateur → Préférences → Paramètres Windows → Mappage de lecteur
- [ ] B) Configuration utilisateur → Préférences → Paramètres Windows → Mappage de lecteur
- [ ] C) Dans le fichier hosts
- [ ] D) Via le registre manuellement sur chaque poste

### Question 18
Que signifie **"Enforced"** (Appliqué) sur une GPO ?
- [ ] A) La GPO est désactivée
- [ ] B) La GPO ne peut PAS être bloquée par les OU enfants (priorité absolue)
- [ ] C) La GPO s'applique plus lentement
- [ ] D) La GPO nécessite un redémarrage

---

## 🌐 PARTIE 3 : DNS WINDOWS SERVER (Questions 19-24)

### Question 19
Quel est le rôle du service **DNS** dans Active Directory ?
- [ ] A) Gérer les imprimantes
- [ ] B) Résoudre noms de domaine (FQDN) en adresses IP + Localiser les contrôleurs de domaine (enregistrements SRV)
- [ ] C) Distribuer les adresses IP
- [ ] D) Chiffrer les communications

### Question 20
Qu'est-ce qu'une **zone de recherche directe** (Forward Lookup Zone) ?
- [ ] A) Résolution Nom → IP (ex: serveur1.entreprise.local → 192.168.1.10)
- [ ] B) Résolution IP → Nom
- [ ] C) Un pare-feu
- [ ] D) Un serveur web

### Question 21
Qu'est-ce qu'une **zone de recherche inversée** (Reverse Lookup Zone) ?
- [ ] A) Résolution Nom → IP
- [ ] B) Résolution IP → Nom (ex: 192.168.1.10 → serveur1.entreprise.local)
- [ ] C) Une sauvegarde DNS
- [ ] D) Un enregistrement MX

### Question 22
Quel type d'enregistrement DNS pointe un nom vers une adresse IP ?
- [ ] A) MX (Mail Exchange)
- [ ] B) CNAME (Canonical Name / Alias)
- [ ] C) **A (pour IPv4)** ou AAAA (pour IPv6)
- [ ] D) SRV (Service)

### Question 23
Qu'est-ce qu'un enregistrement **CNAME** ?
- [ ] A) Un alias (nom alternatif pointant vers un autre nom)
- [ ] B) Une adresse IP
- [ ] C) Un serveur mail
- [ ] D) Un contrôleur de domaine

### Question 24
Les contrôleurs de domaine Active Directory sont localisés via des enregistrements :
- [ ] A) A
- [ ] B) MX
- [ ] C) SRV
- [ ] D) CNAME

---

## 📡 PARTIE 4 : DHCP WINDOWS SERVER (Questions 25-30)

### Question 25
Quel est le rôle du service **DHCP** ?
- [ ] A) Résoudre les noms DNS
- [ ] B) Distribuer automatiquement les adresses IP aux clients
- [ ] C) Gérer les utilisateurs
- [ ] D) Sauvegarder les données

### Question 26
Qu'est-ce qu'une **étendue DHCP** (scope) ?
- [ ] A) Un utilisateur
- [ ] B) Une plage d'adresses IP disponibles pour attribution (ex: 192.168.1.100 - 192.168.1.200)
- [ ] C) Un câble réseau
- [ ] D) Une GPO

### Question 27
Qu'est-ce qu'une **réservation DHCP** ?
- [ ] A) Désactiver le DHCP
- [ ] B) Attribuer toujours la même IP à une machine spécifique (via adresse MAC)
- [ ] C) Bloquer une adresse IP
- [ ] D) Un bail DHCP de 1 an

### Question 28
Qu'est-ce qu'une **exclusion DHCP** ?
- [ ] A) Une adresse ou plage d'adresses exclue de la distribution automatique
- [ ] B) Un utilisateur banni
- [ ] C) Un serveur arrêté
- [ ] D) Une GPO

### Question 29
Quelle est la durée par défaut d'un **bail DHCP** (lease) ?
- [ ] A) 1 heure
- [ ] B) 8 jours (standard Windows)
- [ ] C) 1 an
- [ ] D) Permanent

### Question 30
Outre l'adresse IP, quelles informations peut distribuer un serveur DHCP ?
- [ ] A) Uniquement l'IP
- [ ] B) IP + Masque + Passerelle + DNS + Nom de domaine + Serveur WINS + etc.
- [ ] C) Uniquement le masque
- [ ] D) Rien du tout

---

