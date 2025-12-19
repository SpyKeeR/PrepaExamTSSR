# 🌐 FICHE DE RÉVISION - SUBNETTING & CIDR
## Méthode RAPIDE pour l'examen TSSR

> **⚠️ CRITIQUE** : Le subnetting tombe à TOUS les examens ! Maîtrise cette méthode = points faciles

---

## 📐 MÉTHODE ULTRA-RAPIDE DE SUBNETTING

### Étape 1 : Mémoriser les puissances de 2

| Exposant | Valeur | Hôtes utilisables |
|----------|--------|-------------------|
| 2^1 | 2 | 0 |
| 2^2 | 4 | 2 |
| 2^3 | 8 | 6 |
| 2^4 | 16 | 14 |
| 2^5 | 32 | 30 |
| 2^6 | 64 | 62 |
| 2^7 | 128 | 126 |
| 2^8 | 256 | 254 |
| 2^9 | 512 | 510 |
| 2^10 | 1024 | 1022 |
| 2^11 | 2048 | 2046 |
| 2^12 | 4096 | 4094 |

**Formule** : Hôtes utilisables = 2^n - 2 (enlever adresse réseau et broadcast)

---

### Étape 2 : Table de correspondance CIDR

| CIDR | Masque | Bloc | Hôtes | Binaire (derniers octets) |
|------|--------|------|-------|---------------------------|
| **/8** | 255.0.0.0 | 16 777 216 | 16 777 214 | 00000000.00000000.00000000 |
| **/9** | 255.128.0.0 | 8 388 608 | 8 388 606 | 10000000.00000000.00000000 |
| **/10** | 255.192.0.0 | 4 194 304 | 4 194 302 | 11000000.00000000.00000000 |
| **/11** | 255.224.0.0 | 2 097 152 | 2 097 150 | 11100000.00000000.00000000 |
| **/12** | 255.240.0.0 | 1 048 576 | 1 048 574 | 11110000.00000000.00000000 |
| **/13** | 255.248.0.0 | 524 288 | 524 286 | 11111000.00000000.00000000 |
| **/14** | 255.252.0.0 | 262 144 | 262 142 | 11111100.00000000.00000000 |
| **/15** | 255.254.0.0 | 131 072 | 131 070 | 11111110.00000000.00000000 |
| **/16** | 255.255.0.0 | 65 536 | 65 534 | 11111111.00000000.00000000 |
| **/17** | 255.255.128.0 | 32 768 | 32 766 | 11111111.10000000.00000000 |
| **/18** | 255.255.192.0 | 16 384 | 16 382 | 11111111.11000000.00000000 |
| **/19** | 255.255.224.0 | 8 192 | 8 190 | 11111111.11100000.00000000 |
| **/20** | 255.255.240.0 | 4 096 | 4 094 | 11111111.11110000.00000000 |
| **/21** | 255.255.248.0 | 2 048 | 2 046 | 11111111.11111000.00000000 |
| **/22** | 255.255.252.0 | 1 024 | 1 022 | 11111111.11111100.00000000 |
| **/23** | 255.255.254.0 | 512 | 510 | 11111111.11111110.00000000 |
| **/24** | 255.255.255.0 | 256 | 254 | 11111111.11111111.00000000 |
| **/25** | 255.255.255.128 | 128 | 126 | 11111111.11111111.10000000 |
| **/26** | 255.255.255.192 | 64 | 62 | 11111111.11111111.11000000 |
| **/27** | 255.255.255.224 | 32 | 30 | 11111111.11111111.11100000 |
| **/28** | 255.255.255.240 | 16 | 14 | 11111111.11111111.11110000 |
| **/29** | 255.255.255.248 | 8 | 6 | 11111111.11111111.11111000 |
| **/30** | 255.255.255.252 | 4 | 2 | 11111111.11111111.11111100 |
| **/31** | 255.255.255.254 | 2 | 2* | 11111111.11111111.11111110 |
| **/32** | 255.255.255.255 | 1 | 0 | 11111111.11111111.11111111 |

**Note /31** : Cas spécial (RFC 3021) pour liens point-à-point, pas d'adresse réseau ni broadcast

---

