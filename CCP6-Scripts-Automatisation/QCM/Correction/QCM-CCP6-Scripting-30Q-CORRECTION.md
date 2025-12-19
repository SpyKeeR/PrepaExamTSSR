Q1 : **B** ✅
**Explication** : `echo` affiche texte/variables. `$USER` contient nom utilisateur courant.

📖 **Variables d'environnement courantes en Bash** :

| Variable | Description | Exemple |
|----------|-------------|---------|
| `$USER` | Nom de l'utilisateur courant | john |
| `$HOME` | Répertoire personnel | /home/john |
| `$PATH` | Chemins de recherche des commandes | /usr/bin:/bin |
| `$SHELL` | Shell actuel | /bin/bash |
| `$PWD` | Répertoire courant | /home/john/scripts |
| `$HOSTNAME` | Nom de la machine | server01 |
| `$UID` | ID utilisateur | 1000 |

💡 **Exemples pratiques** :
```bash
# Afficher des variables
echo "Utilisateur: $USER"
echo "Répertoire personnel: $HOME"

# Script de bienvenue
echo "Bonjour $USER, vous êtes sur $HOSTNAME"

# Vérifier le shell utilisé
echo "Votre shell est: $SHELL"

# Créer un backup avec le nom d'utilisateur
cp config.txt /backup/config_${USER}_$(date +%Y%m%d).txt
```

⚠️ **Différences entre shells** :
- **sh** : Variables basiques uniquement
- **bash** : Variables étendues + fonctionnalités avancées
- **zsh** : Compatible bash + extensions (autocomplétion++)

🔐 **Variables personnalisées** :
```bash
# Définir une variable
nom="Alice"
echo "Bonjour $nom"

# Variables en MAJUSCULES pour les constantes
API_KEY="abc123"
MAX_RETRIES=5
```

Q2 : **B** ✅
**Explication** : `>` redirige stdout vers fichier (écrase). `>>` ajoute à la fin.

📖 **Redirections Bash** :

| Opérateur | Action | Exemple |
|-----------|--------|--------|
| `>` | Écrase fichier | `echo "test" > file.txt` |
| `>>` | Ajoute à la fin | `echo "test" >> file.txt` |
| `2>` | Redirige erreurs | `cmd 2> errors.log` |
| `&>` | Stdout + stderr | `cmd &> all.log` |
| `2>&1` | Stderr vers stdout | `cmd > out.txt 2>&1` |

💡 **Exemples** :
```bash
find / -name "*.txt" > found.txt 2> /dev/null  # Ignore erreurs
./script.sh &> complete.log                    # Tout dans 1 fichier
```
|----**B** ✅
**Explication** : Shebang `#!` indique interpréteur à utiliser. `/bin/bash` = shell Bash.

📖 **Différents interpréteurs et shebangs** :
**B** ✅
**Explication** : `[ -f fichier ]` teste existence fichier régulier. `-d` pour dossier, `-e` pour tout type.

📖 **Tableau COMPLET des tests en Bash** :

| Test | Description | Exemple |
|------|-------------|---------|
| `-f file` | Fichier régulier existe | `[ -f config.txt ]` |
| `-d dir` | Répertoire existe | `[ -d /var/log ]` |
| `-e path` | Fichier/répertoire existe (tout type) | `[ -e /tmp/file ]` |
| `-r file` | Fichier lisible | `[ -r data.csv ]` |
| `-w file` | Fichier modifiable | `[ -w log.txt ]` |
| `-x file` | Fichier exécutable | `[ -x script.sh ]` |
| `-s file` | Fichier non vide (size > 0) | `[ -s output.log ]` |
| `-L link` | Lien symbolique | `[ -L /usr/bin/python ]` |

💡 **Différences `[ ]` vs `[[ ]]` (Bash moderne)** :
```bash
# [ ] : Compatible POSIX (sh)
if [ -f "file.txt" ]; then
    echo "Fichier existe"
fi

# [[ ]] : Bash uniquement, plus puissant
if [[ -f "file.txt" && -r "file.txt" ]]; then
    echo "Fichier existe ET lisible"
fi

# [[ ]] supporte regex
if [[ "$name" =~ ^[A-Z] ]]; then
    echo "Commence par majuscule"
fi
```

⚠️ **Combinaisons AND/OR** :
```bash
# AND : Les DEUX conditions doivent être vraies
if [ -f file.txt ] && [ -r file.txt ]; then
    cat file.txt
fi

# OR : Au moins UNE condition vraie
if [ -f config.yml ] || [ -f config.yaml ]; then
    echo "Fichier de config trouvé"
fi

# Combinaison complexe avec [[ ]]
if [[ -f file.txt && ( -r file.txt || -w file.txt ) ]]; then
    echo "Fichier accessible"
fi
```

💡 **Exemples de vérifications pré-script** :
```bash
#!/bin/bash

# Vérifier qu'un fichier existe avant traitement
if [ ! -f /etc/config.conf ]; then
    echo "ERREUR: Fichier de config manquant"
    exit 1
fi

# Vérifier qu'un répertoire existe
if [ ! -d /var/backup ]; then
    echo "Création du répertoire backup..."
    mkdir -p /var/backup
fi

# Vérifier les permissions
if [ ! -x /usr/local/bin/tool ]; then
    echo "ERREUR: Tool non exécutable"
    chmod +x /usr/local/bin/tool
fi

# Vérifier qu'un fichier n'est pas vide
if [ -s data.txt ]; then
    echo "Traitement des données..."
    ./process.sh data.txt
else
    echo "ATTENTION: Fichier data.txt vide!"
fi

# Vérifier un lien symbolique
if [ -L /usr/bin/python ]; then
    echo "Python est un lien vers: $(readlink /usr/bin/python)"
fi
```

🔍 **Tests de comparaison** :
```bash
# Tests sur les dates (fichiers)
if [ file1.txt -nt file2.txt ]; then
    echo "file1 est plus récent que file2"
fi

if [ file1.txt -ot file2.txt ]; then
    echo "file1 est plus ancien que file2"
fi

# Vérifier qu'un fichier appartient à l'utilisateur
if [ -O file.txt ]; then
    echo "Vous êtes propriétaire du fichier"
fi
```
|---------|--------------|-------|
| `#!/bin/bash` | Bash (chemin fixe) | Scripts Bash standards |
| `#!/bin/sh` | Shell POSIX | Scripts portables |
| `#!/usr/bin/env bash` | Bash (via PATH) | Scripts portables modernes |
| `#!/usr/bin/python3` | Python 3 | Scripts Python |
| `#!/usr/bin/perl` | Perl | Scripts Perl |
| `#!/usr/bin/env node` | Node.js | Scripts JavaScript |

💡 **Pourquoi utiliser `/usr/bin/env` ?** :
```bash
# ✅ PORTABLE : Cherche bash dans le PATH
#!/usr/bin/env bash
# Fonctionne si bash est dans /bin, /usr/bin, /usr/local/bin

# ❌ MOINS PORTABLE : Chemin fixe
#!/bin/bash
# Échoue si bash est ailleurs (ex: macOS, BSD)
```

⚠️ **Compatibilité systèmes** :
```bash
# Script portable POSIX (fonctionne partout)
#!/bin/sh
name="World"
echo "Hello, $name"

# Script avec fonctionnalités Bash (arrays, etc.)
#!/bin/bash
names=("Alice" "Bob" "Charlie")
echo "Users: ${names[@]}"

# Script avec options
#!/bin/bash -e  # Arrêt sur erreur
#!/bin/bash -x  # Mode debug (affiche commandes)
```

💡 **Exemples multi-langages** :
```bash
# Script Python
#!/usr/bin/env python3
print("Hello from Python")

# Script Ruby
#!/usr/bin/env ruby
puts "Hello from Ruby"

# Script avec Perl
#!/usr/bin/perl
print "Hello from Perl\n";
```

🔍 **Sans shebang** :
```bash
# Exécution explicite (shebang ignoré)
bash script.sh     # Force bash
sh script.sh       # Force sh
python3 script.py  # Force Python

# Exécution directe (shebang REQUIS + chmod +x)
./script.sh  # Utilise le shebang pour savoir comment exécuter
```

✅ **Bonnes pratiques** :
```bash
#!/usr/bin/env bash
# Description du script
# Auteur: John Doe
# Date: 2025-12-19

set -euo pipefail  # Mode strict
# -e: Arrêt sur erreur
# -u: Erreur si variable non définie
# -o pipefail: Erreur dans les pipes

# Reste du script...
```
| `2>` | Redirige stderr | `cmd 2> errors.log` | Erreurs → errors.log |
| `&>` | Redirige stdout + stderr | `cmd &> all.log` | Tout → all.log |
| `2>&1` | Redirige stderr vers stdout | `cmd > out.txt 2>&1` | Tout → out.txt |
| `< ` | Redirige stdin | `cmd < input.txt` | Lit depuis input.txt |
| `/dev/null` | "Trou noir" (supprime) | `cmd > /dev/null` | Sortie supprimée |

💡 **Exemples pratiques** :
```bash
# Créer un log propre (écrase)
echo "=== Début backup ===" > backup.log

# Ajouter au log existant
echo "Fichiers copiés: 150" >> backup.log

# Rediriger les erreurs séparément
find / -name "*.conf" > found.txt 2> errors.txt

# Tout dans un seul fichier
./script.sh &> complete.log

# Ignorer complètement les erreurs
find / -name "*.txt" 2> /dev/null
```

⚠️ **Cas d'usage professionnels** :
```bash
# Logs applicatifs avec horodatage
echo "$(date '+%Y-%m-%d %H:%M:%S') - Service démarré" >> /var/log/app.log

# Script avec gestion d'erreurs
{
    rsync -av /data/ /backup/
    echo "Backup réussi"
} >> /var/log/backup.log 2>&1

# Redirection combinée pour cron
0 2 * * * /scripts/backup.sh >> /var/log/backup.log 2>&1
```

🔍 **Pièges courants** :
- ❌ `cmd > file 2>&1` : OK
- ❌ `cmd 2>&1 > file` : KO (ordre important!)
- ✅ `cmd &> file` : Plus simple et moderne

Q3 : 
**Explication** : 7=rwx (4+2+1), 5=r-x (4+0+1), 5=r-x. Owner peut exécuter, autres lisent+exécutent.

Q4 : 

**Explication** : Shebang `#!` indique interpréteur à utiliser. `/bin/bash` = shell Bash.

Q5 : 
**Explication** : `[ -f fichier ]` teste existence fichier régulier. `-d` pour dossier, `-e` pour tout type.

Q6 : **B** ✅
**Explication** : Boucle parcourt liste `1 2 3`, affiche chaque valeur. `done` termine.

```bash
for i in 1 2 3; do
    echo "$i"
done

for i in {1..10}; do    # Range
for ((i=1; i<=10; i++)); do  # Style C
```

**Usage** : Traiter fichiers, itérations.

💡 **Boucles while et until** :
```bash
# While : Tant que condition vraie
count=1
while [ $count -le 5 ]; do
    echo "Count: $count"
    ((count++))
done

# Until : Jusqu'à ce que condition soit vraie
count=1
until [ $count -gt 5 ]; do
    echo "Count: $count"
    ((count++))
done

# While avec lecture de fichier (ligne par ligne)
while read line; do
    echo "Ligne: $line"
done < input.txt

# Boucle infinie (avec break)
while true; do
    echo "Entrez 'quit' pour sortir"
    read input
    [ "$input" = "quit" ] && break
done
```

⚠️ **Contrôle de flux : break et continue** :
```bash
# break : Sortir de la boucle
for i in {1..10}; do
    [ $i -eq 5 ] && break
    echo $i
done
# Affiche : 1 2 3 4

# continue : Passer à l'itération suivante
for i in {1..5}; do
    [ $i -eq 3 ] && continue
    echo $i
done
# Affiche : 1 2 4 5

# Utilisation avec find
find /tmp -type f | while read file; do
    [ ! -r "$file" ] && continue  # Passer si non lisible
    echo "Traitement: $file"
done
```

💡 **Parcours fichiers/répertoires** :
```bash
# Parcourir tous les .sh récursivement
find . -name "*.sh" | while read script; do
    echo "Script trouvé: $script"
    chmod +x "$script"
done

# Boucle sur répertoires uniquement
for dir in */; do
    echo "Répertoire: ${dir%/}"
    ls -l "$dir"
done

# Traitement de fichiers avec espace dans le nom
find . -name "*.log" -print0 | while IFS= read -r -d '' file; do
    echo "Log: $file"
done
```

✅ **Cas d'usage professionnels** :
```bash
# Backup de plusieurs serveurs
for server in web1 web2 db1; do
    echo "Backup de $server..."
    rsync -av $server:/data/ /backup/$server/
done

# Traitement par batch
for ((i=1; i<=100; i+=10)); do
    echo "Traitement batch $i à $((i+9))"
    ./process.sh --start=$i --end=$((i+9))
done

# Attente d'un service
while ! curl -s http://localhost:8080 > /dev/null; do
    echo "En attente du service..."
    sleep 2
done
echo "Service démarré!"
```

Q7 : **B** ✅
**Explication** : `$0`=nom script, `$1`=1er arg, `$#`=nombre args.

**Variables** :

| Variable | Description |
|----------|-------------|
| `$0` | Nom script |
| `$1`-`$9` | Arguments |
| `$#` | Nombre |
| `$@` | Tous (séparés) |

```bash
echo "Nom: $0, 1er arg: $1, Total: $#"
```**B** ✅
**Explication** : `grep` recherche motif. Options : `-i` insensible casse, `-v` inverse, `-c` compte.

📖 * **B** ✅
**Explication** : `|` envoie sortie `cat` vers `wc -l` (compte lignes). `wc -w`=mots, `wc -c`=caractères.

📖 **C**B** ✅
**Explication** : `-ge` = Greater or Equal. `-gt`=>, `-lt`=<, `-le`=<=, `-eq`==, `-ne`=!=

📖 **Tableau COMPLET opérateurs de comparaison Bash** :

**Comparaisons numériques** :

| Opérateur | Description | Exemple | Résultat |
|-----------|-------------|---------|----------|
| `-eq` | Égal | `[ 5 -eq 5 ]` | True |
| `-ne` | Différent | `[ 5 -ne 3 ]` | True |
| `-gt` | Strictement supérieur | `[ 5 -gt 3 ]` | True |
| `-ge` | Supérieur ou égal | `[ 5 -ge 5 ]` | True |
| `-lt` | Strictement inférieur | `[ 3 -lt 5 ]` | True |
| `-le` | Inférieur ou égal | `[ 3 -le 5 ]` | True |

**Comparaisons de chaînes** :

| Opérateur | Description | Exemple | Résultat |
|-----------|-------------|---------|----------|
| `=` ou `==` | Égal | `[ "$a" = "test" ]` | True si égal |
| `!=` | Différent | `[ "$a" != "test" ]` | True si différent |
| `<` | Inférieur (ordre alphabétique) | `[[ "$a" < "$b" ]]` | True si a < b |
| `>` | Supérieur (ordre alphabétique) | `[[ "$a" > "$b" ]]` | True si a > b |
| `-z` | Chaîne vide | `[ -z "$var" ]` | True si vide |
| `-n` | Chaîne non vide | `[ -n "$var" ]` | True si non vide |

💡 **Exemples avec comparaisons numériques** :
```bash
#!/bin/bash

age=25

# Vérifier majorité
if [ $age -ge 18 ]; then
    echo "Majeur"
else
    echo "Mineur"
fi

# Comparer des nombres
count=10
if [ $count -gt 5 ] && [ $count -lt 15 ]; then
    echo "Nombre entre 5 et 15"
fi

# Vérifier égalité
if [ $age -eq 25 ]; then
    echo "Âge exact: 25 ans"
fi

# Vérifier différence
if [ $count -ne 0 ]; then
    echo "Count non nul"
fi
```

⚠️ **Double parenthèses : `(( ))` pour arithmétique** :
```bash
# Syntaxe classique avec [ ]
if [ $x -gt 5 ]; then
    echo "x > 5"
fi

# Syntaxe moderne avec (( )) - Plus lisible!
if (( x > 5 )); then
    echo "x > 5"
fi

# Opérateurs standards dans (( ))
(( x > 5 ))   # Supérieur
(( x >= 5 ))  # Supérieur ou égal
(( x < 5 ))   # Inférieur
(( x <= 5 ))  # Inférieur ou égal
(( x == 5 ))  # Égal
(( x != 5 ))  # Différent

# Arithmétique avancée
(( result = x + y ))
(( x++ ))
(( y *= 2 ))

# Combinaisons
if (( x > 5 && y < 10 )); then
    echo "Conditions multiples OK"
fi
```

💡 **Comparaisons de chaînes** :
```bash
#!/bin/bash

name="Alice"

# Égalité
if [ "$name" = "Alice" ]; then
    echo "Bonjour Alice"
fi

# Différence
if [ "$name" != "Bob" ]; then
    echo "Ce n'est pas Bob"
fi

# Chaîne vide
if [ -z "$empty_var" ]; then
    echo "Variable vide"
fi

# Chaîne non vide
if [ -n "$name" ]; then
    echo "Variable définie: $name"
fi

# Comparaison alphabétique (avec [[ ]])
if [[ "apple" < "banana" ]]; then
    echo "apple vient avant banana"
fi

# Pattern matching avec [[ ]]
if [[ "$name" == A* ]]; then
    echo "Nom commence par A"
fi
```

✅ **Tests combinés avec && et ||** :
```bash
# AND : Les DEUX doivent être vrais
if [ $age -ge 18 ] && [ $age -le 65 ]; then
    echo "Âge actif"
fi

# OR : Au moins UN doit être vrai
if [ "$status" = "OK" ] || [ "$status" = "SUCCESS" ]; then
    echo "Statut positif"
fi

# Combinaison complexe
if [ $score -ge 50 ] && { [ "$grade" = "A" ] || [ "$grade" = "B" ]; }; then
    echo "Bon résultat"
fi

# Avec [[ ]] - Syntaxe plus claire
if [[ $age -ge 18 && ( $country == "FR" || $country == "BE" ) ]]; then
    echo "Citoyen européen majeur"
fi
```

🔍 **Pièges courants** :
```bash
# ❌ ERREUR : Oubli des guillemets avec chaînes
var="hello world"
if [ $var = "hello world" ]; then  # ❌ Erreur : trop d'arguments
    echo "OK"
fi

# ✅ CORRECT : Toujours guillemets pour les chaînes
if [ "$var" = "hello world" ]; then  # ✅ OK
    echo "OK"
fi

# ❌ ERREUR : Utiliser = pour nombres
if [ $age = 25 ]; then  # Fonctionne mais pas idiomatique
    echo "25 ans"
fi

# ✅ CORRECT : Utiliser -eq pour nombres
if [ $age -eq 25 ]; then  # ✅ OK
    echo "25 ans"
fi

# ❌ ERREUR : Espaces manquants
if [$age -eq 25]; then  # ❌ Erreur de syntaxe
    echo "25 ans"
fi

# ✅ CORRECT : Espaces obligatoires
if [ $age -eq 25 ]; then  # ✅ OK
    echo "25 ans"
fi
```

💡 **Cas d'usage professionnels** :
```bash
# Vérifier ressources système
mem_used=$(free | awk '/Mem:/ {print $3/$2 * 100}')
if (( $(echo "$mem_used > 90" | bc -l) )); then
    echo "ALERTE: Mémoire > 90%"
    # Envoyer notification
fi

# Validation entrée utilisateur
read -p "Entrez votre âge: " age
if ! [[ "$age" =~ ^[0-9]+$ ]]; then
    echo "ERREUR: Âge invalide"
    exit 1
elif [ $age -lt 0 ] || [ $age -gt 150 ]; then
    echo "ERREUR: Âge hors limites"
    exit 1
fi

# Comparer versions
version="2.5.1"
required="2.4.0"
if [[ "$version" > "$required" ]]; then
    echo "Version OK"
fi
```
```bash
# Pipe simple
cat file.txt | wc -l

# Chaînage multiple commandes
cat access.log | grep "ERROR" | wc -l

# Pipeline complexe d'analyse
cat /var/log/syslog | \
    grep "failed" | \
    awk '{print $1, $2, $3}' | \
    sort | \
    uniq -c | \
    sort -rn | \
    head -10

# Comptage avec wc
wc -l file.txt   # Nombre de lignes
wc -w file.txt   # Nombre de mots
wc -c file.txt   # Nombre d'octets
wc -m file.txt   # Nombre de caractères
```

💡 **tee : Sauvegarder résultat intermédiaire** :
```bash
# tee écrit dans fichier ET affiche à l'écran
echo "Test" | tee output.txt

# Sauvegarder résultat intermédiaire dans pipeline
cat data.txt | \
    grep "important" | \
    tee filtered.txt | \
    sort | \
    uniq > final.txt
# filtered.txt contient la sortie de grep
# final.txt contient le résultat final

# Ajouter avec tee (équivalent >>)
echo "Log" | tee -a logfile.txt

# tee multiple (écrire dans plusieurs fichiers)
echo "Data" | tee file1.txt file2.txt file3.txt
```

⚠️ **Named pipes (FIFO)** :
```bash
# Créer un named pipe
mkfifo /tmp/mypipe

# Terminal 1 : Écrire dans le pipe
echo "Hello" > /tmp/mypipe &

# Terminal 2 : Lire depuis le pipe
cat /tmp/mypipe
# Affiche: Hello

# Utilisation pour communication inter-processus
mkfifo /tmp/backup_pipe
gzip -c < /tmp/backup_pipe > backup.gz &
tar -cf /tmp/backup_pipe /data/
```

💡 **One-liners puissants** :
```bash
# Top 10 des commandes les plus utilisées
history | awk '{print $2}' | sort | uniq -c | sort -rn | head -10

# Trouver les plus gros fichiers
find . -type f -exec du -h {} + | sort -rh | head -10

# Analyser les IP les plus fréquentes dans un log
cat access.log | \
    grep -oE "[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}" | \
    sort | uniq -c | sort -rn | head -20

# Liste des utilisateurs avec leur shell
cat /etc/passwd | cut -d: -f1,7 | column -t -s:

# Résumé de l'utilisation disque par répertoire
du -sh */ | sort -rh | head -10

# Trouver les processus consommant le plus de mémoire
ps aux | sort -rnk 4 | head -10

# Extraire emails d'un fichier
grep -oE "[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}" file.txt | \
    sort -u

# Compter les extensions de fichiers
find . -type f | sed 's/.*\.//' | sort | uniq -c | sort -rn
```

✅ **Cas d'usage professionnels** :
```bash
# Monitoring système en temps réel
vmstat 1 | awk '{print $1, $2, $15}' | tee monitoring.log

# Backup avec progression
tar -czf - /data | pv | ssh user@remote "cat > backup.tar.gz"

# Analyse de logs distribuée
ssh server1 "tail -1000 /var/log/app.log" | \
    grep "ERROR" | \
    wc -l

# Pipeline de transformation données
cat users.csv | \
    cut -d',' -f1,3 | \
    grep "@company.com" | \
    sort | \
    tr '[:lower:]' '[:upper:]' > formatted.txt

# Déploiement multi-serveurs
cat servers.txt | while read server; do
    ssh $server "sudo systemctl restart apache2"
done | tee deploy.log
```

🔍 **Optimisation performance** :
```bash
# ❌ Inefficace : cat inutile
cat file.txt | grep "pattern"

# ✅ Optimal : grep direct
grep "pattern" file.txt

# ❌ Lent : Plusieurs pipes
cat file.txt | grep "a" | grep "b" | grep "c"

# ✅ Plus rapide : grep avec regex
grep "a.*b.*c" file.txt

# Utiliser xargs pour paralléliser
find . -name "*.log" | xargs -P 4 -I {} gzip {}
# -P 4 = 4 processus parallèles
```

💡 **Gestion d'erreurs dans pipes** :
```bash
# Par défaut, le pipe continue même si une commande échoue
false | echo "Ça s'affiche quand même"

# set -o pipefail : Arrêt si erreur dans pipe
set -o pipefail
false | echo "Ça ne s'affichera pas"
echo $?  # Code erreur de false

# Utile pour scripts robustes
#!/bin/bash
set -euo pipefail

cat data.txt | process.sh | upload.sh
# Arrêt immédiat si une des commandes échoue
```
| Option | Description | Exemple |
|--------|-------------|---------|
| `-i` | Insensible à la casse | `grep -i "error" log.txt` |
| `-v` | Inverse (lignes NE contenant PAS le motif) | `grep -v "DEBUG" log.txt` |
| `-c` | Compte les lignes correspondantes | `grep -c "ERROR" log.txt` |
| `-n` | Affiche les numéros de ligne | `grep -n "TODO" code.py` |
| `-r` / `-R` | Recherche récursive dans répertoires | `grep -r "function" ./src/` |
| `-l` | Affiche seulement les noms de fichiers | `grep -l "password" *.conf` |
| `-w` | Mot entier uniquement | `grep -w "cat" file.txt` |
| `-A <N>` | Affiche N lignes après | `grep -A 3 "ERROR" log.txt` |
| `-B <N>` | Affiche N lignes avant | `grep -B 2 "ERROR" log.txt` |
| `-C <N>` | Affiche N lignes avant ET après | `grep -C 3 "ERROR" log.txt` |
| `-E` | Regex étendues (ou `egrep`) | `grep -E "error|warning" log.txt` |
| `-o` | Affiche seulement la partie correspondante | `grep -o "[0-9]\+" file.txt` |

