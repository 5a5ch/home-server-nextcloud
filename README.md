# home-server-nextcloud
Ich baue meinen eigenen Nextcloud-Server um meine Daten zu Hause zu behalten! Das war der Anfang und die Idee des Projekts.

## PROJEKTÜBERSICHT
### Ziele & Gedanken dazu
Da ich meine Daten unterwegs immer gerne bei mir habe, nutze ich schon seit jeher Cloud-Speicher wie Dropbox, iCloud etc.
Die Nachteile solcher Dienste sind der begrenzte Speicher und dass man nicht die absolute Hoheit über sein privaten Daten
hat. Ich musste also immer abwägen welche Daten ich wo speichere und wie ich möglichst effizient mit der Datenmenge umgehe.
Größere Datenmengen aus Musik- und Videoproduktionen in der Cloud zu speichern, war aus Kapazitätsgründen immer problematisch.
Praktische Dinge wie Fotos vom Smartphone synchronisieren bzw. direkt auszulagern ist ohne Abo bei irgendeinem Dienst 
ebenfalls nicht möglich.
Es gibt also zahlreiche Gründe dieses Problem zu lösen und meine Lösung hieß NEXTCLOUD auf einem eigenen Home-Server.
Ich hatte bereits Erfahrung gesammelt mit Nextcloud (ehemals Owncloud), das ich zuvor bereits bei einem Webhoster installiert 
und genutzt hatte. Auch aus Firmenkontext kannte ich die App.
Außerdem bietet Nextcloud weitere nützliches Tools wie Passwörter-Verwaltung, Kalender oder Notizen. Alles Dienste, die ich
aktuell von anderen Anbietern nutze und die zu meinen unabdingbaren täglichen Werkzeugen gehören.
Weitere Pluspunkte sind, dass Nextcloud ein europäisches Produkt ist und zusätzlich noch Open-Source. Es gibt außerdem 
zahlreiche Dokumentationen und eine Community.
Hürden: aus früheren kleinen Projekten war mir von Anfang an klar, dass das trotzdem nicht einfach sein würde, da ich 
kaum über Kenntnisse im Umgang mit Docker, Linux, Kommandozeile, Datenbanken, Sicherheit, Skripten verfüge. Das heißt
ich würde mich mit jedem Thema auseinandersetzen müssen, recherchieren, Code-Zeilen finden und anpassen und durch viel 
Trial-&-Error probieren müssen um ein System aufzusetzen, dass den Anforderungen genügt. 

### Anforderungen
Ja, und welche Anforderungen hat so ein System überhaupt? Ich definiere also Anforderungen.

- Sicherheit
- Zuverlässigkeit
- Verfügbarkeit
- von Außen erreichbar
- mehrere Nutzer
- Kompatibilität mit verschiedenen OS
- Backups
- Updates
- Monitoring
- Erweiterbarkeit

### Recherche und erste Schritte
Ich begann also mit ersten Recherchen zu den verschiedenen Themen und laß mich durch Artikel und Forenbeiträge. Ich nahm
Suchbegriffe, tippte sie in die Suchmaschine und ließ von dort an treiben. Ich laß über Nutzererfahrungen, Hardware-Empfehlungen
und hoffte auch Komplett-Anleitungen, die mich später durch die Einrichtung führen sollten. Und wie das zu Beginn eines Projektes,
von dem ich kaum Ahnung hatte, oft so ist, bekam ich nicht mehr Klarheit, sondern es taten sich immer neue Fragen auf und
die Sache wurde erst ein Mal unübersichtlicher.
Also beschloss ich einfach mal loszulegen. Ich hatte noch einen alten Raspberry Pi 3b und Festplatten lagerten auch genug in
meinen Schubladen. Dass die Hardware des Raspi für Nextcloud zu langsam ist, ist mir bekannt, aber das stört mich erst Mal nicht.
Ich wollte erst Mal Erfahrung sammeln und mich so der Sache annähern.
An dieser Stelle kürze ich etwas ab, da ich die Einleitung nicht unnötig verlängern möchte und nur das Nötigste erwähnen möchte.
Mit dem Raspi habe ich grundlegend die Installation von Nextcloud zum laufen bekommen und den Zugriff über das lokale Netzwerk. 
Es stellte sich allerdings sehr schnell raus, dass die Hardware komplett überfordert ist und das System extrem langsam läuft. 
Für einige Test und zum Ausprobieren war das akzeptabel, aber für den späteren Betrieb weder brauchbar noch praktikabel.

### Hardware
### Software und Versionen
### Netzwerkarchitektur
### Installation und Einrichtung
### Nextcloud-Funktionen
### Sicherheitsmaßnahmen
### Backup- und Wiederherstellungskonzept
### Monitoring und Wartung
### Probleme und Lösungen
### Was ich dabei gelernt habe
### Mögliche zukünftige Erweiterungen


## DETAILS
### SSH-Zugriff mit Schlüsseln
### Benutzer- und Rechteverwaltung
### Firewall-Konfiguration
### Docker beziehungsweise Docker Compose
### VPN oder Reverse Proxy
### HTTPS-Zertifikate
### automatische Updates
### Backup nach dem 3-2-1-Prinzip
### Wiederherstellung eines Backups getestet
### Protokollierung und Fehlersuche
# Monitoring von Speicherplatz und Systemzustand
