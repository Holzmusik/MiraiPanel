<picture>
  <source media="(prefers-color-scheme: dark)" srcset="assets/logo-dark.svg">
  <img src="assets/logo-light.svg" alt="MiraiPanel" width="260">
</picture>

**Control. Sense. One device.**

Der universelle Raumcontroller für deine Loxone-Hausautomation: ein
5,5″-Farbdisplay, acht austauschbare Sensortasten und eine volle
Sensor-Ausstattung — verbunden über LAN/WLAN und MQTT.

<img src="assets/panel-front.png" alt="MiraiPanel Gehäuse" width="260">

Dieses Repo ist die **Projektübersicht** — der eigentliche Code liegt in den
verlinkten Teilprojekten unten. 📄 [Flyer (PDF)](assets/MiraiPanel-Flyer.pdf)

## Was das Gerät macht

- **Anzeige**: 5,5″ Touch-LCD (1280×720), Analoguhr (mit abschaltbarem
  Sekundenzeiger), Datum, Raumklima, Wetter — Light/Dark-Theme mit
  wählbarer Akzentfarbe
- **Audio**: Steuerung eines Loxone Audioservers oder Sonn Core (Play/Pause/
  Skip/Lautstärke/Favoriten), Cover-Art, Titel/Interpret live
- **Gebäudesteuerung**: Licht (inkl. Szenen), Jalousie, Heizung, Schalter —
  alles direkt aus der Loxone-Struktur übernommen, keine manuelle
  Topic-Konfiguration nötig
- **8 Sensortasten**: kapazitiv, individuell beschriftbar und pro Raum
  austauschbar
- **Näherung/Wake**: ToF-Sensor (VL53L0X) + PIR wecken das Display bei
  Annäherung, Screensaver mit Uhr bei Inaktivität
- **Sensor-Suite**: Raumtemperatur/-feuchte, CO₂/VOC/Luftqualität,
  Umgebungslicht, Mikrofon-gestützte Präsenzerkennung, Versorgungsspannung/
  -strom
- **Offene Integration**: MQTT-Schnittstelle — LoxBerry-Plugin für Loxone,
  funktioniert grundsätzlich auch mit Home Assistant, ioBroker & Co.
- **Konfiguration**: komplett über eine Web-Oberfläche direkt am Gerät,
  Firmware-Updates over-the-air

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
| MiraiPanel-LCD *(noch nicht öffentlich)* | Firmware (ESPHome/ESP-IDF) für das ESP32-P4-Panel selbst — Display/LVGL-UI, Sensoren, Touch, Audio, Web-Konfiguration |
| [LoxBerry-Plugin-MiraiPanel](https://github.com/Holzmusik/LoxBerry-Plugin-MiraiPanel) | "MiraiBridge" — LoxBerry-Plugin, verbindet Loxone Miniserver per MQTT mit dem Panel |
| [MiraiPanel-Hardware](https://github.com/Holzmusik/MiraiPanel-Hardware) | Fertigungsdaten für PCB und Gehäuse (Gerber/STEP/DXF) — noch im Aufbau |

## Hardware

- ESP32-P4 (RISC-V) + ESP32-C6-Companion für WLAN, dazu Ethernet
  (automatisches Failover LAN↔WLAN)
- 720×1280 MIPI-DSI-LCD, kapazitiver Touchscreen (GT911) + 8 kapazitive
  Sensortasten (MPR121)
- Sensoren: VL53L0X (ToF-Näherung), PIR, Mikrofon, Temperatur/Feuchte,
  CO₂/VOC/Luftqualität, Umgebungslicht, Versorgungsspannung/-strom
- Eigene PCB (Basis + Sensor-/Touch-Module), siehe
  [MiraiPanel-Hardware](https://github.com/Holzmusik/MiraiPanel-Hardware)

**Montage**: freistehend vor einer Wand, oder nachgerüstet auf dem
bestehenden Sockel eines Lichtschalters — integrierte, unsichtbare
Kabelführung.

**Materialien**: eloxiertes Aluminium, Edelstahl, 3D-gedrucktes Kunststoff
und Glas.

<img src="assets/panel-back.png" alt="MiraiPanel Rückseite" width="220">

---

<sub>MiraiPanel ist ein unabhängiges Projekt und steht in keiner Verbindung
zu oder Unterstützung durch Loxone Electronics GmbH. Loxone ist eine
Marke der Loxone Electronics GmbH.</sub>
