# QCM SCRIPTING - CCP6 BASH & POWERSHELL

**Certification TSSR** - CCP6 : Automatisation par scripts  
**30 questions à choix multiples**

---

## PARTIE 1 : BASH (15 questions)

### Q1 : Variables Bash
Quelle commande affiche la valeur de la variable `$USER` ?
- [ ] A) `print $USER`
- [ ] B) `echo $USER` 
- [ ] C) `display $USER`
- [ ] D) `show $USER`

---

### Q2 : Redirection de sortie
Que fait la commande `ls /home > liste.txt` ?
- [ ] A) Affiche liste fichiers et crée fichier vide
- [ ] B) Écrit sortie `ls` dans liste.txt (écrase si existe) 
- [ ] C) Ajoute sortie `ls` à la fin de liste.txt
- [ ] D) Provoque une erreur

---

### Q3 : Permissions fichiers
Que signifie `chmod 755 script.sh` ?
- [ ] A) Propriétaire: rwx, Groupe: r-x, Autres: r-x 
- [ ] B) Tous: lecture seule
- [ ] C) Propriétaire: rw-, Groupe: r--, Autres: r--
- [ ] D) Erreur de syntaxe

---

### Q4 : Shebang
Quelle ligne doit être en première ligne d'un script Bash ?
- [ ] A) `# Bash script`
- [ ] B) `#!/bin/bash` 
- [ ] C) `<bash>`
- [ ] D) `@echo off`

---

### Q5 : Test condition
Quelle syntaxe teste si fichier existe ?
- [ ] A) `if exist fichier.txt`
- [ ] B) `if [ -f fichier.txt ]` 
- [ ] C) `if test fichier.txt`
- [ ] D) `if (-e fichier.txt)`

---

### Q6 : Boucle for
Que fait ce code : `for i in 1 2 3; do echo $i; done` ?
- [ ] A) Affiche `1 2 3` sur une ligne
- [ ] B) Affiche 1, 2, 3 chacun sur nouvelle ligne 
- [ ] C) Erreur de syntaxe
- [ ] D) Affiche `i` 3 fois

---

### Q7 : Paramètres script
Dans un script, que contient `$1` ?
- [ ] A) Nom du script
- [ ] B) Premier argument passé au script 
- [ ] C) Premier processus lancé
- [ ] D) Nombre d'arguments

---

### Q8 : Commande grep
Que fait `grep "erreur" /var/log/syslog` ?
- [ ] A) Supprime lignes contenant "erreur"
- [ ] B) Affiche lignes contenant "erreur" 
- [ ] C) Compte occurrences "erreur"
- [ ] D) Remplace "erreur" par ""

---

### Q9 : Pipe (tube)
Que fait `cat fichier.txt | wc -l` ?
- [ ] A) Affiche contenu et nombre de mots
- [ ] B) Compte nombre de lignes dans fichier.txt 
- [ ] C) Compte caractères
- [ ] D) Erreur syntaxe

---

### Q10 : Condition if/else
```bash
if [ $age -ge 18 ]; then
  echo "Majeur"
else
  echo "Mineur"
fi
```
Que teste `-ge` ?
- [ ] A) Égal à
- [ ] B) Supérieur ou égal 
- [ ] C) Inférieur strict
- [ ] D) Différent de

---

### Q11 : Variable spéciale
Que contient `$?` après exécution commande ?
- [ ] A) PID processus
- [ ] B) Code retour dernière commande (0=succès) 
- [ ] C) Nombre arguments
- [ ] D) Nom utilisateur

---

### Q12 : Substitution commande
Quelle syntaxe stocke résultat `date` dans variable ?
- [ ] A) `ma_date = date`
- [ ] B) `ma_date=$(date)` 
- [ ] C) `ma_date = $(date)`
- [ ] D) `$ma_date=date`

---

### Q13 : Lecture entrée utilisateur
Quelle commande demande saisie utilisateur ?
- [ ] A) `input nom`
- [ ] B) `read nom` 
- [ ] C) `scan nom`
- [ ] D) `get nom`

---

### Q14 : Condition AND
Quelle syntaxe exécute cmd2 SI cmd1 réussit ?
- [ ] A) `cmd1 && cmd2` 
- [ ] B) `cmd1 || cmd2`
- [ ] C) `cmd1 & cmd2`
- [ ] D) `cmd1 ; cmd2`

---

### Q15 : Fonction Bash
```bash
ma_fonction() {
  echo "Hello $1"
}
ma_fonction "World"
```
Que affiche ce code ?
- [ ] A) `Hello`
- [ ] B) `Hello $1`
- [ ] C) `Hello World` 
- [ ] D) Erreur

