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






























🧭 Voraussetzungen
Aktuelles Backup von Zammad und der Datenbank!

Root- oder Sudo-Rechte

Funktionierendes PostgreSQL-Cluster (z. B. von 13 auf 15)

Aktuelles System auf Debian 11

🔄 Zammad-Upgrade und Debian-Dist-Upgrade
1. Zammad-Instanz deaktivieren und einfrieren
bash
Kopieren
Bearbeiten
systemctl disable zammad --now
apt-mark hold zammad
2. System aktualisieren
bash
Kopieren
Bearbeiten
apt update
apt upgrade
📝 Alternative 1: Manuelles Bearbeiten der APT-Quellen
/etc/apt/sources.list
bash
Kopieren
Bearbeiten
sed -i 's/bullseye/bookworm/g' /etc/apt/sources.list
/etc/apt/sources.list.d/zammad.list
bash
Kopieren
Bearbeiten
sed -i 's/debian 11/debian 12/g' /etc/apt/sources.list.d/zammad.list
🔎 Optional: Bearbeite die Dateien auch manuell mit einem Editor wie nano oder vim.

3. Paketquellen neu einlesen und Upgrade durchführen
bash
Kopieren
Bearbeiten
apt clean
apt update
apt upgrade
apt dist-upgrade
🛠️ PostgreSQL-Migration
4. Altes Cluster (z. B. PostgreSQL 13) stoppen
bash
Kopieren
Bearbeiten
pg_dropcluster --stop 15 main
5. Aktuelle PostgreSQL-Version und Datenbankpfad prüfen
bash
Kopieren
Bearbeiten
cat /opt/zammad/config/database.yml
pg_lsclusters
📌 Die Ausgabe hilft dir, die alte Version und den Port zu identifizieren, z. B.:

Alte Version: 13

Neue Version: 15

Datenbankname: zammad

6. Upgrade des Clusters
bash
Kopieren
Bearbeiten
pg_upgradecluster 13 main /var/lib/postgresql/15/main/
🧹 Alte PostgreSQL-Version entfernen
bash
Kopieren
Bearbeiten
apt autoremove
apt purge postgresql-13 postgresql-client-13
🔁 Neustart & Zammad wieder aktivieren
bash
Kopieren
Bearbeiten
shutdown -r now
Nach dem Neustart:

bash
Kopieren
Bearbeiten
apt-mark unhold zammad
systemctl enable zammad
apt update
apt install zammad
✅ Kontrolle
7. Clusterstatus prüfen
bash
Kopieren
Bearbeiten
pg_lsclusters
Ausgabe-Beispiel:

css
Kopieren
Bearbeiten
Ver Cluster Port Status Owner    Data directory              Log file
13  main    5433 online postgres /var/lib/postgresql/13/main /var/log/postgresql/postgresql-13-main.log
15  main    5432 online postgres /var/lib/postgresql/15/main /var/log/postgresql/postgresql-15-main.log
8. Datenbankkonfiguration prüfen
bash
Kopieren
Bearbeiten
cat /opt/zammad/config/database.yml
Achte auf:

yaml
Kopieren
Bearbeiten
production:
  adapter: postgresql
  database: zammad
  username: zammad
  password: geheim
  host: localhost
  port: 5432
🏁 Fertig!
Deine Zammad-Instanz läuft nun auf einem aktualisierten Debian 12-System mit einer migrierten PostgreSQL-Version. Bei Problemen mit Zammad selbst lohnt sich ein Blick in:

bash
Kopieren
Bearbeiten
journalctl -u zammad
