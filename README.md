# ESP32 LED Control System

![License: MIT](https://img.shields.io/badge/License-MIT-green.svg)
![Platform: ESP32](https://img.shields.io/badge/Platform-ESP32-blue.svg)
![IDE: Arduino](https://img.shields.io/badge/IDE-Arduino%20IDE-00979D.svg)
![Language: C++](https://img.shields.io/badge/Language-Embedded%20C%2FC%2B%2B-orange.svg)

A beginner-friendly IoT and embedded systems project in which an **ESP32 creates its own Wi-Fi network** and hosts a **responsive web page** that turns an external LED **ON or OFF** from any phone or laptop. No router and no internet connection are needed.

---

## Project Overview

The ESP32 DevKit V1 runs in **Wi-Fi Access Point (AP) mode** and hosts a small HTTP web server on port 80. When a phone or laptop joins the ESP32's network and opens `http://192.168.4.1`, the ESP32 serves a web page that shows the current LED status and provides **TURN ON** and **TURN OFF** buttons. Each button press sends an HTTP request to the ESP32, which switches GPIO 2 HIGH or LOW to control the LED.

## Demo

This project has no cloud component. To see it working:

1. Wire the circuit as shown below and upload `src/LED_Control_System.ino`.
2. Connect your phone or laptop to the Wi-Fi network `ESP32-LED-Control`.
3. Open `http://192.168.4.1` and press **TURN ON** / **TURN OFF**.

> Add your own photos and screenshots to the [`images/`](images/README.md) folder to show your working build.

## Features

- ESP32 creates its own Wi-Fi Access Point (no router needed)
- Web server hosted directly on the ESP32 (port 80)
- Routes: `/` (control page), `/on`, `/off`
- Live LED status display (ON / OFF)
- Responsive, clean web interface (works on phones and laptops)
- Web page embedded in the sketch, with no external CDN or libraries
- Serial Monitor debug messages
- Proper HTTP responses (200, 303 redirect, 404)
- Separate `basic_led_test` sketch to verify hardware first

## Hardware Requirements

| Component | Quantity | Notes |
|-----------|:--------:|-------|
| ESP32 DevKit V1 | 1 | Any ESP32 DevKit with GPIO 2 exposed |
| LED (any color, 5 mm) | 1 | External LED |
| Resistor 220Ω | 1 | Current-limiting resistor |
| Breadboard | 1 | For solderless wiring |
| Jumper wires (male-to-male) | 3 | Connections |
| USB cable (data-capable) | 1 | Power and programming |

## Software Requirements

| Software | Purpose |
|----------|---------|
| [Arduino IDE](https://www.arduino.cc/en/software) (2.x recommended) | Write and upload code |
| ESP32 board package by Espressif | Adds ESP32 support to Arduino IDE |
| `WiFi.h`, `WebServer.h` | Included with the ESP32 package (nothing to install separately) |
| USB-to-serial driver (CP210x or CH340, depending on your board) | Only if your computer does not detect the board |

## Circuit Diagram

![Circuit Diagram](circuit/circuit_diagram.png)

An editable version is available at [`circuit/circuit_diagram.svg`](circuit/circuit_diagram.svg).

## Circuit Connections

| From | To | Description |
|------|----|-------------|
| ESP32 GPIO 2 | 220Ω resistor (pin 1) | Output signal |
| 220Ω resistor (pin 2) | LED anode (+, longer leg) | Limits current |
| LED cathode (−, shorter leg) | ESP32 GND | Return path |

## Project Architecture

```
Phone / Laptop
      |
      |  Wi-Fi
      v
ESP32 Access Point   (SSID: ESP32-LED-Control)
      |
      v
ESP32 Web Server     (http://192.168.4.1, port 80)
      |
      v
GPIO 2
      |
    220Ω
      |
     LED
      |
     GND
```

## Installation

1. Download or clone this repository:
   ```bash
   git clone https://github.com/<your-username>/ESP32-LED-Control-System.git
   ```
2. Install the [Arduino IDE](https://www.arduino.cc/en/software).
3. Install the ESP32 board package (next section).
4. Open `src/LED_Control_System.ino` in the Arduino IDE.

> The Arduino IDE expects a sketch to sit inside a folder of the same name. If it asks to create one, click **OK**, or place the `.ino` file in a folder named `LED_Control_System`.

## ESP32 Board Setup

1. In Arduino IDE open **File → Preferences**.
2. In **Additional boards manager URLs**, add:
   ```
   https://espressif.github.io/arduino-esp32/package_esp32_index.json
   ```
3. Open **Tools → Board → Boards Manager**.
4. Search for **esp32** and install **"esp32 by Espressif Systems"**.
5. Select **Tools → Board → ESP32 Arduino → ESP32 Dev Module**.

## Upload Instructions

1. Build the circuit (see [Hardware Setup](docs/HARDWARE_SETUP.md)).
2. Connect the ESP32 to your computer with the USB cable.
3. Select the board: **ESP32 Dev Module**.
4. Select the correct port under **Tools → Port** (e.g. `COM3` on Windows).
5. *(Recommended)* First upload `examples/basic_led_test/basic_led_test.ino` and confirm the LED blinks.
6. Open `src/LED_Control_System.ino` and click **Upload** (→).
7. If you see `Connecting...`, hold the **BOOT** button on the ESP32 until upload begins.
8. Open **Tools → Serial Monitor** and set the baud rate to **115200**.

## Wi-Fi Setup

| Setting | Value |
|---------|-------|
| SSID | `ESP32-LED-Control` |
| Password | `12345678` |
| IP address | `192.168.4.1` |

The password is intentionally simple for learning. Change it in the sketch for real use.

## How to Access the Web Interface

1. On your phone or laptop, open Wi-Fi settings.
2. Connect to **ESP32-LED-Control** using the password **12345678**.
3. If your phone warns that the network has no internet, choose to stay connected.
4. Open a browser and go to: **http://192.168.4.1**

Use `http://` (not `https://`).

## How the System Works

1. **Startup:** The ESP32 boots, sets GPIO 2 as an output and keeps the LED OFF.
2. **Access Point:** `WiFi.softAP()` creates the network `ESP32-LED-Control`.
3. **Web server:** A `WebServer` object listens on port 80 and registers three routes.
4. **Page request:** A browser requests `/`. The ESP32 builds the HTML page with the current status and sends it.
5. **Button press:** Pressing **TURN ON** requests `/on`. The ESP32 sets GPIO 2 HIGH (LED ON), then redirects the browser back to `/`, which shows the updated status. **TURN OFF** requests `/off` and sets GPIO 2 LOW.
6. **Loop:** `server.handleClient()` runs continuously in `loop()` to serve requests.

## Testing Procedure

1. Upload `basic_led_test.ino` and check that the LED blinks once per second.
2. Upload `LED_Control_System.ino`.
3. Open the Serial Monitor (115200 baud) and check the startup messages.
4. Confirm the network `ESP32-LED-Control` appears on your phone or laptop.
5. Connect using the password `12345678`.
6. Open `http://192.168.4.1`.
7. Press **TURN ON**: the LED should light and the status should show **ON**.
8. Press **TURN OFF**: the LED should turn off and the status should show **OFF**.
9. Refresh the page: the status should stay correct.

## Expected Output

**Serial Monitor (115200 baud) on startup:**

```
=== ESP32 LED Control System ===
[LED] Turned OFF
[WiFi] Access Point started
[WiFi] SSID     : ESP32-LED-Control
[WiFi] Password : 12345678
[WiFi] IP       : 192.168.4.1
[HTTP] Web server started on port 80
Open http://192.168.4.1 in your browser
```

**When you press the buttons:**

```
[HTTP] GET /on
[LED] Turned ON
[HTTP] GET /
[HTTP] GET /off
[LED] Turned OFF
[HTTP] GET /
```

> Browsers often also request `/favicon.ico`, so you may see an extra `[HTTP] 404 Not Found: /favicon.ico` line. This is normal.

**Web page:** the title, an LED status indicator (green when ON, red when OFF) and the TURN ON and TURN OFF buttons.

## Troubleshooting

Common problems are covered in [docs/TROUBLESHOOTING.md](docs/TROUBLESHOOTING.md), including:

- ESP32 or COM port not detected
- Upload failed
- LED not turning ON or always ON
- Wi-Fi network not visible
- `192.168.4.1` not opening
- Garbage characters in the Serial Monitor

## Future Improvements

- Multiple LED control
- PWM brightness control
- Sensors (for example temperature or light)
- MQTT communication
- Cloud IoT dashboard
- Authentication for the web interface

These are ideas only and are **not** implemented in this version.

## Technologies Used

- ESP32
- Embedded C/C++
- Arduino IDE
- Wi-Fi (Access Point mode)
- HTTP
- HTML
- CSS

## Learning Outcomes

Through this project an ECE student learns to:

- Interface an LED with a microcontroller GPIO pin
- Apply Ohm's law to choose a current-limiting resistor
- Read a pinout and build a circuit on a breadboard
- Configure and program the ESP32 with Arduino IDE
- Run an ESP32 as a Wi-Fi Access Point
- Understand HTTP requests, routes and responses
- Build a responsive interface using HTML and CSS
- Debug hardware and software using the Serial Monitor
- Document a project and manage it on GitHub

## Documentation

| Document | Description |
|----------|-------------|
| [Project Documentation](docs/PROJECT_DOCUMENTATION.md) | Full internship-style project report |
| [Hardware Setup](docs/HARDWARE_SETUP.md) | Wiring, polarity, safety |
| [Software Setup](docs/SOFTWARE_SETUP.md) | Arduino IDE and upload guide |
| [Troubleshooting](docs/TROUBLESHOOTING.md) | Problems and fixes |
| [Contributing](CONTRIBUTING.md) | How to contribute |

## Project Author

**Selva Kumar SK**
B.E. Electronics and Communication Engineering
VSB Engineering College

## Internship

IoT & Embedded Systems Internship

## License

This project is licensed under the [MIT License](LICENSE).