---

## PARTIE 2 : POWERSHELL (15 questions)

### Q16 : Variables PowerShell
Quelle syntaxe déclare variable PowerShell ?
- [ ] A) `var $nom = "valeur"`
- [ ] B) `$nom = "valeur"` 
- [ ] C) `set nom = "valeur"`
- [ ] D) `let $nom = "valeur"`

---

### Q17 : Cmdlets
Quel est le format standard cmdlets PowerShell ?
- [ ] A) `Action.Object`
- [ ] B) `Verbe-Nom` 
- [ ] C) `nom_action`
- [ ] D) `ActionNom`

---

### Q18 : Pipeline PowerShell
Que fait `Get-Process | Where-Object {$_.CPU -gt 10}` ?
- [ ] A) Liste tous processus
- [ ] B) Liste processus avec CPU >10 secondes 
- [ ] C) Arrête processus CPU >10
- [ ] D) Erreur syntaxe

---

### Q19 : Alias
Quel alias correspond à `Get-ChildItem` ?
- [ ] A) `list`
- [ ] B) `dir` et `ls` 
- [ ] C) `show`
- [ ] D) `cat`

---

### Q20 : Comparaison
Quel opérateur teste égalité (case insensitive) ?
- [ ] A) `==`
- [ ] B) `-eq` 
- [ ] C) `-equal`
- [ ] D) `=`

---

### Q21 : Boucle ForEach
```powershell
$names = @("Alice", "Bob")
foreach ($name in $names) {
  Write-Host $name
}
```
Que affiche ce code ?
- [ ] A) `Alice Bob`
- [ ] B) Alice puis Bob (2 lignes) 
- [ ] C) `$name` 2 fois
- [ ] D) Erreur

---

### Q22 : Condition If
```powershell
if ($age -ge 18) {
  "Majeur"
} else {
  "Mineur"
}
```
Quelle différence avec `Write-Host` ?
- [ ] A) Aucune
- [ ] B) String seule retourne objet (peut être stocké) 
- [ ] C) Erreur syntaxe
- [ ] D) Write-Host obligatoire

---

### Q23 : Paramètres fonction
```powershell
function Get-Square {
  param([int]$nombre)
  return $nombre * $nombre
}
```
Comment appeler cette fonction ?
- [ ] A) `Get-Square 5` ou `Get-Square -nombre 5` 
- [ ] B) `Get-Square(5)`
- [ ] C) `Get-Square $5`
- [ ] D) `Square 5`

---

### Q24 : Gestion erreurs
Quelle structure capture erreurs PowerShell ?
- [ ] A) `if/else`
- [ ] B) `try/catch` 
- [ ] C) `error/handle`
- [ ] D) `check/fail`

---

### Q25 : Objets PowerShell
Que fait `Get-Service | Select-Object Name, Status` ?
- [ ] A) Affiche tous services
- [ ] B) Affiche uniquement colonnes Name et Status 
- [ ] C) Filtre services actifs
- [ ] D) Renomme colonnes

---

### Q26 : Import module
Comment importer module Active Directory ?
- [ ] A) `Load-Module ActiveDirectory`
- [ ] B) `Import-Module ActiveDirectory` 
- [ ] C) `Use-Module ActiveDirectory`
- [ ] D) `Get-Module ActiveDirectory`

---

### Q27 : Commentaires
Quelle syntaxe pour commentaire PowerShell ?
- [ ] A) `// commentaire`
- [ ] B) `# commentaire` 
- [ ] C) `<!-- commentaire -->`
- [ ] D) `/* commentaire */`

---

### Q28 : Types de données
Quelle commande force conversion string → int ?
- [ ] A) `$nombre = int("42")`
- [ ] B) `$nombre = [int]"42"` 
- [ ] C) `$nombre = convert("42", int)`
- [ ] D) `$nombre = ToInt("42")`

---

### Q29 : Exécution script
Pourquoi erreur "cannot be loaded because running scripts is disabled" ?
- [ ] A) Script corrompu
- [ ] B) Execution Policy restrictive 
- [ ] C) Droits insuffisants
- [ ] D) Syntaxe incorrecte

---

### Q30 : Sélection propriétés calculées
```powershell
Get-Process | Select-Object Name,
  @{Name="MemMB";Expression={$_.WS/1MB}}
```
Que fait cette commande ?
- [ ] A) Erreur syntaxe
- [ ] B) Affiche nom processus + mémoire en MB 
- [ ] C) Filtre processus >1MB
- [ ] D) Renomme propriété WS