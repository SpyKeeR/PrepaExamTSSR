# 🐧 FICHE DE RÉVISION EXPRESS - LINUX
## CCP3 - Exploiter des serveurs Linux

> **Objectif** : Maîtriser les commandes essentielles Linux pour l'examen TSSR

---

## 📁 SYSTÈME DE FICHIERS LINUX

### Arborescence principale
```
/           Root - Racine du système
/bin        Binaires essentiels (commandes de base)
/sbin       Binaires système (admin)
/etc        Fichiers de configuration
/home       Répertoires personnels des utilisateurs
/root       Répertoire de l'utilisateur root
/var        Données variables (logs, cache, mail)
/tmp        Fichiers temporaires
/usr        Applications et utilitaires utilisateur
/opt        Applications optionnelles
/dev        Périphériques (devices)
/proc       Informations processus (virtuel)
/sys        Informations système (virtuel)
/mnt        Points de montage temporaires
/media      Points de montage automatiques (USB, CD)
/boot       Fichiers de démarrage (noyau, GRUB)
/lib        Bibliothèques partagées
```

### Fichiers de configuration importants
```bash
/etc/passwd          # Liste des utilisateurs
/etc/shadow          # Mots de passe chiffrés
/etc/group           # Liste des groupes
/etc/gshadow         # Mots de passe des groupes
/etc/fstab           # Montage automatique des systèmes de fichiers
/etc/hosts           # Résolution DNS locale
/etc/hostname        # Nom de la machine
/etc/resolv.conf     # Configuration DNS
/etc/network/interfaces  # Config réseau (Debian)
/etc/ssh/sshd_config # Configuration serveur SSH
/etc/crontab         # Tâches planifiées système
```

---

## 👤 GESTION DES UTILISATEURS ET GROUPES

### Commandes utilisateurs
```bash
# Créer un utilisateur
useradd -m -s /bin/bash -G groupe1,groupe2 username
# -m : créer le home
# -s : shell par défaut
# -G : groupes secondaires
# -u : UID spécifique
# -d : répertoire home personnalisé

# Créer avec UID spécifique
useradd -m -u 1020 -s /bin/bash jbon

# Modifier un utilisateur
usermod -aG groupe username    # Ajouter à un groupe (sans écraser)
usermod -G groupe username     # Définir les groupes (écrase)
usermod -s /bin/bash username  # Changer le shell
usermod -L username            # Verrouiller le compte
usermod -U username            # Déverrouiller le compte

# Supprimer un utilisateur
userdel username          # Sans supprimer le home
userdel -r username       # Avec suppression du home

# Changer le mot de passe
passwd username           # Changer le mdp d'un utilisateur
passwd                    # Changer son propre mdp

# Informations utilisateur
id username               # UID, GID, groupes
whoami                    # Nom d'utilisateur actuel
who                       # Utilisateurs connectés
w                         # Utilisateurs connectés (détaillé)
last                      # Historique des connexions
```

### Commandes groupes
```bash
# Créer un groupe
groupadd nomgroupe
groupadd -g 1050 nomgroupe    # Avec GID spécifique

# Modifier un groupe
groupmod -n nouveaunom anciennom

# Supprimer un groupe
groupdel nomgroupe

# Lister les groupes d'un utilisateur
groups username
```

---

## 🔐 GESTION DES PERMISSIONS

### Comprendre les permissions
```
-rwxr-xr--  1  user  group  1234  date  fichier
│││││││││
│││││││└┴── Autres (other) : r-- (lecture seule)
│││││└┴──── Groupe : r-x (lecture + exécution)
│││└┴────── Propriétaire : rwx (lecture + écriture + exécution)
││└───────── Nombre de liens
│└────────── Type (- fichier, d répertoire, l lien)
```

### Valeurs des permissions
```
r (read)    = 4
w (write)   = 2
x (execute) = 1
- (rien)    = 0

Exemples :
rwx = 4+2+1 = 7
r-x = 4+0+1 = 5
r-- = 4+0+0 = 4
rw- = 4+2+0 = 6
```

