<p align="center">
  <picture>
    <source media="(prefers-color-scheme: dark)" srcset="assets/logo-dark.svg">
    <img src="assets/logo-light.svg" alt="MiraiPanel" width="260">
  </picture>
</p>

<p align="center"><strong>Control. Sense. One device.</strong></p>

Der universelle Raumcontroller für deine Loxone-Hausautomation: ein
5,5″-Farbdisplay, acht austauschbare Sensortasten und eine volle
Sensor-Ausstattung — verbunden über LAN/WLAN und MQTT.

<p align="center">
  <img src="assets/panel-angle.png" alt="MiraiPanel im 3/4-Blick mit Now-Playing-Ansicht" height="460">
  <img src="assets/panel-front.png" alt="MiraiPanel Gehäuse mit Now-Playing-Ansicht" height="460">
  <img src="assets/panel-detail.png" alt="MiraiPanel: Sensortasten und Now-Playing-Ansicht im Detail" height="460">
</p>

<p align="center">
  <a href="https://holzmusik.github.io/MiraiPanel/"><img src="https://img.shields.io/badge/Website-holzmusik.github.io%2FMiraiPanel-7ddc4a?style=for-the-badge" alt="Website"></a>
  <a href="https://github.com/Holzmusik/MiraiPanel-LCD"><img src="https://img.shields.io/badge/Firmware-MiraiPanel--LCD-4a9900?style=for-the-badge" alt="Firmware"></a>
  <a href="https://github.com/Holzmusik/LoxBerry-Plugin-MiraiBridge"><img src="https://img.shields.io/badge/Bridge-MiraiBridge-4a9900?style=for-the-badge" alt="Bridge"></a>
  <a href="https://github.com/Holzmusik/MiraiPanel-Hardware"><img src="https://img.shields.io/badge/Hardware-PCB%20%2B%20Geh%C3%A4use-4a9900?style=for-the-badge" alt="Hardware"></a>
  <a href="assets/MiraiPanel-Flyer.pdf"><img src="https://img.shields.io/badge/Flyer-PDF-4a9900?style=for-the-badge" alt="Flyer PDF"></a>
</p>

Dieses Repo ist die **Projektübersicht** — der eigentliche Code liegt in den
verlinkten Teilprojekten oben/unten.

## Was das Gerät macht

Du gehst am Panel vorbei — es merkt das von selbst und wacht auf, ganz ohne
Wischen oder Antippen. Läuft gerade Musik, siehst du direkt Cover, Titel und
kannst pausieren oder überspringen, ohne zum Handy zu greifen. Sonst zeigt
es eine ruhige Uhr, bis du wieder etwas brauchst.

Ein Fingertipp genügt, um Licht, Jalousien oder die Heizung im Raum zu
steuern — die Bedienelemente entstehen automatisch aus deiner
Loxone-Struktur, du musst nichts von Hand verdrahten. Die acht
Sensortasten daneben lassen sich pro Raum frei belegen und beschriften,
zusätzlich zum Touchscreen.

Im Hintergrund misst das Panel laufend Raumklima, Licht und Bewegung und
gibt diese Werte über MQTT weiter — offen für Loxone, aber genauso nutzbar
mit Home Assistant, ioBroker oder jeder anderen MQTT-Umgebung. Eingerichtet
wird alles über eine Weboberfläche direkt am Gerät, Updates kommen
over-the-air.

## Screenshots

<p align="center">
  <img src="assets/screen-overview.jpg" alt="Übersicht: Licht, Jalousie, Audio, Schalter" width="220">
  <img src="assets/screen-weather.jpg" alt="Wetter-Seite" width="220">
  <img src="assets/screen-switches.jpg" alt="Schalter-Seite" width="220">
  <img src="assets/screen-audio.jpg" alt="Audio Now-Playing" width="220">
</p>

### In Bewegung

Manche Details fallen erst im laufenden Betrieb auf, nicht auf einem
Standbild — zum Beispiel der Songtitel-Scrolltext in der Now-Playing-
Ansicht, wenn Interpret und Titel zusammen nicht in eine Zeile passen.
Aufgenommen direkt vom Gerät selbst über den HTTP-Screenshot-Endpoint des
Panels, keine externe Kamera.

<p align="center">
  <img src="assets/detail-nowplaying-scroll.gif" alt="Songtitel-Scrolltext in der Now-Playing-Ansicht" width="220">
