# ESPHome for Yeelight YLXD42YL (Single-Core ESP32 Fix)

This project provides a stable ESPHome configuration for the **Yeelight YLXD42YL** (and similar 450/480/650 models) that utilizes the **Single-Core ESP32-WROOM-32U** chip. 

The main highlight of this config is the **successful integration of the YLYK01YL BLE Remote** without triggering the dreaded Bootloop/Watchdog Reset common on these single-core devices.



## ⚠️ The Single-Core Challenge

Standard ESPHome BLE configurations often cause a crash (Task Watchdog Timeout) on single-core ESP32 units because the processor cannot handle the Bluetooth stack, Wi-Fi, and Light logic simultaneously during the boot sequence.

### The Fix
This configuration solves the stability issues by:
1. **`setup_priority: -900`**: Delaying the Bluetooth initialization until the very end of the boot process, allowing Wi-Fi and API to stabilize first.
2. **Passive Scanning (`active: false`)**: Reducing CPU load by only listening for advertisements instead of actively requesting data.
3. **ESP-IDF Framework**: Using the modern IDF framework for better task scheduling on a single core.



---

## 🚀 Features

* **YLYK01YL Remote Support**: Full mapping of all 6 buttons (including long presses).
* **Dual Light Mode**: Seamless switching between **Main CWWW Light** and **Moonlight (Nightlight)**.
* **Stable Connectivity**: Optimized for single-core execution to prevent reboots.
* **Local Control**: 100% cloud-free operation via Home Assistant API.

---

## 🔧 Configuration

### 1. Remote Setup
You need the MAC address of your remote. In this version, since the lamp uses the `syssi` component, **bindkey is not required** for standard YLYK01YL remotes as they often broadcast in an unencrypted format compatible with this platform.

### 2. Remote Key Mapping
| Keycode | Button | Action | Long Press |
| :--- | :--- | :--- | :--- |
| `0` | **ON** | Turn on Main Light (4000K) | - |
| `1` | **OFF** | Turn off everything | - |
| `2` | **SUN** | Warm Mode (2700K / 100%) | - |
| `3` | **+** | Increase brightness | Set to 100% |
| `4` | **M** | Toggle Nightlight / Cold White | - |
| `5` | **-** | Decrease brightness | Set to 5% |

---

## 🔌 Hardware Wiring (Internal)

| Function | Pin | Notes |
| :--- | :--- | :--- |
| **Warm White PWM** | GPIO19 | LED Driver Control |
| **Cold White PWM** | GPIO21 | LED Driver Control |
| **Nightlight PWM** | GPIO23 | High Frequency (19.5kHz) |
| **ADC Sensors** | GPIO36/32 | Voltage/Load monitoring |

## The ESP32 will enter the serial bootloader when GPIO0 (test point TP1 at the back) is held low (GND) on reset / power.

---

## 🛠 How to Use

1.  Copy the provided YAML file to your ESPHome dashboard.
2.  Update the `substitutions` block with your remote's MAC address:
    ```yaml
    substitutions:
      name: your-lamp-name
      remote_mac: "XX:XX:XX:XX:XX:XX"
    ```
3.  Ensure your `secrets.yaml` contains `wifi_ssid` and `wifi_password`.
4.  Flash via OTA or USB Serial.

## 📜 Credits
* Based on the work by [@syssi](https://github.com/syssi/esphome-yeelight-ceiling-light).
* Optimized for Single-Core stability.
