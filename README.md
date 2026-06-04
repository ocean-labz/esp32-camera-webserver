<p align="center">
  <img src="https://img.shields.io/badge/ESP32-CAM%20Web%20Server-005C97?style=for-the-badge&logo=espressif&logoColor=white" alt="ESP32-CAM Web Server"/>
  <img src="https://img.shields.io/badge/Arduino_IDE-00979D?style=for-the-badge&logo=arduino&logoColor=white" alt="Arduino IDE"/>
  <img src="https://img.shields.io/badge/Board_Package-v2.0.0-FF6600?style=for-the-badge" alt="ESP32 Board Package v2.0.0"/>
  <img src="https://img.shields.io/badge/License-MIT-green?style=for-the-badge" alt="License: MIT"/>
</p>

<h1 align="center">📷 ESP32-CAM Web Server (Access Point Mode)</h1>

<p align="center">
  <b>A standalone Wi-Fi camera web server that runs entirely on your ESP32-CAM — no router, no internet, no external hardware required.</b>
</p>

<p align="center">
  Connect directly to the ESP32-CAM's built-in Wi-Fi hotspot, open your browser, and instantly view a live MJPEG video stream, capture high-resolution still images, and control camera settings — all from your phone or laptop.
</p>

---

## 📋 Table of Contents

