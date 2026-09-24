# home-server-nextcloud

Ich baue meinen eigenen Nextcloud-Server um meine Daten zu Hause zu behalten! Das war der Anfang und die Idee des Projekts. Ich habe beschlossen den Projektverlauf hier nachträglich zu dokumentieren und dabei vor allem auf meine Gedanken und Überlegungen zu den einzelnen Schritten einzugehen. Ich werde hier nicht viele kryptische Zeilen aus irgendwelchen Nano-Konfigurationsdateien einstellen, da ich selbst zu wenig davon verstehe. Es handelt sich also nicht um eine Anleitung, davon gibt es genug da draußen im Netz. Vielmehr geht es mir darum, meinen eigenen Lernprozess zum grundlegenden Verständnis darzustellen.

## Projektübersicht

### Ziele und Gedanken dazu

Da ich meine Daten unterwegs immer gerne bei mir habe, nutze ich schon seit jeher Cloud-Speicher wie Dropbox, iCloud etc. Die Nachteile solcher Dienste sind der begrenzte Speicher und dass man nicht die absolute Hoheit über seine privaten Daten hat. Ich musste also immer abwägen, welche Daten ich wo speichere und wie ich möglichst effizient mit der Datenmenge umgehe. Größere Datenmengen aus Musik- und Videoproduktionen in der Cloud zu speichern, war aus Kapazitätsgründen immer problematisch. Praktische Dinge wie Fotos vom Smartphone zu synchronisieren beziehungsweise direkt auszulagern, ist ohne Abo bei irgendeinem Dienst ebenfalls nicht möglich. Es gibt also zahlreiche Gründe, dieses Problem zu lösen und meine Lösung hieß NEXTCLOUD auf einem eigenen Home-Server. Ich hatte bereits Erfahrungen mit Nextcloud gesammelt (ehemals ownCloud), das ich zuvor bereits bei einem Webhoster installiert und genutzt hatte. Auch aus dem Firmenkontext kannte ich die App. Außerdem bietet Nextcloud weitere nützliche Tools wie Passwortverwaltung, Kalender oder Notizen. Alles Dienste, die ich aktuell von anderen Anbietern nutze und die zu meinen unabdingbaren täglichen Werkzeugen gehören. Weitere Pluspunkte sind, dass Nextcloud ein europäisches, genauer gesagt ein deutsches Produkt ist und zusätzlich noch Open Source. Es gibt außerdem zahlreiche Dokumentationen und eine Community. Da Nextcloud-Instanzen mehrere Nutzer zulassen, schwebte mir auch vor, den Zugang für den Familienkreis zu ermöglichen, um beispielsweise eine automatische Foto-Synchronisation vom Smartphone zu ermöglichen. Hürden: Aus früheren kleinen Projekten war mir von Anfang an klar, dass das trotzdem nicht einfach sein würde, da ich kaum über Kenntnisse im Umgang mit Docker, Linux, Kommandozeile, Datenbanken, Sicherheit, Skripten verfüge. Das heißt ich würde mich mit jedem Thema auseinandersetzen müssen, recherchieren, Codezeilen finden und anpassen und durch viel Trial and Error probieren müssen, um ein System aufzusetzen, das den Anforderungen genügt.

### Anforderungen

Ja, und welche Anforderungen hat so ein System überhaupt? Ich definiere also Anforderungen.

- Sicherheit
- Zuverlässigkeit
- Verfügbarkeit
- von außen erreichbar
- mehrere Nutzer
- Kompatibilität mit verschiedenen Betriebssystemen
- Backups
- Updates
- Monitoring
- Erweiterbarkeit

### Recherche und erste Schritte

Ich begann also mit ersten Recherchen zu den verschiedenen Themen und las mich durch Artikel und Forenbeiträge. Ich nahm Suchbegriffe, tippte sie in die Suchmaschine und ließ mich von dort an treiben. Ich las über Nutzererfahrungen, Hardware-Empfehlungen und hoffte auch Komplettanleitungen, die mich später durch die Einrichtung führen sollten. Und wie das zu Beginn eines Projekts, von dem ich kaum Ahnung hatte, oft so ist, bekam ich nicht mehr Klarheit, sondern es taten sich immer neue Fragen auf und die Sache wurde erst einmal unübersichtlicher. Also beschloss ich einfach einmal loszulegen. Ich hatte noch einen alten Raspberry Pi 3B und Festplatten lagerten auch genug in meinen Schubladen. Dass die Hardware des Raspberry Pi für Nextcloud zu langsam ist, ist mir bekannt, aber das stört mich erst einmal nicht. Ich wollte erst einmal Erfahrung sammeln und mich so der Sache annähern. An dieser Stelle kürze ich etwas ab, da ich die Einleitung nicht unnötig verlängern möchte und nur das Nötigste erwähnen möchte. Mit dem Raspi habe ich grundlegend die Installation von Nextcloud zum Laufen bekommen und den Zugriff über das lokale Netzwerk. Es stellte sich allerdings sehr schnell raus, dass die Hardware komplett überfordert ist und das System extrem langsam läuft. Für einige Tests und zum Ausprobieren war das akzeptabel, aber für den späteren Betrieb weder brauchbar noch praktikabel.