💡 **Regex basiques vs étendues** :
```bash
# Regex BASIQUE (grep par défaut)
grep "test\|error" file.txt  # \| pour OU (échappé)
grep "^Error.*failed$" log.txt

# Regex ÉTENDUE (grep -E ou egrep)
grep -E "test|error" file.txt  # | pour OU (pas d'échappement)
grep -E "^(Error|Warning)" log.txt
grep -E "[0-9]{3}-[0-9]{4}" phones.txt  # {n,m} quantificateur

# Caractères spéciaux courants
# ^ = début de ligne
# $ = fin de ligne
# . = n'importe quel caractère
# * = 0 ou plus fois
# + = 1 ou plus fois (regex étendue)
# ? = 0 ou 1 fois (regex étendue)
# [abc] = a, b ou c
# [^abc] = tout SAUF a, b, c
```

⚠️ **Exemples d'analyse de logs** :
```bash
# Compter les erreurs dans un log
grep -c "ERROR" /var/log/app.log

# Trouver toutes les erreurs avec contexte
grep -C 5 "ERROR" /var/log/app.log

# Filtrer plusieurs types d'erreurs
grep -E "ERROR|FATAL|CRITICAL" app.log

# Exclure les logs DEBUG et INFO
grep -v -E "DEBUG|INFO" app.log

# Trouver les IP dans un log
grep -oE "[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}" access.log

# Recherche récursive de TODO dans code source
grep -rn "TODO" ./src/ | grep -v node_modules

# Compter occurrences d'un mot (insensible casse)
grep -io "password" * | wc -l

# Lister les fichiers contenant "config"
grep -rl "database_host" /etc/
```

💡 **Alternatives et compléments** :
```bash
# egrep = grep -E (regex étendues)
egrep "error|warning" log.txt

# fgrep = grep -F (pas de regex, plus rapide)
fgrep "literal.string" file.txt

# awk : Plus puissant pour colonnes
awk '/ERROR/ {print $1, $5}' log.txt

# sed : Recherche ET remplacement
sed -n '/ERROR/p' log.txt  # Affiche lignes avec ERROR

# ripgrep (rg) : Moderne, très rapide
rg "error" --type py  # Recherche dans fichiers Python
```

✅ **Cas d'usage professionnels** :
```bash
# Monitoring en temps réel
tail -f /var/log/syslog | grep --color "ERROR"

# Rapport d'erreurs quotidien
grep "ERROR" /var/log/app.log | \
    grep "$(date +%Y-%m-%d)" | \
    sort | uniq -c | sort -rn

# Vérification configuration
grep -r "PermitRootLogin" /etc/ssh/

# Audit sécurité
grep -i "failed\|error\|denied" /var/log/auth.log | \
    grep "$(date +%b\ %d)"

# Extraction données structurées
grep -oP 'user_id=\K[0-9]+' app.log | sort -u
```

🔍 **Performance** :
```bash
# ✅ Rapide : grep avec fichier spécifique
grep "pattern" /var/log/app.log

# ⚠️ Lent : grep récursif sur gros répertoire
grep -r "pattern" /

# ✅ Optimisé : Limiter la recherche
find /var/log -name "*.log" -exec grep "ERROR" {} +
```
| Variable | Description | Exemple |
|----------|-------------|---------|
| `$0` | Nom du script | `./backup.sh` |
| `$1` à `$9` | Arguments positionnels | `$1` = premier arg |
| `${10}` | Arguments 10+ (accolades requises) | `${10}` = 10ème arg |
| `$#` | Nombre d'arguments | Si 3 args → `$#` = 3 |
| `$@` | Tous les arguments (séparés) | `"$@"` = `"$1" "$2" "$3"` |
| `$*` | Tous les arguments (chaîne unique) | `"$*"` = `"$1 $2 $3"` |
| `$?` | Code retour dernière commande | 0 = succès |
| `$$` | PID du script | 12345 |
| `$!` | PID du dernier processus background | 12346 |

💡 **Exemples d'utilisation** :
```bash
#!/bin/bash
# Script: backup.sh
# Usage: ./backup.sh /source /destination

echo "Nom du script: $0"
echo "Premier argument: $1"
echo "Deuxième argument: $2"
echo "Nombre d'arguments: $#"
echo "Tous les arguments: $@"
echo "PID du script: $$"

# Vérifier le nombre d'arguments
if [ $# -ne 2 ]; then
    echo "Usage: $0 <source> <destination>"
    exit 1
fi

# Utiliser les arguments
rsync -av "$1" "$2"
```

⚠️ **shift : Décaler les arguments** :
```bash
#!/bin/bash
# Traiter des options avec shift

while [ $# -gt 0 ]; do
    case $1 in
        -v|--verbose)
            VERBOSE=1
            shift  # Décale $2 vers $1, $3 vers $2, etc.
            ;;
        -o|--output)
            OUTPUT="$2"
            shift 2  # Décale de 2 positions
            ;;
        *)
            echo "Option inconnue: $1"
            exit 1
            ;;
    esac
done
```

💡 **Gestion avancée avec getopts** :
```bash
#!/bin/bash
# Parsing d'options style getopt

while getopts "vhf:o:" opt; do
    case $opt in
        v)
            echo "Mode verbose activé"
            ;;
        h)
            echo "Usage: $0 [-v] [-f fichier] [-o sortie]"
            exit 0
            ;;
        f)
            FILE="$OPTARG"
            echo "Fichier: $FILE"
            ;;
        o)
            OUTPUT="$OPTARG"
            echo "Sortie: $OUTPUT"
            ;;
        \?)
            echo "Option invalide: -$OPTARG"
            exit 1
            ;;
    esac
done

# Arguments restants après les options
shift $((OPTIND-1))
echo "Arguments restants: $@"
```

✅ **Validation robuste des arguments** :
```bash
#!/bin/bash
# Script avec validation complète

# Vérifier nombre minimum d'arguments
if [ $# -lt 2 ]; then
    echo "ERREUR: Nombre d'arguments insuffisant"
    echo "Usage: $0 <source> <destination> [options]"
    exit 1
fi

SOURCE="$1"
DEST="$2"

# Vérifier que la source existe
if [ ! -e "$SOURCE" ]; then
    echo "ERREUR: Source '$SOURCE' introuvable"
    exit 2
fi

# Vérifier que la destination est accessible
if [ ! -d "$DEST" ]; then
    echo "ATTENTION: Création de $DEST"
    mkdir -p "$DEST"
fi

echo "Copie de $SOURCE vers $DEST"
cp -r "$SOURCE" "$DEST"
```

🔍 **Différence $@ vs $*** :
```bash
# Avec espaces dans les arguments
# Appel: ./script.sh "arg 1" "arg 2"

for arg in "$@"; do
    echo "Arg: [$arg]"
done
# Sortie:
# Arg: [arg 1]
# Arg: [arg 2]

for arg in "$*"; do
    echo "Arg: [$arg]"
done
# Sortie:
# Arg: [arg 1 arg 2]

# ✅ Toujours utiliser "$@" pour préserver les espaces!
```

Q8 : 
**Explication** : `grep` recherche motif. Options : `-i` insensible casse, `-v` inverse, `-c` compte.

Q9 :
**Explication** : `|` envoie sortie `cat` vers `wc -l` (compte lignes). `wc -w`=mots, `wc -c`=caractères.

Q10 : 
**Explication** : `-ge` = Greater or Equal. `-gt`=>, `-lt`=<, `-le`=<=, `-eq`==, `-ne`=!=

Q11 : **B** ✅
**Explication** : `$?` = exit code. 0=succès, autre=erreur.

**Codes** :

| Code | Signification |
|------|---------------|
| `0` | Succès |
| `1` | Erreur générale |
| `127` | Commande introuvable |

```bash
ls /inexistant
echo $?  # 2

if [ $? -eq 0 ]; then
    echo "OK"
fi
```
| `0` | Succès | Commande exécutée correctement |
| `1` | Erreur générale | Erreur non spécifique |
| `2` | Mauvaise utilisation | Arguments incorrects |
| `126` | Commande non exécutable | Permissions manquantes |
| `127` | Commande introuvable | Commande n'existe pas |
| `128+N` | Signal reçu | 130 = SIGINT (Ctrl+C) |
| `130` | Script terminé par Ctrl+C | SIGINT (signal 2) |
| `137` | Script tué (kill -9) | SIGKILL (signal 9) |
| `255` | Exit code hors limites | exit code > 255 |

💡 **Utilisation de $? dans scripts** :
```bash
#!/bin/bash

# Vérifier succès d'une commande
mkdir /tmp/test
if [ $? -eq 0 ]; then
    echo "Répertoire créé avec succès"
else
    echo "Échec de création du répertoire"
fi

# Forme condensée avec &&
mkdir /tmp/test && echo "Succès" || echo "Échec"

# Capturer le code retour
ls /inexistant
retcode=$?
if [ $retcode -ne 0 ]; then
    echo "Erreur $retcode: Fichier introuvable"
fi

# Chaîner des commandes avec vérification
command1
if [ $? -eq 0 ]; then
    command2
    if [ $? -eq 0 ]; then
        command3
    fi
fi
```

⚠️ **exit dans les scripts** :
```bash
#!/bin/bash

# Sortir avec code d'erreur spécifique
if [ $# -eq 0 ]; then
    echo "ERREUR: Aucun argument"
    exit 1  # Code personnalisé
fi

# Différents codes pour différentes erreurs
if [ ! -f "$1" ]; then
    echo "ERREUR: Fichier introuvable"
    exit 2
fi

if [ ! -r "$1" ]; then
    echo "ERREUR: Fichier non lisible"
    exit 3
fi

# Succès
echo "Traitement de $1..."
exit 0  # Succès explicite
```

💡 **set -e : Arrêt automatique sur erreur** :
```bash
#!/bin/bash
set -e  # Arrêt immédiat si une commande échoue

# Si une des commandes échoue, le script s'arrête
mkdir /tmp/backup
cd /tmp/backup
tar -czf backup.tar.gz /data
rsync backup.tar.gz user@remote:/backups/

echo "Backup terminé"  # Atteint seulement si tout réussit

# Désactiver temporairement
set +e
command_qui_peut_echouer  # Continue même si échec
set -e

# Alternative : Ignorer erreur d'une commande spécifique
grep "pattern" file.txt || true  # Continue même si pas trouvé
```

✅ **Tests conditionnels basés sur $?** :
```bash
# Forme longue
ping -c 1 server.com > /dev/null
if [ $? -eq 0 ]; then
    echo "Serveur accessible"
else
    echo "Serveur inaccessible"
fi

# Forme condensée (équivalent)
if ping -c 1 server.com > /dev/null; then
    echo "Serveur accessible"
else
    echo "Serveur inaccessible"
fi

# Tests multiples
rsync -av /data /backup
RSYNC_EXIT=$?
case $RSYNC_EXIT in
    0)
        echo "Backup réussi"
        ;;
    1)
        echo "Erreur de syntaxe"
        ;;
    23)
        echo "Erreur transfert partiel"
        ;;
    *)
        echo "Erreur inconnue: $RSYNC_EXIT"
        ;;
esac
```

💡 **Cas d'usage professionnels** :
```bash
# Script de backup avec gestion d'erreurs
#!/bin/bash
set -e
trap 'echo "ERREUR ligne $LINENO: Code $?"' ERR

LOG="/var/log/backup.log"

{
    echo "=== Backup $(date) ==="
    
    tar -czf /backup/data.tar.gz /data/
    echo "Archive créée: $?"
    
    rsync -av /backup/data.tar.gz remote:/backups/
    echo "Transfert réussi: $?"
    
    echo "Backup terminé avec succès"
} >> $LOG 2>&1

exit 0

# Monitoring avec alertes
#!/bin/bash
service nginx status > /dev/null 2>&1
if [ $? -ne 0 ]; then
    echo "ALERTE: Nginx arrêté" | mail -s "Service Down" admin@company.com
    systemctl start nginx
    if [ $? -eq 0 ]; then
        echo "Nginx redémarré"
    else
        echo "CRITIQUE: Impossible de redémarrer Nginx"
        exit 1
    fi
fi

# Tests de connectivité réseau
#!/bin/bash
SERVERS=("web1" "web2" "db1")
FAILED=0

for server in "${SERVERS[@]}"; do
    ping -c 1 -W 2 $server > /dev/null 2>&1
    if [ $? -ne 0 ]; then
        echo "ERREUR: $server inaccessible"
        ((FAILED++))
    fi
done

exit $FAILED  # Retourne le nombre de serveurs en échec
```

🔍 **Debugging avec $?** :
```bash
# Mode debug avancé
#!/bin/bash
set -x  # Affiche toutes les commandes

command1
echo "Command1 exit code: $?"

command2
echo "Command2 exit code: $?"

set +x  # Désactive debug

# Fonction wrapper pour logger exit codes
run_with_log() {
    "$@"
    local exit_code=$?
    echo "$(date): $@ exited with $exit_code" >> script.log
    return $exit_code
}

run_with_log rsync -av /data /backup
```

Q12 : **B** ✅
**Explication** : `$()` ou backticks `` `cmd` `` exécutent commande.

```bash
date_now=$(date +%Y%m%d)
echo "Date: $date_now"

files=$(ls *.txt)
count=$(wc -l < file.txt)
```

**Note** : Préférer `$()` (lisibilité). Pas d'espaces autour `=`.

```bash
# Syntaxe 1 (moderne)
function nom_fonction() {
    # Corps de la fonction
    echo "Hello"
}

# Syntaxe 2 (POSIX, préférée)
nom_fonction() {
    # Corps de la fonction
    echo "Hello"
}

# Appel de fonction
nom_fonction          # Sans arguments
nom_fonction arg1 arg2  # Avec arguments
```

💡 **Arguments de fonctions** :
```bash
#!/bin/bash

# Fonction avec arguments
greet() {
    local name=$1  # Premier argument
    local age=$2   # Deuxième argument
    echo "Bonjour $name, vous avez $age ans"
}

# Appel
greet "Alice" 25
# Sortie: Bonjour Alice, vous avez 25 ans

# Fonction avec nombre variable d'arguments
sum() {
    local total=0
    for num in "$@"; do
        ((total += num))
    done
    echo $total
}

result=$(sum 10 20 30 40)
echo "Somme: $result"  # 100

# Vérifier nombre d'arguments
backup_file() {
    if [ $# -ne 2 ]; then
        echo "Usage: backup_file <source> <destination>"
        return 1
    fi
    
    local source=$1
    local dest=$2
    cp "$source" "$dest"
}
```

⚠️ **Variables locales vs globales** :
```bash
#!/bin/bash

# Variable globale
global_var="Global"

test_scope() {
    # Variable locale (visible seulement dans la fonction)
    local local_var="Local"
    
    # Modification variable globale
    global_var="Modified"
    
    # Nouvelle variable globale depuis fonction
    new_global="Created in function"
    
    echo "Dans fonction:"
    echo "  local_var: $local_var"
    echo "  global_var: $global_var"
}

echo "Avant fonction:"
echo "  global_var: $global_var"

test_scope

echo "Après fonction:"
echo "  global_var: $global_var"  # Modified
echo "  new_global: $new_global"  # Created in function
echo "  local_var: $local_var"    # (vide - n'existe plus)

# ✅ BONNE PRATIQUE : Toujours utiliser local dans fonctions
calculate() {
    local result=$(( $1 + $2 ))
    echo $result
}
```

💡 **return vs echo (valeur de retour)** :
```bash
# return : Code de retour (0-255)
is_file_readable() {
    [ -r "$1" ]
    return $?  # Retourne 0 si lisible, 1 sinon
}

if is_file_readable "/etc/passwd"; then
    echo "Fichier lisible"
fi

# echo : Retourne une valeur (texte/nombre)
get_username() {
    local username=$(whoami)
    echo "$username"  # "Retourne" la valeur
}

user=$(get_username)
echo "Utilisateur: $user"

# Fonction avec return ET echo
divide() {
    if [ $2 -eq 0 ]; then
        echo "ERREUR: Division par zéro" >&2
        return 1
    fi
    
    local result=$(echo "scale=2; $1 / $2" | bc)
    echo $result
    return 0
}

result=$(divide 10 3)
if [ $? -eq 0 ]; then
    echo "Résultat: $result"
else
    echo "Erreur de calcul"
fi
```

✅ **Bibliothèques de fonctions (source)** :
```bash
# Fichier: lib/utils.sh
#!/bin/bash

log_info() {
    echo "[$(date '+%Y-%m-%d %H:%M:%S')] INFO: $1" >> /var/log/app.log
}

log_error() {
    echo "[$(date '+%Y-%m-%d %H:%M:%S')] ERROR: $1" >&2 >> /var/log/app.log
}

check_root() {
    if [ $EUID -ne 0 ]; then
        log_error "Ce script nécessite les droits root"
        return 1
    fi
}

backup_with_date() {
    local source=$1
    local dest="${2}_$(date +%Y%m%d_%H%M%S)"
    cp -r "$source" "$dest"
    log_info "Backup créé: $dest"
}

# Fichier principal: script.sh
#!/bin/bash

# Charger la bibliothèque
source ./lib/utils.sh

# Utiliser les fonctions
log_info "Démarrage du script"
check_root || exit 1
backup_with_date "/etc/config" "/backup/config"
log_info "Script terminé"
```

💡 **Fonctions avancées** :
```bash
# Fonction récursive (Fibonacci)
fib() {
    local n=$1
    if [ $n -le 1 ]; then
        echo $n
    else
        local a=$(fib $((n-1)))
        local b=$(fib $((n-2)))
        echo $((a + b))
    fi
}

# Fonction avec paramètres nommés (via options)
deploy() {
    local env=""
    local branch="main"
    
    while getopts "e:b:" opt; do
        case $opt in
            e) env=$OPTARG ;;
            b) branch=$OPTARG ;;
        esac
    done
    
    echo "Déploiement sur $env depuis $branch"
}

deploy -e production -b release

# Fonction avec validation
create_user() {
    local username=$1
    local email=$2
    
    # Validation
    [[ -z "$username" ]] && { echo "Username requis"; return 1; }
    [[ ! "$email" =~ ^[a-z0-9._%+-]+@[a-z0-9.-]+\.[a-z]{2,}$ ]] && {
        echo "Email invalide"
        return 1
    }
    
    # Création
    useradd -m -c "$email" "$username"
    return $?
}
```

🔍 **Cas d'usage professionnels** :
```bash
#!/bin/bash
# Bibliothèque de fonctions système

# Vérifier service
check_service() {
    local service=$1
    if systemctl is-active --quiet "$service"; then
        echo "✓ $service actif"
        return 0
    else
        echo "✗ $service inactif"
        return 1
    fi
}

# Installer package
install_package() {
    local package=$1
    if dpkg -l | grep -q "^ii.*$package"; then
        echo "$package déjà installé"
        return 0
    fi
    
    echo "Installation de $package..."
    apt-get install -y "$package" > /dev/null 2>&1
    return $?
}

# Créer backup incrémental
incremental_backup() {
    local source=$1
    local dest=$2
    local timestamp=$(date +%Y%m%d_%H%M%S)
    local backup_dir="${dest}/backup_${timestamp}"
    
    # Trouver le dernier backup
    local last_backup=$(ls -td ${dest}/backup_* 2>/dev/null | head -1)
    
    if [ -n "$last_backup" ]; then
        # Backup incrémental
        rsync -a --link-dest="$last_backup" "$source" "$backup_dir"
        echo "Backup incrémental créé: $backup_dir"
    else
        # Premier backup complet
        rsync -a "$source" "$backup_dir"
        echo "Backup complet créé: $backup_dir"
    fi
}

# Rotation logs
rotate_logs() {
    local logfile=$1
    local max_size=${2:-10M}  # Taille max par défaut: 10M
    
    if [ -f "$logfile" ]; then
        local size=$(du -b "$logfile" | cut -f1)
        local max_bytes=$(echo $max_size | sed 's/M/*1024*1024/' | bc)
        
        if [ $size -gt $max_bytes ]; then
            mv "$logfile" "${logfile}.$(date +%Y%m%d)"
            gzip "${logfile}.$(date +%Y%m%d)"
            touch "$logfile"
            echo "Log rotaté: $logfile"
        fi
    fi
}

# Monitoring ressources
check_resources() {
    local cpu=$(top -bn1 | grep "Cpu(s)" | awk '{print $2}' | cut -d% -f1)
    local mem=$(free | awk '/Mem:/ {printf("%.0f", $3/$2*100)}')
    local disk=$(df / | awk 'NR==2 {print $5}' | sed 's/%//')
    
    echo "CPU: ${cpu}% | MEM: ${mem}% | DISK: ${disk}%"
    
    [ ${cpu%.*} -gt 90 ] && echo "⚠️  CPU élevé"
    [ $mem -gt 90 ] && echo "⚠️  Mémoire élevée"
    [ $disk -gt 90 ] && echo "⚠️  Disque plein"
}

# Utilisation
check_service nginx
install_package git
incremental_backup /data /backups
rotate_logs /var/log/app.log 50M
check_resources
``
| Opérateur | Description | Comportement |
|-----------|-------------|--------------|
| `&&` | AND logique | Exécute cmd2 SI cmd1 réussit (exit 0) |
| `||` | OR logique | Exécute cmd2 SI cmd1 échoue (exit ≠ 0) |
| `;` | Séquentiel | Exécute cmd2 TOUJOURS (peu importe cmd1) |
| `&` | Background | Exécute cmd en arrière-plan |
| `|` | Pipe | Envoie stdout de cmd1 vers stdin de cmd2 |

💡 **Court-circuit d'évaluation** :
```bash
# AND (&&) : Arrêt si première commande échoue
mkdir /tmp/test && cd /tmp/test && touch file.txt
# Si mkdir échoue, les autres commandes ne s'exécutent PAS

# OR (||) : Arrêt si première commande réussit
cat file.txt || echo "Fichier introuvable"
# Si cat réussit, echo ne s'exécute PAS

# Séquentiel (;) : Toujours exécuté
cd /inexistant ; echo "Cette ligne s'affiche quand même"

# Background (&) : Non bloquant
sleep 60 & echo "Cette ligne s'affiche immédiatement"
```

⚠️ **Exemples pratiques** :
```bash
# Vérifier existence avant action
[ -f config.txt ] && source config.txt

# Action avec fallback
command_ok || command_fallback

# Création répertoire sécurisée
mkdir -p /backup && chmod 700 /backup

# Test connectivité
ping -c 1 server.com > /dev/null && echo "En ligne" || echo "Hors ligne"

# Enchaînement avec conditions
cd /project && git pull && npm install && npm start

# Backup avec vérification
tar -czf backup.tar.gz /data && \
    rsync backup.tar.gz remote:/backups/ && \
    echo "Backup OK" || \
    echo "ERREUR Backup"
```

💡 **Combinaisons complexes** :
```bash
# AND + OR : Pattern "try-catch"
command && echo "Succès" || echo "Échec"

# Attention à la priorité!
false || true && echo "Affiché"  # true && echo s'exécute
false && true || echo "Affiché"  # echo s'exécute

# Parenthèses pour grouper
(cd /tmp && tar -czf backup.tar.gz data) || echo "Backup échoué"
# Le cd ne change pas le répertoire du shell parent

# Multiple conditions
[ -f file1 ] && [ -r file1 ] && cat file1 || echo "Impossible de lire"

# Chaîne complexe
systemctl stop nginx && \
    cp new_config /etc/nginx/ && \
    systemctl start nginx || \
    (systemctl start nginx && echo "ERREUR: Config non appliquée")
```

✅ **Gestion d'erreurs élégante** :
```bash
#!/bin/bash

# Fonction cleanup
cleanup() {
    echo "Nettoyage..."
    rm -f /tmp/tempfile
}

# Trap pour appeler cleanup en cas d'erreur
trap cleanup EXIT

# Enchaînement avec gestion d'erreurs
mkdir -p /tmp/workspace || exit 1
cd /tmp/workspace || exit 1
wget https://example.com/data.zip || {
    echo "ERREUR: Téléchargement échoué"
    exit 1
}
unzip data.zip && echo "Extraction OK" || {
    echo "ERREUR: Extraction échouée"
    exit 1
}

# Alternative plus lisible avec fonctions
check_and_create_dir() {
    [ -d "$1" ] || mkdir -p "$1" || {
        echo "ERREUR: Impossible de créer $1"
        return 1
    }
}

check_and_create_dir "/var/backup" && echo "Répertoire OK"
```

🔍 **Cas d'usage professionnels** :
```bash
# Déploiement applicatif
#!/bin/bash
set -e  # Arrêt sur erreur

echo "=== Déploiement ==="

# Arrêt service
systemctl stop app && echo "✓ Service arrêté" || exit 1

# Backup config
cp /etc/app/config.yml /backup/ && echo "✓ Config sauvegardée" || exit 1

# Mise à jour
git pull origin main && echo "✓ Code mis à jour" || exit 1
npm install && echo "✓ Dépendances installées" || exit 1
npm run build && echo "✓ Build réussi" || exit 1

# Redémarrage
systemctl start app && echo "✓ Service démarré" || {
    echo "✗ ERREUR: Restauration config"
    cp /backup/config.yml /etc/app/
    systemctl start app
    exit 1
}

echo "=== Déploiement terminé ==="

# Script de monitoring
#!/bin/bash

