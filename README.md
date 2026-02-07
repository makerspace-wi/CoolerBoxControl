# CoolerBoxControl
Unsere Getränkekühlbox stand neulich mal wieder längere Zeit offen - ein Zustand der auf jeden Fall zu vermeiden ist, denn neben nicht gekühlten Getränken ergibt sich dabei ein enormer Stromverbrauch, da der Kühlkompressort ständig läuft.
<br>
Das Makerspace Technik-Team hat sich dazu Gedanken gemacht und wartet mit folgender Lösung auf:
Die Kühlbox wird durch 2 übereinanderliegende Schiebeelemente, von denen eins ganz nach links, das andere ganz rechts (oder umgekehrt) geschoben werden muss, damit die Truhe geschlossen ist.
<br>
<img src="https://github.com/user-attachments/assets/3b224a5f-eef6-48cb-ab57-e48e8039918d" width="400">
<br>
Unser Ansatz sind 2 Infrarot Opto Reflex Sensoren, die links und rechts hinten knapp unter der unteren Laufschine angebracht werden und nach oben schauen. An beide Schiebeelemente werden links und rechts Reflektoren angeklebt, die - nur wenn geschlossen - genau über den Sensoren stehen und dann ein "geschlossen" Signal an den Prozessor melden.
<br>
<img src="https://github.com/user-attachments/assets/9c6b663d-930d-407b-ab10-18039a858a7a" width="300">
<img src="https://github.com/user-attachments/assets/f546fa4a-1c9c-4487-b759-9f8167c2bc52" width="300">
<img src="https://github.com/user-attachments/assets/7d660f90-6e68-4c0d-babe-1eb549d3b7fe" width="300">
<br>
Beide Sensoren sind an einem 4-poligen Flachbahnkabel angeschlossen und der Plan ist, dieses so in den Laufschienen nach aussen zu führen (einkleben mit doppelseitigem Klebeband), dass die Schieber nicht behindert werden.
<br> 
Als Prozessor wird ein ESP8266 D1 mini Board verwendet. In der Haupt-Task wird jede Sekunde abgefragt, ob ein (oder beide) Schieber geöffnet wurden. Wenn dem so ist, wird eine Timer gestartet (momentan 15 Sekunden).
Läuft der Timer ab, ohne dass beide Schieber wieder zurück in ihrer 'geschlossen' Position sind, wird ein Licht- und Tonsignal ausgelöst, um Anwesende hinzuweisen, dass die Getränkebox zu schließen ist.
<br>
<img src="https://github.com/user-attachments/assets/a8ea42ed-357d-41d9-a7d8-f03a35322d14" width="400">
<br>
Sobald beide Schieber wieder geschlossen sind, stoppt die Alamierung.
<br><br>
# CoolerControl

Ein ESP8266-basiertes IoT-Überwachungssystem für Kühlräume und Gefriergeräte mit MQTT-Integration, Temperaturmessung und Türalarm.

## Übersicht

CoolerControl ist ein intelligentes Überwachungssystem, das die Temperatur und den Türstatus von Kühlräumen überwacht und über MQTT-Nachrichten kommuniziert. Das System bietet Alarmfunktionen bei offenstehenden Türen und ermöglicht die Fernkonfiguration über MQTT.

## Features

### Kernfunktionen
- **Temperaturüberwachung**: Kontinuierliche Temperaturmessung mit Dallas DS18B20 Sensor
- **Türüberwachung**: Analoger Sensor zur Erkennung offener Schiebetüren
- **MQTT-Integration**: Vollständige MQTT-Kommunikation mit LWT (Last Will Testament)
- **Alarm-System**: Akustischer (Beeper) und visueller (LED) Alarm bei offenstehenden Türen
- **Verzögerbarer Alarm**: Konfigurierbare Verzögerungszeit vor Alarmauslösung
- **OTA-Updates**: Over-the-Air Firmware-Updates via ElegantOTA
- **NTP-Zeitstempel**: Automatische Zeitsynchronisation mit Sommer-/Winterzeit-Unterstützung
- **Persistente Einstellungen**: Speicherung der Konfiguration im EEPROM

