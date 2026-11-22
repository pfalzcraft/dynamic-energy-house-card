# Energy House Card

Die Standardansicht zeigt einen eingerichteten, gerenderten Hausschnitt. Dachmodule, Fahrzeug, Speicher und Messpunkte werden passend zur Konfiguration eingeblendet. Leitungen erscheinen nur für Strecken, die im Builder mit einem Leistungssensor angelegt sind. Leistungsimpulse laufen in die gemessene Richtung. PV und Hauslast stehen direkt am Gebäude. Unter der Szene öffnet „Geräte und Messwerte“ die vollständige Übersicht.

Hintergrund, Kacheln, Schaltflächen, Schrift und Textfarben folgen dem in HA gewählten Theme (hell oder dunkel). Nur die Wert-Labels im Bild bleiben dunkel, damit sie auf dem Foto lesbar sind.

Im Builder kann zwischen Architekturansicht und schematischem Etagenplan gewechselt werden. Das Architekturmotiv hat zwei sichtbare Wohnetagen. Zu jedem Modul lassen sich die Koordinaten seines Messpunkts in Prozent eintragen, gemessen von der oberen linken Ecke. Leere Felder verwenden die Standardposition. Raumzuordnungen ändern den Messpunkt und die Detailansicht. Für eine dynamische Geschossaufteilung den Etagenplan wählen. Das Architekturmotiv zeigt maximal zwei Speichergehäuse und ein Fahrzeug, zusätzliche Geräte bleiben über die Liste zugänglich. Die Architekturansicht legt über das feste Hausmotiv Tageszeit (Tag, Dämmerung, Nacht nach Sonnenhöhe), Himmel mit Sonne, Mond und Sternen, ziehende Wolken nach Bewölkungsgrad und Wind sowie Regen, Schnee, Hagel, Gewitterblitze und Nebel. Nachts bleibt der beleuchtete Hausschnitt hell, die Umgebung wird dunkel.

Bildstil im Editor: „3D · Haus mit Keller“ (Standard; Jahreszeit nach Datum, Tag/Nacht nach Sonnenstand mit Überblendung in der Dämmerung. Dafür die Bilder `energy-house-card-*.webp` neben der Kartendatei ablegen (HACS erledigt das), ohne sie erscheint immer das Sommerbild. PV, Strommast, Auto, Speicher, Wechselrichter, Wasseruhr und Wärmepumpe erscheinen nur, wenn das Modul angelegt ist; die Wärmepumpe über die Geräte-Option „Als Wärmepumpe zeigen“), „Blueprint · Satteldach mit Keller“, „Blueprint · Flachdach mit Keller“ (neonfarbene Linienzeichnung, Räume mit Etage UG erscheinen im Keller) oder „Eigenes Bild“ mit einer URL wie `/local/haus.webp` (Datei in `/config/www/`, Format 3:2, Module per Bildpunkt X/Y platzieren).

## In Home Assistant installieren

### Über HACS (empfohlen)

1. HACS öffnen → Menü (⋮) → „Benutzerdefinierte Repositories“.
2. `https://github.com/pfalzcraft/energy-house-card` als Typ „Dashboard“ hinzufügen.
3. „Energy House Card“ suchen, herunterladen und die Seite neu laden.
4. Dashboard bearbeiten → Karte hinzufügen → „Energy House“ suchen. Alternativ die YAML-Karte aus `examples/minimal.yaml` einfügen.

HACS legt die Ressource `/hacsfiles/energy-house-card/energy-house-card.js` selbst an. Eine früher manuell angelegte Ressource `/local/energy-house-card.js` danach entfernen.

### Manuell

1. Alle Dateien aus `dist/` (`energy-house-card.js` und die Bilder `energy-house-card-*.webp`) nach `/config/www/` kopieren. Falls `www` erstmals angelegt wurde, Home Assistant neu starten.
2. Mit einem Administratorkonto unter Einstellungen → Dashboards → Ressourcen `/local/energy-house-card.js` als JavaScript-Modul hinzufügen. Seite neu laden.
3. Karte wie oben hinzufügen.