### Commandes chmod
```bash
# Mode symbolique
chmod u+x fichier          # Ajouter exécution au propriétaire
chmod g-w fichier          # Retirer écriture au groupe
chmod o+r fichier          # Ajouter lecture aux autres
chmod a+x fichier          # Ajouter exécution à tous
chmod u+rwx,g+rx,o-rwx fichier  # Combiné

# Mode octal
chmod 755 fichier          # rwxr-xr-x
chmod 644 fichier          # rw-r--r--
chmod 700 fichier          # rwx------ (propriétaire seul)
chmod 777 fichier          # rwxrwxrwx (tous droits - DANGEREUX!)

# Récursif
chmod -R 755 dossier       # Appliquer à tous les sous-éléments
```

### Commandes chown et chgrp
```bash
# Changer le propriétaire
chown user fichier
chown user:group fichier       # Propriétaire + groupe
chown -R user:group dossier    # Récursif

# Changer le groupe
chgrp group fichier
chgrp -R group dossier
```

### umask
```bash
umask                # Afficher le umask actuel
umask 0022           # Définir le umask
# Par défaut :
# Fichiers : 666 - umask = permissions
# Dossiers : 777 - umask = permissions
```

---

## 📂 MANIPULATION DE FICHIERS

### Navigation
```bash
pwd                  # Afficher le répertoire courant
cd /chemin           # Changer de répertoire
cd ~                 # Aller au home
cd -                 # Revenir au répertoire précédent
cd ..                # Remonter d'un niveau
```

### Lister
```bash
ls                   # Lister
ls -l                # Format long (détails)
ls -a                # Afficher fichiers cachés
ls -lh               # Format lisible (K, M, G)
ls -lt               # Trier par date
ls -lS               # Trier par taille
ls -R                # Récursif
```

### Créer/Supprimer
```bash
touch fichier        # Créer fichier vide ou modifier la date
mkdir dossier        # Créer un dossier
mkdir -p /a/b/c      # Créer arborescence complète
rm fichier           # Supprimer fichier
rm -f fichier        # Forcer la suppression
rm -r dossier        # Supprimer dossier (récursif)
rm -rf dossier       # Forcer suppression récursive (DANGEREUX!)
rmdir dossier        # Supprimer dossier vide
```

### Copier/Déplacer
```bash
cp source dest       # Copier fichier
cp -r source dest    # Copier dossier (récursif)
cp -p source dest    # Conserver permissions/dates
mv source dest       # Déplacer ou renommer
```

### Rechercher
```bash
find /chemin -name "*.txt"           # Rechercher par nom
find /chemin -type f                 # Chercher fichiers
find /chemin -type d                 # Chercher dossiers
find /chemin -user username          # Par propriétaire
find /chemin -mtime -7               # Modifiés dans les 7 derniers jours
find /chemin -size +100M             # Taille > 100Mo

locate fichier                        # Recherche rapide (base de données)
updatedb                              # Mettre à jour la base locate

which commande                        # Chemin d'une commande
whereis commande                      # Localiser binaire/man/source
```

---

## 📝 LIRE ET MANIPULER DES FICHIERS

### Afficher le contenu
```bash
cat fichier          # Afficher tout le contenu
cat -n fichier       # Avec numéros de ligne
tac fichier          # Afficher en sens inverse

more fichier         # Afficher page par page
less fichier         # Afficher avec navigation (mieux que more)

head fichier         # 10 premières lignes
head -n 20 fichier   # 20 premières lignes
tail fichier         # 10 dernières lignes
tail -n 20 fichier   # 20 dernières lignes
tail -f fichier      # Suivre en temps réel (logs)
```

### Rechercher dans les fichiers
```bash
grep "texte" fichier              # Rechercher une chaîne
grep -i "texte" fichier           # Insensible à la casse
grep -r "texte" /chemin           # Récursif
grep -n "texte" fichier           # Avec numéros de ligne
grep -v "texte" fichier           # Lignes qui ne contiennent PAS
grep -c "texte" fichier           # Compter les occurrences
grep "^texte" fichier             # Lignes commençant par "texte"
grep "texte$" fichier             # Lignes finissant par "texte"
grep -E "pattern" fichier         # Regex étendue
```