### Hardware und Software

**Grundlegende Fragen:**

#### Welche Hardware benötige ich?

-> Es ist nicht die neueste Hardware nötig. Aus Tutorials, Forenbeiträgen und Artikeln geht klar hervor, dass ältere Hardware sich heute immer noch gut für ein Home-Server-Projekt eignet. Ich entschloss mich zuerst für einen gebrauchten Mac mini. Für 50 € fand ich bei Kleinanzeigen ein Modell aus 2013 mit einem i7-Quad-Core-Prozessor, 8 GB RAM und einer SSD. Leider stellte sich nach dem Kauf heraus, dass eine Neuinstallation von Ubuntu oder macOS nicht möglich ist, da das Gerät mit einem UEFI-Passwort versehen war, das ich nicht kannte und ein Neuaufsetzen des Betriebssystems verhinderte. Ich kontaktierte den Verkäufer, der mir aber glaubhaft versicherte, dass er davon nichts wusste und das Gerät selbst vor Jahren gebraucht gekauft hatte. Im normalen Betrieb wird dieses Passwort nicht abgefragt - deshalb wusste er nicht davon. Ich recherchierte also wie ich diesen Passwortschutz umgehen könnte beziehungsweise ob ein vollständiger Werksreset möglich ist. In meinem Fall war das "leider" nicht möglich. Apple hat hier gut gearbeitet und   so soll es auch sein! Ich stieß bei meiner Recherche auf einige "Kaufmöglichkeiten", die versprachen, den Schutz auszuhebeln. Allerdings für einen      Preis jenseits von 100 €. Das war das Gerät nicht wert und mir erschienen die Angebote auch etwas dubios. Nachdem ich hier in eine Sackgasse kam, entschloss ich mich, die Einzelteile auszubauen und zu verkaufen, was mir auch gelang. RAM, SSD und Logicboard einzeln verkauft brachten mir ca. 45 €. Mein Verlust hielt sich in Grenzen.

Beim nächsten Anlauf suchte ich gezielt nach Thin Clients. Ich stieß auf ein Lenovo ThinkCentre für 35 € und handelte den Versand inklusive aus. Spezifikationen: Intel(R) Core(TM) i5-3470T CPU @ 2,90 GHz 8 GiB Arbeitsspeicher integrierte Grafikeinheit ohne SSD/HDD Ich setzte eine alte 120-GB-OCZ-Vertex ein und schon war die Hardware für das System bereit. Speicher: Ich habe eine Schublade voll mit Festplatten in verschiedenen Größen und entschied mich mit einer alten 1-TB-Platte in einem externen Gehäuse und über USB angeschlossen zu beginnen.

#### Welche Anforderungen stellt die Software?

Ich las überall, dass Linux generell ein wenig ressourcenhungriges System ist. Es wird stetig gewartet und bekommt Updates. Es ist frei. Also wählte ich Ubuntu ohne grafische Oberfläche. Docker und Nextcloud sollten der Hardware keine Probleme bereiten. Das hatte ich mehrmals im Netz abgefragt.

#### Wie zukunftsfähig/erweiterungsfähig soll mein System sein?

Zu dem Zeitpunkt hatte ich noch keine konkreten Zukunftspläne. Aber ich ging davon aus, dass sich neben Nextcloud sicher noch der eine oder andere Dienst installieren ließe. Erst mit den späteren Recherchen stieß ich auf weitere Inspirationen wie Smart Home, Pi-hole, E-Mail-Server. Für ein KI-Projekt, das ich in Zukunft irgendwann noch starten werde, ist die Hardware nicht brauchbar.

#### Welche laufenden Kosten entstehen durch den Stromverbrauch?

Zunächst einmal stellte sich die Frage, wie man das überhaupt berechnet? Rechnet man mit annäherndem Leerlaufverbrauch? Unter Volllast wird das System eher nicht laufen. Finde ich überhaupt Werte für die CPU? Der Prozessor wird mit einer TDP von 35 Watt angegeben. Das ist natürlich erst einmal ein Wert, der sich schwer in Relation setzen lässt. Also befragte ich KI und ließ mir folgende Schätzung geben. Idle, keine Zugriffe ->	etwa 10–15 W Normalbetrieb mit Docker/Nextcloud -> etwa 12–20 W Kurzzeitige Lastspitzen -> etwa 25–40 W Dauerhafte Volllast	-> etwa 35–50 W