</p>

Und einmal am Stück bedient: Theme- und Akzentfarbwechsel in den
Einstellungen, zurück zur Übersicht, Cover-Aufbau in der Audio-Ansicht.

<p align="center">
  <img src="assets/detail-tour.gif" alt="Bedienung am Gerät: Theme-Wechsel, Übersicht, Audio-Cover" width="220">
</p>

### Web-Konfiguration

Eingerichtet wird das Panel über eine Weboberfläche, die direkt am Gerät
läuft — inklusive visuellem Layout-Editor mit Live-Vorschau und einem
Status-Dashboard mit Echtzeitwerten. Im Layout-Editor lassen sich Widgets
aus einem Pool (Licht, Jalousie, Heizung, Schalter, Sensoren, Audio,
Wetter) frei auf die 6 Seiten des Displays verteilen und im Raster
positionieren — nicht benötigte Seiten bleiben einfach leer.

<p align="center">
  <img src="assets/web-layout.png" alt="Layout-Editor mit Live-Vorschau" width="270">
  <img src="assets/web-preview.png" alt="Live-Display-Vorschau" width="270">
  <img src="assets/web-status.png" alt="Status-Dashboard" width="270">
  <img src="assets/web-diagnostics.png" alt="System-Diagnose" width="270">
</p>

### MiraiBridge (LoxBerry-Plugin)

Die Einrichtung auf LoxBerry-Seite läuft genauso über eine Weboberfläche —
Panels anlegen, Räume aus der Loxone-Struktur zuweisen und die erkannten
Funktionsblöcke pro Raum verknüpfen.

<p align="center">
  <img src="assets/bridge-panels.png" alt="MiraiBridge: Panel- und Raumzuordnung" width="400">
  <img src="assets/bridge-settings.png" alt="MiraiBridge: Einstellungen" width="400">
</p>

## Verhalten: Screensaver & Sleep

Das Display eskaliert bei Inaktivität in mehreren Stufen, statt einfach
abrupt auszugehen — und kann bei Bedarf per MQTT sogar in echten
Tiefschlaf versetzt werden, aus dem es zyklisch nur kurz für einen
Sensor-Report aufwacht.

```mermaid
flowchart TD
    Aktiv("Aktiv") -->|"Inaktivität (Timeout)"| Screensaver("Screensaver")
    Screensaver -->|"weiter inaktiv"| Gedimmt("Gedimmt")
    Gedimmt -->|"weiter inaktiv (Burn-in-Schutz)"| DisplayAus("Display aus")

    Aktiv -->|"MQTT sleep=1"| DeepSleep("Deep Sleep")
    Screensaver -->|"MQTT sleep=1"| DeepSleep
    Gedimmt -->|"MQTT sleep=1"| DeepSleep
    DisplayAus -->|"MQTT sleep=1"| DeepSleep

    DeepSleep -->|"Timer (alle N Min.)"| KurzWach("Kurz wach")
    KurzWach -->|"Sensorwerte gesendet (35s)"| DeepSleep
    DeepSleep -->|"Bewegung (GPIO)"| Aktiv

    Screensaver -->|"Touch / ToF / MQTT"| Aktiv
    Gedimmt -->|"Touch / ToF / MQTT"| Aktiv
    DisplayAus -->|"Touch / ToF / MQTT"| Aktiv
```

- **Screensaver**: nach konfigurierbarer Inaktivität blendet sich je nach
  Zustand eine Analoguhr (kein Audio aktiv) oder ein Now-Playing-Overlay
  (Musik läuft) ein
- **Dimmen**: Helligkeit sinkt in einer ersten Stufe ab (auf einen festen
  Nachtwert oder relativ zur zuletzt aktiven Helligkeit)
- **Display aus**: zweite Stufe schaltet die Hintergrundbeleuchtung ganz
  ab, ein Burn-in-Schutz läuft dabei unsichtbar im Hintergrund weiter
- **Aufwecken**: durch Touch, Näherungssensor, oder eine MQTT-Helligkeits-
  Änderung von Loxone aus — jede dieser drei Stufen lässt sich so wieder
  verlassen