### Comptage et tri
```bash
wc fichier           # Lignes, mots, caractères
wc -l fichier        # Nombre de lignes
wc -w fichier        # Nombre de mots
wc -c fichier        # Nombre de caractères

sort fichier         # Trier
sort -r fichier      # Trier en ordre inverse
sort -n fichier      # Trier numériquement
sort -u fichier      # Supprimer doublons

uniq fichier         # Supprimer lignes dupliquées consécutives
uniq -c fichier      # Compter les doublons
```

### Redirection et pipes
```bash
commande > fichier              # Rediriger sortie (écrase)
commande >> fichier             # Rediriger sortie (ajoute)
commande 2> fichier             # Rediriger erreurs
commande &> fichier             # Rediriger tout
commande < fichier              # Lire depuis fichier
commande1 | commande2           # Pipe (sortie vers entrée)

# Exemples
ls -l /etc | grep "conf" | wc -l
cat fichier | sort | uniq > resultat.txt
```

---

## 🌐 GESTION RÉSEAU

### Configuration réseau
```bash
# Afficher la configuration
ip addr show                     # Toutes les interfaces
ip a                             # Version courte
ip addr show eth0                # Une interface spécifique
ifconfig                         # Ancienne commande (deprecated)

# Configurer une IP (temporaire)
ip addr add 192.168.1.10/24 dev eth0
ip addr del 192.168.1.10/24 dev eth0

# Routes
ip route show                    # Table de routage
ip route add default via 192.168.1.1   # Route par défaut
ip route add 10.0.0.0/8 via 192.168.1.254

route -n                         # Ancienne commande

# Interface up/down
ip link set eth0 up
ip link set eth0 down
ifconfig eth0 up
ifconfig eth0 down
```

### Tests réseau
```bash
ping 8.8.8.8                     # Test connectivité
ping -c 4 google.com             # 4 paquets seulement

traceroute google.com            # Tracer la route
tracepath google.com             # Alternative

netstat -tuln                    # Ports ouverts en écoute
netstat -r                       # Table de routage
netstat -a                       # Toutes les connexions

ss -tuln                         # Remplaçant moderne de netstat
ss -tulpn                        # Avec les processus

nslookup google.com              # Résolution DNS
dig google.com                   # Résolution DNS (détaillé)
host google.com                  # Résolution DNS (simple)

# Scanner les ports (si nmap installé)
nmap -sn 192.168.1.0/24          # Scan réseau
nmap -p 1-65535 192.168.1.10     # Scan ports
```

### Fichiers de configuration réseau
```bash
# Debian/Ubuntu - /etc/network/interfaces
auto eth0
iface eth0 inet static
    address 192.168.1.10
    netmask 255.255.255.0
    gateway 192.168.1.1
    dns-nameservers 8.8.8.8 8.8.4.4

# RedHat/CentOS - /etc/sysconfig/network-scripts/ifcfg-eth0
DEVICE=eth0
BOOTPROTO=static
IPADDR=192.168.1.10
NETMASK=255.255.255.0
GATEWAY=192.168.1.1
DNS1=8.8.8.8
ONBOOT=yes

# DNS - /etc/resolv.conf
nameserver 8.8.8.8
nameserver 8.8.4.4

# Hostname
hostnamectl set-hostname nouveau-nom
cat /etc/hostname
```

---

## 🔧 GESTION DES PROCESSUS

### Lister les processus
```bash
ps                   # Processus de l'utilisateur courant
ps aux               # Tous les processus (détaillé)
ps -ef               # Tous les processus (format standard)
ps -u username       # Processus d'un utilisateur

top                  # Processus en temps réel
htop                 # Version améliorée (si installé)

pstree               # Arbre des processus
pgrep nom            # PID d'un processus par nom
pidof processus      # PID d'un processus
```

