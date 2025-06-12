# Zammad Upgrade und Debian Dist-Upgrade Anleitung

## 🧭 Voraussetzungen

- **Aktuelles Backup** von Zammad und der Datenbank!
- **Root- oder Sudo-Rechte**
- Funktionierendes **PostgreSQL-Cluster** (z. B. von Version 13 auf 15)
- Aktuelles System auf **Debian 11**

---

## 🔄 Zammad-Upgrade und Debian-Dist-Upgrade

### Zammad-Instanz deaktivieren und einfrieren

```bash
systemctl disable zammad --now
apt-mark hold zammad
```

## 📝 Bearbeiten der APT-Quellen

```bash
sed -i 's/bullseye/bookworm/g' /etc/apt/sources.list
sed -i 's/debian 11/debian 12/g' /etc/apt/sources.list.d/zammad.list
```
❗ alte Version muss auch in Dateien korrekt sein (hier bullseye, debian 11)

🔎 Optional: Bearbeite die Dateien auch manuell mit einem Editor wie nano oder vim.


### System aktualisieren

```bash
apt update
apt upgrade
```

## 🛠️ PostgreSQL-Migration

### Aktuelle PostgreSQL-Version und Datenbankpfad prüfen

```bash
cat /opt/zammad/config/database.yml
pg_lsclusters
```
📌 Die Ausgabe hilft dir, die alte Version und den Port zu identifizieren, z. B.:

+ Alte Version: 13
+ Neue Version: 15
+ Datenbankname: zammad
+ Port aus database.yml zeigt, welches Cluster von Zammad verwendet wird, wenn keiner angegeben ist, wird Standardport 5432 genutzt.

### Entfernen unverwendeter Cluster (z. B. PostgreSQL 13) 
❗ Entferne nicht das Cluster, das den Port aus database.yml hält
```bash
pg_dropcluster --stop 15 main
```

### Upgrade des Clusters
 
```bash
pg_upgradecluster 13 main /var/lib/postgresql/15/main/
```
📌 tausche "13" mit der version die du vorher rausgefunden hast
 
🧹 Alte PostgreSQL-Version entfernen

```bash
apt autoremove
apt purge postgresql-13 postgresql-client-13
```

## 🔁 Neustart & Zammad wieder aktivieren

```bash
shutdown -r now
```
### Nach dem Neustart:
```bash
apt-mark unhold zammad
systemctl enable zammad
apt update
apt install zammad
```

### Rebuild ElasticSearch index
```bash
zammad run rake zammad:searchindex:rebuild
```

## ✅ Kontrolle
## 1. Clusterstatus prüfen
```bash
pg_lsclusters
```
## 2. Datenbankkonfiguration prüfen
```bash
cat /opt/zammad/config/database.yml
```
## 3. Debianversion prüfen
```bash
lsb_release -a
```
## 2. Zammadversion prüfen
```bash
dpkg -l | grep zammad
```
