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
Weitere Pluspunkte sind, dass Nextcloud ein europäisches, genauer gesagt ein deutsches Produkt ist und zusätzlich noch Open-Source. Es gibt außerdem 
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
Ich begann also mit ersten Recherchen zu den verschiedenen Themen und laß mich durch Artikel und Forenbeiträge. Ich nahm Suchbegriffe, tippte sie in die Suchmaschine und ließ von dort an treiben. Ich laß über Nutzererfahrungen, Hardware-Empfehlungen und hoffte auch Komplett-Anleitungen, die mich später durch die Einrichtung führen sollten. Und wie das zu Beginn eines Projektes, von dem ich kaum Ahnung hatte, oft so ist, bekam ich nicht mehr Klarheit, sondern es taten sich immer neue Fragen auf und die Sache wurde erst ein Mal unübersichtlicher. Also beschloss ich einfach mal loszulegen. Ich hatte noch einen alten Raspberry Pi 3b und Festplatten lagerten auch genug in meinen Schubladen. Dass die Hardware des Raspi für Nextcloud zu langsam ist, ist mir bekannt, aber das stört mich erst Mal nicht. Ich wollte erst Mal Erfahrung sammeln und mich so der Sache annähern. An dieser Stelle kürze ich etwas ab, da ich die Einleitung nicht unnötig verlängern möchte und nur das Nötigste erwähnen möchte. Mit dem Raspi habe ich grundlegend die Installation von Nextcloud zum laufen bekommen und den Zugriff über das lokale Netzwerk. Es stellte sich allerdings sehr schnell raus, dass die Hardware komplett überfordert ist und das System extrem langsam läuft. Für einige Test und zum Ausprobieren war das akzeptabel, aber für den späteren Betrieb weder brauchbar noch praktikabel.

### Hardware & Software
**Grundlegenden Fragen:<br>**
**- "welche Hardware benötige ich?"<br>**
  -> Es ist nicht die neuste Hardware nötig. Aus Tutorials, Forenbeiträgen und Artikeln geht klar hervor, dass ältere Hardware sich heute immer noch gut für ein Home-Server-Projekt eignet. Ich entschloss mich zuerst für einen gebrauchten MacMini. Für 50 € fand ich bei Kleinanzeigen ein Modell aus 2013 mit ein i7-Quad-Core-Prozessor, 8 GB RAM und einer SSD. Leider stellte sich nach dem Kauf heraus, dass eine Neuinstallation von Ubuntu oder macOS nicht möglich ist, da das Gerät mit einem UEFI-Passwort versehen war welches ich nicht kannte und was ein Neuaufsetzen des OS verhinderte. Ich kontaktierte den Verkäufer, der mir aber glaubhaft versichert, dass er davon nichts und wusste und das Gerät selbst vor Jahren gebraucht gekauft hatte. Im normalen Betrieb wird dieses Passwort nicht abgefragt - deshalb wusste er nicht davon. Ich recherchierte also wie ich diesen Passwort-Schutz umgehen könne bzw. ob ein komplettes Werksreset möglich ist. In meinem Fall war das "leider" nicht möglich. Apple hat hier gut gearbeitet und   so soll es auch sein! Ich stieß bei meiner Recherche auf einige "Kaufmöglichkeiten", die versprachen den Schutz auszuhebeln. Allerdings für einen      Preis jenseits von 100 €. Das war das Gerät nicht wert und mir erschienen die Angebote auch etwas dubios. Nachdem ich hier in eine Sackgasse kam, entschloss ich mich die Einzelteile auszubauen und zu verkaufen, was mir auch gelang. RAM, SSD und Logigboard einzeln verkauft, brachten mir ca. 45 €. Mein Verlust hielt sich in Grenzen.<br>