Ich nahm einen Wert von 15 W zwischen Idle und Normalbetrieb an. Hinzu kommt die 3,5" HDD, die in einer uralten ICY-Box steckt. Schätzungen der KI kamen je nach Zugriffshäufigkeit auf 4–10 Watt. Ich einigte mich mit mir auf die Mitte von 7 Watt.

Das errechnete ich: Jahresverbrauch: (0,015 kW + 0,007 kW) * 24 h * 365 * 0,30 €/kWh = 57,82 € Da ich ein Balkonkraftwerk habe, sollte der Stromverbrauch tagsüber größtenteils gedeckt sein. Und wenn man davon ausgeht, dass der Verbrauch nachts eher Richtung Idle geht, so sollten die Kosten in der Realität definitiv niedriger ausfallen. Ich habe mich mit der Frage des Stromverbrauchs eine ganze Zeit beschäftigt. Ich wollte definitiv KEINEN zusätzlichen größeren Verbraucher im Haushalt haben und bei der Hardware-Recherche muss man genau das immer abfragen. Denn die Softwareanforderungen lassen sich mit wirklich viel alter Hardware bedienen, aber alte CPUs sind aus verschiedenen Gründen nicht gerade stromsparend und die Jahresrechnung kann schnell um einen dreistelligen Betrag steigen. An der Stelle lohnt es sich zu rechnen. Denn moderne Hardware, also moderne CPUs, gerade Mobilprozessoren, sind für so ein Projekt ideal. Und damit komme ich zur nächsten Frage.

#### Welches Budget steht mir zur Verfügung?

Ehrlich gesagt wollte ich nicht viel Geld ausgeben. Es ist in erster Linie ein Versuch, von dem ich nicht weiß, wie gut und zuverlässig alles laufen wird. Ich konnte zu Beginn nicht abschätzen, welche Hürden und Stolpersteine mich noch erwarten und ob das Ganze wirklich nachhaltig funktionieren wird. Die Kosten der Hardware habe ich ja oben schon erwähnt und dabei wollte ich es vorerst auch belassen. Für den Anfang: Keep it cheap! Sehe ich später, dass alles gut läuft, kann ich die Rechnung nochmal überdenken. Ein sparsames Gerät, das unter 5 Watt verbraucht und dazu eine SSD für Daten, die über USB mit Strom versorgt wird, würde die laufenden Stromkosten nochmal deutlich reduzieren, was sich über die Zeit aufrechnen kann.

#### Welches OS nutze ich überhaupt?

Vorweg: Ich habe mich für Ubuntu entschieden. Ubuntu ist frei. Es gibt regelmäßig Updates. Es braucht wenig Ressourcen und kann ohne GUI installiert werden. Es gibt bereits viele beispielhafte Projekte, die gut dokumentiert sind. Und es reizt mich Erfahrungen mit Linux zu sammeln. Ich habe mir aber vorher angeschaut, welche Möglichkeiten es gibt und für mich bewertet:

- macOS: ein alter Mac mini wäre preisgünstig gewesen, es hätte aber Probleme mit einem aktuellen macOS früher oder später gegeben. Über OpenCore Legacy Patcher
lässt sich bis Sequoia patchen. Dann ist aber Schluss, Tahoe läuft noch nicht. Außerdem fällt die Unterstützung für USB-A-Anschlüsse irgendwann weg. Ich hatte etwa vor einem Jahr einen iMac Late 2013 (i5 Quad-Core, 16 GB RAM, 1 GB GPU) auf Sequoia geupdatet und leider festgestellt, dass das System wirklich schwerfällig läuft. Zwei Gründe, die dagegen sprechen. Ein neueres Modell mit M-Prozessor wäre sicher ideal für das OS, aber nicht im Budget.

- Windows 11: Mein erstes Bedenken: Windows ist von Haus aus sehr ressourcenhungrig und meine Hardware ist schon etwas älter. Vermutlich muss man lange
und tief in das System eingreifen um unnötige Dienste dauerhaft abzuschalten um das System performanter und ressourcenschonender zu machen. Zweites Fragezeichen: Kompatibilität-Abfrage von Windows an die Hardware: Von Haus aus, ist so ein alter PC nicht mit den Anforderungen kompatibel. Mit dem Rufus Tool zur Erstellung eines Installationsmedium hätte ich das Problem vermutlich umgehen könnten. Kommen wir zu den Updates. Leider habe ich schon oft gehört dass Updates gerne mal vom Benutzer vorgenommene Einstellungen und Anpassungen überschreiben und den von Microsoft gewünschten Zustand wiederherstellen. Das fände ich ehrlich gesagt sehr unschön und so sehr ich mich auf das Projekt freue, möchte ich zukünftig aber nicht ständig fürchten, Einstellungen erneut und erneut machen zu müssen. Und zu guter Letzt bin ich überhaupt kein Freund von Microsofts Einstellung zum Umgang mit sogenannten Diagnose- und Nutzungsdaten. Schon allein die Tatsache Windows nicht ohne Microsoft-Konto nutzen zu können, widerstrebt mir. Windows schied also aus.

