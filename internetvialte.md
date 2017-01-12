# Internetverbindung über einen LTE-Router herstellen

Ein lokales Netzwerk soll über den LTE-Router mit dem Internet verbunden werden.

## Kurzanleitung für das Web-Interface

Es wird vorausgesetzt, dass sich das Gerät in Grundeinstellungen befindet und in Slot 2 ein LTE-Modem eingesteckt ist.

1. Web-Interface des Routers mit einem Browser aufrufen:

* http://192.168.1.1
* Benutzername: insys
* Kennwort: icom

2. Im Menü "Interfaces -> Slot 1 Ethernet" die Ports 1.3, 1.4 und 1.5 dem Interface "net2 LAN" zuordnen

3. Im Menü "Interfaces -> Slot 2" die PIN eintragen und den APN eintragen

4. Im Menü "Interfaces -> IP-Netz2 (net2)" das Netzwerk aktivieren und eine IP-Adresse hinzufügen, die der Router im LAN erhalten soll (PLUS-Symbol).

5. Im Menü "WAN -> WAN-Ketten" eine neue WAN-Kette hinzufügen (PLUS-Symbol).

6. Im Menü "WAN -> WAN-Ketten" der neuen WAN-Kette ein neues Interface hinzufügen.

7. Im Menü "Routing -> statische Routen" eine neue Route hinzufügen und bearbeiten (STIFT-Symbol):

* "Anlegen nach Start von lte2"
* "Art der Route: Default-Route"
* "Gateway: dynamisch erhaltene IP-Adresse verwenden"

8. Im Menü "Netzfilter -> NAT" bei ""Source-NAT" eine neue Regel hinzufügen und bearbeiten:

* "Typ: Masquerade"
* "Protokoll: Alle"
* "Ausgehendes Interface: lte2"

9. Profil aktivieren durch Klick auf das blinkende Zahnrad in der Titelleiste

## Hinweise zur Kurzanleitung

1. In den Grundeinstellungen ist das Gerät ausschließlich über die erste Ethernet-Buchse erreichbar. Dadurch wird vermieden, dass bei einem Zurücksetzen auf Werkseinstellungen bei einem bereits konifgurierten Router nicht versehentlich mehrere Netzwerke gebridged werden.

2. Die Switch-Ports müssen erst einem Interface zugeordnet werden. In diesem Fall bleibt das Interface "net1" ein eigenes Netzwerk, das nur zum Erreichen und Konfigurieren des Routers dient. Das "net2" soll die am Router angeschlossenen Geräte mit dem Internet verbinden. Der Router und dessen Konfgurationsoberfläche kann in dieser AN sowohl von "net1" als auch von "net2" erreicht werden.

3. Wenn die eingelegte SIM-Karte keine PIN benötigt, darf das Feld freigelassen werden. Der APN (Access Point Name) ist wichtig. Wird kein oder der falsche APN koniguriert, kommt unter Umständen keine oder nur eine 3G-Verbindung zustande. Gegebenenfalls muss sich das Modem beim Provider auch noch mit Benutzername und Kennwort authentifizieruen. Die SMS Center Nummer muss in der Regeln nicht eingetragen werden, weil sie normalerweise schon in der SIM-Karte gespeichert ist. Sie wird nur für den Versand von SMS benötigt.

4. Das IP-Netz 2 ist ein Interface, das in der Betriebsart "lokal" immer sofort gestartet wird, wenn der Router gestartet wird. Es können mehrere statische IPv4- und IPv6-Adressen für dieses Interface konfiguriert werden.

5. In einer WAN-Kette werden die Interfaces nacheinander aufgereiht. Die Interfaces in der WAN-Kette werden versucht nacheinander in den Zustand "online" zu bringen. Jedes Interface kann optional durch einen Verbindungscheck versehen werden, das den Zustand "online" bestätigt. Scheitert der Verbindungscheck oder erkennt der Router selbständig, dass ein Interface in einer WAN-Kette nicht mehr den Zustand "online" hat, wird der Aufbau der WAN-Kette als gescheitert betrachtet. Die nächste WAN-Kette wird versucht zu starten. Ist keine weitere WAN-Kette vorhanden, werden alle Interfaces in der aktuellen WAN-Kette in den Zustand "offline" gebracht und beginnend mit dem ersten Interface wieder nacheinander gestartet. Wenn eine zweite WAN-Kette vorhanden ist, wird geprüft, ob die Interfaces beginnend vom Start der WAN-Kette identisch sind. Ist das der Fall und die Interfaces sind nicht der Grund für den Wechsel der WAN-Kette, so bleiben sie im Zustand "online". Es werden nur die Interfaces neu gestartet, deren Verbindungsprüfung fehl schlug und alle darauf folgenden.