<br>
  Beim nächsten Anlauf suchte ich gezielt nach ThinClients. Ich stieß auf ein Lenovo ThinkCentre für 35 € und handelte den Versand inklusive aus.<br>
  Spezifikationen:<br>
  Intel(R) Core(TM) i5-3470T CPU @ 2.90GHz<br>
  8GiB System Memory<br>
  integrierte Grafik-Einheit<br>
  ohne SSD/HDD<br>
  Ich setzte eine alte 120 GB OCZ Vertex ein und schon war die Hardware für das System bereit.<br>
  Speicher: Ich habe eine Schublade voll mit Festplatten in verschiedenen Größen und entschied mich mit einer alten 1TB Platte in einem externen Gehäuse und über USB angeschlossen zu beginnen.<br>
  <br>
**- "Welche Anforderungen stellt die Software?"<br>**
  Ich laß überall, dass Linux generell ein wenig leistunghungriges System ist. Es wird stetig gewartet und bekommt Updates. Es ist frei. Also wählte ich Ubuntu ohne graphische Oberfläche. Docker und Nextcloud sollten der Hardware keine Probleme bereiten. Das hatte ich mehrmals im Netz abgefragt.    <br>
  <br>
**- "Wie zukunftsfähig/erweiterungsfähig soll mein System sein?"<br>**
  Zu dem Zeitpunkt hatte ich noch keine Zukunftsfolgepläne. Aber ich ging davon aus, dass sich neben Nextcloud sicher noch der ein oder andere Dienst
  installieren ließe. Erst mit den späteren Recherchen stieße auf weitere Inspirationen wie Smart-Home, Pihole, Email-Server. Für ein KI-Projekt, was
  ich in Zukunft irgendwann noch starten werde, ist die Hardware nicht brauchbar.<br>
  <br>
**- "Welche laufenden Kosten entstehen durch den Stromverbrauch?"<br>**
  Zunächst einmal stellte sich die Frage wie man das überhaupt berechnet? Rechnet man mit annähernd Leerlauf-Verbrauch? Unter Volllast wird das System eher
  nicht laufen. Finde ich überhaupt Werte für die CPU?<br>
  Der Prozesser wird mit 35-W-TDP angegeben. Das ist natürlich erst Mal ein Wert, der sich schwer in Relation setzen lässt. Also befragte ich KI und ließ mir
  folgende Schätzung geben.<br>
  Idle, keine Zugriffe ->	etwa 10–15 W<br>
  Normalbetrieb mit Docker/Nextcloud -> etwa 12–20 W<br>
  Kurzzeitige Lastspitzen -> etwa 25–40 W<br>
  Dauerhafte Volllast	-> etwa 35–50 W<br>
  <br>
  Ich nahm einen Wert von 15W zwischen Idle und Normalbetrieb an.<br>
  Hinzu kommt die 3,5" HDD, die in einer uralten ICY-Box steckt. Schätzungen der KI kamen je nach Zugriffshäufigkeit auf 4-10 Watt. Ich einigte mich
  mit mir auf die Mitte von 7 Watt.<br>
  
  Das errechnete ich:<br>
  Jahresverbrauch: (0,015KW + 0,007W) * 24h * 365 * 0,30 €/KWh = 57,82 €<br>
  Da ich ein Balkonkraft habe, sollte der Stromverbrauch tagsüber größtenteils gedeckt sein. Und wenn man davon ausgeht, dass der Verbrauch
  nachts eher Richtung Idle geht, so sollten die Kosten in Realität definitiv niedriger ausfallen.<br>
  Ich habe mich mit der Frage des Stromverbrauchs eine ganze Zeit beschäftigt. Ich wollte definitiv KEINEN zusätzlichen größeren Verbraucher im Haushalt
  haben und bei der Hardware-Recherche muss man genau das immer abfragen. Denn die Softwareanforderungen lassen sich mit wirklich viel alter Hardware
  bedienen, aber alte CPU sind aus verschiedenen Gründen nicht gerade stromsparend und die Jahresrechnung kann schnell um einen dreistelligen Betrag steigen.
  An der Stelle lohnt es sich zu rechnen. Denn moderne Hardware, also moderne CPU, gerade Mobilprozessoren, sind für so ein Projekt ideal. Und damit komme ich
  zur nächsten Frage.<br>
  <br>