# Vérifier service et alerter si besoin
systemctl is-active nginx > /dev/null || {
    echo "ALERTE: Nginx arrêté" | mail -s "Service Down" admin@company.com
    systemctl start nginx && echo "Nginx redémarré" || echo "CRITIQUE: Impossible de redémarrer"
}

# Vérification espace disque
disk_usage=$(df / | awk 'NR==2 {print $5}' | sed 's/%//')
[ $disk_usage -lt 90 ] || {
    echo "ALERTE: Disque plein à ${disk_usage}%" | mail -s "Disk Full" admin@company.com
}

# Backup conditionnel
[ -f /data/important.db ] && {
    mysqldump database > /backup/db_$(date +%Y%m%d).sql && \
        echo "Backup DB OK" || \
        echo "ERREUR Backup DB"
}

# Nettoyage logs anciens
find /var/log -name "*.log" -mtime +30 -delete && \
    echo "Logs nettoyés" || \
    echo "Erreur nettoyage"
```

💡 **Différence entre ; et &&** :
```bash
# Avec ; : La seconde commande s'exécute TOUJOURS
cd /inexistant ; ls
# Affiche erreur du cd, puis exécute ls (dans le répertoire actuel)

# Avec && : La seconde commande s'exécute SI la première réussit
cd /inexistant && ls
# Affiche seulement l'erreur du cd, ls ne s'exécute PAS

# Usage professionnel
cd /var/www/html && git pull  # Pull seulement si cd réussit
cd /tmp ; rm -f tempfile      # rm s'exécute toujours
```

⚠️ **Pièges courants** :
```bash
# ❌ PIÈGE : Mauvaise compréhension du court-circuit
false && echo "A" || echo "B"
# Affiche "B" car false échoue, echo "A" ne s'exécute pas, || prend le relais

# Pour un vrai if-then-else, utiliser if:
if false; then
    echo "A"
else
    echo "B"
fi

# ❌ PIÈGE : Background sans attendre
command1 & command2
# command2 s'exécute IMMÉDIATEMENT, sans attendre command1

# ✅ Pour attendre
command1 &
wait
command2
```
| Option | Description | Exemple |
|--------|-------------|---------|
| `-p` | Affiche un prompt | `read -p "Nom: " nom` |
| `-s` | Mode silencieux (password) | `read -s -p "Password: " pwd` |
| `-t` | Timeout en secondes | `read -t 5 -p "Choix: " choice` |
| `-n` | Lit N caractères | `read -n 1 -p "Continuer? (o/n) " rep` |
| `-r` | Désactive backslash escape | `read -r line < file.txt` |
| `-a` | Lit dans un array | `read -a array <<< "val1 val2 val3"` |
| `-d` | Délimiteur personnalisé | `read -d ':' field` |

💡 **Exemples d'entrées utilisateur** :
```bash
#!/bin/bash

# Prompt simple
read -p "Entrez votre nom: " name
echo "Bonjour, $name"

# Password (caché)
read -s -p "Entrez le mot de passe: " password
echo  # Nouvelle ligne après le password
echo "Password enregistré (masqué)"

# Avec timeout
read -t 10 -p "Appuyez sur Entrée dans 10 secondes: " response
if [ $? -eq 0 ]; then
    echo "Merci!"
else
    echo "Timeout dépassé"
fi

# Lecture d'un seul caractère (confirmation)
read -n 1 -p "Supprimer le fichier? (o/n): " confirm
echo
if [ "$confirm" = "o" ]; then
    echo "Fichier supprimé"
fi

# Valeur par défaut
read -p "Port [8080]: " port
port=${port:-8080}  # Utilise 8080 si vide
echo "Port: $port"

# Plusieurs variables simultanées
read -p "Prénom Nom: " prenom nom
echo "Prénom: $prenom, Nom: $nom"
```

⚠️ **Lecture depuis fichier : ligne par ligne** :
```bash
# Méthode 1 : Redirection avec <
while read line; do
    echo "Ligne: $line"
done < /etc/passwd

# Méthode 2 : Avec pipe (attention, sous-shell!)
cat /etc/passwd | while read line; do
    echo "Ligne: $line"
done

# Méthode 3 : Lecture robuste (préserve espaces)
while IFS= read -r line; do
    echo "Ligne: $line"
done < file.txt

# Lecture avec séparateur personnalisé
while IFS=: read -r user pass uid gid info home shell; do
    echo "User: $user, Shell: $shell"
done < /etc/passwd

# Ignorer lignes vides et commentaires
while read line; do
    # Ignorer lignes vides
    [ -z "$line" ] && continue
    # Ignorer commentaires
    [[ "$line" =~ ^#.* ]] && continue
    
    echo "Traitement: $line"
done < config.txt
```

💡 **Validation entrées utilisateur** :
```bash
#!/bin/bash

# Validation nombre
while true; do
    read -p "Entrez un nombre: " num
    if [[ "$num" =~ ^[0-9]+$ ]]; then
        echo "Nombre valide: $num"
        break
    else
        echo "ERREUR: Entrez un nombre valide"
    fi
done

# Validation email
while true; do
    read -p "Email: " email
    if [[ "$email" =~ ^[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}$ ]]; then
        echo "Email valide: $email"
        break
    else
        echo "ERREUR: Email invalide"
    fi
done

# Validation choix (menu)
while true; do
    echo "=== Menu ==="
    echo "1. Backup"
    echo "2. Restore"
    echo "3. Quitter"
    read -p "Choix [1-3]: " choice
    
    case $choice in
        1)
            echo "Lancement backup..."
            break
            ;;
        2)
            echo "Lancement restore..."
            break
            ;;
        3)
            echo "Au revoir"
            exit 0
            ;;
        *)
            echo "ERREUR: Choix invalide"
            ;;
    esac
done

# Confirmation obligatoire
read -p "Tapez 'OUI' pour confirmer la suppression: " confirm
if [ "$confirm" != "OUI" ]; then
    echo "Opération annulée"
    exit 1
fi
```

✅ **Scripts interactifs professionnels** :
```bash
#!/bin/bash
# Script d'installation interactif

echo "=== Installation Application ==="

# Lecture configuration
read -p "Nom de l'application: " app_name
read -p "Port [8080]: " port
port=${port:-8080}

read -s -p "Password admin: " admin_pass
echo

read -p "Email admin: " admin_email

# Validation
if [ -z "$app_name" ]; then
    echo "ERREUR: Nom requis"
    exit 1
fi

if ! [[ "$port" =~ ^[0-9]+$ ]]; then
    echo "ERREUR: Port invalide"
    exit 1
fi

# Confirmation
echo
echo "=== Configuration ==="
echo "Application: $app_name"
echo "Port: $port"
echo "Email: $admin_email"
echo

read -n 1 -p "Continuer? (o/n): " confirm
echo
if [ "$confirm" != "o" ]; then
    echo "Installation annulée"
    exit 0
fi

echo "Installation en cours..."
# ... commandes d'installation ...

# Script de configuration système
#!/bin/bash

read -p "Nom du serveur: " hostname
hostnamectl set-hostname "$hostname"

read -p "IP statique: " ip_address
read -p "Passerelle: " gateway
read -p "DNS: " dns

# Écrire config réseau
cat > /etc/network/interfaces <<EOF
auto eth0
iface eth0 inet static
    address $ip_address
    gateway $gateway
    dns-nameservers $dns
EOF

echo "Configuration appliquée"
```

🔍 **Lecture depuis Here Document** :
```bash
# Lire plusieurs lignes
read -r -d '' config <<'EOF'
server {
    listen 80;
    server_name example.com;
}
EOF

echo "$config" > /etc/nginx/sites-available/site.conf

# Lecture interactive multi-lignes
echo "Entrez votre message (Ctrl+D pour terminer):"
message=$(cat)
echo "Message reçu: $message"

# Read avec array
read -a words <<< "mot1 mot2 mot3"
echo "Premier mot: ${words[0]}"
echo "Tous les mots: ${words[@]}"
```

💡 **read avec process substitution** :
```bash
# Lire sortie de commande
while read line; do
    echo "Process: $line"
done < <(ps aux | grep nginx)

# Lire fichiers CSV
while IFS=, read -r name age city; do
    echo "Nom: $name, Âge: $age, Ville: $city"
done < users.csv
```
| Syntaxe | Description | Exemple |
|---------|-------------|---------|
| `$(commande)` | Moderne (recommandé) | `date=$(date +%Y-%m-%d)` |
| `` `commande` `` | Ancienne (backticks) | ``date=`date +%Y-%m-%d` `` |

💡 **Pourquoi préférer $() aux backticks ?** :
```bash
# ✅ MODERNE : $(commande) - Lisible et imbriquable
result=$(echo "Test")
files=$(ls /tmp)
date=$(date +%Y-%m-%d)

# ❌ ANCIENNE : `commande` - Moins lisible
result=`echo "Test"`
files=`ls /tmp`

# Imbrication facile avec $()
users=$(cat $(find /etc -name passwd))

# Imbrication difficile avec backticks (échappement requis)
users=`cat \`find /etc -name passwd\``  # ❌ Difficile à lire

# Multi-lignes avec $()
result=$(
    echo "Ligne 1"
    echo "Ligne 2"
    echo "Ligne 3"
)
```

⚠️ **Affectation de variables : PAS D'ESPACES** :
```bash
# ✅ CORRECT : Pas d'espaces autour du =
name="Alice"
date=$(date +%Y-%m-%d)
count=10

# ❌ ERREUR : Espaces autour du =
name = "Alice"      # ❌ Erreur de syntaxe
date = $(date)      # ❌ Erreur de syntaxe
count = 10          # ❌ Erreur de syntaxe

# Pourquoi ? Le shell interprète comme une commande:
# "name" avec arguments "=" et "Alice"
```

💡 **Exemples pratiques** :
```bash
#!/bin/bash

# Capturer date pour logs
log_date=$(date '+%Y-%m-%d %H:%M:%S')
echo "[$log_date] Démarrage du script" >> app.log

# Compter fichiers
nb_files=$(ls -1 /tmp | wc -l)
echo "Nombre de fichiers: $nb_files"

# Obtenir nom d'utilisateur
current_user=$(whoami)
echo "Utilisateur: $current_user"

# Extraire information système
kernel=$(uname -r)
hostname=$(hostname)
ip=$(hostname -I | awk '{print $1}')

# Créer nom de fichier avec timestamp
backup_file="backup_$(date +%Y%m%d_%H%M%S).tar.gz"
tar -czf "$backup_file" /data

# Résultat de calcul
total=$((10 + 20))
# Ou avec bc pour nombres à virgule
result=$(echo "10.5 + 20.3" | bc)

# Capture multi-lignes
info=$(cat <<EOF
Serveur: $(hostname)
Date: $(date)
Uptime: $(uptime)
EOF
)
echo "$info"
```

✅ **Substitutions imbriquées** :
```bash
# Fichier le plus récent dans un répertoire
latest=$(ls -t $(find /var/log -name "*.log") | head -1)

# Utilisateurs connectés avec leur shell
users_info=$(cat /etc/passwd | grep $(who | awk '{print $1}') | cut -d: -f1,7)

# Nombre de fichiers modifiés aujourd'hui
count=$(find /data -type f -mtime 0 | wc -l)
echo "Fichiers modifiés aujourd'hui: $count"

# PID d'un processus
nginx_pid=$(pgrep nginx | head -1)
if [ -n "$nginx_pid" ]; then
    echo "Nginx tourne avec PID $nginx_pid"
fi
```

🔍 **Performance et bonnes pratiques** :
```bash
# ❌ INEFFICACE : Appels multiples à la même commande
date1=$(date +%Y)
date2=$(date +%m)
date3=$(date +%d)

# ✅ OPTIMAL : Un seul appel
date_full=$(date +%Y-%m-%d)
year=${date_full:0:4}
month=${date_full:5:2}
day=${date_full:8:2}

# Ou utiliser read
read year month day < <(date +'%Y %m %d')

# ❌ Substitution inutile
useless=$(cat file.txt)
echo "$useless"

# ✅ Direct
cat file.txt

# Capturer stdout ET stderr
output=$(command 2>&1)

# Capturer seulement stdout (ignorer stderr)
output=$(command 2>/dev/null)
```

💡 **Cas d'usage professionnels** :
```bash
# Backup avec timestamp
backup_name="backup_$(hostname)_$(date +%Y%m%d_%H%M%S).tar.gz"
tar -czf "/backup/$backup_name" /data
echo "Backup créé: $backup_name" | mail -s "Backup OK" admin@company.com

# Monitoring espace disque
disk_usage=$(df -h / | awk 'NR==2 {print $5}' | sed 's/%//')
if [ $disk_usage -gt 80 ]; then
    echo "ALERTE: Disque plein à ${disk_usage}%" | mail -s "Disk Alert" admin@company.com
fi

# Vérifier service et redémarrer si besoin
if [ $(systemctl is-active nginx) != "active" ]; then
    systemctl start nginx
    echo "$(date): Nginx redémarré" >> /var/log/monitoring.log
fi

# Générer rapport système
report=$(cat <<EOF
=== Rapport Système ===
Date: $(date)
Hostname: $(hostname)
Kernel: $(uname -r)
Uptime: $(uptime -p)
Load Average: $(uptime | awk -F'load average:' '{print $2}')
Disk Usage: $(df -h / | awk 'NR==2 {print $5}')
Memory: $(free -h | awk 'NR==2 {print $3 "/" $2}')
EOF
)
echo "$report" | mail -s "Daily Report" admin@company.com
```

Q13 :
**Explication** : `read` lit stdin. Exemple : `read -p "Nom: " nom` affiche prompt.

Q14 : 
**Explication** : `&&` = ET logique (cmd2 si $?=0). `||`=OU (cmd2 si échec), `;`=séquentiel.

Q15 : 
**Explication** : `$1` dans fonction = 1er argument passé. Appel : `ma_fonction "World"`

Q16 : **B** ✅
**Explication** : Préfixe `$` obligatoire. Types automatiques ou forcés : `[int]$age = 25`

📖 **Types de données PowerShell** :

| Type | Description | Exemple |
|------|-------------|---------|
| `[string]` | Chaîne de caractères | `[string]$name = "Alice"` |
| `[int]` | Entier 32 bits | `[int]$count = 42` |
| `[long]` | Entier 64 bits | `[long]$bignum = 9999999999` |
| `[double]` | Nombre décimal | `[double]$price = 19.99` |
| `[bool]` | Booléen | `[bool]$active = $true` |
| `[datetime]` | Date/heure | `[datetime]$now = Get-Date` |
| `[array]` | Tableau | `[array]$list = @(1,2,3)` |
| `[hashtable]` | Table de hachage | `[hashtable]$config = @{}` |
| `[xml]` | Document XML | `[xml]$doc = Get-Content file.xml` |
| `[regex]` | Expression régulière | `[regex]$pattern = '\d+'` |

💡 **Portée des variables** :
```powershell
# Variable locale (défaut dans fonction)
$localVar = "Local"

# Variable globale (accessible partout)
$global:configPath = "C:\Config"

# Variable script (accessible dans le script entier)
$script:counter = 0

# Variable privée (non exportable)
$private:secret = "password"

# Exemple
function Test-Scope {
    $localVar = "Dans fonction"
    $global:sharedVar = "Partagé"
    Write-Host "Local: $localVar"
}

Test-Scope
Write-Host "Global: $global:sharedVar"
Write-Host "Local hors fonction: $localVar"  # Vide
```

⚠️ **Variables automatiques** :

| Variable | Description | Exemple |
|----------|-------------|---------|
| `$_` | Objet courant dans pipeline | `1..5 \| ForEach-Object { $_ * 2 }` |
| `$PSVersionTable` | Version PowerShell | `$PSVersionTable.PSVersion` |
| `$PSHome` | Répertoire d'installation PS | `C:\Windows\System32\WindowsPowerShell\v1.0` |
| `$HOME` | Répertoire utilisateur | `C:\Users\John` |
| `$PWD` | Répertoire courant | `C:\Scripts` |
| `$env:PATH` | Variables d'environnement | `$env:COMPUTERNAME` |
| `$args` | Arguments de fonction | Tableau d'arguments |
| `$?` | Succès dernière commande | `$true` ou `$false` |
| `$LastExitCode` | Code retour programme externe | `0` = succès |
| `$Error` | Tableau des erreurs | `$Error[0]` = dernière erreur |

💡 **Exemples d'utilisation** :
```powershell
# Types automatiques (inférence)
$name = "Alice"           # String
$age = 25                 # Int32
$price = 19.99            # Double
$active = $true           # Boolean

# Types explicites
[string]$name = "Alice"
[int]$age = 25
[datetime]$birthdate = "1990-01-15"

# Conversion automatique
[int]$number = "42"       # String → Int
[string]$text = 123       # Int → String

# Arrays
$colors = @("Rouge", "Vert", "Bleu")
$numbers = 1..10          # 1, 2, 3... 10
$mixed = @(1, "two", 3.0, $true)

# Hashtables
$user = @{
    Name = "Alice"
    Age = 25
    Role = "Admin"
}
Write-Host $user.Name     # Alice
Write-Host $user["Age"]   # 25

# Ajout dynamique
$user.Email = "alice@example.com"
$user["Phone"] = "123-456-7890"
```

✅ **Constantes et Read-Only** :
```powershell
# Variable en lecture seule
Set-Variable -Name ReadOnlyVar -Value "Cannot modify" -Option ReadOnly

# Tentative de modification → Erreur
$ReadOnlyVar = "New value"  # ❌ Erreur

# Constante (immuable)
Set-Variable -Name PI -Value 3.14159 -Option Constant

# Les constantes ne peuvent PAS être supprimées
Remove-Variable PI  # ❌ Erreur

# Variables avec description
New-Variable -Name "AppVersion" -Value "1.0.0" -Description "Version de l'application" -Option ReadOnly

# Lister variables avec options
Get-Variable | Where-Object { $_.Options -match "ReadOnly|Constant" }
```

🔍 **Manipulation avancée** :
```powershell
# Vérifier si variable existe
if (Test-Path variable:myVar) {
    Write-Host "Variable existe"
}

# Ou avec Get-Variable
if (Get-Variable -Name myVar -ErrorAction SilentlyContinue) {
    Write-Host "Variable existe"
}

# Supprimer variable
Remove-Variable -Name tempVar

# Lister toutes les variables
Get-Variable

# Variables d'environnement
$env:COMPUTERNAME
$env:USERNAME
$env:PATH

# Modifier variable d'environnement
$env:MY_VAR = "CustomValue"

# Variables d'environnement permanentes (registre)
[System.Environment]::SetEnvironmentVariable("MY_VAR", "Value", "User")
[System.Environment]::SetEnvironmentVariable("MY_VAR", "Value", "Machine")  # Admin requis
```

💡 **Cas d'usage professionnels** :
```powershell
# Configuration centralisée
$global:Config = @{
    ServerName = "PROD-SQL01"
    DatabaseName = "AppDB"
    BackupPath = "\\backup\sql"
    RetentionDays = 30
}

function Connect-Database {
    $connString = "Server=$($global:Config.ServerName);Database=$($global:Config.DatabaseName)"
    # ... connexion
}

# Compteur global
$script:ProcessedFiles = 0

function Process-File {
    param($FilePath)
    # Traitement...
    $script:ProcessedFiles++
}

Get-ChildItem C:\Data -Filter *.txt | ForEach-Object {
    Process-File $_.FullName
}
Write-Host "Fichiers traités: $script:ProcessedFiles"

# Types pour validation
function Set-UserAge {
    param(
        [Parameter(Mandatory=$true)]
        [ValidateRange(0,150)]
        [int]$Age
    )
    
    Write-Host "Âge défini: $Age"
}

Set-UserAge -Age 25    # ✅ OK
Set-UserAge -Age 200   # ❌ Erreur validation

# DateTime
$now = Get-Date
$yesterday = $now.AddDays(-1)
$formatted = $now.ToString("yyyy-MM-dd HH:mm:ss")

Write-Host "Maintenant: $formatted"
Write-Host "Il y a une semaine: $($now.AddDays(-7))"

# Filtrer fichiers par date
Get-ChildItem C:\Logs | Where-Object {
    $_.LastWriteTime -gt $yesterday
}
```

⚠️ **Pièges courants** :
```powershell
# ❌ Variable non initialisée (null par défaut)
$count++  # Fonctionne mais part de $null → 1

# ✅ Initialiser explicitement
$count = 0
$count++

# ❌ Confusion string/int
$a = "10"
$b = "20"
$result = $a + $b  # "1020" (concaténation)

# ✅ Conversion explicite
[int]$a = "10"
[int]$b = "20"
$result = $a + $b  # 30 (addition)

# ❌ Modificateur de scope oublié
function Set-GlobalConfig {
    $config = "Value"  # Variable locale uniquement!
}

# ✅ Utiliser $global: pour persister
function Set-GlobalConfig {
    $global:config = "Value"
}
```

Q17 : **B** ✅
**Explication** : Convention : `Get-Service`, `Set-Location`, `Remove-Item`. Verbes approuvés.

📖 **L**B** ✅
**Explication** : `|` envoie objets. `$_` = objet courant. `.CPU` = propriété.

📖 **Différence fondamentale : Objets vs Texte** :

| PowerShell | Bash |
|------------|------|
| **Objets** avec propriétés/méthodes | **Texte** (strings) |
| Manipulation structurée | Parsing nécessaire |
| `Get-Process \| Where-Object CPU -gt 50` | `ps aux \| awk '$3 > 50'` |
| Accès direct : `$_.Name`, `$_.CPU` | Extraction : `cut`, `awk`, `sed` |

💡 **Membres d'objets : Get-Member** :
```powershell
# Explorer les propriétés et méthodes
Get-Process | Get-Member

# Résultat montre :
# - Properties: Name, Id, CPU, WorkingSet, etc.
# - Methods: Kill(), Refresh(), WaitForExit(), etc.

# Filtrer par type de membre
Get-Service | Get-Member -MemberType Property
Get-Service | Get-Member -MemberType Method

# Exemples d'utilisation
$process = Get-Process -Name "notepad"
$process.Id          # Propriété: ID du processus
$process.CPU         # Propriété: Temps CPU
$process.Kill()      # Méthode: Arrêter le processus

# String methods
$text = "PowerShell"
$text.ToUpper()      # POWERSHELL
$text.Substring(0,5) # Power
$text.Length         # 10
```

⚠️ **Filtrage avec Where-Object** :
```powershell
# Syntaxe complète
Get-Process | Where-Object { $_.CPU -gt 50 }

# Syntaxe simplifiée (PowerShell 3.0+)
Get-Process | Where-Object CPU -gt 50

# Filtres multiples
Get-Process | Where-Object { $_.CPU -gt 50 -and $_.WS -gt 100MB }

# Alias
Get-Process | Where CPU -gt 50
Get-Process | ? { $_.CPU -gt 50 }  # ? = alias

# Exemples pratiques
# Processus utilisant > 100MB RAM
Get-Process | Where-Object { $_.WorkingSet -gt 100MB }

# Services arrêtés
Get-Service | Where-Object { $_.Status -eq "Stopped" }

# Fichiers modifiés dans les 7 derniers jours
Get-ChildItem C:\Data | Where-Object { $_.LastWriteTime -gt (Get-Date).AddDays(-7) }

# Utilisateurs avec nom commençant par 'A'
Get-ADUser -Filter * | Where-Object { $_.Name -like "A*" }

# Filtres complexes
Get-Process | Where-Object {
    $_.CPU -gt 10 -and 
    $_.WorkingSet -gt 50MB -and 
    $_.Name -notlike "System*"
}
```

💡 **Transformation avec Select-Object** :
```powershell
# Sélectionner propriétés spécifiques
Get-Process | Select-Object Name, Id, CPU

# Sélectionner premières/dernières entrées
Get-Process | Select-Object -First 10
Get-Process | Select-Object -Last 5

# Skip (ignorer)
Get-Process | Select-Object -Skip 5 -First 10

# Propriétés uniques
Get-Process | Select-Object -Property ProcessName -Unique

# Propriétés calculées
Get-Process | Select-Object Name, 
    @{Name="CPU_Seconds";Expression={$_.CPU}},
    @{Name="Memory_MB";Expression={[math]::Round($_.WS/1MB,2)}}

# Expansion de propriétés (obtenir valeur directe)
(Get-Process -Name "powershell").Id  # Retourne objet
Get-Process -Name "powershell" | Select-Object -ExpandProperty Id  # Retourne valeur
```

✅ **ForEach-Object (transformation)** :
```powershell
# Syntaxe complète
Get-ChildItem *.txt | ForEach-Object { $_.Name.ToUpper() }

# Syntaxe simplifiée
Get-ChildItem *.txt | ForEach-Object { $_.Name.ToUpper() }

# Alias
Get-ChildItem *.txt | foreach { $_.Name }
Get-ChildItem *.txt | % { $_.Name }  # % = alias

# Exemples pratiques
# Arrêter tous les processus notepad
Get-Process notepad | ForEach-Object { $_.Kill() }

# Redémarrer tous les services IIS
Get-Service | Where-Object { $_.Name -like "*IIS*" } | ForEach-Object {
    Restart-Service -Name $_.Name
    Write-Host "Service $($_.Name) redémarré"
}

# Créer plusieurs fichiers
1..10 | ForEach-Object { New-Item "file$_.txt" }

# Modifier permissions sur fichiers
Get-ChildItem C:\Data *.txt | ForEach-Object {
    $acl = Get-Acl $_.FullName
    $acl.SetAccessRule($rule)
    Set-Acl $_.FullName $acl
}