## 🎯 MÉTHODE RAPIDE : "LE BON BLOC"

### Principe
Pour trouver l'adresse réseau avec un CIDR non classique (ex: /27, /17, /21), il faut trouver le **"bloc"** dans lequel se trouve l'IP.

### Technique en 3 étapes

#### ÉTAPE 1 : Identifier l'octet concerné
- /8 à /16 → 2ème octet
- /16 à /24 → 3ème octet
- /24 à /32 → 4ème octet

#### ÉTAPE 2 : Calculer la taille du bloc
**Formule rapide** : Bloc = 256 - valeur du masque dans l'octet concerné

#### ÉTAPE 3 : Trouver le multiple
Diviser la valeur de l'octet par la taille du bloc, prendre la partie entière, multiplier par le bloc.

---

## 💡 EXEMPLES DÉTAILLÉS

### Exemple 1 : IP = 192.168.1.75/27

**Question** : Adresse réseau, première IP, dernière IP, broadcast ?

**Résolution** :

1. **/27** → Regarde le tableau : Masque = **255.255.255.224**, Bloc = **32**

2. **Octet concerné** : Le 4ème (car /24 à /32)

3. **Valeur de l'octet** : 75

4. **Trouver le bloc** :
   - 75 ÷ 32 = 2,34... → On prend **2**
   - 2 × 32 = **64** → Début du bloc

5. **Résultats** :
   - **Adresse réseau** : 192.168.1.**64**
   - **Première IP utilisable** : 192.168.1.**65**
   - **Dernière IP utilisable** : 192.168.1.**94** (64 + 32 - 2)
   - **Broadcast** : 192.168.1.**95** (64 + 32 - 1)
   - **Plage** : 192.168.1.64/27 (64 à 95)

**Vérification** :
- Bloc de 32 adresses : 64, 65, ..., 94, 95 ✅
- 75 est bien entre 64 et 95 ✅

---

### Exemple 2 : IP = 172.16.135.200/17

**Question** : Adresse réseau, broadcast ?

**Résolution** :

1. **/17** → Masque = **255.255.128.0**, Bloc = **128**

2. **Octet concerné** : Le 3ème (car /16 à /24)

3. **Valeur de l'octet** : 135

4. **Trouver le bloc** :
   - 135 ÷ 128 = 1,05... → On prend **1**
   - 1 × 128 = **128** → Début du bloc

5. **Résultats** :
   - **Adresse réseau** : 172.16.**128**.0
   - **Première IP** : 172.16.**128**.1
   - **Dernière IP** : 172.16.**255**.254
   - **Broadcast** : 172.16.**255**.255
   - **Plage** : 172.16.128.0/17 (128.0 à 255.255)

**Vérification** :
- Bloc de 128 au 3ème octet : 128 à 255 ✅
- 135 est bien entre 128 et 255 ✅

---

### Exemple 3 : IP = 10.45.78.92/21

**Question** : Adresse réseau, nombre d'hôtes, broadcast ?

**Résolution** :

1. **/21** → Masque = **255.255.248.0**, Bloc = **8**

2. **Octet concerné** : Le 3ème

3. **Valeur de l'octet** : 78

4. **Trouver le bloc** :
   - 78 ÷ 8 = 9,75 → On prend **9**
   - 9 × 8 = **72** → Début du bloc

5. **Résultats** :
   - **Adresse réseau** : 10.45.**72**.0
   - **Première IP** : 10.45.**72**.1
   - **Dernière IP** : 10.45.**79**.254
   - **Broadcast** : 10.45.**79**.255
   - **Plage** : 10.45.72.0/21
   - **Hôtes utilisables** : 2^11 - 2 = **2046**

**Vérification** :
- Bloc de 8 : 72, 73, 74, 75, 76, 77, 78, 79 ✅
- 78 est bien dedans ✅

---

### Exemple 4 : IP = 192.16.5.133/29

**Question EXAMEN** : Combien de bits pour la partie réseau ? Combien d'hôtes ?

**Résolution** :

1. **/29** → 29 bits pour le réseau, 3 bits pour les hôtes

2. **Bits réseau** : **29 bits**