### Netzwerk & Kommunikation
- **WiFi Auto-Reconnect**: Automatische Wiederverbindung bei Verbindungsverlust
- **MQTT Auto-Reconnect**: Robuste MQTT-Wiederverbindungsmechanismen
- **Web-Interface**: Einfacher Zugriff auf OTA-Update-Seite
- **LWT-Status**: Last Will Testament für zuverlässige Statusmeldungen

## Hardware-Anforderungen

### Hauptkomponenten
- **Mikrocontroller**: ESP8266 (Wemos D1 Mini oder kompatibel)
- **Temperatursensor**: Dallas DS18B20 (OneWire)
- **Beeper**: Piezo-Beeper oder Buzzer
- **LED**: Status-LED für visuelle Alarme
- **IR-LED**: Infrarot-LED für Sensorschaltung
- **Analoger Sensor**: Zur Türerkennung (z.B. Hall-Sensor, Reed-Kontakt mit Spannungsteiler)

### Pin-Belegung
| Pin | GPIO | Funktion |
|-----|------|----------|
| D1  | GPIO5  | Beeper |
| D2  | GPIO4  | Status-LED |
| D5  | GPIO14 | OneWire (DS18B20) |
| D7  | GPIO13 | IR-LED |
| A0  | ADC0   | Analoger Sensor (Tür) |

## Software-Abhängigkeiten

Das Projekt verwendet folgende Bibliotheken (automatisch über PlatformIO verwaltet):

- **TaskScheduler** - Kooperatives Multitasking
- **PubSubClient** - MQTT-Client
- **ElegantOTA** - OTA-Update-Webinterface
- **AsyncTCP / ESPAsyncWebServer** - Asynchroner Webserver
- **OneWire** - OneWire-Kommunikation
- **DallasTemperature** - Dallas Temperatursensor-Bibliothek
- **ArduinoJson** - JSON-Serialisierung
- **ESP_EEPROM** - EEPROM-Verwaltung
- **NTP** - Netzwerk-Zeitprotokoll

## Installation