# Traitement par batch avec progression
$files = Get-ChildItem C:\Logs
$total = $files.Count
$current = 0

$files | ForEach-Object {
    $current++
    Write-Progress -Activity "Traitement" -PercentComplete (($current/$total)*100)
    # Traitement du fichier
    Compress-Archive -Path $_.FullName -DestinationPath "$($_.FullName).zip"
}
```

🔍 **Pipeline avancé** :
```powershell
# Chaînage complexe
Get-Process | 
    Where-Object { $_.CPU -gt 10 } | 
    Select-Object Name, CPU, @{N="Memory_MB";E={$_.WS/1MB}} | 
    Sort-Object CPU -Descending | 
    Format-Table -AutoSize

# Grouper objets
Get-Process | Group-Object -Property ProcessName | 
    Select-Object Name, Count

# Mesurer/Statistiques
Get-ChildItem C:\Data | Measure-Object -Property Length -Sum -Average -Maximum

# Trier
Get-Process | Sort-Object CPU -Descending | Select-Object -First 10

# Export vers formats
Get-Process | Export-Csv processes.csv -NoTypeInformation
Get-Process | ConvertTo-Json | Out-File processes.json
Get-Service | Export-Clixml services.xml

# Pipeline avec plusieurs transformations
Get-EventLog -LogName System -Newest 100 |
    Where-Object { $_.EntryType -eq "Error" } |
    Select-Object TimeGenerated, Source, Message |
    Sort-Object TimeGenerated -Descending |
    Export-Csv errors.csv -NoTypeInformation
```

💡 **Cas d'usage professionnels** :
```powershell
# Monitoring CPU/RAM
Get-Process | 
    Where-Object { $_.CPU -gt 50 -or $_.WS -gt 500MB } |
    Select-Object Name, Id, 
        @{N="CPU(%)";E={$_.CPU}},
        @{N="Memory(MB)";E={[math]::Round($_.WS/1MB,2)}} |
    Sort-Object "CPU(%)" -Descending |
    Format-Table -AutoSize

# Rapport services critiques
$criticalServices = @("Spooler", "W32Time", "WinRM")
Get-Service | 
    Where-Object { $_.Name -in $criticalServices } |
    Select-Object Name, Status, StartType |
    ForEach-Object {
        if ($_.Status -ne "Running") {
            Write-Warning "⚠️ Service $($_.Name) arrêté!"
            Start-Service -Name $_.Name
        }
    }

# Nettoyage logs anciens
Get-ChildItem C:\Logs -Filter *.log |
    Where-Object { $_.LastWriteTime -lt (Get-Date).AddDays(-30) } |
    ForEach-Object {
        Write-Host "Suppression: $($_.Name)"
        Remove-Item $_.FullName -Force
    }

# Inventaire disques avec alerte
Get-PSDrive -PSProvider FileSystem |
    Where-Object { $_.Free -ne $null } |
    Select-Object Name, 
        @{N="Used(GB)";E={[math]::Round($_.Used/1GB,2)}},
        @{N="Free(GB)";E={[math]::Round($_.Free/1GB,2)}},
        @{N="Total(GB)";E={[math]::Round(($_.Used+$_.Free)/1GB,2)}},
        @{N="Free(%)";E={[math]::Round($_.Free/($_.Used+$_.Free)*100,2)}} |
    ForEach-Object {
        if ($_."Free(%)" -lt 10) {
            Write-Warning "⚠️ Disque $($_.Name) critique: $($_.'Free(%)')% libre"
        }
        $_
    } | Format-Table -AutoSize

# Analyse utilisateurs AD par département
Get-ADUser -Filter * -Properties Department |
    Where-Object { $_.Enabled -eq $true } |
    Group-Object -Property Department |
    Select-Object Name, Count |
    Sort-Object Count -Descending |
    Export-Csv users_by_dept.csv -NoTypeInformation
```

⚠️ **Pièges courants** :
```powershell
# ❌ Tenter de manipuler comme texte
Get-Process | Select-String "powershell"  # Erreur!

# ✅ Utiliser Where-Object sur propriétés
Get-Process | Where-Object { $_.Name -like "*powershell*" }

# ❌ Oublier que les objets ont des types
$processes = Get-Process
$processes[0] + $processes[1]  # Erreur : on n'additionne pas des Process!

# ✅ Additionner des propriétés
($processes | Measure-Object -Property CPU -Sum).Sum

# Performance : Where-Object vs .Where()
# ❌ Lent sur grandes collections
Get-Process | Where-Object { $_.CPU -gt 10 }

# ✅ Plus rapide (méthode intrinsèque)
(Get-Process).Where({ $_.CPU -gt 10 })
```
| Verbe | Usage | Exemples |
|-------|-------|----------|
| `Get` | Récupérer données | Get-Process, Get-Service, Get-ChildItem |
| `Set` | Modifier données | Set-Location, Set-Content, Set-ExecutionPolicy |
| `New` | Créer nouvel objet | New-Item, New-Service, New-Object |
| `Remove` | Supprimer objet | Remove-Item, Remove-Service |
| `Add` | Ajouter élément | Add-Content, Add-Member |
| `Clear` | Effacer contenu | Clear-Content, Clear-History |
| `Copy` | Copier | Copy-Item |
| `Move` | Déplacer | Move-Item |
| `Rename` | Renommer | Rename-Item |
| `Start` | Démarrer | Start-Process, Start-Service |
| `Stop` | Arrêter | Stop-Process, Stop-Service |
| `Restart` | Redémarrer | Restart-Service, Restart-Computer |
| `Test` | Vérifier/Tester | Test-Path, Test-Connection |
| `Import` | Importer | Import-Module, Import-Csv |
| `Export` | Exporter | Export-Csv, Export-Clixml |
| `Convert` | Convertir | ConvertTo-Json, ConvertFrom-Csv |

💡 **Voir tous les verbes approuvés** :
```powershell
# Lister tous les verbes
Get-Verb

# Filtrer par catégorie
Get-Verb | Where-Object { $_.Group -eq "Common" }
Get-Verb | Where-Object { $_.Group -eq "Data" }
Get-Verb | Where-Object { $_.Group -eq "Lifecycle" }

# Trouver cmdlets utilisant un verbe spécifique
Get-Command -Verb Get
Get-Command -Verb Set
Get-Command -Verb New

# Chercher par nom
Get-Command -Noun Service
Get-Command -Noun Process
```

⚠️ **Conventions de nommage** :
```powershell
# ✅ CORRECT : Verbe-Nom en PascalCase
Get-ChildItem
Set-ExecutionPolicy
New-ADUser
Remove-Item

# ❌ INCORRECT : Verbe non approuvé
Fetch-Data      # Utiliser Get-Data
Delete-File     # Utiliser Remove-File
Create-User     # Utiliser New-User

# Validation lors de la création de fonction
function Get-SystemInfo {  # ✅ Verbe approuvé
    # ...
}

function Fetch-SystemInfo {  # ⚠️ Warning : Verbe non approuvé
    # ...
}

# Utiliser Write au lieu de Print
Write-Host "Message"      # ✅
Print "Message"           # ❌ N'existe pas en PowerShell
```

💡 **Alias vs Cmdlets complets** :

| Alias | Cmdlet complet | Origine |
|-------|----------------|---------|
| `ls`, `dir` | Get-ChildItem | Compatibilité cmd/bash |
| `cd`, `chdir` | Set-Location | Compatibilité cmd |
| `pwd` | Get-Location | Compatibilité bash |
| `cat`, `type` | Get-Content | Compatibilité cmd/bash |
| `cp`, `copy` | Copy-Item | Compatibilité cmd/bash |
| `mv`, `move` | Move-Item | Compatibilité cmd/bash |
| `rm`, `del` | Remove-Item | Compatibilité cmd/bash |
| `ps` | Get-Process | Compatibilité bash |
| `kill` | Stop-Process | Compatibilité bash |
| `echo`, `write` | Write-Output | Compatibilité cmd/bash |
| `select` | Select-Object | Raccourci |
| `where` | Where-Object | Raccourci |
| `foreach` | ForEach-Object | Raccourci |
| `%` | ForEach-Object | Raccourci |
| `?` | Where-Object | Raccourci |

💡 **Gestion des alias** :
```powershell
# Voir tous les alias
Get-Alias

# Trouver alias d'une cmdlet
Get-Alias -Definition Get-ChildItem
# Résultat: dir, ls, gci

# Trouver cmdlet d'un alias
Get-Alias ls
# Résultat: Get-ChildItem

# Créer alias temporaire
Set-Alias -Name ll -Value Get-ChildItem
New-Alias -Name edit -Value notepad.exe

# Supprimer alias
Remove-Alias -Name ll

# Alias dans le profile (permanent)
# Fichier: $PROFILE
Set-Alias -Name np -Value "C:\Program Files\Notepad++\notepad++.exe"
Set-Alias -Name py -Value python

# Fonction avec alias
function Get-QuickSystemInfo { Get-ComputerInfo }
Set-Alias -Name sysinfo -Value Get-QuickSystemInfo
```

✅ **Créer ses propres cmdlets** :
```powershell
# Fonction avec verbe approuvé
function Get-DiskSpace {
    param(
        [Parameter(Mandatory=$false)]
        [string]$DriveLetter = "C"
    )
    
    Get-PSDrive $DriveLetter | Select-Object Name, 
        @{N="Used(GB)";E={[math]::Round($_.Used/1GB,2)}},
        @{N="Free(GB)";E={[math]::Round($_.Free/1GB,2)}}
}

# Fonction avec validation et help
function Set-ServiceStatus {
    <#
    .SYNOPSIS
        Modifie l'état d'un service
    .DESCRIPTION
        Démarre ou arrête un service Windows
    .PARAMETER ServiceName
        Nom du service
    .PARAMETER Action
        Action à effectuer (Start ou Stop)
    .EXAMPLE
        Set-ServiceStatus -ServiceName "Spooler" -Action Start
    #>
    [CmdletBinding()]
    param(
        [Parameter(Mandatory=$true)]
        [string]$ServiceName,
        
        [Parameter(Mandatory=$true)]
        [ValidateSet("Start","Stop")]
        [string]$Action
    )
    
    $service = Get-Service -Name $ServiceName
    
    if ($Action -eq "Start") {
        Start-Service -Name $ServiceName
    } else {
        Stop-Service -Name $ServiceName
    }
}

# Module personnalisé (.psm1)
# Fichier: MyModule.psm1
function Get-UserInfo {
    param([string]$Username)
    Get-ADUser -Identity $Username -Properties *
}

function New-UserAccount {
    param(
        [string]$Username,
        [string]$FullName,
        [securestring]$Password
    )
    New-ADUser -Name $Username -DisplayName $FullName -AccountPassword $Password
}

Export-ModuleMember -Function Get-UserInfo, New-UserAccount

# Utilisation
Import-Module .\MyModule.psm1
Get-UserInfo -Username "john.doe"
```

🔍 **Cas d'usage professionnels** :
```powershell
# Bibliothèque de fonctions métier
function Get-ServerHealth {
    [CmdletBinding()]
    param(
        [Parameter(Mandatory=$true)]
        [string]$ComputerName
    )
    
    $cpu = Get-Counter "\\$ComputerName\Processor(_Total)\% Processor Time"
    $mem = Get-Counter "\\$ComputerName\Memory\Available MBytes"
    
    [PSCustomObject]@{
        Server = $ComputerName
        CPU_Percent = [math]::Round($cpu.CounterSamples.CookedValue, 2)
        Memory_Free_MB = [math]::Round($mem.CounterSamples.CookedValue, 2)
        Timestamp = Get-Date
    }
}

# Script de maintenance
function Start-MaintenanceMode {
    [CmdletBinding(SupportsShouldProcess)]
    param(
        [Parameter(Mandatory=$true)]
        [string[]]$Services
    )
    
    foreach ($service in $Services) {
        if ($PSCmdlet.ShouldProcess($service, "Stop Service")) {
            Stop-Service -Name $service
            Write-Verbose "Service $service arrêté"
        }
    }
}

# Fonction avec progression
function Copy-LargeFiles {
    param(
        [string]$Source,
        [string]$Destination
    )
    
    $files = Get-ChildItem -Path $Source -Recurse -File
    $total = $files.Count
    $current = 0
    
    foreach ($file in $files) {
        $current++
        Write-Progress -Activity "Copie en cours" `
                       -Status "Fichier $current sur $total" `
                       -PercentComplete (($current / $total) * 100)
        
        Copy-Item -Path $file.FullName -Destination $Destination
    }
}

# Tests unitaires (Pester)
Describe "Get-DiskSpace" {
    It "Retourne un objet avec les propriétés attendues" {
        $result = Get-DiskSpace -DriveLetter C
        $result | Should -HaveProperty Name
        $result | Should -HaveProperty 'Used(GB)'
        $result | Should -HaveProperty 'Free(GB)'
    }
}
```

⚠️ **Bonnes pratiques** :
```powershell
# ✅ Utiliser cmdlets complets dans scripts (lisibilité)
Get-ChildItem -Path C:\Logs -Filter *.log

# ✅ Alias OK pour ligne de commande interactive
ls C:\Logs *.log

# ✅ Fonctions avec verbes approuvés
function Get-LogFiles { }
function Set-Configuration { }
function Test-NetworkConnection { }

# ✅ Help documentation
function Get-CustomData {
    <#
    .SYNOPSIS
        Description courte
    .DESCRIPTION
        Description détaillée
    .PARAMETER Name
        Description du paramètre
    .EXAMPLE
        Get-CustomData -Name "Test"
    #>
}
```

Q18 : 
**Explication** : `|` envoie objets. `$_` = objet courant. `.CPU` = propriété.

Q19 : **B** ✅
**Explication** : Compatibilité cmd/bash. `dir`=Windows, `ls`=Unix. `Get-Alias` liste tous.

📖 **Tableau des alias courants** :

| Alias | Cmdlet | Origine | Utilisation |
|-------|--------|---------|-------------|
| `ls`, `dir`, `gci` | Get-ChildItem | Unix/CMD/PS | Lister fichiers |
| `cd`, `chdir`, `sl` | Set-Location | CMD/PS | Changer répertoire |
| `pwd`, `gl` | Get-Location | Unix/PS | Répertoire courant |
| `cat`, `type`, `gc` | Get-Content | Unix/CMD/PS | Lire fichier |
| `cp`, `copy`, `cpi` | Copy-Item | Unix/CMD/PS | Copier |
| `mv`, `move`, `mi` | Move-Item | Unix/CMD/PS | Déplacer |
| `rm`, `del`, `erase`, `rd`, `rmdir`, `ri` | Remove-Item | Unix/CMD/PS | Supprimer |
| `md`, `mkdir` | New-Item -ItemType Directory | CMD | Créer dossier |
| `ps`, `gps` | Get-Process | Unix/PS | Lister processus |
| `kill`, `spps` | Stop-Process | Unix/PS | Arrêter processus |
| `echo`, `write` | Write-Output | CMD/Unix | Afficher |
| `cls`, `clear` | Clear-Host | CMD/Unix | Effacer écran |
| `man`, `help` | Get-Help | Unix | Aide |
| `history`, `h` | Get-History | Unix/PS | Historique |
| `select` | Select-Object | PS | Sélectionner |
| `where`, `?` | Where-Object | PS | Filtrer |
| `foreach`, `%` | ForEach-Object | PS | Itérer |
| `sort` | Sort-Object | Unix | Trier |
| `measure` | Measure-Object | PS | Mesurer |

💡 **Gestion des alias** :
```powershell
# Lister tous les alias
Get-Alias

# Lister par ordre alphabétique
Get-Alias | Sort-Object Name

# Chercher alias d'une cmdlet
Get-Alias -Definition Get-ChildItem
# Résultat: dir, gci, ls

# Chercher cmdlet d'un alias
Get-Alias -Name ls
# Résultat: Get-ChildItem

# Voir toutes les commandes disponibles pour une action
Get-Command *item*
Get-Command *service*

# Filtrer par verbe
Get-Command -Verb Get
Get-Command -Noun Service
```

⚠️ **Créer alias personnalisés** :
```powershell
# Alias temporaire (session courante)
Set-Alias -Name np -Value "C:\Program Files\Notepad++\notepad++.exe"
Set-Alias -Name ll -Value Get-ChildItem
Set-Alias -Name py -Value python

# Utilisation
np script.ps1  # Ouvre avec Notepad++
ll C:\Scripts  # Liste fichiers

# Supprimer alias
Remove-Alias -Name ll

# ⚠️ Ne peut pas utiliser d'alias pour cmdlet avec paramètres
# ❌ Cela ne marche pas :
Set-Alias -Name log -Value "Get-EventLog -LogName System"

# ✅ Utiliser une fonction à la place
function log { Get-EventLog -LogName System }
```

💡 **Alias dans le profile PowerShell** :
```powershell
# Trouver le fichier profile
$PROFILE
# Exemple: C:\Users\John\Documents\WindowsPowerShell\Microsoft.PowerShell_profile.ps1

# Créer le profile s'il n'existe pas
if (!(Test-Path $PROFILE)) {
    New-Item -Path $PROFILE -ItemType File -Force
}

# Éditer le profile
notepad $PROFILE

# Contenu du profile (exemples d'alias permanents)
# ============================================

# Alias pour applications
Set-Alias -Name np -Value "C:\Program Files\Notepad++\notepad++.exe"
Set-Alias -Name code -Value "C:\Program Files\VS Code\Code.exe"
Set-Alias -Name chrome -Value "C:\Program Files\Google\Chrome\Application\chrome.exe"

# Fonctions personnalisées
function ll { Get-ChildItem | Format-Table -AutoSize }
function la { Get-ChildItem -Force }  # Inclure fichiers cachés
function lsd { Get-ChildItem -Directory }  # Seulement dossiers
function home { Set-Location $HOME }
function docs { Set-Location "$HOME\Documents" }
function downloads { Set-Location "$HOME\Downloads" }

# Fonctions réseau
function ipconfig-quick { ipconfig | Select-String "IPv4","Default Gateway" }
function myip { (Invoke-WebRequest -Uri "http://ifconfig.me/ip").Content }
function pingtest { Test-Connection -ComputerName google.com -Count 2 }

# Fonctions système
function Update-Profile { . $PROFILE }
function Edit-Profile { notepad $PROFILE }
function Get-DiskSpace { 
    Get-PSDrive | Where-Object {$_.Free -ne $null} | 
    Select-Object Name, @{N="Used(GB)";E={[math]::Round($_.Used/1GB,2)}}, 
                       @{N="Free(GB)";E={[math]::Round($_.Free/1GB,2)}}
}

# Alias Git (si utilisé)
function gs { git status }
function ga { git add --all }
function gc { param($message) git commit -m $message }
function gp { git push }
function gl { git log --oneline --graph --decorate }

# ============================================

# Recharger le profile sans redémarrer
. $PROFILE
```

✅ **Compatibilité cmd/Unix** :
```powershell
# Ces commandes fonctionnent grâce aux alias

# Style Unix
ls -la /home/user
cat file.txt | grep "error"
ps | grep "chrome"
rm -rf folder/

# Style Windows CMD
dir C:\Windows
type file.txt
del file.txt
cls

# PowerShell natif (recommandé dans scripts)
Get-ChildItem -Path C:\Windows -Force
Get-Content -Path file.txt
Remove-Item -Path file.txt -Force
Clear-Host
```

🔍 **Différences importantes** :
```powershell
# ❌ Alias ne sont PAS identiques aux commandes Unix/CMD
# Exemple: ls avec paramètres Unix
ls -lah  # ❌ Erreur en PowerShell

# ✅ Utiliser paramètres PowerShell
Get-ChildItem -Force  # Équivalent de -a (all)
Get-ChildItem -Recurse  # Équivalent de -R

# Grep n'existe pas nativement
cat file.txt | grep "pattern"  # ❌ grep n'existe pas

# ✅ Utiliser Select-String
Get-Content file.txt | Select-String "pattern"

# Ou fonction dans profile
function grep { param($pattern) Select-String -Pattern $pattern }
```

💡 **Cas d'usage professionnels** :
```powershell
# Profile d'administrateur système
# ==================================

# Navigation rapide
function scripts { Set-Location "C:\Scripts" }
function logs { Set-Location "C:\Windows\Logs" }
function iis { Set-Location "C:\inetpub\wwwroot" }

# Vérifications système rapides
function Check-Services {
    $critical = @("Spooler", "W32Time", "WinRM", "MSSQLSERVER")
    Get-Service $critical | Format-Table Name, Status, StartType
}

function Check-Disks {
    Get-PSDrive -PSProvider FileSystem | 
    Where-Object {$_.Free -ne $null} |
    ForEach-Object {
        $pct = [math]::Round($_.Free/($_.Used+$_.Free)*100,2)
        if ($pct -lt 10) { $color = "Red" }
        elseif ($pct -lt 20) { $color = "Yellow" }
        else { $color = "Green" }
        
        Write-Host "$($_.Name): ${pct}% libre" -ForegroundColor $color
    }
}

# Raccourcis administration
function Restart-IIS { iisreset }
function Get-LastErrors { 
    Get-EventLog -LogName System -EntryType Error -Newest 10 
}
function Get-FailedLogins {
    Get-EventLog -LogName Security | 
    Where-Object {$_.EventID -eq 4625} | 
    Select-Object -First 20
}

# Fonctions réseau
function Test-Ports {
    param([string]$Computer)
    @(80, 443, 3389, 22, 21) | ForEach-Object {
        Test-NetConnection -ComputerName $Computer -Port $_
    }
}

# Backup rapide
function Backup-Important {
    $date = Get-Date -Format "yyyyMMdd_HHmmss"
    $dest = "D:\Backups\$date"
    New-Item -Path $dest -ItemType Directory
    
    Copy-Item "C:\Scripts" $dest -Recurse
    Copy-Item "C:\Config" $dest -Recurse
    
    Write-Host "Backup créé: $dest" -ForegroundColor Green
}

# ==================================

# Alias pour déploiement
Set-Alias -Name deploy -Value "C:\Scripts\Deploy-Application.ps1"
Set-Alias -Name monitor -Value "C:\Scripts\Monitor-System.ps1"
```

⚠️ **Bonnes pratiques** :
```powershell
# ✅ Dans scripts: Utiliser cmdlets complets
Get-ChildItem -Path C:\Data -Recurse
Get-Content -Path log.txt | Select-String "ERROR"

# ✅ En interactif: Alias OK pour rapidité
ls C:\Data -r
cat log.txt | sls "ERROR"

# ✅ Documenter les alias personnalisés
# Dans profile, ajouter commentaires
# Alias pour Notepad++
Set-Alias np "C:\Program Files\Notepad++\notepad++.exe"

# ❌ Éviter d'overrider alias standards
# Set-Alias -Name ls -Value "MyCustomFunction"  # Confusant!
```

Q20 : **B** ✅
**Explication** : `-eq`=égal, `-ne`≠, `-gt`>, `-lt`<. Ajout `c` = case sensitive : `-ceq`

📖 **Tableau COMPLET opérateurs de comparaison** :

**Comparaisons de base (insensibles à la casse par défaut)** :

| Opérateur | Description | Exemple |
|-----------|-------------|---------|
| `-eq` | Égal | `5 -eq 5` → $true |
| `-ne` | Différent | `5 -ne 3` → $true |
| `-gt` | Strictement supérieur | `5 -gt 3` → $true |
| `-ge` | Supérieur ou égal | `5 -ge 5` → $true |
| `-lt` | Strictement inférieur | `3 -lt 5` → $true |
| `-le` | Inférieur ou égal | `3 -le 5` → $true |

**Versions sensibles à la casse (ajout du préfixe 'c')** :

| Opérateur | Description | Exemple |
|-----------|-------------|---------|
| `-ceq` | Égal (case sensitive) | `"Hello" -ceq "hello"` → $false |
| `-cne` | Différent (case sensitive) | `"Hello" -cne "hello"` → $true |
| `-cgt`, `-cge`, `-clt`, `-cle` | Comparaisons alphabétiques sensibles | |

**Opérateurs de chaînes** :

| Opérateur | Description | Exemple |
|-----------|-------------|---------|
| `-like` | Pattern matching (wildcards) | `"PowerShell" -like "Power*"` → $true |
| `-clike` | Pattern matching (case sensitive) | `"PowerShell" -clike "power*"` → $false |
| `-notlike` | Négation de -like | `"PowerShell" -notlike "Bash*"` → $true |
| `-match` | Regex matching | `"test123" -match "\d+"` → $true |
| `-cmatch` | Regex (case sensitive) | `"Test" -cmatch "test"` → $false |
| `-notmatch` | Négation de -match | `"PowerShell" -notmatch "\d+"` → $true |
| `-contains` | Collection contient élément | `@(1,2,3) -contains 2` → $true |
| `-notcontains` | Collection ne contient pas | `@(1,2,3) -notcontains 5` → $true |
| `-in` | Élément dans collection | `2 -in @(1,2,3)` → $true |
| `-notin` | Élément pas dans collection | `5 -notin @(1,2,3)` → $true |

💡 **Exemples comparaisons numériques** :
```powershell
# Comparaisons simples
$age = 25

if ($age -ge 18) {
    Write-Host "Majeur"
}

if ($age -gt 18 -and $age -lt 65) {
    Write-Host "Âge actif"
}

# Avec variables
$count = 10
$max = 20

if ($count -le $max) {
    Write-Host "Dans les limites"
}

