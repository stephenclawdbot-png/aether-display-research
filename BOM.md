# AETHER Display - Bill of Materials

## Cycle 4 v0.1 Prototype Build

*Last updated: 2026-03-28*

---

## Executive Summary

**Total BOM Cost:** ~$1,644 USD  
**Core Build (Required):** $1,129  
**Optional/Upgrades:** $445  
**Particle Fabrication:** $70  
**High-Speed Camera Rental:** $500 (1 week)

---

## Core Hardware ($1,129)

### Acoustic Subsystem ($42)

| Component | Part Number | Description | Qty | Unit $ | Total $ | Supplier |
|-----------|-------------|-------------|-----|--------|---------|----------|
| Ultrasonic TX | MA40S4S | Murata 40kHz transmitter | 4 | $3.00 | $12.00 | DigiKey |
| Ultrasonic RX | MA40S4R | Murata 40kHz receiver | 4 | $3.00 | $12.00 | DigiKey |
| Piezo driver | DRV8662 | Piezo haptic driver | 4 | $4.50 | $18.00 | TI/Adafruit |
| Alt: Transducers | TCT40-16T/R | Budget 40kHz pair | 4 | $3.00 | $12.00 | AliExpress |

**Recommendation:** Use MA40S4S/R for Q-factor > 50. Budget option TCT40-16 acceptable for proof-of-concept.

### Mechanical Frame ($53)

| Component | Spec | Description | Qty | Unit $ | Total $ | Supplier |
|-----------|------|-------------|-----|--------|---------|----------|
| Extrusion 2020 | 600mm | Aluminum 20×20mm | 4 | $5.00 | $20.00 | McMaster/MakerStore |
| Corner brackets | 2020 compatible | L-brackets with bolts | 16 | $0.50 | $8.00 | Amazon |
| Transducer mounts | PETG printed | Custom design (STL) | 4 | $1.25 | $5.00 | Local |
| Rubber isolation | 10×10mm | Adhesive vibration pads | 20 | $0.25 | $5.00 | Amazon |
| Acrylic base | 400×400×3mm | Base platform | 1 | $15.00 | $15.00 | Local |

### Processing & Control ($359)

| Component | Part Number | Description | Qty | Unit $ | Total $ | Supplier |
|-----------|-------------|-------------|-----|--------|---------|----------|
| FPGA Dev Board | Nexys A7-100T | Xilinx Artix-7 | 1 | $265.00 | $265.00 | Digilent |
| *Alt: FPGA | CMOD A7-35T | Smaller Artix-7 | 1 | $89.00 | $89.00 | Digilent |
| Level shifters | TXS0108E | 8-bit bidirectional | 4 | $2.00 | $8.00 | DigiKey |
| Logic analyzer | Saleae clone | 24MHz 8-channel | 1 | $15.00 | $15.00 | Amazon |
| USB cables | USB-A to micro | For programming | 3 | $3.00 | $9.00 | Amazon |
| Breadboards | 830-tie | Half-size | 4 | $5.00 | $20.00 | Amazon |
| Jumpers | Mixed pack | M-M, M-F, F-F | 3 | $5.00 | $15.00 | Amazon |

**Nexys A7-100T Pinout Highlights:**
- 100MHz onboard oscillator → timing precision
- USB-UART → data readout
- PMOD headers → emitter drive signals
- HDMI/XADC → reserved for camera sync

### Power Distribution ($48)

| Component | Part Number | Description | Qty | Unit $ | Total $ | Supplier |
|-----------|-------------|-------------|-----|--------|---------|----------|
| 12V DC supply | LRS-100-12 | Meanwell 100W | 1 | $25.00 | $25.00 | DigiKey |
| Buck converter | LM2596 module | Adj 3.3V/5V output | 4 | $2.00 | $8.00 | Amazon |
| Barrel jack | 5.5×2.1mm | Panel mount | 2 | $1.50 | $3.00 | Amazon |
| Power switch | Rocker | SPST panel mount | 1 | $2.00 | $2.00 | Amazon |
| Fuse holder | 5×20mm | Panel mount | 1 | $3.00 | $3.00 | Amazon |
| Fuses | 5A fast-blow | 5×20mm | 5 | $1.60 | $8.00 | Amazon |

### Passive Components - Base Kit ($15)

| Component | Value | Package | Qty | Notes |
|-----------|-------|---------|-----|-------|
| Resistor 1% | 100Ω | 0603 | 20 | Termination matching |
| Resistor 1% | 1kΩ | 0603 | 20 | Pull-ups |
| Resistor 1% | 10kΩ | 0603 | 20 | Biasing |
| Capacitor C0G | 10nF | 0603 | 20 | Decoupling |
| Capacitor X7R | 100nF | 0603 | 20 | Bypass |
| Capacitor X7R | 10μF | 0805 | 10 | Bulk decoupling |
| Capacitor electrolytic | 100μF | SMD | 5 | Power rail filtering |

### Cabling ($27)

| Component | Spec | Description | Qty | Unit $ | Total $ |
|-----------|------|-------------|-----|--------|---------|
| Coaxial cable | RG174 | 50Ω, 10m spool | 1 | $12.00 | $12.00 |
| Coax connectors | SMA | Male/female kit | 10 | $1.00 | $10.00 |
| Wire strippers | - | For coax preparation | 1 | $5.00 | $5.00 |

### High-Speed Optical Reference ($545)

