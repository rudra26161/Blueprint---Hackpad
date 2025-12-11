# Rudra's Hack Pad!

A custom 15 key macropad built with the XIAO RP2040, rotary encoder, and addressable RGB LEDs.  
This README documents the design, schematic, PCB, case, and bill of materials (BOM).

---

## 📸 Screenshots

### Overall Hackpad
![Hackpad Screenshot]([images/hackpad_overview.png](https://github.com/rudra26161/Blueprint---Hackpad/blob/main/Screenshot%202025-12-11%20161556.png))

### Schematic
![Schematic Screenshot](images/hackpad_schematic.png)

### PCB Layout
![Hackpad Screenshot](Screenshot%202025-12-11%20161556.png)

### Case & Assembly
![Case Screenshot](images/hackpad_case.png)

---

## 🛠️ Features
- 15k key matrix of push-button switches with diodes for isolation
- Rotary encoder for volume control and layer switching
- 2 × SK6812MINI addressable RGB LEDs for visual feedback
- Powered by Seeed Studio XIAO RP2040 microcontroller
- KMK firmware support with layers, macros, and encoder handling

---

## 📦 Bill of Materials (BOM)

| Item | Quantity | Description |
|------|----------|-------------|
| XIAO RP2040 | 1 | Microcontroller board |
| Push-button switches | 16 | Standard MX-style |
| Diodes (1N4148) | 16 | For matrix isolation |
| Rotary encoder | 1 | With push-button switch |
| SK6812MINI RGB LEDs | 2 | Addressable LEDs for feedback |
| PCB | 1 | Custom-designed Hackpad PCB |
| Case | 1 | 3D-printed or laser-cut enclosure |
| Mounting hardware | 4 | Screws + standoffs for case assembly |

---

## ⚙️ Firmware
- Written in [KMK Firmware](https://github.com/KMKfw/kmk_firmware)
- Supports:
  - Matrix scanning
  - Layer switching
  - Macros
  - Encoder input
  - RGB LED feedback