# Comparaisons dans Where-Object
Get-Process | Where-Object { $_.CPU -gt 50 }
Get-ChildItem | Where-Object { $_.Length -gt 1MB }
Get-Service | Where-Object { $_.Status -eq "Running" }
```

⚠️ **Sensibilité à la casse** :
```powershell
# Par défaut : INSENSIBLE à la casse
"Hello" -eq "hello"    # $true
"PowerShell" -eq "powershell"  # $true

# Avec 'c' : SENSIBLE à la casse
"Hello" -ceq "hello"   # $false
"PowerShell" -ceq "PowerShell"  # $true

# Application pratique
$username = "Admin"

# Insensible (défaut)
if ($username -eq "admin") {
    Write-Host "C'est un admin"  # S'exécute!
}

# Sensible
if ($username -ceq "admin") {
    Write-Host "C'est un admin"  # Ne s'exécute PAS
}

# Utile pour validation stricte
$password = "SecurePass123"
if ($password -ceq "SecurePass123") {
    Write-Host "Mot de passe correct"
}
```

💡 **Opérateurs -like et wildcards** :
```powershell
# Wildcards: * (plusieurs caractères), ? (un caractère)

# -like avec *
"PowerShell" -like "Power*"     # $true
"PowerShell" -like "*Shell"     # $true
"PowerShell" -like "*wer*"      # $true

# -like avec ?
"test.txt" -like "test.???"     # $true
"test.txt" -like "t??t.txt"     # $true

# Exemples pratiques
# Filtrer fichiers
Get-ChildItem | Where-Object { $_.Name -like "*.log" }
Get-ChildItem | Where-Object { $_.Name -like "backup_*" }

# Vérifier noms
$serviceName = "WinRM"
if ($serviceName -like "Win*") {
    Write-Host "Service Windows"
}

# Multiple patterns
$file = "document.docx"
if ($file -like "*.doc" -or $file -like "*.docx") {
    Write-Host "Document Word"
}
```

✅ **Opérateurs -match et regex** :
```powershell
# -match utilise les expressions régulières

# Exemples basiques
"test123" -match "\d+"          # $true (contient chiffres)
"user@example.com" -match "@"   # $true
"192.168.1.1" -match "\d+\.\d+\.\d+\.\d+"  # $true

# Extraction avec $Matches
"Error: Code 404" -match "Code (\d+)"
$Matches[0]  # "Code 404" (match complet)
$Matches[1]  # "404" (premier groupe capturé)

# Exemples pratiques
# Valider email
$email = "user@example.com"
if ($email -match "^[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}$") {
    Write-Host "Email valide"
}

# Extraire IP d'un log
$log = "Connection from 192.168.1.100 at 10:30"
if ($log -match "\d+\.\d+\.\d+\.\d+") {
    $ip = $Matches[0]
    Write-Host "IP trouvée: $ip"
}

# Filtrer logs
Get-Content app.log | Where-Object { $_ -match "ERROR|FATAL" }

# Valider format
$code = "ABC-123"
if ($code -match "^[A-Z]{3}-\d{3}$") {
    Write-Host "Code valide"
}
```

🔍 **Opérateurs -contains et -in** :
```powershell
# -contains : Collection CONTIENT élément
$numbers = @(1, 2, 3, 4, 5)
$numbers -contains 3  # $true
$numbers -contains 10  # $false

# -in : Élément DANS collection (ordre inversé)
3 -in $numbers  # $true
10 -in $numbers  # $false

# Exemples pratiques
# Vérifier appartenance
$allowedUsers = @("admin", "user1", "user2")
$currentUser = "user1"

if ($currentUser -in $allowedUsers) {
    Write-Host "Accès autorisé"
}

# Vérifier service critique
$criticalServices = @("W32Time", "WinRM", "MSSQLSERVER")
Get-Service | Where-Object {
    $_.Name -in $criticalServices -and $_.Status -ne "Running"
} | ForEach-Object {
    Write-Warning "Service critique arrêté: $($_.Name)"
    Start-Service $_.Name
}

# Filtrer extensions
$imageExtensions = @(".jpg", ".png", ".gif", ".bmp")
Get-ChildItem | Where-Object {
    $_.Extension -in $imageExtensions
}

# Multiple values
$status = "Stopped"
if ($status -in @("Stopped", "StopPending", "Paused")) {
    Write-Host "Service non actif"
}
```

💡 **Cas d'usage professionnels** :
```powershell
# Monitoring avec seuils
$cpuThreshold = 80
$memThreshold = 90

Get-Process | ForEach-Object {
    $cpuPercent = $_.CPU
    $memMB = $_.WS / 1MB
    
    if ($cpuPercent -gt $cpuThreshold -or $memMB -gt 500) {
        [PSCustomObject]@{
            ProcessName = $_.Name
            PID = $_.Id
            CPU = $cpuPercent
            Memory_MB = [math]::Round($memMB, 2)
            Alert = if ($cpuPercent -gt $cpuThreshold) { "CPU élevé" } else { "Mémoire élevée" }
        }
    }
}

# Validation paramètres
function Set-UserInfo {
    param(
        [string]$Username,
        [int]$Age,
        [string]$Email
    )
    
    # Validation
    if ($Username -notmatch "^[a-zA-Z0-9_]{3,20}$") {
        throw "Username invalide (3-20 caractères alphanumériques)"
    }
    
    if ($Age -lt 0 -or $Age -gt 150) {
        throw "Âge invalide (0-150)"
    }
    
    if ($Email -notmatch "^[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}$") {
        throw "Email invalide"
    }
    
    Write-Host "Utilisateur créé: $Username"
}

# Analyse logs par niveau
$logLevels = @{
    Error = 0
    Warning = 0
    Info = 0
}

Get-Content app.log | ForEach-Object {
    if ($_ -match "ERROR") { $logLevels.Error++ }
    elseif ($_ -match "WARNING") { $logLevels.Warning++ }
    elseif ($_ -match "INFO") { $logLevels.Info++ }
}

$logLevels.GetEnumerator() | ForEach-Object {
    Write-Host "$($_.Key): $($_.Value)" -ForegroundColor $(
        if ($_.Key -eq "Error") { "Red" }
        elseif ($_.Key -eq "Warning") { "Yellow" }
        else { "Green" }
    )
}

# Vérification configuration réseau
$config = Get-NetIPAddress | Where-Object {
    $_.InterfaceAlias -notlike "*Loopback*" -and
    $_.AddressFamily -eq "IPv4" -and
    $_.IPAddress -match "^192\.168\."
}

if ($config) {
    Write-Host "IP privée détectée: $($config.IPAddress)"
}
```

⚠️ **Différences avec Bash** :
```powershell
# PowerShell utilise des mots complets
# Bash                  PowerShell
# -eq                   -eq
# -ne                   -ne
# -gt                   -gt
# -ge                   -ge
# -lt                   -lt
# -le                   -le

# PowerShell est INSENSIBLE à la casse par défaut
"Test" -eq "test"  # $true en PowerShell
# En Bash: [ "Test" = "test" ] → false

# Opérateurs logiques
# Bash: -a (AND), -o (OR)
# PowerShell: -and, -or, -not, !
if ($x -gt 5 -and $y -lt 10) { }
if ($x -gt 5 -or $y -lt 10) { }
if (-not $active) { }
if (!$active) { }
```

Q21 : **B** ✅
**Explication** : `@()` = array. `foreach` parcourt collection. Alternative : `$names | ForEach-Object`

📖 **Syntaxes de création d'arrays** :

```powershell
# Array vide
$empty = @()

# Array avec valeurs
$numbers = @(1, 2, 3, 4, 5)
$names = @("Alice", "Bob", "Charlie")
$mixed = @(1, "deux", 3.0, $true, (Get-Date))

# Sans @() (conversion automatique)
$simple = 1, 2, 3, 4, 5

# Range
$range = 1..10           # 1, 2, 3... 10
$letters = 'a'..'z'      # a, b, c... z

# Array depuis commande
$processes = @(Get-Process)
$files = @(Get-ChildItem C:\Data)
```

💡 **Accès aux éléments** :

```powershell
$colors = @("Rouge", "Vert", "Bleu", "Jaune")

# Accès par index (commence à 0)
$colors[0]      # "Rouge"
$colors[2]      # "Bleu"

# Dernier élément
$colors[-1]     # "Jaune"
$colors[-2]     # "Bleu"

# Slice (plage)
$colors[0..2]   # "Rouge", "Vert", "Bleu"
$colors[1..3]   # "Vert", "Bleu", "Jaune"

# Éléments spécifiques
$colors[0,2,3]  # "Rouge", "Bleu", "Jaune"

# Nombre d'éléments
$colors.Count   # 4
$colors.Length  # 4
```

⚠️ **Opérations sur arrays** :

```powershell
$numbers = @(1, 2, 3)

# Ajout d'éléments
$numbers += 4            # @(1, 2, 3, 4)
$numbers += @(5, 6)      # @(1, 2, 3, 4, 5, 6)

# ⚠️ += crée nouveau array (coûteux en performance)
# Alternative pour grandes collections : ArrayList
$list = [System.Collections.ArrayList]@()
$list.Add(1) | Out-Null
$list.Add(2) | Out-Null

# Suppression (recréation array)
$numbers = $numbers | Where-Object { $_ -ne 3 }  # Retire 3

# Trier
$sorted = $numbers | Sort-Object
$sorted_desc = $numbers | Sort-Object -Descending

# Inverser
[Array]::Reverse($numbers)

# Unique
$unique = $numbers | Select-Object -Unique

# Vérifier présence
$numbers -contains 3     # $true
3 -in $numbers           # $true

# Trouver index
$index = [Array]::IndexOf($numbers, 3)

# Maximum / Minimum
($numbers | Measure-Object -Maximum).Maximum
($numbers | Measure-Object -Minimum).Minimum
```

💡 **Hashtables (dictionnaires)** :

```powershell
# Création hashtable vide
$hash = @{}

# Création avec valeurs
$user = @{
    Name = "Alice"
    Age = 25
    Role = "Admin"
    Active = $true
}

# Accès propriétés
$user.Name           # "Alice"
$user["Age"]         # 25
$user.Role           # "Admin"

# Ajout / Modification
$user.Email = "alice@example.com"
$user["Phone"] = "123-456-7890"
$user.Age = 26       # Modification

# Suppression
$user.Remove("Phone")

# Vérifier existence clé
$user.ContainsKey("Email")  # $true

# Lister clés et valeurs
$user.Keys           # Name, Age, Role, Active, Email
$user.Values         # Alice, 26, Admin, True, alice@example.com

# Itérer
foreach ($key in $user.Keys) {
    Write-Host "$key : $($user[$key])"
}

$user.GetEnumerator() | ForEach-Object {
    Write-Host "$($_.Key) = $($_.Value)"
}
```

✅ **Boucles foreach** :

```powershell
# Foreach (statement)
$names = @("Alice", "Bob", "Charlie")

foreach ($name in $names) {
    Write-Host "Bonjour $name"
}

# ForEach-Object (cmdlet dans pipeline)
$names | ForEach-Object {
    Write-Host "Bonjour $_"
}

# Alias
$names | foreach { Write-Host $_ }
$names | % { Write-Host $_ }

# Différence statement vs cmdlet
# Statement : Plus rapide, pas de pipeline
foreach ($file in Get-ChildItem) {
    $file.Name
}

# Cmdlet : Dans pipeline, plus flexible
Get-ChildItem | ForEach-Object {
    $_.Name
}

# ForEach avec index
$names = @("Alice", "Bob", "Charlie")
for ($i = 0; $i -lt $names.Count; $i++) {
    Write-Host "$i : $($names[$i])"
}

# Ou avec ForEach-Object
$names | ForEach-Object -Begin { $i = 0 } -Process {
    Write-Host "$i : $_"
    $i++
}
```

🔍 **Arrays multidimensionnels** :

```powershell
# Array 2D
$matrix = @(
    @(1, 2, 3),
    @(4, 5, 6),
    @(7, 8, 9)
)

# Accès
$matrix[0][0]  # 1
$matrix[1][2]  # 6
$matrix[2][1]  # 8

# Parcourir
foreach ($row in $matrix) {
    foreach ($cell in $row) {
        Write-Host $cell -NoNewline
    }
    Write-Host ""
}

# Array d'objets
$users = @(
    @{Name="Alice"; Age=25},
    @{Name="Bob"; Age=30},
    @{Name="Charlie"; Age=35}
)

$users | ForEach-Object {
    Write-Host "$($_.Name) a $($_.Age) ans"
}
```

💡 **Cas d'usage professionnels** :

```powershell
# Configuration multi-serveurs
$servers = @(
    @{Name="WEB01"; IP="192.168.1.10"; Role="Web"},
    @{Name="DB01"; IP="192.168.1.20"; Role="Database"},
    @{Name="APP01"; IP="192.168.1.30"; Role="Application"}
)

foreach ($server in $servers) {
    Write-Host "Test de $($server.Name) ($($server.IP))..."
    Test-Connection -ComputerName $server.IP -Count 1 -Quiet
}

# Collecte de données système
$systemInfo = @{
    Hostname = $env:COMPUTERNAME
    OS = (Get-CimInstance Win32_OperatingSystem).Caption
    CPU = (Get-CimInstance Win32_Processor).Name
    RAM_GB = [math]::Round((Get-CimInstance Win32_ComputerSystem).TotalPhysicalMemory/1GB, 2)
    Disks = @(Get-PSDrive -PSProvider FileSystem | Where-Object {$_.Free})
}

# Export configuration
$systemInfo | ConvertTo-Json | Out-File system-info.json

# Traitement par lots
$filesToProcess = @(Get-ChildItem C:\Data -Filter *.txt)
$results = @()

foreach ($file in $filesToProcess) {
    $result = @{
        FileName = $file.Name
        Size = $file.Length
        Lines = (Get-Content $file.FullName | Measure-Object -Line).Lines
        ProcessedDate = Get-Date
    }
    $results += $result
}

$results | Export-Csv -Path results.csv -NoTypeInformation

# Backup multiple sources
$backupSources = @(
    "C:\Data",
    "C:\Config",
    "C:\Scripts"
)

$backupDest = "D:\Backups\$(Get-Date -Format 'yyyyMMdd')"
New-Item -Path $backupDest -ItemType Directory -Force

foreach ($source in $backupSources) {
    if (Test-Path $source) {
        $folderName = Split-Path $source -Leaf
        Copy-Item -Path $source -Destination "$backupDest\$folderName" -Recurse
        Write-Host "✓ Backup de $source terminé"
    } else {
        Write-Warning "⚠ Source introuvable: $source"
    }
}

# Gestion utilisateurs AD (bulk)
$newUsers = @(
    @{Username="jdoe"; FirstName="John"; LastName="Doe"; Department="IT"},
    @{Username="asmith"; FirstName="Alice"; LastName="Smith"; Department="HR"},
    @{Username="bjones"; FirstName="Bob"; LastName="Jones"; Department="Sales"}
)

foreach ($user in $newUsers) {
    try {
        New-ADUser -SamAccountName $user.Username `
                   -GivenName $user.FirstName `
                   -Surname $user.LastName `
                   -Department $user.Department `
                   -Enabled $true
        Write-Host "✓ Utilisateur créé: $($user.Username)" -ForegroundColor Green
    } catch {
        Write-Host "✗ Erreur pour $($user.Username): $_" -ForegroundColor Red
    }
}

# Monitoring services critiques
$criticalServices = @("WinRM", "W32Time", "MSSQLSERVER", "IIS")
$serviceStatus = @()

foreach ($serviceName in $criticalServices) {
    $service = Get-Service -Name $serviceName -ErrorAction SilentlyContinue
    if ($service) {
        $serviceStatus += [PSCustomObject]@{
            Service = $serviceName
            Status = $service.Status
            StartType = $service.StartType
            Alert = if ($service.Status -ne "Running") { "⚠ Service arrêté!" } else { "✓ OK" }
        }
    } else {
        $serviceStatus += [PSCustomObject]@{
            Service = $serviceName
            Status = "Non installé"
            StartType = "N/A"
            Alert = "⚠ Service non trouvé"
        }
    }
}

$serviceStatus | Format-Table -AutoSize
```

⚠️ **Performance : Array vs ArrayList** :

```powershell
# ❌ LENT : Array avec += (pour grandes collections)
$array = @()
Measure-Command {
    1..10000 | ForEach-Object {
        $array += $_  # Recrée array à chaque fois!
    }
}

# ✅ RAPIDE : ArrayList
$list = [System.Collections.ArrayList]@()
Measure-Command {
    1..10000 | ForEach-Object {
        $list.Add($_) | Out-Null
    }
}

# ✅ RAPIDE : Generic List
$list = [System.Collections.Generic.List[int]]::new()
Measure-Command {
    1..10000 | ForEach-Object {
        $list.Add($_)
    }
}
```

Q22 : **B** ✅
**Explication** : `"Texte"` retourne objet, `Write-Host` affiche (pas capturable).

📖 **Différences fondamentales Write-Output vs Write-Host** :

| Commande | Destination | Capturable | Usage |
|----------|-------------|------------|-------|
| `Write-Output` | Pipeline (objet) | ✅ Oui | Retourner des données |
| `Write-Host` | Console (UI) | ❌ Non | Affichage utilisateur |

💡 **Write-Output : Retourne des objets** :

```powershell
# Write-Output retourne dans le pipeline
function Get-ComputerName {
    Write-Output $env:COMPUTERNAME
}

$name = Get-ComputerName  # ✅ Capturable
Write-Host "Ordinateur: $name"

# Équivalent (implicite)
function Get-ComputerName {
    $env:COMPUTERNAME  # Retour implicite
}

# Multiple objets
function Get-SystemInfo {
    Write-Output "Hostname: $env:COMPUTERNAME"
    Write-Output "User: $env:USERNAME"
    Write-Output "Domain: $env:USERDOMAIN"
}

$info = Get-SystemInfo  # $info contient les 3 lignes
$info | ForEach-Object { Write-Host $_ }

# Objets structurés
function Get-DiskInfo {
    [PSCustomObject]@{
        DriveLetter = "C:"
        FreeSpace_GB = 50.5
        TotalSpace_GB = 250
    }
}

$disk = Get-DiskInfo  # ✅ Objet capturable
$disk.FreeSpace_GB    # Accès propriété
```

⚠️ **Write-Host : Affichage uniquement** :

```powershell
# Write-Host affiche mais ne retourne RIEN
function Show-Message {
    Write-Host "Ceci est un message"
}

$result = Show-Message  # ❌ $result est vide ($null)

# Utile pour :
# - Messages utilisateur
# - Progression
# - Messages colorés

Write-Host "Succès!" -ForegroundColor Green
Write-Host "Attention!" -ForegroundColor Yellow
Write-Host "Erreur!" -ForegroundColor Red -BackgroundColor White

# ⚠️ Problème dans functions
function Get-Data {
    Write-Host "Traitement en cours..."  # ❌ Pollue la sortie
    Get-Process
}

$processes = Get-Data  
# Affiche "Traitement en cours..." à l'écran
# Mais $processes contient bien les processus
```

💡 **Famille Write-* : Autres cmdlets** :

```powershell
# Write-Verbose : Messages détaillés (si -Verbose)
function Do-Something {
    [CmdletBinding()]
    param()
    
    Write-Verbose "Démarrage du traitement..."
    # Traitement
    Write-Verbose "Traitement terminé"
}

Do-Something               # Rien
Do-Something -Verbose      # Affiche messages Verbose

# Write-Debug : Messages de debug (si -Debug)
function Test-Function {
    [CmdletBinding()]
    param()
    
    Write-Debug "Variable = $variable"
    Write-Debug "Point de contrôle atteint"
}

Test-Function -Debug

# Write-Warning : Avertissements (toujours visible)
if ($disk.FreeSpace_GB -lt 10) {
    Write-Warning "Espace disque faible!"
}

# Write-Error : Erreurs (stream d'erreurs)
if (-not (Test-Path $file)) {
    Write-Error "Fichier introuvable: $file"
}

# Write-Information : Informations (PowerShell 5+)
Write-Information "Information importante" -InformationAction Continue

# Write-Progress : Barre de progression
1..100 | ForEach-Object {
    Write-Progress -Activity "Traitement" `
                   -Status "Fichier $_/100" `
                   -PercentComplete $_
    Start-Sleep -Milliseconds 50
}
```

✅ **Bonnes pratiques dans fonctions** :

```powershell
# ❌ MAUVAIS : Write-Host dans fonction
function Get-ServerStatus {
    $status = Test-Connection server01 -Quiet
    Write-Host "Serveur est en ligne: $status"  # ❌ Non capturable
    return $status
}

# ✅ BON : Write-Output ou return
function Get-ServerStatus {
    [CmdletBinding()]
    param([string]$ServerName)
    
    Write-Verbose "Test de $ServerName..."  # Messages debug avec Verbose
    $status = Test-Connection $ServerName -Quiet
    
    # Retour objet structuré
    [PSCustomObject]@{
        Server = $ServerName
        Online = $status
        Timestamp = Get-Date
    }
}

$result = Get-ServerStatus -ServerName "server01" -Verbose
if ($result.Online) {
    Write-Host "✓ $($result.Server) en ligne" -ForegroundColor Green
}

# ✅ BON : Séparer logique et affichage
function Get-LowDiskSpace {
    [CmdletBinding()]
    param([int]$ThresholdPercent = 10)
    
    Get-PSDrive -PSProvider FileSystem | Where-Object {
        $_.Free -and (($_.Free / ($_.Used + $_.Free)) * 100) -lt $ThresholdPercent
    }
}

# Fonction retourne données, appelant gère affichage
$lowDisks = Get-LowDiskSpace -ThresholdPercent 15
foreach ($disk in $lowDisks) {
    Write-Host "⚠ Disque $($disk.Name) faible!" -ForegroundColor Yellow
}
```

🔍 **Cas d'usage professionnels** :

```powershell
# Script avec progression et logs
function Backup-Files {
    [CmdletBinding()]
    param(
        [string]$Source,
        [string]$Destination
    )
    
    Write-Verbose "Analyse de $Source..."
    $files = Get-ChildItem -Path $Source -Recurse -File
    $total = $files.Count
    $current = 0
    
    Write-Verbose "Trouvé $total fichiers à copier"
    
    foreach ($file in $files) {
        $current++
        
        # Progression (UI)
        Write-Progress -Activity "Backup en cours" `
                       -Status "Fichier $current sur $total" `
                       -PercentComplete (($current / $total) * 100)
        
        # Log détaillé (si -Verbose)
        Write-Verbose "Copie: $($file.Name)"
        
        Copy-Item -Path $file.FullName -Destination $Destination -Force
    }
    
    # Résultat (capturable)
    [PSCustomObject]@{
        Source = $Source
        Destination = $Destination
        FilesCopied = $total
        CompletedAt = Get-Date
        Success = $true
    }
}

# Utilisation
$result = Backup-Files -Source "C:\Data" -Destination "D:\Backup" -Verbose

if ($result.Success) {
    Write-Host "✓ Backup terminé: $($result.FilesCopied) fichiers" -ForegroundColor Green
}

# Fonction avec gestion erreurs
function Install-Application {
    [CmdletBinding()]
    param(
        [string]$ApplicationName
    )
    
    try {
        Write-Verbose "Début installation: $ApplicationName"
        
        Write-Host "Installation de $ApplicationName..." -NoNewline
        
        # Simulation installation
        Start-Sleep -Seconds 2
        
        Write-Host " OK" -ForegroundColor Green
        
        # Retour succès
        [PSCustomObject]@{
            Application = $ApplicationName
            Status = "Installé"
            InstallDate = Get-Date
        }
        
    } catch {
        Write-Host " ÉCHEC" -ForegroundColor Red
        Write-Error "Erreur installation: $_"
        
        # Retour erreur
        [PSCustomObject]@{
            Application = $ApplicationName
            Status = "Échec"
            Error = $_.Exception.Message
        }
    }
}

# Rapport système avec affichage conditionnel
function Get-SystemHealthReport {
    [CmdletBinding()]
    param(
        [switch]$ShowOutput
    )
    
    Write-Verbose "Collecte informations système..."
    
    $cpu = (Get-Counter '\Processor(_Total)\% Processor Time').CounterSamples.CookedValue
    $mem = Get-CimInstance Win32_OperatingSystem
    $memPercent = (($mem.TotalVisibleMemorySize - $mem.FreePhysicalMemory) / $mem.TotalVisibleMemorySize) * 100
    
    $report = [PSCustomObject]@{
        Hostname = $env:COMPUTERNAME
        CPU_Percent = [math]::Round($cpu, 2)
        Memory_Percent = [math]::Round($memPercent, 2)
        Uptime = (Get-Date) - (Get-CimInstance Win32_OperatingSystem).LastBootUpTime
        Timestamp = Get-Date
    }
    
    # Affichage optionnel
    if ($ShowOutput) {
        Write-Host "`n=== Rapport Système ===" -ForegroundColor Cyan
        Write-Host "Serveur: $($report.Hostname)"
        Write-Host "CPU: $($report.CPU_Percent)%" -ForegroundColor $(if ($report.CPU_Percent -gt 80) {"Red"} else {"Green"})
        Write-Host "Mémoire: $($report.Memory_Percent)%" -ForegroundColor $(if ($report.Memory_Percent -gt 90) {"Red"} else {"Green"})
        Write-Host "Uptime: $($report.Uptime.Days) jours`n"
    }
    
    # Toujours retourner l'objet
    $report
}

