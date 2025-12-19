
## ✅ CORRECTIONS DÉTAILLÉES

### PARTIE 1 - SYSTÈME DE FICHIERS

**Question 1 : B) `/etc` - Répertoire des fichiers de configuration système**

Le répertoire `/etc` est le **cœur de la configuration Linux** contenant tous les fichiers de configuration système.

**Fichiers essentiels** :

| Fichier | Rôle |
|---------|------|
| `/etc/passwd` | Base utilisateurs |
| `/etc/shadow` | Mots de passe chiffrés |
| `/etc/group` | Groupes système |
| `/etc/fstab` | Montages automatiques |
| `/etc/resolv.conf` | Serveurs DNS |
| `/etc/ssh/sshd_config` | Configuration SSH |

**Commandes utiles** :
```bash
ls -l /etc/*.conf          # Lister configs
sudo cp /etc/file /etc/file.backup  # Backup avant modif
```

**Question 2 : B) `/home` - Répertoires personnels des utilisateurs**

Le répertoire `/home` contient les **espaces personnels des utilisateurs**. Chaque utilisateur possède un sous-répertoire (ex: `/home/alice`).

**Structure** :
```
/home/alice/
├── Documents/, Downloads/
├── .bashrc     # Config shell
└── .ssh/       # Clés SSH
```

**Variables et navigation** :
```bash
echo $HOME      # Affiche /home/alice
cd ~            # Va dans son home
```

**Cas particulier root** : `/root` (et non `/home/root`) pour garantir l'accès même si `/home` ne monte pas.

**Commandes** :
```bash
du -sh /home/*              # Espace par utilisateur
sudo useradd -m username    # Créer avec home
```

**Question 3 : B) `/etc/passwd` - Base de données des utilisateurs**

Contient les **informations de tous les comptes utilisateurs**.

**Format** : `username:x:UID:GID:GECOS:home:shell`

**Champs principaux** :

| Champ | Description | Exemple |
|-------|-------------|--------|
| Username | Nom de connexion | `alice` |
| UID | User ID (0=root, ≥1000=users) | `1001` |
| GID | Group ID | `1001` |
| Home | Répertoire personnel | `/home/alice` |
| Shell | Interpréteur | `/bin/bash` ou `/usr/sbin/nologin` |

**Exemple** :
```
alice:x:1001:1001:Alice:/home/alice:/bin/bash
```

**Commandes** :
```bash
grep alice /etc/passwd      # Trouver utilisateur
awk -F: '$3 >= 1000 {print $1}' /etc/passwd  # Users normaux
sudo pwck                   # Vérifier intégrité
```

⚠️ `x` indique que le hash est dans `/etc/shadow` (plus sécurisé).

**Question 4 : B) `/etc/shadow` - Mots de passe chiffrés**

Stocke les **mots de passe chiffrés** (lisible root uniquement).

**Format** : `username:hash:lastchange:min:max:warn:inactive:expire`

**Types de hash** :

| Préfixe | Algorithme | Sécurité |
|---------|------------|----------|
| `$6$` | SHA-512 | ✅ Standard actuel |
| `$y$` | yescrypt | ✅ Moderne |
| `!` ou `*` | Verrouillé | Compte désactivé |

**Exemple** :
```
alice:$6$salt$hash...:19000:0:99999:7:::
```

**Commandes** :
```bash
sudo passwd alice       # Changer mot de passe
sudo passwd -l alice    # Verrouiller
sudo passwd -S alice    # Voir statut
sudo chage -l alice     # Politique expiration
```

**Permissions critiques** : `-rw-r----- root:shadow`

**Question 5 : B) `/var/log` - Répertoire des fichiers journaux système**

Centralise **tous les journaux système et applications**.

**Fichiers essentiels** :

| Fichier | Contenu |
|---------|--------|
| `syslog`/`messages` | Logs système |
| `auth.log` | SSH, sudo |
| `kern.log` | Noyau |
| `apache2/access.log` | Web |

**Commandes** :
```bash
tail -f /var/log/syslog
grep -i error /var/log/syslog
journalctl -u ssh --since today
```
ls /etc/logrotate.d/

