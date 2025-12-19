# CORRECTIONS - EXERCICES SUBNETTING

**20 exercices progressifs - Du niveau débutant à expert**  
**Barème : 100 points (5 points par exercice)**

---

## 🟢 NIVEAU 1 - DÉBUTANT (Exercices 1-5)

### ✔️ Exercice 1 : 192.168.1.50/24

**Résolution** :
- /24 = 255.255.255.0, Bloc = 256
- Octet concerné : 4ème
- 50 ÷ 256 = 0 → 0 × 256 = **0**

**Réponses** :
- a) Adresse réseau : **192.168.1.0**
- b) Broadcast : **192.168.1.255**
- c) Première IP : **192.168.1.1**
- d) Dernière IP : **192.168.1.254**
- e) Hôtes : **254** (2^8 - 2)

**Points** : 5/5

---

### ✔️ Exercice 2 : 172.16.10.100/16

**Résolution** :
- /16 = 255.255.0.0
- Classe B standard

**Réponses** :
- a) Réseau : **172.16.0.0**
- b) Broadcast : **172.16.255.255**
- c) Hôtes : **65 534** (2^16 - 2)
- d) Masque : **255.255.0.0**

**Points** : 5/5

---

### ✔️ Exercice 3 : 10.45.78.92/8

**Réponses** :
- a) Réseau : **10.0.0.0**
- b) Broadcast : **10.255.255.255**
- c) Classe : **A**
- d) Privée : **OUI** (10.0.0.0/8)

**Points** : 5/5

---

### ✔️ Exercice 4 : 192.168.5.200/25

**Résolution** :
- /25 = 255.255.255.128, Bloc = 128
- 200 ÷ 128 = 1 → 1 × 128 = **128**

**Réponses** :
- a) Réseau : **192.168.5.128**
- b) Broadcast : **192.168.5.255** (128 + 128 - 1)
- c) Première IP : **192.168.5.129**
- d) Dernière IP : **192.168.5.254**
- e) Hôtes : **126** (2^7 - 2)

**Points** : 5/5

---

### ✔️ Exercice 5 : 172.20.100.75/26

**Résolution** :
- /26 = 255.255.255.192, Bloc = 64
- 75 ÷ 64 = 1 → 1 × 64 = **64**

**Réponses** :
- a) Masque : **255.255.255.192**
- b) Réseau : **172.20.100.64**
- c) Broadcast : **172.20.100.127**
- d) Plage : **172.20.100.65 - 172.20.100.126**

**Points** : 5/5

---

## 🟡 NIVEAU 2 - INTERMÉDIAIRE (Exercices 6-10)

### ✔️ Exercice 6 : 192.168.1.75/27

**Résolution** :
- /27 → 27 bits réseau, 5 bits hôtes
- Bloc = 256 - 224 = 32
- 75 ÷ 32 = 2 → 2 × 32 = **64**

**Réponses** :
- a) Bits réseau : **27**
- b) Bits hôtes : **5** (32 - 27)
- c) Réseau : **192.168.1.64**
- d) Broadcast : **192.168.1.95**
- e) Hôtes : **30** (2^5 - 2)

**Points** : 5/5

---

### ✔️ Exercice 7 : 192.16.5.133/29

**Résolution** :
- /29 = 29 bits réseau, 3 bits hôtes
- Bloc = 256 - 248 = 8
- 133 ÷ 8 = 16 → 16 × 8 = **128**

**Réponses** :
- a) Bits réseau : **29 bits**
- b) Bits hôtes : **3 bits**
- c) Machines : **6** (2^3 - 2)
- d) Réseau : **192.16.5.128**
- e) Broadcast : **192.16.5.135**

**Points** : 5/5

---

### ✔️ Exercice 8 : 192.168.1.17/28

**Résolution** :
- /28, Bloc = 16
- 17 ÷ 16 = 1 → 1 × 16 = **16**

**Réponses** :
- a) Réseau : **192.168.1.16**
- b) Broadcast : **192.168.1.31**
- c) Première IP : **192.168.1.17**
- d) Dernière IP : **192.168.1.30**

**Points** : 5/5

---

### ✔️ Exercice 9 : 172.30.45.180/23

**Résolution** :
- /23 = 255.255.254.0, Bloc = 2 (au 3ème octet)
- 45 ÷ 2 = 22 → 22 × 2 = **44**

**Réponses** :
- a) Masque : **255.255.254.0**
- b) Réseau : **172.30.44.0**
- c) Broadcast : **172.30.45.255**
- d) Hôtes : **510** (2^9 - 2)

**Points** : 5/5

---

### ✔️ Exercice 10 : 10.120.88.200/22

**Résolution** :
- /22 = 255.255.252.0, Bloc = 4
- 88 ÷ 4 = 22 → 22 × 4 = **88**