### Gérer les processus
```bash
kill PID             # Envoyer SIGTERM (arrêt propre)
kill -9 PID          # Envoyer SIGKILL (arrêt forcé)
kill -15 PID         # SIGTERM explicite
killall nom          # Tuer tous les processus par nom
pkill nom            # Tuer par nom (pattern matching)

nice -n 10 commande  # Lancer avec priorité basse
renice -5 PID        # Changer la priorité d'un processus

# Background / Foreground
commande &           # Lancer en arrière-plan
jobs                 # Lister les jobs en arrière-plan
fg %1                # Ramener au premier plan
bg %1                # Reprendre en arrière-plan
Ctrl+Z               # Suspendre un processus
Ctrl+C               # Interrompre un processus
```

---

## 🗄️ GESTION DES DISQUES ET PARTITIONS

### Afficher les disques
```bash
lsblk                # Lister les disques et partitions
lsblk -f             # Avec systèmes de fichiers
fdisk -l             # Lister les disques (détaillé)
df -h                # Espace disque utilisé (lisible)
df -T                # Avec type de système de fichiers
du -sh /chemin       # Taille d'un répertoire
du -h --max-depth=1  # Taille des sous-répertoires (1 niveau)

blkid                # UUID et type des partitions
```

### Partitionnement
```bash
fdisk /dev/sdb       # Partitionner un disque
# Commandes dans fdisk :
# n : nouvelle partition
# d : supprimer partition
# p : afficher partitions
# w : écrire et quitter
# q : quitter sans sauver

parted /dev/sdb      # Alternative plus moderne
```

### Systèmes de fichiers
```bash
# Créer un système de fichiers
mkfs.ext4 /dev/sdb1
mkfs.xfs /dev/sdb1
mkfs.vfat /dev/sdb1

# Vérifier un système de fichiers
fsck /dev/sdb1
fsck.ext4 /dev/sdb1
e2fsck /dev/sdb1

# Réparer
fsck -y /dev/sdb1    # Réparation automatique
```

### Montage
```bash
# Monter une partition
mount /dev/sdb1 /mnt
mount -t ext4 /dev/sdb1 /mnt
mount -o ro /dev/sdb1 /mnt     # Lecture seule

# Démonter
umount /mnt
umount /dev/sdb1

# Montage automatique - /etc/fstab
# Format : device  mountpoint  fstype  options  dump  pass
/dev/sdb1  /data  ext4  defaults  0  2
UUID=xxx-xxx  /data  ext4  defaults  0  2

# Monter tout ce qui est dans fstab
mount -a

# Afficher les montages
mount                # Tous les montages
findmnt              # Arbre des montages
```

### LVM (Logical Volume Manager)
```bash
# Physical Volumes
pvcreate /dev/sdb1
pvdisplay
pvscan

# Volume Groups
vgcreate vg_data /dev/sdb1
vgdisplay
vgextend vg_data /dev/sdc1

# Logical Volumes
lvcreate -L 10G -n lv_data vg_data
lvdisplay
lvextend -L +5G /dev/vg_data/lv_data
resize2fs /dev/vg_data/lv_data    # Agrandir le filesystem
```

---

## 📦 GESTION DES PAQUETS

### Debian/Ubuntu (APT)
```bash
apt update                    # Mettre à jour la liste des paquets
apt upgrade                   # Mettre à jour les paquets installés
apt full-upgrade              # Mise à jour complète
apt install paquet            # Installer un paquet
apt remove paquet             # Désinstaller (garder config)
apt purge paquet              # Désinstaller (supprimer config)
apt autoremove                # Supprimer dépendances inutiles
apt search mot-clé            # Rechercher un paquet
apt show paquet               # Informations sur un paquet
apt list --installed          # Lister paquets installés

dpkg -i paquet.deb            # Installer .deb
dpkg -l                       # Lister paquets installés
dpkg -L paquet                # Lister fichiers d'un paquet
```

### RedHat/CentOS (YUM/DNF)
```bash
yum update                    # Mettre à jour
yum install paquet            # Installer
yum remove paquet             # Désinstaller
yum search mot-clé            # Rechercher
yum info paquet               # Informations
yum list installed            # Paquets installés

dnf update                    # DNF (remplaçant de yum)
dnf install paquet

rpm -ivh paquet.rpm           # Installer .rpm
rpm -qa                       # Lister paquets installés
rpm -ql paquet                # Lister fichiers d'un paquet
```

---

## 🔐 SSH - Secure Shell