Im Editor blendet „Vorschau-Knöpfe“ unter dem Bild Schaltflächen ein, mit denen sich Tageszeit, Wetter und Jahreszeit ausprobieren lassen.

## Haus aufbauen

Im Karteneditor zuerst dem Netzanschlusspunkt seinen Leistungssensor zuordnen. Der Hausverbrauch wird berechnet. Unter „Modul hinzufügen“ PV, beliebig viele Speicher, Fahrzeuge, Räume und Messwerte ergänzen. HA-Bereich und Gerät schränken die Vorschlagsliste ein. Entitäts-IDs lassen sich auch direkt eintragen. Die Zuordnung ist kartenspezifisch. Sie verändert das Geräteverzeichnis in HA nicht.

Im schematischen Etagenplan für ein Haus mit zwei Geschossen und Keller „UG“, „EG“ und „OG“ an den Raummodulen wählen. Die Geschosse wachsen von unten nach oben, je Reihe passen vier Räume. Mehr Räume werden auf eine weitere Reihe verteilt. Freie Etagenbezeichnungen folgen den Standardetagen. Die Szene ist ein schematischer Schnitt. Raummaße und Grundrisskoordinaten sind nicht Teil dieser Version.

| Modul | Messwert | Zusätzliche Zuordnung |
|---|---|---|
| Haus | Kein Sensor, wird berechnet: PV + Netzbezug − Einspeisung + Speicherentladung − Speicherladung − Fahrzeugladung | Live aus den Leistungssensoren, im Zeitraum aus den Energiezählern. Live-Leistung eines Speichers = Summe seiner Strecken mit Leistungssensor. Fehlt ein Wert (Sensor nicht eingetragen oder nicht verfügbar), rechnet die Karte ohne ihn und nennt ihn unter „Ohne Wert“. |
| Netz | Bidirektionale Leistung, positiv bei Bezug; Energiezähler für Bezug und Einspeisung | Vorzeichen im Builder umkehrbar. Das Label im Bild zeigt den Bezug mit mdi:transmission-tower-export (Pfeil vom Mast weg); im Zeitraum steht die Einspeisung (mdi:transmission-tower-import) darunter, sobald etwas eingespeist wurde, bei „Jetzt“ wechselt das Icon mit der Richtung |
| PV | Erzeugungsleistung, positiv bei Erzeugung | Ein gemeinsamer PV-Knoten |
| Speicher | Ladestand in %, Energiezähler „Geladen“ und „Entladen“ (Wh/kWh) | Steuerentität für den Modus. Die Karte zeigt Ladestand sowie geladene und entladene Energie im gewählten Zeitraum. |
| Fahrzeug | Ladeleistung, positiv beim Laden; optional Energiezähler „Geladen“, optional Batterieinhalt (kWh, oder % plus Akkugröße) | Je Fahrzeug Ladestand und Wallbox-Modus. Mit Ladestand zeigt das Label im Bild den %-Wert. Aus dem Verlauf des Batterieinhalts (stündliche Mittelwerte der Statistik) berechnet die Karte geladene und gefahrene Energie im Zeitraum; Anstiege zählen als geladen, Abfälle als gefahren (inkl. Standverbrauch). Ein eigener Ladezähler hat Vorrang. |
| Gerät | Verbrauchsleistung, optional Energiezähler (z. B. Shelly 1PM) | Beliebig viele. Teil des Hausverbrauchs, fließt nicht in dessen Berechnung ein. Die Kachel zeigt den Anteil am Hausverbrauch. Im Bild teilen sich Speicher, Fahrzeuge und Geräte je ein Label. Geräte mit Darstellung (Wärmepumpe, Waschmaschine, Trockner) bekommen ein eigenes Label mit passendem Icon an ihrem Platz im Keller. Die Hauskachel zeigt, welcher Teil des Hausverbrauchs keinem Gerät zugeordnet ist. Unter „Geräte und Messwerte“ listet die Karte zusätzlich alle Leistungssensoren aus HA (W, kW, MW), sortierbar nach Wert oder Name und einklappbar. Sensoren, die die Karte noch nicht nutzt, sind ab 300 W orange und ab 1 kW rot markiert; ein Tipp öffnet den HA-Dialog. Mit ✕ wird ein Sensor ausgeblendet; die Auswahl speichert HA pro Benutzer (gilt auf allen Geräten), „Ausgeblendete“ zeigt sie zum Wiedereinblenden. Unter der Liste vergleicht eine Prüfsumme die Summe der Verbraucher mit dem Hausverbrauch. Quellen (Netz, PV, Speicher, Fahrzeuge, Strecken) und negative Werte zählen nicht mit. |
| Wasser | Zählerstand (m³/L) oder Durchfluss | Zähler werden an `state_class` (total/total_increasing) oder der Einheit erkannt: im Zeitraum der Verbrauch, bei „Jetzt“ der Durchfluss in L/min als Mittel der letzten 10 Minuten |
| Raum | Beliebiger Raumsensor, z. B. Temperatur | Etage, HA-Bereich und beliebig viele Entitäten oder Gruppen (Licht, Rollladen, group, Klima, Sensoren). Das Label zeigt je Entität eine Zeile untereinander (Lichter an, mittlere Rollladenposition, Temperatur); ein Tipp auf eine Zeile öffnet den HA-Dialog der Entität, ein Tipp auf den Raumpunkt die Schaltknöpfe |
| Netz-CO₂ | Fossiler Anteil des Netzstroms in % (Integration „Electricity Maps“, wie im HA-Energie-Dashboard), optional CO₂-Intensität | Zeigt, wie viel des Hausverbrauchs im Zeitraum CO₂-arm war: Hausverbrauch minus Netzbezug × fossiler Anteil. Braucht den Bezugszähler am Netz. Rechnet mit dem aktuellen fossilen Anteil, nicht stundengenau. |

