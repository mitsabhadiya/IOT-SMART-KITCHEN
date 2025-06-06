# 🏠 IoT Smart Kitchen System

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Arduino](https://img.shields.io/badge/Arduino-IDE-blue.svg)](https://www.arduino.cc/)
[![ESP32](https://img.shields.io/badge/ESP32-Compatible-green.svg)](https://www.espressif.com/en/products/socs/esp32)
[![Blynk](https://img.shields.io/badge/Blynk-IoT-orange.svg)](https://blynk.io/)

An intelligent IoT-based kitchen monitoring and control system that automatically manages temperature, air quality, and ventilation using ESP32 microcontrollers and wireless communication.

🎯 Features

- **Real-time Environmental Monitoring**: Temperature, humidity, and gas detection
- **Automated Climate Control**: Smart fan speed control and servo-actuated air valves
- **Wireless Communication**: ESP-NOW protocol for reliable inter-device communication
- **Remote Control**: Blynk IoT platform for mobile and web dashboard access
- **Data logging**: SD card storage with MATLAB integration for real-time plotting
- **Safety Alerts**: Gas detection with buzzer and LED warnings
- **Peltier Module**: Heating and cooling capabilities

## 🛠️ Hardware Components

### Core Components
- **ESP8266** (Main Controller)
- **XIAO ESP32-S3** (Sensor Node)
- **DHT11** Temperature & Humidity Sensor
- **MQ-2** Gas Sensor
- **16GB MicroSD Card** + Breakout Module

### Control Components
- **3x Servo Motors** (Air valve control)
- **12V Axial Fan** with Tachometer
- **Peltier Module** (TEC1-12706)
- **IRLZ34N N-Channel MOSFET** (Fan control)
- **128x64 OLED Display**

### Safety & Alert Components
- **Buzzer** (Gas detection alert)
- **LED Indicators**
- **220Ω Resistors**

## 📋 Prerequisites

### Software Requirements
- [Arduino IDE](https://www.arduino.cc/en/software) (v1.8.19 or later)
- [MATLAB](https://www.mathworks.com/products/matlab.html) (for data visualization)
- [Blynk IoT App](https://blynk.io/) (Mobile/Web)

### Arduino Libraries
```cpp
// Core Libraries
#include <WiFi.h>
#include <esp_now.h>
#include <DHT.h>
#include <SD.h>
#include <SPI.h>

// Display & Control
#include <Wire.h>
#include <Adafruit_SSD1306.h>
#include <ESP32Servo.h>

// IoT Platform
#include <BlynkSimpleEsp32.h>
```

### Library Installation
1. Open Arduino IDE
2. Go to **Tools** → **Manage Libraries**
3. Install the following libraries:
   - `DHT sensor library` by Adafruit
   - `ESP32Servo` by Kevin Harrington
   - `Adafruit SSD1306` by Adafruit
   - `Blynk` by Volodymyr Shymanskyy
   - `ArduinoJson` by Benoit Blanchon

## ⚡ Quick Start

### 1. Clone the Repository
```bash
git clone https://github.com/yourusername/iot-smart-kitchen.git
cd iot-smart-kitchen
```

### 2. Hardware Setup
1. Connect components according to the [wiring diagram](docs/wiring-diagram.md)
2. Ensure proper power supply (5V for servos, 12V for fan)
3. Insert formatted SD card into the breakout module

### 3. Software Configuration

#### Configure WiFi Credentials
```cpp
// In main_controller.ino
const char* ssid = "YOUR_WIFI_SSID";
const char* password = "YOUR_WIFI_PASSWORD";

// In sensor_node.ino  
const char* ssid = "YOUR_WIFI_SSID";
const char* password = "YOUR_WIFI_PASSWORD";
```

#### Setup Blynk Authentication
```cpp
// In main_controller.ino
char auth[] = "YOUR_BLYNK_AUTH_TOKEN";
```

### 4. Upload Code
1. Upload `sensor_node/sensor_node.ino` to XIAO ESP32-S3
2. Upload `main_controller/main_controller.ino` to ESP8266
3. Open Serial Monitor to verify communication

### 5. Configure Blynk Dashboard
1. Create new project in Blynk App
2. Add widgets according to [Blynk setup guide](docs/blynk-setup.md)
3. Get your Auth Token and update the code

## 📊 Usage

### Automatic Mode
The system automatically:
- Monitors temperature and adjusts fan speed via PWM
- Controls servo valves based on temperature thresholds:
  - `< 20°C`: Valves closed
  - `20-30°C`: Proportional control
  - `> 30°C`: Full ventilation mode
- Activates Peltier module for heating/cooling
- Logs data every 5 seconds to SD card

### Manual Control
Via Blynk dashboard:
- Set target temperature
- Manual fan speed control
- Individual servo position control
- System reset and calibration
- Real-time monitoring graphs

### Safety Features
- Gas detection triggers immediate alerts
- Automatic valve closure on system failure
- Failsafe servo positions during WiFi loss

## 📈 Data Analysis

### MATLAB Integration
```matlab
% Run the live plotting script
run('matlab/live_data_plot.m')
```

Features:
- Real-time temperature and humidity plotting
- Historical data analysis from SD card
- Trend analysis and pattern recognition

### Data Format
CSV files stored on SD card:
```
Timestamp,Temperature,Humidity,Gas_Level,Fan_RPM
2025-06-06 14:30:15,24.5,55.3,165,750
2025-06-06 14:30:20,24.7,55.1,162,780
```

## 🔧 Configuration

### Temperature Thresholds
```cpp
// In main_controller.ino
const float TEMP_MIN = 20.0;      // Minimum comfort temperature
const float TEMP_MAX = 30.0;      // Maximum comfort temperature  
const float TEMP_TARGET = 25.0;   // Default target temperature
```

### Gas Detection Sensitivity
```cpp
// In sensor_node.ino
const int GAS_THRESHOLD = 200;    // Adjust based on calibration
```

### Communication Settings
```cpp
// ESP-NOW MAC Address (update with your device MAC)
uint8_t broadcastAddress[] = {0xFF, 0xFF, 0xFF, 0xFF, 0xFF, 0xFF};
```

## 🐛 Troubleshooting

### Common Issues

#### ESP-NOW Communication Fails
- Verify MAC addresses are correct
- Check power supply stability
- Ensure both devices are on same WiFi channel

#### DHT11 Returns NaN Values
- Check wiring connections
- Replace sensor if faulty
- Add delay between readings

#### Servo Jittering
- Use ESP32Servo library instead of default Servo
- Ensure adequate power supply (5V, 2A minimum)
- Add capacitors for power stabilization

#### SD Card Not Detected
- Format card as FAT32
- Check SPI wiring connections
- Verify CS pin configuration

### Debug Mode
Enable debug output:
```cpp
#define DEBUG_MODE 1
```

### Serial Monitor Commands
- `RESET` - Reset all components
- `CALIBRATE` - Recalibrate sensors
- `STATUS` - Show system status

## 🔒 Security Considerations

- ESP-NOW provides encrypted communication
- Physical SD card storage prevents cloud data breaches
- Regular firmware updates recommended
- Disable debug ports in production
- Use private WiFi networks

## 🤝 Contributing

1. Fork the repository
2. Create feature branch (`git checkout -b feature/AmazingFeature`)
3. Commit changes (`git commit -m 'Add AmazingFeature'`)
4. Push to branch (`git push origin feature/AmazingFeature`)
5. Open Pull Request

### Development Guidelines
- Follow Arduino coding standards
- Add comments for complex functions
- Test on hardware before submitting
- Update documentation for new features

## 📝 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## 👥 Authors

**Group 7 - Connected Systems and IoT**
- **Mit Vinubhai Sabhadiya** - Project Lead, ESP32 Communication, MATLAB Integration
- **Mestiyage Gunathilake** - Arduino Development, System Overview, Blynk Dashboard
- **Shah Muhammad** - DHT11 Implementation, Fan Control
- **Oluwatmide Omojayogbe** - SD Card Module, Circuit Design
- **Naman Sharma** - Servo Calibration, Testing, Implementation

## 🙏 Acknowledgments

- Espressif Systems for ESP32/ESP8266 documentation
- Blynk team for IoT platform
- Arduino community for libraries and support
- University tutors: Muhammad Jamro, Lawal Daura

## 📚 Documentation

- [Hardware Wiring Guide](docs/wiring-diagram.md)
- [Blynk Setup Instructions](docs/blynk-setup.md)
- [MATLAB Integration Guide](docs/matlab-setup.md)
- [API Reference](docs/api-reference.md)
- [Troubleshooting Guide](docs/troubleshooting.md)

## 🔗 Useful Links

- [ESP-NOW Protocol Documentation](https://docs.espressif.com/projects/esp-idf/en/latest/esp32/api-reference/network/esp_now.html)
- [Blynk IoT Platform](https://blynk.io/)
- [DHT11 Sensor Datasheet](http://www.aosong.com/userfiles/files/media/DHT11%E8%8B%B1%E6%96%87%E7%89%88%E8%AF%B4%E6%98%8E%E4%B9%A6.pdf)
- [MQ-2 Gas Sensor Datasheet](https://www.hwsensor.com/attachments/article/173/MQ-2.pdf)

---

**⭐ If you find this project helpful, please give it a star!**

**🚀 Ready to build your own IoT Smart Kitchen? Start with the [Quick Start Guide](#-quick-start)!**
