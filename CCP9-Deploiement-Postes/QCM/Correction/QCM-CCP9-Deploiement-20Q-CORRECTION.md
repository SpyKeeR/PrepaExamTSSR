**Question 1 : B) Sysprep /generalize /oobe**

**Explication** : `sysprep /generalize` supprime les informations spécifiques (SID, nom PC, activation) pour rendre l'image réutilisable. `/oobe` lance l'expérience de première utilisation au prochain boot. Sans généralisation, deux PCs auraient le même SID (problème de sécurité AD).

```powershell
C:\Windows\System32\Sysprep\sysprep.exe /generalize /oobe /shutdown
```

**Cas d'usage** : Préparer un PC maître Windows 10/11 avant capture d'image pour déploiement sur 100 postes identiques.

**Question 2 : A) Active Directory, DHCP, DNS, partition NTFS**

**Explication** : WDS s'intègre à AD pour authentifier les clients et gérer les permissions. DHCP fournit les adresses IP et les options PXE (66/67). DNS résout le nom du serveur WDS. NTFS est obligatoire pour stocker les images (ACL et compression).

| Composant | Rôle |
|-----------|------|
| **AD** | Authentification/autorisation |
| **DHCP** | Attribution IP + options PXE |
| **DNS** | Résolution nom serveur |
| **NTFS** | Stockage images (ACL) |

**Cas d'usage** : Infrastructure Server 2022 avec domaine contoso.local pour déployer 200 postes Win11.

**Question 3 : A) Boot réseau via DHCP et TFTP**

**Explication** : Le client PXE (firmware réseau) envoie une requête DHCP pour obtenir IP + adresse serveur TFTP. Il télécharge le bootloader (wdsnbp.com) puis le menu de démarrage via TFTP. Aucun support local nécessaire (USB, disque vide).

```
Client PXE → DHCP Discover → DHCP Offer (IP + options 66/67) → TFTP boot.wim
```

**Cas d'usage** : Déployer Windows sur 50 PCs neufs sans clé USB ni intervention manuelle.

**Question 4 : B) Format compressé Microsoft manipulé par DISM**

**Explication** : WIM (Windows Imaging Format) est un format basé fichiers (pas secteurs comme Ghost). Supporte compression LZX, multi-index (plusieurs éditions dans 1 fichier) et montage lecture/écriture. DISM est l'outil natif pour monter, modifier et appliquer les WIM.

```powershell
Dism /Get-ImageInfo /ImageFile:install.wim  # Lister les index
Dism /Mount-Wim /WimFile:install.wim /Index:1 /MountDir:C:\Mount
```

**Cas d'usage** : Un seul install.wim contient Win11 Pro (index 1) et Enterprise (index 2) pour économiser l'espace.

**Question 5 : B) MDT orchestre avec Task Sequences (applications, drivers)**

**Explication** : WDS déploie uniquement l'image OS brute. MDT ajoute des Task Sequences pour automatiser installation d'apps, injection drivers, configuration BIOS, jonction domaine et personnalisation post-déploiement. MDT utilise WDS comme mécanisme de transport.

| Critère | WDS | MDT |
|---------|-----|-----|
| **OS seul** | ✓ | ✓ |
| **Applications** | ✗ | ✓ |
| **Drivers** | Limité | ✓ Inject auto |
| **Personnalisation** | Unattend.xml | Task Sequences |

**Cas d'usage** : MDT pour déployer Win11 + Office 365 + drivers HP + join domaine en 1 clic.

**Question 6 : B) Automatise installation (nom PC, domaine, partitionnement)**

**Explication** : Unattend.xml contient les réponses aux questions du setup Windows. Sections principales : **windowsPE** (partitions, clé), **specialize** (nom PC, domaine), **oobeSystem** (compte local, fuseau). Créé avec Windows SIM (System Image Manager).

| Section | Contenu |
|---------|------|
| **windowsPE** | Partitionnement, langue, clé produit |
| **specialize** | Nom PC, join domaine, réseau |
| **oobeSystem** | Compte admin, skip OOBE |

**Cas d'usage** : Déployer 100 PCs avec nom auto PC-001 à PC-100 et jonction domaine contoso.local sans interaction.

**Question 7 : A) Option 66 (IP serveur), 67 (fichier boot)**

**Explication** : Option 66 indique l'IP ou nom DNS du serveur TFTP/WDS. Option 67 spécifie le chemin du bootloader réseau (ex: `boot\x64\wdsnbp.com` pour UEFI x64). Le client PXE télécharge ce fichier pour afficher le menu WDS.

```powershell
Set-DhcpServerv4OptionValue -OptionId 66 -Value "192.168.1.10"
Set-DhcpServerv4OptionValue -OptionId 67 -Value "boot\x64\wdsnbp.com"
```