3. **Bits hôtes** : 32 - 29 = **3 bits**

4. **Hôtes** : 2^3 = 8 adresses, donc **6 hôtes utilisables** (8 - 2)

5. **Bloc** : 256 - 248 = **8**

6. **Trouver le réseau** :
   - 133 ÷ 8 = 16,625 → On prend **16**
   - 16 × 8 = **128**

7. **Résultats** :
   - **Adresse réseau** : 192.16.5.**128**
   - **Première IP** : 192.16.5.**129**
   - **Dernière IP** : 192.16.5.**134**
   - **Broadcast** : 192.16.5.**135**
   - **Hôtes utilisables** : **6**

---

### Exemple 5 : IP = 192.168.1.17/28

**Question EXAMEN** : Adresse réseau et broadcast ?

**Résolution** :

1. **/28** → Masque = **255.255.255.240**, Bloc = **16**

2. **Trouver le bloc** :
   - 17 ÷ 16 = 1,0625 → **1**
   - 1 × 16 = **16**

3. **Résultats** :
   - **Adresse réseau** : 192.168.1.**16**
   - **Broadcast** : 192.168.1.**31** (16 + 16 - 1)

---

## 🔢 TECHNIQUE DU MASQUE INVERSÉ (WILDCARD)

### Principe
Masque générique (wildcard) = Inverse du masque de sous-réseau

**Formule** : Wildcard = 255.255.255.255 - Masque

### Exemples

| CIDR | Masque | Wildcard |
|------|--------|----------|
| /24 | 255.255.255.0 | 0.0.0.255 |
| /27 | 255.255.255.224 | 0.0.0.31 |
| /29 | 255.255.255.248 | 0.0.0.7 |
| /17 | 255.255.128.0 | 0.0.127.255 |
| /21 | 255.255.248.0 | 0.0.7.255 |

**Usage** : ACL Cisco, OSPF, configurations réseau

---

## 📊 CLASSES D'ADRESSES IPv4 (Rappel)

| Classe | Premier octet | CIDR par défaut | Masque par défaut | Plage | Usage |
|--------|---------------|-----------------|-------------------|-------|-------|
| **A** | 1-126 | /8 | 255.0.0.0 | 1.0.0.0 - 126.255.255.255 | Très grands réseaux |
| **B** | 128-191 | /16 | 255.255.0.0 | 128.0.0.0 - 191.255.255.255 | Moyens réseaux |
| **C** | 192-223 | /24 | 255.255.255.0 | 192.0.0.0 - 223.255.255.255 | Petits réseaux |
| **D** | 224-239 | - | - | 224.0.0.0 - 239.255.255.255 | Multicast |
| **E** | 240-255 | - | - | 240.0.0.0 - 255.255.255.255 | Réservé/Expérimental |

**Adresses spéciales** :
- **127.0.0.0/8** : Loopback (localhost)
- **169.254.0.0/16** : APIPA (auto-configuration)
- **0.0.0.0** : Route par défaut

---

## 🏠 ADRESSES PRIVÉES (RFC 1918)

| Classe | Plage | CIDR | Masque | Nombre de réseaux |
|--------|-------|------|--------|-------------------|
| **A** | 10.0.0.0 - 10.255.255.255 | /8 | 255.0.0.0 | 1 réseau de 16M hôtes |
| **B** | 172.16.0.0 - 172.31.255.255 | /12 | 255.240.0.0 | 16 réseaux de 65K hôtes |
| **C** | 192.168.0.0 - 192.168.255.255 | /16 | 255.255.0.0 | 256 réseaux de 254 hôtes |

**Important** : Ces adresses ne sont PAS routables sur Internet (NAT requis)

---

## 🎯 VLSM (Variable Length Subnet Mask)

### Principe
Découper un réseau en sous-réseaux de tailles différentes selon les besoins.

### Méthode

1. **Lister les besoins** par ordre décroissant
2. **Attribuer le CIDR** adapté à chaque besoin
3. **Commencer par le plus grand** sous-réseau
4. **Continuer séquentiellement**

### Exemple : Découper 192.168.1.0/24

**Besoins** :
- Réseau A : 100 hôtes
- Réseau B : 50 hôtes
- Réseau C : 20 hôtes
- Réseau D : 10 hôtes