- [Project Overview](#-project-overview)
- [Features](#-features)
- [Hardware Requirements](#-hardware-requirements)
- [Pin Configuration](#-pin-configuration)
  - [AI-Thinker ESP32-CAM (Default)](#ai-thinker-esp32-cam-default)
  - [Supported Camera Models](#supported-camera-models)
- [Software Requirements](#-software-requirements)
- [Installation & Setup](#-installation--setup)
  - [1. Install ESP32 Board Package](#1-install-esp32-board-package)
  - [2. Install Required Libraries](#2-install-required-libraries)
  - [3. Configure and Upload](#3-configure-and-upload)
- [Connecting to the ESP32-CAM Hotspot](#-connecting-to-the-esp32-cam-hotspot)
- [Accessing the Camera Web Interface](#-accessing-the-camera-web-interface)
- [Project Structure](#-project-structure)
- [Troubleshooting](#-troubleshooting)
- [License](#-license)

---

## 📖 Project Overview

This project turns an **ESP32-CAM module** into a **self-contained IP camera** that operates in **Access Point (AP) mode**. Unlike most ESP32 camera examples that connect to your home Wi-Fi router, this version creates its own Wi-Fi network — so you can use it **anywhere**, even in remote locations without existing network infrastructure.

When powered on, the ESP32-CAM broadcasts a Wi-Fi hotspot named **`OCEANLABZ-AP`**. Connect your phone or laptop to this network, navigate to `http://192.168.4.1`, and you'll see a full-featured camera control dashboard with:

- **Live MJPEG video streaming** at adjustable resolution and quality
- **High-resolution still image capture** (JPEG or BMP format)
- **Camera controls**: brightness, contrast, saturation, sharpness, zoom, white balance, exposure, gain, and more
- **Face detection & recognition** (when enabled via ESP-WHO)
- **Built-in LED flash control** (for AI-Thinker boards with the white LED)

The firmware is based on the official [Espressif ESP32 Camera Web Server example](https://github.com/espressif/arduino-esp32/tree/master/libraries/ESP32/examples/Camera/CameraWebServer), modified to operate in **Access Point mode** instead of Station (Wi-Fi client) mode, making it truly portable and self-contained.

---

## ✨ Features

| Feature | Details |
|---------|---------|
| 📡 **Standalone Access Point** | No router needed — the ESP32-CAM creates its own Wi-Fi network |
| 🎥 **Live MJPEG Streaming** | Real-time video via `http://192.168.4.1/stream` |
| 📸 **High-Res Still Capture** | Take JPEG or BMP snapshots at up to UXGA (1600×1200) resolution |
| 🔧 **Full Camera Control** | Adjust brightness, contrast, saturation, sharpness, zoom, white balance, exposure, AEC, AGC, gain, and more via the web UI |
| 🔍 **Face Detection** | Built-in face detection with bounding box overlay (requires ESP-WHO) |
| 🧑 **Face Recognition** | Optional face recognition capability (requires ESP-WHO) |
| 💡 **LED Flash** | Control the onboard white LED illuminator for low-light photography |
| 🔌 **8 Camera Models** | Pin configurations for AI-Thinker, WROVER Kit, ESP-Eye, M5Stack variants, and TTGO T-Journal |
| 📋 **Resolution Auto-Detect** | Automatically selects UXGA with PSRAM or SVGA without PSRAM |
| ⚡ **High Frame Rate** | Defaults to QVGA resolution on startup for smooth streaming |

---

## 🛠️ Hardware Requirements

### Essential Hardware

| Component | Description |
|-----------|-------------|
| **ESP32-CAM Board** | AI-Thinker ESP32-CAM module (or compatible — see [supported models](#supported-camera-models)) |
| **USB-to-Serial Adapter** | FTDI232, CP2102, or similar (3.3V logic) for programming |
| **Power Supply** | 5V / 1A minimum (the ESP32-CAM draws significant current, especially with the LED flash enabled) |
| **Jumper Wires** | Female-to-female wires to connect the USB adapter to the ESP32-CAM |

### Optional Hardware

| Component | Purpose |
|-----------|---------|
| **Breadboard** | For easy prototyping and connections |
| **External Antenna** | Improves Wi-Fi range (select AI-Thinker boards have a U.FL connector) |
| **Battery Pack** | For portable, cordless operation (e.g., 18650 Li-ion with 5V boost converter) |

### ⚠️ Important Notes

- The ESP32-CAM **does not have a built-in USB/UART bridge**. You **must** use an external USB-to-Serial adapter (FTDI, CP2102, CH340G) to upload code.
- Connect the adapter's **3.3V pin** to the ESP32-CAM's 3.3V pin — **do not use 5V** directly.
- During uploading, connect **GPIO 0 to GND** to put the ESP32-CAM into **flash download mode**.
- Remove the GPIO 0 ↔ GND jumper **after uploading** and press the reset button to run the program.

---

## 🔌 Pin Configuration

### AI-Thinker ESP32-CAM (Default)

This is the **default** camera model selected in the firmware (`#define CAMERA_MODEL_AI_THINKER`). Below is the wiring diagram for programming with a USB-to-Serial adapter:

| USB-to-Serial Adapter | ESP32-CAM |
|-----------------------|-----------|
| 3.3V | 3.3V |
| TXD | U0R (GPIO 3) |
| RXD | U0T (GPIO 1) |
| GND | GND |
| — | **GPIO 0 → GND** (for flashing only) |

#### ESP32-CAM Pin Mapping (AI-Thinker)

| Signal | GPIO Pin |
|--------|----------|
| PWDN | GPIO 32 |
| RESET | -1 (Not connected) |
| XCLK | GPIO 0 |
| SIOD (SDA) | GPIO 26 |
| SIOC (SCL) | GPIO 27 |
| Y9 (D7) | GPIO 35 |
| Y8 (D6) | GPIO 34 |
| Y7 (D5) | GPIO 39 |
| Y6 (D4) | GPIO 36 |
| Y5 (D3) | GPIO 21 |
| Y4 (D2) | GPIO 19 |
| Y3 (D1) | GPIO 18 |
| Y2 (D0) | GPIO 5 |
| VSYNC | GPIO 25 |
| HREF | GPIO 23 |
| PCLK | GPIO 22 |

### Supported Camera Models

The firmware includes pre-configured pin definitions for **8 different camera boards**. To use a different model, uncomment the appropriate line in [`esp32-camera-webserver.ino`](esp32-camera-webserver.ino:17) and comment out the default:

```cpp
// #define CAMERA_MODEL_WROVER_KIT     // Has PSRAM
// #define CAMERA_MODEL_ESP_EYE        // Has PSRAM
// #define CAMERA_MODEL_M5STACK_PSRAM  // Has PSRAM
// #define CAMERA_MODEL_M5STACK_V2_PSRAM // M5Camera version B, Has PSRAM
// #define CAMERA_MODEL_M5STACK_WIDE   // Has PSRAM
// #define CAMERA_MODEL_M5STACK_ESP32CAM // No PSRAM
#define CAMERA_MODEL_AI_THINKER       // Has PSRAM (DEFAULT)
// #define CAMERA_MODEL_TTGO_T_JOURNAL // No PSRAM
```

> **Note:** Models marked "Has PSRAM" can capture UXGA (1600×1200) JPEG images. Models without PSRAM are limited to SVGA (800×600).

All pin definitions are available in [`camera_pins.h`](camera_pins.h) for reference.

---

## 💻 Software Requirements

| Software | Version | Purpose |
|----------|---------|---------|
| [Arduino IDE](https://www.arduino.cc/en/software) | 1.8.x or 2.x | Development environment |
| **ESP32 Board Package** | **⚠️ v2.0.0** | **Required — see instructions below** |
| [USB-to-Serial Driver](https://www.silabs.com/developers/usb-to-uart-bridge-vcp-drivers) | Latest | Driver for your USB adapter (CP2102 / CH340G / FTDI) |

### ⚡ CRITICAL: ESP32 Board Package Version 2.0.0

> **This project requires ESP32 Arduino Board Package version `2.0.0`.**
>
> Later versions (3.x) have breaking changes in the `esp_camera` and `esp_http_server` libraries that are incompatible with this code. Version `2.0.0` is the last stable release with full camera support.

---

## 🚀 Installation & Setup

### 1. Install ESP32 Board Package

1. Open **Arduino IDE** → **File** → **Preferences**
2. In the **"Additional Boards Manager URLs"** field, add:
   ```
   https://raw.githubusercontent.com/espressif/arduino-esp32/gh-pages/package_esp32_index.json
   ```
3. Go to **Tools** → **Board** → **Boards Manager**
4. Search for `ESP32` and install **version 2.0.0**:
   ```
   esp32 by Espressif Systems  →  Version 2.0.0  →  Install
   ```
5. After installation, select your board: **Tools** → **Board** → **ESP32 Arduino** → **AI Thinker ESP32-CAM**


### 3. Configure and Upload

1. Download or clone this repository:
   ```bash
   git clone https://github.com/your-username/esp32-camera-webserver.git
   ```
2. Open [`esp32-camera-webserver.ino`](esp32-camera-webserver.ino) in Arduino IDE.
3. (Optional) Customize the Wi-Fi hotspot credentials in [`esp32-camera-webserver.ino`](esp32-camera-webserver.ino:23):
   ```cpp
   const char* ssid = "OCEANLABZ-AP";
   const char* password = "12345678";
   ```
4. **Prepare the ESP32-CAM for flashing:**
   - Connect **GPIO 0 to GND** (this puts the board in flash/download mode)
   - Connect your USB-to-Serial adapter (see [Pin Configuration](#ai-thinker-esp32-cam-default))
   - Press the **RST** button on the ESP32-CAM

5. In Arduino IDE:
   - **Board**: `AI Thinker ESP32-CAM`
   - **Port**: Select the COM port of your USB adapter
   - **Flash Size**: `4MB`
   - **Partition Scheme**: `Huge APP (3MB No OTA)`
   - **Upload Speed**: `115200`

6. Click **Upload** (→) button.

7. **After uploading:**
   - **Remove** the GPIO 0 ↔ GND jumper wire
   - Press the **RST** button to reboot the ESP32-CAM
   - The board will start in AP mode and broadcast the Wi-Fi network

---

## 📶 Connecting to the ESP32-CAM Hotspot

1. **Power on** the ESP32-CAM (via USB-to-Serial adapter or external 5V supply).

2. On your **phone, tablet, or laptop**, open the **Wi-Fi settings** menu.

3. Look for a network named **`OCEANLABZ-AP`** and connect to it.

4. Enter the password: **`12345678`**

5. Once connected, you'll see a notification that there is **no internet connection** — this is expected. Keep the connection active.

---

## 🌐 Accessing the Camera Web Interface

1. Open a **web browser** (Chrome, Firefox, Safari, Edge).

2. Navigate to: **[http://192.168.4.1](http://192.168.4.1)**

3. The camera control dashboard will load. You'll see:

   - **Live video stream** in the center of the page
   - **Control panel** on the right/bottom with sliders and buttons for:
     - Resolution (framesize)
     - JPEG quality
     - Brightness, contrast, saturation, sharpness
     - White balance, exposure, gain
     - Face detection toggle
     - LED flash toggle
     - Get Still (capture a snapshot)

### Direct Endpoints

| URL | Description |
|-----|-------------|
| `http://192.168.4.1/` | Main web interface dashboard |
| `http://192.168.4.1/stream` | Raw MJPEG video stream |
| `http://192.168.4.1/capture` | Capture and download a JPEG still image |
| `http://192.168.4.1/capture?bmp=true` | Capture and download a BMP still image |

---


## 📂 Project Structure

```
esp32-camera-webserver/
│
├── esp32-camera-webserver.ino    # Main Arduino sketch — AP setup, camera init, web server start
├── camera_pins.h                 # GPIO pin definitions for 8 supported camera models
├── app_httpd.cpp                 # HTTP server — streaming, capture, face detection, camera controls
├── camera_index.h                # Compressed HTML/CSS/JS web interface (gzipped, for OV2640/OV3660/OV5640)
│
├── README.md                     # This file
└── LICENSE                       # MIT License
```

### File Descriptions

| File | Description |
|------|-------------|
| [`esp32-camera-webserver.ino`](esp32-camera-webserver.ino) | The main entry point. It initializes the camera sensor, configures Wi-Fi in AP mode (hotspot), and starts the HTTP camera server. All configuration (SSID, password, camera model) is done here. |
| [`camera_pins.h`](camera_pins.h) | Contains pre-defined GPIO pin mappings for 8 different ESP32 camera boards. Select your board by uncommenting the appropriate `#define` in the main `.ino` file. |
| [`app_httpd.cpp`](app_httpd.cpp) | The full HTTP server implementation. Handles the stream (`/stream`), capture (`/capture`), BMP capture, camera settings control (`/control`), face detection, and all other HTTP endpoints. |
| [`camera_index.h`](camera_index.h) | The web interface HTML, CSS, and JavaScript — compiled and gzipped into a C header file. Contains separate versions for OV2640 (6787 bytes), OV3660 (8887 bytes), and OV5640 camera sensors. |

---

## 🔧 Troubleshooting

### ❌ Camera init failed with error 0x...

| Cause | Solution |
|-------|----------|
| Loose wiring | Check all connections between the camera module and the ESP32 board |
| Insufficient power | Use a **5V / 1A minimum** power supply. The ESP32-CAM can draw 500mA+, especially with the LED flash |
| Wrong camera model | Ensure the correct `#define CAMERA_MODEL_*` is uncommented in [`esp32-camera-webserver.ino`](esp32-camera-webserver.ino:17) |
| Damaged camera ribbon | The camera FFC (flat flex cable) is fragile. Try reseating it gently |

### ❌ Cannot connect to "OCEANLABZ-AP" Wi-Fi

| Cause | Solution |
|-------|----------|
| ESP32-CAM not powered | Check that the blue/red power LED on the board is lit |
| Wrong password | The default password is `12345678` (configured in [`esp32-camera-webserver.ino`](esp32-camera-webserver.ino:24)) |
| Board not booted | Press the **RST** button on the ESP32-CAM |
| Range too far | The ESP32's built-in antenna has limited range. Stay within 5–10 meters in open space |
| Upload mode still active | Ensure **GPIO 0 is NOT connected to GND** after flashing, then press RST |

### ❌ Web page loads but no video stream

| Cause | Solution |
|-------|----------|
| Camera not initialized | Check the Serial Monitor (115200 baud) for `Camera init failed` errors |
| Low light conditions | Enable the LED flash or increase the exposure/gain via the web controls |
| Wrong sensor selected | The firmware auto-detects the sensor. Ensure you're using OV2640 or OV3660 |
| Browser incompatibility | Use **Chrome** or **Firefox** (Safari has limited MJPEG support) |

### ❌ Upload fails in Arduino IDE

| Cause | Solution |
|-------|----------|
| GPIO 0 not grounded | Connect **GPIO 0 → GND** before uploading, then press RST |
| Wrong COM port | Check your Device Manager for the correct COM port of your USB adapter |
| Wrong board selected | Select **Tools → Board → ESP32 Arduino → AI Thinker ESP32-CAM** |
| Driver not installed | Install the USB-to-Serial driver (CP2102 / CH340G / FTDI) for your adapter |
| Wrong ESP32 package version | Ensure you have **ESP32 Board Package v2.0.0** installed, not v3.x |

### ❌ Image is corrupted / partial / greenish

| Cause | Solution |
|-------|----------|
| PSRAM not available | Without PSRAM, UXGA resolution will produce corrupted images. Reduce to SVGA or lower |
| Insufficient power | The ESP32-CAM needs stable power. Try a dedicated 5V supply instead of USB from a laptop |
| Loose camera connection | Reseat the camera FFC ribbon cable firmly |

### 📡 Serial Monitor Output

Connect to the ESP32-CAM via Serial Monitor (baud rate: **115200**) to see diagnostic messages:

```
AP IP address: 192.168.4.1
Camera Ready! Use 'http://192.168.4.1' to connect
```

If you see `Camera init failed with error 0x...`, refer to the troubleshooting guide above.

---

## 📄 License

This project is licensed under the **MIT License**. See the [LICENSE](LICENSE) file for details.

```
MIT License

Copyright (c) 2024

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.
```

---

<p align="center">
  Made with ❤️ for the ESP32-CAM community
  <br>
  <sub>Built on <a href="https://github.com/espressif/arduino-esp32/tree/master/libraries/ESP32/examples/Camera/CameraWebServer">Espressif's CameraWebServer</a> example</sub>
</p>