### Voraussetzungen
- [VSCode](https://code.visualstudio.com/) mit [PlatformIO Extension](https://platformio.org/install/ide?install=vscode)
- Git (für Bibliotheks-Downloads)

### Setup

1. **Repository klonen oder Projekt öffnen**
   ```bash
   cd /Users/dieter/Documents/PlatformIO/Projects/CoolerControl
   ```

2. **Credentials-Datei erstellen**
   
   Erstelle `src/Credentials_ms.h` mit folgendem Inhalt:
   ```cpp
   #ifndef CREDENTIALS_H
   #define CREDENTIALS_H
   
   // WiFi Credentials
   #define WIFI_SSID "IhrWiFiName"
   #define WIFI_PW "IhrWiFiPasswort"
   
   // MQTT Broker
   #define MQTT_BROKER "mqtt.broker.adresse"
   #define MQTT_PORT 1883
   #define MQTT_USER "mqtt_benutzer"
   #define MQTT_PASS "mqtt_passwort"
   
   #endif
   ```

3. **Projekt kompilieren**
   ```bash
   platformio run
   ```

4. **Auf ESP8266 hochladen**
   ```bash
   platformio run --target upload
   ```

5. **Serial Monitor starten** (optional)
   ```bash
   platformio device monitor
   ```

## Konfiguration

### MQTT Topics

| Topic | Typ | Beschreibung |
|-------|-----|--------------|
| `cooler/status` | Publish | Periodischer Status (Temperatur, Einstellungen, etc.) |
| `cooler/doors` | Publish | Türstatus und Alarmzustand |
| `cooler/lwt` | Publish | Last Will Testament (online/offline) |
| `cooler/in` | Subscribe | Kommandos und Konfiguration |

### MQTT-Kommandos

Sende JSON-Nachrichten an `cooler/in`:

**Alarmverzögerung setzen** (in Sekunden):
```json
{delay:15}
```

**Analog-Schwellwert setzen** (0-1023):
```json
{level:512}
```

**System aktivieren/deaktivieren**:
```json
{enable:1}
```
oder
```json
{enable:0}
```

**System-Reset**:
```json
{reset:1}
```

### MQTT-Statusnachrichten

**Status-Topic** (`cooler/status`):
```json
{
  "revision": "1.6_250617",
  "enable_flag": 1,
  "ip": "192.168.1.100",
  "rssi": -45,
  "cnt_reconnect": 0,
  "temp": 4.5,
  "client": "cooler_1",
  "alarm_delay": 10,
  "adc_value": 123,
  "analog_level": 100,
  "eeprom_use": 2.5,
  "last TS": "07.02.26 15:30:45"
}
```

**Tür-Topic** (`cooler/doors`):
```json
{
  "doors": 1,
  "Alarm": 1
}
```
- `doors`: 0 = geschlossen, 1 = offen
- `Alarm`: 0 = kein Alarm, 1 = Alarm aktiv

**LWT-Topic** (`cooler/lwt`):
- `online` - Gerät ist verbunden
- `offline` - Gerät hat Verbindung verloren

## Funktionsweise

### Hauptschleife
Das System verwendet einen TaskScheduler für nicht-blockierendes Multitasking:

1. **Task t1** (500ms): Hauptschleife - liest Analogwert und prüft Türstatus
2. **Task t2** (10s): Veröffentlicht periodischen Status
3. **Task t3**: Einmalig - startet Alarm nach Verzögerungszeit
4. **Task t4** (1s): LED/Beeper-Blinken während Alarm
5. **Task t5** (10s): Veröffentlicht Türstatus

### Alarmlogik

1. Tür öffnet sich → ADC-Wert steigt über Schwellwert
2. Verzögerungstimer startet (konfigurierbar, Standard: 10s)
3. Nach Ablauf der Verzögerung: Alarm aktiviert (LED blinkt, Beeper piept)
4. Tür schließt sich → Alarm deaktiviert

### WiFi & MQTT Reconnect

- **WiFi**: Automatische Wiederverbindung bei Verbindungsverlust
- **MQTT**: Wiederverbindungsversuche alle 5 Sekunden mit Zähler
- **LWT**: Broker meldet automatisch "offline" bei unerwartetem Verbindungsverlust

## OTA-Updates

Nach dem ersten Upload kann das Gerät per OTA (Over-The-Air) aktualisiert werden:

1. IP-Adresse des Geräts ermitteln (siehe Serial Monitor oder MQTT-Status)
2. Im Browser öffnen: `http://<IP-ADRESSE>/update`
3. Neue Firmware-Datei: `.pio/build/d1_mini/firmware.bin`
4. Über Webinterface hochladen

## Anpassungen

### Client-ID ändern
In `main.cpp` (Zeile 35):
```cpp
const char *clientId = "cooler_1"; // Eindeutige ID vergeben!
```

### Temperatur-Offset kalibrieren
In `main.cpp` (Zeile 53):
```cpp
float tempOffset = -5.0; // Nach Bedarf anpassen
```

### Zeitzone anpassen
In `setup()` (Zeilen 164-165):
```cpp
ntp.ruleDST("CEST", Last, Sun, Mar, 2, 120);
ntp.ruleSTD("CET", Last, Sun, Oct, 3, 60);
```

### MQTT Root-Topic ändern
In `main.cpp` (Zeile 31):
```cpp
const char *topic = "cooler"; // Basis-Topic
```

## Debugging

Debug-Ausgaben aktivieren durch Auskommentieren in `main.cpp`:
```cpp
#define DEBUG
```

Serial Monitor mit 115200 Baud öffnen:
```bash
platformio device monitor -b 115200
```

## Versionshistorie

- **v1.6 (17.06.2025)**: Aktuelle Version mit LWT-Unterstützung

## Lizenz

Dieses Projekt ist freie Software. Verwendung auf eigene Verantwortung.

## Autor

CoolerControl - ESP8266 Kühlraum-Überwachung

---

**Hinweis**: Vor der Verwendung müssen die WiFi- und MQTT-Zugangsdaten in `src/Credentials_ms.h` konfiguriert werden.