**Résolution** :

1. **Réseau A (100 hôtes)** :
   - Besoin : 2^n ≥ 102 (100 + 2) → n = 7 bits hôtes
   - CIDR : /25 (126 hôtes)
   - **192.168.1.0/25** (0 à 127)

2. **Réseau B (50 hôtes)** :
   - Besoin : 2^n ≥ 52 → n = 6 bits
   - CIDR : /26 (62 hôtes)
   - **192.168.1.128/26** (128 à 191)

3. **Réseau C (20 hôtes)** :
   - Besoin : 2^n ≥ 22 → n = 5 bits
   - CIDR : /27 (30 hôtes)
   - **192.168.1.192/27** (192 à 223)

4. **Réseau D (10 hôtes)** :
   - Besoin : 2^n ≥ 12 → n = 4 bits
   - CIDR : /28 (14 hôtes)
   - **192.168.1.224/28** (224 à 239)

---

## 🧮 FORMULES À CONNAÎTRE PAR CŒUR

### Nombre de sous-réseaux
**Formule** : 2^(bits empruntés)

**Exemple** : Réseau /24 → /27 = 3 bits empruntés → 2^3 = **8 sous-réseaux**

### Nombre d'hôtes par sous-réseau
**Formule** : 2^(bits hôtes) - 2

**Exemple** : /27 = 5 bits hôtes → 2^5 - 2 = **30 hôtes**

### Taille du bloc
**Formule** : 256 - valeur du masque dans l'octet

**Exemple** : /27 → masque = 224 → 256 - 224 = **32**

---

## 💡 ASTUCES POUR L'EXAMEN

### 1. Reconnaître le type de question

**Type A** : "Donnez l'adresse réseau de X.X.X.X/Y"
→ Utilise la méthode du bloc

**Type B** : "Combien d'hôtes dans X.X.X.X/Y ?"
→ 2^(32-Y) - 2

**Type C** : "Découpez en N sous-réseaux"
→ VLSM, commence par le plus grand

### 2. Vérifications rapides

✅ **L'IP est-elle dans le bon bloc ?**
- 192.168.1.75/27 → Réseau 192.168.1.64
- 75 est entre 64 et 95 ? OUI ✅

✅ **Le broadcast est-il correct ?**
- Broadcast = Début du PROCHAIN bloc - 1
- Réseau 64, bloc de 32 → Prochain = 96
- Broadcast = 96 - 1 = **95** ✅

✅ **Nombre d'hôtes cohérent ?**
- /27 = 30 hôtes
- Bloc de 32 - 2 (réseau + broadcast) = 30 ✅

### 3. Pièges à éviter

❌ **Ne pas oublier -2** pour les hôtes utilisables
❌ **Confondre masque et wildcard**
❌ **Oublier que 0 est une adresse valide** (ex: 192.168.1.0/24)
❌ **Confondre CIDR et nombre de bits hôtes**

---

## 🎓 MÉMO ULTRA-RAPIDE

```
ÉTAPES SUBNETTING :
1. CIDR → Masque (table de correspondance)
2. Calculer BLOC = 256 - valeur masque
3. Diviser IP_octet / BLOC → partie entière × BLOC
4. Réseau = Résultat au bon octet
5. Broadcast = Réseau + BLOC - 1
6. Première IP = Réseau + 1
7. Dernière IP = Broadcast - 1

FORMULES CRITIQUES :
✅ Hôtes = 2^(bits hôtes) - 2
✅ Sous-réseaux = 2^(bits empruntés)
✅ Bloc = 256 - valeur masque

CIDR COURANTS :
/24 = 256 (254 hôtes)
/25 = 128 (126 hôtes)
/26 = 64 (62 hôtes)
/27 = 32 (30 hôtes)
/28 = 16 (14 hôtes)
/29 = 8 (6 hôtes)
/30 = 4 (2 hôtes) → Liens point-à-point
```

---

**🎓 Maîtrise cette fiche = Tu éclates le subnetting à l'examen ! 💪**

**📝 Passe maintenant aux 20 exercices pratiques pour t'entraîner !**