### Configuration SSH
```bash
# Fichier de configuration : /etc/ssh/sshd_config

Port 22                       # Changer le port (ex: 2222)
PermitRootLogin no            # Interdire connexion root
PasswordAuthentication yes    # Autoriser mot de passe
PubkeyAuthentication yes      # Autoriser clés publiques
AllowUsers user1 user2        # Autoriser certains utilisateurs
```

### Utilisation SSH
```bash
ssh user@192.168.1.10         # Connexion SSH
ssh -p 2222 user@serveur      # Port personnalisé
ssh user@serveur "commande"   # Exécuter une commande distante

# Copier des fichiers
scp fichier user@serveur:/chemin
scp user@serveur:/chemin fichier
scp -r dossier user@serveur:/chemin

# Clés SSH
ssh-keygen -t rsa -b 4096     # Générer une paire de clés
ssh-copy-id user@serveur      # Copier la clé publique
```

### Redémarrer le service SSH
```bash
systemctl restart sshd
systemctl status sshd
systemctl enable sshd         # Démarrage automatique
service ssh restart           # Debian/Ubuntu
```

---

## 🕐 TÂCHES PLANIFIÉES (CRON)

### Crontab
```bash
crontab -e           # Éditer le crontab de l'utilisateur
crontab -l           # Lister les tâches planifiées
crontab -r           # Supprimer le crontab

# Format : MIN HEURE JOUR MOIS JOUR_SEMAINE COMMANDE
# * * * * * commande
# │ │ │ │ │
# │ │ │ │ └─── Jour de la semaine (0-7, 0 et 7 = dimanche)
# │ │ │ └───── Mois (1-12)
# │ │ └─────── Jour du mois (1-31)
# │ └───────── Heure (0-23)
# └─────────── Minute (0-59)

# Exemples
0 2 * * * /scripts/sauvegarde.sh          # Tous les jours à 2h
*/5 * * * * /scripts/check.sh             # Toutes les 5 minutes
0 0 * * 0 /scripts/hebdo.sh               # Tous les dimanches à minuit
0 */2 1-15 2-9 * /scripts/custom.sh       # Toutes les 2h, jour 1-15, mois 2-9

# Fichiers cron système
/etc/crontab         # Crontab système
/etc/cron.d/         # Cron supplémentaires
/etc/cron.daily/     # Scripts quotidiens
/etc/cron.hourly/    # Scripts horaires
```

---

## 📊 SURVEILLANCE SYSTÈME

### Informations système
```bash
uname -a             # Informations système
uname -r             # Version du noyau
hostname             # Nom de la machine
uptime               # Temps de fonctionnement
who                  # Utilisateurs connectés
w                    # Utilisateurs + activité
last                 # Historique connexions

cat /etc/os-release  # Infos distribution
lsb_release -a       # Infos distribution (détaillé)
```

### Utilisation ressources
```bash
free -h              # Mémoire RAM
free -m              # Mémoire en Mo
cat /proc/meminfo    # Infos mémoire détaillées

df -h                # Espace disque
df -i                # Inodes

top                  # Processus en temps réel
htop                 # Version améliorée

vmstat               # Statistiques système
vmstat 2 5           # Toutes les 2 secondes, 5 fois

iostat               # Statistiques I/O
iotop                # I/O en temps réel (si installé)

mpstat               # Statistiques CPU

lscpu                # Infos CPU
cat /proc/cpuinfo    # Détails CPU
```

### Logs système
```bash
# Fichiers de logs principaux
/var/log/syslog      # Messages système (Debian/Ubuntu)
/var/log/messages    # Messages système (RedHat/CentOS)
/var/log/auth.log    # Authentification
/var/log/kern.log    # Noyau
/var/log/dmesg       # Messages boot

# Commandes logs
dmesg                # Messages du noyau
dmesg | grep -i error

journalctl           # Logs systemd
journalctl -xe       # Derniers logs (détaillés)
journalctl -u sshd   # Logs d'un service
journalctl -f        # Suivre les logs en temps réel
journalctl --since "1 hour ago"
journalctl --since "2024-11-12 10:00:00"

tail -f /var/log/syslog         # Suivre un log en temps réel
tail -n 100 /var/log/auth.log   # 100 dernières lignes
```