**Rental Recommended for Cycle 4**

| Component | Model | Description | Qty | Unit $ | Total $ | Source |
|-----------|-------|-------------|-----|--------|---------|--------|
| High-speed camera | Phantom VEO 640S | 1280×800 @ 1000fps | 1 | $500.00 | $500.00 | Vision Research |
| Macro lens | 50mm f/2.8 | Canon EF mount | 1 | inc. | inc. | Rental |
| LED ring light | 49mm adjustable | Continuous illumination | 1 | $25.00 | $25.00 | Amazon |
| Calibration target | 0.1mm grid | Checkerboard 100×100mm | 1 | $20.00 | $20.00 | ThorLabs |

**Purchase Alternative:** Chronos 2.1-HD at ~$3,000 (if repeated testing needed)

---

## Optional / Upgrade Components ($445)

These enhance capabilities but are not required for Cycle 4 validation.

| Component | Description | Qty | Unit $ | Total $ |
|-----------|-------------|-----|--------|---------|
| PMOD OLED | SSD1331 96×64 display | 1 | $20.00 | $20.00 |
| Temp/humidity sensor | SHT30-DIS | Environmental compensation | 2 | $5.00 | $10.00 |
| Oscilloscope | Rigol DS1104Z | 100MHz 4-channel | 1 | $400.00 | $400.00 |
| Function generator | FY6900 | 40kHz signal source | 1 | $100.00 | $100.00 |
| Spectrum analyzer | TinySA | 960MHz hand-held | 1 | $60.00 | $60.00 |
| Thermal camera | FLIR ONE Pro | Emitter characterization | 1 | $450.00 | $450.00 |

---

## Particle Fabrication ($70)

**Base Materials**

| Component | Description | Qty | Unit $ | Total $ | Supplier |
|-----------|-------------|-----|--------|---------|----------|
| Glass microspheres | 0.5mm ± 0.05, borosilicate | 100 | $0.15 | $15.00 | Cospheric |
| Barium titanate | Piezoelectric powder | 50g | $25.00 | $25.00 | Sigma |
| Silver epoxy | Conductive coating | 25g | $30.00 | $30.00 | MG Chemicals |

**Particle Target Specifications**
- Diameter: 0.5mm (acceptable: 0.4-0.6mm)
- Resonant frequency: 40kHz ± 2kHz
- Q-factor: > 30
- Coating uniformity: <10% thickness variation

---

## Procurement Checklist

### Order Priority 1 (Start Build)
- [ ] FPGA board (Nexys A7-100T or CMOD A7-35T)
- [ ] Aluminum extrusion + corner brackets
- [ ] Ultrasonic transducers (4 pairs)
- [ ] Basic passive components kit
- [ ] Power supply + buck converters

### Order Priority 2 (Complete Assembly)
- [ ] Coaxial cable + connectors
- [ ] Level shifters + logic analyzer
- [ ] Breadboards + jumper wires
- [ ] OLED display (optional)

### Order Priority 3 (Validation)
- [ ] Schedule high-speed camera rental
- [ ] Calibration target
- [ ] LED ring light
- [ ] Glass microspheres + coating materials

---

## Assembly Cost Breakdown

| Phase | Components | Cost | Prerequisite |
|-------|------------|------|--------------|
| Frame | Extrusion, brackets, acrylic | $53 | - |
| Emitters | Transducers, drivers, cables | $69 | Frame complete |
| Electronics | FPGA, level shifters, passives | $401 | - |
| Power | Supply, converters, wiring | $48 | Electronics ready |
| Optical | Camera rental, calibration | $545 | Tracking functional |
| Particles | Glass spheres, coating | $70 | Array complete |
| **Total** | | **$1,186** | |

---

## Supplier Links

### Primary Sources
- **DigiKey:** digikey.com - Fast shipping, full selection
- **Mouser:** mouser.com - Alternative to DigiKey
- **Adafruit:** adafruit.com - Breakout boards, drivers
- **SparkFun:** sparkfun.com - Dev boards, components
- **McMaster-Carr:** mcmaster.com - Mechanical hardware

### Budget Sources
- **AliExpress:** aliexpress.com - Transducers (2-4 week ship)
- **Amazon:** amazon.com - Passives, cables, tools
- **MakerStore:** makerstore.cc - Aluminum extrusion

### Specialty
- **Digilent:** digilent.com - Nexys FPGA boards
- **Vision Research:** phantomhighspeed.com - Camera rental
- **Cospheric:** cospheric.com - Precision microspheres
- **Sigma-Aldrich:** sigmaaldrich.com - Chemicals

---

## Notes

1. **FPGA Board:** Nexys A7-100T is full-featured. CMOD A7-35T is portable/cheaper but requires external USB-UART.

2. **Camera Rental:** Book 2 weeks in advance. Typical rates: $500-800/week for Phantom VEO class.

3. **Transducers:** MA40S4S/R are quality parts. TCT40-16 are acceptable for initial work but may have ±5kHz spread.

4. **Coax Cable:** RG174 is thin and flexible. RG58 lower loss but stiffer. Match lengths to <5mm for timing.

5. **Particle Fabrication:** If coating fails, consider purchasing pre-coated particles (~$2/each) for validation while iterating process.

---

*Document auto-generated from Cycle 4 specifications*  
*For updates, edit cycles/CYCLE_04_HARDWARE_BUILD.md*