- **Night Mode / Deep Sleep**: per MQTT-Befehl aktivierbar (z.B. nachts)
  — der ESP32-P4 geht dann in echten Tiefschlaf (< 0,1 W statt ~2,3 W im
  Normalbetrieb) und wacht in einem konfigurierbaren Intervall (Standard
  10 Minuten) nur für wenige Sekunden auf, um aktuelle Sensorwerte an
  Loxone zu melden, bevor er wieder schläft. Bewegung am Panel weckt es
  stattdessen sofort vollständig auf.

  Auch im dunklen, scheinbar "ausgeschalteten" Zustand bleibt das Panel
  damit kein blinder Fleck: Die Raumtemperatur wird weiterhin regelmäßig
  an Loxone gemeldet, statt für Stunden komplett zu verstummen — ein
  ungewöhnlicher Temperaturanstieg (z.B. durch einen beginnenden Brand)
  fällt so auch nachts zeitnah auf, um schnellstmöglich zu reagieren.
  Kein Ersatz für einen zugelassenen Rauchmelder, aber ein sinnvoller
  zusätzlicher Beitrag zur Früherkennung, ganz ohne Mehrverbrauch im
  Wachzustand. Auch Raumtemperaturregler bekommen so zuverlässig
  Livewerte, um weiterhin die aktuelle Raumtemperatur regeln zu können.

## Architektur

```mermaid
flowchart LR
    Other("weitere MQTT-Clients<br/>eigene Integrationen") <-->|MQTT| Broker(("MQTT Broker"))
    Broker <-->|MQTT| Bridge("MiraiBridge<br/>(LoxBerry-Plugin)")
    Broker <-->|MQTT| Panel("MiraiPanel<br/>(beliebig viele)")
    MS("Loxone Miniserver") <-->|WebSocket| Bridge
    Bridge <-->|"WebSocket<br/>(Live-Metadaten)"| Audio("Loxone Audioserver<br/>/ Sonn Core")
    Panel -->|"HTTP<br/>Play/Pause/Skip/Favoriten"| Audio
    Bridge -->|"HTTP<br/>Cover-Bild (Proxy)"| Panel
```

Im Zentrum steht ein gewöhnlicher MQTT-Broker — MiraiPanel ist ein
MQTT-Client wie jeder andere, kein Loxone-exklusives Gerät, und beliebig
viele Panels können gleichzeitig verbunden sein. Die MiraiBridge
übersetzt zwischen diesem MQTT-Netz und der WebSocket-Welt des Loxone
Miniservers und ist damit nur einer von mehreren denkbaren Wegen hinein.
Sie liest beim Start/bei jeder Änderung die komplette Miniserver-Struktur
(`LoxAPP3.json`) und leitet daraus automatisch alle benötigten
MQTT-Topics pro konfiguriertem Baustein ab — keine Topics von Hand
eintragen. Für Audio hält die Bridge zusätzlich eine eigene
WebSocket-Verbindung zum Audioserver/Sonn Core für latenzarme
Titel/Cover/Lautstärke-Updates, die sie per MQTT ans Panel weiterreicht. Nur
für aktive Befehle (Play/Pause/Skip, Favoriten laden) spricht das Panel den
Audioserver direkt per HTTP an, am Broker vorbei.

Cover-Bilder holt sich das Panel dagegen über einen kleinen Bild-Proxy in
der MiraiBridge statt direkt von der Original-Quelle: Der Proxy lädt das
Bild, wandelt es unabhängig vom Ausgangsformat (PNG, WebP, auch progressive
JPEGs, an denen der ESP32-Decoder sonst scheitern würde) in ein
kompatibles Baseline-JPEG um, skaliert es auf die jeweils benötigte
Zielgröße und cached das Ergebnis — spart Rechenzeit auf dem Panel und
unnötige wiederholte Downloads derselben Cover-URL.

## Teilprojekte