**Cas d'usage** : Configuration DHCP pour permettre à 200 PCs UEFI de booter en PXE sur le serveur WDS.

**Question 8 : B) Déploiement simultané avec un seul flux réseau**

**Explication** : Le multicast WDS envoie l'image une seule fois sur une adresse IP multicast (239.x.x.x). Tous les clients reçoivent le même flux simultanément. Économise massivement la bande passante : 50 PCs = 1x trafic au lieu de 50x en unicast.

| Type | Bande passante | Vitesse |
|------|----------------|--------|
| **Unicast** | N × taille image | Rapide par client |
| **Multicast** | 1 × taille image | Lent (attente tous) |

**Cas d'usage** : Déployer 100 PCs simultanément dans une salle de formation sans saturer le réseau 1 Gbps.

**Question 9 : A) Mount-Wim, Add-Driver, Add-Package, Commit-Wim**

**Explication** : `/Mount-Wim` monte l'image en lecture/écriture. `/Add-Driver` injecte drivers .inf, `/Add-Package` installe mises à jour .cab/.msu. `/Commit-Wim` sauvegarde les modifications, `/Unmount-Wim /Discard` annule. Workflow classique : mount → modify → commit.

```powershell
Dism /Mount-Wim /WimFile:install.wim /Index:1 /MountDir:C:\Mount
Dism /Image:C:\Mount /Add-Driver /Driver:C:\Drivers /Recurse
Dism /Commit-Wim /MountDir:C:\Mount
```

**Cas d'usage** : Intégrer drivers réseau HP dans une image Win11 avant déploiement sur 50 PC HP.

**Question 10 : A) KMS (serveur local), MAK (clé compte fixe)**

**Explication** : KMS (Key Management Service) active via serveur local, renouvellement tous les 180j, minimum 25 PCs/5 servers. MAK (Multiple Activation Key) active directement sur serveurs Microsoft, compteur décrémenté, pas de renouvellement. KMS préféré en entreprise (pas de connexion Internet).

| Type | Serveur | Renouvellement | Minimum |
|------|---------|----------------|--------|
| **KMS** | Local | 180 jours | 25 PCs |
| **MAK** | Microsoft | Non | 1 activation |

**Cas d'usage** : KMS pour 500 PCs en réseau privé, MAK pour 10 laptops nomades sans VPN.

**Question 11 : B) Add-Computer -DomainName -Credential -Restart**

**Explication** : `Add-Computer` joint un PC au domaine AD via PowerShell. `-Credential` spécifie un compte autorisé (Domain Admins ou délégation OU). `-Restart` redémarre automatiquement après jonction. Alternative : section `<Identification>` dans unattend.xml.

```powershell
$Cred = Get-Credential CONTOSO\admin
Add-Computer -DomainName contoso.local -Credential $Cred -Restart -Force
```

**Cas d'usage** : Script post-déploiement qui joint automatiquement 100 PCs au domaine après installation OS.

**Question 12 : A) DISM /Add-Driver avec image montée**

**Explication** : Monter l'image avec `/Mount-Wim`, puis utiliser `/Add-Driver /Driver:C:\Drivers /Recurse` pour scanner tous les sous-dossiers et injecter les .inf détectés. `/ForceUnsigned` force l'ajout de drivers non signés. Commit pour sauvegarder.

```powershell
Dism /Image:C:\Mount /Add-Driver /Driver:C:\Drivers\HP /Recurse
Dism /Image:C:\Mount /Get-Drivers  # Vérifier injection
```

**Cas d'usage** : Intégrer drivers réseau, chipset et GPU Dell dans une image avant déploiement sur parc homogène.

**Question 13 : A) OS léger pour déploiement et dépannage**

**Explication** : WinPE (Windows Preinstallation Environment) est un mini-Windows bootable (500 Mo) basé sur le noyau NT. Utilisé pour capturer/appliquer des images, exécuter DISM, partitionner des disques ou dépanner un OS corrompu. Chargé en RAM, ne persiste pas après reboot.

```powershell
# Créer WinPE personnalisé
copype amd64 C:\WinPE
Dism /Mount-Wim /WimFile:C:\WinPE\media\sources\boot.wim /Index:1 /MountDir:C:\Mount
```

**Cas d'usage** : Booter sur une clé USB WinPE pour capturer l'image d'un PC maître avec DISM.

**Question 14 : B) Multicast économise bande passante (1 flux pour tous)**

**Explication** : En unicast, le serveur WDS envoie l'image séparément à chaque client : 100 clients × 5 Go = 500 Go trafic réseau. En multicast, un seul flux partagé : 1 × 5 Go = 5 Go trafic total. Gain critique sur liens <10 Gbps.