# Exemple config Apache
/var/log/apache2/*.log {
    daily               # Rotation quotidienne
    rotate 14           # Garder 14 jours
    compress            # Compresser anciens
    delaycompress       # Compresser J-1 (pas J)
    notifempty          # Skip si vide
    create 0640 www-data adm
    sharedscripts
    postrotate
        /etc/init.d/apache2 reload > /dev/null
    endscript
}

# Forcer rotation manuelle
sudo logrotate -f /etc/logrotate.conf
```

**Analyse avancée** :
```bash
# Top 10 IP dans access.log
awk '{print $1}' /var/log/apache2/access.log | sort | uniq -c | sort -rn | head -10

# Codes HTTP retournés
awk '{print $9}' /var/log/apache2/access.log | sort | uniq -c

# Pages les plus visitées
awk '{print $7}' /var/log/apache2/access.log | sort | uniq -c | sort -rn | head -20

# Détection attaques
sudo grep 'Failed password' /var/log/auth.log | awk '{print $(NF-3)}' | sort | uniq -c | sort -rn
```

**Gestion espace disque** :
```bash
# Vérifier taille logs
du -sh /var/log/*
du -sh /var/log/apache2/

# Logs les plus gros
du -h /var/log/* | sort -rh | head -10

# Nettoyer vieux logs (prudence !)
sudo find /var/log -name "*.gz" -mtime +30 -delete
sudo journalctl --vacuum-time=7d      # Garder 7 jours
sudo journalctl --vacuum-size=500M    # Limite 500MB
```

⚠️ **Sécurité et bonnes pratiques** :
- Surveiller `/var/log/auth.log` quotidiennement (détection intrusions)
- Centraliser logs vers serveur syslog (rsyslog, syslog-ng)
- Archiver logs critiques (conformité, forensics)
- Protéger permissions (chmod 640, propriétaire root)
- Implémenter SIEM si infrastructure importante

💡 **Outils complémentaires** :
```bash
# Fail2ban : bannir IP après tentatives échouées
sudo apt install fail2ban

# Logwatch : résumé quotidien par email
sudo apt install logwatch

# GoAccess : analyseur web logs en temps réel
sudo apt install goaccess
goaccess /var/log/apache2/access.log -o report.html --log-format=COMBINED
```

**Question 6 : B) `/etc/fstab` - Table des systèmes de fichiers montés automatiquement**

Définit **partitions montées au boot**.

**Format** : `<device> <mountpoint> <filesystem> <options> <dump> <pass>`

**Exemple** :
```bash
UUID=abc123...  /       ext4   defaults  0  1
UUID=789xyz...  /home   ext4   noatime   0  2
```

**Tester avant reboot** :
```bash
sudo mount -a  # Test syntaxe
```

**Options de montage courantes** :

| Option | Effet | Usage |
|--------|-------|-------|
| `defaults` | rw,suid,dev,exec,auto,nouser,async | Standard |
| `noatime` | Pas MAJ date accès (performance) | Serveurs, SSD |
| `ro` | Lecture seule | Sécurité |
| `rw` | Lecture/écriture | Normal |
| `noexec` | Pas exécution binaires | `/tmp`, sécurité |
| `nosuid` | Ignore bit SUID | Sécurité |
| `nodev` | Pas périphériques | Sécurité |
| `user` | Montage par utilisateurs | Clés USB |
| `noauto` | Pas montage auto boot | Montage manuel |
| `nofail` | Continue boot si échec | Partitions optionnelles |

**UUID vs /dev/sdX** :
```bash
# Trouver UUID d'une partition
sudo blkid
sudo blkid /dev/sda1

# Sortie exemple
/dev/sda1: UUID="abc123-def4-5678" TYPE="ext4" PARTUUID="..."

# UUID recommandé car stable (ordre /dev/sdX peut changer)
```

**Tester fstab AVANT reboot** :
```bash
# Démonter tout
sudo umount -a

# Remonter selon fstab (teste syntaxe)
sudo mount -a

# Si erreur : corriger fstab AVANT reboot !
# Sinon : système non bootable !

# Vérifier montages
df -h
mount | grep ^/dev
```

**Montage partitions spécifiques** :
```bash
# Monter partition NTFS Windows
UUID=xxx /mnt/windows ntfs-3g defaults,permissions,locale=fr_FR.UTF-8 0 0

# Partition temporaire en RAM
tmpfs /tmp tmpfs defaults,noatime,nosuid,nodev,size=2G 0 0

# Partage NFS
192.168.1.50:/export/data /mnt/nfs nfs defaults,_netdev 0 0

# Partage CIFS/SMB Windows
//192.168.1.100/Public /mnt/public cifs username=alice,password=pass123,uid=1000,gid=1000 0 0

# Mieux : credentials file
//server/share /mnt/share cifs credentials=/root/.smbcreds,uid=1000 0 0
```

**Fichier credentials CIFS** :
```bash
# Créer /root/.smbcreds
sudo nano /root/.smbcreds

username=alice
password=MonP@ss123
domain=ENTREPRISE

# Sécuriser
sudo chmod 600 /root/.smbcreds
```

**Dépannage** :
```bash
# Système ne boote pas après modif fstab
# 1. Booter en mode rescue/single
# 2. Remonter / en RW
mount -o remount,rw /

# 3. Éditer fstab
nano /etc/fstab

# 4. Commenter ligne problématique
# UUID=xxx /mnt/probleme ext4 defaults 0 2

# 5. Reboot
reboot
```

🔧 **Bonnes pratiques** :
- **Toujours** utiliser UUID (pas /dev/sdX)
- Tester avec `mount -a` avant reboot
- Utiliser `nofail` pour partitions optionnelles (USB, NFS)
- Sécuriser credentials (chmod 600)
- Commenter chaque ligne (# Description)
- Backup avant modif : `sudo cp /etc/fstab /etc/fstab.backup`

💡 **Astuce systemd** :
```bash
# Alternative moderne : systemd mount units
sudo systemctl list-units --type=mount

# Générer mount unit depuis fstab
sudo systemd-fstab-generator
```

**Question 7 : C) `/dev` - Périphériques (devices) matériels**

Le répertoire `/dev` contient les **fichiers spéciaux représentant les périphériques** :

**Concept** : Sous Linux, **tout est fichier**, y compris le matériel !

**Types de périphériques** :

| Type | Description | Exemples |
|------|-------------|----------|
| **Block devices** (b) | Accès par blocs (disques) | `/dev/sda`, `/dev/sdb1`, `/dev/nvme0n1` |
| **Character devices** (c) | Accès caractère par caractère | `/dev/tty`, `/dev/null`, `/dev/random` |
| **Symbolic links** (l) | Liens symboliques | `/dev/disk/by-uuid/xxx` |

**Périphériques essentiels** :

```bash
# Lister périphériques avec types
ls -l /dev/ | head -20

# Périphériques disques
/dev/sda        # Premier disque SATA/SCSI
/dev/sda1       # Première partition du disque sda
/dev/sda2       # Deuxième partition
/dev/sdb        # Deuxième disque
/dev/nvme0n1    # Disque NVMe (SSD moderne)
/dev/nvme0n1p1  # Partition 1 du NVMe

# Périphériques terminaux
/dev/tty        # Terminal actuel
/dev/tty1       # Console virtuelle 1 (Ctrl+Alt+F1)
/dev/pts/0      # Pseudo-terminal (SSH, terminal GUI)

# Périphériques spéciaux
/dev/null       # "Trou noir" - supprime tout
/dev/zero       # Source infinie de zéros
/dev/random     # Générateur aléatoire (bloquant)
/dev/urandom    # Générateur aléatoire (non bloquant)

# Périphériques réseau (via /sys maintenant)
/dev/loop0      # Périphérique loop (montage images ISO)
```

**Numéros Major et Minor** :
```bash
# Afficher major/minor
ls -l /dev/sda*

brw-rw---- 1 root disk 8, 0 Dec 19 10:00 /dev/sda
brw-rw---- 1 root disk 8, 1 Dec 19 10:00 /dev/sda1
brw-rw---- 1 root disk 8, 2 Dec 19 10:00 /dev/sda2
#                     ↑  ↑
#                  Major Minor

# Major = type driver (8 = SCSI disk)
# Minor = instance spécifique (0=sda, 1=sda1, 16=sdb, etc.)
```

**Utilisations pratiques** :

```bash
# Écrire sur disque directement
sudo dd if=/dev/zero of=/dev/sdb bs=1M count=100  # Écrit 100MB de zéros

# Créer image disque
sudo dd if=/dev/sda of=/backup/disk.img bs=4M status=progress

# Supprimer sortie commande
commande > /dev/null        # Supprime stdout
commande 2> /dev/null       # Supprime stderr
commande &> /dev/null       # Supprime tout

# Générer données aléatoires
dd if=/dev/urandom of=random.dat bs=1M count=10

# Monter image ISO
sudo mount -o loop image.iso /mnt/iso
# Équivalent à :
sudo losetup /dev/loop0 image.iso
sudo mount /dev/loop0 /mnt/iso
```

**Organisation par UUID/label** :
```bash
# Liens symboliques pratiques
ls -l /dev/disk/by-uuid/
ls -l /dev/disk/by-label/
ls -l /dev/disk/by-id/
ls -l /dev/disk/by-path/

# Exemple
lrwxrwxrwx 1 root root 10 Dec 19 /dev/disk/by-uuid/abc123... -> ../../sda1
```

**Informations périphériques** :
```bash
# Lister disques et partitions
lsblk
lsblk -f    # Avec UUID et filesystem

# Sortie exemple
NAME   MAJ:MIN RM   SIZE RO TYPE MOUNTPOINT
sda      8:0    0 238.5G  0 disk 
├─sda1   8:1    0   512M  0 part /boot/efi
├─sda2   8:2    0   200G  0 part /
└─sda3   8:3    0  38.5G  0 part [SWAP]

# Détails périphérique
sudo fdisk -l /dev/sda
sudo hdparm -I /dev/sda     # Infos disque
sudo smartctl -a /dev/sda   # SMART (santé disque)

# Partitions montées
df -h
mount | grep ^/dev
```

**Création périphérique (rare, géré par udev)** :
```bash
# Créer fichier périphérique manuellement
sudo mknod /dev/mydevice b 8 0
#                         ↑ ↑ ↑
#                       type M m
# b = block, c = character
```

**udev : gestionnaire périphériques dynamiques** :
```bash
# Règles udev
ls /etc/udev/rules.d/
ls /lib/udev/rules.d/

# Exemple règle : renommer interface réseau
# /etc/udev/rules.d/70-persistent-net.rules
SUBSYSTEM=="net", ACTION=="add", ATTR{address}=="00:11:22:33:44:55", NAME="eth0"

# Recharger règles
sudo udevadm control --reload-rules
sudo udevadm trigger

# Surveiller événements udev en temps réel
sudo udevadm monitor
```

⚠️ **Attention dangers** :
```bash
# DANGEREUX : Efface tout le disque !
sudo dd if=/dev/zero of=/dev/sda  # ❌ Perte totale données

# Toujours vérifier device AVANT dd/mkfs !
lsblk           # Confirmer bon périphérique
sudo fdisk -l   # Double vérification
```

💡 **Astuces** :
- `/dev/null` : supprimer output inutile
- `/dev/urandom` : générer mots de passe, clés
- `lsblk` : vue claire disques/partitions
- `udevadm` : debug détection matériel

**Question 8 : B) `/etc/resolv.conf` - Configuration des serveurs DNS**

Définit **serveurs DNS** pour résolution noms.

**Format** :
```bash
nameserver 8.8.8.8
nameserver 8.8.4.4
search entreprise.local
```

**DNS publics** : Google (8.8.8.8), Cloudflare (1.1.1.1), Quad9 (9.9.9.9)

**Tests** :
```bash
ping google.com
nslookup google.com
dig google.com
```
| `nameserver` | Serveur DNS (max 3) | `nameserver 8.8.8.8` |
| `search` | Domaines recherche automatique | `search local domain.com` |
| `domain` | Domaine local | `domain entreprise.local` |
| `options` | Options résolution | `options timeout:2` |
| `sortlist` | Ordre préférence réseaux | `sortlist 192.168.0.0/16` |

**Serveurs DNS publics courants** :

| Provider | DNS primaire | DNS secondaire | Caractéristiques |
|----------|--------------|----------------|------------------|
| **Google** | `8.8.8.8` | `8.8.4.4` | Rapide, fiable |
| **Cloudflare** | `1.1.1.1` | `1.0.0.1` | Rapide, privé |
| **Quad9** | `9.9.9.9` | `149.112.112.112` | Sécurisé, bloque malware |
| **OpenDNS** | `208.67.222.222` | `208.67.220.220` | Filtrage contenu |
| **DNS local** | `192.168.1.1` | - | Routeur/serveur local |

**Directive search - résolution automatique** :
```bash
# Avec search entreprise.local example.com
search entreprise.local example.com

# Ping "serveur1" cherchera automatiquement :
ping serveur1
# → Essaie : serveur1.entreprise.local
# → Puis : serveur1.example.com
# → Puis : serveur1 (si échecs)

# Exemple pratique
ping dc01
# Résout automatiquement dc01.entreprise.local
```

**Options avancées** :
```bash
options timeout:2      # Timeout requête DNS (secondes)
options attempts:3     # Nombre tentatives
options rotate         # Rotation serveurs (load balancing)
options ndots:2        # Nombre points avant search
options edns0          # Extensions DNS (DNSSEC)
```

**Gestion moderne avec systemd-resolved** :
```bash
# Sur Ubuntu 18.04+, resolv.conf = lien symbolique
ls -l /etc/resolv.conf
# lrwxrwxrwx ... /etc/resolv.conf -> ../run/systemd/resolve/stub-resolv.conf

# Statut systemd-resolved
systemd-resolve --status
resolvectl status

# Configuration DNS via systemd
sudo nano /etc/systemd/resolved.conf

[Resolve]
DNS=8.8.8.8 1.1.1.1
FallbackDNS=8.8.4.4 1.0.0.1
Domains=entreprise.local
DNSSEC=allow-downgrade

# Redémarrer
sudo systemctl restart systemd-resolved
```

**Méthodes de configuration selon distribution** :

**Debian/Ubuntu (netplan)** :
```yaml
# /etc/netplan/01-netcfg.yaml
network:
  version: 2
  ethernets:
    eth0:
      dhcp4: no
      addresses:
        - 192.168.1.100/24
      gateway4: 192.168.1.1
      nameservers:
        addresses: [8.8.8.8, 8.8.4.4]
        search: [entreprise.local]

# Appliquer
sudo netplan apply
```

**Debian/Ubuntu (interfaces - ancien)** :
```bash
# /etc/network/interfaces
auto eth0
iface eth0 inet static
    address 192.168.1.100
    netmask 255.255.255.0
    gateway 192.168.1.1
    dns-nameservers 8.8.8.8 8.8.4.4
    dns-search entreprise.local
```

**RedHat/CentOS (NetworkManager)** :
```bash
# Via nmcli
sudo nmcli con mod eth0 ipv4.dns "8.8.8.8 8.8.4.4"
sudo nmcli con mod eth0 ipv4.dns-search "entreprise.local"
sudo nmcli con up eth0

# Ou éditer fichier
sudo nano /etc/sysconfig/network-scripts/ifcfg-eth0

DNS1=8.8.8.8
DNS2=8.8.4.4
DOMAIN=entreprise.local
```

**Tests résolution DNS** :
```bash
# Test basique
ping google.com
nslookup google.com

# Test avec serveur spécifique
nslookup google.com 8.8.8.8

# Dig (plus détaillé)
dig google.com
dig @8.8.8.8 google.com
dig google.com +short        # Juste l'IP

# Host
host google.com
host google.com 8.8.8.8

# Résolution inverse
dig -x 8.8.8.8
host 8.8.8.8

# Tracer chemin résolution
dig google.com +trace
```

**DNS local avec dnsmasq** :
```bash
# Installer dnsmasq (cache DNS + DHCP)
sudo apt install dnsmasq

# Configurer
sudo nano /etc/dnsmasq.conf

server=8.8.8.8
server=8.8.4.4
cache-size=1000
domain=entreprise.local

# Hosts locaux
address=/serveur1.local/192.168.1.10
address=/serveur2.local/192.168.1.11

# Redémarrer
sudo systemctl restart dnsmasq

# Pointer resolv.conf vers dnsmasq
echo "nameserver 127.0.0.1" | sudo tee /etc/resolv.conf
```

**Protection contre écrasement** :
```bash
# Empêcher DHCP d'écraser resolv.conf

# Méthode 1 : immuable
sudo chattr +i /etc/resolv.conf
lsattr /etc/resolv.conf
# ----i--------e----- /etc/resolv.conf

# Retirer
sudo chattr -i /etc/resolv.conf

# Méthode 2 : NetworkManager
sudo nano /etc/NetworkManager/NetworkManager.conf
[main]
dns=none

sudo systemctl restart NetworkManager
```

⚠️ **Problèmes courants** :
```bash
# Symptôme : Pas de résolution noms
# Vérifications :
cat /etc/resolv.conf        # Serveurs corrects ?
ping 8.8.8.8                # Connectivité réseau OK ?
nslookup google.com 8.8.8.8 # DNS externe fonctionne ?
systemctl status systemd-resolved  # Service actif ?

# Flush cache DNS
sudo systemd-resolve --flush-caches
sudo systemctl restart systemd-resolved

# Ou avec nscd
sudo /etc/init.d/nscd restart
```

💡 **Bonnes pratiques** :
- Utiliser au moins 2 serveurs DNS (redondance)
- DNS primaire = local/rapide, secondaire = public (Google, Cloudflare)
- Dans `/etc/hosts` : hosts critiques (serveurs internes)
- Surveiller temps résolution : `dig google.com | grep "Query time"`
- DNS Split-Horizon : internes via DNS local, externes via public

**Question 9 : B) `/etc/ssh/sshd_config` - Configuration du serveur SSH**

Configure **serveur SSH** (daemon sshd).

**Options sécurité** :
```bash
Port 2222                    # Port custom
PermitRootLogin no           # Bloquer root
PasswordAuthentication no    # Forcer clés SSH
PubkeyAuthentication yes     # Autoriser clés
```

**Valider & redémarrer** :
```bash
sudo sshd -t
sudo systemctl restart sshd
```

# Écoute sur IP spécifique
ListenAddress 0.0.0.0           # Toutes interfaces
# ListenAddress 192.168.1.10    # IP spécifique

# Protocole (SSH-2 uniquement, SSH-1 obsolète)
Protocol 2

# Connexion root
PermitRootLogin no              # ✅ Recommandé : interdire root direct
# PermitRootLogin prohibit-password  # Alternative : autoriser avec clé uniquement
# PermitRootLogin yes            # ❌ Dangereux

# Authentification par mot de passe
PasswordAuthentication no       # ✅ Forcer clés SSH
# PasswordAuthentication yes     # Permettre mots de passe

# Authentification par clé publique
PubkeyAuthentication yes        # ✅ Recommandé

# Fichier clés autorisées
AuthorizedKeysFile .ssh/authorized_keys

# Authentification vide (DANGER)
PermitEmptyPasswords no         # ✅ Toujours no

# X11 Forwarding
X11Forwarding no                # Désactiver si inutile

# Timeout connexion
ClientAliveInterval 300         # Ping client toutes les 5 min
ClientAliveCountMax 2           # 2 échecs = déconnexion

# Tentatives authentification
MaxAuthTries 3                  # 3 essais max
MaxSessions 10                  # 10 sessions simultanées

# Login grace time
LoginGraceTime 60               # 60 sec pour s'authentifier

# Banner
Banner /etc/ssh/banner.txt      # Message avant login
```

**Restrictions d'accès par utilisateur/groupe** :
```bash
# Autoriser uniquement certains utilisateurs
AllowUsers alice bob charlie

# Autoriser groupe
AllowGroups sshusers admins

# Interdire utilisateurs
DenyUsers root guest

# Interdire groupes
DenyGroups nologin

# Combinaison
AllowGroups sshusers
DenyUsers testuser
```

**Configuration par IP/réseau** :
```bash
# Autoriser root uniquement depuis LAN
Match Address 192.168.1.0/24
    PermitRootLogin yes
    PasswordAuthentication yes

Match Address *,!192.168.1.0/24
    PermitRootLogin no
    PasswordAuthentication no
```

**Authentification par clés SSH** :

**Côté client - génération clé** :
```bash
# Générer paire clés (sur machine cliente)
ssh-keygen -t ed25519 -C "alice@laptop"
# Ou RSA 4096 bits
ssh-keygen -t rsa -b 4096 -C "alice@laptop"

# Fichiers créés
~/.ssh/id_ed25519      # Clé PRIVÉE (garder secrète !)
~/.ssh/id_ed25519.pub  # Clé PUBLIQUE (à copier sur serveur)

# Copier clé sur serveur
ssh-copy-id -i ~/.ssh/id_ed25519.pub alice@serveur.com

# Ou manuellement
cat ~/.ssh/id_ed25519.pub | ssh alice@serveur.com "mkdir -p ~/.ssh && cat >> ~/.ssh/authorized_keys"

# Connexion (clé utilisée automatiquement)
ssh alice@serveur.com
```

**Côté serveur - authorized_keys** :
```bash
# Structure
~/.ssh/authorized_keys

# Permissions CRITIQUES
chmod 700 ~/.ssh
chmod 600 ~/.ssh/authorized_keys

# Contenu (une clé par ligne)
ssh-ed25519 AAAAC3Nza...qeXXD alice@laptop
ssh-rsa AAAAB3NzaC1...vfE2B bob@desktop

# Avec options
no-port-forwarding,no-X11-forwarding ssh-ed25519 AAAAC3Nz... restricted@host
from="192.168.1.0/24" ssh-rsa AAAAB3... admin@office
command="/usr/bin/rsync" ssh-rsa AAAAB3... backup@server
```

**Validation et redémarrage** :
```bash
# Tester syntaxe configuration
sudo sshd -t

# Si OK, redémarrer
sudo systemctl restart sshd
sudo systemctl status sshd

# Logs SSH (surveillance)
sudo tail -f /var/log/auth.log | grep sshd
sudo journalctl -u sshd -f

# Connexions actives
who
w
last | head -20
```

**Sécurité avancée** :

**Fail2ban - bannir attaques brute force** :
```bash
# Installer
sudo apt install fail2ban

# Configurer
sudo nano /etc/fail2ban/jail.local

[sshd]
enabled = true
port = 2222
logpath = /var/log/auth.log
maxretry = 3
bantime = 3600
findtime = 600

# Redémarrer
sudo systemctl restart fail2ban

# Voir bans actifs
sudo fail2ban-client status sshd
```

**Authentification à deux facteurs (2FA)** :
```bash
# Installer Google Authenticator
sudo apt install libpam-google-authenticator

# Configurer pour utilisateur
google-authenticator
# Scanner QR code avec app mobile

# Activer dans PAM
sudo nano /etc/pam.d/sshd
auth required pam_google_authenticator.so

# Activer dans sshd_config
ChallengeResponseAuthentication yes
AuthenticationMethods publickey,keyboard-interactive

sudo systemctl restart sshd
```

**Tunnels et redirections** :
```bash
# Port forwarding local
ssh -L 8080:localhost:80 user@serveur
# http://localhost:8080 → serveur:80

# Port forwarding remote
ssh -R 9090:localhost:3000 user@serveur
# serveur:9090 → client:3000

# SOCKS proxy
ssh -D 1080 user@serveur
# Configure navigateur : SOCKS5 localhost:1080

# Configurer dans sshd_config
AllowTcpForwarding yes
GatewayPorts no
```

**Fichiers et permissions SSH** :

| Fichier/Dossier | Permissions | Propriétaire |
|-----------------|-------------|---------------|
| `~/.ssh/` | `700` (drwx------) | user:user |
| `~/.ssh/id_rsa` | `600` (-rw-------) | user:user |
| `~/.ssh/id_rsa.pub` | `644` (-rw-r--r--) | user:user |
| `~/.ssh/authorized_keys` | `600` (-rw-------) | user:user |
| `~/.ssh/known_hosts` | `600` (-rw-------) | user:user |
| `~/.ssh/config` | `600` (-rw-------) | user:user |

⚠️ **Erreurs à éviter** :
```bash
# Tester AVANT de fermer session actuelle !
# Ouvrir NOUVELLE fenêtre terminal, tester connexion
# Si ça marche pas : corriger dans session originale
# Sinon : risque lockout (plus d'accès serveur) !

# Backup configuration
sudo cp /etc/ssh/sshd_config /etc/ssh/sshd_config.backup
```

💡 **Configuration client (~/.ssh/config)** :
```bash
# Simplifier connexions
nano ~/.ssh/config

Host prod
    HostName 192.168.1.10
    User alice
    Port 2222
    IdentityFile ~/.ssh/id_ed25519_prod
    
Host *.local
    User admin
    Port 22
    IdentityFile ~/.ssh/id_rsa

# Connexion simplifiée
ssh prod  # Au lieu de : ssh -p 2222 alice@192.168.1.10
```

**Question 10 : A) `/bin` - Commandes essentielles du système**

Contient **commandes essentielles** pour tous utilisateurs.

**Hiérarchie** : `/bin` (essentielles), `/sbin` (admin), `/usr/bin` (non-essentielles)

**Commandes** :
```bash
ls, cp, mv, rm      # Fichiers
ps, kill, date      # Système
bash, sh            # Shells
gzip, tar           # Compression
```

**Variable PATH** : `echo $PATH` → ordre de recherche
**Localiser** : `which ls`, `type ls`
mkdir       # Créer dossier
rmdir       # Supprimer dossier vide
touch       # Créer fichier vide
cat         # Afficher contenu
less        # Lecture paginée
more        # Idem (ancien)

# Commandes système
ps          # Processus
kill        # Tuer processus
date        # Date/heure
echo        # Afficher texte
sleep       # Pause

# Commandes shell
bash        # Shell Bash
sh          # Shell standard
dash        # Shell léger (sh moderne)

# Compression
gzip        # Compresser
gunzip      # Décompresser
bzip2       # Compression bzip2
tar         # Archives

# Réseau basique
ping        # Test connectivité
netstat     # Statistiques réseau

# Édition
nano        # Éditeur simple
vi          # Éditeur standard

# Recherche
grep        # Recherche texte
find        # Recherche fichiers
```

**Commandes dans `/sbin` (System Binaries)** :
```bash
# Administration système (nécessitent souvent root)
ls /sbin/ | grep -E "(init|shutdown|reboot|ifconfig|ip|route)"

ifconfig    # Config réseau (obsolète)
ip          # Config réseau moderne
route       # Table routage
shutdown    # Arrêt système
reboot      # Redémarrage
init        # Changement runlevel
fdisk       # Partitionnement disques
mkfs        # Créer système fichiers
fsck        # Vérifier/réparer FS
mount       # Monter partitions
umount      # Démonter
iptables    # Pare-feu
modprobe    # Charger modules kernel
```

**Variable PATH** :
```bash
# Afficher PATH
echo $PATH
# /usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin

# Ordre de recherche (premier trouvé = exécuté)
1. /usr/local/sbin
2. /usr/local/bin
3. /usr/sbin
4. /usr/bin
5. /sbin
6. /bin

# Trouver emplacement commande
which ls
# /usr/bin/ls

which -a python
# /usr/bin/python
# /usr/local/bin/python

type -a ls
# ls is aliased to `ls --color=auto'
# ls is /usr/bin/ls

# Localiser toutes occurrences
whereis ls
# ls: /usr/bin/ls /usr/share/man/man1/ls.1.gz
```

**Ajouter au PATH** :
```bash
# Temporaire (session actuelle)
export PATH=$PATH:/opt/myapp/bin

# Permanent (utilisateur)
echo 'export PATH=$PATH:/opt/myapp/bin' >> ~/.bashrc
source ~/.bashrc

# Permanent (global tous utilisateurs)
echo 'export PATH=$PATH:/opt/myapp/bin' | sudo tee -a /etc/profile
# Ou créer fichier
sudo nano /etc/profile.d/myapp.sh
export PATH=$PATH:/opt/myapp/bin

sudo chmod +x /etc/profile.d/myapp.sh
```

**Différence binaires vs scripts** :
```bash
# Voir type fichier
file /bin/ls
# /bin/ls: ELF 64-bit LSB executable...

file /usr/bin/apt
# /usr/bin/apt: Python script, ASCII text executable

# Lister binaires ELF
find /bin -type f -exec file {} \; | grep ELF

# Lister scripts
find /bin -type f -exec file {} \; | grep script
```

**Liens symboliques dans /bin** :
```bash
# Beaucoup de commandes sont des liens
ls -l /bin/ | grep '^l'

# Exemples
lrwxrwxrwx 1 root root 4 /bin/sh -> dash
lrwxrwxrwx 1 root root 7 /bin/uncompress -> gunzip
lrwxrwxrwx 1 root root 6 /bin/bunzip2 -> bzip2

# /bin souvent lien vers /usr/bin (modern)
ls -ld /bin
lrwxrwxrwx 1 root root 7 /bin -> usr/bin
```

**Commandes de diagnostic** :
```bash
# Lister toutes commandes disponibles
compgen -c | sort | less

# Commandes built-in du shell
help
compgen -b

# Commandes externes
compgen -c | while read cmd; do which $cmd 2>/dev/null; done | sort -u

# Statistiques
ls /bin | wc -l        # Nombre binaires dans /bin
ls /usr/bin | wc -l    # Nombre dans /usr/bin
```

**Installation logiciels** :

| Méthode | Emplacement binaires | Gestion |
|---------|---------------------|----------|
| **Paquet distro** (apt/yum) | `/usr/bin`, `/usr/sbin` | Gestionnaire paquets |
| **Compilation source** | `/usr/local/bin` | Manuelle |
| **Snap** | `/snap/bin` | snapd |
| **Flatpak** | `/var/lib/flatpak/exports/bin` | flatpak |
| **AppImage** | Où vous voulez | Portable |
| **Docker** | Conteneur isolé | docker |

**Sécurité et permissions** :
```bash
# Binaires avec SUID (s'exécutent avec droits propriétaire)
find /bin /sbin /usr/bin /usr/sbin -perm -4000 -ls

# Exemples SUID légitimes
-rwsr-xr-x 1 root root /usr/bin/passwd    # Changer MDP (écrit /etc/shadow)
-rwsr-xr-x 1 root root /usr/bin/sudo     # Élévation privilèges
-rwsr-xr-x 1 root root /bin/ping         # Socket raw (ICMP)

# ⚠️ Surveiller SUID non autorisés (backdoors potentiels)
```

**Backup binaires système** :
```bash
# Créer liste binaires
dpkg -L | grep '/bin/' > /backup/binaries_list.txt

# Checksums (détection modifications)
sudo sha256sum /bin/* /sbin/* > /backup/binaries_checksums.txt

# Vérifier intégrité ultérieurement
sudo sha256sum -c /backup/binaries_checksums.txt
```

💡 **Astuces pratiques** :
```bash
# Exécuter binaire sans être dans PATH
./monprogramme        # Dossier courant
/chemin/complet/prog  # Chemin absolu

# Ignorer alias et exécuter binaire original
\ls            # Ignore alias ls='ls --color=auto'
command ls     # Alternative
/usr/bin/ls    # Chemin complet

# Mesurer taille répertoires binaires
du -sh /bin /sbin /usr/bin /usr/sbin
```

🔧 **Différence historique vs moderne** :

**Traditionnel** :
- `/bin` : commandes essentielles utilisateurs
- `/sbin` : commandes essentielles admin
- `/usr/bin` : commandes supplémentaires
- `/usr/sbin` : outils admin supplémentaires

**Moderne (systemd)** :
- `/bin` → `/usr/bin` (lien symbolique)
- `/sbin` → `/usr/sbin` (lien symbolique)
- Tout dans `/usr/bin` et `/usr/sbin`
- Simplifie structure, garde compatibilité

---

### PARTIE 2 - GESTION UTILISATEURS ET GROUPES

**Question 11 : B) `useradd -m username` - Créer un utilisateur avec son home**

La commande `useradd` crée de nouveaux comptes utilisateurs :

**Options principales** :

| Option | Description | Exemple |
|--------|-------------|---------|
| `-m` | **Créer home** | `useradd -m alice` |
| `-d` | Définir home | `useradd -m -d /home/custom alice` |
| `-s` | Définir shell | `useradd -m -s /bin/zsh alice` |
| `-u` | Définir UID | `useradd -m -u 1500 alice` |
| `-g` | Groupe principal (GID) | `useradd -m -g users alice` |
| `-G` | Groupes supplémentaires | `useradd -m -G sudo,docker alice` |
| `-c` | Commentaire (GECOS) | `useradd -m -c "Alice Dubois" alice` |
| `-e` | Date expiration | `useradd -m -e 2025-12-31 alice` |
| `-k` | Répertoire skel | `useradd -m -k /etc/myskel alice` |

**Exemples complets** :
```bash
# Basique : utilisateur avec home
sudo useradd -m alice

# Complet : home + shell + groupes + commentaire
sudo useradd -m -s /bin/bash -G sudo,docker -c "Alice Dubois" alice

# Avec UID spécifique
sudo useradd -m -u 1050 -s /bin/bash alice

# Home personnalisé
sudo useradd -m -d /data/users/alice -s /bin/bash alice

# Utilisateur système (no login, no home)
sudo useradd -r -s /usr/sbin/nologin serviceapp

# Avec date expiration
sudo useradd -m -e 2025-12-31 -c "Stagiaire" bob
```

**Différence `-m` vs pas `-m`** :
```bash
# SANS -m : home non créé
sudo useradd alice
ls -ld /home/alice
# ls: cannot access '/home/alice': No such file or directory

# AVEC -m : home créé automatiquement
sudo useradd -m alice
ls -ld /home/alice
# drwxr-xr-x 2 alice alice 4096 Dec 19 10:30 /home/alice
```

**Fichiers template (skeleton)** :
```bash
# Contenu copié depuis /etc/skel lors création home
ls -la /etc/skel/
# .bashrc
# .bash_logout
# .profile
# .bash_aliases
# Documents/
# Downloads/

# Personnaliser template
sudo cp /etc/skel/.bashrc /etc/skel/.bashrc.backup
sudo nano /etc/skel/.bashrc
# Ajouter : alias ll='ls -lah'

# Créer utilisateur (copie skel)
sudo useradd -m newuser
ls -la /home/newuser/
# Fichiers de /etc/skel sont copiés
```

**Valeurs par défaut** :
```bash
# Voir paramètres par défaut
useradd -D

GROUP=100
HOME=/home
INACTIVE=-1
EXPIRE=
SHELL=/bin/sh
SKEL=/etc/skel
CREATE_MAIL_SPOOL=no

# Modifier défauts
sudo useradd -D -s /bin/bash        # Shell par défaut
sudo useradd -D -b /home/users      # Base home

# Ou éditer directement
sudo nano /etc/default/useradd
```

**Configuration dans `/etc/login.defs`** :
```bash
sudo nano /etc/login.defs

# UID range
UID_MIN         1000
UID_MAX         60000
SYS_UID_MIN     100
SYS_UID_MAX     999

# GID range
GID_MIN         1000
GID_MAX         60000

# Home permissions
UMASK           022

# Password aging
PASS_MAX_DAYS   99999
PASS_MIN_DAYS   0
PASS_WARN_AGE   7

# Create home automatically
CREATE_HOME     yes
```

**Workflow complet création utilisateur** :
```bash
# 1. Créer utilisateur
sudo useradd -m -s /bin/bash -c "Alice Dubois" alice

# 2. Définir mot de passe
sudo passwd alice

# 3. Ajouter aux groupes
sudo usermod -aG sudo,docker alice

# 4. Vérifier
id alice
getent passwd alice
ls -ld /home/alice

# 5. Tester connexion
sudo -i -u alice
whoami
pwd
exit
```

**Alternative : `adduser` (interactif)** :
```bash
# adduser = script wrapper de useradd (Debian/Ubuntu)
sudo adduser alice

# Prompt interactif :
# Enter new UNIX password:
# Full Name []: Alice Dubois
# Room Number []:
# Work Phone []:
# Home Phone []:
# Other []:

# Automatique :
# - Crée home (-m)
# - Copie /etc/skel
# - Crée groupe alice
# - Demande mot de passe
```

**Différence useradd vs adduser** :

| Commande | Type | Interactif | Détails |
|----------|------|------------|--------|
| `useradd` | Commande bas niveau | ❌ Non | Syntaxe stricte, options requises |
| `adduser` | Script wrapper | ✅ Oui | User-friendly, crée home auto |

💡 **Bonnes pratiques** :
```bash
# Toujours utiliser -m (ou adduser)
# Toujours définir shell (-s /bin/bash)
# Ajouter commentaire (-c "Nom Prénom")
# Groupes selon besoin (-G sudo,docker)
# Vérifier après création

# Utilisateur développeur type
sudo useradd -m -s /bin/bash -G sudo,docker,www-data -c "Dev Team" devuser
sudo passwd devuser
```

⚠️ **Erreurs courantes** :
```bash
# Oublier -m : home non créé !
sudo useradd alice  # ❌
sudo useradd -m alice  # ✅

# Home existe déjà
# useradd: warning: the home directory already exists.
# Solution : supprimer ou utiliser -d autre_chemin

# UID déjà utilisé
# useradd: UID 1001 is not unique
# Solution : laisser auto ou choisir autre UID
```

**Question 12 : B) `usermod -aG groupe user` - Ajouter utilisateur à un groupe**

La commande `usermod` modifie les comptes utilisateurs existants :

**Options principales** :

| Option | Description | Exemple |
|--------|-------------|---------|
| `-aG` | **Ajouter à groupe(s)** (append) | `usermod -aG sudo alice` |
| `-G` | Définir groupes (remplace tous) | `usermod -G sudo,docker alice` |
| `-g` | Changer groupe principal | `usermod -g developers alice` |
| `-s` | Changer shell | `usermod -s /bin/zsh alice` |
| `-d` | Changer home | `usermod -d /home/newhome alice` |
| `-m` | Déplacer home (avec -d) | `usermod -d /new -m alice` |
| `-l` | Renommer login | `usermod -l newname alice` |
| `-u` | Changer UID | `usermod -u 1500 alice` |
| `-L` | Verrouiller compte | `usermod -L alice` |
| `-U` | Déverrouiller compte | `usermod -U alice` |
| `-e` | Date expiration | `usermod -e 2025-12-31 alice` |
| `-c` | Modifier commentaire | `usermod -c "Alice Martin" alice` |

**Différence CRITIQUE : `-G` vs `-aG`** :

```bash
# Situation initiale
id alice
# uid=1001(alice) gid=1001(alice) groups=1001(alice),27(sudo),999(docker)

# MAUVAIS : -G seul REMPLACE tous les groupes secondaires
sudo usermod -G adm alice
id alice
# uid=1001(alice) gid=1001(alice) groups=1001(alice),4(adm)
# ❌ PERDU : sudo, docker

# BON : -aG AJOUTE sans effacer
sudo usermod -aG adm alice
id alice
# uid=1001(alice) gid=1001(alice) groups=1001(alice),27(sudo),999(docker),4(adm)
# ✅ CONSERVÉ : sudo, docker + ajouté adm
```

**Gestion groupes** :

```bash
# Ajouter à UN groupe
sudo usermod -aG sudo alice

# Ajouter à PLUSIEURS groupes
sudo usermod -aG sudo,docker,www-data alice

# Définir TOUS groupes (attention, remplace !)
sudo usermod -G sudo,docker,www-data alice

# Changer groupe PRINCIPAL
sudo usermod -g developers alice

# Retirer d'un groupe (via gpasswd)
sudo gpasswd -d alice docker

# Voir groupes actuels
groups alice
id alice
getent group sudo | grep alice
```

**Modification shell** :

```bash
# Shells disponibles
cat /etc/shells
# /bin/sh
# /bin/bash
# /bin/zsh
# /bin/fish
# /usr/bin/tmux

# Changer shell
sudo usermod -s /bin/zsh alice

# Vérifier
getent passwd alice
# alice:x:1001:1001:Alice:/home/alice:/bin/zsh

# Utilisateur peut changer son propre shell
chsh -s /bin/zsh
```

**Modification home** :

```bash
# Changer home SANS déplacer fichiers
sudo usermod -d /data/alice alice
# → /etc/passwd mis à jour, fichiers restent dans ancien home

# Changer home AVEC déplacement
sudo usermod -d /data/alice -m alice
# → Fichiers déplacés vers nouveau home

# Vérifier
getent passwd alice
ls -ld /data/alice
```

**Renommer utilisateur** :

```bash
# Renommer login (utilisateur doit être déconnecté)
sudo usermod -l alice_dubois alice

# Renommer home aussi
sudo usermod -d /home/alice_dubois -m alice_dubois

# Renommer groupe
sudo groupmod -n alice_dubois alice

# Vérifier
id alice_dubois
ls -ld /home/alice_dubois
```

**Verrouillage compte** :

```bash
# Verrouiller (ajoute ! devant hash dans /etc/shadow)
sudo usermod -L alice

# Vérifier statut
sudo passwd -S alice
# alice L 12/19/2024 0 99999 7 -1
# ↑ L = Locked

# Tenter connexion
ssu alice
# su: Authentication failure

# Déverrouiller
sudo usermod -U alice
sudo passwd -S alice
# alice P 12/19/2024 0 99999 7 -1
# ↑ P = Password set
```

**Expiration compte** :

```bash
# Définir date expiration
sudo usermod -e 2025-12-31 alice

# Vérifier
sudo chage -l alice | grep "Account expires"
# Account expires                                 : Dec 31, 2025

# Compte sans expiration
sudo usermod -e "" alice

# Expirer immédiatement (forcer changement MDP)
sudo usermod -e 1 alice
```

**Changer UID/GID** :

```bash
# Changer UID
sudo usermod -u 2000 alice

# ATTENTION : fichiers gardent ancien UID !
find / -user 1001 2>/dev/null
# /home/alice/fichiers conservent UID 1001

# Changer propriétaire fichiers
sudo find /home/alice -user 1001 -exec chown alice {} \;
# Ou
sudo chown -R alice:alice /home/alice
```

**Modification commentaire (GECOS)** :

```bash
# Ajouter/modifier infos
sudo usermod -c "Alice Dubois, Dév, Ext 1234" alice

# Voir
getent passwd alice
# alice:x:1001:1001:Alice Dubois, Dév, Ext 1234:/home/alice:/bin/bash

# Format GECOS : Nom,Bureau,Tel_Travail,Tel_Maison,Autre
```

**Exemples réels** :

```bash
# Donner droits sudo
sudo usermod -aG sudo alice

# Accès Docker
sudo usermod -aG docker alice
# Utilisateur doit se déconnecter/reconnecter

# Accès fichiers web
sudo usermod -aG www-data alice

# Utilisateur développeur complet
sudo usermod -aG sudo,docker,www-data,developers alice -s /bin/zsh

# Compte temporaire stagiaire
sudo usermod -e 2025-06-30 -c "Stagiaire Été" stagiaire

# Désactiver compte sans supprimer
sudo usermod -L -e 1 ancien_employe
```

**Vérifications après modification** :

```bash
# Infos complètes utilisateur
id alice
getent passwd alice
groups alice

# Politique mot de passe
sudo chage -l alice

# Derniers logins
last alice | head -5

# Processus actifs
ps -u alice

# Fichiers appartenant
find /home -user alice 2>/dev/null | head -10
```

⚠️ **Pièges à éviter** :

```bash
# ❌ ERREUR : Oublier -a avec -G
sudo usermod -G docker alice  # ÉCRASE tous groupes !

# ✅ CORRECT
sudo usermod -aG docker alice # AJOUTE au groupe

# ❌ Modifier utilisateur connecté
# usermod: user alice is currently used by process 1234
# Solution : déconnecter user ou reboot

# ❌ Changer UID sans mettre à jour fichiers
# Fichiers deviennent orphelins (affichent UID numérique)
ls -l /home/alice
# -rw-r--r-- 1 1001 1001 ...
# Solution : chown -R après usermod -u
```

💡 **Mémo rapide** :
- `-aG` = **A**jouter **G**roupe (APPEND)
- `-G` seul = Remplacer TOUS groupes
- `-g` = Groupe **principal**
- `-L` = **L**ock (verrouiller)
- `-U` = **U**nlock (déverrouiller)
- Utilisateur doit se reconnecter pour nouveaux groupes

**Question 13 : B) `passwd username` - Changer le mot de passe d'un utilisateur**

La commande `passwd` gère les mots de passe utilisateurs :

**Syntaxe de base** :

```bash
# Changer SON propre mot de passe
passwd
# Prompt : Current password:
# Prompt : New password:
# Prompt : Retype new password:

# Changer mot de passe d'un AUTRE utilisateur (root requis)
sudo passwd alice
# Prompt : New password:
# Prompt : Retype new password:

# Root peut changer MDP sans connaître ancien
```

**Options principales** :

| Option | Description | Exemple |
|--------|-------------|---------|
| `-l` | Verrouiller compte (Lock) | `passwd -l alice` |
| `-u` | Déverrouiller (Unlock) | `passwd -u alice` |
| `-d` | Supprimer mot de passe (Delete) | `passwd -d alice` |
| `-e` | Expirer MDP (force changement) | `passwd -e alice` |
| `-S` | Voir statut | `passwd -S alice` |
| `-n` | Jours minimum avant changement | `passwd -n 7 alice` |
| `-x` | Jours maximum validité | `passwd -x 90 alice` |
| `-w` | Jours avertissement | `passwd -w 7 alice` |
| `-i` | Jours inactivité après exp | `passwd -i 14 alice` |

**Verrouillage/déverrouillage** :

```bash
# Verrouiller compte (ajoute ! devant hash)
sudo passwd -l alice

# Vérifier
sudo passwd -S alice
# alice L 12/19/2024 0 99999 7 -1
#       ↑ L = Locked

sudo grep alice /etc/shadow
# alice:!$6$hash...:19000:0:99999:7:::
#       ↑ ! = verrouillé

# Déverrouiller
sudo passwd -u alice
sudo passwd -S alice
# alice P 12/19/2024 0 99999 7 -1
#       ↑ P = Password set
```

**Expiration et renouvellement** :

```bash
# Forcer changement au prochain login
sudo passwd -e alice

# Résultat : alice doit changer MDP à connexion
# You are required to change your password immediately

# Définir durée validité MDP (90 jours)
sudo passwd -x 90 alice

# Minimum 7 jours avant pouvoir changer
sudo passwd -n 7 alice

# Avertir 7 jours avant expiration
sudo passwd -w 7 alice

# Compte inactif 14 jours après expiration MDP
sudo passwd -i 14 alice
```

**Statut détaillé** :

```bash
# Voir statut mot de passe
sudo passwd -S alice
# alice P 12/19/2024 0 99999 7 -1
# │     │ │          │ │     │ │
# │     │ │          │ │     │ Inactivité après exp (-1=jamais)
# │     │ │          │ │     Avertissement (jours)
# │     │ │          │ Max days
# │     │ │          Min days
# │     │ Dernier changement
# │     Statut (P/L/NP)
# Username

# Statut possibles :
# P = Password (MDP défini)
# L = Locked (verrouillé)
# NP = No Password (pas de MDP)
```

**Politique de mots de passe** :

**Via `/etc/login.defs`** :
```bash
sudo nano /etc/login.defs

# Durée validité MDP
PASS_MAX_DAYS   90      # Expire après 90 jours
PASS_MIN_DAYS   1       # Attendre 1 jour avant changer
PASS_WARN_AGE   7       # Avertir 7 jours avant
PASS_MIN_LEN    8       # Longueur minimum (obsolète, voir PAM)

# S'applique aux NOUVEAUX utilisateurs uniquement
# Utilisateurs existants : utiliser passwd ou chage
```

**Via `chage` (change age)** :
```bash
# Voir politique actuelle
sudo chage -l alice

Last password change                    : Dec 19, 2024
Password expires                        : Mar 19, 2025
Password inactive                       : never
Account expires                         : never
Minimum number of days between changes  : 0
Maximum number of days between changes  : 90
Number of days of warning before exp    : 7

# Modifier politique complète
sudo chage alice
# Prompt interactif pour tous paramètres

# Ou options directes
sudo chage -M 90 alice   # Max 90 jours
sudo chage -m 1 alice    # Min 1 jour
sudo chage -W 7 alice    # Warn 7 jours
sudo chage -I 14 alice   # Inactive 14 jours après exp
sudo chage -E 2025-12-31 alice  # Expiration compte

# Forcer changement au prochain login
sudo chage -d 0 alice
```

**Complexité mots de passe (PAM)** :

```bash
# Installer module PAM
sudo apt install libpam-pwquality

# Configurer
sudo nano /etc/security/pwquality.conf

# Longueur minimum
minlen = 12

# Au moins 1 majuscule
ucredit = -1

# Au moins 1 minuscule
lcredit = -1

# Au moins 1 chiffre
dcredit = -1

# Au moins 1 caractère spécial
ocredit = -1

# Nombre caractères différents
difok = 3

# Vérifier dans dictionnaire
dictcheck = 1

# Rejeter si contient username
usercheck = 1

# Nombre tentatives
retry = 3
```

**Génération mot de passe sécurisé** :

```bash
# Générer MDP aléatoire
# Méthode 1 : pwgen
sudo apt install pwgen
pwgen -s 16 1    # 16 caractères sécurisés
# Sortie : 9mK#vP2$xQ7wL@Nt

# Méthode 2 : openssl
openssl rand -base64 16
# Sortie : 8xN2pQ9mL4vK3wR1

# Méthode 3 : /dev/urandom
tr -dc 'A-Za-z0-9!@#$%^&*' < /dev/urandom | head -c 16
# Sortie : T4$mX9@nP2#qK5&w

# Définir programmatically
echo "alice:NouveauP@ss123" | sudo chpasswd

# Ou avec hash pré-généré
HASH=$(openssl passwd -6 "MonP@ssw0rd")
sudo usermod -p "$HASH" alice
```

**Politique entreprise typique** :

```bash
# Appliquer politique stricte à tous users
for user in alice bob charlie; do
    sudo chage -M 90 -m 1 -W 7 -I 14 $user
    sudo passwd -e $user  # Forcer changement
done

# Vérifier application
for user in alice bob charlie; do
    echo "=== $user ==="
    sudo chage -l $user | grep -E "(expires|between|warning)"
done
```

**Historique mots de passe** :

```bash
# Empêcher réutilisation 5 derniers MDP
sudo nano /etc/pam.d/common-password

# Ajouter
password required pam_pwhistory.so remember=5

# Fichier historique
ls -l /etc/security/opasswd
```

**Auditer mots de passe faibles** :

```bash
# Installer john the ripper
sudo apt install john

# Extraire hashes
sudo cat /etc/shadow > /tmp/shadow.txt

# Tester force brute (simulation)
john --wordlist=/usr/share/wordlists/rockyou.txt /tmp/shadow.txt

# Résultats montrent MDP faibles
john --show /tmp/shadow.txt

# Nettoyer
rm /tmp/shadow.txt
```

⚠️ **Sécurité** :

```bash
# DANGEREUX : Compte sans mot de passe
sudo passwd -d alice  # ❌ NE JAMAIS FAIRE
# Alice peut se connecter sans MDP !

# Vérifier comptes sans MDP
sudo awk -F: '$2 == "" {print $1}' /etc/shadow

# BON : Verrouiller plutôt que supprimer
sudo passwd -l alice  # ✅
```

💡 **Bonnes pratiques** :
- **Complexité** : Minimum 12 caractères, mix majuscules/minuscules/chiffres/spéciaux
- **Expiration** : 90 jours maximum (compliance)
- **Historique** : Mémoriser 5-10 derniers (anti-réutilisation)
- **Verrouillage** : Après 3-5 tentatives échouées (fail2ban)
- **Audit** : Tester régulièrement avec john/hashcat
- **2FA** : Implémenter Google Authenticator (PAM)

**Commandes associées** :
```bash
passwd      # Changer MDP
chage       # Politique vieillissement
chpasswd    # Changer MDP batch
pwck        # Vérifier intégrité /etc/passwd et /etc/shadow
lastlog     # Derniers logins
```

**Question 14 : B) `userdel -r username` - Supprimer un utilisateur et son répertoire**

La commande `userdel` supprime un compte utilisateur du système :

**Syntaxe et options** :
```bash
# Supprimer seulement le compte (garde le home)
userdel username

# Supprimer compte + home + mail
userdel -r username

# Forcer la suppression (même si connecté)
userdel -f username

# Combinaison force + remove
userdel -rf username
```

**Différences entre les options** :

| Commande | Compte | Home | Mail | Fichiers utilisateur |
|----------|--------|------|------|---------------------|
| `userdel user` | ✅ Supprimé | ❌ Conservé | ❌ Conservé | ❌ Conservés |
| `userdel -r user` | ✅ Supprimé | ✅ Supprimé | ✅ Supprimé | ✅ Supprimés |
| `userdel -f user` | ✅ Force | Selon -r | Selon -r | Force même si connecté |
| `userdel -rf user` | ✅ Force | ✅ Supprimé | ✅ Supprimé | ✅ Force tout |

**Impact système** :
- `/etc/passwd` : Ligne de l'utilisateur supprimée
- `/etc/shadow` : Hash du mot de passe supprimé  
- `/etc/group` : Références au user supprimées
- `/home/username` : Supprimé seulement avec `-r`
- `/var/mail/username` : Supprimé seulement avec `-r`
- `/var/spool/cron/crontabs/username` : Supprimé avec `-r`

**Cas d'usage professionnel** :
```bash
# Employé qui quitte l'entreprise (garder les fichiers pour audit)
sudo userdel jdupont
# Home reste dans /home/jdupont pour archivage/transfert

# Compte temporaire/test (tout supprimer)
sudo userdel -r testuser

# Utilisateur toujours connecté (forcer)
sudo userdel -f olduser
# ⚠️ Peut corrompre données si processus actifs

# Suppression avec backup préalable
sudo tar -czf /backup/jdupont-$(date +%Y%m%d).tar.gz /home/jdupont
sudo userdel -r jdupont
```

**⚠️ Attention - Vérifications préalables** :
```bash
# Toujours vérifier les processus actifs
ps -u username
# Si processus actifs → les tuer d'abord
sudo pkill -u username
# Ou forcer avec -9
sudo pkill -9 -u username

# Sauvegarder le home avant -r si besoin
sudo cp -a /home/username /backup/

# Vérifier fichiers appartenant à user (hors home)
sudo find / -user username 2>/dev/null

# Vérifier crontabs
sudo crontab -u username -l
```

**Fichiers orphelins** :
```bash
# Après userdel, fichiers hors /home deviennent orphelins
# Affichent UID numérique au lieu du nom

# Avant suppression :
ls -l /srv/data/
# -rw-r--r-- 1 alice users 1024 Dec 19 file.txt

# Après userdel alice (sans -r sur /srv) :
ls -l /srv/data/
# -rw-r--r-- 1 1050 users 1024 Dec 19 file.txt
#                ↑ UID orphelin

# Rechercher fichiers orphelins
sudo find / -nouser -o -nogroup 2>/dev/null

# Réattribuer à nouvel utilisateur
sudo chown -R newuser:newgroup /srv/data/
```

**Alternative GUI** :
- Ubuntu : Paramètres → Utilisateurs → Supprimer
- Red Hat : `system-config-users`
- Webmin : Users and Groups

**Vérification post-suppression** :
```bash
# Confirmer suppression
id username  
# id: 'username': no such user

# Home absent si -r utilisé
ls /home
# jdupont/ n'apparaît pas

# Vérifier /etc/passwd
grep username /etc/passwd
# Pas de résultat

# Vérifier /etc/shadow
sudo grep username /etc/shadow
# Pas de résultat

# Vérifier mail
ls /var/mail/username
# ls: cannot access: No such file or directory
```

**Différence avec `deluser` (Debian)** :
```bash
# userdel = commande standard Linux
sudo userdel -r username

# deluser = script Debian/Ubuntu (plus convivial)
sudo deluser --remove-home username
sudo deluser --remove-all-files username  # Plus agressif

# deluser supprime aussi :
# - Fichiers de config dans /etc
# - Références dans d'autres fichiers
```

**Cas particuliers** :
```bash
# Ne peut pas supprimer utilisateur avec UID 0
sudo userdel root
# userdel: user root is a protected account

# Groupe privé vide est supprimé automatiquement
# Si alice a groupe privé 'alice', groupe supprimé avec user

# Groupes partagés conservés
sudo userdel alice  # Groupe 'developers' conservé si d'autres membres
```

💡 **Bonnes pratiques** :
1. **Toujours backup** avant suppression définitive
2. **Sans `-r`** pour archivage (départ employé)
3. **Avec `-r`** pour comptes temporaires/tests
4. **Vérifier processus** avant suppression
5. **Auditer fichiers** orphelins après suppression
6. **Documenter** : qui, quand, pourquoi (conformité)

**Script de suppression sécurisée** :
```bash
#!/bin/bash
# safe-userdel.sh

USER=$1

if [ -z "$USER" ]; then
    echo "Usage: $0 username"
    exit 1
fi

# Vérifier existence
if ! id "$USER" &>/dev/null; then
    echo "Erreur : Utilisateur $USER n'existe pas"
    exit 1
fi

echo "=== Suppression utilisateur $USER ==="

# Backup home
echo "1. Backup du home..."
sudo tar -czf "/backup/${USER}-$(date +%Y%m%d-%H%M%S).tar.gz" "/home/$USER"

# Lister processus
echo "2. Processus actifs :"
ps -u "$USER" | grep -v PID

# Demander confirmation
read -p "Continuer la suppression ? (y/N) " -r
if [[ ! $REPLY =~ ^[Yy]$ ]]; then
    echo "Annulé"
    exit 0
fi

# Tuer processus
echo "3. Arrêt des processus..."
sudo pkill -u "$USER"
sleep 2
sudo pkill -9 -u "$USER" 2>/dev/null

# Supprimer
echo "4. Suppression..."
sudo userdel -r "$USER"

echo "✅ Utilisateur $USER supprimé"
echo "📦 Backup : /backup/${USER}-$(date +%Y%m%d)*.tar.gz"
```

**Question 15 : B) `id username` - Afficher les identifiants utilisateur**

La commande `id` affiche les **UID, GID et tous les groupes** d'un utilisateur :

**Syntaxe et formats de sortie** :
```bash
# Afficher infos de l'utilisateur courant
id
# uid=1000(alice) gid=1000(alice) groups=1000(alice),27(sudo),999(docker)

# Afficher infos d'un utilisateur spécifique
id username
id alice
# uid=1050(alice) gid=1050(alice) groups=1050(alice),27(sudo),999(docker),33(www-data)

# Seulement UID
id -u alice
# 1050

# Seulement GID (groupe principal)
id -g alice
# 1050

# Seulement nom utilisateur
id -un alice
# alice

# Seulement nom groupe principal
id -gn alice
# alice

# Tous les groupes (GID)
id -G alice
# 1050 27 999 33

# Tous les groupes (noms)
id -Gn alice
# alice sudo docker www-data
```

**Options principales** :

| Option | Description | Sortie exemple |
|--------|-------------|----------------|
| `id` | Tout (UID+GID+groups) | `uid=1000(alice) gid=1000(alice) groups=...` |
| `id -u` | UID numérique | `1000` |
| `id -un` | UID nom | `alice` |
| `id -g` | GID principal numérique | `1000` |
| `id -gn` | GID principal nom | `alice` |
| `id -G` | Tous GID numériques | `1000 27 999` |
| `id -Gn` | Tous GID noms | `alice sudo docker` |
| `id -r` | Real ID (vs effective) | Utile avec sudo |

**Différence avec autres commandes** :

| Commande | Affiche | Cas d'usage |
|----------|---------|-------------|
| `id` | UID, GID, tous groupes | Complet, diagnostic |
| `whoami` | Nom utilisateur courant | Simple, scripts |
| `who` | Qui est connecté | Sessions actives |
| `w` | Qui + activité | Monitoring |
| `groups` | Seulement groupes | Focus groupes |
| `getent passwd` | Ligne complète /etc/passwd | Détails compte |

**Comparaison pratique** :
```bash
# id - Sortie complète
id alice
# uid=1050(alice) gid=1050(alice) groups=1050(alice),27(sudo),999(docker)

# whoami - Juste le nom
whoami
# alice

# groups - Juste les groupes
groups alice
# alice : alice sudo docker

# getent - Ligne passwd complète
getent passwd alice
# alice:x:1050:1050:Alice Dubois:/home/alice:/bin/bash
```

**Real vs Effective ID** :
```bash
# Utilisateur normal
id
# uid=1000(alice) gid=1000(alice)

# Avec sudo (EUID devient 0, mais RUID reste 1000)
sudo id
# uid=0(root) gid=0(root) groups=0(root)

# Voir Real UID (qui a lancé sudo)
id -ru
# 1000  (alice originale)

# Voir Effective UID (droits actuels)
id -u
# 0  (root via sudo)

# Dans un script, vérifier qui exécute réellement
REAL_USER=$(id -run)
EFFECTIVE_USER=$(id -un)
echo "Lancé par : $REAL_USER, Exécuté en tant que : $EFFECTIVE_USER"
```

**Cas d'usage professionnels** :

**1. Vérifier appartenance à un groupe** :
```bash
# Méthode 1 : avec id
if id -nG "$USER" | grep -qw "docker"; then
    echo "✅ $USER est dans docker"
else
    echo "❌ $USER n'est PAS dans docker"
fi

# Méthode 2 : avec groups
if groups "$USER" | grep -qw "docker"; then
    echo "✅ Dans docker"
fi

# Vérifier groupe sudo (admin)
id -Gn alice | grep -q sudo && echo "Admin" || echo "User normal"
```

**2. Scripts nécessitant root** :
```bash
#!/bin/bash
# check-root.sh

if [ "$(id -u)" -ne 0 ]; then
    echo "❌ Ce script doit être exécuté en root"
    echo "Utilisez : sudo $0"
    exit 1
fi

echo "✅ Exécution en root"
# Suite du script...
```

**3. Récupérer UID pour chown** :
```bash
# Créer fichiers avec propriétaire spécifique
USER_UID=$(id -u alice)
USER_GID=$(id -g alice)

sudo install -o $USER_UID -g $USER_GID -m 644 config.txt /etc/app/

# Vérifier
ls -l /etc/app/config.txt
# -rw-r--r-- 1 alice alice 1024 Dec 19 config.txt
```

**4. Audit sécurité** :
```bash
# Lister tous les UID dans le système
awk -F: '{print $1, $3}' /etc/passwd | sort -k2 -n

# Trouver utilisateurs avec UID 0 (danger !)
awk -F: '$3 == 0 {print $1}' /etc/passwd
# root  ← Normal
# hacker ← ⚠️ ALERTE !

# Vérifier UID cohérents
for user in $(awk -F: '$3 >= 1000 {print $1}' /etc/passwd); do
    id "$user" | grep -q "uid=$(id -u $user)" || echo "⚠️ Incohérence : $user"
done
```

**5. Changer identité dans scripts** :
```bash
# Exécuter commande en tant qu'autre user
sudo -u alice id
# uid=1050(alice) gid=1050(alice) groups=1050(alice),27(sudo)

# Avec su
su - alice -c "id"

# Comparaison
echo "Avant su:"
id
su - alice
echo "Après su:"
id
```

**Informations détaillées** :
```bash
# UID = User ID
# - 0 = root (superutilisateur)
# - 1-999 = Utilisateurs système/services
# - 1000+ = Utilisateurs normaux (standard Ubuntu/Debian)
# - 500+ = Utilisateurs normaux (RedHat/CentOS ancien)

# GID = Group ID
# - Même logique que UID
# - Groupe principal de l'utilisateur

# Groups = Groupes supplémentaires
# - Droits additionnels
# - Max 16 groupes supplémentaires (limite historique)
# - Linux moderne : 65536 groupes possibles
```

**Fichiers source des informations** :
```bash
# id lit ces fichiers :

# /etc/passwd - Infos utilisateurs
grep alice /etc/passwd
# alice:x:1050:1050:Alice Dubois:/home/alice:/bin/bash
#       │ │    │    │          │            └─ Shell
#       │ │    │    │          └─ Home
#       │ │    │    └─ GECOS (commentaire)
#       │ │    └─ GID principal
#       │ └─ UID
#       └─ Password (x = dans /etc/shadow)

# /etc/group - Infos groupes
grep alice /etc/group
# alice:x:1050:
# sudo:x:27:alice,bob
# docker:x:999:alice,charlie
```

**Debug et dépannage** :
```bash
# Problème : Utilisateur ajouté à groupe mais id ne le montre pas
sudo usermod -aG docker alice
id alice | grep docker
# docker n'apparaît pas !

# Solution : Utilisateur doit se reconnecter
# Ou forcer avec newgrp
newgrp docker
id
# docker apparaît maintenant

# Vérifier dans fichier directement
getent group docker
# docker:x:999:alice,bob

# Vérifier cache NSS (Name Service Switch)
getent passwd alice
```

**Syntaxe alternative avec getent** :
```bash
# getent = get entries from databases

# Équivalent id
getent passwd alice
# alice:x:1050:1050:Alice Dubois:/home/alice:/bin/bash

getent group docker
# docker:x:999:alice,bob,charlie

# Avantage : fonctionne aussi avec LDAP, NIS, etc.
```

💡 **Astuces** :
```bash
# UID de root
id -u root
# 0

# Mon UID dans variable
MY_UID=$(id -u)
echo $MY_UID

# Tester si root
[ $(id -u) -eq 0 ] && echo "Je suis root"

# Liste des admins (groupe sudo)
getent group sudo | cut -d: -f4
# alice,bob,admin

# Compter groupes d'un user
id -G alice | wc -w
# 4
```

⚠️ **Attention** :
- `id` sans argument = utilisateur courant
- `id username` = utilisateur spécifique
- Après `usermod -aG`, nécessite re-login pour voir nouveau groupe
- `id -u` utile dans scripts (comparaison numérique)
- `id -un` utile pour logs (nom lisible)

**Question 16 : B) `useradd -u 1020 username` - Créer utilisateur avec UID spécifique**

La commande `useradd -u` permet de **définir un UID (User ID) personnalisé** lors de la création d'un compte :

**Syntaxe et exemples** :
```bash
# Créer avec UID spécifique
useradd -u 1020 alice

# Complet : UID + home + shell
useradd -m -u 1020 -s /bin/bash alice

# Vérifier UID assigné
id -u alice
# 1020

# Voir dans /etc/passwd
grep alice /etc/passwd
# alice:x:1020:1020::/home/alice:/bin/bash
#         ↑    ↑
#        UID  GID
```

**Plages UID Linux standard** :

| Plage UID | Type | Description | Exemples |
|-----------|------|-------------|----------|
| **0** | root | Superutilisateur | root |
| **1-99** | Système statique | Assignés par distro | bin, daemon, sync |
| **100-999** | Système dynamique | Services/daemons | www-data, mysql, apache |
| **1000-59999** | Utilisateurs normaux | Comptes humains | alice, bob, charlie |
| **60000-64999** | Réservé | Usage interne système | - |
| **65534** | nobody/nogroup | Processus sans privilèges | nobody |
| **65535** | Invalide | Valeur interdite | - |

**Distribution-specific** :

| Distribution | UID users | UID system |
|--------------|-----------|------------|
| **Debian/Ubuntu** | ≥ 1000 | 100-999 |
| **RHEL/CentOS 6** | ≥ 500 | 1-499 |
| **RHEL/CentOS 7+** | ≥ 1000 | 100-999 |
| **Arch Linux** | ≥ 1000 | 100-999 |

**Configuration dans `/etc/login.defs`** :
```bash
cat /etc/login.defs | grep UID

UID_MIN                  1000
UID_MAX                 60000
SYS_UID_MIN               100
SYS_UID_MAX               999
```

**Pourquoi spécifier un UID ?**

**1. Synchronisation entre serveurs** :
```bash
# Serveur 1
useradd -m -u 2000 alice

# Serveur 2 (même UID pour partage NFS)
useradd -m -u 2000 alice

# NFS partagé : fichiers créés par alice sur srv1
# sont lisibles par alice sur srv2 (même UID)
```

**2. Migration de systèmes** :
```bash
# Ancien serveur
id alice
# uid=1050

# Nouveau serveur (garder même UID)
useradd -m -u 1050 alice
# Fichiers restaurés gardent permissions correctes
```

**3. Plage réservée entreprise** :
```bash
# Convention entreprise :
# 2000-2999 : Admins
# 3000-3999 : Développeurs  
# 4000-4999 : Support

useradd -m -u 2050 -c "Admin Système" admin1
useradd -m -u 3050 -c "Développeur" dev1
useradd -m -u 4050 -c "Support N1" support1
```

**Vérifier UID disponibles** :
```bash
# Lister tous les UID utilisés
awk -F: '{print $3}' /etc/passwd | sort -n

# Trouver prochain UID libre
awk -F: '$3 >= 1000 && $3 < 60000 {print $3}' /etc/passwd | sort -n | tail -1
# 1005

# Prochain = 1006

# Rechercher UID spécifique libre
UID_TEST=1020
if ! getent passwd $UID_TEST >/dev/null; then
    echo "UID $UID_TEST disponible"
else
    echo "UID $UID_TEST déjà utilisé par : $(getent passwd $UID_TEST | cut -d: -f1)"
fi
```

**⚠️ Conflits UID** :
```bash
# ERREUR : UID déjà utilisé
useradd -u 1000 bob
# useradd: UID 1000 is not unique

# Vérifier qui utilise cet UID
getent passwd 1000
# alice:x:1000:1000::/home/alice:/bin/bash

# Forcer (DANGEREUX - peut casser permissions !)
useradd -o -u 1000 bob
# -o = --non-unique (permet UID dupliqué)

# ⚠️ NE JAMAIS FAIRE sauf cas très spécifique
# Deux users avec même UID = MÊME DROITS FICHIERS !
```

**UID 0 - Privilèges root** :
```bash
# UID 0 = root (superutilisateur)
id -u root
# 0

# Créer autre compte avec UID 0 (TRÈS DANGEREUX !)
useradd -o -u 0 backdoor
# backdoor a maintenant droits root !

# Audit sécurité : détecter UID 0 non-root
awk -F: '$3 == 0 && $1 != "root" {print "⚠️ ALERTE : " $1}' /etc/passwd

# Corriger
sudo userdel backdoor
```

**Changer UID existant** :
```bash
# Méthode 1 : usermod
sudo usermod -u 2000 alice

# Problème : fichiers gardent ancien UID
find /home/alice -uid 1050
# Tous fichiers affichent 1050 au lieu de alice

# Solution : mettre à jour fichiers
sudo find / -user 1050 -exec chown -h alice {} \; 2>/dev/null
# ou
sudo find / -uid 1050 -exec chown -h alice {} \; 2>/dev/null

# Méthode complète
OLD_UID=$(id -u alice)
sudo usermod -u 2000 alice
sudo find / -uid $OLD_UID -exec chown -h alice {} \; 2>/dev/null
```

**UID et GID (relation)** :
```bash
# Par défaut : UID = GID (groupe privé utilisateur)
useradd -m alice
id alice
# uid=1000(alice) gid=1000(alice) groups=1000(alice)

# Spécifier UID sans GID : GID = UID
useradd -m -u 2000 bob
id bob
# uid=2000(bob) gid=2000(bob)

# Spécifier UID ET GID différents
useradd -m -u 2000 -g users charlie
id charlie
# uid=2000(charlie) gid=100(users) groups=100(users)
```

**Cas d'usage professionnels** :

**1. Script de création d'utilisateurs en masse** :
```bash
#!/bin/bash
# create-users.sh

START_UID=5000
CURRENT_UID=$START_UID

while IFS=, read -r username fullname department; do
    echo "Création : $username ($fullname - $department)"
    
    # Vérifier UID libre
    while getent passwd $CURRENT_UID >/dev/null; do
        ((CURRENT_UID++))
    done
    
    # Créer utilisateur
    sudo useradd -m -u $CURRENT_UID -c "$fullname - $department" -s /bin/bash "$username"
    
    echo "✅ $username créé avec UID $CURRENT_UID"
    ((CURRENT_UID++))
done < users.csv
```

**2. Synchronisation multi-serveurs** :
```bash
# Serveur maître
getent passwd alice > /tmp/alice.txt
# alice:x:2050:2050:Alice Dubois:/home/alice:/bin/bash

# Serveur esclave
source /tmp/alice.txt
useradd -m -u 2050 -c "Alice Dubois" alice
```

**3. Restauration après crash** :
```bash
# Backup avant crash
awk -F: '$3 >= 1000 {print $1, $3}' /etc/passwd > /backup/uids.txt

# Après réinstallation, recréer avec mêmes UID
while read username uid; do
    useradd -m -u $uid $username
done < /backup/uids.txt
```

**Limites et contraintes** :
```bash
# UID maximum (32 bits signé)
# 2^31 - 1 = 2147483647

# En pratique, login.defs limite à 60000

# UID réservés à éviter :
# 0      → root
# 1-99   → Système statique
# 65534  → nobody
# 65535  → Invalide (valeur erreur)
```

💡 **Bonnes pratiques** :
1. **Plages cohérentes** : Définir convention entreprise (ex: 2000-2999 = admins)
2. **UID identiques** : Entre serveurs partageant NFS/CIFS
3. **Documenter** : Tenir à jour mapping UID ↔ Rôle
4. **Éviter UID < 1000** : Réservés système
5. **Vérifier disponibilité** : Avant d'assigner
6. **Backup** : Sauvegarder `/etc/passwd` et `/etc/shadow`

**Outils complémentaires** :
```bash
# Voir UID suivant qui sera assigné
grep ^UID_MIN /etc/login.defs
# UID_MIN 1000

# Derniers UID assignés
tail /etc/passwd | awk -F: '{print $1, $3}'

# Statistiques UID
awk -F: '{print $3}' /etc/passwd | sort -n | uniq -c

# Range check
for uid in {1000..1020}; do
    getent passwd $uid >/dev/null && echo "$uid utilisé" || echo "$uid libre"
done
```

**Question 17 : D) A et C sont correctes - Verrouillage de compte**

Il existe **deux commandes principales** pour verrouiller un compte utilisateur : `usermod -L` et `passwd -l`.

**Syntaxe des deux méthodes** :
```bash
# Méthode 1 : usermod -L (Lock)
sudo usermod -L username
sudo usermod --lock username   # Forme longue

# Méthode 2 : passwd -l (lock)
sudo passwd -l username
sudo passwd --lock username    # Forme longue

# Les deux font LA MÊME CHOSE : ajoutent ! devant le hash
```

**Mécanisme technique** :
```bash
# Avant verrouillage
sudo grep alice /etc/shadow
# alice:$6$randomsalt$hash...:19000:0:99999:7:::
#       ↑ Hash valide du mot de passe

# Après verrouillage (usermod -L OU passwd -l)
sudo grep alice /etc/shadow
# alice:!$6$randomsalt$hash...:19000:0:99999:7:::
#       ↑ ! ajouté devant le hash

# Le ! invalide le hash → connexion par mot de passe impossible
```

**Comparaison des méthodes** :

| Aspect | `usermod -L` | `passwd -l` | Résultat |
|--------|--------------|-------------|----------|
| Syntaxe | `usermod -L user` | `passwd -l user` | Identique |
| Fichier modifié | `/etc/shadow` | `/etc/shadow` | Identique |
| Action | Ajoute `!` devant hash | Ajoute `!` devant hash | Identique |
| Déverrouillage | `usermod -U` | `passwd -u` | Identique |
| Standard POSIX | ✅ | ✅ | Les deux standards |
| Disponibilité | Toutes distros | Toutes distros | Universelles |

**États du compte dans /etc/shadow** :

| Format hash | État | Connexion MDP | Connexion SSH clé |
|-------------|------|---------------|-------------------|
| `$6$hash...` | Normal | ✅ Possible | ✅ Possible |
| `!$6$hash...` | Verrouillé | ❌ Impossible | ✅ Possible |
| `!!` | Jamais défini | ❌ Impossible | ✅ Possible |
| `*` | Désactivé | ❌ Impossible | ❌ Impossible |
| (vide) | Pas de MDP | ⚠️ Sans MDP ! | ✅ Possible |

**Différence CRITIQUE : SSH avec clés** :
```bash
# Verrouiller compte
sudo usermod -L alice

# Connexion par mot de passe
ssh alice@serveur
# Permission denied (mot de passe verrouillé)

# Connexion par clé SSH (FONCTIONNE TOUJOURS !)
ssh -i ~/.ssh/id_rsa alice@serveur
# ✅ Connexion réussie !

# ⚠️ Le verrouillage ne bloque QUE l'authentification par mot de passe
# Les clés SSH continuent de fonctionner
```

**Bloquer complètement l'accès** :
```bash
# Méthode 1 : Changer le shell
sudo usermod -s /usr/sbin/nologin alice
# ou
sudo usermod -s /bin/false alice

# Effet
ssh alice@serveur
# This account is currently not available

# Méthode 2 : Expirer le compte
sudo chage -E 0 alice
# Compte expiré immédiatement

# Méthode 3 : Combinaison (le plus sûr)
sudo usermod -L -s /usr/sbin/nologin -e 1 alice
#              ↑  ↑                    ↑
#           Lock  No shell          Expire
```

**Vérifier l'état du compte** :
```bash
# Méthode 1 : passwd -S (Status)
sudo passwd -S alice
# alice L 12/19/2024 0 99999 7 -1
#       ↑ L = Locked

# Statuts possibles :
# P = Password (actif avec mot de passe)
# L = Locked (verrouillé)
# NP = No Password (pas de mot de passe défini)

# Méthode 2 : Regarder /etc/shadow
sudo grep alice /etc/shadow | cut -d: -f2
# !$6$hash...  → Commence par ! = verrouillé

# Méthode 3 : Tenter connexion
su - alice
# su: Authentication failure  → Verrouillé
```

**Déverrouillage** :
```bash
# Méthode 1 : usermod -U (Unlock)
sudo usermod -U alice

# Méthode 2 : passwd -u (unlock)
sudo passwd -u alice

# Vérifier déverrouillage
sudo passwd -S alice
# alice P 12/19/2024 0 99999 7 -1
#       ↑ P = Password (déverrouillé)

sudo grep alice /etc/shadow | cut -d: -f2
# $6$hash...  → Plus de ! = déverrouillé
```

**Cas d'usage professionnels** :

**1. Employé en congés (temporaire)** :
```bash
# Avant départ en congés
sudo usermod -L alice
echo "Alice verrouillée le $(date)" >> /var/log/user_locks.log

# Au retour
sudo usermod -U alice
echo "Alice déverrouillée le $(date)" >> /var/log/user_locks.log
```

**2. Incident de sécurité (compromission suspectée)** :
```bash
# Verrouillage immédiat
sudo usermod -L suspicious_user

# Bloquer aussi SSH avec clés
sudo usermod -s /bin/false suspicious_user

# Tuer sessions actives
sudo pkill -u suspicious_user

# Audit
sudo last suspicious_user | head -20
sudo grep suspicious_user /var/log/auth.log
```

**3. Compte de service (pas d'accès interactif)** :
```bash
# Créer compte service
sudo useradd -r -s /usr/sbin/nologin -d /var/lib/appservice appservice

# Hash dans /etc/shadow
sudo grep appservice /etc/shadow
# appservice:!:...  → ! = pas de mot de passe défini

# Peut exécuter processus, mais pas se connecter
sudo -u appservice /usr/bin/app
# ✅ Fonctionne

su - appservice
# This account is currently not available ❌
```

**4. Script de verrouillage automatique (inactivité)** :
```bash
#!/bin/bash
# lock-inactive-users.sh

INACTIVE_DAYS=90

# Trouver users inactifs
lastlog -b $INACTIVE_DAYS | tail -n +2 | while read username _; do
    # Vérifier si compte existe et actif
    if id "$username" &>/dev/null && sudo passwd -S "$username" | grep -q " P "; then
        echo "Verrouillage : $username (inactif depuis $INACTIVE_DAYS jours)"
        sudo usermod -L "$username"
    fi
done
```

**Différence avec chage -E (expiration)** :
```bash
# Verrouillage (réversible facilement)
sudo usermod -L alice
sudo usermod -U alice  # Déverrouillage simple

# Expiration (date d'échéance)
sudo chage -E 2025-12-31 alice
# Compte expire le 31/12/2025

sudo chage -E -1 alice  # Annuler expiration

# Expirer immédiatement
sudo chage -E 0 alice
# Account expired; contact your administrator
```

**Fichier /etc/shadow - Détails** :
```bash
# Format d'une ligne
alice:$6$salt$hash:19000:0:99999:7:30:19200:
# │    │            │     │ │     │ │  │      
# │    │            │     │ │     │ │  │      Réservé
# │    │            │     │ │     │ │  Date expiration compte (jours depuis 1970)
# │    │            │     │ │     │ Jours inactivité après expiration
# │    │            │     │ │     Jours avertissement avant expiration
# │    │            │     │ Jours maximum avant changement obligatoire
# │    │            │     Jours minimum avant pouvoir changer
# │    │            Dernier changement MDP (jours depuis 01/01/1970)
# │    Hash du mot de passe (! = verrouillé)
# Username

# États du champ mot de passe :
# $6$hash...       → MDP actif (SHA-512)
# !$6$hash...      → Verrouillé (! ajouté)
# !!               → Jamais défini
# *                → Désactivé système
# (vide)           → Pas de MDP (DANGEREUX)
```

**Audit et sécurité** :
```bash
# Lister tous les comptes verrouillés
sudo awk -F: '$2 ~ /^!/ && $2 !~ /^!!$/ {print $1}' /etc/shadow
# alice
# bob
# testuser

# Lister comptes actifs
sudo passwd -Sa | grep " P "

# Lister comptes sans mot de passe (DANGER !)
sudo awk -F: '$2 == "" {print "⚠️ " $1 " - PAS DE MOT DE PASSE !"}' /etc/shadow

# Verrouiller TOUS les comptes sauf exceptions
for user in $(awk -F: '$3 >= 1000 {print $1}' /etc/passwd); do
    if [[ ! "$user" =~ ^(alice|admin|ops)$ ]]; then
        sudo usermod -L "$user"
    fi
done
```

**Logs et traçabilité** :
```bash
# Logs de verrouillage dans /var/log/auth.log
sudo grep "password changed for\|account locked" /var/log/auth.log

# Exemple
# Dec 19 10:30:15 server passwd[1234]: account alice locked by root
# Dec 19 14:20:45 server passwd[5678]: account alice unlocked by root
```

💡 **Bonnes pratiques** :
1. **Verrouiller** plutôt que supprimer (réversible)
2. **Documenter** : Qui, quand, pourquoi (audit)
3. **Bloquer SSH** : Changer shell si nécessaire
4. **Automatiser** : Scripts pour inactivité >90 jours
5. **Surveiller** : Alertes sur tentatives connexion comptes verrouillés
6. **Politique** : Verrouillage temporaire pour congés, définitif pour départs

**Erreurs courantes** :
```bash
# ❌ Oublier sudo
usermod -L alice
# usermod: Permission denied

# ❌ Penser que -L bloque SSH avec clés
# Non ! SSH clés fonctionne toujours

# ❌ Confondre avec chage
chage -L alice  # ❌ Option inexistante
usermod -L alice  # ✅ Correct

# ❌ Verrouiller root par erreur
sudo usermod -L root  # DANGEREUX !
# Risque de lockout total du système
```

**Question 18 : A) `groupadd -g 1050 nomgroupe` - Créer groupe avec GID spécifique**

La commande `groupadd -g` permet de **créer un groupe avec un GID (Group ID) personnalisé** :

**Syntaxe et options** :
```bash
# Créer groupe avec GID automatique
sudo groupadd developers

# Créer groupe avec GID spécifique
sudo groupadd -g 1050 developers

# Créer groupe système (GID < 1000)
sudo groupadd -r servicegroup
# ou
sudo groupadd --system servicegroup

# Forcer GID non unique (DANGEREUX)
sudo groupadd -o -g 1050 duplicate

# Vérifier création
getent group developers
# developers:x:1050:
```

**Options principales** :

| Option | Description | Exemple |
|--------|-------------|---------||
| `-g GID` | Définir GID spécifique | `groupadd -g 2000 admins` |
| `-r` | Groupe système (GID < 1000) | `groupadd -r appservice` |
| `-o` | Autoriser GID non unique | `groupadd -o -g 100 dup` |
| `-f` | Force (exit 0 si existe) | `groupadd -f developers` |
| `-K` | Override defaults | `groupadd -K GID_MIN=2000` |

**Plages GID standard** :

| Plage GID | Type | Usage | Exemples |
|-----------|------|-------|----------|
| **0** | root | Groupe root | root |
| **1-99** | Système statique | Assignés distro | bin, daemon |
| **100-999** | Système dynamique | Services | www-data, docker |
| **1000-59999** | Utilisateurs | Groupes normaux | developers, sales |
| **65534** | nogroup | Sans groupe | nogroup |

**Configuration `/etc/login.defs`** :
```bash
grep GID /etc/login.defs

GID_MIN                  1000
GID_MAX                 60000
SYS_GID_MIN               100
SYS_GID_MAX               999
```

**Fichier `/etc/group`** :
```bash
# Format
cat /etc/group | grep developers
# developers:x:1050:alice,bob,charlie
# │          │ │    │
# │          │ │    Liste membres (séparés par ,)
# │          │ GID
# │          Password (x = dans /etc/gshadow)
# Nom groupe

# Voir tous les groupes
cat /etc/group

# Groupes avec GID >= 1000
awk -F: '$3 >= 1000 {print $1, $3}' /etc/group
```

**Vérifier GID disponibles** :
```bash
# Lister GID utilisés
awk -F: '{print $3}' /etc/group | sort -n

# Trouver prochain GID libre
awk -F: '$3 >= 1000 && $3 < 60000 {print $3}' /etc/group | sort -n | tail -1
# 1025
# → Prochain = 1026

# Vérifier GID spécifique
GID_TEST=1050
if ! getent group $GID_TEST >/dev/null; then
    echo "GID $GID_TEST disponible"
else
    GROUP=$(getent group $GID_TEST | cut -d: -f1)
    echo "GID $GID_TEST utilisé par : $GROUP"
fi
```

**Pourquoi spécifier un GID ?**

**1. Synchronisation multi-serveurs** :
```bash
# Serveur 1
sudo groupadd -g 5000 developers

# Serveur 2 (même GID pour NFS)
sudo groupadd -g 5000 developers

# Fichiers partagés NFS conservent permissions
chgrp developers /srv/shared/project
# Fonctionne sur les deux serveurs
```

**2. Migration systèmes** :
```bash
# Ancien serveur
getent group developers
# developers:x:2050:alice,bob

# Nouveau serveur (garder même GID)
sudo groupadd -g 2050 developers
sudo usermod -aG developers alice
sudo usermod -aG developers bob

# Fichiers restaurés gardent bonnes permissions
```

**3. Convention entreprise** :
```bash
# Plages réservées :
# 5000-5099 : Groupes IT
# 5100-5199 : Groupes Finance
# 5200-5299 : Groupes RH

sudo groupadd -g 5010 it-admins
sudo groupadd -g 5020 it-developers
sudo groupadd -g 5110 finance-analysts
sudo groupadd -g 5210 hr-managers
```

**Groupes privés vs partagés** :
```bash
# Groupe privé (un user = un groupe)
# Créé automatiquement avec useradd -m
useradd -m alice
id alice
# uid=1000(alice) gid=1000(alice) groups=1000(alice)

getent group alice
# alice:x:1000:  ← Groupe privé vide (juste GID principal alice)

# Groupe partagé (plusieurs membres)
sudo groupadd -g 2000 developers
sudo usermod -aG developers alice
sudo usermod -aG developers bob

getent group developers
# developers:x:2000:alice,bob  ← Plusieurs membres
```

**Conflits GID** :
```bash
# ERREUR : GID déjà utilisé
sudo groupadd -g 1000 newgroup
# groupadd: GID '1000' already exists

# Voir qui utilise
getent group 1000
# alice:x:1000:

# Forcer (DANGEREUX !)
sudo groupadd -o -g 1000 duplicate
# Deux groupes avec même GID = permissions identiques !
# ⚠️ Ne JAMAIS faire en production
```

**Changer GID existant** :
```bash
# Modifier GID
sudo groupmod -g 3000 developers

# Problème : fichiers gardent ancien GID
find /srv -gid 2000
# Affichent 2000 au lieu de developers

# Solution : mettre à jour fichiers
sudo find / -gid 2000 -exec chgrp developers {} \; 2>/dev/null

# Méthode complète
OLD_GID=$(getent group developers | cut -d: -f3)
sudo groupmod -g 3000 developers
sudo find / -gid $OLD_GID -exec chgrp developers {} \; 2>/dev/null
```

**Cas d'usage professionnels** :

**1. Groupes par projet** :
```bash
# Créer groupes projets
sudo groupadd -g 6000 project-alpha
sudo groupadd -g 6001 project-beta

# Assigner équipes
sudo usermod -aG project-alpha alice,bob,charlie
sudo usermod -aG project-beta bob,david,eve

# Répertoires projets
sudo mkdir -p /srv/projects/{alpha,beta}
sudo chgrp project-alpha /srv/projects/alpha
sudo chgrp project-beta /srv/projects/beta
sudo chmod 2770 /srv/projects/*
# 2 = setgid (fichiers héritent groupe répertoire)
```

**2. Groupes fonctionnels** :
```bash
# Admins système
sudo groupadd -g 7000 sysadmins
sudo usermod -aG sysadmins,sudo alice

# Développeurs
sudo groupadd -g 7010 developers  
sudo usermod -aG developers,docker bob

# Support
sudo groupadd -g 7020 support
sudo usermod -aG support charlie
```

**3. Script création masse** :
```bash
#!/bin/bash
# create-groups.sh

START_GID=8000
CURRENT_GID=$START_GID

while IFS=, read -r groupname description; do
    # Vérifier GID libre
    while getent group $CURRENT_GID >/dev/null; do
        ((CURRENT_GID++))
    done
    
    # Créer groupe
    sudo groupadd -g $CURRENT_GID "$groupname"
    echo "✅ $groupname créé avec GID $CURRENT_GID"
    
    ((CURRENT_GID++))
done < groups.csv
```

**Commandes associées** :
```bash
# Créer groupe
groupadd -g 1050 mygroup

# Modifier groupe
groupmod -g 2000 mygroup
groupmod -n newname oldname

# Supprimer groupe
groupdel mygroup

# Ajouter user au groupe
usermod -aG mygroup alice
gpasswd -a alice mygroup  # Alternative

# Retirer user du groupe
gpasswd -d alice mygroup

# Définir administrateurs groupe
gpasswd -A alice,bob mygroup

# Lister membres
getent group mygroup
grep mygroup /etc/group
```

**Audit et vérification** :
```bash
# Lister tous les groupes
cat /etc/group

# Groupes utilisateur
groups alice
id -Gn alice

# Groupes vides
for group in $(cut -d: -f1 /etc/group); do
    members=$(getent group $group | cut -d: -f4)
    [ -z "$members" ] && echo "Groupe vide : $group"
done

# Duplications GID
awk -F: '{print $3}' /etc/group | sort -n | uniq -d

# GID système vs utilisateurs
awk -F: '$3 < 1000 {print "Système:", $1, $3}' /etc/group
awk -F: '$3 >= 1000 {print "User:", $1, $3}' /etc/group
```

💡 **Bonnes pratiques** :
1. **GID cohérents** : Entre serveurs partageant fichiers
2. **Documentation** : Maintenir mapping GID ↔ Fonction
3. **Plages réservées** : Par département/projet
4. **Backup** : Sauvegarder `/etc/group` et `/etc/gshadow`
5. **Audit** : Vérifier groupes vides/obsolètes
6. **Nommage** : Convention claire (ex: `proj-alpha`, `team-dev`)

⚠️ **Erreurs à éviter** :
- GID dupliqués (`-o`) sauf cas très spécifique
- Modifier GID sans mettre à jour fichiers
- Supprimer groupe encore utilisé
- Oublier synchronisation multi-serveurs (NFS)

**Question 19 : D) B et C sont correctes - Afficher utilisateurs connectés**

Les commandes `who` et `w` affichent les utilisateurs actuellement connectés au système :

**Commande `who`** :
```bash
# Afficher utilisateurs connectés
who
# alice    pts/0        2024-12-19 10:30 (192.168.1.100)
# bob      tty1         2024-12-19 09:15
# charlie  pts/1        2024-12-19 11:00 (192.168.1.200)
```

**Commande `w`** :
```bash
# Afficher utilisateurs + leur activité
w
#  11:30:25 up 5 days,  3:45,  3 users,  load average: 0.52, 0.48, 0.45
# USER     TTY      FROM             LOGIN@   IDLE   JCPU   PCPU WHAT
# alice    pts/0    192.168.1.100    10:30    0.00s  0.12s  0.03s vim file.txt
# bob      tty1     -                09:15   42:15   0.05s  0.05s -bash
# charlie  pts/1    192.168.1.200    11:00    5:00   0.01s  0.01s w
```

**Tableau comparatif détaillé** :

| Commande | Infos affichées | Format | Usage |
|----------|-----------------|--------|-------|
| **`who`** | User, TTY, Date connexion, IP | Simple, lignes | Voir qui est connecté |
| **`w`** | User, TTY, IP, Heure, IDLE, Commande active | Détaillé, statistiques | Monitoring activité |
| **`users`** | Juste noms (séparés espaces) | Minimal | Scripts, comptage rapide |
| **`whoami`** | Nom utilisateur courant | Minimal | Scripts, vérifications |
| **`who am i`** | Session actuelle | Détails session | Debug connexions |
| **`last`** | Historique connexions | Logs archives | Audit, forensics |
| **`lastlog`** | Dernière connexion par user | Tous users | Sécurité, inactivité |

**Options `who`** :
```bash
# who basique
who
# alice    pts/0        2024-12-19 10:30 (192.168.1.100)

# who avec en-têtes
who -H
# NAME     LINE         TIME             COMMENT
# alice    pts/0        2024-12-19 10:30 (192.168.1.100)

# who détaillé (-a = all)
who -a
# Inclut processus système, runlevel, boot time

# who comptage seulement
who -q
# alice bob charlie
# # users=3

# who avec IDLE time
who -u
# alice    pts/0        2024-12-19 10:30  old        1234 (192.168.1.100)
#                                          ↑ IDLE     ↑ PID

# Session courante
who am i
# alice    pts/0        2024-12-19 10:30 (192.168.1.100)
```

**Options `w`** :
```bash
# w complet (par défaut)
w

# w sans en-tête
w -h
# alice    pts/0    192.168.1.100    10:30    0.00s  0.12s  0.03s vim file.txt

# w format court
w -s
# USER     TTY      FROM              IDLE WHAT
# alice    pts/0    192.168.1.100     0.00s vim file.txt

# w pour utilisateur spécifique
w alice
#  11:30:25 up 5 days,  3:45,  1 user,  load average: 0.52, 0.48, 0.45
# USER     TTY      FROM             LOGIN@   IDLE   JCPU   PCPU WHAT
# alice    pts/0    192.168.1.100    10:30    0.00s  0.12s  0.03s vim file.txt

# w sans adresses IP (seulement noms)
w -f

# w avec JCPU et PCPU
w -u
```

**Signification des colonnes `w`** :

| Colonne | Signification | Exemple |
|---------|---------------|-------|
| **USER** | Nom utilisateur | alice |
| **TTY** | Terminal | pts/0 (SSH), tty1 (console) |
| **FROM** | IP/host origine | 192.168.1.100 |
| **LOGIN@** | Heure connexion | 10:30 |
| **IDLE** | Temps inactivité | 0.00s, 5:00, 1:30m |
| **JCPU** | Temps CPU de tous processus TTY | 0.12s |
| **PCPU** | Temps CPU processus actuel (WHAT) | 0.03s |
| **WHAT** | Commande en cours d'exécution | vim file.txt |

**Load average (en-tête `w`)** :
```bash
#  11:30:25 up 5 days,  3:45,  3 users,  load average: 0.52, 0.48, 0.45
#  │        │           │      │         │
#  │        │           │      │         Charge 1min, 5min, 15min
#  │        │           │      Nombre users connectés
#  │        │           Uptime
#  │        Uptime
#  Heure actuelle
```

**Autres commandes connexion** :

**`users` - Liste simple** :
```bash
users
# alice bob charlie

# Compter utilisateurs
users | wc -w
# 3
```

**`last` - Historique complet** :
```bash
# Dernières connexions
last | head -20
# alice    pts/0    192.168.1.100    Thu Dec 19 10:30   still logged in
# bob      tty1                      Thu Dec 19 09:15 - 10:00  (00:45)
# charlie  pts/1    192.168.1.200    Wed Dec 18 14:22 - 18:30  (04:08)

# Connexions utilisateur spécifique
last alice | head -10

# Derniers reboots
last reboot | head -5
# reboot   system boot  5.15.0-91    Thu Dec 19 09:00   still running

# Depuis date
last -s 2024-12-15

# Dernières 10 entrées
last -n 10
```

**`lastlog` - Dernière connexion par user** :
```bash
# Tous les utilisateurs
lastlog
# Username         Port     From             Latest
# root                                       **Never logged in**
# alice            pts/0    192.168.1.100    Thu Dec 19 10:30:45 +0100 2024
# bob              tty1                      Thu Dec 19 09:15:22 +0100 2024
# charlie          pts/1    192.168.1.200    Wed Dec 18 14:22:10 +0100 2024

# Utilisateur spécifique
lastlog -u alice

# Utilisateurs jamais connectés
lastlog | grep "Never logged in"

# Inactifs depuis > 90 jours
lastlog -b 90
```

**Types de terminaux (TTY)** :

| TTY | Type | Description | Exemple |
|-----|------|-------------|----------|
| **tty1-tty6** | Console physique | Ctrl+Alt+F1 à F6 | Login local |
| **tty7** | X11/Wayland | Interface graphique | Ubuntu Desktop |
| **pts/0, pts/1...** | Pseudo-terminal | SSH, terminal GUI | Connexion distante |
| **:0** | Display X11 | Session graphique | GNOME, KDE |

**Cas d'usage professionnels** :

**1. Monitoring temps réel** :
```bash
# Boucle watch (rafraîchit toutes les 2s)
watch -n 2 'w -h'

# Alerter si > 10 utilisateurs
USERS=$(who | wc -l)
if [ $USERS -gt 10 ]; then
    echo "⚠️ ALERTE : $USERS utilisateurs connectés" | mail -s "Alerte connexions" admin@example.com
fi
```

**2. Détecter utilisateurs suspects** :
```bash
# Connexions depuis IPs externes
w -h | grep -v "192.168." | grep -v "10." | grep -v "172."

# Utilisateurs inactifs > 1 heure
w -h | awk '$5 ~ /:[0-9]/ {split($5,a,":"); if(a[1] > 1) print $1, $5}'

# Sessions multiples même user
who | awk '{print $1}' | sort | uniq -d
```

**3. Kick utilisateur inactif** :
```bash
# Lister sessions
w -h

# Identifier PID
ps -u alice

# Tuer session
sudo pkill -KILL -t pts/3
# ou
sudo skill -KILL -u alice
```

**4. Audit sécurité** :
```bash
# Connexions root (devrait être vide)
who | grep root
last root | head -5

# Connexions hors heures ouvrables
last | grep -E "(2[0-3]|0[0-6]):"

# IPs connexions récentes
last -i | awk '{print $3}' | sort | uniq -c | sort -nr
```

**Scripts pratiques** :

**1. Rapport connexions** :
```bash
#!/bin/bash
# connection-report.sh

echo "=== RAPPORT CONNEXIONS $(date) ==="
echo
echo "Utilisateurs actuellement connectés :"
who | wc -l
echo
echo "Détails :"
w -h
echo
echo "Dernières connexions (24h) :"
last -s yesterday | head -20
```

**2. Alerter connexions inhabituelles** :
```bash
#!/bin/bash
# detect-unusual-logins.sh

# IP whitelist
ALLOWED_RANGE="192.168.1"

who -u | while read user tty date time idle pid ip; do
    # Enlever parenthèses de l'IP
    ip=${ip//[()]/}
    
    if [[ ! $ip =~ ^$ALLOWED_RANGE ]]; then
        echo "⚠️ Connexion suspecte : $user depuis $ip"
        logger -t security "Connexion suspecte : $user depuis $ip"
    fi
done
```

**Différences distribution** :
```bash
# Linux
who, w, users → Standard

# BSD/macOS
w → Format légèrement différent

# Solaris
who -a → Plus verbeux
```

💡 **Mémo rapide** :
- `who` → Qui est là ?
- `w` → Qui fait quoi ?
- `users` → Comptage rapide
- `last` → Historique
- `lastlog` → Dernière connexion

⚠️ **Sécurité** :
```bash
# Désactiver affichage pour non-root
sudo chmod 700 /usr/bin/w
# Ou via PAM

# Logs connexions
sudo tail -f /var/log/wtmp    # who/w lisent ce fichier
sudo tail -f /var/log/btmp    # Échecs connexion
sudo tail -f /var/log/lastlog # lastlog lit ce fichier
```

**Question 20 : D) A et C sont correctes - Changer le shell utilisateur**

Pour modifier le **shell par défaut** d'un utilisateur, on peut utiliser `usermod -s` ou `chsh` :

**Méthode 1 : `usermod -s` (admin)** :
```bash
# Syntaxe
sudo usermod -s /bin/bash username
sudo usermod --shell /bin/zsh username  # Forme longue

# Exemples
sudo usermod -s /bin/zsh alice
sudo usermod -s /bin/bash bob
sudo usermod -s /usr/bin/fish charlie
sudo usermod -s /usr/sbin/nologin serviceaccount
```

**Méthode 2 : `chsh` (utilisateur ou admin)** :
```bash
# Utilisateur change SON propre shell
chsh
# Prompt : Password for alice:
# Prompt : New shell [/bin/bash]: /bin/zsh

# Ou directement
chsh -s /bin/zsh

# Admin change shell d'un autre user
sudo chsh -s /bin/bash alice
sudo chsh --shell /bin/zsh bob  # Forme longue
```

**Comparaison des méthodes** :

| Aspect | `usermod -s` | `chsh` |
|--------|--------------|--------|
| **Permissions** | Root uniquement | User (soi) ou root (autres) |
| **Syntaxe** | `usermod -s shell user` | `chsh -s shell [user]` |
| **Validation** | Peu stricte | Vérifie `/etc/shells` |
| **Interactif** | Non | Oui (si sans -s) |
| **Usage** | Scripts admin | Utilisateurs finaux |

**Fichier `/etc/shells` - Shells autorisés** :
```bash
# Lister shells valides
cat /etc/shells
# /bin/sh
# /bin/bash
# /bin/rbash       # Bash restreint
# /bin/dash        # Debian Almquist Shell
# /usr/bin/zsh     # Z Shell
# /usr/bin/fish    # Friendly Interactive Shell
# /usr/bin/tcsh    # TENEX C Shell
# /bin/ksh         # Korn Shell
# /usr/sbin/nologin  # Pas de login
# /bin/false       # Bloque login
```

**Shells courants et usages** :

| Shell | Chemin | Usage | Caractéristiques |
|-------|--------|-------|------------------|
| **bash** | `/bin/bash` | Standard Linux | Scripting, interactif |
| **sh** | `/bin/sh` | POSIX minimal | Scripts portables |
| **zsh** | `/usr/bin/zsh` | Avancé, moderne | Auto-complétion++, plugins |
| **fish** | `/usr/bin/fish` | User-friendly | Suggestions, couleurs |
| **dash** | `/bin/dash` | Léger, rapide | Scripts système Ubuntu |
| **nologin** | `/usr/sbin/nologin` | Bloquer login | Comptes service |
| **false** | `/bin/false` | Bloquer login | Retourne toujours erreur |

**Vérifier shell actuel** :
```bash
# Variable environnement
echo $SHELL
# /bin/bash

# /etc/passwd
grep alice /etc/passwd | cut -d: -f7
# /bin/bash

# getent
getent passwd alice | cut -d: -f7

# Processus actuel
ps -p $$
```

**Cas d'usage professionnels** :

**1. Comptes de service (no login)** :
```bash
# Compte applicatif ne doit pas se connecter
sudo useradd -r -s /usr/sbin/nologin appservice
sudo useradd -r -s /bin/false backupuser

# Tentative connexion
su - appservice
# This account is currently not available.
```

**2. Migration bash → zsh (développeurs)** :
```bash
# Installer zsh
sudo apt install zsh

# Ajouter à shells autorisés
echo "/usr/bin/zsh" | sudo tee -a /etc/shells

# Changer pour devs
for dev in alice bob; do
    sudo usermod -s /usr/bin/zsh $dev
done
```

**Différence `/usr/sbin/nologin` vs `/bin/false`** :

| Shell | Comportement | Message | Usage |
|-------|--------------|---------|-------|
| **`/usr/sbin/nologin`** | Affiche message puis refuse | "This account is currently not available." | Comptes service, désactivés |
| **`/bin/false`** | Retourne code erreur 1 | Aucun | Scripts, tests, système |

💡 **Bonnes pratiques** :
- **Vérifier installation** : Avant de définir nouveau shell
- **Tester d'abord** : `exec /bin/zsh` puis si OK → `chsh`
- **nologin pour services** : Comptes applicatifs
- **Garder session root** : Avant modifier shell admin

---

### PARTIE 3 - PERMISSIONS

**Question 21 : B) 754 - Calcul des permissions octales**

Les permissions Linux utilisent un **système octal (base 8)** où chaque droit a une valeur :

**Valeurs de base** :
```
r (read)    = 4
w (write)   = 2
x (execute) = 1
- (aucun)   = 0
```

**Calcul pour rwxr-xr--** :
```
Proprietaire : rwx = 4+2+1 = 7
Groupe       : r-x = 4+0+1 = 5
Autres       : r-- = 4+0+0 = 4

Résultat : 754
```

**Tableau de conversion complet** :

| Symbolique | Binaire | Octal | Calcul | Description |
|------------|---------|-------|--------|-------------|
| `---` | 000 | **0** | 0+0+0 | Aucun droit |
| `--x` | 001 | **1** | 0+0+1 | Exécution seule |
| `-w-` | 010 | **2** | 0+2+0 | Écriture seule |
| `-wx` | 011 | **3** | 0+2+1 | Écriture + Exécution |
| `r--` | 100 | **4** | 4+0+0 | Lecture seule |
| `r-x` | 101 | **5** | 4+0+1 | Lecture + Exécution |
| `rw-` | 110 | **6** | 4+2+0 | Lecture + Écriture |
| `rwx` | 111 | **7** | 4+2+1 | Tous les droits |

**Exercices de conversion** :

```bash
# rwxr-xr-x → ?
rwx = 7, r-x = 5, r-x = 5 → 755

# rw-r--r-- → ?
rw- = 6, r-- = 4, r-- = 4 → 644

# rwx------ → ?
rwx = 7, --- = 0, --- = 0 → 700

# rw-rw-r-- → ?
rw- = 6, rw- = 6, r-- = 4 → 664

# r-xr-x--- → ?
r-x = 5, r-x = 5, --- = 0 → 550
```

**Permissions courantes et leur usage** :

| Octal | Symbolique | Usage typique |
|-------|------------|---------------|
| **644** | `rw-r--r--` | Fichiers normaux (config, documents) |
| **755** | `rwxr-xr-x` | Scripts, binaires, répertoires |
| **600** | `rw-------` | Fichiers privés (clés SSH, passwords) |
| **700** | `rwx------` | Répertoires privés, scripts perso |
| **664** | `rw-rw-r--` | Fichiers partagés groupe |
| **775** | `rwxrwxr-x` | Répertoires partagés groupe |
| **777** | `rwxrwxrwx` | ⚠️ Tous droits (DANGEREUX) |
| **000** | `----------` | Aucun droit (fichier bloqué) |

**Application avec chmod** :
```bash
# Appliquer 754
chmod 754 script.sh
ls -l script.sh
# -rwxr-xr-- 1 alice users 1024 Dec 19 script.sh

# Vérifier conversion
stat -c "%a %n" script.sh
# 754 script.sh
```

**Conversion inverse (octal → symbolique)** :
```bash
# 754 → rwxr-xr--
7 → rwx (propriétaire)
5 → r-x (groupe)
4 → r-- (autres)

# 640 → rw-r-----
6 → rw- (propriétaire)
4 → r-- (groupe)
0 → --- (autres)
```

**Méthode mnémotechnique** :
```
Pour calculer rapidement :
1. r présent ? +4
2. w présent ? +2
3. x présent ? +1
4. Additionner

Exemple rwx : 4+2+1 = 7
Exemple r-x : 4+0+1 = 5
Exemple r-- : 4+0+0 = 4
```

💡 **Astuce** : Les permissions 644, 755, 600, 700 couvrent 90% des cas d'usage

**Question 22 : A et D) chmod 700 - Accès exclusif propriétaire**

Pour donner **tous les droits au propriétaire et aucun aux autres**, deux syntaxes équivalentes :

**Méthode octale** : `chmod 700 fichier`
**Méthode symbolique** : `chmod u+rwx,go-rwx fichier`

**Décomposition 700** :
```
7 (propriétaire) : rwx (4+2+1) = tous droits
0 (groupe)       : --- (0+0+0) = aucun droit
0 (autres)       : --- (0+0+0) = aucun droit

Résultat : rwx------
```

**Décomposition symbolique** :
```bash
u+rwx    # User (propriétaire) : ajouter read, write, execute
go-rwx   # Group et Others : retirer read, write, execute
```

**Équivalences syntaxiques** :

| Octal | Symbolique variante 1 | Symbolique variante 2 | Symbolique variante 3 |
|-------|-----------------------|-----------------------|-----------------------|
| `700` | `u+rwx,go-rwx` | `u=rwx,go=` | `u=rwx,g=,o=` |
| `700` | `u+rwx,g-rwx,o-rwx` | `u=rwx,g-rwx,o-rwx` | - |

**Comparaison pratique** :
```bash
# Méthode 1 : Octal (concise)
chmod 700 secret.sh

# Méthode 2 : Symbolique
chmod u+rwx,go-rwx secret.sh

# Méthode 3 : Symbolique avec =
chmod u=rwx,go= secret.sh

# Vérifier résultat
ls -l secret.sh
# -rwx------ 1 alice users 1024 Dec 19 secret.sh
```

**Cas d'usage sécurité 700** :

**1. Clés SSH privées** :
```bash
# Clé privée DOIT être 600 ou 700 (répertoire)
chmod 700 ~/.ssh
chmod 600 ~/.ssh/id_rsa
chmod 644 ~/.ssh/id_rsa.pub
chmod 600 ~/.ssh/authorized_keys

# SSH refuse si permissions trop ouvertes
ssh -i ~/.ssh/id_rsa user@server
# @@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
# @         WARNING: UNPROTECTED PRIVATE KEY FILE!   @
# @@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
# Permissions 0644 for '/home/alice/.ssh/id_rsa' are too open.
```

**2. Scripts avec credentials** :
```bash
# Script contenant mots de passe
cat backup-db.sh
#!/bin/bash
DB_PASSWORD="SecretP@ss123"
mysqldump -u root -p$DB_PASSWORD ...

# OBLIGATOIRE : 700
chmod 700 backup-db.sh
ls -l backup-db.sh
# -rwx------ 1 alice users 512 Dec 19 backup-db.sh

# Autres users ne peuvent ni lire ni exécuter
```

**3. Répertoires personnels sensibles** :
```bash
# Documents confidentiels
mkdir ~/Private
chmod 700 ~/Private

# Personne d'autre ne peut lister/accéder
ls -ld ~/Private
# drwx------ 2 alice users 4096 Dec 19 /home/alice/Private
```

**4. Fichiers de configuration avec secrets** :
```bash
# Config app avec API keys
chmod 700 ~/.config/app/
chmod 600 ~/.config/app/credentials.json

# Fichier mot de passe
chmod 600 ~/.pgpass      # PostgreSQL
chmod 600 ~/.my.cnf      # MySQL
chmod 600 ~/.netrc       # FTP credentials
```

**Différences avec autres permissions** :

| Permission | Octal | Qui peut lire ? | Qui peut modifier ? | Qui peut exécuter ? |
|------------|-------|-----------------|---------------------|---------------------|
| `rwx------` | 700 | Propriétaire seul | Propriétaire seul | Propriétaire seul |
| `rwxr-x---` | 750 | Prop + Groupe | Propriétaire seul | Prop + Groupe |
| `rwxr-xr-x` | 755 | Tous | Propriétaire seul | Tous |
| `rw-------` | 600 | Propriétaire seul | Propriétaire seul | Personne |
| `rw-r-----` | 640 | Prop + Groupe | Propriétaire seul | Personne |

**Erreurs courantes** :
```bash
# ❌ ERREUR : Donner 777 (trop ouvert)
chmod 777 script.sh  # Tout le monde peut modifier !

# ✅ CORRECT : 700 si usage personnel
chmod 700 script.sh

# ✅ ou 755 si partage en lecture seule
chmod 755 script.sh
```

💡 **Quand utiliser 700 ?**
- Scripts/binaires personnels
- Fichiers avec mots de passe
- Clés cryptographiques
- Répertoires privés
- Tout ce que vous seul devez voir/modifier/exécuter

**Question 23 : D) Toutes correctes - Ajouter exécution pour tous**

Les syntaxes `+x`, `a+x` et `ugo+x` sont **strictement équivalentes** :

**Syntaxes équivalentes** :
```bash
chmod +x script.sh       # Implicite = all
chmod a+x script.sh      # a = all (explicite)
chmod ugo+x script.sh    # u+g+o = all (verbeux)
```

**Résultat identique** :
```bash
# Fichier initial
ls -l script.sh
# -rw-r--r-- 1 alice users 512 Dec 19 script.sh

# Avec n'importe quelle syntaxe
chmod +x script.sh
# OU chmod a+x script.sh
# OU chmod ugo+x script.sh

# Résultat
ls -l script.sh
# -rwxr-xr-x 1 alice users 512 Dec 19 script.sh
#    ↑  ↑  ↑ x ajouté partout
```

**Catégories utilisateurs (chmod symbolique)** :

| Symbole | Signification | Cible |
|---------|---------------|-------|
| **u** | User (owner) | Propriétaire |
| **g** | Group | Groupe |
| **o** | Others | Autres |
| **a** | All | Tous (u+g+o) |
| *(rien)* | Implicite All | Tous (défaut) |

**Opérateurs symboliques** :

| Opérateur | Action | Exemple |
|-----------|--------|----------|
| **+** | Ajouter | `chmod +x` (ajoute exécution) |
| **-** | Retirer | `chmod -x` (retire exécution) |
| **=** | Définir exactement | `chmod u=rwx` (impose rwx) |

**Différences selon la cible** :

```bash
# Fichier initial : rw-r--r-- (644)
ls -l file.txt
# -rw-r--r-- 1 alice users 100 Dec 19 file.txt

# Ajouter x seulement pour propriétaire
chmod u+x file.txt
ls -l file.txt
# -rwxr--r-- 1 alice users 100 Dec 19 file.txt

# Ajouter x seulement pour groupe
chmod g+x file.txt
ls -l file.txt
# -rw-r-xr-- 1 alice users 100 Dec 19 file.txt

# Ajouter x seulement pour autres
chmod o+x file.txt
ls -l file.txt
# -rw-r--r-x 1 alice users 100 Dec 19 file.txt

# Ajouter x pour TOUS (équivalent)
chmod a+x file.txt
chmod +x file.txt
chmod ugo+x file.txt
ls -l file.txt
# -rwxr-xr-x 1 alice users 100 Dec 19 file.txt
```

**Combinaisons multiples** :
```bash
# Plusieurs opérations en une commande (séparées par virgules)
chmod u+rwx,g+rx,o+r file.sh
# Prop: +rwx, Groupe: +rx, Autres: +r

# Ajouter pour certains, retirer pour d'autres
chmod u+x,go-w file.sh
# Prop: +execute, Groupe+Autres: -write

# Mixer opérateurs
chmod u=rwx,g=rx,o=r file.sh
# Prop: exactement rwx, Groupe: rx, Autres: r
```

**Cas d'usage professionnels** :

**1. Rendre script exécutable** :
```bash
# Après création script
vim backup.sh
# #!/bin/bash
# echo "Backup..."

# Rendre exécutable
chmod +x backup.sh

# Tester
./backup.sh
# Backup...
```

**2. Binaire pour tous vs usage personnel** :
```bash
# Programme partagé (tous peuvent exécuter)
chmod a+x /usr/local/bin/mytool
# ou simplement
chmod +x /usr/local/bin/mytool

# Programme personnel (seulement moi)
chmod u+x ~/bin/personal-script
chmod go-rwx ~/bin/personal-script  # Retirer tout accès autres
```

**3. Répertoires (droit x = traverser)** :
```bash
# Pour répertoire, x = peut entrer dans répertoire
mkdir project
chmod +x project   # Tous peuvent traverser

# Sans x sur répertoire
chmod -x project
ls project
# ls: cannot access 'project': Permission denied

# Avec x mais sans r
chmod u+x,u-r project
cd project          # ✅ Fonctionne
ls                  # ❌ Permission denied (pas de r)
```

**Tableau récapitulatif +x** :

| Fichier avant | Commande | Résultat | Qui gagne x ? |
|---------------|----------|----------|---------------|
| `rw-r--r--` | `chmod +x` | `rwxr-xr-x` | Tous (u+g+o) |
| `rw-r--r--` | `chmod u+x` | `rwxr--r--` | Propriétaire seul |
| `rw-r--r--` | `chmod g+x` | `rw-r-xr--` | Groupe seul |
| `rw-r--r--` | `chmod o+x` | `rw-r--r-x` | Autres seul |
| `rw-r--r--` | `chmod ug+x` | `rwxr-xr--` | Prop + Groupe |

**Retirer exécution** :
```bash
# Retirer x pour tous
chmod -x file.sh
# ou
chmod a-x file.sh

# Retirer x seulement pour autres
chmod o-x file.sh
```

**Vérifier si exécutable** :
```bash
# Méthode 1 : ls
ls -l script.sh | grep "^-..x"
# Si résultat non vide → exécutable pour propriétaire

# Méthode 2 : test
if [ -x script.sh ]; then
    echo "Exécutable"
else
    echo "Non exécutable"
fi

# Méthode 3 : stat
stat -c "%A" script.sh | grep x
```

💡 **Mémo** :
- `+x` sans précision = `a+x` = `ugo+x` = Tous
- `u+x` = Propriétaire seul
- `go+x` = Groupe + Autres
- Sur répertoire : `x` = droit de traverser (cd)

**Question 24 : D) Toutes correctes - Changer propriétaire ET groupe**

Pour modifier **propriétaire et groupe simultanément**, trois syntaxes valides :

**Syntaxe A) Deux commandes** : `chown user fichier ; chgrp group fichier`
**Syntaxe B) Deux-points** : `chown user:group fichier`
**Syntaxe C) Point** : `chown user.group fichier`

**Comparaison des méthodes** :

| Syntaxe | Commandes | Efficacité | Standard |
|---------|-----------|------------|----------|
| `chown user fichier ; chgrp group fichier` | 2 | Moins efficace | ✅ Universel |
| `chown user:group fichier` | 1 | Efficace | ✅ Recommandé |
| `chown user.group fichier` | 1 | Efficace | ✅ Alternative |

**Exemples pratiques** :
```bash
# Fichier initial
ls -l data.txt
# -rw-r--r-- 1 root root 1024 Dec 19 data.txt

# Méthode A : Deux commandes séparées
sudo chown alice data.txt
sudo chgrp developers data.txt
ls -l data.txt
# -rw-r--r-- 1 alice developers 1024 Dec 19 data.txt

# Méthode B : user:group (RECOMMANDÉE)
sudo chown alice:developers data.txt
ls -l data.txt
# -rw-r--r-- 1 alice developers 1024 Dec 19 data.txt

# Méthode C : user.group (alternative)
sudo chown alice.developers data.txt
ls -l data.txt
# -rw-r--r-- 1 alice developers 1024 Dec 19 data.txt
```

**Toutes les syntaxes chown** :

| Syntaxe | Action | Exemple |
|---------|--------|----------|
| `chown user fichier` | Change propriétaire seul | `chown alice file` |
| `chown user: fichier` | Change user + groupe primaire user | `chown alice: file` |
| `chown user:group fichier` | Change user ET groupe | `chown alice:devs file` |
| `chown :group fichier` | Change groupe seul | `chown :devs file` |
| `chown user.group fichier` | Change user ET groupe (alt) | `chown alice.devs file` |
| `chgrp group fichier` | Change groupe seul | `chgrp devs file` |

**Cas spéciaux** :
```bash
# Changer user + assigner groupe primaire user
sudo chown alice: file.txt
# Équivalent à :
sudo chown alice:alice file.txt

# Changer SEULEMENT le groupe
sudo chown :developers file.txt
# Équivalent à :
sudo chgrp developers file.txt

# Référence à partir d'un autre fichier
sudo chown --reference=model.txt target.txt
# target.txt prend owner:group de model.txt
```

**Options principales** :

| Option | Description | Exemple |
|--------|-------------|----------|
| `-R` | Récursif | `chown -R user:group dir/` |
| `-v` | Verbeux | `chown -v alice file` |
| `-c` | Affiche seulement changements | `chown -c alice *.txt` |
| `--reference` | Copier depuis fichier | `chown --reference=src dest` |
| `-h` | Ne suit pas symlinks | `chown -h alice link` |
| `--from` | Si owner actuel correspond | `chown --from=root alice file` |

**Cas d'usage professionnels** :

**1. Transfert fichiers entre utilisateurs** :
```bash
# Alice transfère projet à Bob
sudo chown -R bob:developers /srv/project/
sudo chmod -R 750 /srv/project/

# Vérifier
ls -ld /srv/project
# drwxr-x--- 5 bob developers 4096 Dec 19 /srv/project
```

**2. Serveur web (www-data)** :
```bash
# Fichiers web doivent appartenir à www-data
sudo chown -R www-data:www-data /var/www/html/

# Permissions correctes
sudo find /var/www/html -type d -exec chmod 755 {} \;
sudo find /var/www/html -type f -exec chmod 644 {} \;
```

**3. Restauration backup** :
```bash
# Après restore, fichiers appartiennent à root
tar -xzf backup.tar.gz
ls -l
# -rw-r--r-- 1 root root ...

# Redonner à utilisateur
sudo chown -R alice:alice restored_files/
```

**4. Synchronisation permissions multi-serveurs** :
```bash
# Copier ownership d'un fichier modèle
sudo chown --reference=/srv/template.conf /srv/new.conf
```

💡 **Pourquoi user:group recommandé ?**
- **Une seule commande** (plus rapide)
- **Atomique** (change tout en une opération)
- **Standard moderne** (Linux, BSD)
- **Moins d'appels système**

**Question 25 : A) Fichiers 644, Dossiers 755 - umask 022**

Le **umask (user file creation mask)** définit les permissions **retirées** lors de la création de fichiers/dossiers :

**Formule de calcul** :
```
Permissions finales = Permissions par défaut - umask

Pour FICHIERS : 666 - umask
Pour DOSSIERS : 777 - umask
```

**Calcul avec umask 022** :
```bash
# Fichiers
666 (rw-rw-rw-)  →  Défaut théorique
-022 (----w--w-)  →  umask (retire write groupe+autres)
────────────────
=644 (rw-r--r--)  →  Résultat final

# Dossiers
777 (rwxrwxrwx)  →  Défaut théorique  
-022 (----w--w-)  →  umask
────────────────
=755 (rwxr-xr-x)  →  Résultat final
```

**Démonstration pratique** :
```bash
# Voir umask actuel
umask
# 0022

# Créer fichier
touch newfile.txt
ls -l newfile.txt
# -rw-r--r-- 1 alice users 0 Dec 19 newfile.txt
#  644 (rw-r--r--)

# Créer dossier
mkdir newdir
ls -ld newdir
# drwxr-xr-x 2 alice users 4096 Dec 19 newdir
#  755 (rwxr-xr-x)
```

**Tableau umask courants** :

| umask | Fichiers | Dossiers | Usage typique |
|-------|----------|----------|---------------|
| **0022** | 644 (rw-r--r--) | 755 (rwxr-xr-x) | **Défaut** : Lecture publique |
| **0002** | 664 (rw-rw-r--) | 775 (rwxrwxr-x) | Collaboration groupe |
| **0077** | 600 (rw-------) | 700 (rwx------) | Sécurité max (privé) |
| **0027** | 640 (rw-r-----) | 750 (rwxr-x---) | Groupe lecture seule |
| **0007** | 660 (rw-rw----) | 770 (rwxrwx---) | Partage groupe total |
| **0000** | 666 (rw-rw-rw-) | 777 (rwxrwxrwx) | ⚠️ Dangereux (tout ouvert) |

**Calcul détaillé bit par bit** :
```
umask 022 (octal)

0 2 2
│ │ │
│ │ └─ Autres : 2 (binaire 010) = -w- (retire write)
│ └─── Groupe : 2 (binaire 010) = -w- (retire write)  
└───── User   : 0 (binaire 000) = --- (rien retiré)

Fichier 666 :    rw- rw- rw-
umask 022  :     --- -w- -w-  (bits à retirer)
             ───────────────
Résultat   :     rw- r-- r--  = 644

Dossier 777 :    rwx rwx rwx
umask 022  :     --- -w- -w-  (bits à retirer)
             ───────────────  
Résultat   :     rwx r-x r-x  = 755
```

**Modifier umask** :
```bash
# Voir umask actuel (octal)
umask
# 0022

# Voir umask actuel (symbolique)
umask -S
# u=rwx,g=rx,o=rx

# Changer umask (session courante)
umask 0077

# Vérifier
umask
# 0077

# Créer fichier avec nouveau umask
touch private.txt
ls -l private.txt
# -rw------- 1 alice users 0 Dec 19 private.txt
#  600 (rw-------)
```

**umask permanent** :
```bash
# Pour utilisateur (bash)
echo "umask 0022" >> ~/.bashrc
echo "umask 0022" >> ~/.profile

# Pour utilisateur (zsh)
echo "umask 0022" >> ~/.zshrc

# Système (tous users)
sudo nano /etc/profile
# Ajouter : umask 0022

# Par défaut système
sudo nano /etc/login.defs
# UMASK 022

# Recharger config
source ~/.bashrc
```

**Cas d'usage professionnels** :

**1. Environnement sécurisé (umask 0077)** :
```bash
# Admins système, fichiers sensibles
umask 0077

# Fichiers créés
touch admin-script.sh
ls -l admin-script.sh
# -rw------- (600)

mkdir admin-data
ls -ld admin-data
# drwx------ (700)

# Personne d'autre ne peut accéder
```

**2. Projet collaboratif (umask 0002)** :
```bash
# Équipe développement
umask 0002

# Fichiers créés
touch team-code.py
ls -l team-code.py
# -rw-rw-r-- (664)
# Groupe peut modifier

mkdir team-project
ls -ld team-project  
# drwxrwxr-x (775)
# Groupe peut créer fichiers dedans
```

**3. Serveur web (umask 0022)** :
```bash
# Fichiers web lisibles par tous
umask 0022

touch /var/www/html/index.html
ls -l /var/www/html/index.html
# -rw-r--r-- (644)
# Lisible par serveur web (www-data)
```

**4. Script avec umask temporaire** :
```bash
#!/bin/bash
# backup-script.sh

# Sauvegarder umask actuel
OLD_UMASK=$(umask)

# Umask strict pour backup
umask 0077

# Créer backup (sera 600/700)
tar czf /backup/data-$(date +%Y%m%d).tar.gz /data

# Restaurer umask
umask $OLD_UMASK
```

**Différences umask par défaut** :

| Système | umask défaut | Fichiers | Dossiers |
|---------|--------------|----------|----------|
| **Ubuntu/Debian** | 0022 | 644 | 755 |
| **RedHat/CentOS** | 0022 | 644 | 755 |
| **Root** | 0022 | 644 | 755 |
| **Services** | Varie | Dépend service | - |

**Exercices de calcul** :
```bash
# umask 0027
Fichiers : 666 - 027 = 640 (rw-r-----)
Dossiers : 777 - 027 = 750 (rwxr-x---)

# umask 0007  
Fichiers : 666 - 007 = 660 (rw-rw----)
Dossiers : 777 - 007 = 770 (rwxrwx---)

# umask 0000
Fichiers : 666 - 000 = 666 (rw-rw-rw-) ⚠️
Dossiers : 777 - 000 = 777 (rwxrwxrwx) ⚠️
```

⚠️ **Attention** :
```bash
# umask n'AJOUTE PAS de permissions
# Il ne peut que retirer

# umask 0000 ne donne PAS 777 aux fichiers
# Fichiers restent 666 max (pas x par défaut)

# Pour x sur fichier : chmod explicit
chmod +x fichier
```

💡 **Bonnes pratiques** :
- **0022** : Standard, équilibré
- **0077** : Sécurité maximale (admin)
- **0002** : Collaboration en équipe
- Définir dans `~/.bashrc` pour persistance
- Scripts sensibles : umask temporaire 0077

**Question 26 : D) B et C correctes - Option récursive**

Pour appliquer **récursivement** (dossier + contenu), deux syntaxes équivalentes :
- **`-R`** (courte)
- **`--recursive`** (longue)

**Syntaxe** :
```bash
chmod -R 755 /srv/project
chmod --recursive 755 /srv/project  # Équivalent

chown -R alice:devs /srv/project
chown --recursive alice:devs /srv/project  # Équivalent
```

**Comparaison** :

| Option | Forme | Portabilité | Usage |
|--------|-------|-------------|-------|
| `-R` | Courte (POSIX) | ✅ Universelle | ✅ Recommandé (concis) |
| `--recursive` | Longue (GNU) | Linux/BSD | Scripts auto-documentés |

**Exemple pratique** :
```bash
# Structure projet
mkdir -p /srv/project/{src,docs,tests}
touch /srv/project/src/main.py
touch /srv/project/docs/README.md

# Sans -R (ERREUR)
chmod 755 /srv/project
# Change seulement /srv/project, pas le contenu

# Avec -R (CORRECT)
chmod -R 755 /srv/project
# Change /srv/project ET tous fichiers/sous-dossiers

ls -lR /srv/project
# drwxr-xr-x /srv/project/src
# drwxr-xr-x /srv/project/docs  
# -rwxr-xr-x /srv/project/src/main.py
# ...
```

**⚠️ DANGERS du récursif** :

**1. Permissions trop ouvertes** :
```bash
# ❌ TRÈS DANGEREUX
sudo chmod -R 777 /var/www
# TOUT devient modifiable par TOUS !

# ✅ CORRECT
sudo chmod -R 755 /var/www
```

**2. Fichiers vs dossiers** :
```bash
# Problème : Fichiers ne doivent pas être exécutables
chmod -R 755 /srv/data
# Maintenant fichiers textes sont -rwxr-xr-x (x inutile)

# Solution : find séparé
find /srv/data -type f -exec chmod 644 {} \;  # Fichiers
find /srv/data -type d -exec chmod 755 {} \;  # Dossiers
```

**3. Permissions système écrasées** :
```bash
# ❌ CATASTROPHE
sudo chmod -R 777 /
# Système DÉTRUIT (SSH, sudo, etc. cassés)

# ❌ Également dangereux
sudo chmod -R 755 /etc
# Fichiers sensibles (/etc/shadow) exposés
```

**Bonnes pratiques récursif** :

**1. Cibler précisément** :
```bash
# ✅ BON : Uniquement répertoire projet
chmod -R 750 ~/project

# ❌ MAUVAIS : Trop large
chmod -R 750 ~  # Tout le home !
```

**2. Différencier fichiers/dossiers** :
```bash
# Méthode professionnelle
# Dossiers : 755 (rwxr-xr-x)
find /srv/web -type d -exec chmod 755 {} +

# Fichiers : 644 (rw-r--r--)
find /srv/web -type f -exec chmod 644 {} +

# Scripts seulement : +x
find /srv/web -type f -name "*.sh" -exec chmod +x {} +
```

**3. Vérifier avant d'appliquer** :
```bash
# Dry-run avec find
find /srv/project -type f
# Lister ce qui sera changé

# Puis appliquer
find /srv/project -type f -exec chmod 644 {} +
```

**Cas d'usage professionnels** :

**1. Site web après upload** :
```bash
# Fichiers uploadés appartiennent à mauvais user
sudo chown -R www-data:www-data /var/www/html

# Permissions correctes
find /var/www/html -type d -exec chmod 755 {} +
find /var/www/html -type f -exec chmod 644 {} +

# Upload directories writable
chmod 775 /var/www/html/uploads
```

**2. Projet transféré** :
```bash
# Alice donne projet à Bob
sudo chown -R bob:developers /srv/project

# Groupe peut lire/exécuter, pas modifier
chmod -R 750 /srv/project
```

**3. Backup restoration** :
```bash
# Fichiers extraits appartiennent à root
tar xzf backup.tar.gz

# Redonner à user
sudo chown -R alice:alice restored/
chmod -R u+rwX,go+rX restored/
# Note: X majuscule = x seulement pour dossiers
```

**Syntaxe chmod avec X majuscule** :
```bash
# X = Execute seulement sur dossiers (pas fichiers)
chmod -R u+rwX,go+rX /srv/data

# Résultat :
# Dossiers : rwxr-xr-x (755)
# Fichiers : rw-r--r-- (644)
# Scripts déjà +x : gardent x
```

**Progression / verbose** :
```bash
# Voir chaque changement
chmod -R -v 755 /srv/project
# mode of '/srv/project' changed from 0700 to 0755
# mode of '/srv/project/file1' changed from 0600 to 0755
# ...

# Seulement changements effectués
chmod -R -c 755 /srv/project
# Affiche uniquement si permission a changé

# Chown verbeux
chown -R -v alice:devs /srv/project
```

💡 **Mémo sécurité** :
- ✅ `chmod -R` sur VOS projets
- ❌ `chmod -R` sur `/`, `/etc`, `/var`
- ✅ `find` pour séparer fichiers/dossiers
- ❌ `chmod -R 777` (jamais en production)
- ✅ Tester sur copie avant production

**Question 27 : A) 664 - Lecture des permissions**

Lecture des permissions symboliques **rw-rw-r--** :

**Décomposition** :
```
rw-rw-r--
│││ │││ │││
│││ │││ │└└─ Autres : r-- = 4+0+0 = 4
│││ │└└───── Groupe : rw- = 4+2+0 = 6  
│└└─────────── Propriétaire : rw- = 4+2+0 = 6

Résultat : 664
```

**Méthode de lecture** :
1. **Diviser en 3 groupes de 3 caractères**
2. **Chaque groupe** = Propriétaire / Groupe / Autres
3. **Calculer octal** : r=4, w=2, x=1, -=0

**Exercices de lecture** :

```bash
# Exercice 1
-rwxr-xr-x
Propriétaire : rwx = 7
Groupe       : r-x = 5
Autres       : r-x = 5
Réponse : 755

# Exercice 2  
-rw-r--r--
Propriétaire : rw- = 6
Groupe       : r-- = 4
Autres       : r-- = 4
Réponse : 644

# Exercice 3
-rwx------
Propriétaire : rwx = 7
Groupe       : --- = 0
Autres       : --- = 0
Réponse : 700

# Exercice 4
drwxrwxr-x
d            = Répertoire (ignorer pour calcul)
Propriétaire : rwx = 7
Groupe       : rwx = 7
Autres       : r-x = 5
Réponse : 775

# Exercice 5
-rw-rw----
Propriétaire : rw- = 6
Groupe       : rw- = 6
Autres       : --- = 0
Réponse : 660
```

**Format complet ls -l** :
```bash
ls -l fichier.txt
# -rw-rw-r-- 1 alice developers 1024 Dec 19 10:30 fichier.txt
#  │││ │││ │││ │ │     │          │    │           │
#  │││ │││ │││ │ │     │          │    │           Nom
#  │││ │││ │││ │ │     │          │    Date
#  │││ │││ │││ │ │     │          Taille (octets)
#  │││ │││ │││ │ │     Groupe
#  │││ │││ │││ │ Propriétaire
#  │││ │││ │││ Nombre de liens
#  │││ │││ │└└─ Autres : r--
#  │││ │└└───── Groupe : rw-
#  │└└─────────── Propriétaire : rw-
#  Type fichier
```

**Types de fichiers (1er caractère)** :

| Caractère | Type | Exemple |
|-----------|------|----------|
| **-** | Fichier régulier | `-rw-r--r-- file.txt` |
| **d** | Répertoire | `drwxr-xr-x dir/` |
| **l** | Lien symbolique | `lrwxrwxrwx link -> target` |
| **c** | Périphérique caractère | `crw-rw---- /dev/tty0` |
| **b** | Périphérique bloc | `brw-rw---- /dev/sda1` |
| **p** | Pipe nommé | `prw-r--r-- fifo` |
| **s** | Socket | `srwxr-xr-x socket` |

**Permissions spéciales** :
```bash
# SUID (s à la place de x propriétaire)
-rwsr-xr-x  →  4755
#    ↑ s = SUID + x

# SGID (s à la place de x groupe)
-rwxr-sr-x  →  2755  
#      ↑ s = SGID + x

# Sticky bit (t à la place de x autres)
drwxrwxrwt  →  1777
#         ↑ t = Sticky + x

# Sans x (majuscules)
-rwSr-xr-x  →  4654 (S = SUID sans x)
-rwxr-Sr-x  →  2754 (S = SGID sans x)
drwxrwxrwT  →  1776 (T = Sticky sans x)
```

**Vérifier permissions d'un fichier** :
```bash
# Méthode 1 : ls -l
ls -l file.txt
# -rw-rw-r-- ...

# Méthode 2 : stat (plus détaillé)
stat file.txt
# Access: (0664/-rw-rw-r--)  Uid: (1000/  alice)   Gid: (1000/  alice)

# Méthode 3 : stat format court
stat -c "%a %A %n" file.txt  
# 664 -rw-rw-r-- file.txt
#  ^octal ^symbolique ^nom

# Méthode 4 : getfacl (ACL)
getfacl file.txt
# user::rw-
# group::rw-
# other::r--
```

**Interpréter permissions pour actions** :

| Permission | Fichier | Répertoire |
|------------|---------|------------|
| **r** (read) | Lire contenu | Lister fichiers (ls) |
| **w** (write) | Modifier contenu | Créer/supprimer fichiers |
| **x** (execute) | Exécuter | Traverser (cd), accéder fichiers |

**Tests pratiques** :
```bash
# Créer fichier test
touch test.txt
chmod 664 test.txt
ls -l test.txt
# -rw-rw-r-- test.txt

# Propriétaire (alice) peut :
cat test.txt      # ✅ Lire (r)
echo "x" > test.txt # ✅ Écrire (w)
./test.txt        # ❌ Exécuter (pas x)

# Membre groupe peut :
cat test.txt      # ✅ Lire (r)
echo "x" >> test.txt # ✅ Écrire (w)  
./test.txt        # ❌ Exécuter (pas x)

# Autres peuvent :
cat test.txt      # ✅ Lire (r)
echo "x" >> test.txt # ❌ Écrire (pas w)
./test.txt        # ❌ Exécuter (pas x)
```

💡 **Mémo rapide** :
- **r** = 4, **w** = 2, **x** = 1
- **3 groupes** : Propriétaire / Groupe / Autres
- **Additionner** chaque groupe
- **664** = Fichiers partagés groupe
- **644** = Fichiers standard
- **755** = Exécutables/dossiers

**Question 28 : A) chmod g-w fichier - Retirer écriture au groupe**

Pour **retirer l'écriture (w) au groupe**, syntaxe : `chmod g-w fichier`

**Décomposition** :
```
chmod g-w fichier
      │ │
      │ Opérateur - (retirer)
      Cible g (group)
      Permission w (write)
```

**Avant/Après** :
```bash
# Fichier initial
ls -l data.txt
# -rw-rw-r-- 1 alice devs 1024 Dec 19 data.txt
#     └── groupe a rw-

# Retirer w au groupe
chmod g-w data.txt

# Résultat
ls -l data.txt
# -rw-r--r-- 1 alice devs 1024 Dec 19 data.txt
#     └── groupe a maintenant r-- (w retiré)
```

**Syntaxe complète retrait** :

| Commande | Action | Résultat |
|----------|--------|----------|
| `chmod u-w file` | Retire write propriétaire | `r-xr-xr-x` |
| `chmod g-w file` | Retire write groupe | `rwxr-xr-x` |
| `chmod o-w file` | Retire write autres | `rwxrwxr-x` |
| `chmod a-w file` | Retire write tous | `r-xr-xr-x` |
| `chmod u-x file` | Retire execute propriétaire | `rw-rwxrwx` |
| `chmod g-r file` | Retire read groupe | `rwx-wxrwx` |
| `chmod o-rwx file` | Retire tout aux autres | `rwxrwx---` |
| `chmod go-w file` | Retire write groupe+autres | `rwxr-xr-x` |

**Exemples pratiques** :

**1. Protéger fichier contre modifications accidentelles** :
```bash
# Retirer write à tous (y compris propriétaire)
chmod a-w important.conf
ls -l important.conf
# -r--r--r-- 1 alice users 512 Dec 19 important.conf

# Tentative modification
echo "test" >> important.conf
# bash: important.conf: Permission denied

# Remettre write si nécessaire
chmod u+w important.conf
```

**2. Fichier lecture seule pour groupe** :
```bash
# Groupe peut lire mais pas modifier
chmod g-w,g+r project.txt
ls -l project.txt
# -rw-r--r-- 1 alice devs 1024 Dec 19 project.txt
```

**3. Bloquer exécution script** :
```bash
# Retirer x à tous
chmod a-x script.sh
ls -l script.sh
# -rw-rw-r-- 1 alice users 512 Dec 19 script.sh

# Plus exécutable
./script.sh
# bash: ./script.sh: Permission denied
```

**Combinaisons ajout+retrait** :
```bash
# Ajouter read, retirer write  
chmod u+r,u-w file

# Propriétaire : tout, Autres : rien
chmod u+rwx,go-rwx file

# Groupe : lecture seule
chmod g+r,g-wx file
```

💡 **Mémo** : `g-w` = **G**roupe **moins** **W**rite

**Question 29 : B) Applique le SUID (Set User ID)**

Le **SUID (Set User ID)** permet à un exécutable de **s'exécuter avec les droits de son propriétaire** (pas de l'utilisateur qui le lance) :

**Syntaxe SUID** :
```bash
# Méthode octale
chmod 4755 fichier
#     ↑ 4 = SUID bit
#      755 = permissions normales

# Méthode symbolique
chmod u+s fichier
#       ↑ s = SUID
```

**Visualisation** :
```bash
# Sans SUID
ls -l script.sh
# -rwxr-xr-x 1 root root 1024 Dec 19 script.sh
#    ↑ x normal

# Avec SUID
chmod 4755 script.sh
ls -l script.sh  
# -rwsr-xr-x 1 root root 1024 Dec 19 script.sh
#    ↑ s = SUID (x remplacé par s)

# SUID sans x (rare)
chmod 4644 script.sh
ls -l script.sh
# -rwSr--r-- 1 root root 1024 Dec 19 script.sh
#    ↑ S majuscule = SUID sans execute
```

**Fonctionnement SUID** :
```bash
# Fichier appartient à root avec SUID
ls -l /usr/bin/passwd
# -rwsr-xr-x 1 root root 68208 /usr/bin/passwd
#    ↑ s = SUID

# Alice (utilisateur normal) exécute passwd
passwd
# Le processus s'exécute avec UID=0 (root) !
# → Peut modifier /etc/shadow (fichier root-only)

# Sans SUID, alice ne pourrait pas modifier /etc/shadow
```

**Exemples système légitimes** :

| Commande | Propriétaire | SUID | Pourquoi ? |
|----------|--------------|------|------------|
| **`/usr/bin/passwd`** | root | ✅ | Modifier `/etc/shadow` (root-only) |
| **`/usr/bin/sudo`** | root | ✅ | Élévation privilèges |
| **`/bin/ping`** | root | ✅ | Socket raw ICMP (root-only) |
| **`/usr/bin/su`** | root | ✅ | Changer utilisateur |
| **`/usr/bin/mount`** | root | ✅ | Monter partitions |
| **`/usr/bin/chsh`** | root | ✅ | Modifier `/etc/passwd` |

**Trouver fichiers SUID** :
```bash
# Recherche SUID système
find /usr/bin /usr/sbin /bin /sbin -perm -4000 -type f -ls 2>/dev/null

# Sortie exemple
# -rwsr-xr-x 1 root root 68208 /usr/bin/passwd
# -rwsr-xr-x 1 root root 166056 /usr/bin/sudo
# -rwsr-xr-x 1 root root 44784 /usr/bin/chsh

# Recherche SUID dans TOUT le système (lent)
sudo find / -perm -4000 -type f -ls 2>/dev/null

# Format lisible
find /usr/bin -perm -4000 -exec ls -lh {} \;
```

**⚠️ DANGERS du SUID** :

**1. Escalade de privilèges** :
```bash
# ❌ TRÈS DANGEREUX : Script SUID root
#!/bin/bash
# script.sh (appartient à root)
rm -rf $1  # Variable user-contrôlée !

chmod 4755 script.sh
chown root:root script.sh

# Utilisateur malveillant
./script.sh "/"  # 🔥 Supprime TOUT en tant que root !
```

**2. Backdoor potentielle** :
```bash
# Attaquant crée backdoor
sudo cp /bin/bash /tmp/.hidden
sudo chmod 4755 /tmp/.hidden
sudo chown root:root /tmp/.hidden

# N'importe qui peut devenir root
/tmp/.hidden -p  # -p = preserve privileges
# Vous êtes maintenant root !
whoami
# root
```

**Audit sécurité SUID** :
```bash
# 1. Lister SUID non standard
find / -perm -4000 -type f 2>/dev/null | grep -v -E '(bin/passwd|bin/sudo|bin/su|bin/ping)'

# 2. SUID appartenant à users (DANGER !)
find / -perm -4000 ! -user root -type f 2>/dev/null

# 3. Fichiers SUID récemment modifiés
find / -perm -4000 -type f -mtime -7 2>/dev/null

# 4. Baseline sécurité
find / -perm -4000 -type f 2>/dev/null > /var/log/suid-baseline.txt
# Comparer régulièrement
```

**SGID (Set Group ID)** :
```bash
# SGID = Exécute avec GID du fichier
chmod 2755 fichier
chmod g+s fichier

ls -l fichier  
# -rwxr-sr-x 1 alice devs 1024 fichier
#       ↑ s = SGID

# Sur répertoire : fichiers créés héritent groupe
mkdir shared
chmod 2775 shared
chown alice:devs shared

cd shared
touch newfile  # newfile appartient au groupe 'devs' automatiquement
```

**Sticky Bit** :
```bash
# Sticky = Seul propriétaire peut supprimer
chmod 1777 /tmp
chmod +t /tmp

ls -ld /tmp
# drwxrwxrwt 1 root root 4096 /tmp
#         ↑ t = Sticky bit

# Alice crée fichier dans /tmp
touch /tmp/alice-file

# Bob ne peut PAS supprimer fichier d'Alice
rm /tmp/alice-file  # Permission denied
# (même si /tmp est 777)
```

**Permissions spéciales combinées** :

| Octal | Bits spéciaux | Symbolique | Usage |
|-------|----------------|------------|-------|
| **4755** | SUID | `-rwsr-xr-x` | Exécutable root |
| **2755** | SGID | `-rwxr-sr-x` | Groupe exécution |
| **1777** | Sticky | `drwxrwxrwt` | Répertoire partagé |
| **6755** | SUID+SGID | `-rwsr-sr-x` | Rare |
| **7777** | SUID+SGID+Sticky | `-rwsrwsrwt` | Très rare |

**Retirer SUID** :
```bash
# Méthode 1
chmod u-s fichier

# Méthode 2  
chmod 0755 fichier
#     ↑ 0 = retire tous bits spéciaux

# Vérifier
ls -l fichier
# -rwxr-xr-x (plus de s)
```

💡 **Bonnes pratiques** :
- ⚠️ **JAMAIS SUID sur scripts shell** (vulnérables)
- ✅ Auditer régulièrement fichiers SUID
- ✅ Retirer SUID inutiles
- ✅ Privilégier `sudo` avec contrôle fin
- ⚠️ Méfiance si SUID hors `/usr/bin`, `/bin`

**Question 30 : D) A et C correctes - chown récursif**

Pour changer **propriétaire récursivement**, options `-R` ou `--recursive` :

**Syntaxe** :
```bash
chown -R user:group /path
chown --recursive user:group /path  # Équivalent
```

**Cas d'usage serveur web** :

**1. Installation site web** :
```bash
# Fichiers uploadés appartiennent à root
ls -l /var/www/html
# drwxr-xr-x 5 root root 4096 Dec 19 html

# Donner à www-data (user serveur web)
sudo chown -R www-data:www-data /var/www/html

ls -l /var/www/html
# drwxr-xr-x 5 www-data www-data 4096 Dec 19 html

# Permissions correctes
sudo chmod -R 755 /var/www/html
```

**2. WordPress / CMS** :
```bash
# Strucuture WordPress
/var/www/wordpress/
├── wp-content/
│   ├── uploads/    # Doit être writable
│   ├── themes/
│   └── plugins/
└── wp-config.php  # Doit être protégé

# Propriétaire www-data
sudo chown -R www-data:www-data /var/www/wordpress

# Permissions files
sudo find /var/www/wordpress -type f -exec chmod 644 {} +

# Permissions dossiers
sudo find /var/www/wordpress -type d -exec chmod 755 {} +

# Upload writable
sudo chmod 775 /var/www/wordpress/wp-content/uploads

# Config protégé
sudo chmod 600 /var/www/wordpress/wp-config.php
```

**3. Applications Node.js / Python** :
```bash
# App appartient à user app
sudo chown -R appuser:appuser /opt/myapp

# Logs writable
sudo chown -R appuser:appuser /var/log/myapp
sudo chmod 755 /var/log/myapp
```

💡 **Mémo** : `-R` = Récursif (dossier + contenu)

---

### PARTIE 4 - MANIPULATION DE FICHIERS

**Question 31 : D) A et B correctes - Numérotation lignes**

Pour **numéroter les lignes d'un fichier**, deux commandes : `cat -n` et `nl` :

**Comparaison** :
```bash
# cat -n : Numérote TOUTES les lignes (y compris vides)
cat -n file.txt
#      1  Ligne 1
#      2  Ligne 2
#      3  
#      4  Ligne 4

# nl : Numérote SEULEMENT lignes non vides (défaut)
nl file.txt
#      1  Ligne 1
#      2  Ligne 2
#         
#      3  Ligne 4
```

**Options cat** :

| Option | Description | Exemple |
|--------|-------------|----------|
| `-n` | Numérote toutes lignes | `cat -n file` |
| `-b` | Numérote lignes non vides | `cat -b file` |
| `-s` | Compresse lignes vides multiples | `cat -s file` |
| `-A` | Affiche caractères invisibles | `cat -A file` |
| `-E` | Affiche $ à fin de ligne | `cat -E file` |
| `-T` | Affiche ^I pour tabs | `cat -T file` |

**Options nl (avancées)** :

| Option | Description | Exemple |
|--------|-------------|----------|
| `-b a` | Numérote toutes lignes | `nl -b a file` |
| `-b t` | Numérote lignes non vides (défaut) | `nl -b t file` |
| `-n ln` | Alignement à gauche | `nl -n ln file` |
| `-n rn` | Alignement à droite (défaut) | `nl -n rn file` |
| `-n rz` | Zéros à gauche | `nl -n rz file` |
| `-w 3` | Largeur numéros | `nl -w 3 file` |
| `-s " | "` | Séparateur | `nl -s " | " file` |

💡 **Mémo** : `cat -n` = toutes lignes, `nl` = lignes non vides

**Question 32 : B) tail -f fichier - Suivi temps réel**

La commande `tail -f` **suit un fichier en temps réel** (follow) :

**Syntaxe** :
```bash
# Suivre log en temps réel
tail -f /var/log/syslog
# Affiche nouvelles lignes au fur et à mesure
# Ctrl+C pour quitter
```

**Options tail** :

| Option | Description | Exemple |
|--------|-------------|----------|
| `-f` | Follow (suit fichier) | `tail -f log` |
| `-F` | Follow + retry si fichier recréé | `tail -F log` |
| `-n 20` | 20 dernières lignes | `tail -n 20 log` |
| `-n +5` | À partir ligne 5 | `tail -n +5 log` |
| `-c 100` | 100 derniers octets | `tail -c 100 log` |
| `--pid=PID` | Suit jusqu'à fin processus | `tail -f --pid=1234 log` |

**Cas d'usage monitoring** :

```bash
# Logs système
sudo tail -f /var/log/syslog
sudo tail -f /var/log/auth.log  # Authentifications
sudo tail -f /var/log/kern.log  # Kernel

# Apache/Nginx
sudo tail -f /var/log/apache2/access.log
sudo tail -f /var/log/apache2/error.log
sudo tail -f /var/log/nginx/error.log

# Application
tail -f /var/log/myapp/app.log

# Plusieurs fichiers simultanément
tail -f /var/log/syslog /var/log/auth.log

# Filtrer en temps réel (avec grep)
tail -f /var/log/syslog | grep ERROR
tail -f /var/log/apache2/access.log | grep "404"
```

**Différence -f vs -F** :
```bash
# -f : Suit fichier, arrête si fichier supprimé
tail -f app.log
# Si app.log supprimé → tail arrête

# -F : Suit fichier, continue si fichier recréé (logrotate)
tail -F app.log  
# Si app.log supprimé puis recréé → tail continue !
```

💡 **Astuce** : `-f` = Follow, `-F` = Follow + Retry

**Question 33 : B) find /chemin -mtime -7 - Fichiers modifiés récemment**

La commande `find` avec `-mtime` recherche fichiers par **date de modification** :

**Syntaxe -mtime** :
```bash
# -mtime -N : Modifiés depuis MOINS de N jours
find /chemin -mtime -7
# Fichiers modifiés depuis moins de 7 jours (semaine dernière)

# -mtime +N : Modifiés depuis PLUS de N jours
find /chemin -mtime +30
# Fichiers modifiés il y a plus de 30 jours (anciens)

# -mtime N : Modifiés EXACTEMENT il y a N jours
find /chemin -mtime 7
# Fichiers modifiés il y a exactement 7 jours
```

**Options temporelles find** :

| Option | Critère | Unité | Exemple |
|--------|---------|-------|----------|
| **-mtime** | Modification | Jours | `find -mtime -7` |
| **-mmin** | Modification | Minutes | `find -mmin -60` |
| **-atime** | Accès (lecture) | Jours | `find -atime +90` |
| **-amin** | Accès | Minutes | `find -amin -30` |
| **-ctime** | Changement métadonnées | Jours | `find -ctime -1` |
| **-cmin** | Changement métadonnées | Minutes | `find -cmin -120` |
| **-newer** | Plus récent que fichier | - | `find -newer ref.txt` |

**Différence mtime/atime/ctime** :
```bash
# mtime (Modification Time)
# Change quand CONTENU modifié
echo "data" >> file.txt  # mtime change

# atime (Access Time)  
# Change quand fichier LU
cat file.txt  # atime change

# ctime (Change Time)
# Change quand MÉTADONNÉES modifiées
chmod 644 file.txt  # ctime change
chown alice file.txt  # ctime change
```

**Exemples pratiques** :

```bash
# Fichiers modifiés aujourd'hui
find /srv -mtime 0

# Fichiers modifiés hier
find /srv -mtime 1

# Modifiés dernière semaine
find /srv -mtime -7

# Modifiés il y a plus de 6 mois
find /srv -mtime +180

# Logs anciens (> 30 jours)
find /var/log -name "*.log" -mtime +30

# Fichiers récemment accédés (< 1h)
find /tmp -amin -60

# Combiné avec actions
find /backup -mtime +90 -delete  # Supprimer backups > 90 jours
```

**Intervalles de dates** :
```bash
# Entre 7 et 14 jours
find /srv -mtime +7 -mtime -14

# Moins de 24h ET plus de 1h
find /srv -mmin +60 -mmin -1440
```

💡 **Mémo** : `-mtime -N` = moins de N jours, `-mtime +N` = plus de N jours

**Question 34 : B) grep -i error fichier - Recherche insensible à la casse**

La commande `grep -i` effectue une **recherche insensible à la casse** (ignore case) :

**Syntaxe** :
```bash
# Recherche sensible casse (défaut)
grep "error" fichier
# Trouve : error
# Ne trouve PAS : Error, ERROR, ErRoR

# Recherche INsensible casse
grep -i "error" fichier
# Trouve : error, Error, ERROR, ErRoR, eRrOr
```

**⚠️ ATTENTION Syntaxe** :
```bash
# ✅ CORRECT : Option courte (un tiret)
grep -i "error" fichier

# ✅ CORRECT : Option longue (DEUX tirets)
grep --ignore-case "error" fichier

# ❌ ERREUR : Option longue avec un seul tiret
grep -ignore-case "error" fichier
# grep: invalid option -- 'g'
# Les options longues GNU nécessitent TOUJOURS --
```

**Options grep principales** :

| Option courte | Option longue | Description |
|--------------|---------------|-------------|
| `-i` | `--ignore-case` | Insensible casse |
| `-v` | `--invert-match` | Inverse (lignes NE contenant PAS) |
| `-r` | `--recursive` | Récursif |
| `-n` | `--line-number` | Numéro ligne |
| `-c` | `--count` | Compter occurrences |
| `-l` | `--files-with-matches` | Noms fichiers seulement |
| `-w` | `--word-regexp` | Mot entier |
| `-A 3` | `--after-context=3` | 3 lignes après |
| `-B 3` | `--before-context=3` | 3 lignes avant |
| `-C 3` | `--context=3` | 3 lignes avant+après |

**Exemples pratiques** :

```bash
# Logs erreurs (toutes casses)
grep -i "error" /var/log/syslog
grep -i "warning" /var/log/syslog

# Avec numéros lignes
grep -in "error" app.log
# 42:Connection ERROR
# 156:error: timeout

# Compter erreurs
grep -ic "error" app.log
# 15

# Plusieurs patterns
grep -i -E "error|warning|fatal" app.log

# Contexte autour erreurs
grep -i -C 3 "error" app.log  # 3 lignes avant + 3 après
```

💡 **Mémo** : `-i` = **I**gnore case (insensible casse)

**Question 35 : B) wc -l fichier - Compter les lignes**

La commande `wc` (**word count**) compte lignes, mots et caractères :

**Options principales** :
```bash
# -l : Compter LIGNES
wc -l fichier.txt
# 150 fichier.txt

# -w : Compter MOTS
wc -w fichier.txt
# 987 fichier.txt

# -c : Compter CARACTÈRES (bytes)
wc -c fichier.txt
# 5420 fichier.txt

# -m : Compter CARACTÈRES (multibyte)
wc -m fichier.txt

# Sans option : Tout
wc fichier.txt
# 150  987  5420 fichier.txt
#  │    │    │
#  lignes mots bytes
```

**Exemples pratiques** :
```bash
# Lignes de code projet
find . -name "*.py" -exec cat {} \; | wc -l
# 15847

# Utilisateurs système
wc -l < /etc/passwd
# 42

# Fichiers dans répertoire
ls | wc -l
# 25

# Connexions actives
who | wc -l
# 3
```

💡 **Mémo** : `-l` = **L**ignes, `-w` = **W**ords (mots), `-c` = **C**aractères

**Question 36 : D) B et C correctes - Copie récursive**

Pour **copier récursivement un répertoire**, options `-r` ou `-R` :

**Syntaxe** :
```bash
cp -r source/ destination/
cp -R source/ destination/  # Équivalent
cp -a source/ destination/  # Archive (recommandé)
```

**Différences options** :

| Option | Description | Préserve timestamps | Préserve symlinks | Préserve permissions |
|--------|-------------|---------------------|-------------------|----------------------|
| **-r** | Récursif basique | ❌ | ❌ (suit liens) | ✅ |
| **-R** | Récursif (alias -r) | ❌ | ❌ (suit liens) | ✅ |
| **-a** | Archive (= -dR --preserve=all) | ✅ | ✅ (copie liens) | ✅ |

**Exemples** :
```bash
# Copie récursive simple
cp -r /srv/project /backup/

# Copie archive (préserve tout)
cp -a /srv/project /backup/
# Préserve : timestamps, permissions, ownership, symlinks

# Copie avec progress
cp -rv /srv/project /backup/
# -v = verbose (affiche chaque fichier)
```

💡 **Recommandation** : Utiliser `-a` pour backups (préserve tout)

**Question 37 : B) Affiche lignes NE contenant PAS "texte" - Invert match**

L'option `grep -v` (**invert match**) inverse la recherche :

**Fonctionnement** :
```bash
# Sans -v : Affiche lignes AVEC "error"
grep "error" app.log
# ERROR: Connection failed
# error: timeout

# Avec -v : Affiche lignes SANS "error"
grep -v "error" app.log  
# INFO: Starting application
# DEBUG: Loading config
# SUCCESS: Task completed
```

**Cas d'usage filtrage** :

```bash
# Fichiers NON cachés
ls -a | grep -v "^\."

# Utilisateurs NON système (UID < 1000)
awk -F: '$3 >= 1000' /etc/passwd

# Logs sans INFO
grep -v "INFO" app.log

# Plusieurs exclusions
grep -v -e "DEBUG" -e "INFO" app.log
# ou
grep -vE "DEBUG|INFO" app.log

# Processus sauf grep lui-même
ps aux | grep apache | grep -v grep
```

💡 **Mémo** : `-v` = in**V**ert (inverse la recherche)

**Question 38 : B) commande >> fichier - Redirection append**

Les redirections **ajoutent ou écrasent** la sortie vers un fichier :

**Tableau complet redirections** :

| Syntaxe | Action | Exemple |
|---------|--------|----------|
| `> fichier` | Écrase (overwrite) | `echo "test" > file` |
| `>> fichier` | Ajoute à la fin (append) | `echo "test" >> file` |
| `2> fichier` | Écrase stderr | `cmd 2> errors.log` |
| `2>> fichier` | Ajoute stderr | `cmd 2>> errors.log` |
| `&> fichier` | stdout + stderr (écrase) | `cmd &> all.log` |
| `&>> fichier` | stdout + stderr (append) | `cmd &>> all.log` |
| `2>&1` | stderr vers stdout | `cmd > file 2>&1` |
| `< fichier` | Entrée depuis fichier | `wc -l < file` |

**Exemples** :
```bash
# Écraser fichier
echo "nouveau" > file.txt
# file.txt contient maintenant : nouveau

# Ajouter à la fin
echo "ligne2" >> file.txt
# file.txt contient : nouveau\nligne2

# Logs avec timestamp
date >> /var/log/myapp.log
echo "App started" >> /var/log/myapp.log
```

💡 **Mémo** : `>` = écrase, `>>` = ajoute (append)

**Question 39 : B) Envoie sortie vers entrée autre commande - Pipe**

Le **pipe `|`** chaîne les commandes (sortie → entrée) :

**Principe** :
```bash
commande1 | commande2 | commande3
# sortie cmd1 → entrée cmd2 → sortie cmd2 → entrée cmd3
```

**Exemples classiques** :
```bash
# Lister et filtrer
ls -l | grep ".txt"

# Processus avec tri
ps aux | sort -k 3 -nr | head -10
# Top 10 CPU

# Compter fichiers
ls | wc -l

# Logs avec pagination
tail -f /var/log/syslog | grep ERROR | less

# Pipeline complexe
cat access.log | grep "404" | awk '{print $1}' | sort | uniq -c | sort -nr | head -20
# Top 20 IPs avec erreurs 404
```

💡 **Mémo** : `|` = pipe (tuyau), connecte sortie → entrée

**Question 40 : B) find /etc -name "*.conf" - Wildcards find**

La commande `find` utilise des **wildcards (glob patterns)** pour filtrer par nom :

**Wildcards principaux** :

| Pattern | Signification | Exemple |
|---------|---------------|----------|
| **`*`** | N'importe quoi (0+ caractères) | `*.txt` |
| **`?`** | Un caractère | `file?.txt` |
| **`[abc]`** | Un de ces caractères | `file[123].txt` |
| **`[a-z]`** | Plage caractères | `file[a-z].txt` |
| **`[!abc]`** | Pas ces caractères | `file[!0-9].txt` |

**Syntaxe find** :
```bash
# Fichiers .conf dans /etc
find /etc -name "*.conf"
# ⚠️ Guillemets OBLIGATOIRES pour *

# Sans guillemets (ERREUR shell)
find /etc -name *.conf
# Shell expand * AVANT find = problèmes

# Insensible casse
find /etc -iname "*.CONF"
# Trouve .conf, .Conf, .CONF

# Fichiers commençant par "log"
find /var/log -name "log*"

# Fichiers avec 1 caractère
find /tmp -name "?"

# Fichiers log1.txt à log9.txt
find /var/log -name "log[0-9].txt"
```

💡 **Important** : Toujours mettre wildcards entre guillemets avec find

---

### PARTIE 5 - RÉSEAU

**Question 41 : D) Toutes correctes - Afficher configuration réseau**

Trois commandes affichent la **configuration réseau IP** :

**Comparaison** :

| Commande | Statut | Sortie | Recommandation |
|----------|--------|--------|----------------|
| **`ifconfig`** | ⚠️ Déprécié | Format classique | Anciens systèmes |
| **`ip addr show`** | ✅ Moderne | Format détaillé | ✅ Recommandé |
| **`ip a`** | ✅ Moderne | Format détaillé | ✅ Alias court |

**Exemples** :
```bash
# ifconfig (ancien)
ifconfig
# eth0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
#       inet 192.168.1.100  netmask 255.255.255.0  broadcast 192.168.1.255

# ip addr show (moderne)
ip addr show
ip a  # Alias
# 2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500
#     inet 192.168.1.100/24 brd 192.168.1.255 scope global eth0
```

**Commandes ip principales** :
```bash
ip addr show        # Adresses IP
ip a                # Alias court
ip link show        # Interfaces (MAC, état)
ip route show       # Table routage
ip neigh show       # Cache ARP
```

💡 **Mémo** : Utiliser `ip` (moderne) plutôt que `ifconfig` (déprécié)

**Question 42 : D) Toutes correctes - Afficher table de routage**

Trois commandes affichent la **table de routage** :

**Commandes** :
```bash
# Moderne (recommandé)
ip route show
ip r  # Alias

# Classiques
route -n
netstat -rn
```

**Lecture table routage** :
```bash
ip route show
# default via 192.168.1.1 dev eth0 proto dhcp metric 100
# 192.168.1.0/24 dev eth0 proto kernel scope link src 192.168.1.100
# 172.17.0.0/16 dev docker0 proto kernel scope link src 172.17.0.1
#
# ↑ Route par défaut (0.0.0.0/0) via passerelle 192.168.1.1
# ↑ Réseau local 192.168.1.0/24 directement sur eth0
# ↑ Réseau Docker sur docker0
```

**Ajouter/Supprimer routes** :
```bash
# Ajouter route
sudo ip route add 10.0.0.0/8 via 192.168.1.254 dev eth0

# Supprimer route
sudo ip route del 10.0.0.0/8

# Route par défaut
sudo ip route add default via 192.168.1.1
```

💡 **Mémo** : `ip route show` = table routage complète

**Question 43 : A) ping IP - Tester connectivité**

La commande `ping` envoie des **paquets ICMP** pour tester la connectivité :

**Syntaxe basique** :
```bash
# Ping continu (Ctrl+C pour arrêter)
ping 8.8.8.8
# PING 8.8.8.8 (8.8.8.8) 56(84) bytes of data.
# 64 bytes from 8.8.8.8: icmp_seq=1 ttl=117 time=12.3 ms
# 64 bytes from 8.8.8.8: icmp_seq=2 ttl=117 time=11.8 ms

# Ping nom domaine
ping google.com
```

**Options principales** :

| Option | Description | Exemple |
|--------|-------------|----------|
| `-c N` | N paquets puis stop | `ping -c 4 8.8.8.8` |
| `-i N` | Intervalle N secondes | `ping -i 0.5 8.8.8.8` |
| `-s N` | Taille paquet | `ping -s 1000 8.8.8.8` |
| `-W N` | Timeout N secondes | `ping -W 2 8.8.8.8` |
| `-q` | Quiet (résumé seulement) | `ping -q -c 10 8.8.8.8` |
| `-f` | Flood (admin) | `sudo ping -f 8.8.8.8` |

**Interprétation résultats** :
```bash
ping -c 4 8.8.8.8
# --- 8.8.8.8 ping statistics ---
# 4 packets transmitted, 4 received, 0% packet loss, time 3005ms
# rtt min/avg/max/mdev = 11.234/12.456/14.123/1.234 ms
#
# ✅ 0% loss = Connexion parfaite
# ⚠️ 1-10% loss = Connexion dégradée
# ❌ >10% loss = Problème réseau
# ❌ 100% loss = Pas de connexion
```

**Diagnostics** :
```bash
# 1. Test passerelle
ping 192.168.1.1
# Si OK → Réseau local fonctionne

# 2. Test DNS public
ping 8.8.8.8
# Si OK → Internet accessible

# 3. Test résolution DNS
ping google.com  
# Si OK → DNS fonctionne
```

💡 **Mémo** : `ping -c 4` = 4 paquets puis arrêt automatique

**Question 44 : A) netstat -tuln - Ports en écoute**

La commande `netstat` affiche les **connexions réseau et ports** :

**Décomposition -tuln** :
```bash
netstat -tuln
# -t : TCP
# -u : UDP
# -l : Listening (en écoute)
# -n : Numérique (pas résolution DNS)
```

**Sortie exemple** :
```bash
netstat -tuln
# Proto Recv-Q Send-Q Local Address           Foreign Address         State
# tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN
# tcp        0      0 0.0.0.0:80              0.0.0.0:*               LISTEN
# tcp        0      0 127.0.0.1:3306          0.0.0.0:*               LISTEN
# udp        0      0 0.0.0.0:68              0.0.0.0:*
#
# ↑ SSH (22) écoute sur toutes IPs
# ↑ HTTP (80) écoute sur toutes IPs
# ↑ MySQL (3306) écoute seulement localhost
# ↑ DHCP client (68) UDP
```

**Options principales** :

| Option | Description |
|--------|-------------|
| `-t` | Connexions TCP |
| `-u` | Connexions UDP |
| `-l` | Ports en écoute (LISTEN) |
| `-n` | Format numérique (pas DNS) |
| `-p` | PID/Programme |
| `-a` | Toutes connexions |
| `-r` | Table routage |

**Exemples diagnostics** :
```bash
# Qui écoute sur port 80 ?
sudo netstat -tlnp | grep :80
# tcp  0  0 0.0.0.0:80  0.0.0.0:*  LISTEN  1234/nginx

# Toutes connexions établies
netstat -tn

# Statistiques
netstat -s
```

💡 **Mémo** : `-tuln` = **T**CP **U**DP **L**isten **N**umeric

**Question 45 : A) ss - Socket statistics (moderne)**

La commande `ss` est le **remplacement moderne de netstat** :

**Pourquoi ss > netstat ?**

| Aspect | netstat | ss |
|--------|---------|----|
| **Vitesse** | Lent | ✅ Rapide |
| **Statut** | Déprécié | ✅ Actif |
| **Informations** | Basique | ✅ Détaillées |
| **Syntaxe** | Classique | Moderne |

**Équivalences** :
```bash
# netstat → ss
netstat -tuln  →  ss -tuln
netstat -tnp   →  ss -tnp
netstat -s     →  ss -s
```

**Exemples ss** :
```bash
# Ports en écoute
ss -tuln

# Avec processus
sudo ss -tulnp

# Connexions établies
ss -tn state established

# Filtrer port
ss -tn sport = :80

# Statistiques
ss -s
```

💡 **Mémo** : `ss` = plus rapide que `netstat` (recommandé)

**Question 46 : A) dig domain.com - Requêtes DNS**

La commande `dig` effectue des **requêtes DNS détaillées** :

**Syntaxe basique** :
```bash
# Requête simple
dig google.com
# ; <<>> DiG 9.18.12 <<>> google.com
# ;; ANSWER SECTION:
# google.com.  300  IN  A  142.250.200.46
```

**Options principales** :
```bash
# Court (juste IP)
dig +short google.com
# 142.250.200.46

# Type enregistrement
dig google.com A      # IPv4
dig google.com AAAA   # IPv6
dig google.com MX     # Mail
dig google.com NS     # Nameservers
dig google.com TXT    # Texte

# Serveur DNS spécifique
dig @8.8.8.8 google.com

# Reverse lookup
dig -x 8.8.8.8
```

**Alternatives** :
```bash
# nslookup (interactif)
nslookup google.com

# host (simple)
host google.com
```

💡 **Mémo** : `dig +short` = juste l'IP (sortie courte)

**Question 47 : B) /etc/network/interfaces - Config réseau Debian**

La **configuration réseau varie selon la distribution** :

**Debian/Ubuntu ancien** :
```bash
# Fichier : /etc/network/interfaces
auto eth0
iface eth0 inet static
    address 192.168.1.100
    netmask 255.255.255.0
    gateway 192.168.1.1
    dns-nameservers 8.8.8.8 8.8.4.4
```

**Ubuntu moderne (Netplan)** :
```yaml
# Fichier : /etc/netplan/01-netcfg.yaml
network:
  version: 2
  ethernets:
    eth0:
      addresses:
        - 192.168.1.100/24
      gateway4: 192.168.1.1
      nameservers:
        addresses: [8.8.8.8, 8.8.4.4]

# Appliquer
sudo netplan apply
```

**RedHat/CentOS** :
```bash
# Fichier : /etc/sysconfig/network-scripts/ifcfg-eth0
DEVICE=eth0
BOOTPROTO=static
IPADDR=192.168.1.100
NETMASK=255.255.255.0
GATEWAY=192.168.1.1
DNS1=8.8.8.8
DNS2=8.8.4.4
ONBOOT=yes

# Redémarrer
sudo systemctl restart network
```

💡 **Mémo** : Debian → `/etc/network/interfaces`, RedHat → `/etc/sysconfig/network-scripts/`

**Question 48 : D) Toutes correctes - Activer interface réseau**

Trois commandes pour **activer une interface réseau** :

**Méthodes** :
```bash
# Méthode 1 : ifconfig (ancien)
sudo ifconfig eth0 up

# Méthode 2 : ifup (Debian)
sudo ifup eth0

# Méthode 3 : ip (moderne)
sudo ip link set eth0 up
```

**Désactiver** :
```bash
sudo ifconfig eth0 down
sudo ifdown eth0
sudo ip link set eth0 down
```

**Vérifier état** :
```bash
# Voir si UP
ip link show eth0
# 2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP>
#                             ↑ UP = activé

# Avec ifconfig
ifconfig eth0
# eth0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>
```

💡 **Recommandation** : Utiliser `ip link set` (commande moderne)

**Question 49 : D) A et B correctes - Tracer chemin réseau**

Les commandes `traceroute` et `tracepath` **tracent le chemin des paquets** :

**Syntaxe** :
```bash
# traceroute (nécessite installation)
sudo apt install traceroute
traceroute google.com

# tracepath (préinstallé)
tracepath google.com
```

**Sortie exemple** :
```bash
traceroute google.com
#  1  192.168.1.1 (192.168.1.1)  1.234 ms
#  2  10.0.0.1 (10.0.0.1)  5.678 ms
#  3  * * *
#  4  172.16.0.1 (172.16.0.1)  12.345 ms
# ...
# 10  142.250.200.46 (142.250.200.46)  23.456 ms
#
# ↑ Passerelle locale (hop 1)
# ↑ * * * = Routeur ne répond pas (normal)
# ↑ Destination finale (hop 10)
```

**Options** :
```bash
# Nombre max hops
traceroute -m 20 google.com

# Sans résolution DNS
traceroute -n google.com

# Protocole spécifique
traceroute -I google.com  # ICMP
traceroute -T google.com  # TCP
```

💡 **Mémo** : Trace le **chemin** entre vous et destination (hop par hop)

**Question 50 : D) B et C correctes - Changer hostname**

Pour modifier le **hostname (nom machine)** de façon permanente :

**Méthode 1 : hostnamectl (systemd - RECOMMANDÉ)** :
```bash
# Changer hostname
sudo hostnamectl set-hostname new-hostname

# Vérifier
hostnamectl
#    Static hostname: new-hostname
#          Icon name: computer-vm
#            Chassis: vm

# Redémarrage NON nécessaire
```

**Méthode 2 : Fichier /etc/hostname** :
```bash
# Éditer fichier
sudo nano /etc/hostname
# Remplacer contenu par : new-hostname

# Aussi modifier /etc/hosts
sudo nano /etc/hosts
# 127.0.0.1  localhost
# 127.0.1.1  new-hostname

# Redémarrer
sudo reboot
```

**Méthode temporaire (hostname)** :
```bash
# Change seulement jusqu'au reboot
sudo hostname temp-hostname

# Vérifier
hostname
# temp-hostname

# Après reboot → ancien nom revient
```

**Vérifier hostname** :
```bash
# Méthode 1
hostname

# Méthode 2
hostnamectl

# Méthode 3
cat /etc/hostname

# Méthode 4
uname -n
```

💡 **Recommandation** : Utiliser `hostnamectl` (permanent sans reboot)

---

## 📊 BARÈME ET ÉVALUATION

| Score | Niveau |
|-------|--------|
| 90-100 | ⭐⭐⭐ Excellent - Prêt pour l'examen ! |
| 75-89 | ⭐⭐ Très bien - Revoir quelques points |
| 60-74 | ⭐ Bien - Réviser les erreurs |
| 40-59 | ⚠️ Moyen - Revoir la fiche de révision |
| 0-39 | 🔴 Insuffisant - Révision approfondie nécessaire |


---

**💪 Bon courage ! Tu progresses à chaque QCM !**