| Repo | Inhalt |
|---|---|
| [MiraiPanel-LCD](https://github.com/Holzmusik/MiraiPanel-LCD) | Firmware (ESPHome/ESP-IDF) für das ESP32-P4-Panel selbst — Display/LVGL-UI, Sensoren, Touch, Audio, Web-Konfiguration *(Quellcode folgt, noch in Test/Entwicklung)* |
| [LoxBerry-Plugin-MiraiBridge](https://github.com/Holzmusik/LoxBerry-Plugin-MiraiBridge) | "MiraiBridge" — LoxBerry-Plugin, verbindet Loxone Miniserver per MQTT mit dem Panel |
| [MiraiPanel-Hardware](https://github.com/Holzmusik/MiraiPanel-Hardware) | Fertigungsdaten für PCB und Gehäuse (Gerber/STEP/DXF) — noch im Aufbau |

## Hardware

Eigene PCB (Basis + Sensor-/Touch-Module) und Gehäuse, siehe
[MiraiPanel-Hardware](https://github.com/Holzmusik/MiraiPanel-Hardware).
Montage freistehend, wandmontiert, oder nachgerüstet auf dem bestehenden
Sockel eines Lichtschalters (kompakte Variante) — integrierte, unsichtbare
Kabelführung.

Freistehend zeigt sich, wie schlank die Säule wirklich wirkt: vom Boden bis
auf Blickhöhe, ohne wuchtigen Sockel — nur ein schmaler Standfuß, die
Kabelführung verschwindet unsichtbar im Inneren der Stange.

<p align="center">
  <img src="assets/mount-1.png" alt="Freistehende Säulen-Variante in voller Höhe" height="460">
  <img src="assets/mount-2.png" alt="Montagevarianten im Vergleich: freistehend, wandmontiert und kompakt" height="460">
</p>

<p align="center">
  <img src="assets/panel-back.png" alt="MiraiPanel Rückseite" width="220">
</p>

## Technische Daten

| Kategorie | Merkmal | Details |
|---|---|---|
| **Display & Bedienung** | Display | 5,5″ Farb-LCD, Touch, 1280×720 px |
| | Seiten & Layout | 6 frei belegbare Seiten, Raster-Positionierung per Drag-and-Drop; 21 Pool-Widgets in 7 Kategorien (Licht, Jalousie, Heizung, Schalter, Sensoren, Audio, Wetter) |
| | Sensortasten | 8× kapazitiv, einstellbare Empfindlichkeit, individuell beschriftbar & pro Raum austauschbar |
| | Näherung | ToF-Sensor + PIR wecken das Display bei Annäherung |
| | Feedback | Tastenklick per Buzzer, zusätzlich per MQTT auslösbarer Dauer-Warnton |
| | UI | Light-/Dark-Theme (auch automatisch über den Lichtsensor), 5 Akzentfarben |
| &nbsp; | | |
| **Sensoren** | Raumklima | Temperatur/Feuchte, CO₂/VOC/Luftqualität |
| | Bewegung | PIR-Sensor |
| | Präsenz | Mikrofon mit einstellbarer Schwelle |
| | Licht | Umgebungslichtsensor |
| | Versorgung | Spannungs-/Strommessung |
| &nbsp; | | |
| **Konnektivität** | Netzwerk | LAN 10/100, automatisches Failover auf WLAN |
| | Protokoll | MQTT — offene Schnittstelle für jede MQTT-Umgebung |
| | Loxone | LoxBerry-Plugin (MiraiBridge) für direkte Integration |
| | Auch nutzbar mit | Home Assistant, ioBroker und jedem MQTT-Broker |
| &nbsp; | | |
| **Stromversorgung** | Versorgung | extern 10–30 V DC, 24 V nominal |
| | Verbrauch | ca. 1,8 W bei aktivem Display |
| | Sleep-Modus | aktivierbar, automatisches Wecken bei aktuellen Sensorwerten, Dauer konfigurierbar |
| &nbsp; | | |
| **Konfiguration** | Einrichtung | vollständig über Web-Oberfläche am Gerät konfigurierbar |
| | Updates | Firmware-Updates over-the-air, Firmware-Datei auch direkt per Drag & Drop im Browser |
| &nbsp; | | |
| **Gehäuse** | Montage | freistehend, wandmontiert, oder nachgerüstet auf bestehendem Lichtschalter-Sockel |
| | Materialien | eloxiertes Aluminium, Edelstahl, 3D-gedrucktes Kunststoff, Glas |

---

<sub>MiraiPanel ist ein unabhängiges Projekt und steht in keiner Verbindung
zu oder Unterstützung durch Loxone Electronics GmbH. Loxone ist eine
Marke der Loxone Electronics GmbH.</sub>

<sub>© Holzmusik. Dieses Repo dient der Projektvorstellung — es ist (noch)
keine Lizenz vergeben, es gelten die Standard-Urheberrechte.</sub>