- Raspberry Pi OS: Das OS ist eine Linux-Distribution, die speziell für den Raspberry Pi entwickelt ist. Es gibt sie mit und ohne GUI. Prinzipiell wäre das eine gute Wahl
insbesondere in Kombination mit einem Modell 4 oder 5 des Raspberry Pi.

#### Gibt es Empfehlungen aus Foren oder Artikeln?

Ja, die gibt es massenhaft. Man kann lesen, lesen, lesen und ebenso viele Tutorials schauen. Das ist gut. Es zeigt, wie beliebt und aktuell das Thema ist. Nextcloud ist hier nicht die einzige Lösung. Bei meiner Recherche habe ich auch kurze Blicke auf andere Optionen geworfen. Ehrlich gesagt habe ich mich aber nicht tiefergehend mit anderen Lösungen beschäftigt. Mein Fokus lag von Anfang an auf Nextcloud. Ich bin ein Anfänger und jeder Schritt des Projekts ist für mich mehr oder weniger Neuland und erfordert einiges an neuem Wissen. Gerade am Anfang hatte ich viele Fragezeichen zu klären und oftmals hatte ich nach dem Recherchieren mehr Fragen als Antworten auf meinem Zettel. So beschloss ich zu diesem Zeitpunkt nicht mit Alternativen auseinanderzusetzen.

### Software und Versionen

Ziel ist es, Nextcloud zum Laufen zu bringen. Das ist auf vielen Wegen möglich. Ich entschied mich nach einiger Recherche für folgenden Unterbau:

- Ubuntu ohne GUI -> Zugriff über SSH von meinem Hauptrechner über das lokale Netz.
- Docker
- Nextcloud im Container
- Reverse Proxy
- Kuma Monitoring

Alle Applikationen in der zurzeit der Installation aktuellen Version.

### Vorgehensweise bei Installation und Einrichtung

