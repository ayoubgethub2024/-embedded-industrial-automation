# embedded-industrial-automation
industrial Carbon Cleaning Machine Controller
Industrial carbon cleaning machine control system based on ESP8266 with real-time monitoring, WebSocket dashboard, and serial communication with ATmega328.

File Name
industrial_cleaner_controller.ino

Overview
This firmware controls an industrial carbon cleaning machine. It handles machine operation, sensor data acquisition, real-time monitoring, and provides a web-based control interface accessible from any device on the same network.

Features
Dual-Microcontroller Architecture – ESP8266 communicates with ATmega328 via serial (UART) with CRC validation

Real-Time Web Dashboard – Built with HTML/CSS/JS, served directly from the ESP8266

WebSocket Communication – Bidirectional real-time data streaming to web clients and mobile applications

Industrial Control – Machine ON/OFF control, timer settings, engine size selection

Sensor Monitoring – Real-time display of temperature (3 channels), pressure, water level, HC level

Data Logging – Operation logs stored in LittleFS with JSON format

OTA Updates – Over-the-air firmware updates from remote server

Service Mode – Password-protected service access with calibration and system info

Session Timer – Configurable cleaning cycles (30/45/60 minutes)

Service Lockout – Automatic lockout after set operating hours, resettable via calibration

Hardware Requirements
Component	Specification
Microcontroller	ESP8266 (NodeMCU / Wemos D1 Mini)
Secondary MCU	ATmega328 (for sensor interfacing)
Display	None (web-based UI)
Connectivity	Wi-Fi 2.4GHz
Storage	LittleFS (on ESP8266)
Inputs	2x push buttons (ON/OFF) with interrupts
Pin Configuration
Pin	Function
D12 (GPIO12)	ON button (INPUT_PULLUP, interrupt on FALLING)
D13 (GPIO13)	OFF button (INPUT_PULLUP, interrupt on FALLING)
TX/RX	Serial communication to ATmega328 (9600 baud)
Serial Communication Protocol
The ESP8266 communicates with ATmega328 using a custom binary protocol:

Format: *[temp1][temp2][pressure][level1][level2][p2][current][leak][start_led][temp3][CRC]

CRC Calculation: (1*temp1) + (2*temp2) + (3*pressure) + (4*level1) + (5*level2) + (6*p2) + (7*current) + (8*leak) + (9*start_led) + (10*temp3)

Wi-Fi Configuration
Default credentials:

SSID: Configured in code

Password: Configured in code

Fallback AP Mode: If Wi-Fi connection fails, the device creates an access point:

SSID: QS10P[CHIP_ID]

IP: 192.168.4.1

Web Interface Features
The built-in web dashboard provides:

Section	Features
Control Panel	ON/OFF buttons, machine state indicator
Session Timer	Preset durations (30/45/60 min), remaining time display
Engine Size	0-1L, 1-2L, 2-3L, 3-4L selection
Sensors	Temperature (3x), Pressure, Water Level, HC Level
Operation Log	Table of past operations (ID, Time, Engine Size)
Service Panel	Password-protected access with IP, Device ID, Memory stats, Calibration, OTA update
API Endpoints
Endpoint	Method	Description
/	GET	Main web interface
/data	GET	JSON sensor and status data
/olog	GET	JSON operation log data
/passcode?passcode=X	GET	Service mode authentication (passcode = chip ID)
/ota	GET	Trigger OTA update (service mode only)
WebSocket Commands
Send these via WebSocket to control the machine:

Command	Action
start	Start machine operation
stop	Stop machine operation
toggle	Toggle ON/OFF
calibrate	Reset service timer (service mode only)
1000	Set engine size to 0-1L
2000	Set engine size to 1-2L
3000	Set engine size to 2-3L
4000	Set engine size to 3-4L
t30	Set timer to 30 minutes
t45	Set timer to 45 minutes
t60	Set timer to 60 minutes
Data Storage (LittleFS)
File	Content
/OLog.json	Operation log (JSON array)
/t_hrs.txt	Total operating hours
/t_min.txt	Total operating minutes
/t_sec.txt	Total operating seconds
/on_hrs.txt	Power-on hours
/on_min.txt	Power-on minutes
/cal_hrs.txt	Last calibration hours
/cal_min.txt	Last calibration minutes
OTA Update
OTA updates are fetched from a remote server:

Update is triggered via /ota endpoint

Requires service mode to be active

Device connects to configured Wi-Fi before updating

Error Codes
Code	Description
-10	Service lockout – calibration required
-1	Service mode activated (UI blur effect)
Dependencies (Libraries)
cpp
#include <Arduino.h>
#include <Hash.h>
#include <FS.h>
#include <LittleFS.h>
#include <ESP8266WiFi.h>
#include <ESP8266HTTPClient.h>
#include <ESP8266httpUpdate.h>
#include <ESPAsyncTCP.h>
#include <ESPAsyncWebServer.h>
#include <ESP8266mDNS.h>
#include <ArduinoJson.h>
Install via Arduino Library Manager or PlatformIO.

PlatformIO Configuration (platformio.ini)
ini
[env:nodemcuv2]
platform = espressif8266
board = nodemcuv2
framework = arduino
monitor_speed = 9600
board_build.filesystem = littlefs
Version History
Version	Changes
2.1	Current release – Full feature set with WebSocket, OTA, logging
Author
Embedded Systems & Industrial Automation Engineer

Related Repositories
ATmega328 sensor interface firmware

PCB design files for controller board

Mobile application source code (React Native / Expo)

License
Proprietary – All rights reserved.