**- "Welches Budget steht mir zu Verfügung?"<br>**
  Ehrlich gesagt wollte ich nicht viel Geld ausgeben. Es ist in erster Linie ein Versuch von dem ich nicht weiß wie gut und zuverlässig alles laufen wird. Ich
  konnte zu Beginn nicht abschätzen, welche Hürden und Stolpersteine mich noch erwarten und ob das Ganze wirklich nachhaltig funktionieren wird.
  Die Kosten der Hardware habe ich ja oben schon erwähnt und dabei wollte ich es vorerst auch belassen.
  Für den Anfang: Keep it cheap!
  Sehe ich später, dass alles gut läuft, kann ich die Rechnung nochmal überdenken. Ein sparsames Gerät das unter 5 Watt verbraucht und dazu eine SSD für Daten,
  die über USB mit Strom versorgt wird, würde die laufenden Stromkosten nochmal deutlich reduzieren, was sich über die Zeit aufrechnen kann.<br>
  <br>
**- "Welches OS nutze ich überhaupt?"<br>**
  Vorweg:<br>
  Ich habe mich für Ubuntu entschieden.<br>
  Ubuntu ist frei. Es gibt regelmäßig Updates. Es braucht wenig Ressourcen und kann ohne GUI installiert werden. Es gibt bereits viele beispielhafte Projekte, die 
  gut dokumentiert sind. Und es reizt mich Erfahrung mit Linux zu sammeln.<br>
  Ich habe mir aber vorher angeschaut welche Möglichkeiten es gibt und für mich bewertet:<br>
  - macOS: ein alter MacMini wäre preisgünstig gewesen, es hätte aber Probleme mit einem aktuellen macOS früher oder später gegeben. Über OpenCorePatcher
    lässt sich bis Sequoia patchen. Dann ist aber Schluss, Tahoe läuft noch nicht. Außerdem fällt die USB-A-Anschluss Unterstützung irgendwann weg. Ich 
    hatte etwa vor einem Jahr einen iMac Late 2013 (i5 Quad-Core, 16 GB RAM, 1 GB GPU) auf Sequoia geupdatet und leider festgestellt, dass das System
    wirklich schwerfällig läuft. Zwei Gründe, die dagegen sprechen. Ein neueres Modell mit M-Prozessor wäre sicher ideal für das OS, aber nicht im Budget.<br>
  - Windows 11: Mein erstes Bedenken: Windows ist von Haus aus sehr ressourcenhungrig und meine Hardware ist schon etwas älter. Vermutlich muss man lange 
    und tief in das System eingreifen um unnötige Dienste dauerhaft abzuschalten um das System performanter und ressourcenschonender zu machen. Zweites
    Fragezeichen: Kompatibilität-Abfrage von Windows an die Hardware: Von Haus aus, ist so ein alter PC nicht mit den Anforderungen kompatibel. Mit dem Rufus Tool
    zur Erstellung eines Installations-Medium hätte ich das Problem vermutlich umgehen können. Kommen wir zu den Updates. Leider habe ich schon oft gehört
    dass Updates gerne mal vom User gemachte Einstellungen und Anpassungen überschreiben und den von Microsoft gewünschten Zustand wiederherstellen. Das fände ich
    ehrlich gesagt sehr unschön und so sehr ich mich auf das Projekt freue, möchte ich zukünftig aber nicht ständig fürchten Einstellungen erneut und erneut
    machen zu müssen. Und zu guter Letzt bin ich überhaupt kein Freund von Microsofts Einstellung zum Umgang mit sogenannten Diagnose- und Nutzungsdaten. Schon
    allein die Tatsache Windows nicht ohne Microsoft-Konto nutzen zu können widerstrebt mir. Windows schied also aus.<br>
  - Raspberry OS: Das OS ist eine Linux-Distribution, die speziell für den Raspberry gemacht ist. Es gibt sie mit und ohne GUI. Prinzipiell wäre das ein gute Wahl
    insbesondere in Kombination mit einem Modell 4 oder 5 des Raspi.
  <br>