Ich komme nun zu einem grundlegenden Gedanken, der mich schon während all der Überlegungen begleitet hat. Wie gehe ich die Sache an? Aus früheren Projekten weiß ich, dass auf mich eine Menge Recherche zukommt und dass mich viele der Themen beim Ausprobieren an den Rand der Verzweiflung bringen werden. Warum weiß ich das? Ich kenne mich schlicht nicht aus. Jeden Terminalbefehl, jede Zeile in einer Konfigurationsdatei, werde ich irgendwoher aus dem Netz kopieren, vielleicht leicht modifizieren und hoffen sie in meinem Fall das Richtige macht. Die Schwierigkeit ist hier, dass vermutlich keines der Programme oder der Dienste, die es zwingend zu installieren und zu konfigurieren gilt, mit einer GUI arbeitet auf der man alle Einstellungsmöglichkeiten an- und abhaken kann, wo noch ein schöner Hilfetext erscheint, wenn man mit der Maus darüber darüberfährt. Alles passiert in der Kommandozeile oder in irgendeinem Texteditor und alles sieht wahnsinnig kryptisch aus. Bereits in der Vergangenheit habe ich Homepages in HTML "geschrieben" oder mir ein NAS auf dem Raspi eingerichtet. Bei einer Homepage ist es ein großes Puzzle. Ich suche mir passende Codeschnipsel, versuche zu verstehen was dort steht und kopiere sie mit leichten Anpassungen. Dann wundere ich mich, warum sie nicht funktionieren und probiere so lange herum bis es klappt. Ziemlich müßig und ehrlich gesagt nicht so effektiv. Ich habe natürlich grundlegend immer etwas dabei gelernt, gerade was die Funktionsweisen einer Sache, eines Codeschnipsels, eines Dienstes angeht, die detaillierte Vorgehensweise ist dabei aber nicht hängen geblieben. Meine Berührungspunkte mit der **KI** waren bis dahin minimal. Im Alltag nutzte ich bis dahin keine KI. Mein Alltagsbegleiter war Google. Und ehrlich gesagt, hatte ich keine Fragen an die KI. Mir fehlte bis dahin das Vorstellungsvermögen, wie ich KI sinnvoll für mich einsetzen konnte. Für die Wetterabfrage braucht es keine KI und die Öffnungszeiten des nächsten Supermarkt zeigt die Karten-App an. Neue Rezepte finde ich bei Chefkoch und bei Thomann kaufe ich Saiten für meine Gitarre. Aber mit diesem Projekt bot sich mir eine erste und gute Gelegenheit, mich mit KI vertraut zu machen und zu lernen, wie mir die KI hilfreich sein konnte bei meinem Vorhaben. Ich würde sehen, welche Vor- und Nachteile mir das bringt. Ich würde sehen wie man mit KI arbeitet und ich würde mir auch endlich selbst einen Eindruck verschaffen. Ich erwähne das, weil die KI mir entscheidend geholfen hat, mein Projekt bis zum aktuellen Stand umzusetzen. Ich kann vorwegnehmen, dass ich ziemlich begeistert bin. Ich habe schnell gelernt, dass meine Prompts entscheidend für eine gute Antwort sind. Ich habe mir alles ausführlich erklären lassen und bin über die Antworten zu vielen neuen Themen gekommen. Manchmal habe ich lange Diskussionen geführt, um dann festzustellen, es ist besser etwas nicht zu machen. Ich habe auch festgestellt, dass KI sich gerne mal täuscht, im ersten Moment aber immer sehr überzeugt von ihrem Vorschlag ist. Ich habe das berühmte Halluzinieren nachvollziehen können und gelernt je mehr Informationen man zur Verfügung stellt, desto wahrscheinlicher wird eine gute Antwort. Definitiv ist es wichtig, jede Antwort kritisch zu hinterfragen, Belege einzufordern und zu prüfen. Mit Hilfe der KI näherte ich mich den Themen und erwarb ein Grundverständnis. Mit der KI lernte ich NICHT tiefere Kenntnisse der Konfiguration verschiedener Dienste unter Linux. Die KI spuckt einen Terminalbefehl aus, drei Zeilen lang und mit vielen Variablen, Optionen versehen ist und ich kopierte diese Befehle. Aber ich ließ mir immer ausführlich erklären warum und wofür eine Konfiguration oder ein Dienst nötig ist. KI ist ein mächtiges Assistenzsystem, mit dem man viel lernen kann. Man kann aber auch einfach schnell sein und nicht hinterfragen, es wird schon irgendwie laufen. Das mag ich persönlich nicht. Ich fühle mich nicht wohl, wenn ich nicht grundlegend verstehe, was ich mache und mich nicht bewusst entscheide. Ich habe schnell festgestellt, dass es sich lohnt, lange und ausführlich zu fragen. Die KI kennt keine Ungeduld und keine dummen Fragen!!! Andersherum war ich noch nie um dumme Fragen verlegen ;-)

### Netzwerkarchitektur

### Netzwerkarchitektur

Der Home-Server ist kabelgebunden in das lokale Netzwerk eingebunden. Die interne IP-Adresse wird über DHCP vom Router vergeben und ist dort dauerhaft für den Server reserviert.

Mehrere Desktop- und Mobilgeräte greifen auf die Nextcloud-Instanz zu. Der Zugriff ist sowohl innerhalb des Heimnetzes als auch von außen möglich.

Externe Anfragen erreichen zunächst den Router und werden über die erforderlichen Portfreigaben an einen Reverse Proxy auf dem Server weitergeleitet. Der Reverse Proxy nimmt die verschlüsselten HTTPS-Verbindungen entgegen und leitet die Anfragen abhängig von der verwendeten Adresse an den passenden Dienst weiter.

Die Administration des Servers erfolgt innerhalb des lokalen Netzwerks über eine verschlüsselte SSH-Verbindung.

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

Nextcloud bietet verschiedene Funktionen und Dienste. Es gibt einen App-Store, der nützliche Erweiterungen bietet. Ich nutze aktuell:

- **Datei-Synchronisierung:** Dateien lassen sich über eine Weboberfläche oder eine App auf meinem Nextcloud-Benutzerkonto auf dem Home-Server ablegen. Die Nextcloud-Clients legen auch fest, welche Daten offline verfügbar sind und welche Daten auf dem Server bleiben. Damit behalte ich im Alltag meine Daten verfügbar auf dem Smartphone, Laptop und meinem Schreibtischrechner. Da ich auf dem Laptop sämtliche Daten in die Cloud synchronisiere, entsteht auf meinem Hauptrechner automatisch eine Sicherung neuer Daten, die auf dem Laptop anfallen. Ein praktischer Nebeneffekt, der natürlich nicht die regelmäßige Datensicherung ersetzt. Auf dem Smartphone gehe ich deutlich sparsamer mit Offline-Daten um. Hier ist es praktisch, regelmäßig den Cache zu leeren und über die Optionen sehe ich auf einen Blick, welche Dateien offline gespeichert sind. Das ist wichtig, um die Speicherbelegung des Smartphones im Griff zu behalten.

