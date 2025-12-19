# 📚 CCP7 - SÉCURISATION ACCÈS INTERNET - INDEX

> **Coefficient** : 10% 🟢  
> **Temps de révision estimé** : 3-4h  
> **Contenu** : 25 QCM + 8 Questions ouvertes

---

## 🎯 Objectifs du CCP7

Maîtriser la sécurité réseau et les pare-feux :
- Architecture de sécurité réseau (DMZ, zones)
- Pare-feux (stateful/stateless, règles)
- pfSense (configuration, interfaces, NAT, règles)
- Proxy (forward, reverse, filtrage)
- VPN (IPsec, OpenVPN, types de VPN)
- Filtrage URL et contrôle d'accès
- IDS/IPS (détection et prévention d'intrusions)
- Concepts de sécurité (CIA, defense in depth)

---

## 📂 Organisation des ressources

```
CCP7-Securisation-Acces-Internet/
│
├── Fiche-Revisions/
│   └── FICHE-CCP7-Securite-Reseau-pfSense.md      → Théorie complète
│
├── QCM/
│   ├── Evaluation/
│   │   └── QCM-CCP7-Securite-Reseau-25Q-EVALUATION.md
│   └── Correction/
│       └── QCM-CCP7-Securite-Reseau-25Q-CORRECTION.md
│
└── Questions-Ouvertes/
    ├── Evaluation/
    │   └── QUESTIONS-OUVERTES-CCP7-Securite-8Q-EVALUATION.md
    └── Correction/
        └── QUESTIONS-OUVERTES-CCP7-Securite-8Q-CORRECTION.md
```

---

## 📖 1. FICHE DE RÉVISION

### FICHE-CCP7-Securite-Reseau-pfSense.md
**Chemin** : `Fiche-Revisions/FICHE-CCP7-Securite-Reseau-pfSense.md`

**Contenu** :
- 🔒 Principes de sécurité (CIA : Confidentialité, Intégrité, Disponibilité)
- 🛡️ Architecture réseau sécurisée (DMZ, zones LAN/WAN/DMZ)
- 🔥 Pare-feux (stateful vs stateless, règles allow/deny)
- 🚪 pfSense (configuration, interfaces, alias, règles)
- 🔄 NAT et PAT (types, configuration)
- 🌐 Proxy (forward vs reverse, Squid)
- 🔐 VPN (site-to-site, remote access, IPsec, OpenVPN)
- 👁️ IDS/IPS (Snort, Suricata, signatures)
- 📜 Filtrage URL et contrôle parental

**Temps de lecture** : ⏱️ 45-60 min

**À faire** :
- [ ] Lire la fiche complète
- [ ] Comprendre architecture DMZ
- [ ] Apprendre ordre d'évaluation des règles pfSense
- [ ] Différencier IDS vs IPS

---

## 📝 2. QCM (25 questions)

### QCM-CCP7-Securite-Reseau-25Q-EVALUATION.md
**Chemin** : `QCM/Evaluation/QCM-CCP7-Securite-Reseau-25Q-EVALUATION.md`

**Contenu** : 25 questions QCM

**À faire** :
- [ ] Faire le QCM sans aide
- [ ] Chronométrer (30 min)
- [ ] Noter tes réponses

**Thèmes couverts** :
- Architecture sécurité (7 questions)
- pfSense et pare-feux (8 questions)
- VPN (5 questions)
- Proxy et IDS/IPS (5 questions)

---

### QCM-CCP7-Securite-Reseau-25Q-CORRECTION.md
**Chemin** : `QCM/Correction/QCM-CCP7-Securite-Reseau-25Q-CORRECTION.md`

**Contenu** : Corrections détaillées

**À faire** :
- [ ] Corriger : Score = ___/25
- [ ] Comprendre différence IDS vs IPS
- [ ] Revoir règles pfSense

**Score cible** : 20/25 (80%) minimum

---

## 📋 3. QUESTIONS OUVERTES (8 questions)

### QUESTIONS-OUVERTES-CCP7-Securite-8Q-EVALUATION.md
**Chemin** : `Questions-Ouvertes/Evaluation/QUESTIONS-OUVERTES-CCP7-Securite-8Q-EVALUATION.md`

**Contenu** : 8 questions détaillées

**À faire** :
- [ ] Essayer de répondre seul
- [ ] Dessiner des schémas d'architecture
- [ ] Expliquer les concepts de sécurité

**Types de questions** :
- Configuration pfSense (règles, NAT)
- Architecture DMZ
- Configuration VPN
- Mise en place IDS/IPS
- Scénarios de sécurité

**Temps recommandé** : ⏱️ 45 min

---

### QUESTIONS-OUVERTES-CCP7-Securite-8Q-CORRECTION.md
**Chemin** : `Questions-Ouvertes/Correction/QUESTIONS-OUVERTES-CCP7-Securite-8Q-CORRECTION.md`

**Contenu** : Corrections détaillées avec schémas

---

## 📊 SUIVI DE PROGRESSION

### Checklist globale

- [ ] Fiche lue
- [ ] QCM terminé : Score = ___/25 (___%)
- [ ] Questions ouvertes terminées
- [ ] Architecture DMZ comprise
- [ ] pfSense maîtrisé

---

## 💡 POINTS CLÉS À RETENIR

### Architecture en zones

```
INTERNET
    |
  [WAN]
    |
[FIREWALL]
    |
    +---> [DMZ] -----> Serveurs publics (Web, Mail)
    |
    +---> [LAN] -----> Réseau interne
```

### Règles pare-feu pfSense

**Ordre d'évaluation** :
1. Top → Bottom (première règle qui match gagne)
2. Action : Pass (autoriser) ou Block (bloquer)
3. Interface : Appliquer sur interface entrante
4. Source et Destination
5. Protocole et Port

**Règle par défaut** : Block all (tout bloquer)

### Types de VPN

| Type | Usage | Protocole | Exemple |
|------|-------|-----------|---------|
| **Site-to-Site** | Relier 2 sites | IPsec | Siège ↔ Succursale |
| **Remote Access** | Utilisateur distant | OpenVPN, L2TP | Télétravail |
| **Client-to-Site** | Accès VPN client | OpenVPN | VPN entreprise |

### IDS vs IPS

| Aspect | IDS | IPS |
|--------|-----|-----|
| **Fonction** | Détecte | Détecte + Bloque |
| **Mode** | Passif (écoute) | Actif (inline) |
| **Action** | Alerte | Bloque attaque |
| **Risque** | Aucun | Faux positifs |

### Proxy Forward vs Reverse

| Type | Direction | Usage |
|------|-----------|-------|
| **Forward** | LAN → Internet | Filtrage sortant, cache |
| **Reverse** | Internet → LAN | Protection serveurs web |

---

## 🔗 NAVIGATION

- 📖 [README.md](../README.md)
- 📚 [INDEX-GENERAL.md](../INDEX-GENERAL.md)
- 📅 [PLANNING-REVISION-5-JOURS.md](../PLANNING-REVISION-5-JOURS.md)

---

**Commence maintenant** : [Fiche-Revisions/FICHE-CCP7-Securite-Reseau-pfSense.md](Fiche-Revisions/FICHE-CCP7-Securite-Reseau-pfSense.md)

**Bon courage ! 🔐💪**
