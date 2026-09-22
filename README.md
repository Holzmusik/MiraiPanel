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
  <img src="assets/panel-front.png" alt="MiraiPanel Gehäuse" width="260">
</p>

Dieses Repo ist die **Projektübersicht** — der eigentliche Code liegt in den
verlinkten Teilprojekten unten. 📄 [Flyer (PDF)](assets/MiraiPanel-Flyer.pdf)

## Was das Gerät macht

- **Anzeige**: 5,5″ Touch-LCD (1280×720), mehrere Seiten (Licht/Jalousie/
  Audio/Schalter, Wetter, Heizung/Raumklima) mit Wisch-Navigation —
  Light/Dark-Theme mit wählbarer Akzentfarbe, Kopfzeile mit Innen-/
  Außenklima und Uhrzeit
- **Audio**: Steuerung eines Loxone Audioservers oder Sonn Core (Play/Pause/
  Skip/Lautstärke/Favoriten), Cover-Art, Titel/Interpret live
- **Gebäudesteuerung**: Licht (inkl. Szenen), Jalousie, Heizung, Schalter —
  alles direkt aus der Loxone-Struktur übernommen, keine manuelle
  Topic-Konfiguration nötig
- **8 Sensortasten**: kapazitiv, individuell beschriftbar und pro Raum
  austauschbar
- **Näherung/Wake + Screensaver**: ToF-Sensor + PIR wecken das
  Display bei Annäherung; bei Inaktivität blendet sich je nach Zustand ein
  Now-Playing-Overlay (läuft Musik) oder eine Analoguhr (sonst) als
  Bildschirmschoner ein
- **Sensor-Suite**: Raumtemperatur/-feuchte, CO₂/VOC/Luftqualität,
  Umgebungslicht, Mikrofon-gestützte Präsenzerkennung, Versorgungsspannung/
  -strom
- **Offene Integration**: MQTT-Schnittstelle — LoxBerry-Plugin für Loxone,
  funktioniert grundsätzlich auch mit Home Assistant, ioBroker & Co.
- **Konfiguration**: komplett über eine Web-Oberfläche direkt am Gerät,
  Firmware-Updates over-the-air

## Screenshots

<p align="center">
  <img src="assets/screen-overview.jpg" alt="Übersicht: Licht, Jalousie, Audio, Schalter" width="220">
  <img src="assets/screen-weather.jpg" alt="Wetter-Seite" width="220">
  <img src="assets/screen-switches.jpg" alt="Schalter-Seite" width="220">
  <img src="assets/screen-audio.jpg" alt="Audio Now-Playing" width="220">
</p>

## Architektur

```
┌──────────────────┐   WebSocket    ┌──────────────────────┐   MQTT   ┌───────────────────┐
│ Loxone Miniserver │◄──────────────►│ MiraiBridge            │◄────────►│ MiraiPanel          │
│                  │                │ (LoxBerry-Plugin)      │          │ (ESP32-P4-Firmware)  │
└──────────────────┘                └──────────┬───────────┘          └─────────┬──────────┘
                                                 │ WebSocket                      │ HTTP (Play/Pause/
                                                 │ (Live-Metadaten)               │  Skip/Favoriten)
                                                 ▼                                ▼
                                     ┌──────────────────────────────────────────────┐
                                     │        Loxone Audioserver / Sonn Core           │
                                     └──────────────────────────────────────────────┘
```

Die MiraiBridge liest beim Start/bei jeder Änderung die komplette
Miniserver-Struktur (`LoxAPP3.json`) und leitet daraus automatisch alle
benötigten MQTT-Topics pro konfiguriertem Baustein ab — keine Topics von
Hand eintragen. Für Audio hält die Bridge zusätzlich eine eigene
WebSocket-Verbindung zum Audioserver/Sonn Core für latenzarme
Titel/Cover/Lautstärke-Updates, die sie per MQTT ans Panel weiterreicht. Nur
für aktive Befehle (Play/Pause/Skip, Favoriten laden) spricht das Panel den
Audioserver direkt per HTTP an, an der Bridge vorbei.

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

<p align="center">
  <img src="assets/mount-1.png" alt="Montagevarianten: freistehend, wandmontiert und kompakt" width="320">
  <img src="assets/mount-2.png" alt="Detailansicht Display und Sensortasten" width="320">
</p>

<p align="center">
  <img src="assets/panel-back.png" alt="MiraiPanel Rückseite" width="220">
</p>

## Technische Daten

**Display & Bedienung**

| | |
|---|---|
| Display | 5,5″ Farb-LCD, Touch, 1280×720 px |
| Sensortasten | 8× kapazitiv, einstellbare Empfindlichkeit, individuell beschriftbar & pro Raum austauschbar |
| Näherung | ToF-Sensor + PIR wecken das Display bei Annäherung |
| Feedback | konfigurierbare Tastentöne |
| UI | Light-/Dark-Theme, wählbare Akzentfarbe |

**Sensoren**

| | |
|---|---|
| Raumklima | Temperatur/Feuchte, CO₂/VOC/Luftqualität |
| Bewegung | PIR-Sensor |
| Präsenz | Mikrofon mit einstellbarer Schwelle |
| Licht | Umgebungslichtsensor |
| Versorgung | Spannungs-/Strommessung |

**Konnektivität & Integration**

| | |
|---|---|
| Netzwerk | LAN 10/100, automatisches Failover auf WLAN |
| Protokoll | MQTT — offene Schnittstelle für jede MQTT-Umgebung |
| Loxone | LoxBerry-Plugin (MiraiBridge) für direkte Integration |
| Auch nutzbar mit | Home Assistant, ioBroker und jedem MQTT-Broker |

**Stromversorgung**

| | |
|---|---|
| Versorgung | extern 10–30 V DC, 24 V nominal |
| Verbrauch | ca. 1,8 W bei aktivem Display |
| Sleep-Modus | aktivierbar, automatisches Wecken bei aktuellen Sensorwerten, Dauer konfigurierbar |

**Konfiguration & Updates**

| | |
|---|---|
| Einrichtung | vollständig über Web-Oberfläche am Gerät konfigurierbar |
| Updates | Firmware-Updates over-the-air |

**Gehäuse & Montage**

| | |
|---|---|
| Montage | freistehend, wandmontiert, oder nachgerüstet auf bestehendem Lichtschalter-Sockel |
| Materialien | eloxiertes Aluminium, Edelstahl, 3D-gedrucktes Kunststoff, Glas |

---

<sub>MiraiPanel ist ein unabhängiges Projekt und steht in keiner Verbindung
zu oder Unterstützung durch Loxone Electronics GmbH. Loxone ist eine
Marke der Loxone Electronics GmbH.</sub>