- **Benutzerverwaltung:** Als Nextcloud-Admin kann ich verschiedene Benutzerkonten mit Benutzerkonten anlegen und Berechtigungen vergeben. So hat jeder Benutzer seinen eigenen Bereich für Daten und Dienste. Untereinander sind Datei- und Ordnerfreigaben möglich.
- **Freigaben:** Datei- und Ordnerfreigaben sind mit unterschiedlichen Rechten zwischen Benutzern möglich, aber auch öffentliche Freigaben sind machbar. Lesen, Erstellen, Ändern, Löschen, Weiterteilen sind die Rechte, die vergeben werden können.
- **automatischer Fotoupload vom Smartphone:** Speicherplatz auf Smartphones ist teuer und begrenzt und bei uns in der Familie belegen Fotos immer einen Großteil des Speichers. Ist die Nextcloud-App auf dem Smartphone installiert, bietet die Fotos-App einen automatischen Upload der Foto- und Videomediathek an. Das bringt zwei Vorteile. Zum einen sind die Fotos direkt als Kopie gesichert und zum anderen kann ich auf dem Smartphone einfach und schnell Fotos löschen und Speicher freigeben.

- **Notizen (im Test):** Notizen sind mein Alltagshelfer und ich mache mir ständig Notizen aller Art. Entsprechend ist die Verfügbarkeit sehr wichtig für mich. Mittlerweile habe ich alle meine Notizen nach Nextcloud umgezogen und nutze nur noch diesen Dienst.

- **Passwort-Verwaltung (im Test):** Ich nutze generell einen Passwortmanager. Ohne diesen näher zu benennen, muss ich kaum betonen, dass die Sicherheit und die Funktion sehr, sehr wichtig sind. Bevor ich hier einen kompletten Umzug mache, werde ich den von Nextcloud angebotenen Dienst näher beleuchten und ausführlich testen. Das habe ich bis jetzt nicht getan. Prinzipiell ist die App am Handy installiert und über die Weboberfläche ist der Passwortmanager am PC zugänglich.

- **Kontakte (im Test):** Von einem Umzug meiner Kontakte zu Nextcloud verspreche ich mir eine betriebssystemübergreifende Nutzung. Der Praxistest steht hier noch aus.

- **Integriertes Office:** Mit Collabora bietet Nextcloud eine Office-Lösung, die auf dem Server als Anwendung läuft. Pluspunkt ist natürlich, dass es damit möglich ist, eine Vielzahl an Dokumenten von unterwegs zu lesen und zu bearbeiten. Auf dem Smartphone versuche ich das ehrlicherweise zu vermeiden. Ob die Office-Anwendungen mit MS Office oder dem Apple-Office mithalten können, welche Hürden die Kompatibilität mit sich bringt, kann ich bis jetzt nicht wirklich gut beurteilen. Für die einfachen Dinge funktioniert es gut.

Zukünftig ist für mich interessant

- **KI-Erweiterung/Integration**
- **E-Mail-Server**

### Sicherheitsmaßnahmen

Ja, das Thema Sicherheit ist das Wichtigste. Während Dienste mal nicht funktionieren dürfen oder irgendwelche nervigen Sync-Probleme mit ständigen Time-Out-Meldungen nerven können, sollte sicherheitstechnisch nichts schiefgehen, da es um meine persönlichen Daten geht. Das Thema KI und Sicherheit sollte man auch mit Vorsicht genießen. Nicht jede Empfehlung und Anleitung, die die KI vorschlägt, ist automatisch sicher! Ein Thema, das in Bezug auf Vibe-Coding für Anfänger, also das klassische Beispiel des jungen Unternehmers, der mit Vibe-Coding seine Internetpräsenz aufbaut und später stellt sich heraus, dass die Kundendatenbank offen im Netz lag. DSGVO-Horror-Szenario. An anderen Stellen lese ich, ein offener Port am Router wie es für meinen Home-Server nötig ist, ist generell ein Einfallstor und dort draußen scannen Bots ständig und suchen solche Tore.

Nextcloud Sicherheitsmaßnahmen

- Zwei-Faktor-Authentifizierung [noch nicht eingerichtet]
- Admin-Konto: nur ich bin Admin und kann Einstellungen vornehmen und Berechtigungen vergeben.
- Anmeldung: nach fünf fehlgeschlagenen Anmeldeversuchen wird die IP-Adresse für 24 Stunden gesperrt. Ich erhalte eine automatische Benachrichtigung über jeden fehlgeschlagenen Anmeldeversuch. Ein Skript ermittelt den Standort der IP-Adresse. Technisch sieht das so aus:
fehlgeschlagene Logins werden als LOG-File gespeichert. fail2ban-jail wertet die Einträge aus. ein Cronjob läuft alle 10 Minuten: Abfrage nach aktiven Sperren Zähler wird mit vorigem Durchlauf verglichen Nextcloud-Log wird ausgelesen IP fehlgeschlagener Logins wird ermittelt Geolokalisierung wird online abgefragt Status und Meldung an KUMA

