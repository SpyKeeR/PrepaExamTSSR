# 📚 CCP4 - SERVICES RÉSEAU - INDEX

> **Coefficient** : 10% 🟢  
> **Temps de révision estimé** : 8-10h (le plus dense !)  
> **Contenu** : 60 QCM + 20 Questions ouvertes + 20 Exercices subnetting

---

## 🎯 Objectifs du CCP4

Maîtriser les réseaux et services :
- Modèle OSI et TCP/IP (7 couches)
- Protocoles (TCP, UDP, ICMP, ARP, DNS, DHCP, HTTP)
- Ports et services (liste complète à mémoriser)
- Subnetting et CIDR (calculs, masques VLSM)
- Routage (statique, RIP, OSPF, EIGRP)
- VLAN et trunking (802.1Q)
- Commandes Cisco IOS (show, config, routing)
- Sécurité réseau (ACL, NAT, PAT)

---

## 📂 Organisation des ressources

```
CCP4-Services-Reseau/
│
├── Fiche-Revisions/
│   ├── FICHE-02-Ports-Protocoles-Acronymes.md     → Liste ports
│   ├── FICHE-04-Reseau-Subnetting-CIDR.md         → Subnetting
│   └── FICHE-05-Commandes-Cisco-IOS.md            → Cisco
│
├── QCM/
│   ├── Evaluation/
│   │   ├── QCM-Reseau-Partie1-Q1-20-EVALUATION.md
│   │   ├── QCM-Reseau-Partie2-Q21-40-EVALUATION.md
│   │   └── QCM-Reseau-Partie3-Q41-60-EVALUATION.md
│   └── Correction/
│       ├── QCM-Reseau-Partie1-Q1-20-CORRECTION.md
│       ├── QCM-Reseau-Partie2-Q21-40-CORRECTION.md
│       └── QCM-Reseau-Partie3-Q41-60-CORRECTION.md
│
├── Questions-Ouvertes/
│   ├── Evaluation/
│   │   ├── Questions-Ouvertes-Reseau-Partie1-Q1-10-EVALUATION.md
│   │   └── Questions-Ouvertes-Reseau-Partie2-Q11-20-EVALUATION.md
│   └── Correction/
│       ├── Questions-Ouvertes-Reseau-Partie1-Q1-10-CORRECTION.md
│       └── Questions-Ouvertes-Reseau-Partie2-Q11-20-CORRECTION.md
│
└── Exercices-Subnetting/
    ├── Evaluation/
    │   └── Exercices-Subnetting-20-Exercices-EVALUATION.md
    └── Correction/
        └── Exercices-Subnetting-20-Exercices-CORRECTION.md
```

---

## 📖 1. FICHES DE RÉVISION

### FICHE-02-Ports-Protocoles-Acronymes.md
**Chemin** : `Fiche-Revisions/FICHE-02-Ports-Protocoles-Acronymes.md`

**Contenu** : Liste complète des ports à connaître

**Ports essentiels à mémoriser** :
- TCP 20/21 : FTP
- TCP 22 : SSH
- TCP 23 : Telnet
- TCP 25 : SMTP
- TCP 53 / UDP 53 : DNS
- UDP 67/68 : DHCP
- TCP 80 : HTTP
- TCP 110 : POP3
- TCP 143 : IMAP
- TCP 443 : HTTPS
- TCP 3389 : RDP
- TCP 445 : SMB

**Temps** : ⏱️ 30 min

---

### FICHE-04-Reseau-Subnetting-CIDR.md
**Chemin** : `Fiche-Revisions/FICHE-04-Reseau-Subnetting-CIDR.md`

**Contenu** :
- Calculs d'adresses réseau, broadcast, plage
- Masques de sous-réseau (CIDR /8 à /32)
- VLSM (Variable Length Subnet Mask)
- Classes d'adresses (A, B, C, D, E)
- Adresses privées (RFC 1918)
- Méthode rapide de calcul

**Temps** : ⏱️ 60 min

**À faire** :
- [ ] Lire la fiche
- [ ] Mémoriser table CIDR
- [ ] Faire 20 exercices pratiques

---

### FICHE-05-Commandes-Cisco-IOS.md
**Chemin** : `Fiche-Revisions/FICHE-05-Commandes-Cisco-IOS.md`

**Contenu** :
- Modes Cisco (User EXEC, Privileged EXEC, Global Config)
- Configuration interfaces (IP, shutdown/no shutdown)
- Routage (static, default, RIP, OSPF)
- VLAN et trunking
- Show commands (show ip route, show vlan, show int)

**Temps** : ⏱️ 45 min

---

## 📝 2. QCM (60 questions en 3 parties)

### Partie 1 : Q1-20
**Chemin Evaluation** : `QCM/Evaluation/QCM-Reseau-Partie1-Q1-20-EVALUATION.md`  
**Chemin Correction** : `QCM/Correction/QCM-Reseau-Partie1-Q1-20-CORRECTION.md`

