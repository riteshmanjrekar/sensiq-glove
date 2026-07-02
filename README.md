# 🖐️ SensiQ Glove — Low-Cost Gesture-to-Speech Assistive Glove

A low-cost, wearable assistive device that converts hand gestures into speech — designed for individuals with speech impairments, temporary or permanent conditions affecting speech, or anyone who needs an alternative communication method. Built using Arduino Nano, FSR sensors, MPU6050, and DFPlayer Mini.

---

## 📌 Overview

Millions of people worldwide live with speech impairments due to conditions like ALS, stroke, cerebral palsy, laryngectomy, or temporary vocal cord injuries. Commercial gesture-to-speech devices are expensive and inaccessible. SensiQ Glove solves this by providing an affordable, wearable alternative that translates finger gestures and hand orientations into spoken audio output in real time.

The key innovation: **FSR (Force Sensitive Resistor) sensors instead of flex sensors** — significantly reducing the cost of the glove while maintaining reliable gesture detection. A multi-modal input system using FSR sensors, MPU6050 orientation detection, and a Hall effect magnetic trigger creates a rich vocabulary of gestures mapped to different audio banks.

---

## ⚙️ Features

- Finger bend detection using FSR sensors (threshold-based)
- Single finger gestures trigger individual audio tracks
- Combination gestures (multiple fingers simultaneously) trigger unique tracks
- 3-axis hand orientation detection via MPU6050 (palm up / sideways / down)
- Orientation-based audio bank switching — same gesture plays different track per mode
- Hall effect magnetic unlock — chest proximity triggers a special audio bank
- 3 standard modes + 1 magnetic unlock mode = rich gesture vocabulary
- Audio output via DFPlayer Mini + PAM8403 amplifier + speaker
- Tracks stored on SD card — fully customizable audio
- Custom battery pack powered — fully wireless and portable
- Compact form factor using Arduino Nano
- Auto-calibration on startup — adapts FSR rest values to each user's hand pressure and glove fit
- Expandable to all 5 fingers (thumb + 4 fingers)
- Significantly lower cost than commercial AAC (Augmentative and Alternative Communication) devices

---

## 🛠️ Hardware Components

| Component | Purpose |
|---|---|
| Arduino Nano | Main microcontroller — small form factor for wearable use |
| FSR Sensors (x2, expandable to x5) | Finger bend/pressure detection |
| 10kΩ Resistors | Voltage divider circuit for FSR analog reading |
| MPU6050 IMU Module | Hand orientation detection (palm up/sideways/down) via I2C |
| Hall Effect Sensor Module | Magnetic proximity detection for special mode unlock |
| DFPlayer Mini | MP3 audio playback from SD card |
| MicroSD Card | Stores all audio tracks organized in banks |
| PAM8403 Amplifier Module | Boosts audio output volume |
| Small Speaker | Audio output |
| Custom Battery Pack | Portable wireless power supply |
| Glove | Wearable base for mounting all components |
| Jumper Wires | Component connections |

---

## 📱 Software & Platform

- **Arduino IDE** — Firmware development
- **Embedded C/C++** — Programming language
- **DFRobotDFPlayerMini Library** — DFPlayer Mini control
- **MPU6050 / Wire Library** — IMU orientation reading via I2C
- **Analog threshold logic** — FSR gesture detection

---

## 🔄 How It Works

### Core Gesture Detection
```
FSR Sensor reads analog pressure value
            ↓
  Value above threshold? → Finger = BENT
  Value below threshold? → Finger = STRAIGHT
            ↓
  Check all active FSR sensors simultaneously
            ↓
  Single finger bent   → Play assigned single track
  Multiple fingers bent → Play assigned combo track
```

### Orientation-Based Mode Switching
```
MPU6050 reads accelerometer/gyroscope data
            ↓
  Determines hand orientation:
  ┌─────────────────────────────────────────────────┐
  │ Palm UP       → Audio Bank A (e.g. Greetings)   │
  │ Palm SIDEWAYS → Audio Bank B (e.g. Requests)    │
  │ Palm DOWN     → Audio Bank C (e.g. Emergency)   │
  └─────────────────────────────────────────────────┘
            ↓
  Same finger gesture → different track per bank
```

### Hall Effect Magnetic Unlock
```
User brings palm close to chest
(small magnet attached to clothing near chest)
            ↓
  Hall Effect Sensor detects magnetic field
            ↓
  Unlocks Special Audio Bank D
  (custom phrases, personal messages, etc.)
            ↓
  Finger gestures now play Bank D tracks
```

### Auto-Calibration on Startup
```
User places palm flat on surface (rest position)
↓
Arduino reads all FSR baseline values for ~few seconds
↓
Rest values stored as reference per sensor
↓
All gesture thresholds calculated relative to rest value
↓
Glove ready — adapts to any user's hand size and glove fit

> This ensures reliable detection regardless of how tightly the glove fits or how much natural pressure different users apply.
```

---

## 📊 System Architecture

```
[FSR Sensors] ──────────────────────────┐
[MPU6050 IMU] ──── I2C ────────────────▶ [Arduino Nano] ──▶ [DFPlayer Mini] ──▶ [PAM8403 Amp] ──▶ [Speaker]
[Hall Effect] ──────────────────────────┘         │                │
                                                   │         [MicroSD Card]
                                              [Battery Pack]  (Audio Banks)
```

---

## 🗂️ Audio Bank Structure