Getrennte Bezugs- und Einspeisesensoren lassen sich über einen HA-Templatesensor zusammenfassen (Bezug minus Einspeisung, gleiche Einheit). Bei fehlendem Eingang muss der Templatesensor unavailable liefern. kWh-Zähler sind keine Momentanleistung. Unter 5 W bleibt die Animation stehen, der Betrag bleibt sichtbar. Fehlende Leistungswerte erscheinen als Strich.

### Wie die Stromwege zu lesen sind

Die Mitte des Schemas ist das elektrische Hausnetz. Die Pfeile zeigen für jeden Anschluss Bezug oder Abgabe. Liefert die PV und laden zwei Speicher, zeigen alle drei Zweige ihre gemessene Richtung. Ein Netzbezug zur selben Zeit macht die Herkunft einzelner Ladeleistungen mehrdeutig. Der Builder erlaubt deshalb zusätzliche gerichtete Strecken mit eigenem Leistungssensor, zum Beispiel PV → Speicher, Speicher → Haus, PV → Netz und Netz → Haus. Jede Strecke hat je Richtung ein Leistungsfeld (Von → Nach und optional Nach → Von). Mit nur einem Sensor ist sie einseitig, mit beiden zeigt der größere Wert die Richtung, etwa Laden oder Entladen eines Speichers. Eine negative Streckenleistung dreht den Pfeil ebenfalls um. Bei 0 W oder fehlender Messung zeigt die Zeile die konfigurierte Bezugsrichtung mit ruhender Linie. Die Karte verteilt Überschuss nicht rechnerisch auf einzelne Strecken. Fahrzeugladung wird vom Hausverbrauch abgezogen.

Strecken im Bild verlaufen rechtwinklig: senkrecht aus dem Modul, waagrecht, senkrecht ins Ziel. Die Höhe der waagrechten Linie lässt sich je Strecke im Editor als Y in % festlegen (leer = Mitte). Strecken dürfen sich Linien teilen.