---

## 🚀 GESTION DES SERVICES (SYSTEMD)

### Commandes systemctl
```bash
systemctl start service      # Démarrer un service
systemctl stop service       # Arrêter un service
systemctl restart service    # Redémarrer un service
systemctl reload service     # Recharger la config
systemctl status service     # État du service
systemctl enable service     # Activer au démarrage
systemctl disable service    # Désactiver au démarrage
systemctl is-enabled service # Vérifier si activé
systemctl is-active service  # Vérifier si actif

systemctl list-units         # Lister les unités
systemctl list-units --type=service  # Lister les services
systemctl list-unit-files    # Lister tous les services
```

### Anciennes commandes (sysvinit)
```bash
service ssh start
service ssh stop
service ssh restart
service ssh status
/etc/init.d/ssh start
```

---

## 🔑 COMMANDES IMPORTANTES POUR L'EXAMEN

### Top 20 des commandes à connaître ABSOLUMENT
```bash
1.  ls -la                    # Lister fichiers
2.  cd /chemin                # Naviguer
3.  mkdir -p /chemin          # Créer dossier
4.  rm -rf /chemin            # Supprimer
5.  cp -r source dest         # Copier
6.  mv source dest            # Déplacer/Renommer
7.  chmod 755 fichier         # Permissions
8.  chown user:group fichier  # Propriétaire
9.  cat fichier               # Afficher contenu
10. grep "texte" fichier      # Rechercher dans fichier
11. find / -name "*.conf"     # Rechercher fichiers
12. ps aux                    # Processus
13. kill -9 PID               # Tuer processus
14. systemctl restart service # Gérer services
15. ip addr show              # Config réseau
16. ping 8.8.8.8              # Test réseau
17. useradd -m username       # Créer utilisateur
18. passwd username           # Changer mot de passe
19. df -h                     # Espace disque
20. tail -f /var/log/syslog   # Suivre logs
```

---

## ⚡ ASTUCES RAPIDES

### Raccourcis clavier
```
Ctrl + C     # Interrompre commande
Ctrl + Z     # Suspendre commande
Ctrl + D     # Déconnexion (EOF)
Ctrl + L     # Effacer l'écran (= clear)
Ctrl + A     # Début de ligne
Ctrl + E     # Fin de ligne
Ctrl + U     # Supprimer jusqu'au début
Ctrl + K     # Supprimer jusqu'à la fin
Ctrl + R     # Recherche dans historique
!!           # Répéter dernière commande
!$           # Dernier argument
Tab          # Auto-complétion
```

### Variables utiles
```bash
$HOME        # Répertoire home
$USER        # Nom utilisateur
$PATH        # Chemins des exécutables
$PWD         # Répertoire courant
$SHELL       # Shell actuel
echo $HOME   # Afficher une variable
```

---

## 🎯 POINTS CRITIQUES POUR L'EXAMEN

### ⚠️ À ne JAMAIS oublier
1. **Changer port SSH** : modifier `/etc/ssh/sshd_config` → `Port 2222` → `systemctl restart sshd`
2. **Permissions web** : `chown www-data:www-data /var/www` et `chmod 755`
3. **Ajouter utilisateur à un groupe SANS écraser** : `usermod -aG groupe user`
4. **Créer utilisateur complet** : `useradd -m -s /bin/bash -G groupes username`
5. **Chmod récursif** : `chmod -R 755 dossier`
6. **Chercher processus** : `ps aux | grep nom`
7. **Ports ouverts** : `netstat -tuln` ou `ss -tuln`
8. **Logs en temps réel** : `tail -f /var/log/syslog`
9. **Redémarrer service** : `systemctl restart service`
10. **Tester config avant restart** : toujours vérifier la syntaxe !

---

## 📚 POUR ALLER PLUS LOIN

- Man pages : `man commande`
- Help : `commande --help`
- Apropos : `apropos mot-clé` (chercher dans les man pages)
- Info : `info commande`

---

**🎓 Tu maîtrises cette fiche = Tu maîtrises Linux pour l'examen TSSR ! 💪**