**- "Gibt es Empfehlungen aus Foren oder Artikeln?"<br>**
Ja, die gibt es massenhaft. Man kann lesen, lesen, lesen und ebensoviele Tutorials schauen. Das ist gut. Es zeigt wie beliebt und aktuell das Thema ist. Nextcloud
ist hier nicht die einzige Lösung. Bei meiner Recherche habe ich auch kurze Blicke auf andere Optionen geworfen. Ehrlich gesagt, habe ich mich aber nicht tiefer-
gehend mit anderen Lösungen beschäftigt. Mein Fokus lag von Anfang an auf Nextcloud. Ich bin ein Anfänger und jeder Schritt des Projekts ist für mich mehr oder
weniger Neuland und erfordert einiges Anlesen an Wissen. Gerade am Anfang hatte ich viele Fragezeichen zu klären und oftmals hatte ich nach dem Recherchieren 
mehr Fragen als Antworten auf meinem Zettel. So beschloss ich mich zu diesem Zeitpunkt nicht mit Alternativen auseinanderzusetzen.

### Software und Versionen
Ziel ist es Nextcloud zum laufen zu bringen. Das ist auf vielen Wegen möglich. Ich entschied mich nach einiger Recherche für folgenden Unterbau:
- Ubuntu ohne GUI -> Zugriff über SSH von meinem Hauptrechner über das lokale Netz.
- Docker
- Nextcloud im Container
- Reverse Proxy
- Kuma Monitoring

Alle Applikationen in der zur Zeit der Installation aktuellsten Version.


### Vorgehensweise bei Installation und Einrichtung
Ich komme nun zu einem grundlegenden Gedanken, der mich schon während all den Überlegungen begleitet hat. Wie gehe ich die Sache an? Aus früheren Projekten weiß ich, dass auf mich eine Menge Recherche zukommt und dass mich viele der Themen beim Ausprobieren an den Rand der Verwzweiflung bringen werden. Warum weiß ich das? Ich kenne mich schlicht nicht aus. Jeden Terminal-Befehl, jede Zeile in einer Config-Datei, werde ich irgendwoher aus dem Netz kopieren, vielleicht leicht modifizieren und hoffen sie in meinem Fall das Richtige macht. Die Schwierigkeit ist hier, dass vermutlich keines der Programme oder der Dienste, die es zwingend zu installieren und zu konfigurieren gilt, mit einer GUI arbeitet auf der man alle Einstellungsmöglichkeiten an- und abhaken kann, wo noch ein schöner Hilfetext erscheint, wenn man mit der Maus darüber hovert. Alles passiert in der Kommandozeile oder in irgendeinem Texteditor und alles sieht wahnsinnig kryptisch aus.<br>
Bereits in der Vergangenheit habe ich Homepages in HMTL "geschrieben" oder mir ein NAS auf dem Raspi eingerichtet. Bei einer Homepage ist es ein großes Puzzle. Ich suche mich passende Code-Schnipsel, versuche zu verstehen was dort steht und kopiere sie mit leichen Anpassungen. Dann wundere ich mich warum sie nicht funktionieren und probiere so lange herum bis es klappt. Ziemlich müßig und ehrlich gesagt nicht so effektiv. Ich habe natürlich grundlegend immer etwas dabei gelernt, gerade was die Funktionsweisen einer Sache, eines Codeschnipsel, eines Diensts angeht, die detaillierte Vorgehensweise ist dabei aber nicht hängengeblieben.<br>
Meine Berührungspunkte mit KI waren bis dahin minimal. Im Alltag nutzte ich bis dahin keine KI. Mein Alltagsbegleiter war Google. Und ehrlich gesagt, hatte ich keine Fragen an die KI. Mir fehlte bis dahin das Vorstellungsvermögen, wie ich KI sinnvoll für mich einsetzen konnte. Für die Wetterabfrage braucht es keine KI und die Öffnungszeiten vom nächsten Supermarkt zeigt die Karten-App an. Neue Rezepte finde ich bei Chefkoch und bei Thomann kaufe ich Saiten für meine Gitarre.<br>
Aber mit diesem Projekt bot sich mir eine erste und gute Gelegenheit mich mit KI vertraut zu machen und zu lernen wie mir die KI hilfreich sein konnte bei meinem Vorhaben. Ich würde sehen welche Vor- und Nachteile mir das bringt. Ich würde sehen wie man mit KI arbeitet und ich würde mir auch endlich selbst einen Eindruck verschaffen. Ich erwähne das, weil die KI mir entscheidend geholfen hat mein Projekt bis zum aktuellen Stand umzusetzen. Ich kann vorweg nehmen, dass ich ziemlich begeistert bin. Ich habe schnell gelernt, dass meine Prompts entscheidend für eine gute Antwort sind. Ich habe mir alles ausführlich erklären lassen und bin über die Antworten zu vielen neuen Themen gekommen. Manchmal habe ich lange Diskussionen geführt um dann festzustellen, es ist besser etwas nicht zu machen. Ich habe auch festgestellt, dass KI sich gerne mal täuscht, im ersten Moment aber immer sehr überzeugt von Ihrem Vorschlag ist. Ich habe das berühmte Halluzinieren nachvollziehen können und gerlernt je mehr Infomation man zu Verfügung stellt, desto wahrscheinlicher wird eine gute Antwort. Definitiv ist es wichtig, jede Antwort kritisch zu hinterfragen, Belege einzufordern und zu prüfen. Mit Hilfe der KI näherte ich mich den Themen und erwarb ein Grundverständis. Mit der KI lernte ich NICHT tiefere Kenntnisse der Konfiguration verschiedener Dienste unter Linux. Die KI spuckt einen Terminal-Befehl aus, drei Zeilen lang und mit vielen Variablen, Optionen versehen ist und ich kopierte diese Befehle. Aber ich ließ mir immer ausführlich erklären warum und wofür eine Konfiguration oder ein Dienst nötig ist.<br>
KI ist ein mächtiges Assistenz-System, mit dem man viel Lernen kann. Man kann aber auch einfach schnell sein und nicht hinterfragen, es wird schon irgendwie laufen. Das mag ich persönlich nicht. Ich fühle mich nicht wohl, wenn ich nicht grundlegend verstehe, was ich mache und mich nicht bewusst entscheide. Ich habe schnell festgestellt, dass es sich lohnt lange und ausführlich zu fragen. Die KI kennt keine Ungeduld und keine dummen Fragen!!! Anderherum war ich noch nie um dumme Fragen verlegen ;-)