# Sans affichage (pour export/traitement)
$report = Get-SystemHealthReport
$report | Export-Csv system-health.csv -Append -NoTypeInformation

# Avec affichage (pour utilisateur)
$report = Get-SystemHealthReport -ShowOutput
```

⚠️ **Redirections et streams** :

```powershell
# Stream 1 : Success (Output)
# Stream 2 : Error
# Stream 3 : Warning
# Stream 4 : Verbose
# Stream 5 : Debug
# Stream 6 : Information

# Capturer seulement Output
$output = Get-Process 2>$null  # Ignore erreurs

# Capturer tout
$all = Get-Process *>&1

# Fonction qui utilise tous les streams
function Test-AllStreams {
    [CmdletBinding()]
    param()
    
    Write-Output "Ceci va dans Success stream"
    Write-Error "Ceci va dans Error stream"
    Write-Warning "Ceci va dans Warning stream"
    Write-Verbose "Ceci va dans Verbose stream"
    Write-Debug "Ceci va dans Debug stream"
}

# Capturer différemment
Test-AllStreams -Verbose -Debug 2>&1 | Out-File all-output.txt
```

Q23 : **A** ✅
**Explication** : Paramètres positionnels ou nommés. `param()` définit paramètres.

📖 **Syntaxe complète du bloc param()** :

```powershell
function Mon-Fonction {
    [CmdletBinding()]  # Active fonctionnalités avancées
    param(
        # Paramètre obligatoire avec type
        [Parameter(Mandatory=$true)]
        [string]$Nom,
        
        # Paramètre optionnel avec valeur par défaut
        [Parameter(Mandatory=$false)]
        [int]$Age = 18,
        
        # Paramètre avec validation
        [Parameter()]
        [ValidateSet("Admin","User","Guest")]
        [string]$Role = "User",
        
        # Switch parameter (booléen)
        [switch]$Force
    )
    
    Write-Host "Nom: $Nom, Age: $Age, Role: $Role"
    if ($Force) {
        Write-Host "Mode forcé activé"
    }
}

# Appels possibles
Mon-Fonction -Nom "Alice"
Mon-Fonction -Nom "Bob" -Age 25 -Role "Admin"
Mon-Fonction -Nom "Charlie" -Force
```

💡 **Types de paramètres** :

```powershell
function Test-Parameters {
    param(
        # String
        [string]$Text = "Défaut",
        
        # Nombres
        [int]$Count = 0,
        [double]$Price = 0.0,
        
        # Booléen
        [bool]$Active = $true,
        
        # DateTime
        [datetime]$Date = (Get-Date),
        
        # Array
        [string[]]$Names = @(),
        
        # Hashtable
        [hashtable]$Config = @{},
        
        # Switch (booléen simplifié)
        [switch]$Verbose,
        
        # Scriptblock
        [scriptblock]$Action = {},
        
        # Type personnalisé
        [System.IO.FileInfo]$File
    )
}
```

⚠️ **Validation des paramètres** :

```powershell
function New-User {
    param(
        # Obligatoire
        [Parameter(Mandatory=$true)]
        [string]$Username,
        
        # Range de valeurs
        [ValidateRange(0,150)]
        [int]$Age,
        
        # Valeurs prédéfinies
        [ValidateSet("Admin","User","Guest","ReadOnly")]
        [string]$Role,
        
        # Longueur string
        [ValidateLength(8,20)]
        [string]$Password,
        
        # Pattern regex
        [ValidatePattern('^[a-zA-Z0-9]{3,20}$')]
        [string]$AccountName,
        
        # Fichier existe
        [ValidateScript({Test-Path $_})]
        [string]$ConfigFile,
        
        # Pas null ou vide
        [ValidateNotNullOrEmpty()]
        [string]$Email,
        
        # Nombre éléments array
        [ValidateCount(1,10)]
        [string[]]$Groups
    )
    
    Write-Host "Utilisateur $Username créé"
}

# Exemples d'appels
New-User -Username "jdoe" -Age 25 -Role "User" -Password "SecureP@ss" -AccountName "johndoe123"

# ❌ Erreurs si validation échoue
New-User -Username "jdoe" -Age 200  # Erreur: Age hors limites
New-User -Username "jdoe" -Role "SuperAdmin"  # Erreur: Role invalide
```

💡 **Paramètres positionnels vs nommés** :

```powershell
function Copy-Files {
    param(
        [Parameter(Position=0, Mandatory=$true)]
        [string]$Source,
        
        [Parameter(Position=1, Mandatory=$true)]
        [string]$Destination,
        
        [Parameter()]
        [switch]$Force
    )
    
    Write-Host "Copie de $Source vers $Destination"
}

# Appels équivalents
Copy-Files -Source "C:\Data" -Destination "D:\Backup"  # Nommés
Copy-Files "C:\Data" "D:\Backup"  # Positionnels
Copy-Files "C:\Data" "D:\Backup" -Force  # Mixte
```

✅ **Paramètres avancés** :

```powershell
function Get-ServiceInfo {
    [CmdletBinding(DefaultParameterSetName='ByName')]
    param(
        # Parameter Set 1
        [Parameter(ParameterSetName='ByName', Position=0)]
        [string]$Name,
        
        # Parameter Set 2
        [Parameter(ParameterSetName='ByStatus')]
        [ValidateSet("Running","Stopped")]
        [string]$Status,
        
        # Commun aux deux sets
        [Parameter()]
        [string]$ComputerName = $env:COMPUTERNAME,
        
        # Accepte pipeline
        [Parameter(ValueFromPipeline=$true)]
        [string]$ServiceName
    )
    
    process {
        if ($PSCmdlet.ParameterSetName -eq 'ByName') {
            Get-Service -Name $Name -ComputerName $ComputerName
        } else {
            Get-Service -ComputerName $ComputerName | Where-Object Status -eq $Status
        }
    }
}

# Appels
Get-ServiceInfo -Name "Spooler"
Get-ServiceInfo -Status "Running"
"Spooler","W32Time" | Get-ServiceInfo
```

🔍 **Paramètres pipeline** :

```powershell
function Process-File {
    [CmdletBinding()]
    param(
        # Accepte valeur depuis pipeline (par valeur)
        [Parameter(ValueFromPipeline=$true)]
        [string]$FilePath,
        
        # Accepte propriété depuis pipeline
        [Parameter(ValueFromPipelineByPropertyName=$true)]
        [string]$Name
    )
    
    begin {
        Write-Host "=== Début traitement ==="
    }
    
    process {
        # Exécuté pour chaque objet du pipeline
        Write-Host "Traitement: $FilePath"
    }
    
    end {
        Write-Host "=== Fin traitement ==="
    }
}

# Utilisation
Get-ChildItem *.txt | Process-File

# Ou avec propriété
Get-ChildItem | Process-File
```

💡 **Cas d'usage professionnels** :

```powershell
# Fonction complète de déploiement
function Deploy-Application {
    [CmdletBinding(SupportsShouldProcess=$true)]
    param(
        [Parameter(Mandatory=$true, Position=0)]
        [ValidateScript({Test-Path $_})]
        [string]$PackagePath,
        
        [Parameter(Mandatory=$true, Position=1)]
        [ValidateSet("Dev","Test","Prod")]
        [string]$Environment,
        
        [Parameter()]
        [string[]]$Servers = @("localhost"),
        
        [Parameter()]
        [ValidateRange(1,10)]
        [int]$ParallelJobs = 2,
        
        [Parameter()]
        [PSCredential]$Credential,
        
        [switch]$Restart,
        [switch]$Backup,
        [switch]$Force
    )
    
    begin {
        Write-Verbose "Début déploiement sur $Environment"
        
        if ($Backup) {
            Write-Verbose "Création backup..."
        }
    }
    
    process {
        foreach ($server in $Servers) {
            if ($PSCmdlet.ShouldProcess($server, "Déployer application")) {
                Write-Host "Déploiement sur $server..." -ForegroundColor Cyan
                
                # Logique de déploiement
                try {
                    Copy-Item -Path $PackagePath -Destination "\\$server\C$\Deploy" -Force
                    
                    if ($Restart) {
                        Restart-Service -Name "AppService" -Force
                    }
                    
                    Write-Host "✓ Déploiement réussi sur $server" -ForegroundColor Green
                } catch {
                    Write-Error "Erreur sur $server : $_"
                }
            }
        }
    }
    
    end {
        Write-Verbose "Déploiement terminé"
    }
}

# Utilisation
Deploy-Application -PackagePath "C:\Packages\app.zip" `
                   -Environment "Prod" `
                   -Servers @("web01","web02") `
                   -Restart `
                   -Backup `
                   -Verbose

# Avec WhatIf (test sans exécution)
Deploy-Application -PackagePath "C:\Packages\app.zip" `
                   -Environment "Prod" `
                   -Servers @("web01","web02") `
                   -WhatIf

# Fonction de monitoring
function Get-SystemMetrics {
    [CmdletBinding()]
    param(
        [Parameter(ValueFromPipeline=$true)]
        [string[]]$ComputerName = $env:COMPUTERNAME,
        
        [ValidateSet("CPU","Memory","Disk","All")]
        [string]$Metric = "All",
        
        [ValidateRange(1,3600)]
        [int]$IntervalSeconds = 5,
        
        [int]$Count = 1,
        
        [switch]$Continuous
    )
    
    process {
        foreach ($computer in $ComputerName) {
            $iteration = 0
            
            do {
                $iteration++
                
                $metrics = [PSCustomObject]@{
                    Computer = $computer
                    Timestamp = Get-Date
                }
                
                if ($Metric -in @("CPU","All")) {
                    $cpu = (Get-Counter "\\$computer\Processor(_Total)\% Processor Time" -ErrorAction SilentlyContinue).CounterSamples.CookedValue
                    $metrics | Add-Member -NotePropertyName "CPU_Percent" -NotePropertyValue ([math]::Round($cpu,2))
                }
                
                if ($Metric -in @("Memory","All")) {
                    $mem = Get-CimInstance Win32_OperatingSystem -ComputerName $computer
                    $memPercent = (($mem.TotalVisibleMemorySize - $mem.FreePhysicalMemory) / $mem.TotalVisibleMemorySize) * 100
                    $metrics | Add-Member -NotePropertyName "Memory_Percent" -NotePropertyValue ([math]::Round($memPercent,2))
                }
                
                $metrics
                
                if ($Continuous -or $iteration -lt $Count) {
                    Start-Sleep -Seconds $IntervalSeconds
                }
                
            } while ($Continuous -or $iteration -lt $Count)
        }
    }
}

# Utilisation
Get-SystemMetrics -ComputerName "server01" -Metric "CPU" -Count 10 -IntervalSeconds 2
"web01","web02","db01" | Get-SystemMetrics -Metric "All" -Continuous
```

⚠️ **Bonnes pratiques** :

```powershell
# ✅ Utiliser [CmdletBinding()] pour fonctionnalités avancées
function My-Function {
    [CmdletBinding()]  # Active -Verbose, -Debug, -ErrorAction, etc.
    param(...)
}

# ✅ Types explicites
param([string]$Name, [int]$Age)  # Pas param($Name, $Age)

# ✅ Validation dès la déclaration
param([ValidateRange(1,100)][int]$Percent)

# ✅ Help documentation
function Get-Data {
    <#
    .SYNOPSIS
        Récupère des données
    .DESCRIPTION
        Description détaillée de la fonction
    .PARAMETER Path
        Chemin du fichier
    .EXAMPLE
        Get-Data -Path "C:\data.txt"
    #>
    param([string]$Path)
}

# ✅ Gestion erreurs
function Safe-Function {
    [CmdletBinding()]
    param(
        [Parameter(Mandatory=$true)]
        [ValidateScript({Test-Path $_})]
        [string]$Path
    )
    
    try {
        # Code...
    } catch {
        Write-Error "Erreur: $_"
        throw
    }
}
```

Q24 : **B** ✅
**Explication** :
```powershell
try {
  Get-Item "C:\inexistant"
} catch {
  Write-Host "Erreur: $_"
}
```

📖 **Structure complète Try/Catch/Finally** :

```powershell
try {
    # Code susceptible de générer une erreur
    Get-Item "C:\inexistant" -ErrorAction Stop
    
} catch [System.IO.FileNotFoundException] {
    # Traiter erreur spécifique
    Write-Host "Fichier introuvable" -ForegroundColor Red
    
} catch [System.UnauthorizedAccessException] {
    # Autre type d'erreur spécifique
    Write-Host "Accès refusé" -ForegroundColor Red
    
} catch {
    # Attraper toutes les autres erreurs
    Write-Host "Erreur générale: $_" -ForegroundColor Red
    Write-Host "Type: $($_.Exception.GetType().FullName)"
    
} finally {
    # S'exécute TOUJOURS (erreur ou non)
    Write-Host "Bloc finally: Nettoyage..."
}
```

💡 **Accès aux détails d'erreur** :

```powershell
try {
    Get-Content "C:\inexistant.txt" -ErrorAction Stop
} catch {
    # Variable automatique $_ contient l'erreur
    
    Write-Host "Message: $($_.Exception.Message)"
    Write-Host "Type: $($_.Exception.GetType().FullName)"
    Write-Host "Ligne: $($_.InvocationInfo.ScriptLineNumber)"
    Write-Host "Commande: $($_.InvocationInfo.Line)"
    Write-Host "Stack Trace: $($_.ScriptStackTrace)"
    
    # Erreur complète
    Write-Host $_.Exception | Format-List -Force
}
```

⚠️ **Types d'exceptions courants** :

```powershell
# FileNotFoundException
try {
    Get-Content "C:\inexistant.txt" -ErrorAction Stop
} catch [System.IO.FileNotFoundException] {
    Write-Error "Fichier introuvable"
}

# DirectoryNotFoundException
try {
    Get-ChildItem "C:\DossierInexistant" -ErrorAction Stop
} catch [System.IO.DirectoryNotFoundException] {
    Write-Error "Dossier introuvable"
}

# UnauthorizedAccessException
try {
    Get-Content "C:\Windows\System32\config\SAM" -ErrorAction Stop
} catch [System.UnauthorizedAccessException] {
    Write-Error "Accès refusé"
}

# DivideByZeroException
try {
    $result = 10 / 0
} catch [System.DivideByZeroException] {
    Write-Error "Division par zéro"
}

# ArgumentException
try {
    Get-Process -Name "" -ErrorAction Stop
} catch [System.ArgumentException] {
    Write-Error "Argument invalide"
}

# ManagementException (WMI/CIM)
try {
    Get-CimInstance -ClassName "FakeClass" -ErrorAction Stop
} catch [Microsoft.Management.Infrastructure.CimException] {
    Write-Error "Erreur CIM/WMI"
}

# InvalidOperationException
try {
    $service = Get-Service -Name "ServiceInexistant" -ErrorAction Stop
    Stop-Service $service
} catch [System.InvalidOperationException] {
    Write-Error "Opération invalide"
}
```

💡 **Variable $Error** :

```powershell
# $Error contient TOUTES les erreurs de la session
# $Error[0] = dernière erreur
# $Error[1] = avant-dernière, etc.

# Voir dernière erreur
$Error[0]

# Détails complets
$Error[0] | Format-List -Force

# Nombre d'erreurs
$Error.Count

# Vider les erreurs
$Error.Clear()

# Exporter log d'erreurs
$Error | Export-Clixml errors.xml

# Filtrer erreurs
$Error | Where-Object { $_.Exception -is [System.IO.FileNotFoundException] }
```

✅ **ErrorAction : Contrôle comportement erreurs** :

```powershell
# ErrorAction values:
# - Stop: Génère erreur terminante (capturable par catch)
# - Continue: Affiche erreur, continue (défaut)
# - SilentlyContinue: Ignore erreur, pas d'affichage
# - Inquire: Demande à l'utilisateur quoi faire
# - Ignore: Ignore complètement (même pas dans $Error)

# Sans ErrorAction Stop, catch ne capture PAS
try {
    Get-Item "C:\inexistant"  # ❌ Erreur NON terminante
} catch {
    Write-Host "Pas capturé!"  # Ne s'exécute PAS
}

# Avec ErrorAction Stop
try {
    Get-Item "C:\inexistant" -ErrorAction Stop  # ✅ Erreur terminante
} catch {
    Write-Host "Capturé!"  # S'exécute
}

# SilentlyContinue : Ignorer erreurs
$file = Get-Item "C:\inexistant" -ErrorAction SilentlyContinue
if (-not $file) {
    Write-Host "Fichier introuvable (mais pas d'erreur affichée)"
}

# Preference globale
$ErrorActionPreference = "Stop"  # Toutes les erreurs sont terminantes
$ErrorActionPreference = "Continue"  # Défaut
$ErrorActionPreference = "SilentlyContinue"  # Mode silencieux

# Dans fonctions
function Get-Data {
    [CmdletBinding()]
    param(
        [string]$Path,
        [ValidateSet("Stop","Continue","SilentlyContinue")]
        [string]$ErrorAction = "Stop"
    )
    
    Get-Content $Path -ErrorAction $ErrorAction
}
```

🔍 **Finally : Nettoyage garanti** :

```powershell
function Process-File {
    param([string]$Path)
    
    $reader = $null
    
    try {
        Write-Verbose "Ouverture fichier..."
        $reader = [System.IO.StreamReader]::new($Path)
        
        while ($null -ne ($line = $reader.ReadLine())) {
            # Traiter ligne
            Write-Host $line
        }
        
    } catch {
        Write-Error "Erreur lecture: $_"
        
    } finally {
        # Fermeture garantie même en cas d'erreur
        if ($reader) {
            Write-Verbose "Fermeture fichier..."
            $reader.Close()
            $reader.Dispose()
        }
    }
}

# Connexion base de données
function Query-Database {
    $connection = $null
    
    try {
        $connection = New-Object System.Data.SqlClient.SqlConnection
        $connection.ConnectionString = $connString
        $connection.Open()
        
        # Requête...
        
    } catch {
        Write-Error "Erreur SQL: $_"
        
    } finally {
        # Fermeture connexion garantie
        if ($connection -and $connection.State -eq 'Open') {
            $connection.Close()
            $connection.Dispose()
        }
    }
}
```

💡 **Cas d'usage professionnels** :

```powershell
# Fonction robuste de backup
function Backup-Data {
    [CmdletBinding()]
    param(
        [Parameter(Mandatory=$true)]
        [string]$Source,
        
        [Parameter(Mandatory=$true)]
        [string]$Destination
    )
    
    $timestamp = Get-Date -Format "yyyyMMdd_HHmmss"
    $backupPath = Join-Path $Destination "Backup_$timestamp"
    
    try {
        # Vérifications
        if (-not (Test-Path $Source)) {
            throw "Source introuvable: $Source"
        }
        
        if (-not (Test-Path $Destination)) {
            Write-Verbose "Création destination: $Destination"
            New-Item -Path $Destination -ItemType Directory -ErrorAction Stop | Out-Null
        }
        
        # Backup
        Write-Host "Backup de $Source..." -ForegroundColor Cyan
        
        Copy-Item -Path $Source -Destination $backupPath -Recurse -ErrorAction Stop
        
        Write-Host "✓ Backup réussi: $backupPath" -ForegroundColor Green
        
        return [PSCustomObject]@{
            Success = $true
            Source = $Source
            Destination = $backupPath
            Timestamp = Get-Date
            Files = (Get-ChildItem $backupPath -Recurse -File).Count
        }
        
    } catch [System.IO.IOException] {
        Write-Error "Erreur I/O: $($_.Exception.Message)"
        return [PSCustomObject]@{
            Success = $false
            Error = "Erreur I/O"
            Details = $_.Exception.Message
        }
        
    } catch [System.UnauthorizedAccessException] {
        Write-Error "Accès refusé: $($_.Exception.Message)"
        return [PSCustomObject]@{
            Success = $false
            Error = "Accès refusé"
            Details = $_.Exception.Message
        }
        
    } catch {
        Write-Error "Erreur inattendue: $($_.Exception.Message)"
        Write-Error $_.ScriptStackTrace
        
        # Nettoyage si backup partiel
        if (Test-Path $backupPath) {
            Remove-Item $backupPath -Recurse -Force -ErrorAction SilentlyContinue
        }
        
        return [PSCustomObject]@{
            Success = $false
            Error = "Erreur générale"
            Details = $_.Exception.Message
        }
        
    } finally {
        Write-Verbose "Opération terminée"
    }
}

# Déploiement avec rollback
function Deploy-Application {
    [CmdletBinding()]
    param(
        [string]$PackagePath,
        [string]$DestinationPath
    )
    
    $backupPath = $null
    
    try {
        # Backup de l'existant
        if (Test-Path $DestinationPath) {
            $backupPath = "$DestinationPath.backup_$(Get-Date -Format 'yyyyMMddHHmmss')"
            Write-Host "Création backup: $backupPath"
            Copy-Item -Path $DestinationPath -Destination $backupPath -Recurse -ErrorAction Stop
        }
        
        # Déploiement
        Write-Host "Déploiement..."
        Copy-Item -Path $PackagePath -Destination $DestinationPath -Recurse -Force -ErrorAction Stop
        
        # Test post-déploiement
        Write-Host "Test..."
        $testResult = Test-ApplicationHealth -Path $DestinationPath
        
        if (-not $testResult) {
            throw "Tests post-déploiement échoués"
        }
        
        Write-Host "✓ Déploiement réussi" -ForegroundColor Green
        
        # Suppression backup si succès
        if ($backupPath -and (Test-Path $backupPath)) {
            Remove-Item $backupPath -Recurse -Force
        }
        
    } catch {
        Write-Error "Déploiement échoué: $_"
        
        # ROLLBACK
        if ($backupPath -and (Test-Path $backupPath)) {
            Write-Warning "Rollback en cours..."
            
            try {
                if (Test-Path $DestinationPath) {
                    Remove-Item $DestinationPath -Recurse -Force
                }
                Move-Item -Path $backupPath -Destination $DestinationPath
                Write-Host "✓ Rollback réussi" -ForegroundColor Yellow
                
            } catch {
                Write-Error "CRITIQUE: Rollback échoué!"
                throw
            }
        }
        
        throw  # Re-throw pour propager l'erreur
        
    } finally {
        Write-Host "Fin de la procédure de déploiement"
    }
}

# Logging centralisé avec gestion erreurs
function Write-Log {
    [CmdletBinding()]
    param(
        [string]$Message,
        [ValidateSet("INFO","WARNING","ERROR")]
        [string]$Level = "INFO",
        [string]$LogPath = "C:\Logs\app.log"
    )
    
    try {
        $timestamp = Get-Date -Format "yyyy-MM-dd HH:mm:ss"
        $logEntry = "[$timestamp] [$Level] $Message"
        
        # Créer dossier si nécessaire
        $logDir = Split-Path $LogPath -Parent
        if (-not (Test-Path $logDir)) {
            New-Item -Path $logDir -ItemType Directory -Force -ErrorAction Stop | Out-Null
        }
        
        # Écrire log
        Add-Content -Path $LogPath -Value $logEntry -ErrorAction Stop
        
        # Rotation si > 10MB
        $logFile = Get-Item $LogPath -ErrorAction SilentlyContinue
        if ($logFile -and $logFile.Length -gt 10MB) {
            $archivePath = "$LogPath.$(Get-Date -Format 'yyyyMMdd_HHmmss')"
            Move-Item -Path $LogPath -Destination $archivePath
            Compress-Archive -Path $archivePath -DestinationPath "$archivePath.zip"
            Remove-Item $archivePath
        }
        
    } catch {
        # En cas d'erreur logging, écrire dans Event Log
        Write-EventLog -LogName Application -Source "MyApp" -EventId 1000 `
                       -EntryType Error -Message "Erreur logging: $_" `
                       -ErrorAction SilentlyContinue
    }
}
```

⚠️ **Throw : Générer erreur personnalisée** :

```powershell
function Divide-Numbers {
    param(
        [double]$Numerator,
        [double]$Denominator
    )
    
    if ($Denominator -eq 0) {
        throw [System.DivideByZeroException]::new("Division par zéro interdite")
    }
    
    return $Numerator / $Denominator
}

try {
    $result = Divide-Numbers -Numerator 10 -Denominator 0
} catch [System.DivideByZeroException] {
    Write-Error "Erreur mathématique: $_"
}

# Throw avec message simple
if ($age -lt 0) {
    throw "L'âge ne peut pas être négatif"
}

# Throw avec ErrorRecord personnalisé
$errorRecord = [System.Management.Automation.ErrorRecord]::new(
    [System.ArgumentException]::new("Argument invalide"),
    "InvalidArgument",
    [System.Management.Automation.ErrorCategory]::InvalidArgument,
    $null
)
throw $errorRecord
```

Q25 : **B** ✅
**Explication** : `Select-Object` sélectionne propriétés. Alternative : `Get-Service | ft Name, Status`

📖 **Options de Select-Object** :

| Option | Description | Exemple |
|--------|-------------|---------||
| `-Property` | Sélectionner propriétés | `Select-Object Name, Status` |
| `-First` | Premiers N éléments | `Select-Object -First 10` |
| `-Last` | Derniers N éléments | `Select-Object -Last 5` |
| `-Skip` | Ignorer N premiers | `Select-Object -Skip 3` |
| `-Unique` | Valeurs uniques | `Select-Object -Unique` |
| `-ExpandProperty` | Extraire valeur unique | `Select-Object -ExpandProperty Name` |
| `-ExcludeProperty` | Exclure propriétés | `Select-Object * -ExcludeProperty Id` |

💡 **Sélection de propriétés** :

```powershell
# Sélectionner propriétés spécifiques
Get-Process | Select-Object Name, Id, CPU

