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
## Technische Details
Der Controller für die Getränkekühlbox ist ein ESP8266 D1 mini mit folgenden Libraries:<br>
#include <ESP8266WiFi.h><br>
#include <TaskScheduler.h><br>
#include <WiFiClient.h><br>
#include <PubSubClient.h><br>
#include <ESPAsyncWebServer.h><br>
#include <ElegantOTA.h><br>
#include <ESPAsyncTCP.h><br>
#include <OneWire.h><br>
#include <DallasTemperature.h><br>
#include <ESP_EEPROM.h><br>
#include <ArduinoJson.h><br>
#include <WiFiUdp.h><br>
#include "NTP.h"<br><br>

Nach dem Einschalten versucht der Controller sich mit dem lokalen Netzwerk zu verbinden und schaltet bei Erfolg die blaue LED auf dem D1 mini Board ein.
Per NTP holt sich der Controller die aktuelle Zeit, das aktuelle Datum und zeigt diese dann auch per MQTT im Topic cooler/status an.
Wie auch bei vielen anderen Projekten im Makerspace nutzen wir den Task/Scheduler (a lightweight implementation of cooperative multitasking), da wir damit sehr gute Erfahrung gemacht haben. 
Der Controller 'published' alle 15 Sekunden per MQTT folgende Werte in json-Format:<br>
{"revision":"0.8_240721",<br>
"enable_flag":1,<br>
"ip":"192.168.1.38",<br>
"rssi":-73,<br>
"cnt_reconnect":0,<br>
"temp":27.875,<br>
"client":"cooler_1",<br>
"alarm_delay":15,<br>
"analog_level":120,<br>
"ADC_Read":205,<br>
"eeprom_use":3,<br>
"last TS":"21.07.24 - 16:58:29"}<br>
<br>
Unter Topic cooler/doors wird der aktuelle Status der Verschlusstüren und der Alarmzustand dargestellt.<br>
Auch lassen sich verschiedene Betriebsparameter (Alarm-Delay, analog Level, wipe EEPROm und enable_flag) per MQTT setzen.
<br>Zusätzlich gibt es einen Dallas 1-Wire Temperatursensor, dieser soll in die Kunststoffablaufkappe montiert werden, um die Kühlboxinnentemperatur zu erfassen.
<br>Der Programm-Code kann per OTA (Over the air) auch aus der Ferne geändert aufgespielt werden.

