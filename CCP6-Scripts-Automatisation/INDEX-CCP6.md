# 📚 CCP6 - SCRIPTS ET AUTOMATISATION - INDEX

> **Coefficient** : 25% 🔥🔥 **PRIORITÉ ABSOLUE N°1**  
> **Temps de révision estimé** : 5-6h  
> **Contenu** : 30 QCM + 10 Questions ouvertes

---

## 🎯 Objectifs du CCP6

Maîtriser le scripting Bash et PowerShell :
- Syntaxe des deux langages
- Variables et types de données
- Structures de contrôle (if, for, while, case)
- Fonctions et paramètres
- Gestion des erreurs
- Manipulation de fichiers et chaînes
- Scripts d'automatisation système

---

## 📂 Organisation des ressources

```
CCP6-Scripts-Automatisation/
│
├── Fiche-Revisions/
│   └── FICHE-06-Scripting-Bash-PowerShell.md      → Théorie complète
│
├── QCM/
│   ├── Evaluation/
│   │   └── QCM-CCP6-Scripting-30Q-EVALUATION.md   → 15 Bash + 15 PowerShell
│   └── Correction/
│       └── QCM-CCP6-Scripting-30Q-CORRECTION.md
│
└── Questions-Ouvertes/
    ├── Evaluation/
    │   └── QUESTIONS-OUVERTES-CCP6-Scripting-10Q-EVALUATION.md
    └── Correction/
        └── QUESTIONS-OUVERTES-CCP6-Scripting-10Q-CORRECTION.md
```

---

## 📖 1. FICHE DE RÉVISION

### FICHE-06-Scripting-Bash-PowerShell.md
**Chemin** : `Fiche-Revisions/FICHE-06-Scripting-Bash-PowerShell.md`

**Contenu** :

**🐧 BASH** :
- Syntaxe de base (shebang `#!/bin/bash`)
- Variables (`VAR="valeur"`, `$VAR`)
- Structures de contrôle (`if [ ]; then; fi`, `for i in; do; done`)
- Opérateurs de comparaison (`-eq`, `-ne`, `-gt`, `-lt`)
- Tests de fichiers (`-f`, `-d`, `-e`, `-r`, `-w`, `-x`)
- Fonctions (`function nom() { }`)
- Paramètres (`$1`, `$2`, `$@`, `$#`)
- Redirections (`>`, `>>`, `2>`, `&>`)

**💻 POWERSHELL** :
- Syntaxe de base et cmdlets
- Variables (`$var = "valeur"`)
- Types de données (`[string]`, `[int]`, `[array]`, `[hashtable]`)
- Structures de contrôle (`if () { }`, `foreach ($item in $list) { }`)
- Pipeline (`|`, `Where-Object`, `Select-Object`, `ForEach-Object`)
- Fonctions (`function Nom-Fonction { param() }`)
- Gestion des erreurs (`Try/Catch/Finally`, `-ErrorAction`)
- Modules et cmdlets (Import-Module, Get-Command)

**Temps de lecture** : ⏱️ 90 min

**À faire** :
- [ ] Lire la fiche complète
- [ ] Comparer syntaxe Bash vs PowerShell
- [ ] Tester les exemples dans un terminal
- [ ] Créer tes propres scripts d'entraînement

---

## 📝 2. QCM (30 questions)

### QCM-CCP6-Scripting-30Q-EVALUATION.md
**Chemin** : `QCM/Evaluation/QCM-CCP6-Scripting-30Q-EVALUATION.md`

**Contenu** : 30 questions (15 Bash + 15 PowerShell)

**À faire** :
- [ ] Faire le QCM complet sans aide
- [ ] Chronométrer (40 min)
- [ ] Noter tes réponses

**Thèmes couverts** :
- Syntaxe de base (10 questions)
- Structures de contrôle (8 questions)
- Fonctions et paramètres (6 questions)
- Gestion d'erreurs (6 questions)

---

### QCM-CCP6-Scripting-30Q-CORRECTION.md
**Chemin** : `QCM/Correction/QCM-CCP6-Scripting-30Q-CORRECTION.md`

**Contenu** : Corrections détaillées avec explications

**À faire** :
- [ ] Corriger : Score = ___/30
- [ ] Comprendre chaque erreur de syntaxe
- [ ] Refaire les scripts ratés