# Toutes les propriétés
Get-Process | Select-Object *

# Exclure certaines propriétés
Get-Process | Select-Object * -ExcludeProperty Threads, Modules

# Alias courant
Get-Process | select Name, Id, CPU
```

⚠️ **Propriétés calculées** :

```powershell
# Syntaxe complète
Get-Process | Select-Object Name, 
    @{Name="CPU_Percent";Expression={$_.CPU}},
    @{Name="Memory_MB";Expression={[math]::Round($_.WS/1MB,2)}}

# Syntaxe courte (alias)
Get-Process | Select-Object Name, 
    @{N="CPU";E={$_.CPU}},
    @{N="Memory_MB";E={[math]::Round($_.WS/1MB,2)}}

# Ou avec L (Label)
Get-Process | Select-Object Name, 
    @{L="Memory_MB";E={$_.WS/1MB}}

# Exemples de calculs
Get-ChildItem | Select-Object Name,
    @{N="Size_MB";E={[math]::Round($_.Length/1MB,2)}},
    @{N="Age_Days";E={((Get-Date) - $_.LastWriteTime).Days}},
    @{N="Extension";E={$_.Extension.ToUpper()}}

# Conversion de dates
Get-Service | Select-Object Name,
    @{N="LastStart";E={(Get-Date).ToString("yyyy-MM-dd")}}

# Calculs conditionnels
Get-Process | Select-Object Name,
    @{N="Priority";E={
        if ($_.CPU -gt 100) { "High" }
        elseif ($_.CPU -gt 50) { "Medium" }
        else { "Low" }
    }}
```

💡 **First, Last, Skip** :

```powershell
# Premiers 10 processus
Get-Process | Select-Object -First 10

# 5 derniers fichiers modifiés
Get-ChildItem | Sort-Object LastWriteTime -Descending | Select-Object -First 5

# Pagination : Ignorer 10, prendre 5
Get-Process | Select-Object -Skip 10 -First 5

# Tout sauf les 3 premiers
Get-ChildItem | Select-Object -Skip 3

# Dernier élément uniquement
Get-Process | Select-Object -Last 1
```

✅ **Unique** :

```powershell
# Valeurs uniques d'une propriété
Get-Process | Select-Object -Property ProcessName -Unique

# Extensions de fichiers uniques
Get-ChildItem | Select-Object -Property Extension -Unique

# Combinaison
Get-EventLog -LogName System -Newest 1000 | 
    Select-Object -Property Source -Unique | 
    Sort-Object Source
```

🔍 **ExpandProperty : Extraire valeur** :

```powershell
# Avec Select-Object normal (retourne objet)
$names = Get-Process | Select-Object Name
$names[0]  # @{Name=chrome}

# Avec ExpandProperty (retourne valeur directe)
$names = Get-Process | Select-Object -ExpandProperty Name
$names[0]  # chrome

# Utile pour arrays
$emails = Get-ADUser -Filter * | Select-Object -ExpandProperty EmailAddress
# $emails est un simple array de strings

# Utilisation dans scripts
$services = Get-Service | Where-Object Status -eq "Running" | 
    Select-Object -ExpandProperty Name

foreach ($service in $services) {
    # $service est directement le nom (string)
    Write-Host $service
}
```

💡 **Vs Format-Table** :

```powershell
# ✅ Select-Object : Retourne OBJETS (utilisables)
$processes = Get-Process | Select-Object Name, CPU
$processes[0].CPU  # ✅ Accès propriété OK
$processes | Export-Csv processes.csv  # ✅ Export OK

# ❌ Format-Table : Retourne FORMAT (affichage seulement)
$processes = Get-Process | Format-Table Name, CPU
$processes[0].CPU  # ❌ Erreur
$processes | Export-Csv processes.csv  # ❌ Erreur

# Règle : Format-* toujours en DERNIER
Get-Process | 
    Select-Object Name, CPU | 
    Sort-Object CPU -Descending | 
    Format-Table -AutoSize  # ✅ Format en dernier
```

💡 **Cas d'usage professionnels** :

```powershell
# Rapport disques avec calculs
Get-PSDrive -PSProvider FileSystem | Where-Object {$_.Free} | 
    Select-Object Name,
        @{N="Used_GB";E={[math]::Round($_.Used/1GB,2)}},
        @{N="Free_GB";E={[math]::Round($_.Free/1GB,2)}},
        @{N="Total_GB";E={[math]::Round(($_.Used+$_.Free)/1GB,2)}},
        @{N="Free_Percent";E={[math]::Round($_.Free/($_.Used+$_.Free)*100,2)}},
        @{N="Status";E={
            $pct = $_.Free/($_.Used+$_.Free)*100
            if ($pct -lt 10) { "CRITICAL" }
            elseif ($pct -lt 20) { "WARNING" }
            else { "OK" }
        }}

# Top 10 processus par mémoire
Get-Process | 
    Select-Object Name, Id,
        @{N="Memory_MB";E={[math]::Round($_.WS/1MB,2)}},
        @{N="CPU_Time";E={$_.CPU}} | 
    Sort-Object Memory_MB -Descending | 
    Select-Object -First 10

# Fichiers avec âge et taille
Get-ChildItem C:\Logs -Recurse | 
    Select-Object Name, FullName,
        @{N="Size_MB";E={[math]::Round($_.Length/1MB,3)}},
        @{N="Age_Days";E={((Get-Date) - $_.LastWriteTime).Days}},
        @{N="Modified";E={$_.LastWriteTime.ToString("yyyy-MM-dd HH:mm")}},
        @{N="ToDelete";E={((Get-Date) - $_.LastWriteTime).Days -gt 30}} | 
    Where-Object ToDelete -eq $true

# Services avec dépendances
Get-Service | Select-Object Name, Status, StartType,
    @{N="DependentServices";E={($_.DependentServices | Measure-Object).Count}},
    @{N="RequiredServices";E={($_.RequiredServices | Measure-Object).Count}}

# Utilisateurs AD formatés
Get-ADUser -Filter * -Properties * | Select-Object 
    @{N="Username";E={$_.SamAccountName}},
    @{N="FullName";E={"$($_.GivenName) $($_.Surname)"}},
    @{N="Email";E={$_.EmailAddress}},
    @{N="LastLogon";E={[DateTime]::FromFileTime($_.LastLogonTimestamp).ToString("yyyy-MM-dd")}},
    @{N="Enabled";E={$_.Enabled}},
    @{N="PasswordExpired";E={$_.PasswordExpired}}
```

⚠️ **Performance** :

```powershell
# ✅ RAPIDE : Select avec propriétés spécifiques
Get-Process | Select-Object Name, Id, CPU

# ❌ LENT : Select * puis filtre
Get-Process | Select-Object * | Where-Object Name -like "chrome*"

# ✅ OPTIMAL : Filtre d'abord
Get-Process | Where-Object Name -like "chrome*" | Select-Object Name, Id, CPU
```

Q26 : **B** ✅
**Explication** : `Import-Module` charge cmdlets module. `Get-Module -ListAvailable` liste.

📖 **Gestion des modules PowerShell** :

```powershell
# Lister modules disponibles
Get-Module -ListAvailable

# Lister modules chargés
Get-Module

# Importer un module
Import-Module ActiveDirectory
Import-Module Azure
Import-Module -Name Pester

# Supprimer module de la session
Remove-Module ActiveDirectory

# Voir commandes d'un module
Get-Command -Module ActiveDirectory

# Info sur un module
Get-Module ActiveDirectory | Format-List
```

💡 **Modules système courants** :

| Module | Description | Cmdlets clés |
|--------|-------------|-------------|
| `ActiveDirectory` | Gestion AD | Get-ADUser, New-ADUser, Set-ADUser |
| `NetAdapter` | Configuration réseau | Get-NetAdapter, Set-NetIPAddress |
| `DnsClient` | DNS client | Resolve-DnsName, Get-DnsClientCache |
| `Storage` | Gestion stockage | Get-Disk, Get-Volume, New-Partition |
| `Hyper-V` | Virtualisation | Get-VM, New-VM, Start-VM |
| `PKI` | Certificats | Get-Certificate, Import-Certificate |
| `PrintManagement` | Imprimantes | Get-Printer, Add-Printer |
| `ScheduledTasks` | Tâches planifiées | Get-ScheduledTask, Register-ScheduledTask |
| `WebAdministration` | IIS | Get-Website, New-WebAppPool |
| `SqlServer` | SQL Server | Invoke-Sqlcmd, Backup-SqlDatabase |

⚠️ **Installer modules depuis PowerShell Gallery** :

```powershell
# Voir modules disponibles en ligne
Find-Module -Name "*Azure*"

# Installer module (nécessite admin)
Install-Module -Name Az -Scope CurrentUser
Install-Module -Name Pester -Force
Install-Module -Name PSReadLine

# Mettre à jour module
Update-Module -Name Az

# Désinstaller module
Uninstall-Module -Name OldModule

# Voir versions installées
Get-InstalledModule
Get-InstalledModule -Name Az -AllVersions
```

💡 **Créer son propre module (.psm1)** :

```powershell
# Fichier: MyTools.psm1
# =======================

function Get-SystemInfo {
    [CmdletBinding()]
    param()
    
    [PSCustomObject]@{
        Hostname = $env:COMPUTERNAME
        OS = (Get-CimInstance Win32_OperatingSystem).Caption
        CPU = (Get-CimInstance Win32_Processor).Name
        RAM_GB = [math]::Round((Get-CimInstance Win32_ComputerSystem).TotalPhysicalMemory/1GB, 2)
    }
}

function Test-ServerConnection {
    [CmdletBinding()]
    param(
        [Parameter(Mandatory=$true)]
        [string]$ComputerName
    )
    
    Test-Connection -ComputerName $ComputerName -Count 2 -Quiet
}

function Write-ColorLog {
    param(
        [string]$Message,
        [ValidateSet("INFO","WARNING","ERROR")]
        [string]$Level = "INFO"
    )
    
    $color = switch ($Level) {
        "INFO" { "Green" }
        "WARNING" { "Yellow" }
        "ERROR" { "Red" }
    }
    
    Write-Host "[$Level] $Message" -ForegroundColor $color
}

# Exporter fonctions publiques
Export-ModuleMember -Function Get-SystemInfo, Test-ServerConnection, Write-ColorLog

# =======================

# Utilisation
Import-Module .\MyTools.psm1
Get-SystemInfo
Test-ServerConnection -ComputerName "server01"
Write-ColorLog -Message "Test" -Level "INFO"
```

✅ **Module avec manifeste (.psd1)** :

```powershell
# Créer manifeste
New-ModuleManifest -Path .\MyTools.psd1 `
    -Author "John Doe" `
    -CompanyName "MyCompany" `
    -RootModule "MyTools.psm1" `
    -Description "Outils système personnalisés" `
    -PowerShellVersion "5.1" `
    -FunctionsToExport @('Get-SystemInfo', 'Test-ServerConnection')

# Structure module complet
# MyTools/
#   ├── MyTools.psd1  (Manifeste)
#   ├── MyTools.psm1  (Module principal)
#   ├── Public/       (Fonctions publiques)
#   │   ├── Get-SystemInfo.ps1
#   │   └── Test-ServerConnection.ps1
#   ├── Private/      (Fonctions internes)
#   │   └── Write-Log.ps1
#   └── Tests/        (Tests Pester)
#       └── MyTools.Tests.ps1
```

🔍 **Emplacement des modules** :

```powershell
# Voir chemins de recherche modules
$env:PSModulePath -split ';'

# Emplacements standards:
# - C:\Program Files\WindowsPowerShell\Modules (système)
# - C:\Users\<User>\Documents\WindowsPowerShell\Modules (utilisateur)
# - C:\Windows\System32\WindowsPowerShell\v1.0\Modules (built-in)

# Ajouter chemin personnalisé
$env:PSModulePath += ";C:\MyModules"

# Installation module personnel
# Copier dossier module vers:
Copy-Item -Path .\MyTools -Destination "$HOME\Documents\WindowsPowerShell\Modules\" -Recurse

# Puis importer
Import-Module MyTools
```

💡 **Cas d'usage professionnels** :

```powershell
# Module d'administration AD
Import-Module ActiveDirectory

# Créer utilisateurs
New-ADUser -Name "John Doe" -SamAccountName "jdoe" -UserPrincipalName "jdoe@company.com"

# Rechercher utilisateurs
Get-ADUser -Filter {Department -eq "IT"} -Properties *

# Module réseau
Import-Module NetAdapter

# Configuration IP
New-NetIPAddress -InterfaceAlias "Ethernet" -IPAddress "192.168.1.100" -PrefixLength 24 -DefaultGateway "192.168.1.1"

# DNS
Set-DnsClientServerAddress -InterfaceAlias "Ethernet" -ServerAddresses "8.8.8.8","8.8.4.4"

# Module SQL Server
Import-Module SqlServer

# Backup base de données
Backup-SqlDatabase -ServerInstance "SQL01" -Database "AppDB" -BackupFile "C:\Backups\AppDB.bak"

# Exécuter requête
Invoke-Sqlcmd -ServerInstance "SQL01" -Database "AppDB" -Query "SELECT TOP 10 * FROM Users"

# Module IIS
Import-Module WebAdministration

# Créer site
New-Website -Name "MyApp" -Port 80 -PhysicalPath "C:\inetpub\myapp" -ApplicationPool "DefaultAppPool"

# Gérer pool d'applications
Restart-WebAppPool -Name "DefaultAppPool"

# Module Azure (Az)
Install-Module -Name Az -AllowClobber -Scope CurrentUser
Import-Module Az

# Se connecter
Connect-AzAccount

# Lister VMs
Get-AzVM

# Créer ressource
New-AzResourceGroup -Name "MyRG" -Location "West Europe"
```

⚠️ **Auto-loading des modules** :

```powershell
# PowerShell 3.0+ : Chargement automatique
# Pas besoin d'Import-Module si module dans $env:PSModulePath

Get-ADUser john.doe  # Active Directory module chargé automatiquement

# Désactiver auto-loading
$PSModuleAutoLoadingPreference = "None"

# Réactiver
$PSModuleAutoLoadingPreference = "All"
```

Q27 : **B** ✅
**Explication** : `#` ligne simple. `<# ... #>` bloc multi-lignes.

📖 **Types de commentaires PowerShell** :

```powershell
# Commentaire sur une ligne
$variable = 10  # Commentaire en fin de ligne

# Commentaire multi-lignes classique
# Première ligne
# Deuxième ligne
# Troisième ligne

<#
    Bloc de commentaire multi-lignes
    Peut s'étendre sur plusieurs lignes
    Plus lisible pour longs commentaires
#>

# Les deux styles sont valides
Write-Host "Hello"  # Style simple
<# Write-Host "Commenté" #>  # Bloc inline
```

💡 **Comment-Based Help : Documentation de fonctions** :

```powershell
function Get-ServerStatus {
    <#
    .SYNOPSIS
        Vérifie l'état d'un serveur
    
    .DESCRIPTION
        Cette fonction teste la connectivité d'un serveur et retourne
        des informations détaillées sur son état.
    
    .PARAMETER ComputerName
        Nom ou adresse IP du serveur à tester
    
    .PARAMETER Port
        Port à tester (optionnel)
    
    .PARAMETER Timeout
        Timeout en secondes pour le test
    
    .EXAMPLE
        Get-ServerStatus -ComputerName "server01"
        Teste le serveur server01
    
    .EXAMPLE
        Get-ServerStatus -ComputerName "192.168.1.10" -Port 443
        Teste le port 443 sur le serveur spécifié
    
    .EXAMPLE
        "web01","web02" | Get-ServerStatus
        Teste plusieurs serveurs via pipeline
    
    .INPUTS
        System.String
        Accepte noms de serveurs via pipeline
    
    .OUTPUTS
        PSCustomObject
        Retourne objet avec propriétés Server, Status, ResponseTime
    
    .NOTES
        Auteur: John Doe
        Date: 2025-12-19
        Version: 1.0
    
    .LINK
        https://docs.mycompany.com/powershell/get-serverstatus
    #>
    
    [CmdletBinding()]
    param(
        [Parameter(Mandatory=$true, ValueFromPipeline=$true)]
        [string]$ComputerName,
        
        [int]$Port,
        
        [int]$Timeout = 5
    )
    
    # Implémentation...
}

# Afficher l'aide
Get-Help Get-ServerStatus
Get-Help Get-ServerStatus -Full
Get-Help Get-ServerStatus -Examples
Get-Help Get-ServerStatus -Parameter ComputerName
```

⚠️ **Mots-clés Comment-Based Help** :

| Mot-clé | Description |
|---------|-------------|
| `.SYNOPSIS` | Brève description (une ligne) |
| `.DESCRIPTION` | Description détaillée |
| `.PARAMETER` | Description d'un paramètre |
| `.EXAMPLE` | Exemple d'utilisation |
| `.INPUTS` | Type d'objets acceptés en entrée |
| `.OUTPUTS` | Type d'objets retournés |
| `.NOTES` | Notes diverses (auteur, version, etc.) |
| `.LINK` | Liens vers documentation |
| `.COMPONENT` | Composant ou technologie utilisée |
| `.ROLE` | Rôle ou responsabilité |
| `.FUNCTIONALITY` | Fonctionnalité fournie |

💡 **Bonnes pratiques de commentaires** :

```powershell
<#
    Script: Backup-System.ps1
    Description: Effectue backup complet du système
    Auteur: John Doe
    Date: 2025-12-19
    Version: 1.2.0
    
    Historique:
    1.0.0 - 2025-01-15 - Version initiale
    1.1.0 - 2025-06-10 - Ajout compression
    1.2.0 - 2025-12-19 - Ajout notifications email
#>

# ===== CONFIGURATION =====
$BackupPath = "D:\Backups"
$RetentionDays = 30
$EmailTo = "admin@company.com"

# ===== FONCTIONS =====

function Write-Log {
    # Écrire entrée dans log
    param([string]$Message)
    
    $timestamp = Get-Date -Format "yyyy-MM-dd HH:mm:ss"
    Add-Content -Path $LogFile -Value "[$timestamp] $Message"
}

function Send-Notification {
    # Envoyer email de notification
    param(
        [string]$Subject,
        [string]$Body
    )
    
    Send-MailMessage -To $EmailTo -From "backup@company.com" `
        -Subject $Subject -Body $Body -SmtpServer "smtp.company.com"
}

# ===== SCRIPT PRINCIPAL =====

try {
    # Vérifier espace disque disponible
    Write-Log "Vérification espace disque..."
    $disk = Get-PSDrive D
    
    if ($disk.Free -lt 10GB) {
        throw "Espace disque insuffisant"
    }
    
    # Créer répertoire backup avec timestamp
    Write-Log "Création répertoire backup..."
    $backupFolder = Join-Path $BackupPath (Get-Date -Format "yyyyMMdd_HHmmss")
    New-Item -Path $backupFolder -ItemType Directory | Out-Null
    
    # Copier fichiers importants
    Write-Log "Copie fichiers..."
    Copy-Item -Path "C:\Data" -Destination $backupFolder -Recurse
    
    # Compresser backup
    Write-Log "Compression backup..."
    Compress-Archive -Path $backupFolder -DestinationPath "$backupFolder.zip"
    Remove-Item -Path $backupFolder -Recurse -Force
    
    # Nettoyer anciens backups
    Write-Log "Nettoyage anciens backups..."
    Get-ChildItem $BackupPath -Filter *.zip | 
        Where-Object {$_.LastWriteTime -lt (Get-Date).AddDays(-$RetentionDays)} | 
        Remove-Item -Force
    
    Write-Log "Backup terminé avec succès"
    Send-Notification -Subject "Backup OK" -Body "Backup terminé: $backupFolder.zip"
    
} catch {
    # Gestion d'erreurs
    Write-Log "ERREUR: $_"
    Send-Notification -Subject "Backup ÉCHEC" -Body "Erreur: $_"
    throw
}
```

✅ **Commentaires TODO et FIXME** :

```powershell
# TODO: Implémenter validation des paramètres
# FIXME: Bug avec chemins contenant espaces
# HACK: Solution temporaire, à améliorer
# NOTE: Cette fonction nécessite privilèges admin
# OPTIMIZE: Améliorer performance de cette boucle
# XXX: Code à revoir complètement

function Process-Data {
    param($Data)
    
    # TODO: Ajouter gestion d'erreurs
    # FIXME: Ne fonctionne pas avec arrays vides
    
    foreach ($item in $Data) {
        # OPTIMIZE: Utiliser -Filter au lieu de Where-Object
        # Code...
    }
}

# Rechercher TODOs dans scripts
Get-ChildItem -Path C:\Scripts -Recurse -Filter *.ps1 | 
    Select-String -Pattern "TODO:|FIXME:|HACK:" | 
    Format-Table -AutoSize
```

🔍 **Commentaires pour debug** :

```powershell
# Commentaire temporaire pour debug
# Write-Host "Debug: Variable = $variable"

# Désactiver bloc de code temporairement
<#
if ($Debug) {
    Write-Host "Mode debug activé"
    Write-Host "Valeur: $value"
    Write-Host "Type: $($value.GetType())"
}
#>

# Conditions de compilation
$DEBUG_MODE = $true

if ($DEBUG_MODE) {
    # Code debug uniquement
    Write-Host "=== DEBUG ==="
    Get-Variable | Format-Table Name, Value
}
```

💡 **Documentation automatique** :

```powershell
# Générer documentation HTML depuis comment-based help
Get-Command -Module MyModule | ForEach-Object {
    $help = Get-Help $_.Name -Full
    # Convertir en HTML ou Markdown
}

# Utiliser platyPS pour générer Markdown
Install-Module -Name platyPS
Import-Module platyPS

New-MarkdownHelp -Module MyModule -OutputFolder .\docs
```

⚠️ **Ce qu'il ne faut PAS faire** :

```powershell
# ❌ Commentaires évidents (inutiles)
$count = 0  # Initialiser count à 0
$count++    # Incrémenter count

# ❌ Commentaires obsolètes
# Cette fonction utilise API v1  ← Si code utilise v2!
function Get-Data { ... }

# ❌ Trop de commentaires (noie le code)
foreach ($item in $items) {  # Pour chaque item
    # On traite l'item
    Write-Host $item  # Afficher l'item
}  # Fin de la boucle

# ✅ Commentaires utiles (expliquent le POURQUOI)
# Timeout de 30s car API peut être lente
$timeout = 30

# Utilisation de RunspacePool pour paralléliser (10x plus rapide)
$runspacePool = [runspacefactory]::CreateRunspacePool(1, 10)
```

Q28 : **B** ✅
**Explication** : `[type]` cast valeur. Exemples : `[string]$x`, `[datetime]`, `[array]`

📖 **Types courants PowerShell** :

| Type | Description | Exemple |
|------|-------------|---------|  
| `[string]` | Chaîne de caractères | `[string]$text = "Hello"` |
| `[int]` | Entier 32 bits | `[int]$count = 42` |
| `[long]` | Entier 64 bits | `[long]$bignum = 999999999999` |
| `[double]` | Nombre décimal | `[double]$price = 19.99` |
| `[decimal]` | Décimal précis | `[decimal]$money = 19.99` |
| `[bool]` | Booléen | `[bool]$active = $true` |
| `[datetime]` | Date et heure | `[datetime]$now = "2025-12-19"` |
| `[array]` | Tableau | `[array]$list = @(1,2,3)` |
| `[hashtable]` | Table de hachage | `[hashtable]$dict = @{}` |
| `[xml]` | Document XML | `[xml]$doc = Get-Content file.xml` |
| `[regex]` | Expression régulière | `[regex]$pattern = '\d+'` |
| `[ipaddress]` | Adresse IP | `[ipaddress]$ip = "192.168.1.1"` |
| `[guid]` | GUID unique | `[guid]$id = [guid]::NewGuid()` |
| `[scriptblock]` | Bloc de code | `[scriptblock]$code = {Write-Host "Test"}` |

💡 **Conversion explicite vs implicite** :

```powershell
# Conversion IMPLICITE (automatique)
$text = "123"       # String
$number = $text + 1 # "1231" (concaténation)

# Conversion EXPLICITE (cast)
$text = "123"
$number = [int]$text
$result = $number + 1  # 124 (addition)

# Exemples de casts
[string]123          # "123"
[int]"456"           # 456
[double]"3.14"       # 3.14
[bool]1              # $true
[bool]0              # $false
[datetime]"2025-12-19"  # DateTime object
```

⚠️ **Opérateur -as (conversion douce)** :

```powershell
# Cast classique : Génère erreur si échec
[int]"abc"  # ❌ Erreur : Cannot convert

# -as : Retourne $null si échec (pas d'erreur)
"abc" -as [int]  # ✅ $null (pas d'erreur)
"123" -as [int]  # 123

# Utile pour validation
$input = "abc"
$number = $input -as [int]

if ($null -eq $number) {
    Write-Host "Entrée invalide"
} else {
    Write-Host "Nombre valide: $number"
}

# Exemples
"192.168.1.1" -as [ipaddress]  # IPAddress object
"invalid-ip" -as [ipaddress]    # $null

"<root><item>1</item></root>" -as [xml]  # XML document
"invalid xml" -as [xml]                  # $null
```

💡 **Conversions DateTime** :

```powershell
# String → DateTime
$date1 = [datetime]"2025-12-19"
$date2 = [datetime]"12/19/2025"
$date3 = [datetime]"19 December 2025"

