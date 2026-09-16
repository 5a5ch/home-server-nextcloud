# Aufbau einer automatisierten Backupstrategie für Nextcloud

**Datum:** 16. September 2026  
**Umgebung:** Ubuntu Server, Docker Compose, Nextcloud, MariaDB, externe USB-Festplatte, CIFS-Netzwerkfreigabe, systemd, Uptime Kuma, Telegram

## 1. Ausgangssituation

Am vorherigen Arbeitstag waren Nextcloud und Collabora erfolgreich auf eine neue, getrennte Container- und Domainstruktur migriert worden. Zusätzlich war die lokale Speicherstruktur der über USB angeschlossenen Festplatte bereinigt worden:

```text
/mnt/nextcloud/
├── data/
└── system-image/
```

Dabei gilt:

- `/mnt/nextcloud/data` enthält die produktiven Nextcloud-Dateidaten.
- `/mnt/nextcloud/system-image` enthält komprimierte Abbilder der Ubuntu-Systemplatte.
- `/mnt/bkp_os_server` ist eine dauerhaft konfigurierte CIFS-Freigabe auf einem Hauptrechner im lokalen Netzwerk.
- Der Hauptrechner ist nicht durchgehend eingeschaltet.

Ein erster konsistenter MariaDB-Dump und ein erstes Konfigurationsarchiv waren bereits erfolgreich getestet worden. Das zugehörige Skript lag unter:

```text
/usr/local/sbin/nextcloud-local-backup
```

Die Testbackups wurden zunächst auf der Ubuntu-Systemplatte unter folgendem Pfad abgelegt:

```text
/home/USER/nextcloud-backups
```

Ziel des Arbeitstags war der Aufbau einer automatisierten, mehrstufigen Backupstrategie mit lokaler Sicherung auf der USB-Festplatte, einer zusätzlichen Netzwerkkopie und einer Überwachung über Uptime Kuma und Telegram.

---

## 2. Ziel der Backupstrategie

Die Backupstrategie wurde in zwei voneinander getrennte Stufen aufgeteilt.

### 2.1 Stufe 1: Lokales Backup auf die USB-Festplatte

Das lokale Backup sollte täglich folgende Inhalte sichern:

- konsistenter Dump der Nextcloud-MariaDB-Datenbank
- Docker-Compose-Dateien
- Nginx-Konfiguration
- Collabora-Konfiguration
- Nextclouds `config.php`
- Let's-Encrypt-Konfiguration
- DDNS- und WOPI-Automatisierung
- systemd-Dienste und Timer
- das Backup-Skript selbst

Die produktiven Nextcloud-Dateidaten liegen bereits auf der USB-Festplatte und werden daher nicht täglich nochmals vollständig dupliziert.

### 2.2 Stufe 2: Netzwerkkopie auf den Hauptrechner

Die gesamte USB-Festplatte sollte zweimal pro Woche auf eine CIFS-Freigabe des Hauptrechners kopiert werden:

```text
Montag und Donnerstag um 18:00 Uhr
```

Dabei sollte das Skript vor Beginn prüfen:

1. ob die lokale USB-Quellplatte korrekt eingehängt ist
2. ob der Hauptrechner beziehungsweise die Netzwerkfreigabe antwortet
3. ob das Ziel tatsächlich als CIFS-Dateisystem eingebunden ist
4. ob die Netzwerkfreigabe beschreibbar ist

Wenn der Hauptrechner ausgeschaltet oder die Freigabe nicht verfügbar ist, darf dies den Ubuntu-Server oder Nextcloud nicht beeinträchtigen. Nur der Backupjob soll kontrolliert fehlschlagen und eine Benachrichtigung auslösen.

---

## 3. Aufbewahrungsstrategie

Zunächst stand die Frage im Raum, nur die letzten drei Sicherungen zu behalten. Dieser Ansatz wurde verworfen, weil ein Fehler möglicherweise erst einige Tage später erkannt wird. In diesem Fall könnten bereits alle drei Sicherungen denselben fehlerhaften Zustand enthalten.

Festgelegt wurde folgende Rotation:

```text
Tägliche Sicherungen:
14 Tage aufbewahren

Monatssicherungen:
erste erfolgreiche Sicherung eines Monats zusätzlich archivieren

Monatssicherungen:
24 Monate aufbewahren
```

Diese Kombination ermöglicht:

- eine feine tägliche Wiederherstellung für die letzten zwei Wochen
- einen langfristigen monatlichen Rückgriff über zwei Jahre
- einen sehr überschaubaren Speicherbedarf

Zum Zeitpunkt der Einrichtung hatte ein vollständiger Sicherungssatz ungefähr folgende Größe:

```text
Komprimierter MariaDB-Dump: etwa 12 MB
Konfigurationsarchiv:        etwa 127 KB
```

Bei unveränderter Größenordnung ergeben sich ungefähr:

```text
14 tägliche Sicherungen:  etwa 170 MB
24 Monatssicherungen:     etwa 290 MB
Gesamt:                   etwa 460 MB
```

Selbst bei deutlich wachsender Datenbank bleibt der Speicherbedarf im Vergleich zu den produktiven Dateidaten gering.

---

## 4. Backupverzeichnis auf der USB-Festplatte

Da das produktive Nextcloud-Datenverzeichnis inzwischen unter `/mnt/nextcloud/data` liegt, konnte daneben ein separater Backupbereich angelegt werden.

Zunächst wurde folgende Struktur erstellt:

```bash
sudo mkdir -p /mnt/nextcloud/backups/local
sudo chown root:root /mnt/nextcloud/backups
sudo chown root:root /mnt/nextcloud/backups/local
sudo chmod 700 /mnt/nextcloud/backups
sudo chmod 700 /mnt/nextcloud/backups/local
```

Die Berechtigungen wurden kontrolliert:

```text
drwx------ root root
```

Das vorhandene Backup-Skript wurde vorübergehend auf diesen neuen Zielpfad umgestellt und erfolgreich getestet.

Ein Testlauf erzeugte:

```text
/mnt/nextcloud/backups/local/nextcloud-db-2026-09-16_07-00-33.sql.gz
/mnt/nextcloud/backups/local/server-config-2026-09-16_07-00-33.tar.gz
```

Die Dateirechte lauteten:

```text
-rw------- root root
```

Damit waren die Sicherungen ausschließlich für `root` lesbar und beschreibbar.

---

## 5. Trennung in tägliche und monatliche Sicherungen

Nach Festlegung der Aufbewahrungsstrategie wurde die Struktur angepasst:

```bash
sudo mv /mnt/nextcloud/backups/local /mnt/nextcloud/backups/daily
sudo mkdir -p /mnt/nextcloud/backups/monthly
sudo chown root:root /mnt/nextcloud/backups/monthly
sudo chmod 700 /mnt/nextcloud/backups/monthly
```

Die resultierende Struktur war:

```text
/mnt/nextcloud/backups/
├── daily/
│   ├── nextcloud-db-....sql.gz
│   └── server-config-....tar.gz
└── monthly/
```

Vor der Erweiterung wurde das funktionierende Backup-Skript gesichert:

```bash
sudo cp /usr/local/sbin/nextcloud-local-backup \
  /usr/local/sbin/nextcloud-local-backup.bak
```

---

## 6. Erweiterung des lokalen Backup-Skripts

Das Skript wurde um folgende Funktionen erweitert:

- getrennte Verzeichnisse `daily` und `monthly`
- Schreiben in temporäre Dateien
- Löschen unvollständiger Dateien bei Fehlern
- Integritätsprüfung des komprimierten Datenbank-Dumps
- Integritätsprüfung des Konfigurationsarchivs
- Sicherung von Nextclouds `config.php`
- zusätzliche Sicherung des Backup-Skripts selbst
- Erstellung der ersten Monatssicherung
- Aufbewahrung täglicher Sicherungen für 14 Tage
- Begrenzung der Monatssicherungen auf 24 Monatsordner
- atomarere Erstellung eines Monatsordners über einen temporären Ordner

Das Skript verwendet:

```bash
set -euo pipefail
umask 077
```

Dadurch gilt:

- das Skript beendet sich bei nicht behandelten Fehlern
- nicht gesetzte Variablen werden als Fehler behandelt
- Fehler innerhalb von Pipelines werden erkannt
- neu erzeugte Dateien erhalten restriktive Berechtigungen

### 6.1 Konsistenter MariaDB-Dump

Die aktuell verwendeten Datenbankzugangsdaten werden aus der laufenden Nextcloud-Konfiguration gelesen, ohne sie auszugeben:

```bash
DB_USER="$(
    /usr/bin/docker exec nextcloud-app php -r \
    'include "/var/www/html/config/config.php"; echo $CONFIG["dbuser"];'
)"
```

Entsprechend werden auch Datenbankpasswort und Datenbankname gelesen.

Der eigentliche Dump wird mit folgenden Optionen erzeugt:

```text
--single-transaction
--quick
--routines
--events
--triggers
```

Der Dump wird direkt komprimiert und zunächst als temporäre Datei geschrieben:

```bash
/usr/bin/docker exec \
    -e MYSQL_PWD="$DB_PASS" \
    nextcloud-db \
    mariadb-dump \
    --host=127.0.0.1 \
    --user="$DB_USER" \
    --single-transaction \
    --quick \
    --routines \
    --events \
    --triggers \
    "$DB_NAME" \
    | /usr/bin/gzip > "$DB_TEMP"
```

Vor der endgültigen Umbenennung wird die Datei geprüft:

```bash
/usr/bin/gzip -t "$DB_TEMP"
test -s "$DB_TEMP"
```

### 6.2 Sicherung der Konfiguration

Nextclouds `config.php` liegt innerhalb des Containers und wird deshalb zunächst in ein temporäres Verzeichnis kopiert:

```bash
TEMP_DIR="$(/usr/bin/mktemp -d)"

/usr/bin/docker cp \
    nextcloud-app:/var/www/html/config/config.php \
    "$TEMP_DIR/config.php"
```

Das Konfigurationsarchiv enthält unter anderem:

```text
/home/USER/nextcloud/docker-compose.yml
/home/USER/proxy/docker-compose.yml
/home/USER/proxy/nginx.conf
/home/USER/collabora/docker-compose.yml
/etc/letsencrypt
/etc/systemd/system/ionos-ddns.service
/etc/systemd/system/ionos-ddns.timer
/usr/local/sbin/ionos-ddns-update
/usr/local/sbin/nextcloud-local-backup
/etc/ionos-ddns-url
Nextcloud config.php
```

Das Archiv wird vor der endgültigen Ablage geprüft:

```bash
/usr/bin/tar -tzf "$CONFIG_TEMP" >/dev/null
test -s "$CONFIG_TEMP"
```

### 6.3 Monatssicherung

Wenn für den aktuellen Monat noch kein Monatsordner existiert, wird der erste erfolgreiche Tagessatz zusätzlich archiviert.

Beispiel:

```text
/mnt/nextcloud/backups/monthly/2026-09/
├── nextcloud-db-2026-09-16_07-24-20.sql.gz
└── server-config-2026-09-16_07-24-20.tar.gz
```

Der Monatsordner wird zunächst als versteckter temporärer Ordner angelegt und erst nach erfolgreichem Kopieren beider Dateien umbenannt. Dadurch bleibt bei einem Abbruch kein scheinbar vollständiger, tatsächlich aber leerer Monatsstand zurück.

### 6.4 Rotation

Tägliche Sicherungsdateien, die älter als 14 Tage sind, werden automatisch gelöscht:

```bash
/usr/bin/find "$DAILY_DIR" \
    -maxdepth 1 \
    -type f \
    \( -name 'nextcloud-db-*.sql.gz' \
       -o -name 'server-config-*.tar.gz' \) \
    -mtime +13 \
    -delete
```

Von den Monatsordnern werden nur die 24 neuesten behalten.

---

## 7. Fehler beim ersten Rotationslauf

Beim ersten Testlauf wurden Datenbank-Dump, Konfigurationsarchiv und Monatssicherung erfolgreich erstellt. Danach brach das Skript mit folgendem Fehler ab:

```text
/usr/bin/find: invalid expression; I was expecting to find a ')' somewhere but did not see one.
```

Der Ergebniscode war:

```text
1
```

Die Ursache lag nicht in der Backup-Logik. Beim Einfügen des Skripts war der Inhalt mitten im `find`-Ausdruck abgeschnitten worden. Das Skript endete bei:

```bash
\( -name 'nextcloud-db-*.sql.gz' \
```

Dadurch fehlten:

- der zweite Dateiname
- die schließende Klammer
- `-mtime`
- `-delete`
- die gesamte Monatsrotation
- die abschließenden Erfolgsmeldungen

Der fehlende Abschnitt wurde ergänzt und das reparierte Dateiende kontrolliert.

Die Syntaxprüfung ergab anschließend:

```bash
sudo bash -n /usr/local/sbin/nextcloud-local-backup
echo "Status: $?"
```

Ergebnis:

```text
Status: 0
```

Wichtig: Der fehlerhafte Rotationslauf hatte keine gültigen Sicherungen gelöscht. Datenbank-Dump, Konfigurationsarchiv und Monatssicherung waren bereits erfolgreich erstellt worden.

---

## 8. Automatisierung des lokalen Backups mit systemd

Für das lokale Backup wurde ein eigener systemd-Dienst angelegt:

```text
/etc/systemd/system/nextcloud-local-backup.service
```

Inhalt:

```ini
[Unit]
Description=Lokales Nextcloud-Datenbank- und Konfigurationsbackup
After=docker.service
Requires=docker.service
RequiresMountsFor=/mnt/nextcloud
OnFailure=nextcloud-local-backup-failure.service

[Service]
Type=oneshot
ExecStart=/usr/local/sbin/nextcloud-local-backup
ExecStartPost=-/usr/local/sbin/nextcloud-backup-notify up "Lokales Nextcloud-Backup erfolgreich"
```

Die Option:

```ini
RequiresMountsFor=/mnt/nextcloud
```

stellt sicher, dass systemd den erforderlichen Mount berücksichtigt.

Das Minuszeichen vor dem Benachrichtigungsbefehl ist beabsichtigt:

```ini
ExecStartPost=-/usr/local/sbin/nextcloud-backup-notify ...
```

Wenn Uptime Kuma nicht erreichbar ist, soll ein ansonsten erfolgreiches Backup nicht als fehlgeschlagen gelten.

---

## 9. Überwachung mit Uptime Kuma und Telegram

### 9.1 Ziel

Die Überwachung sollte melden:

- fehlgeschlagenes lokales Backup
- erstes erfolgreiches Backup nach einem Fehler
- vollständig ausgebliebenes Backup

Auf eine Telegram-Meldung nach jedem erfolgreichen Lauf wurde bewusst verzichtet. Bei normalem Betrieb bleibt der Kuma-Monitor grün und erhält einen neuen Heartbeat. Telegram wird nur bei einem Zustandswechsel ausgelöst.

### 9.2 Push-Monitor

In Uptime Kuma wurde ein neuer Push-Monitor angelegt:

```text
Nextcloud lokales Backup
```

Die geheime Push-URL wurde geschützt gespeichert:

```text
/etc/nextcloud-backup-kuma-url
```

Dateirechte:

```bash
sudo chown root:root /etc/nextcloud-backup-kuma-url
sudo chmod 600 /etc/nextcloud-backup-kuma-url
```

### 9.3 Benachrichtigungshelfer

Der Benachrichtigungshelfer liegt unter:

```text
/usr/local/sbin/nextcloud-backup-notify
```

Er unterstützt die beiden Zustände:

```text
up
down
```

Die geheime Kuma-URL wird aus der geschützten Datei gelesen. Status und Nachricht werden URL-kodiert übertragen.

### 9.4 Funktionstest

Ein erfolgreicher Test-Push setzte den Monitor auf grün. Zunächst wurde keine Telegram-Nachricht ausgelöst, weil kein relevanter Statuswechsel stattfand.

Danach wurde ein Fehlerzustand getestet:

```bash
sudo /usr/local/sbin/nextcloud-backup-notify \
  down \
  "Test: Nextcloud-Backup fehlgeschlagen"
```

Anschließend wurde der Monitor wieder auf grün gesetzt:

```bash
sudo /usr/local/sbin/nextcloud-backup-notify \
  up \
  "Test beendet: Nextcloud-Backup-Monitor wieder bereit"
```

Telegram meldete beide Statuswechsel:

```text
[Down] Test: Nextcloud-Backup fehlgeschlagen
[Up] Test beendet: Nextcloud-Backup-Monitor wieder bereit
```

Damit waren folgende Komponenten erfolgreich geprüft:

- Kuma-Push-URL
- Statuswechsel in Uptime Kuma
- Telegram-Anbindung
- Fehleralarm
- Entwarnung

### 9.5 Fehlerdienst

Für fehlgeschlagene lokale Backups wurde ein eigener systemd-Dienst angelegt:

```text
/etc/systemd/system/nextcloud-local-backup-failure.service
```

Inhalt:

```ini
[Unit]
Description=Kuma-Fehlermeldung für lokales Nextcloud-Backup

[Service]
Type=oneshot
ExecStart=-/usr/local/sbin/nextcloud-backup-notify down "Lokales Nextcloud-Backup fehlgeschlagen"
```

Der Hauptdienst verweist über `OnFailure` auf diesen Fehlerdienst.

### 9.6 Prüfung der Unit-Dateien

Die systemd-Dateien wurden geprüft:

```bash
sudo systemd-analyze verify \
  /etc/systemd/system/nextcloud-local-backup.service \
  /etc/systemd/system/nextcloud-local-backup-failure.service
```

Es erschien lediglich eine Warnung zu einer nicht unterstützten Option in `snapd.service`. Diese Warnung betraf nicht die neu angelegten Backup-Dienste.

### 9.7 Erfolgreicher Systemd-Testlauf

Der Dienst wurde manuell über systemd gestartet:

```bash
sudo systemctl start nextcloud-local-backup.service
```

Der Status zeigte:

```text
Lokales Backup erfolgreich
Deactivated successfully
Finished Lokales Nextcloud-Datenbank- und Konfigurationsbackup
```

`inactive (dead)` ist bei einem erfolgreich abgeschlossenen `oneshot`-Dienst normal.

Da der Kuma-Monitor bereits grün war, wurde keine weitere Telegram-Nachricht ausgelöst. Der Push aktualisierte lediglich den erfolgreichen Heartbeat.

---

## 10. Täglicher Backup-Timer

Für die tägliche Ausführung wurde folgender Timer angelegt:

```text
/etc/systemd/system/nextcloud-local-backup.timer
```

Inhalt:

```ini
[Unit]
Description=Tägliches lokales Nextcloud-Backup

[Timer]
OnCalendar=*-*-* 03:30:00
Persistent=true
RandomizedDelaySec=10m

[Install]
WantedBy=timers.target
```

Aktivierung:

```bash
sudo systemctl daemon-reload
sudo systemctl enable --now nextcloud-local-backup.timer
```

Die Kontrolle erfolgte mit:

```bash
systemctl list-timers nextcloud-local-backup.timer --no-pager
```

Der nächste Lauf wurde korrekt zwischen 03:30 und 03:40 Uhr UTC geplant. Aufgrund der lokalen Sommerzeit entspricht dies einer späteren lokalen Uhrzeit.

`Persistent=true` bedeutet, dass ein während eines ausgeschalteten Servers verpasster Lauf nach dem nächsten Start nachgeholt wird.

---

## 11. Aufbau des Netzwerkbackups

### 11.1 Separater Kuma-Monitor

Für das Netzwerkbackup wurde ein eigener Push-Monitor angelegt:

```text
Nextcloud Netzwerk-Backup
```

Der Monitor wurde getrennt vom lokalen Backup angelegt, damit beide Sicherungsstufen unabhängig überwacht werden können.

Die geheime Push-URL wurde gespeichert unter:

```text
/etc/nextcloud-network-backup-kuma-url
```

Dateirechte:

```text
-rw------- root:root
```

Als Heartbeat-Zeitfenster wurden fünf Tage gewählt. Zwischen Donnerstag und Montag liegen maximal vier Tage. Der zusätzliche Tag bietet Reserve, ohne ein dauerhaft ausgebliebenes Backup lange zu verbergen.

### 11.2 Separater Benachrichtigungshelfer

Der vorhandene Benachrichtigungshelfer wurde kopiert:

```bash
sudo cp /usr/local/sbin/nextcloud-backup-notify \
  /usr/local/sbin/nextcloud-network-backup-notify
```

In der Kopie wurde nur die URL-Datei geändert:

```text
/etc/nextcloud-network-backup-kuma-url
```

Die Syntaxprüfung war erfolgreich:

```text
Status: 0
```

---

## 12. Netzwerk-Backup-Skript

Das Netzwerk-Backup-Skript wurde angelegt unter:

```text
/usr/local/sbin/nextcloud-network-backup
```

Die wesentlichen Pfade lauten anonymisiert:

```bash
SOURCE="/mnt/nextcloud/"
MOUNTPOINT="/mnt/bkp_os_server"
DESTINATION="$MOUNTPOINT/nextcloud/"
```

### 12.1 Prüfung der lokalen Quelle

Das Skript prüft, ob die lokale USB-Platte als `ext4` eingehängt ist:

```bash
/usr/bin/findmnt -n -T "$SOURCE" -o FSTYPE \
    | /usr/bin/grep -qx 'ext4'
```

Damit wird verhindert, dass bei einer fehlenden USB-Platte versehentlich aus einem leeren lokalen Mountverzeichnis gesichert wird.

### 12.2 Prüfung der Netzwerkfreigabe

Der Zugriff auf die Freigabe wird zeitlich begrenzt geprüft:

```bash
/usr/bin/timeout 15 /usr/bin/stat "$MOUNTPOINT"
```

Dadurch soll der Backupjob bei einem ausgeschalteten Hauptrechner nicht unbegrenzt hängen.

Danach wird sichergestellt, dass das Ziel tatsächlich als `cifs` eingebunden ist:

```bash
/usr/bin/findmnt -n -T "$MOUNTPOINT" -o FSTYPE \
    | /usr/bin/grep -qx 'cifs'
```

### 12.3 Schreibtest

Vor dem eigentlichen Backup legt das Skript kurz eine leere Testdatei auf der Netzwerkfreigabe an und löscht diese sofort wieder:

```bash
WRITE_TEST="$MOUNTPOINT/.nextcloud-backup-write-test-$$"
```

Damit wird geprüft, ob die Freigabe tatsächlich beschreibbar ist.

### 12.4 Rsync

Der eigentliche Kopiervorgang nutzt:

```bash
/usr/bin/rsync \
    -aHAX \
    --numeric-ids \
    --partial \
    --info=progress2 \
    --human-readable \
    --stats \
    "$SOURCE" \
    "$DESTINATION"
```

Bedeutung der wichtigsten Optionen:

- `-a`: Archivmodus
- `-H`: Hardlinks erhalten
- `-A`: ACLs erhalten
- `-X`: erweiterte Attribute erhalten
- `--numeric-ids`: numerische Eigentümer- und Gruppenkennungen verwenden
- `--partial`: teilweise übertragene Dateien bei Abbruch behalten
- `--info=progress2`: Gesamtfortschritt anzeigen
- `--human-readable`: Größen lesbar darstellen
- `--stats`: Abschlussstatistik ausgeben

Der gesamte Lauf ist auf maximal zwölf Stunden begrenzt:

```bash
/usr/bin/timeout --signal=TERM 12h
```

Nach einem erfolgreichen Lauf wird der Netzwerkbackup-Monitor auf `UP` gesetzt.

### 12.5 Fehlerbehandlung

Bei folgenden Fällen wird ein `DOWN`-Status an den Netzwerkbackup-Monitor gesendet:

- USB-Quellplatte fehlt
- Hauptrechner oder Freigabe antwortet nicht
- CIFS-Freigabe ist nicht eingehängt
- Netzwerkfreigabe ist nicht beschreibbar
- rsync schlägt fehl

Ein Benachrichtigungsfehler selbst wird ignoriert, damit die Meldetechnik nicht den eigentlichen Backupstatus verfälscht.

---

## 13. Fehler durch ein versehentlich eingefügtes Backtick-Zeichen

Bei der ersten Syntaxprüfung des Netzwerk-Backup-Skripts erschien:

```text
unexpected EOF while looking for matching `
syntax error: unexpected end of file
Status: 2
```

Ursache war ein versehentlich eingefügtes einzelnes Backtick-Zeichen am Dateiende. Dieses Zeichen wurde mit `grep` lokalisiert und entfernt.

Die anschließende Syntaxprüfung ergab:

```text
Status: 0
```

Das Skript war zu diesem Zeitpunkt noch nicht ausgeführt worden. Es wurden daher keine Dateien kopiert oder verändert.

---

## 14. Kontrollierter Test der Netzwerkbedingungen

Vor dem ersten vollständigen Lauf wurden ausschließlich die Vorbedingungen geprüft. Es wurden keine Nextcloud-Daten kopiert.

Der Test bestätigte:

```text
OK: USB-Quellplatte ist als ext4 eingehängt.
OK: Netzwerkfreigabe antwortet.
OK: Ziel ist tatsächlich als CIFS eingehängt.
OK: Netzwerkfreigabe ist beschreibbar.
```

Der Schreibtest erzeugte nur kurz eine leere Testdatei und entfernte diese sofort wieder.

Damit waren die Voraussetzungen für den ersten vollständigen Netzwerk-Backup-Lauf erfüllt.

---

## 15. Bereinigung des Netzwerkziels

Vor dem ersten vollständigen Lauf wurde entschieden, alte Sicherungen im vorgesehenen Nextcloud-Unterordner auf dem Hauptrechner zu entfernen. Ziel war ein sauberer Neuaufbau der Netzwerkkopie.

Dabei galt:

- Nur der Inhalt des vorgesehenen Nextcloud-Backup-Unterordners durfte entfernt werden.
- Andere Sicherungen auf derselben Freigabe durften nicht betroffen sein.
- Der Zielordner selbst konnte stehen bleiben, da das Skript den Ordner bei Bedarf ebenfalls anlegen kann.

Der erste Lauf nach dieser Bereinigung ist daher keine inkrementelle Aktualisierung, sondern eine vollständige Übertragung der USB-Platte mit einem Umfang von ungefähr 258 GB.

---

## 16. Verfügbarkeit von Nextcloud während der Sicherung

Nextcloud bleibt während der lokalen und der Netzwerk-Sicherung grundsätzlich erreichbar. Es wird kein Wartungsmodus aktiviert.

### Lokales Backup

Der MariaDB-Dump wird mit `--single-transaction` konsistent im laufenden Betrieb erzeugt. Die Konfigurationsdateien werden parallel archiviert.

### Netzwerkbackup

`rsync` liest die Dateien von der USB-Platte, stoppt aber keine Container. Benutzer können Nextcloud weiterhin verwenden.

Einschränkung: Eine Datei, die sich genau während der Übertragung ändert, kann auf dem Netzwerkziel vorübergehend einen nicht vollständig zeitgleichen Stand haben. Beim nächsten rsync-Lauf wird die geänderte Datei erneut übertragen.

Ein vollständig zeitgleicher Stand aller Dateidaten und der Datenbank wäre nur mit Wartungsmodus oder einem Dateisystem-Snapshot erreichbar. Gegen eine stundenlange Unterbrechung wurde bewusst entschieden.

---

## 17. Erster vollständiger Netzwerk-Backup-Lauf

Für den ersten vollständigen Lauf wurde die Fortschrittsanzeige `--info=progress2` in das Skript aufgenommen.

Der Start erfolgt über:

```bash
sudo /usr/local/sbin/nextcloud-network-backup
echo "Ergebniscode: $?"
```

Während des Laufs zeigt rsync unter anderem:

- bereits übertragene Datenmenge
- Gesamtfortschritt in Prozent
- Übertragungsrate
- ungefähre Restdauer

Zum Zeitpunkt der Erstellung dieser Dokumentation lief die erste vollständige Übertragung noch. Das endgültige Ergebnis, die Laufzeit und die Abschlussstatistik dürfen erst nach Abschluss ergänzt werden.

### Nach Abschluss zu ergänzen

```text
Startzeit:             noch einzutragen
Endzeit:               noch einzutragen
Laufzeit:              noch einzutragen
Übertragene Daten:     noch einzutragen
Übertragungsrate:      noch einzutragen
Ergebniscode:          noch einzutragen
Kuma-Status:           noch einzutragen
Telegram-Statuswechsel: noch einzutragen
```

---

## 18. Verworfene oder korrigierte Ansätze

### 18.1 Nur drei Sicherungsstände behalten

Verworfen, weil Fehler möglicherweise erst nach mehreren Tagen erkannt werden. Stattdessen werden 14 Tagesstände und 24 Monatsstände behalten.

### 18.2 Monatssicherung an einem festen Kalendertag

Ein separater Lauf exakt am ersten Tag des Monats wurde nicht verwendet. Falls der Server zu diesem Zeitpunkt ausgeschaltet wäre, könnte der Monatsstand fehlen.

Stattdessen wird die erste tatsächlich erfolgreiche Sicherung eines Monats zusätzlich archiviert.

### 18.3 Leerer Monatsordner als gültiger Sicherungsstand

Die erste Skriptfassung hätte den endgültigen Monatsordner vor dem Kopieren angelegt. Bei einem Abbruch hätte ein leerer Ordner die spätere Erstellung blockieren können.

Die korrigierte Fassung verwendet zunächst einen versteckten temporären Monatsordner und benennt diesen erst nach erfolgreicher Prüfung beider Dateien um.

### 18.4 Telegram-Nachricht bei jedem erfolgreichen Backup

Dieser Ansatz wurde nicht verwendet. Ein grüner Kuma-Monitor erhält bei jedem erfolgreichen Lauf einen Heartbeat, ohne jedes Mal Telegram auszulösen.

Telegram meldet stattdessen:

- Statuswechsel auf `DOWN`
- Statuswechsel zurück auf `UP`
- ausgebliebenen Heartbeat

Dadurch bleiben Benachrichtigungen relevant und werden nicht durch tägliche Erfolgsmeldungen entwertet.

### 18.5 Benachrichtigungsfehler als Backupfehler behandeln

Verworfen. Wenn Uptime Kuma vorübergehend nicht erreichbar ist, darf ein erfolgreiches Backup nicht als fehlgeschlagen gelten. Benachrichtigungsbefehle werden deshalb fehlertolerant ausgeführt.

### 18.6 Netzwerkbackup ohne Mountprüfung

Verworfen. Ohne Prüfung könnte rsync bei fehlender CIFS-Verbindung in ein lokales leeres Mountverzeichnis schreiben. Das Skript verlangt deshalb ausdrücklich den Dateisystemtyp `cifs`.

---

## 19. Technischer Zustand am Ende der dokumentierten Arbeiten

### Lokales Backup

- Backupziel auf USB-Platte eingerichtet
- täglicher Datenbank-Dump funktionsfähig
- Konfigurationsarchiv funktionsfähig
- Nextcloud `config.php` enthalten
- Integritätsprüfungen aktiv
- temporäre Dateien werden bei Fehlern bereinigt
- 14 tägliche Stände vorgesehen
- 24 Monatssicherungen vorgesehen
- systemd-Dienst eingerichtet
- täglicher systemd-Timer eingerichtet
- Uptime-Kuma-Überwachung eingerichtet
- Telegram-Fehleralarm und Entwarnung getestet

### Netzwerkbackup

- separates Skript eingerichtet
- separater Kuma-Monitor eingerichtet
- USB-Quellplatte geprüft
- CIFS-Ziel geprüft
- Schreibzugriff geprüft
- Fortschrittsanzeige aktiviert
- erster vollständiger Lauf gestartet
- Zeitplan Montag und Donnerstag noch einzurichten

---

## 20. Noch offene Aufgaben

Nach Abschluss des ersten vollständigen Netzwerkbackups stehen folgende Arbeiten aus:

 1. Ergebniscode und rsync-Abschlussstatistik prüfen
 2. Zielstruktur auf dem Hauptrechner kontrollieren
 3. Kuma-Heartbeat des Netzwerkbackups überprüfen
 4. gegebenenfalls Telegram-Statuswechsel kontrollieren
 5. systemd-Service für das Netzwerkbackup erstellen
 6. systemd-Timer für Montag und Donnerstag um 18:00 Uhr erstellen
 7. Verhalten bei ausgeschaltetem Hauptrechner kontrolliert testen
 8. Verhalten bei nicht eingehängter CIFS-Freigabe testen
 9. vorhandene Testbackups auf der Ubuntu-Systemplatte bewerten und gegebenenfalls entfernen
10. Wiederherstellung eines MariaDB-Dumps testweise dokumentieren
11. Wiederherstellung des Konfigurationsarchivs dokumentieren
12. optional Prüfsummen für größere Sicherungsbestände ergänzen
13. abschließenden Server-Neustart durchführen und Timer, Container und Mounts kontrollieren

---

## 21. Hinweise zur Veröffentlichung

Vor einer Veröffentlichung in einem öffentlichen Repository müssen folgende Informationen anonymisiert oder durch Platzhalter ersetzt bleiben:

- echte Benutzernamen
- interne und öffentliche IP-Adressen
- CIFS-Freigabenamen
- echte Domainnamen
- API-Schlüssel
- DDNS-Update-URLs
- Uptime-Kuma-Push-URLs
- Telegram-Zugangsdaten
- Datenbankpasswörter
- Dateinamen, die Rückschlüsse auf reale Benutzer ermöglichen

Geheime URL-Dateien sollten niemals in Git aufgenommen werden. Für ein öffentliches Repository sollten nur Beispieldateien bereitgestellt werden, etwa:

```text
ionos-ddns-url.example
nextcloud-backup-kuma-url.example
nextcloud-network-backup-kuma-url.example
```

Die echten Dateien sollten über `.gitignore` ausgeschlossen werden.