| Scénario | Unicast | Multicast |
|----------|---------|----------|
| **10 PCs × 5 Go** | 50 Go | 5 Go |
| **100 PCs × 5 Go** | 500 Go | 5 Go |

**Cas d'usage** : Multicast obligatoire pour déployer 200 PCs simultanément sur un réseau 1 Gbps (éviter saturation).

**Question 15 : B) Orchestration complète (OS, apps, drivers, config)**

**Explication** : Une Task Sequence MDT est une liste ordonnée d'actions : partitionner, appliquer OS, injecter drivers, installer apps, joindre domaine, exécuter scripts. Supporte conditions (WMI queries), variables (%Model%) et groupes pour personnalisation par modèle PC.

| Étape | Action |
|-------|--------|
| **1. Partition** | Format C:, créer partitions |
| **2. OS** | Appliquer install.wim |
| **3. Drivers** | Injection auto par modèle |
| **4. Apps** | Chrome, Office, 7-Zip |
| **5. Domain** | Join + rename |

**Cas d'usage** : Task Sequence qui déploie Win11 + drivers Dell + Office 365 + join domaine en 45 min sans interaction.

**Question 16 : A) Gestionnaire de paquets CLI pour Windows**

**Explication** : Chocolatey installe logiciels via ligne de commande (comme apt/yum Linux). Syntaxe : `choco install <package>`. Paramètre `-y` pour accepter automatiquement. Utile en scripts post-déploiement pour installer des apps standardisées sans intervention.

```powershell
choco install googlechrome firefox 7zip adobereader -y
choco upgrade all -y  # Tout mettre à jour
```

**Cas d'usage** : Script MDT qui installe automatiquement Chrome, 7-Zip, VLC sur tous les nouveaux PCs.

**Question 17 : B) Format MSI uniquement (Windows Installer)**

**Explication** : GPO Software Installation (Computer Configuration\Software Settings) déploie uniquement les packages .MSI. Les .EXE doivent être repackagés en MSI ou déployés via script. Mode Published (utilisateur) ou Assigned (forcé au boot/logon).

```
# Structure partage
\\SRV01\Packages$\7-Zip\7z.msi
\\SRV01\Packages$\Chrome\chrome.msi

GPO : Assigned → Installe au boot PC
```

**Cas d'usage** : GPO qui déploie 7-Zip.msi sur tous les PCs du domaine au prochain redémarrage.

**Question 18 : A) DISM /Export-Image (recompression, suppression delta)**

**Explication** : `/Export-Image` reconstruit le WIM en éliminant les fichiers supprimés et recompressant avec LZX maximum. Une image modifiée 10 fois accumule des deltas : export nettoie et réduit la taille de 20-40%. Améliore aussi les performances de déploiement.

```powershell
Dism /Export-Image /SourceImageFile:install.wim /SourceIndex:1 ^
     /DestinationImageFile:install_optimized.wim /Compress:max
```

**Cas d'usage** : Optimiser un install.wim de 8 Go devenu 12 Go après 15 modifications DISM pour revenir à 7 Go.

**Question 19 : B) Facilite gestion/support et application GPO**

**Explication** : Postes standardisés (même OS, apps, config) simplifient le troubleshooting (problèmes reproductibles), l'inventaire (moins de variantes) et l'application GPO (pas de cas particuliers). Réduit les coûts de support et formation.

| Avantage | Impact |
|----------|--------|
| **Support** | Scripts universels |
| **GPO** | Application homogène |
| **Formation** | Une procédure pour tous |
| **Inventaire** | 2 images au lieu de 20 |

**Cas d'usage** : Entreprise 500 PCs avec 2 images (Bureau et Laptop) au lieu de 50 configurations différentes.

**Question 20 : A) Sysprep → Capture WinPE → Déploiement WDS/MDT**

**Explication** : Workflow complet : 1) Configurer PC maître (OS + apps), 2) Sysprep /generalize, 3) Booter en WinPE, 4) Capturer avec DISM, 5) Importer dans WDS/MDT, 6) Déployer via PXE. Le cycle itératif permet de mettre à jour l'image en recapturant le maître.

```
PC Maître → Sysprep → Boot WinPE → DISM /Capture → install.wim → WDS → PXE Deploy
```

**Cas d'usage** : Créer image Win11 Pro + Office + drivers Dell, puis déployer sur 200 PC identiques en 1h.

---

## RÉPONSES : 1B, 2A, 3A, 4B, 5B, 6B, 7A, 8B, 9A, 10A, 11B, 12A, 13A, 14B, 15B, 16A, 17B, 18A, 19B, 20A

**Note /20** : Score / 1

---

**WORKFLOW MÉMO** :
```
1. Sysprep /generalize
2. Boot PXE (DHCP 66/67)
3. WDS/MDT déploie
4. Drivers injectés
5. Apps installées
6. Join domaine auto
7. Activation KMS
```