### Netzwerkarchitektur
Der Server mit der USB-Festplatte steht im Wohnzimmer und ist kabelgebunden an einen Mesh-Repeater, der wiederum das WLAN-Signal vom Router aus dem Stockwerk darunter verstärkt,  angeschlossen. Ins Internet geht es über den Router. Die IP-Adresse wird vom Router über DCHP vergeben. Der Router ist so eingestellt, dass er diese Adresse dauerhaft an den Server vergibt. Zugriff auf den Server sollen später ein Laptop, ein stationärer Rechner und vier Smartphones haben. Insgesamt vier Benutzer. Zugriff soll über außen erfolgen. Darauf gehe ich im Detail später ein. Auf dem Server wird ein Reverse Proxy laufen, der die Anfragen von außen an die entsprechenden Dienste auf dem Server weiterleiten wird. Auf dem Router wird eine Portweiterleitung eingerichtet, die auf den Server zeigt. <br>

[NETZWERKDIAGRAMM einfügen]

*welche Geräte beteiligt sind*
*wie der Server mit dem Netzwerk verbunden ist*
*welche Geräte auf Nextcloud zugreifen*
*ob der Zugriff nur im Heimnetz oder auch von außen möglich ist*
*welche Rolle der Router spielt*
*ob du VPN, Portfreigabe oder einen Reverse Proxy verwendest*
*welche Dienste auf dem Server laufen*
*welche Zugriffe erlaubt oder verhindert werden*