6. Bei eine Interface einer WAN-Kette erkennt der Router in der Regel selbständig, ob es im Zustand "online" ist oder nicht. Ein zusätzlicher Verbindungscheck kann aber trotzdem sinnvoll sein. Bei Ethernet-Interfaces ist der Ethernet-Link nur ein unzureichendes Merkmal dafür, ob auch IP-Verkehr möglich ist. Ein DNS-Check oder ein ping an eine bestimmte IP-Adresse liefert eher Gewissheit, dass bestimmte Ziele erreicht werden kann. Eine Internetverbindung über LTE ist gegebenfalls unbrauchbar, wenn die DNS-Auflösung nicht mehr funktioniert. In solchen Fällen kann es sinnvoll sein, die WAN-Kette zu wechseln und die Internetverbindung über ein anderes Medium aufzubauen oder die bestehenden Interfaces neu zu starten.

7. Beim Start eines Interfaces (lokal oder in einer WAN-Kette) wird immer geprüft, ob für dieses Interface eine Route konfiguriert ist. Die Art der Route kann eine Default-Route (0.0.0.0/0), eine Netzwerk-Route (z.B. ins Netz 172.16.0.0/16) oder eine Host-Route (z.B. 172.16.7.4/16) sein. Das Gateway, das für das die neue Route führen soll, wird in diesem Fall vom Modem dynamisch erhalten. Eine statische IP-Adresse ist nur bei wenigen Providern bekannt. Alternativ könne man bei einem LTE-Modem auch das Interface "lte2" als Gateway für die Route eintragen. Ein IP-Paket wird über die Route gesendet, dessen Netzmaske am Genauesten ist, also /32 bei IPv4 und /128 bei IPv6). Die Prioriät der Route definiert, welche Route genutzt werden soll, wenn mehreren Routen mit identischer Reichweite angelegt sind, wobei die niedigste Zahl bei der Angabe der Priorität die Route definiert, die am ehesten benutzt werden soll. Auf diese Art lassen sich mehrere Default-Routen zugleich anlegen.

8. Die meisten Provider verteilen private IP-Adressen an die sich zum Netz verbindenden Modems, weil es nicht mehr genügend freie öffentliche IPv4-Adressen gibt. Damit die IP-Pakete, die vom LAN ins Internet geschickt weden, ihren Weg aus dem Internet wieder zurück in das richtige LAN finden, muss der Router die Absender-IPv4-Adresse mit der IP-Adresse ersetzen, die das Modem vom Provider dynamisch erhalten hat. Dieser Vorgang heißt Masquerading. Das ist nicht notwendig, wenn der Provider öffentlich erreichbare IP-Adressen vergibt. In dem Fall ist es unbedingt wichtig, die IP-Filter zu aktivieren und so den zugelassenen IP-Traffic zu reglementieren.

9. Alle Einstellungen werden in Profilen gespeichert. Erst das Aktivieren eines Profils bewirkt, dass der Router die Einstellungen auch übernimmt und ausführt.
    Nachdem das Profil aktiviert wurde, beginnt die WAN-LED grün zu blinken. Sie zeigt an, dass die Interfaces (in diesem Fall nur lte2) der WAN-Kette noch nicht alle online sind.
    Die bisher grün blinkende Signal-LED sollte nach einiger Zeit orange blinken. Die Blinkfrequenz zeigt die Signalqualität an. Die Farbe zeigt an, ob das Modem eingebucht aber noch offline ist (grün), oder ob es online ist (orange).
    Nachdem die Signal-LED orange blinkt, leuchtet die WAN-LED dauerhaft grün: Alle Interfaces der WAN-Kette sind nun online.

## Troubleshooting

* Der Zustand der WAN-Kette und deren Interfaces wird im Webinerface im Menü "Status" dargestellt. Erreicht ein Interface nicht den Zustand "online" kann dessen Zustand ebenfalls dort im Menü untersucht werden.
* Kommt kein Netzwerkverkehr zustande, können die im Router integrierten Werkzeuge zum Debugging benutzt werden: (LINK zu debugging mit integrierten Tools)
* Am Router angeschlossenen Netzwerkgeräte können die Internetverbindung nicht nutzen können. Das kann daran liegen, dass sie den Router noch nicht als Gateway und als DNS-Server eingetragen haben.
* Zu Testzwecken könenn die IP-Filter deaktiviert werden. Auch der Router selber unterliegt den IP-Filtern. Muss der Router selber Pakete absenden, wird dafür ein OUTPUT-Regeln benötigt. Soll der Router selber Pakete empfangen dürfen, wird eien INPUT-Regel benötigt. Um IP-Pakete an routen zu dürfen, ist eine FORWARD-Regel notwendig.

## Konfiguration herunterladen

* ASCII-Format
* Profil
