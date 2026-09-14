<div align="center">

# 🚁 Mark4 V2 — Custom FPV Drone Build
### 10" Freestyle / Long-Range Quadcopter

**Personal Build Project**

---

[![Betaflight](https://img.shields.io/badge/Firmware-Betaflight-8A2BE2?style=flat-square)](https://betaflight.com)
[![ExpressLRS](https://img.shields.io/badge/RC_Link-ExpressLRS-1E90FF?style=flat-square)](https://www.expresslrs.org)
[![Frame](https://img.shields.io/badge/Frame-Mark4_V2_10in-orange?style=flat-square)](#hardware)
[![Status](https://img.shields.io/badge/Status-In_Progress-yellow?style=flat-square)](#build-status)
[![License](https://img.shields.io/badge/License-MIT-green?style=flat-square)](LICENSE)

</div>

---

## Overview

This is a ground-up FPV quadcopter build on a **Mark4 V2 10" (427mm)** frame, designed for freestyle flying with long-range capability. The build runs stock **Betaflight** on an F722-based flight controller stack, uses **ExpressLRS (915MHz)** for the RC link, and transmits analog FPV video over 5.8GHz.

The project started as an exploration of building a flight controller entirely from scratch (custom firmware on STM32/ESP32), but was descoped in favor of a proven Betaflight-based stack to prioritize getting a stable, flying aircraft first. Custom firmware work may be revisited as a separate future project.

---

## Features (Planned)

- **10" prop, 900KV motor combo** — tuned for efficient cruising with freestyle-capable punch
- **ICM42688P gyro** on the FC stack — modern, low-noise IMU for clean PID performance
- **ExpressLRS 915MHz link** — long-range-capable RC control with better penetration than 2.4GHz
- **5.8GHz analog FPV** via a 3W-capable VTX (operated within legal power limits — see [Notes](#notes))
- **Dual-frequency-capable receiver** for flexibility across future builds
- **Betaflight dynamic notch filtering** — handles motor noise/vibration without hand-tuned hardware filters

---

## System Architecture

```
┌────────────────────┐         2.4GHz or 915GHz ELRS          ┌───────────────────────┐
│   Radio / Handset   │ ───────────────────────────────────►  │   SpeedyBee Nano RX   │
│  915MHz ELRS TX     │            RC Link                     │  (Dual-Freq, 915MHz)  │
└────────────────────┘                                         └───────────┬───────────┘
                                                                            │ CRSF (UART)
                                                                            ▼
                                                                ┌───────────────────────┐
                                                                │   DAKEFPV F722 FC     │
                                                                │  STM32F722 + ICM42688P │
                                                                │   (Betaflight)         │
                                                                └───────────┬───────────┘
                                                                            │ DShot
                                                                            ▼
                                                                ┌───────────────────────┐
                                                                │  BLHeli_S 60A 4-in-1  │
                                                                │        ESC             │
                                                                └───────────┬───────────┘
                                                                            │
                                                            ┌───────────────┼───────────────┐
                                                            ▼               ▼               ▼ ▼
                                                          M1              M2              M3 M4
                                                       (3115 900KV Motors — 10" Props, x4)

┌────────────────────┐        5.8GHz Analog Video            ┌───────────────────────┐
│   B19 19x19mm       │ ───────────────────────────────────► │   FPV Goggles          │
│   1500TVL Camera    │      via Reaper Extreme 3W VTX        │   (TBD)                │
└────────────────────┘        + Albatross V2 antenna          └───────────────────────┘
```

---

## Hardware

| Component | Specification | Role |
|---|---|---|
| Frame | Mark4 V2, 10" (427mm wheelbase), 30.5x30.5 stack mount | Airframe |
| Flight Controller | DAKEFPV F722, STM32F722, ICM42688P gyro | Flight stabilization (Betaflight) |
| ESC | BLHeli_S 60A 4-in-1 (bundled with FC as a stack) | Motor power delivery |
| Motors | 4x 3115, 900KV, 3-6S rated | Propulsion |
| Propellers | 10" | Lift |
| Camera | B19, 19x19mm, 1500TVL, 1/3" CMOS, 2.1mm lens, WDR/Low-Lux | FPV video capture |
| VTX | FOXEER Reaper Extreme 3W, 5.8GHz, MMCX, adjustable 25mW–3W | Video transmission |
| VTX Antenna | iFlight Albatross V2, 5.8GHz, 2.4dBi, RHCP, 90°, MMCX | Video signal radiation |
| Receiver (RX) | SpeedyBee Nano ELRS, Dual-Frequency (2.4G/915MHz) | RC signal reception |
| TX Module | 915MHz ExpressLRS, JR-bay | RC signal transmission |
| Battery | *TBD — 6S LiPo/Li-ion, sized for 3115 900KV motors* | Power |
| Goggles | *TBD — must support 5.8GHz analog* | FPV video display |

### Wiring Summary

| Link | Interface | Parameters |
|---|---|---|
| RX ↔ FC | UART (CRSF) | Matched baud per ELRS default |
| FC ↔ ESC | DShot (via stack connector) | DShot300/600 |
| ESC ↔ Motors | 3-phase, bullet connectors or direct solder | — |
| Camera → VTX | Analog video (solder pads) | 5V from FC/VTX BEC |
| VTX → Antenna | MMCX | Direct-mount, no adapter needed |

---

## Build Status

- [x] Frame, motors, props sourced
- [x] FC + ESC stack sourced
- [x] Camera + VTX + VTX antenna sourced (connector compatibility confirmed: MMCX-to-MMCX)
- [x] RX + TX module sourced and frequency-matched (915MHz)
- [ ] Battery selection
- [ ] Goggles selection
- [ ] Physical assembly
- [ ] Betaflight configuration (ports, receiver protocol, motor mapping)
- [ ] ELRS bind
- [ ] Props-off bench test
- [ ] PID tuning / filter tuning
- [ ] Maiden flight

---

## Getting Started (Once Assembled)

**1. Bind the RC link**
Bind the SpeedyBee Nano RX to the 915MHz ELRS TX module before connecting anything else.

**2. Flash / verify Betaflight**
Connect the F722 FC via USB and confirm it's running a current Betaflight target in Betaflight Configurator.

**3. Configure ports & receiver**
Set the UART used by the RX to **Serial-based Receiver (CRSF)** in the Ports tab, and select **CRSF** as the receiver protocol.

**4. Motor direction & mapping check — props OFF**
Use the Motors tab in Betaflight Configurator to spin each motor individually and confirm direction and position match the frame's layout.

**5. Configure VTX power**
Set the Reaper Extreme's power level appropriately for your local regulations (see [Notes](#notes)).

**6. Bench test, then hand test**
Verify pitch/roll/yaw response direction is correct before ever attempting flight.

**7. Maiden flight**
Start conservative — low throttle, open area, be ready to cut power.

---

## Notes

- **VTX legal power**: The Reaper Extreme 3W is adjustable from 25mW to 3W. In the US and many other countries, operating above 25mW on 5.8GHz analog video legally requires a HAM (amateur radio) license. Set power levels according to your local regulations.
- **RX frequency**: The SpeedyBee Nano RX is dual-frequency capable (2.4GHz/915MHz) — confirmed set to 915MHz to match the TX module.
- **Firmware**: Stock Betaflight was chosen over custom flight-controller firmware to prioritize a working, flying aircraft. A from-scratch flight controller (e.g., on ESP32-S3, following community "clean architecture" approaches) remains a possible future project, separate from this build.
- This README reflects the drone build only. A custom radio transmitter/handset build was considered separately and is out of scope here.

---

## References

**Firmware & Protocol**
- [Betaflight Documentation](https://betaflight.com/docs/wiki)
- [ExpressLRS Documentation](https://www.expresslrs.org)

**Hardware**
- Mark4 V2 10" Frame — long-range/freestyle carbon fiber frame, 427mm wheelbase
- DAKEFPV F722 60A Stack — STM32F722 FC + BLHeli_S 60A 4-in-1 ESC
- FOXEER Reaper Extreme 3W VTX
- iFlight Albatross V2 Antenna

---

<div align="center">

*Personal FPV Drone Build — In Progress*

</div>