### Nextcloud-Funktionen
Nextcloud bietet verschiedene Funktionen und Dienste. Es gibt einen App-Store, der nützliche Erweiterungen bietet.<br>
Ich nutze aktuell
**- Datei-Synchronisierung:** private Daten aus dem Alltag behalten ich verfügbar auf dem Smartphone, Laptop und meinem Schreibtisch-Rechner. Da ich auf dem Laptop sämtliche Daten in die Cloud synchronisiere, entsteht auf meinem Hauptrechner automatisch eine Sicherung neuer Daten, die auf dem Laptop anfallen. Ein praktischer Nebeneffekt, der natürlich nicht die regelmäßige Datensicherung ersetzt.
**- automatischer Photoupload vom Smartphone:** Speicherplatz auf Smartphones ist teuer und begrenzt und bei uns in der Familie belegen Photos immer einen Großteil des Speichers. Ist die Nextcloud-App auf dem Smartphone installiert, bietet die Photos-App ein automatischen Upload der Foto- und Videomediathek an. Das bringt zwei Vorteile. Zum einen sind die Photos direkt als Kopie gesichert und zum anderen kann ich auf dem Smartphone einfach und schnell Photos löschen und Speicher freigeben.
**- Notizen (im Test):** Notizen sind mein Alltagshelfer und ich mache mir ständig Notizen aller Art. Entsprechend ist die Verfügbarkeit sehr wichtig für mich. Mittlerweile habe ich alle meine Notizen nach Nextcloud umgezogen und nutze nur noch diesen Dienst.
**- Passwort-Verwaltung (im Test):** Ich nutze generell einen Passwort-Manager. Ohne diesen näher zu benennen, muss ich kaum betonen, dass die Sicherheit und die Funktion sehr, sehr wichtig sind. Bevor ich hier einen kompletten Umzug mache, werde ich den von Nextcloud angebotenen Dienst näher beleuchten und ausführlich testen. Das habe ich bis jetzt nicht getan. Prinzipiell ist die App am Handy installiert und über die Webobefläche ist der Passwort-Manager am PC zugänglich.
**- Kontakte (im Test):** von einem Umzug meiner Kontakte zu Nextcloud verspreche ich mir eine betriebssystemübergreifende Nutzung. Der Praxistest steht hier noch aus.
**- integriertes Office:** mit Collabora bietet Nextcloud eine Office-Lösung, die auf dem Server als Anwendung läuft. Pluspunkt ist natürlich das es damit möglich ist eine Vielzahl an Dokumenten von unterwegs zu lesen und zu bearbeiten. Am Smartphone versuche das ehrlicherweise zu vermeiden. Ob die Office-Anwendungen mit MS Office oder dem Apple-Office mithalten können, welche Hürden die Kompatibilität mit sich bringt, kann ich bis jetzt nicht wirklich gut beurteilen. Für die einfachen Dinge funktioniert es gut.

Zukünftig ist für mich interessant
- KI-Erweiterung/Integration
- eMail-Server

### Sicherheitsmaßnahmen
Ja, das Thema Sicherheit ist vielleicht das Wichtigste. Während Dienste mal nicht funktionieren dürfen oder irgendwelche nervigen Sync-Probleme mit ständigen Time-Out-Meldungen nerven können, sollte sicherheitstechnisch nichts schief gehen, da ja es um meine persönlichen Daten geht. Das Thema KI und Sicherheit sollte man auch mit Vorsicht genießen. Nicht jede Empfehlung und Anleitung, welche die KI vorschlägt ist automatisch sicher! Ein Thema welches in Bezug auf Vibe-Coding für Anfänger, also das klassische Beispiel des jungen Unternehmers, der mit Vibe-Coding seine Internetpräsenz aufbaut und später stellt sich heraus, dass die Kundendatenbank offen im Netz lag. DSGVO-Horror-Szenario.<br>
Nextcloud selbst bringt ein paar Werkzeuge  mit.<br>
- Zwei-Faktor-Authentifizierung 2FA
- Log-Files
<br>
Mit Uptime-Kuma habe ich ein Tool entdeckt, dass es ermöglicht Regeln und Abfragen zu erstellen und diese zu überwachen.<br>
Soll lassen sich Zugriffe und Anmeldeversuche überwachen.
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
