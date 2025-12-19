# QCM SAUVEGARDES - CCP8

**Certification TSSR** - CCP8 : Sauvegardes et restaurations  
**20 questions essentielles**

---

### Q1 : Types sauvegardes
Quelle sauvegarde copie uniquement fichiers modifiés depuis dernière complète ?
- [ ]  A) Complète
- [ ] B) Différentielle 
- [ ] C) Incrémentale
- [ ] D) Synthétique

---

### Q2 : Restauration rapide
Quel type nécessite MOINS de bandes pour restaurer ?
- [ ] A) Complète + Incrémentales  (si dernière incrémentale récente)
- [ ] B) Complète + Différentielle 
- [ ] C) Incrémentale seule
- [ ] D) Différentielle seule

---

### Q3 : Règle 3-2-1
Que signifie règle sauvegarde 3-2-1 ?
- [ ] A) 3 copies, 2 supports différents, 1 hors site 
- [ ] B) 3 serveurs, 2 datacenters, 1 cloud
- [ ] C) 3 jours, 2 semaines, 1 mois
- [ ] D) 3 bandes, 2 disques, 1 cloud

---

### Q4 : RPO (Recovery Point Objective)
RPO = 4h signifie ?
- [ ] A) Restauration en 4h max
- [ ] B) Perte données acceptable = 4h max 
- [ ] C) Sauvegarde toutes les 4h
- [ ] D) Rétention 4h

---

### Q5 : Veeam Backup
Quelle technologie Veeam utilise pour sauvegardes incrémentielles rapides ?
- [ ] A) Snapshots
- [ ] B) Changed Block Tracking (CBT) 
- [ ] C) Deduplication
- [ ] D) Compression

---

### Q6 : Snapshot vs Backup
Différence principale snapshot/sauvegarde ?
- [ ] A) Snapshot = instantané même stockage, Backup = copie séparée 
- [ ] B) Aucune différence
- [ ] C) Snapshot permanent, Backup temporaire
- [ ] D) Snapshot lent, Backup rapide

---

### Q7 : Tar Linux
Commande créer archive compressée ?
- [ ] A) `tar -xzf backup.tar.gz /data`
- [ ] B) `tar -czf backup.tar.gz /data` 
- [ ] C) `tar -tzf backup.tar.gz /data`
- [ ] D) `tar -rzf backup.tar.gz /data`

---

### Q8 : Windows Server Backup
Commande PowerShell sauvegarde complète vers E: ?
- [ ] A) `Start-Backup -Target E:`
- [ ] B) `wbadmin start backup -backupTarget:E: -include:C: -allCritical` 
- [ ] C) `Backup-Volume C: -Destination E:`
- [ ] D) `New-Backup -Path E: -Source C:`

---

### Q9 : Baie NAS
Avantage principal NAS pour sauvegardes ?
- [ ] A) Performance maximale
- [ ] B) Stockage réseau centralisé accessible 
- [ ] C) Chiffrement matériel
- [ ] D) Coût faible

---

### Q10 : Bande LTO
LTO-7 capacité native ?
- [ ] A) 1.5 TB
- [ ] B) 3 TB
- [ ] C) 6 TB 
- [ ] D) 12 TB

---

### Q11 : Rsync Linux
Que fait `rsync -av /source/ /backup/` ?
- [ ] A) Copie complète écrasant destination
- [ ] B) Synchronisation incrémentale préservant attributs 
- [ ] C) Compression archives
- [ ] D) Suppression fichiers source

---

### Q12 : Deduplication
Quel gain typique déduplication sauvegardes ?
- [ ] A) 10-20%
- [ ] B) 30-50% 
- [ ] C) 80-90%
- [ ] D) 5%

---

### Q13 : Veeam réplication
Différence Backup / Replication Veeam ?
- [ ] A) Backup = fichiers, Replication = VMs complètes
- [ ] B) Backup = archive, Replication = VM prête démarrage 
- [ ] C) Aucune
- [ ] D) Backup quotidien, Replication temps réel

---

### Q14 : Restauration granulaire
Que permet restauration granulaire Exchange ?
- [ ] A) Restaurer base complète uniquement
- [ ] B) Restaurer emails individuels depuis backup 
- [ ] C) Restaurer serveur complet
- [ ] D) Restaurer config uniquement

---

### Q15 : GFS (Grandfather-Father-Son)
Schéma GFS typique ?
- [ ] A) Quotidien-Hebdomadaire-Mensuel 
- [ ] B) Complet-Différentiel-Incrémental
- [ ] C) Local-Distant-Cloud
- [ ] D) Primaire-Secondaire-Tertiaire

---

### Q16 : Test restauration
Fréquence recommandée tests restauration ?
- [ ] A) Annuelle
- [ ] B) Trimestrielle 
- [ ] C) Après chaque sauvegarde
- [ ] D) Jamais (automatique)

---

### Q17 : Chiffrement sauvegardes
Algorithme recommandé chiffrement ?
- [ ] A) DES
- [ ] B) MD5
- [ ] C) AES-256 
- [ ] D) Base64

---

### Q18 : Commande dd Linux
`dd if=/dev/sda of=/backup/disk.img bs=4M` fait quoi ?
- [ ] A) Formate disque
- [ ] B) Clone disque complet bit-à-bit 
- [ ] C) Sauvegarde fichiers uniquement
- [ ] D) Compresse partition

---

### Q19 : Veeam Instant VM Recovery
Avantage principal ?
- [ ] A) Compression maximale
- [ ] B) Démarrage VM directement depuis backup 
- [ ] C) Restauration plus rapide que normale
- [ ] D) Coût réduit

---

### Q20 : Rétention sauvegardes
Politique 7-4-12 signifie ?
- [ ] A) 7 quotidiennes, 4 hebdomadaires, 12 mensuelles 
- [ ] B) 7 jours, 4 mois, 12 ans
- [ ] C) 7 serveurs, 4 sites, 12 bandes
- [ ] D) 7h sauvegarde, 4h transfert, 12h rétention
