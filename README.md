# MiraiPanel

Smartes Wandpanel für Loxone-Hausautomation: ESP32-P4-Touchdisplay mit eigener
PCB/Gehäuse-Konstruktion, direkter Loxone-Anbindung über MQTT und Anzeige/
Steuerung von Uhr, Audio, Sensoren, Licht, Jalousie, Heizung und Szenen.

Dieses Repo ist die **Projektübersicht** — der eigentliche Code liegt in den
verlinkten Teilprojekten unten.

## Was das Gerät macht

- **Anzeige**: Analoguhr (mit Sekundenzeiger, abschaltbar), Datum, Raumklima
  (Temperatur/Luftfeuchte/CO₂/VOC/Helligkeit), Wetter
- **Audio**: Steuerung eines Loxone Audioservers oder Sonn Core (Play/Pause/
  Skip/Lautstärke/Favoriten), Cover-Art, Titel/Interpret live
- **Gebäudesteuerung**: Licht (inkl. Szenen), Jalousie, Heizung, Schalter —
  alles direkt aus der Loxone-Struktur übernommen, keine manuelle
  Topic-Konfiguration nötig
- **Näherung/Wake**: ToF-Sensor (VL53L0X) + PIR wecken das Display bei
  Annäherung, Screensaver mit Uhr bei Inaktivität
- **Bedienung**: kapazitive Touch-Tasten (Hardware) + Touchscreen-UI,
  konfigurierbar über eine Web-Oberfläche direkt am Gerät
- **Sound**: Mikrofon (Geräuscherkennung), Buzzer für Feedback/Klingel

## Architektur

```
┌─────────────────┐   WebSocket    ┌──────────────────────┐   MQTT   ┌──────────────────┐
│ Loxone Miniserver │◄─────────────►│ MiraiBridge            │◄────────►│ MiraiPanel        │
│                  │                │ (LoxBerry-Plugin)      │          │ (ESP32-P4-Firmware)│
└─────────────────┘                └──────────────────────┘          └──────────────────┘
                                                                              ▲
                                                             direkte WebSocket-│
                                                        Verbindung (Cover/Titel)
                                                                              ▼
                                                                    ┌──────────────────┐
                                                                    │ Loxone Audioserver │
                                                                    │ / Sonn Core        │
                                                                    └──────────────────┘
```

Die MiraiBridge liest beim Start/bei jeder Änderung die komplette
Miniserver-Struktur (`LoxAPP3.json`) und leitet daraus automatisch alle
benötigten MQTT-Topics pro konfiguriertem Baustein ab — keine Topics von
Hand eintragen. Fürs Audio verbindet sich das Panel zusätzlich direkt
(unabhängig von der Bridge) mit dem Audioserver für latenzarme Live-Updates.

## Teilprojekte

| Repo | Inhalt |
|---|---|
| [MiraiPanel-LCD](https://github.com/Holzmusik/MiraiPanel-LCD) | Firmware (ESPHome/ESP-IDF) für das ESP32-P4-Panel selbst — Display/LVGL-UI, Sensoren, Touch, Audio, Web-Konfiguration |
| [LoxBerry-Plugin-MiraiPanel](https://github.com/Holzmusik/LoxBerry-Plugin-MiraiPanel) | "MiraiBridge" — LoxBerry-Plugin, verbindet Loxone Miniserver per MQTT mit dem Panel |
| [MiraiPanel-Hardware](https://github.com/Holzmusik/MiraiPanel-Hardware) | Fertigungsdaten für PCB und Gehäuse (Gerber/STEP/DXF) — noch im Aufbau |

## Hardware (Kurzüberblick)

- ESP32-P4 (RISC-V, mit ESP32-C6-Companion für WLAN)
- 720×1280 MIPI-DSI-LCD, kapazitiver Touchscreen (GT911)
- Eigene PCB (Basis + Sensor-/Touch-Module), 3D-gedrucktes/gefrästes Gehäuse
- Sensoren: VL53L0X (ToF-Näherung), PIR, Mikrofon, Temperatur/Feuchte
  (HTU21D + AHT10), CO₂/VOC (ENS160), Helligkeit (BH1750), Strom/Spannung
  (INA219)
- Ethernet (IP101) mit automatischem WiFi-Failover