- Zertifikate
- Port 443, HTTPS
- max. 5 Anmeldeversuche -> Sperrung der IP
- Mit Uptime Kuma habe ich ein Tool entdeckt, das es ermöglicht, Regeln und Abfragen zu erstellen und diese zu überwachen. Es lassen sich Zugriffe und Anmeldeversuche überwachen. Sicherheitsrelevante Systemupdates müssen regelmäßig geprüft und eingespielt werden. Auch dabei hilft Kuma. Passwörter: Ich nutze einen Passwortmanager und lasse mir dort Passwörter generieren. Standardpasswörter verwende ich nicht, genauso wenig habe ich ein Passwort für mehrere Konten.

### Backup- und Wiederherstellungskonzept

Aus zwei einfachen Gründen benötigt es ein Konzept für Backups.
1. Ich habe keine Lust wieder von vorne anzufangen, falls meine Systemplatte abraucht!
2. Ich möchte meine Daten nicht verlieren!<br

#### Backups

Zum Thema Backup habe ich folgendes Konzept für meinen Fall entwickelt. Der Server hat eine System-SSD und eine über USB angeschlossene Daten-HDD. Grob gesagt wird das System regelmäßig auf einen gesonderten Ordner auf der Daten-HDD gesichert. Und die komplette Daten-HDD wird über das Netzwerk auf einer weiteren Festplatte gesichert. Der ganze Prozess läuft mittlerweile automatisiert zu festen Zeiten ab. Ältere Backups werden nach einer definierten Regel aufbewahrt beziehungsweise automatisch gelöscht. Insgesamt werden Backups über 24 Monate gespeichert. Wohlgemerkt meine ich damit die Systembackups. Die Daten werden immer nur so gesichert, wie sie auch aktuell auf dem Datenspeicher liegen. Im Folgenden gehe ich auf ein paar Details ein. Im Detail sieht die Strategie also so aus:

### Stufe 1: Lokales Backup auf die USB-Festplatte

Das lokale Backup sollte täglich folgende Inhalte sichern:

- konsistenter Dump der Nextcloud-MariaDB-Datenbank
- Docker-Compose-Dateien
- Nginx-Konfiguration
- Collabora-Konfiguration
- Nextclouds config.php
- Let's-Encrypt-Konfiguration
- DDNS- und WOPI-Automatisierung
- systemd-Dienste und Timer
- das Backup-Skript selbst

### Stufe 2: Netzwerkkopie auf den Hauptrechner

Die gesamte USB-Festplatte sollte zweimal pro Woche auf einen Freigabeordner des Hauptrechners kopiert werden:

Ein Skript prüft vor Beginn immer:
1. ob die lokale USB-Quellplatte korrekt eingehängt ist
2. ob das Netzlaufwerk korrekt gemountet ist

Ist der Hauptrechner ausgeschaltet schlägt das Backup fehl und eine Benachrichtigung wird ausgelöst.

## Aufbewahrung

Um zu entscheiden was wie lange aufbewahrt wird, überlegte ich folgendes. Wie groß würden die Konfigurations-Backups sein und auf welche Gesamtgröße würde das hinauslaufen? Klar war sofort, dass ich die eigentlichen Daten nicht noch mit Zeitstempeln doppelt und dreifach speichern würde. Ich entschied mich für folgende Variante:

Tägliche Sicherungen: 14 Tage aufbewahren

Monatssicherungen: erste erfolgreiche Sicherung eines Monats zusätzlich archivieren

Monatssicherungen: 24 Monate aufbewahren

daraus ergeben sich diese Vorteile:

- tägliche Wiederherstellung für die letzten zwei Wochen
- monatlicher Rückgriff über zwei Jahre
- überschaubarer Speicherbedarf

Zum Zeitpunkt der Einrichtung hatte eine vollständige Sicherung ungefähr folgende Größe:

Komprimierter MariaDB-Dump: etwa 12 MB Konfigurationsarchiv: etwa 127 KB

Bei unveränderter Größenordnung ergeben sich ungefähr:

14 tägliche Sicherungen:  etwa 170 MB 24 Monatssicherungen:     etwa 290 MB Gesamt:                   etwa 460 MB

Selbst bei deutlich wachsender Datenbank bleibt der Speicherbedarf im Vergleich zu den produktiven Daten gering.

#### Praktische Umsetzung