```
SD Card/
│
├── /01/    ← Bank A — Palm UP mode (e.g. Greetings)
│   ├── 001.mp3   (Index finger bent)
│   ├── 002.mp3   (Middle finger bent)
│   ├── 003.mp3   (Index + Middle combo)
│   └── ...
│
├── /02/    ← Bank B — Palm SIDEWAYS mode (e.g. Requests)
│   ├── 001.mp3
│   └── ...
│
├── /03/    ← Bank C — Palm DOWN mode (e.g. Emergency)
│   ├── 001.mp3
│   └── ...
│
└── /04/    ← Bank D — Magnetic UNLOCK mode (Special phrases)
    ├── 001.mp3
    └── ...
```

> All audio tracks are fully customizable — record any voice, language, or phrase and place them in the correct bank folder.

---

## 💻 Code Structure

```
SensiQ-Glove/
│
├── src/
│   └── sensiq_glove.ino       # Main Arduino sketch
│
├── circuit/
│   └── circuit_diagram.png    # Circuit diagram (coming soon)
│
├── audio/
│   └── sd_card_structure.md   # How to organize SD card audio banks
│
├── docs/
│   └── calibration_guide.md   # FSR threshold calibration guide
│
└── README.md
```

---

## 🚀 Getting Started

### Prerequisites

- Arduino IDE installed
- Arduino Nano board package installed
- Libraries installed:
  - `DFRobotDFPlayerMini`
  - `MPU6050` or `Wire` + `I2Cdev`

### Installation

1. **Clone this repository**
```bash
git clone https://github.com/YOUR_USERNAME/sensiq-glove.git
cd sensiq-glove
```

2. **Prepare SD card**
- Format SD card as FAT32
- Create folders `/01/`, `/02/`, `/03/`, `/04/`
- Add `.mp3` audio tracks in each folder (001.mp3, 002.mp3 ...)

3. **Calibrate FSR threshold**

Open `src/sensiq_glove.ino` and adjust:
```cpp
int fsrThreshold = 500;  // Adjust based on your FSR and finger pressure
```

4. **Upload to Arduino Nano**
- Select board: `Arduino Nano`
- Select processor: `ATmega328P`
- Select correct COM port
- Click Upload

5. **Wear and calibrate MPU6050**
- Place hand in neutral position on startup
- MPU6050 auto-calibrates on boot

---

## 📐 Circuit Connections

### FSR Voltage Divider (per sensor)
```
5V ──── FSR Sensor ──── A0 (Arduino)
                    |
                  10kΩ
                    |
                  GND
```

| Arduino Pin | Connected To |
|---|---|
| A0 | FSR Sensor 1 (finger 1) |
| A1 | FSR Sensor 2 (finger 2) |
| A2–A4 | FSR Sensors 3–5 (expansion) |
| SDA (A4) | MPU6050 SDA |
| SCL (A5) | MPU6050 SCL |
| D2 | Hall Effect Sensor OUT |
| D10 (TX) | DFPlayer Mini RX |
| D11 (RX) | DFPlayer Mini TX |
| 5V | VCC for all modules |
| GND | Common Ground |

> Full circuit diagram will be added soon.

---

## 🔋 Power System

- Custom battery pack for fully wireless operation
- Compatible with any 5V USB power bank for quick use
- Low power consumption due to Arduino Nano's compact design

---

## 📈 Future Improvements (v2.0 Roadmap)

- [ ] Expand to all 5 fingers (thumb + 4 fingers) with 5 FSR sensors
- [ ] Bluetooth audio output (wireless speaker support)
- [ ] Mobile app for real-time audio bank customization
- [ ] OLED display showing current mode and active gesture
- [ ] Machine learning-based gesture recognition for complex signs
- [ ] Multi-language audio bank support
- [ ] Rechargeable Li-ion battery with TP5100 charging circuit
- [ ] 3D printed glove housing for cleaner component mounting
- [ ] Sign language alphabet support
- [ ] Haptic motor feedback on calibration complete — vibration confirms glove is ready to use

---

## 🎯 Problem It Solves

| Problem | SensiQ Glove Solution |
|---|---|
| Commercial AAC devices cost ₹50,000–₹5,00,000+ | SensiQ Glove built under ₹2,000 |
| Flex sensors make gloves expensive | FSR sensors used — same function, fraction of cost |
| Fixed gesture vocabulary | Orientation-based bank switching multiplies vocabulary |
| Limited to one context | 3 modes + magnetic unlock = 4 different phrase sets |
| Not portable | Custom battery pack — fully wireless |
| Audio not customizable | Any MP3 track can be placed on SD card |

---

## 🎓 Learning Outcomes

- FSR sensor interfacing and voltage divider design
- MPU6050 IMU programming via I2C
- Hall effect sensor integration
- DFPlayer Mini audio control via SoftwareSerial
- PAM8403 audio amplifier circuit design
- Multi-modal input fusion logic
- Wearable embedded systems design
- Assistive technology product thinking
- Runtime sensor calibration and adaptive threshold logic

---

## 🏆 Project Highlights

- ✅ Fully working system — all modes implemented and tested
- ✅ Novel use of FSR over flex sensors for cost reduction
- ✅ Multi-modal input: pressure + orientation + magnetic proximity
- ✅ Customizable audio banks — adaptable to any language or need
- ✅ Designed for real-world assistive use, not just demonstration
- ✅ Expandable architecture — easy to scale to 5 fingers

---

## 👤 Author

**Ritesh Manjrekar]**
- GitHub: [@riteshmanjrekar(https://github.com/riteshmanjrekar)
- LinkedIn: (www.linkedin.com/in/ritesh-manjrekar-397007417)

---

## 📄 License

This project is open source and available under the [MIT License](LICENSE).

---

> SensiQ Glove was built with one goal — give a voice to those who struggle to speak, at a price anyone can afford. Built as part of my embedded systems and assistive technology journey.