**Score cible** : 24/30 (80%) minimum

---

## 📋 3. QUESTIONS OUVERTES (10 questions)

### QUESTIONS-OUVERTES-CCP6-Scripting-10Q-EVALUATION.md
**Chemin** : `Questions-Ouvertes/Evaluation/QUESTIONS-OUVERTES-CCP6-Scripting-10Q-EVALUATION.md`

**Contenu** : 10 exercices pratiques de scripting

**À faire** :
- [ ] Écrire les scripts complets
- [ ] Tester dans un terminal
- [ ] Vérifier que ça fonctionne

**Types d'exercices** :
- Script de sauvegarde automatique
- Script de création d'utilisateurs (CSV)
- Script de surveillance système
- Script de nettoyage de logs
- Automatisation de tâches répétitives

**Temps recommandé** : ⏱️ 120 min

---

### QUESTIONS-OUVERTES-CCP6-Scripting-10Q-CORRECTION.md
**Chemin** : `Questions-Ouvertes/Correction/QUESTIONS-OUVERTES-CCP6-Scripting-10Q-CORRECTION.md`

**Contenu** : Scripts complets commentés

---

## 📊 SUIVI DE PROGRESSION

### Checklist globale

- [ ] Fiche lue (Bash + PowerShell)
- [ ] QCM terminé : Score = ___/30 (___%)
- [ ] 10 scripts rédigés et testés
- [ ] Syntaxe Bash maîtrisée
- [ ] Syntaxe PowerShell maîtrisée

---

## 💡 AIDE-MÉMOIRE SYNTAXE

### 🐧 BASH

```bash
#!/bin/bash

# Variables
NOM="Jean"
AGE=25

# If
if [ $AGE -gt 18 ]; then
    echo "Majeur"
fi

# For loop
for i in {1..5}; do
    echo "Iteration $i"
done

# While loop
while [ $AGE -lt 30 ]; do
    AGE=$((AGE + 1))
done

# Function
function saluer() {
    echo "Bonjour $1"
}
saluer "Jean"

# Tests fichiers
if [ -f "/etc/passwd" ]; then
    echo "Fichier existe"
fi
```

### 💻 POWERSHELL

```powershell
# Variables
$Nom = "Jean"
$Age = 25

# If
if ($Age -gt 18) {
    Write-Host "Majeur"
}

# ForEach loop
foreach ($i in 1..5) {
    Write-Host "Iteration $i"
}

# While loop
while ($Age -lt 30) {
    $Age++
}

# Function
function Saluer {
    param([string]$Prenom)
    Write-Host "Bonjour $Prenom"
}
Saluer -Prenom "Jean"

# Try/Catch
Try {
    Get-Content "fichier.txt" -ErrorAction Stop
} Catch {
    Write-Host "Erreur: $($_.Exception.Message)"
}

# Pipeline
Get-Process | Where-Object {$_.CPU -gt 100} | Select-Object Name, CPU
```

---

## 🎯 DIFFÉRENCES CLÉS BASH vs POWERSHELL

| Aspect | Bash | PowerShell |
|--------|------|------------|
| **Variables** | `VAR="valeur"` (pas d'espace) | `$Var = "valeur"` |
| **If** | `if [ ]; then; fi` | `if () { }` |
| **Comparaison** | `-eq`, `-ne`, `-gt`, `-lt` | `-eq`, `-ne`, `-gt`, `-lt` |
| **For** | `for i in liste; do; done` | `foreach ($i in $liste) { }` |
| **Fonction** | `function nom() { }` | `function Nom { param() }` |
| **Erreurs** | `set -e`, codes retour | `Try/Catch`, `-ErrorAction` |
| **Objets** | Texte (chaînes) | Objets .NET |

---

## 🔗 NAVIGATION

- 📖 [README.md](../README.md)
- 📚 [INDEX-GENERAL.md](../INDEX-GENERAL.md)
- 📅 [PLANNING-REVISION-5-JOURS.md](../PLANNING-REVISION-5-JOURS.md)

---

**Commence maintenant** : [Fiche-Revisions/FICHE-06-Scripting-Bash-PowerShell.md](Fiche-Revisions/FICHE-06-Scripting-Bash-PowerShell.md)

**Bon courage ! 💻🚀**