**Thèmes** : Modèle OSI, TCP/IP, protocoles de base

---

### Partie 2 : Q21-40
**Chemin Evaluation** : `QCM/Evaluation/QCM-Reseau-Partie2-Q21-40-EVALUATION.md`  
**Chemin Correction** : `QCM/Correction/QCM-Reseau-Partie2-Q21-40-CORRECTION.md`

**Thèmes** : Subnetting, routage, VLAN

---

### Partie 3 : Q41-60
**Chemin Evaluation** : `QCM/Evaluation/QCM-Reseau-Partie3-Q41-60-EVALUATION.md`  
**Chemin Correction** : `QCM/Correction/QCM-Reseau-Partie3-Q41-60-CORRECTION.md`

**Thèmes** : Cisco IOS, ACL, NAT/PAT

---

**À faire** :
- [ ] QCM Partie 1 : Score = ___/20
- [ ] QCM Partie 2 : Score = ___/20
- [ ] QCM Partie 3 : Score = ___/20
- [ ] **Total** : ___/60 (___%)

**Score cible** : 48/60 (80%) minimum

---

## 📋 3. QUESTIONS OUVERTES (20 questions)

### Partie 1 : Q1-10
**Chemin Evaluation** : `Questions-Ouvertes/Evaluation/Questions-Ouvertes-Reseau-Partie1-Q1-10-EVALUATION.md`  
**Chemin Correction** : `Questions-Ouvertes/Correction/Questions-Ouvertes-Reseau-Partie1-Q1-10-CORRECTION.md`

**Thèmes** : Architecture réseau, protocoles

---

### Partie 2 : Q11-20
**Chemin Evaluation** : `Questions-Ouvertes/Evaluation/Questions-Ouvertes-Reseau-Partie2-Q11-20-EVALUATION.md`  
**Chemin Correction** : `Questions-Ouvertes/Correction/Questions-Ouvertes-Reseau-Partie2-Q11-20-CORRECTION.md`

**Thèmes** : Configuration Cisco, ACL, troubleshooting

**Temps recommandé** : ⏱️ 90 min (45 min par partie)

---

## 🧮 4. EXERCICES SUBNETTING (20 exercices)

**Chemin Evaluation** : `Exercices-Subnetting/Evaluation/Exercices-Subnetting-20-Exercices-EVALUATION.md`  
**Chemin Correction** : `Exercices-Subnetting/Correction/Exercices-Subnetting-20-Exercices-CORRECTION.md`

**Contenu** : 20 exercices de calcul subnetting

**À faire** :
- [ ] Faire les 20 exercices
- [ ] Vérifier avec les corrections
- [ ] Refaire ceux ratés

**Temps recommandé** : ⏱️ 60 min

---

## 📊 SUIVI DE PROGRESSION

### Checklist globale

- [ ] 3 fiches lues
- [ ] QCM Partie 1 : ___/20
- [ ] QCM Partie 2 : ___/20
- [ ] QCM Partie 3 : ___/20
- [ ] Questions ouvertes terminées
- [ ] 20 exercices subnetting faits

---

## 💡 AIDE-MÉMOIRE SUBNETTING

### Table CIDR rapide

| CIDR | Masque | Nombre d'hôtes | Usage |
|------|--------|----------------|-------|
| /30 | 255.255.255.252 | 2 | Lien point-à-point |
| /29 | 255.255.255.248 | 6 | Très petit réseau |
| /28 | 255.255.255.240 | 14 | Petit réseau |
| /27 | 255.255.255.224 | 30 | Petit réseau |
| /26 | 255.255.255.192 | 62 | Moyen réseau |
| /25 | 255.255.255.128 | 126 | Moyen réseau |
| /24 | 255.255.255.0 | 254 | Réseau standard (classe C) |
| /16 | 255.255.0.0 | 65534 | Réseau classe B |
| /8 | 255.0.0.0 | 16 millions | Réseau classe A |

### Modèle OSI (à connaître par cœur)

1. **Physique** : Câbles, signaux électriques
2. **Liaison de données** : MAC, Switch, Ethernet
3. **Réseau** : IP, Routeur, ICMP
4. **Transport** : TCP, UDP, ports
5. **Session** : Établissement de sessions
6. **Présentation** : Chiffrement, compression
7. **Application** : HTTP, FTP, SMTP, DNS

**Mnémotechnique** : **P**lease **D**o **N**ot **T**hrow **S**ausage **P**izza **A**way

---

## 🔗 NAVIGATION

- 📖 [README.md](../README.md)
- 📚 [INDEX-GENERAL.md](../INDEX-GENERAL.md)
- 📅 [PLANNING-REVISION-5-JOURS.md](../PLANNING-REVISION-5-JOURS.md)

---

**Commence maintenant** : [Fiche-Revisions/FICHE-02-Ports-Protocoles-Acronymes.md](Fiche-Revisions/FICHE-02-Ports-Protocoles-Acronymes.md)

**Bon courage ! 🌐💪**