Um eine saubere Dateistruktur zu erhalten, musste ich auf der Daten-HDD erst einige Änderungen durchführen. Ursprünglich war Nextcloud so konfiguriert, dass es die Daten-HDD komplett, also ab dem Root-Verzeichnis, für seine Verzeichnis-Strukturen nutzt. Füge ich in diese Struktur einen weiteren Ordner für Backups ein, beeinträchtigt das das System nicht unbedingt negativ, aber der Ordner wird eventuell in die Indizierung eingeschlossen. Sauber ist also eine Trennung. Dafür war nicht viel nötig. Ich habe also im Root-Verzeichnis schlicht einen Ordner für die Nextcloud-Daten erstellt und alle Daten von Nextcloud dorthin verschoben. Nextcloud selbst muss das natürlich auch wissen und die Konfigurationsdateien entsprechend aktualisiert werden.

Für die lokale Sicherung wurde ein Skript eingerichtet, das täglich einen konsistenten Dump der MariaDB-Datenbank erstellt. Dieser enthält die Konten, Freigaben, Dateizuordnungen und weiteren Nextcloud-Metadaten, jedoch nicht meine eigentlichen Benutzerdateien. Weitere Konfigurationen, die gesichert werden, sind: Docker-Compose-Dateien Nextcloud-Konfiguration Nginx-Konfiguration Collabora-Konfiguration TLS-Zertifikate DDNS- und WOPI-Konfiguration systemd-Dienste und Timer Backup-Skripte

Die Ausführung erfolgt über einen systemd-Timer und Uptime Kuma hilft bei der Überwachung.

In der zweiten Sicherungsstufe wird die gesamte USB-Festplatte auf einen Freigabeordner auf meinen Hauptrechner kopiert. Ein Skript prüft, ob die Festplatte eingehängt ist und ob der Freigabeordner im Netzwerk erreichbar ist. Die Übertragung läuft über rsync. Die erste Übertragung dauert länger, weil alle Daten übertragen werden. Spätere Durchläufe sind inkrementell. Das heißt nur neue und geänderte Daten werden übertragen.

Nextcloud bleibt während der Backups erreichbar und geht nicht in den Wartungsmodus. Die Datenbank wird dennoch konsistent exportiert. Dateien, die während des Backups verändert werden, werden beim nächsten Backup übertragen. Ein vollständiges Backup würde einen Snapshot des Systems erfordern oder das Versetzen von Nextcloud in den Wartungsmodus. Das wäre natürlich möglich und würde sich nachts anbieten. Nachts läuft mein Rechner aber in der Regel nicht und damit würden diese Backups regelmäßig ins Leere laufen.

1-2-3 Regel: Nach dieser Regel sollte ich noch ein Backup außer Haus haben. Das gibt es zurzeit noch nicht. Ich habe Ideen dazu, wie sich das günstig und unkompliziert realisieren ließe und kann vielleicht in Zukunft darüber berichten.

### Monitoring und Wartung

Wie bereits kurz beschrieben nutze ich Uptime Kuma für das Monitoring. Der Dienst läuft in einem Container in Docker. Folgende Monitore habe ich mir eingerichtet.

- - Benachrichtigung über fehlgeschlagene Anmeldeversuche inkl. Standort
- Benachrichtigung falls sicherheitsrelevante Updates für Ubuntu verfügbar sind.
- - Benachrichtigung ob Container und Cronjob laufen
- - Benachrichtigung falls Nextcloud-Dienst nicht online/erreichbar
- - Benachrichtigung falls die Festplattenbelegung über 75% ansteigt.

### Probleme und Lösungen

### Was ich dabei gelernt habe

### Mögliche zukünftige Erweiterungen

### Praktische Erfahrungen

- Hardware ist schnell und ausreichend für die kleine Anzahl an Benutzern in der jetzigen Konfiguration
- Lokale KI-Nutzung nicht möglich! Hierfür braucht es zwingend moderne Hardware.
- Synchronisation ist sehr schnell. Geräteübergreifend sind Änderungen fast augenblicklich sichtbar.
- Nextcloud läuft sehr stabil und zuverlässig. Noch keine Abstürze
- No-IP. Der Dienst für die kostenlose Nutzung gut geeignet. Einzig hatte ich mehrmals das Problem, dass mein Server nicht erreichbar war. Meine IP hatte sich geändert und irgendwas bei der Kommunikation zwischen Router und dem Dienst von No-IP lief schief. Schnelle Abhilfe war immer die IP manuell bei No-IP zu ändern. Im privaten Kontext erst einmal kein Problem, wenn man weiß wo die Ursache liegt. Zwischenzeitlich habe ich das aber umgestellt auf eine `.de`-Domain und der Server gleicht regelmäßig die IP-Adresse ab um die Erreichbarkeit zu gewährleisten.

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
### TESTÜBERSCHRIFT