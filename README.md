# USB-C LiPo Charger & 3.3V Power Management Board

A compact, highly efficient power management module designed for battery-powered IoT and embedded hardware projects. This board combines USB Type-C power delivery, single-cell Lithium-Polymer (LiPo/Li-Ion) battery charging via the **MCP73831**, and ultra-low noise 3.3V regulation via the **AP2112K** LDO.

---

## 🌟 Key Features

* **Modern USB Type-C Input:** Native USB-C connector with onboard $5.1\text{ k}\Omega$ pull-down resistors on CC1/CC2 pins, allowing compatibility with USB-C to USB-C power supplies as well as legacy USB-A cables.
* **Integrated LiPo Charging:** Powered by the **MCP73831** linear charge management controller featuring constant-current/constant-voltage (CC/CV) algorithms.
* **Programmable Charge Rate:** Charge current configurable via an external resistor ($R_{\text{PROG}}$), factory set to ~500mA (or 100mA for smaller batteries).
* **High-Efficiency 3.3V LDO Regulator:** Incorporates the **AP2112K-3.3** offering up to 600mA output current with very low dropout voltage and high ripple rejection, ideal for Wi-Fi/Bluetooth MCUs (ESP32, STM32, nRF52).
* **Dual Status LEDs:** Visually indicates charging status (Charging vs. Charge Complete) and 3.3V system power status.
* **ESD & Reverse Polarity Considerations:** Thermal shutdown, short-circuit protection, and input power filtering.

---

## 📊 Technical Specifications

| Parameter | Value / Range | Notes |
| :--- | :--- | :--- |
| **Input Voltage ($V_{\text{BUS}}$)** | 4.5V – 5.5V DC | Via USB Type-C |
| **Battery Chemistry** | 3.7V Single-Cell LiPo / Li-Ion | $4.2\text{V}$ max charge voltage |
| **Charge Controller** | MCP73831T-2ACI/OT | SOT-23-5 |
| **Default Charge Current ($I_{\text{REG}}$)** | 500mA ($R_{\text{PROG}} = 2\text{ k}\Omega$) | Configurable from 100mA to 500mA |
| **Regulator (LDO)** | AP2112K-3.3TRG1 | SOT-23-5 |
| **Output Voltage ($V_{\text{OUT}}$)** | 3.3V DC ($\pm1.5\%$) | Fixed output |
| **Max Output Current** | 600mA continuous | Peak capability for ESP32 RF bursts |
| **Dropout Voltage** | ~250mV @ 600mA | Enables stable 3.3V even as battery drops |

---

## 🏗️ System Block Diagram

```
====================================================================================
                        [ USB Type-C Input (5V) ]
                                    │
               ┌────────────────────┴────────────────────┐
               ▼                                         ▼
   [ CC1/CC2 5.1k Pull-Downs ]              [ MCP73831 LiPo Charger ]
   (Enables USB-C PD 5V)                                 │
                                                         ├────────> [ Charge LED ]
                                                         │
                                                         ▼
                                             [ LiPo Battery (3.7V) ]
                                                         │
                                                         ▼
                                            [ AP2112K 3.3V LDO Regulator ]
                                                         │
                                                         ├────────> [ Power LED ]
                                                         │
                                                         ▼
                                              [ 3.3V System Output ]
====================================================================================
```

---

## ⚙️ Pinout Configuration

### Input & Output Connectors

| Connector / Pad | Pin | Signal | Description |
| :--- | :--- | :--- | :--- |
| **USB-C** | - | `VBUS` / `GND` | 5V Power Input from USB Charger |
| **J1 (Battery)** | 1 | `BAT+` | Positive battery connection (+3.7V nominal) |
| | 2 | `BAT-` / `GND` | Common System Ground |
| **J2 (3.3V Out)**| 1 | `+3.3V` | Regulated 3.3V Output for System/MCU |
| | 2 | `GND` | Common System Ground |
| **J3 (Raw Bat Out)**| 1 | `VBAT` | Unregulated Battery Voltage Output (optional) |
| | 2 | `GND` | Common System Ground |

---

## 🧮 Calculating $R_{\text{PROG}}$ for Charge Current

The charging current $I_{\text{REG}}$ of the MCP73831 is set using a single programming resistor ($R_{\text{PROG}}$) connected from the `PROG` pin to Ground. 

The formula provided by Microchip is:

$$I_{\text{REG}} = \frac{1000\text{ V}}{R_{\text{PROG}}\text{ (k}\Omega\text{)}}$$

### Recommended Resistor Values:
* **100 mA Charge Rate:** $R_{\text{PROG}} = 10\text{ k}\Omega$ *(Safe for small 100mAh–200mAh batteries)*
* **200 mA Charge Rate:** $R_{\text{PROG}} = 5\text{ k}\Omega$
* **500 mA Charge Rate:** $R_{\text{PROG}} = 2\text{ k}\Omega$ *(Standard for >500mAh batteries)*

---

## 📋 Bill of Materials (BOM)

| Reference | Qty | Value / Part Number | Package / Footprint | Description |
| :--- | :--- | :--- | :--- | :--- |
| **U1** | 1 | MCP73831T-2ACI/OT | SOT-23-5 | LiPo Charge Controller |
| **U2** | 1 | AP2112K-3.3TRG1 | SOT-23-5 | 3.3V 600mA Low Dropout Regulator |
| **J_USB** | 1 | TYPE-C-31-M-12 | 16-Pin / 12-Pin SMD | USB Type-C Receptacle |
| **J_BAT** | 1 | JST PH 2.0mm | 2-Pin Thru-hole / SMD | LiPo Battery Connector |
| **R1, R2** | 2 | $5.1\text{ k}\Omega$ ($\pm1\%$) | 0603 | CC1/CC2 USB-C Pull-Down Resistors |
| **R3** | 1 | $2\text{ k}\Omega$ ($\pm1\%$) | 0603 | $R_{\text{PROG}}$ Charge Current Resistor (500mA) |
| **R4, R5** | 2 | $1\text{ k}\Omega$ ($\pm5\%$) | 0603 | LED Current Limiting Resistors |
| **C1, C2** | 2 | $4.7\mu\text{F}$ X7R / X5R | 0805 | $V_{\text{BUS}}$ & $V_{\text{BAT}}$ Decoupling Capacitors |
| **C3, C4** | 2 | $1.0\mu\text{F}$ X7R / X5R | 0603 | AP2112K Input & Output Stability Capacitors |
| **D1** | 1 | RED LED | 0603 | STAT (Charging Indicator) |
| **D2** | 1 | GREEN LED | 0603 | 3.3V Power Indicator |

---

## 🛠️ PCB Layout & Guidelines

1. **Thermal Dissipation:** Both the MCP73831 and AP2112K dissipate heat during high-current operations. Place copper pours/planes connected to `GND` directly beneath these ICs to act as heat sinks.
2. **Capacitor Placement:** Place the input/output ceramic capacitors ($C1$, $C2$, $C3$, $C4$) as close as humanly possible to the power pins of the ICs to suppress noise and maintain LDO loop stability.
3. **USB-C Routing:** Route the $V_{\text{BUS}}$ and `GND` traces from the USB-C connector with at least **25 mil to 40 mil** trace widths to handle up to 1A of input power without voltage drop.

---

## 📜 License

Distributed under the MIT License. Feel free to use, modify, and integrate this design into your open-source or commercial products.