**Réponses** :
- a) Réseau : **10.120.88.0**
- b) Broadcast : **10.120.91.255**
- c) Hôtes : **1022** (2^10 - 2)
- d) Classe : **A**

**Points** : 5/5

---

## 🔴 NIVEAU 3 - AVANCÉ (Exercices 11-15)

### ✔️ Exercice 11 : 172.16.135.200/17

**Résolution** :
- /17 = 255.255.128.0, Bloc = 128
- 135 ÷ 128 = 1 → 1 × 128 = **128**

**Réponses** :
- a) Masque : **255.255.128.0**
- b) Réseau : **172.16.128.0**
- c) Broadcast : **172.16.255.255**
- d) Hôtes : **32 766** (2^15 - 2)
- e) Plage : **172.16.128.0 - 172.16.255.255**

**Points** : 5/5

---

### ✔️ Exercice 12 : 10.45.78.92/21

**Résolution** :
- /21 = 255.255.248.0, Bloc = 8
- 78 ÷ 8 = 9 → 9 × 8 = **72**

**Réponses** :
- a) Masque : **255.255.248.0**
- b) Réseau : **10.45.72.0**
- c) Broadcast : **10.45.79.255**
- d) Hôtes : **2046** (2^11 - 2)
- e) Wildcard : **0.0.7.255** (255.255.255.255 - 255.255.248.0)

**Points** : 5/5

---

### ✔️ Exercice 13 : 192.168.50.75/19

**Résolution** :
- /19 = 255.255.224.0, Bloc = 32
- 50 ÷ 32 = 1 → 1 × 32 = **32**

**Réponses** :
- a) Réseau : **192.168.32.0**
- b) Broadcast : **192.168.63.255**
- c) Première IP : **192.168.32.1**
- d) Dernière IP : **192.168.63.254**
- e) Sous-réseaux /24 : **32** (2^(24-19) = 2^5)

**Points** : 5/5

---

### ✔️ Exercice 14 : 172.25.100.250/20

**Résolution** :
- /20 = 255.255.240.0, Bloc = 16
- 100 ÷ 16 = 6 → 6 × 16 = **96**

**Réponses** :
- a) Masque : **255.255.240.0**
- b) Réseau : **172.25.96.0**
- c) Broadcast : **172.25.111.255**
- d) Hôtes : **4094** (2^12 - 2)
- e) 172.25.110.50 : **OUI** (110 est entre 96 et 111)

**Points** : 5/5

---

### ✔️ Exercice 15 : 192.168.128.45/18

**Résolution** :
- /18 = 255.255.192.0, Bloc = 64
- 128 ÷ 64 = 2 → 2 × 64 = **128**

**Réponses** :
- a) Réseau : **192.168.128.0**
- b) Broadcast : **192.168.191.255**
- c) Réseaux /24 : **64** (2^6)
- d) 10ème /24 : **192.168.137.0/24** (128 + 9 = 137)

**Points** : 5/5

---

## ⚫ NIVEAU 4 - EXPERT (Exercices 16-20)

### ✔️ Exercice 16 : VLSM 192.168.1.0/24

**Résolution** :

1. **Site A (100 hôtes)** :
   - Besoin : 2^n ≥ 102 → n = 7 bits → /25
   - **192.168.1.0/25** (0 à 127)

2. **Site B (50 hôtes)** :
   - Besoin : 2^n ≥ 52 → n = 6 bits → /26
   - **192.168.1.128/26** (128 à 191)

3. **Site C (25 hôtes)** :
   - Besoin : 2^n ≥ 27 → n = 5 bits → /27
   - **192.168.1.192/27** (192 à 223)

4. **Site D (10 hôtes)** :
   - Besoin : 2^n ≥ 12 → n = 4 bits → /28
   - **192.168.1.224/28** (224 à 239)

5. **Liens point-à-point (2 hôtes)** :
   - /30 (4 adresses, 2 utilisables)
   - **Lien 1** : 192.168.1.240/30 (240 à 243)
   - **Lien 2** : 192.168.1.244/30 (244 à 247)
   - **Lien 3** : 192.168.1.248/30 (248 à 251)

**Points** : 5/5

---

### ✔️ Exercice 17 : VLSM 10.0.0.0/16

**Résolution** :

**a) 2000 hôtes** :
- 2^n ≥ 2002 → n = 11 bits → **/21** (2046 hôtes)

**b) 500 hôtes** :
- 2^n ≥ 502 → n = 9 bits → **/23** (510 hôtes)

**c) 50 hôtes** :
- 2^n ≥ 52 → n = 6 bits → **/26** (62 hôtes)

**d) Calcul espace total** :
- 10 × /21 = 10 × 2048 = 20 480 adresses
- 20 × /23 = 20 × 512 = 10 240 adresses
- 50 × /26 = 50 × 64 = 3 200 adresses
- **Total** : 33 920 adresses
- **Disponible en /16** : 65 536 adresses
- **Réponse** : **OUI, ça rentre** (reste ~31 616 adresses)

