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

### System aktualisieren

```bash
apt update
apt upgrade
```

## 📝 Alternative 1: Manuelles Bearbeiten der APT-Quellen

```bash
sed -i 's/bullseye/bookworm/g' /etc/apt/sources.list
sed -i 's/debian 11/debian 12/g' /etc/apt/sources.list.d/zammad.list
```
🔎 Optional: Bearbeite die Dateien auch manuell mit einem Editor wie nano oder vim.

## 🛠️ PostgreSQL-Migration

### Altes Cluster (z. B. PostgreSQL 13) stoppen

```bash
pg_dropcluster --stop 15 main
```
### Aktuelle PostgreSQL-Version und Datenbankpfad prüfen

```bash
cat /opt/zammad/config/database.yml
pg_lsclusters
```
📌 Die Ausgabe hilft dir, die alte Version und den Port zu identifizieren, z. B.:

+ Alte Version: 13
+ Neue Version: 15
+ Datenbankname: zammad

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

## ✅ Kontrolle
## 1. Clusterstatus prüfen
```bash
pg_lsclusters
```
## 2. Datenbankkonfiguration prüfen
```bash
cat /opt/zammad/config/database.yml
```