### Wetter und Bedienung

Die Zeitraumleiste wählt Jetzt, Heute, Woche, Monat, Jahr oder „Zeitraum“. „Jetzt“ zeigt überall die Momentanleistung, alle anderen Zeiträume die Energie im Zeitraum aus den Energiezählern. Ein Modul ohne Energiezähler zeigt auch dort die Momentanleistung. Der Startzeitraum der Karte ist im Editor wählbar. Bei „Zeitraum“ erscheinen zwei Datumsfelder (Von/Bis, jeweils ganze Tage in der HA-Zeitzone). Ohne Auswahl gelten die letzten sieben Tage. Ist die Energiebilanz des Hauses im Zeitraum negativ, weist die Haus-Kachel darauf hin, dann passt ein Zähler nicht.

Sonne und Mond stehen dort am Himmel, wo sie gerade sind (berechnet aus Uhrzeit und dem HA-Standort, Osten links). Der Mond erscheint nur, wenn er über dem Horizont steht, mit seiner aktuellen Phase (zunehmend rechts, abnehmend links beleuchtet); tagsüber blass, bei dichter Bewölkung gar nicht. Die Höhe im Bild ist relativ zum höchsten Stand des Tages, damit die tiefe Wintersonne über dem Dach sichtbar bleibt. Die Wetterentität steuert Bewölkung sowie Regen- oder Schneepartikel und liefert Temperatur und Wind für die Kopfzeile. Optional lassen sich eigene Sensoren für Windgeschwindigkeit, Windrichtung (Grad oder Text) und Niederschlag eintragen; sie stehen ebenfalls in der Kopfzeile. sun.sun liefert Sonnenhöhe und Azimut für Tageslicht, Dämmerung und Nacht. Ohne Sonnenentität dient die Ortszeit des Anzeigegeräts als Rückfallregel. Die Animation respektiert die Einstellung für reduzierte Bewegung.

Ein Fingertipp auf einen Raum, das Solardach oder einen Messwert öffnet die HA-Detailansicht. Batterieschrank und Auto im Bild öffnen das jeweils erste Gerät, im Flussschema ist jedes Gerät einzeln zugänglich. Bei zugeordneter Steuerentität öffnet sich eine Auswahl unter den Messwerten. Für select und input_select liest sie die tatsächlichen Optionen aus HA und sendet erst nach einem weiteren Tipp select_option. Andere Steuerelemente lassen sich in der regulären HA-Detailansicht bedienen. Die Karte schreibt während des Renderns keine Sollwerte. Wenn eine HA-Automation den Lademodus verwaltet, entscheidet diese weiterhin nach ihren eigenen Regeln.

### Beispiel mit Varta, Anker und Smart Meter

Den Smart Meter am Netzanschlusspunkt dem Modul „Stromnetz“ zuweisen. Varta element und Anker SOLIX Max AC als zwei getrennte Speichermodule eintragen. Am Netzbezug und an einem bekannten Ladevorgang die Vorzeichen prüfen. Für das Anker-Modul kann die bereits vorhandene Entität select.anker_solix_max_ac_operating_mode_device_runs_by_command_in_third_party_controlled als Steuerentität gewählt werden. Sie wählt den Betriebsmodus. Der Richtungswähler select.anker_solix_max_ac_grid_flow liefert keine gemessene Ladeleistung. Alle Leistungssensoren bewusst aus der eigenen Installation auswählen.

## Aufbau des Repositories

dist/energy-house-card.js enthält Custom Element, Renderer, Haus-Builder und Styles ohne externe Laufzeitabhängigkeiten. hacs.json benennt das Frontend-Paket. Es gibt keinen Build-Schritt. Zum Testen die Datei nach /config/www/energy-house-card.js kopieren und das Dashboard neu laden.

Dokumentation: [HA Custom Cards](https://developers.home-assistant.io/docs/frontend/custom-ui/custom-card/) · [HACS Dashboard-Repositories](https://www.hacs.xyz/docs/publish/plugin/).