**Points** : 5/5

---

### ✔️ Exercice 18 : Supernetting

**Résolution** :

**Analyse** :
- 192.168.0.0/24 = 192.168.0.0
- 192.168.1.0/24 = 192.168.1.0
- 192.168.2.0/24 = 192.168.2.0
- 192.168.3.0/24 = 192.168.3.0

**Binaire 3ème octet** :
- 0 = 00000000
- 1 = 00000001
- 2 = 00000010
- 3 = 00000011

**Bits communs** : 6 premiers bits (000000)

**Réponses** :
- a) Route agrégée : **192.168.0.0/22**
- b) Masque : **255.255.252.0**
- c) Adresses : **1024** (4 × 256)

**Points** : 5/5

---

### ✔️ Exercice 19 : Problème avancé VLAN

**Résolution** :

**a) CIDR originel** :
- 1022 hôtes utilisables → 2^10 - 2 = 1022
- Donc 2^10 adresses total = 1024
- **192.168.0.0/22** (0.0 à 3.255)

**b) Découpage** :
- **Segment 1** (254) : 192.168.0.0/24 (CIDR /24)
- **Segment 2** (254) : 192.168.1.0/24 (CIDR /24)
- **Segment 3** (254) : 192.168.2.0/24 (CIDR /24)
- **Segment 4** (126) : 192.168.3.0/25 (CIDR /25)
- **Segment 5** (62) : 192.168.3.128/26 (CIDR /26)
- **Segment 6** (62) : 192.168.3.192/26 (CIDR /26)

**c) Plages** :
- Seg 1 : 192.168.0.1 - 192.168.0.254
- Seg 2 : 192.168.1.1 - 192.168.1.254
- Seg 3 : 192.168.2.1 - 192.168.2.254
- Seg 4 : 192.168.3.1 - 192.168.3.126
- Seg 5 : 192.168.3.129 - 192.168.3.190
- Seg 6 : 192.168.3.193 - 192.168.3.254

**d) Dernière IP** :
- Segment 1 : **192.168.0.255** (broadcast)
- Segment 2 : **192.168.1.255** (broadcast)
- Segment 3 : **192.168.2.255** (broadcast)
- Segment 4 : **192.168.3.127** (broadcast)
- Segment 5 : **192.168.3.191** (broadcast)
- Segment 6 : **192.168.3.255** (broadcast)

**Points** : 5/5

---

### ✔️ Exercice 20 : Routage et subnetting

**Réponses** :

**a) Route choisie** : **10.10.0.0/22 via OSPF**

**Justification** : 
- Principe du **masque le plus long** (Longest Prefix Match)
- /22 est plus spécifique que /16
- Le routeur choisit TOUJOURS la route avec le masque le plus long

**b) Plage /22** :
- /22 = Bloc de 4 au 3ème octet
- **10.10.0.0 - 10.10.3.255**

**c) Hôtes /22** :
- 2^10 - 2 = **1022 hôtes**

**d) Préférence** :
- Masque plus long = route plus spécifique
- Plus une route est spécifique, plus elle est prioritaire
- /22 > /16 en spécificité

**Points** : 5/5

---

## 📊 BARÈME TOTAL : 100 POINTS

**Score total** : /100 points (5 points par exercice)

| Score | Niveau |
|-------|--------|
| 90-100 | ⭐⭐⭐ Expert - Prêt pour l'examen ! |
| 75-89 | ⭐⭐ Très bon - Quelques révisions |
| 60-74 | ⭐ Bien - Revoir les exercices ratés |
| 40-59 | ⚠️ Moyen - Relire la fiche de révision |
| 0-39 | 🔴 Insuffisant - Refaire tous les exercices |

---

## 🎯 MÉTHODE DE CALCUL RAPIDE

### Formule universelle :

```
1. Identifier l'octet concerné par le masque
2. Calculer le BLOC : 256 - valeur_masque_octet
3. Diviser l'octet IP par le BLOC
4. Multiplier le résultat (partie entière) par le BLOC = Adresse réseau
5. Broadcast = (Adresse réseau + BLOC - 1)
6. Première IP = Adresse réseau + 1
7. Dernière IP = Broadcast - 1
```

### Tableau des masques courants :

| CIDR | Masque | Bloc | Hôtes |
|------|--------|------|-------|
| /24 | 255.255.255.0 | 256 | 254 |
| /25 | 255.255.255.128 | 128 | 126 |
| /26 | 255.255.255.192 | 64 | 62 |
| /27 | 255.255.255.224 | 32 | 30 |
| /28 | 255.255.255.240 | 16 | 14 |
| /29 | 255.255.255.248 | 8 | 6 |
| /30 | 255.255.255.252 | 4 | 2 |

---

**💪 Recommence les exercices ratés jusqu'à avoir 100% ! Tu vas y arriver !**
