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
Da Nextcloud-Instanzen mehrere Nutzer zulassen, schwebte mir auch vor den Zugang für den Familienkreis zu ermöglichen um 
beispielsweise eine automatische Foto-Synchronisation vom Smartphone zu ermöglichen.
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
Grundlegenden Fragen waren: 
- "welche Hardware benötige ich?"<br>
  -> Es ist nicht die neuste Hardware nötig. Aus Tutorials, Forenbeiträgen und Artikeln geht klar hervor, dass ältere Hardware sich heute
  immer noch gut für ein Home-Server-Projekt eignet. Ich entschloss mich zuerst für einen gebrauchten MacMini. Für 50 € fand ich bei Kleinanzeigen
  ein Modell aus 2013 mit ein i7-Quad-Core-Prozessor, 8 GB RAM und einer SSD. Leider stellte sich nach dem Kauf heraus, dass eine Neuinstallation von 
  Ubuntu oder macOS nicht möglich ist, da das Gerät mit einem UEFI-Passwort versehen war. Ich kontaktierte den Verkäufer, der mir aber glaubhaft versicherte
  das er davon nichts und wusste und das Gerät selbst vor Jahren gebraucht gekauft hatte. Im normalen Betrieb wird dieses Passwort nicht abgefragt - deshalb
  wusste er nicht davon. Ich recherchierte also wie ich diesen Passwort-Schutz umgehen könne bzw. ob ein komplettes Werksreset möglich ist. In meinem Fall
  war das "leider" nicht möglich. Apple hat hier gut gearbeitet und so soll es auch sein! Ich stieß bei meiner Recherche auf einige "Kaufmöglichkeiten", die
  versprachen den Schutz auszuhebeln. Allerdings für einen Preis jenseits von 100 €. Das war das Gerät nicht wert und mir erschienen die Angebote auch etwas
  dubios. Nachdem ich hier in eine Sackgasse kam, entschloss ich mich die Einzelteile auszubauen und zu verkaufen, was mir auch gelang. RAM, SSD und Logigboard
  einzeln verkauft, brachten mir ca. 45 €. Mein Verlust hielt sich in Grenzen.

  Beim nächsten Anlauf suchte ich gezielt nach ThinClients. Ich stieß auf ein Lenovo ThinkCentre für 35 € und handelte den Versand inklusive aus.
  Spezifikationen:
  Intel(R) Core(TM) i5-3470T CPU @ 2.90GHz
  8GiB System Memory
  integrierte Grafik-Einheit
  ohne SSD/HDD
  Ich setzte eine alte 120 GB OCZ Vertex ein und schon war die Hardware für das System bereit.

  Speicher: Ich habe eine Schublade voll mit Festplatten in verschiedenen Größen und entschied mich mit einer alten 1TB Platte in einem externen Gehäuse und
  über USB angeschlossen zu beginnen.
  
- "Welche Anforderungen stellt die Software?"
  Ich laß überall, dass Linux generell ein wenig leistunghungriges System ist. Es wird stetig gewartet und bekommt Updateas. Es ist frei. Also wählte ich
  Ubuntu ohne graphische Oberfläche. Docker und Nextcloud sollten der Hardware keine Probleme bereiten. Das hatte ich mehrmals im Netz abgefragt.
  
- "Wie zukunftsfähig/erweiterungsfähig soll mein System sein?"
  Zu dem Zeitpunkt hatte ich noch keine Zukunftsfolgepläne. Aber ich ging davon aus, dass sich neben Nextcloud sicher noch der ein oder andere Dienst
  installieren ließe. Erst mit den späteren Recherchen stieße auf weitere Inspirationen wie Smart-Home, Pihole, Email-Server
- "Welche laufenden Kosten entstehen durch den Stromverbrauch?"
- "Welche Software/OS nutze ich überhaupt?"
- "Welches Budget steht mir zu Verfügung?"
- "Gibt es Empfehlungen aus Foren oder Artikeln?"

### Software und Versionen
Ziel ist es Nextcloud zum laufen zu bringen. Das ist auf vielen Wegen möglich. Ich entschied mich nach einiger Recherche für folgenden Unterbau:
- Ubuntu ohne GUI -> Zugriff über SSH von meinem Hauptrechner über das lokale Netz.
- Docker
- Nextcloud im Container
- Reverse Proxy
- Kuma Monitoring

Alle Applikationen in der zur Zeit der Installation aktuellsten Version.

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