# Formats personnalisés
$date = [datetime]::ParseExact("19-12-2025", "dd-MM-yyyy", $null)

# Timestamp Unix → DateTime
$unixTimestamp = 1734566400
$epoch = [datetime]"1970-01-01 00:00:00"
$date = $epoch.AddSeconds($unixTimestamp)

# DateTime → String formaté
$now = Get-Date
$formatted = $now.ToString("yyyy-MM-dd HH:mm:ss")
$formatted2 = $now.ToString("dd/MM/yyyy")

# Comparaisons
$date1 = [datetime]"2025-01-01"
$date2 = [datetime]"2025-12-31"
$date2 -gt $date1  # $true
```

✅ **Conversions numériques** :

```powershell
# Strings → Nombres
[int]"42"           # 42
[double]"3.14159"   # 3.14159
[long]"9999999999"  # 9999999999

# Arrondi automatique
[int]3.7    # 4 (arrondi)
[int]3.2    # 3 (arrondi)

# Contrôle arrondi avec Math
[Math]::Floor(3.7)    # 3 (arrondi inférieur)
[Math]::Ceiling(3.2)  # 4 (arrondi supérieur)
[Math]::Round(3.5)    # 4 (arrondi standard)

# Conversions bytes
$bytes = 1024
$kb = $bytes / 1KB        # 1
$mb = 1073741824 / 1MB    # 1024
$gb = 1099511627776 / 1GB # 1024

# Formatage nombres
$price = 1234.56
$price.ToString("C")    # Format monétaire (dépend locale)
$price.ToString("N2")   # 1,234.56 (2 décimales)
```

🔍 **Conversions collections** :

```powershell
# String → Array
$text = "a,b,c,d"
$array = $text -split ','  # @("a","b","c","d")

# Array → String
$array = @("a", "b", "c")
$text = $array -join ","   # "a,b,c"
$text2 = $array -join ";"  # "a;b;c"

# String multi-lignes → Array
$text = @"
Ligne 1
Ligne 2
Ligne 3
"@
$lines = $text -split "`n"

# Collection → Array typé
$stringArray = [string[]]@(1, 2, 3)  # @("1","2","3")
$intArray = [int[]]@("1", "2", "3")  # @(1,2,3)
```

💡 **Validation types paramètres** :

```powershell
function Process-Data {
    param(
        # Type obligatoire
        [int]$Count,
        [string]$Name,
        [datetime]$StartDate,
        
        # Type avec validation
        [ValidateRange(1,100)]
        [int]$Percentage,
        
        # Array typé
        [string[]]$ServerNames,
        [int[]]$Ports
    )
    
    Write-Host "Count: $Count (type: $($Count.GetType().Name))"
}

# Appels avec conversion automatique
Process-Data -Count "42" -Name 123  # Convertit automatiquement

# Erreur si conversion impossible
Process-Data -Count "abc"  # ❌ Erreur
```

✅ **Cas d'usage professionnels** :

```powershell
# Parsing logs avec conversions
Get-Content app.log | ForEach-Object {
    if ($_ -match '^(\d{4}-\d{2}-\d{2}) (\d{2}:\d{2}:\d{2}) \[(\w+)\] (.+)$') {
        [PSCustomObject]@{
            Timestamp = [datetime]"$($Matches[1]) $($Matches[2])"
            Level = [string]$Matches[3]
            Message = [string]$Matches[4]
        }
    }
}

# Conversion données CSV
$data = Import-Csv users.csv
$data | ForEach-Object {
    [PSCustomObject]@{
        Username = [string]$_.Username
        Age = [int]$_.Age
        HireDate = [datetime]$_.HireDate
        Salary = [decimal]$_.Salary
        Active = [bool]::Parse($_.Active)
    }
}

# Validation IP
function Test-IPAddress {
    param([string]$IP)
    
    $ipObj = $IP -as [ipaddress]
    
    if ($null -eq $ipObj) {
        Write-Error "IP invalide: $IP"
        return $false
    }
    
    Write-Host "IP valide: $($ipObj.IPAddressToString)"
    return $true
}

# Parsing XML
$xmlString = @"
<config>
    <server name="web01" port="80" />
    <server name="db01" port="3306" />
</config>
"@

$xml = [xml]$xmlString
$xml.config.server | ForEach-Object {
    [PSCustomObject]@{
        Name = [string]$_.name
        Port = [int]$_.port
    }
}

# Conversion tailles fichiers
function Convert-FileSize {
    param(
        [long]$Bytes,
        [ValidateSet("KB","MB","GB","TB")]
        [string]$Unit = "MB"
    )
    
    $result = switch ($Unit) {
        "KB" { [math]::Round($Bytes / 1KB, 2) }
        "MB" { [math]::Round($Bytes / 1MB, 2) }
        "GB" { [math]::Round($Bytes / 1GB, 2) }
        "TB" { [math]::Round($Bytes / 1TB, 2) }
    }
    
    "$result $Unit"
}

Get-ChildItem | Select-Object Name, 
    @{N="Size_MB";E={Convert-FileSize $_.Length -Unit MB}}

# Génération GUID
function New-UniqueID {
    [guid]::NewGuid().ToString()
}

$sessionId = New-UniqueID
Write-Host "Session ID: $sessionId"

# Regex avec type
$pattern = [regex]'\b\d{3}-\d{3}-\d{4}\b'  # Format téléphone
$text = "Appelez le 555-123-4567 pour info"

if ($pattern.IsMatch($text)) {
    $phone = $pattern.Match($text).Value
    Write-Host "Téléphone trouvé: $phone"
}
```

⚠️ **Pièges courants** :

```powershell
# ❌ Confusion string/int
$a = "10"
$b = "20"
$result = $a + $b  # "1020" (concaténation)

# ✅ Cast explicite
[int]$a = "10"
[int]$b = "20"
$result = $a + $b  # 30 (addition)

# ❌ Null non géré
$value = $null
$number = [int]$value  # ❌ Erreur

# ✅ Avec -as et vérification
$value = $null
$number = $value -as [int]
if ($null -ne $number) {
    Write-Host "Nombre: $number"
}

# ❌ Format date ambigu
[datetime]"01/02/2025"  # 01 Feb ou 02 Jan ? (dépend locale)

# ✅ Format ISO non ambigu
[datetime]"2025-02-01"  # Toujours 01 Février
```

Q29 : **B** ✅
**Explication** : `Set-ExecutionPolicy RemoteSigned` autorise scripts locaux. Vérif : `Get-ExecutionPolicy`

📖 **Niveaux d'Execution Policy** :

| Niveau | Description | Usage |
|--------|-------------|-------|
| `Restricted` | Aucun script autorisé (défaut) | Sécurité maximale |
| `AllSigned` | Seulement scripts signés (locaux ET distants) | Environnement sécurisé |
| `RemoteSigned` | Scripts locaux OK, distants doivent être signés | **Recommandé** |
| `Unrestricted` | Tous scripts OK, avertissement pour distants | Développement |
| `Bypass` | Rien n'est bloqué, aucun avertissement | Scripts automatisés |
| `Undefined` | Pas de policy définie (hérite niveau supérieur) | Par défaut |

💡 **Portées (Scopes)** :

| Portée | Description | Priorité |
|--------|-------------|----------|
| `MachinePolicy` | Défini par GPO (tout l'ordinateur) | 1 (Highest) |
| `UserPolicy` | Défini par GPO (utilisateur) | 2 |
| `Process` | Session PowerShell courante uniquement | 3 |
| `CurrentUser` | Utilisateur courant | 4 |
| `LocalMachine` | Tous les utilisateurs de la machine | 5 (Lowest) |

⚠️ **Commandes de gestion** :

```powershell
# Voir policy courante
Get-ExecutionPolicy

# Voir toutes les policies par scope
Get-ExecutionPolicy -List

# Résultat exemple:
#         Scope ExecutionPolicy
#         ----- ---------------
# MachinePolicy       Undefined
#    UserPolicy       Undefined
#       Process       Undefined
#   CurrentUser    RemoteSigned
#  LocalMachine    RemoteSigned

# Définir policy (nécessite admin pour LocalMachine)
Set-ExecutionPolicy RemoteSigned
Set-ExecutionPolicy RemoteSigned -Scope CurrentUser  # Sans admin
Set-ExecutionPolicy Bypass -Scope Process  # Session courante

# Forcer (pas de confirmation)
Set-ExecutionPolicy RemoteSigned -Force

# Réinitialiser
Set-ExecutionPolicy Undefined -Scope CurrentUser
```

💡 **RemoteSigned : Le plus courant** :

```powershell
# Définir RemoteSigned pour utilisateur courant
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser

# Comportement:
# ✅ Scripts locaux (C:\, D:\) → Autorisés
# ❌ Scripts distants (réseau, Internet) → Bloqués sauf si signés
# ✅ Scripts téléchargés mais "débloqués" → Autorisés

# Débloquer un script téléchargé
Unblock-File -Path C:\Scripts\Downloaded.ps1

# Vérifier si fichier est bloqué
Get-Item C:\Scripts\Downloaded.ps1 -Stream Zone.Identifier

# Débloquer tous les scripts d'un dossier
Get-ChildItem C:\Scripts -Recurse | Unblock-File
```

✅ **Bypass temporaire** :

```powershell
# Pour une session uniquement (pas d'admin requis)
Set-ExecutionPolicy -ExecutionPolicy Bypass -Scope Process

# Lancer PowerShell avec Bypass
powershell.exe -ExecutionPolicy Bypass -File script.ps1

# Depuis cmd ou batch
powershell.exe -ExecutionPolicy Bypass -Command "& 'C:\Scripts\MyScript.ps1'"

# Arguments additionnels
powershell.exe -ExecutionPolicy Bypass -File script.ps1 -Param1 "Value"
```

🔐 **AllSigned : Scripts signés** :

```powershell
# Définir AllSigned (nécessite certificat)
Set-ExecutionPolicy AllSigned

# Créer certificat auto-signé (test uniquement!)
$cert = New-SelfSignedCertificate -Type CodeSigningCert `
    -Subject "CN=PowerShell Code Signing" `
    -CertStoreLocation Cert:\CurrentUser\My

# Signer un script
$cert = Get-ChildItem Cert:\CurrentUser\My -CodeSigningCert
Set-AuthenticodeSignature -FilePath .\script.ps1 -Certificate $cert

# Vérifier signature
Get-AuthenticodeSignature .\script.ps1

# Résultat montre:
# - SignerCertificate : Info certificat
# - Status : Valid, NotSigned, HashMismatch, etc.
```

💡 **Cas d'usage professionnels** :

```powershell
# Script d'initialisation poste utilisateur
# À exécuter une seule fois
if ((Get-ExecutionPolicy -Scope CurrentUser) -eq "Restricted") {
    Write-Host "Configuration Execution Policy..."
    Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser -Force
    Write-Host "✓ Execution Policy configurée"
}

# Vérification dans script d'installation
function Test-ExecutionPolicy {
    $policy = Get-ExecutionPolicy
    
    if ($policy -in @("Restricted", "AllSigned")) {
        Write-Warning "Execution Policy restrictive: $policy"
        Write-Host "Exécutez: Set-ExecutionPolicy RemoteSigned -Scope CurrentUser"
        return $false
    }
    
    return $true
}

if (-not (Test-ExecutionPolicy)) {
    exit 1
}

# Script avec bypass automatique (tâche planifiée)
$action = New-ScheduledTaskAction -Execute "PowerShell.exe" `
    -Argument "-ExecutionPolicy Bypass -File C:\Scripts\Backup.ps1"
    
$trigger = New-ScheduledTaskTrigger -Daily -At "02:00"

Register-ScheduledTask -TaskName "DailyBackup" `
    -Action $action -Trigger $trigger

# Profile PowerShell
# Fichier: $PROFILE
# Vérifier et configurer au premier lancement
if (-not (Test-Path $PROFILE)) {
    New-Item -Path $PROFILE -ItemType File -Force
    
    Add-Content -Path $PROFILE -Value @'
# Configuration initiale
if ((Get-ExecutionPolicy -Scope CurrentUser) -ne "RemoteSigned") {
    Set-ExecutionPolicy RemoteSigned -Scope CurrentUser -Force
}
'@
}
```

⚠️ **GPO : Gestion centralisée** :

```powershell
# Vérifier si policy définie par GPO
Get-ExecutionPolicy -List

# Si MachinePolicy ou UserPolicy ≠ Undefined:
# → Défini par GPO (non modifiable localement)

# Emplacement GPO:
# Computer Configuration → Policies → Administrative Templates
#   → Windows Components → Windows PowerShell
#     → Turn on Script Execution

# Options GPO:
# - Allow only signed scripts
# - Allow local scripts and remote signed scripts (RemoteSigned)
# - Allow all scripts (Unrestricted)
```

🔍 **Déboguer problèmes Execution Policy** :

```powershell
# Erreur commune
<#
    File C:\Scripts\test.ps1 cannot be loaded because running 
    scripts is disabled on this system.
#>

# Solutions:

# 1. Vérifier policy
Get-ExecutionPolicy -List

# 2. Changer pour CurrentUser
Set-ExecutionPolicy RemoteSigned -Scope CurrentUser

# 3. Débloquer fichier si téléchargé
Unblock-File C:\Scripts\test.ps1

# 4. Bypass pour test
powershell -ExecutionPolicy Bypass -File C:\Scripts\test.ps1

# 5. Vérifier zone (fichier réseau?)
Get-Content C:\Scripts\test.ps1 -Stream Zone.Identifier

# Si zone présente:
# [ZoneTransfer]
# ZoneId=3  (3 = Internet, 4 = Intranet)

# Supprimer zone
Remove-Item C:\Scripts\test.ps1 -Stream Zone.Identifier
```

💡 **Sécurité : Bonnes pratiques** :

```powershell
# ✅ RECOMMANDÉ pour la plupart des cas
Set-ExecutionPolicy RemoteSigned -Scope CurrentUser

# ✅ Débloquer scripts téléchargés de sources fiables
Unblock-File -Path .\trusted-script.ps1

# ✅ Utiliser Process scope pour tests
Set-ExecutionPolicy Bypass -Scope Process

# ⚠️ ATTENTION : Unrestricted ou Bypass permanent
# Utiliser seulement si nécessaire et comprendre les risques

# ❌ NE PAS FAIRE (désactive sécurité)
Set-ExecutionPolicy Unrestricted -Scope LocalMachine

# ✅ Pour production : AllSigned avec certificats d'entreprise
Set-ExecutionPolicy AllSigned -Scope LocalMachine

# ✅ Tester scripts dans environnement isolé d'abord
# ✅ Scanner scripts avec antivirus
# ✅ Réviser code avant exécution
```

Q30 : **B** ✅
**Explication** : `@{Name='...';Expression=...}` crée propriété calculée. `$_.WS` = Working Set (RAM).

📖 **Syntaxe complète propriétés calculées** :

```powershell
# Syntaxe longue (recommandée pour scripts)
@{Name="NouvelleColonne"; Expression={$_.Propriete * 2}}

# Syntaxe courte (alias)
@{N="NouvelleColonne"; E={$_.Propriete * 2}}
@{L="NouvelleColonne"; E={$_.Propriete * 2}}  # L = Label

# Exemple complet
Get-Process | Select-Object Name, Id,
    @{Name="Memory_MB"; Expression={[math]::Round($_.WS/1MB, 2)}},
    @{Name="CPU_Percent"; Expression={$_.CPU}}
```

💡 **Composants de la syntaxe** :

| Clé | Alias | Description |
|-----|-------|-------------|
| `Name` | `N`, `Label`, `L` | Nom de la propriété calculée |
| `Expression` | `E` | Scriptblock qui calcule la valeur |
| `FormatString` | - | Format d'affichage (optionnel) |
| `Width` | - | Largeur colonne (optionnel) |
| `Alignment` | - | Alignement (optionnel) |

⚠️ **Conversions courantes** :

```powershell
# Bytes → MB/GB
Get-Process | Select-Object Name,
    @{N="Memory_MB";E={[math]::Round($_.WS/1MB, 2)}},
    @{N="Memory_GB";E={[math]::Round($_.WS/1GB, 3)}}

# Bytes → KB avec unité
Get-ChildItem | Select-Object Name,
    @{N="Size";E={"$([math]::Round($_.Length/1KB, 2)) KB"}}

# Secondes → Minutes
Get-Process | Select-Object Name,
    @{N="CPU_Minutes";E={[math]::Round($_.CPU/60, 2)}}

# Dates : Calcul d'âge
Get-ChildItem | Select-Object Name,
    @{N="Age_Days";E={((Get-Date) - $_.LastWriteTime).Days}},
    @{N="Age_Hours";E={[math]::Round(((Get-Date) - $_.LastWriteTime).TotalHours, 1)}}

# Booléen → Texte lisible
Get-Service | Select-Object Name,
    @{N="Running";E={if($_.Status -eq "Running"){"✓ Oui"}else{"✗ Non"}}}
```

💡 **Formatage dates** :

```powershell
# Formats standards
Get-ChildItem | Select-Object Name,
    @{N="Modified";E={$_.LastWriteTime.ToString("yyyy-MM-dd")}},
    @{N="Time";E={$_.LastWriteTime.ToString("HH:mm:ss")}},
    @{N="Full";E={$_.LastWriteTime.ToString("yyyy-MM-dd HH:mm:ss")}}

# Date relative
Get-ChildItem | Select-Object Name,
    @{N="Modified";E={
        $days = ((Get-Date) - $_.LastWriteTime).Days
        if ($days -eq 0) { "Aujourd'hui" }
        elseif ($days -eq 1) { "Hier" }
        else { "Il y a $days jours" }
    }}

# Format ISO 8601
Get-Process | Select-Object Name,
    @{N="StartTime";E={$_.StartTime.ToString("o")}}  # Format ISO
```

✅ **Calculs conditionnels** :

```powershell
# If/Else dans Expression
Get-Process | Select-Object Name,
    @{N="Priority";E={
        if ($_.CPU -gt 100) { "HIGH" }
        elseif ($_.CPU -gt 50) { "MEDIUM" }
        else { "LOW" }
    }}

# Switch dans Expression
Get-Service | Select-Object Name,
    @{N="Status_FR";E={
        switch ($_.Status) {
            "Running" { "En cours" }
            "Stopped" { "Arrêté" }
            "Paused" { "En pause" }
            default { "Inconnu" }
        }
    }}

# Couleur basée sur valeur
Get-PSDrive | Where-Object {$_.Free} | Select-Object Name,
    @{N="Status";E={
        $pct = [math]::Round($_.Free/($_.Used+$_.Free)*100)
        if ($pct -lt 10) { "🔴 CRITICAL" }
        elseif ($pct -lt 20) { "🟡 WARNING" }
        else { "🟢 OK" }
    }}
```

🔍 **Combinaison de propriétés** :

```powershell
# Concaténation
Get-ADUser -Filter * | Select-Object
    @{N="FullName";E={"$($_.GivenName) $($_.Surname)"}},
    @{N="Email";E={$_.EmailAddress}}

# Calculs multiples
Get-ChildItem | Select-Object Name,
    @{N="Size_MB";E={[math]::Round($_.Length/1MB, 2)}},
    @{N="Size_GB";E={[math]::Round($_.Length/1GB, 3)}},
    @{N="SizeClass";E={
        $mb = $_.Length/1MB
        if ($mb -lt 1) { "Small" }
        elseif ($mb -lt 100) { "Medium" }
        else { "Large" }
    }}

# Propriété d'objet imbriqué
Get-Process | Select-Object Name,
    @{N="ThreadCount";E={$_.Threads.Count}},
    @{N="Modules";E={($_.Modules | Measure-Object).Count}}
```

💡 **Cas d'usage professionnels** :

```powershell
# Rapport disques avec alertes
Get-PSDrive -PSProvider FileSystem | Where-Object {$_.Free} |
    Select-Object Name,
        @{N="Used_GB";E={[math]::Round($_.Used/1GB, 2)}},
        @{N="Free_GB";E={[math]::Round($_.Free/1GB, 2)}},
        @{N="Total_GB";E={[math]::Round(($_.Used+$_.Free)/1GB, 2)}},
        @{N="Free_%";E={[math]::Round($_.Free/($_.Used+$_.Free)*100, 1)}},
        @{N="Status";E={
            $pct = $_.Free/($_.Used+$_.Free)*100
            if ($pct -lt 10) { "CRITICAL" }
            elseif ($pct -lt 20) { "WARNING" }
            else { "OK" }
        }},
        @{N="Action";E={
            $pct = $_.Free/($_.Used+$_.Free)*100
            if ($pct -lt 10) { "CLEANUP REQUIRED" }
            else { "None" }
        }} |
    Format-Table -AutoSize

# Top processus avec pourcentages
$totalMemory = (Get-CimInstance Win32_ComputerSystem).TotalPhysicalMemory

Get-Process | Select-Object Name, Id,
    @{N="Memory_MB";E={[math]::Round($_.WS/1MB, 2)}},
    @{N="Memory_%";E={[math]::Round($_.WS/$totalMemory*100, 2)}},
    @{N="CPU_Time";E={$_.CPU}},
    @{N="Threads";E={$_.Threads.Count}},
    @{N="Started";E={$_.StartTime.ToString("yyyy-MM-dd HH:mm")}} |
    Sort-Object "Memory_MB" -Descending |
    Select-Object -First 10

# Fichiers avec métadonnées enrichies
Get-ChildItem C:\Logs -Recurse -File | Select-Object
    @{N="Name";E={$_.Name}},
    @{N="Folder";E={$_.Directory.Name}},
    @{N="Size_MB";E={[math]::Round($_.Length/1MB, 2)}},
    @{N="Created";E={$_.CreationTime.ToString("yyyy-MM-dd")}},
    @{N="Modified";E={$_.LastWriteTime.ToString("yyyy-MM-dd")}},
    @{N="Age_Days";E={((Get-Date) - $_.LastWriteTime).Days}},
    @{N="Extension";E={$_.Extension.ToUpper()}},
    @{N="ToArchive";E={((Get-Date) - $_.LastWriteTime).Days -gt 30}},
    @{N="ToDelete";E={((Get-Date) - $_.LastWriteTime).Days -gt 90}} |
    Export-Csv files-report.csv -NoTypeInformation

# Services avec dépendances
Get-Service | Select-Object Name, Status, StartType,
    @{N="DisplayName";E={$_.DisplayName}},
    @{N="Dependencies";E={($_.DependentServices | Measure-Object).Count}},
    @{N="Required";E={($_.RequiredServices | Measure-Object).Count}},
    @{N="CanStop";E={if($_.CanStop){"Yes"}else{"No"}}},
    @{N="CanPause";E={if($_.CanPauseAndContinue){"Yes"}else{"No"}}},
    @{N="Status_Icon";E={
        switch ($_.Status) {
            "Running" { "✓" }
            "Stopped" { "✗" }
            default { "?" }
        }
    }} | Format-Table -AutoSize

# Utilisateurs AD avec analyse
Get-ADUser -Filter * -Properties * | Select-Object
    @{N="Username";E={$_.SamAccountName}},
    @{N="FullName";E={"$($_.GivenName) $($_.Surname)"}},
    @{N="Email";E={$_.EmailAddress}},
    @{N="Department";E={$_.Department}},
    @{N="Enabled";E={if($_.Enabled){"✓"}else{"✗"}}},
    @{N="LastLogon";E={
        if ($_.LastLogonDate) {
            $_.LastLogonDate.ToString("yyyy-MM-dd")
        } else {
            "Never"
        }
    }},
    @{N="DaysSinceLogon";E={
        if ($_.LastLogonDate) {
            ((Get-Date) - $_.LastLogonDate).Days
        } else {
            999
        }
    }},
    @{N="PasswordExpired";E={if($_.PasswordExpired){"⚠ Yes"}else{"No"}}},
    @{N="AccountStatus";E={
        if (-not $_.Enabled) { "Disabled" }
        elseif ($_.LockedOut) { "Locked" }
        elseif ($_.PasswordExpired) { "Password Expired" }
        else { "Active" }
    }} | Export-Csv users-audit.csv -NoTypeInformation
```

⚠️ **Avec Format-Table** :

```powershell
# Propriété calculée avec largeur personnalisée
Get-Process | Format-Table Name,
    @{N="Memory (MB)"; E={[math]::Round($_.WS/1MB,2)}; Width=15; Alignment="Right"},
    @{N="CPU"; E={$_.CPU}; Width=10; FormatString="N2"}

# FormatString options:
# N2 = Nombre avec 2 décimales
# C = Monétaire
# P = Pourcentage
# D = Date courte
```

---

## RÉPONSES RAPIDES

**Bash** : 1B, 2B, 3A, 4B, 5B, 6B, 7B, 8B, 9B, 10B, 11B, 12B, 13B, 14A, 15C  
**PowerShell** : 16B, 17B, 18B, 19B, 20B, 21B, 22B, 23A, 24B, 25B, 26B, 27B, 28B, 29B, 30B

---

**BARÈME** : 1 point par bonne réponse = 30 points total

**Note sur 20** : Score / 1.5

---

**ASTUCES RÉVISION** :
- **Bash** : Maîtriser conditions `[ ]`, redirections `> >>`, variables `$`, pipes `|`
- **PowerShell** : Verbe-Nom, pipeline objets, paramètres `-`, opérateurs `-eq -gt`
- **Pièges** : Espaces (`$x=1`  vs `$x = 1` ❌ Bash), casse (Bash sensible, PS non)
