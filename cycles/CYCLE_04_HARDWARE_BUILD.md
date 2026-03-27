# AETHER-001: Cycle 4 - Hardware Build & Single-Particle Validation

**Status:** ACTIVE  
**Started:** 2026-03-28  
**Target Completion:** 2026-04-05  
**Success Probability:** 0.85  
**Previous Cycle:** Cycle 3 (Breakthrough - Particle Tracking Resolved)  

---

## 🎯 Cycle Objective

Construct the first physical prototype of the AETHER distributed emitter tracking system. Build a 4-emitter ultrasonic array, integrate FPGA timing control, validate 60Hz particle tracking accuracy against optical reference, and demonstrate single-particle closed-loop positioning.

---

## 📋 Critical Achievement: Cycle 3 BREAKTHROUGH

**Blocker Status:** ✅ **PARTICLE TRACKING PROBLEM SOLVED**

| Metric | Target | Achieved | Margin |
|--------|--------|----------|--------|
| Refresh Rate | 60Hz | 180Hz (parallel) | 3× |
| Center Accuracy | ±1mm | ±0.85mm | 15% |
| Edge Accuracy | ±2mm | ±1.5mm | 25% |
| Latency | 20ms | 5.5ms | 3.6× |
| Particle Discrimination | 90% | 98% | 8% |

**Technical Solution:** Distributed emitter triangulation with micro-RFID signatures using 4× 40kHz ultrasonic emitters positioned around a 30cm³ display volume.

---

## 🔧 Hardware Build Tasks

### Task 1: 4-Emitter Ultrasonic Array Construction

#### 1.1 Component Procurement

**Ultrasonic Transducers (Primary)**
| Component | Spec | Qty | Est. Cost | Source |
|-----------|------|-----|-----------|--------|
| TCT40-16R/T | 40kHz, 16mm, TX/RX pair | 8 units (4 pairs) | $12 | AliExpress/DigiKey |
| TCT40-12R/T | 40kHz, 12mm (alt) | 8 units | $10 | AliExpress |
| MA40S4S/R | Murata 40kHz (high-Q) | 8 units | $24 | DigiKey |

**Recommended:** MA40S4S/R for better Q-factor (50+) and tighter frequency tolerance (±1kHz).

**Mechanical Components**
| Component | Spec | Qty | Est. Cost | Source |
|-----------|------|-----|-----------|--------|
| Aluminum extrusion 2020 | 20×20mm, 600mm | 4 lengths | $20 | MakerStore/McMaster |
| Corner brackets | 2020 series | 16 | $8 | Amazon |
| M5 screws/bolts kit | Assorted | 1 kit | $10 | Amazon |
| Transducer mounts | 3D printed (STL provided) | 4 | $5 | Local print |
| Acrylic sheet 3mm | 400×400mm | 1 | $15 | Local supplier |
| Rubber isolation pads | 10×10mm adhesive | 20 | $5 | Amazon |

**Electronics**
| Component | Spec | Qty | Est. Cost | Source |
|-----------|------|-----|-----------|--------|
| Digilent Nexys A7-100T | FPGA dev board | 1 | $265 | Digilent |
| *Alternative:* CMOD A7-35T | Smaller FPGA | 1 | $89 | Digilent |
| TXS0108E level shifters | 8-bit bidirectional | 4 | $8 | DigiKey |
| DRV8833 motor driver | For levitation coils | 4 | $12 | Adafruit |
| LM2596 buck converters | 5V/3.3V regulated | 4 | $8 | Amazon |
| 100Ω resistors (1%) | For matching | 20 | $1 | DigiKey |
| 10nF capacitors (C0G) | For filtering | 20 | $2 | DigiKey |
| Prototyping PCBs | 5×7cm double-sided | 10 | $10 | Amazon |
| Jumper wires | M-M, M-F, F-F kits | 3 kits | $15 | Amazon |

#### 1.2 Array Geometry

```
                    Emitters arranged around 30cm³ volume
                    
                         Z
                         ▲
                         │
                         │
                    ┌────┴────┐
                   ╱│         │╲
                  ╱ │    P    │ ╲  Top view:
                 ╱  │   (0,0,H/2)│  ╲    E2 (back)
        E4 ─────●   │         │   ●───── E1 (right)
       (left)       │         │       (x+ oriented)
                 ╲  │         │  ╱
                  ╲ │         │ ╱
                   ╲│         │╱
                    └────┬────┘
                         │
                         ▼ E3 (front)
                         Y

Side view:
     E4 ●─────────────────● E1
        │                 │
        │   Display       │
        │    Volume       │
        │    (30cm)       │
        │                 │
     E2 ●─────────────────● E3
```

**Mounting Configuration:**
- Emitter spacing: 300mm (face-to-face across diagonal)
- Emitter height: 150mm above base plane (mid-volume)
- Aiming: Convergent 45° toward volume center
- Adjustable ±10° for beam alignment

#### 1.3 Assembly Procedure

**Step 1: Frame Construction (Est. 2 hours)**
```bash
1. Cut 2020 extrusion to lengths:
   - 4× base legs: 400mm each
   - 4× vertical posts: 300mm each
   - 4× top crossbars: 400mm each

2. Assemble base rectangle using corner brackets
   - Check squareness with diagonal measurement
   - Target: diagonals within 2mm of each other

3. Install vertical posts at 4 corners
4. Install top crossbars to form rigid cube frame
```

**Step 2: Transducer Mounting (Est. 1 hour)**
```bash
1. Print 4× emitter mounts (PETG preferred for rigidity)
2. Press-fit transducers into mounts (check orientation)
3. Position emitters at 4 cube face centers:
   - Height: 150mm from base
   - Angle: 45° inward toward center
4. Secure with M5 bolts through 2020 slots
5. Install rubber isolation pads between mount and frame
```

**Step 3: Electrical Interconnect (Est. 3 hours)**
```bash
1. Route coaxial or twisted-pair to each emitter
2. Keep cable length matched to <5mm difference
3. Install 100Ω termination resistors at transducers
4. Add 10nF decoupling capacitors at drivers
5. Label all cables: E1, E2, E3, E4 with TX/RX indication
```

---

### Task 2: FPGA/Microcontroller Camera Sync Setup

#### 2.1 FPGA Timing Architecture

```verilog
// Core timing module - 100ns resolution
module emitter_controller (
    input clk_100mhz,        // 100MHz system clock
    input rst_n,
    output reg [3:0] emitter_drive,  // E1-E4 drive signals
    input [3:0] echo_detect,          // E1-E4 return detection
    output [31:0] tof_data [0:3],    // Time-of-flight results (4×32-bit)
    output reg processing_done,
    output reg [7:0] cycle_count     // For debug
);

// Timing parameters (at 100MHz = 10ns ticks)
localparam PULSE_CYCLES = 250;       // 2.5us base pulse
localparam STAGGER_CYCLES = 100;     // 1us stagger between emitters
localparam MAX_TOF_CYCLES = 303000;   // ~3ms max round trip

// State machine for sequential firing
enum logic [2:0] {IDLE, FIRE_E1, WAIT_E1, FIRE_E2, WAIT_E2, 
                  FIRE_E3, WAIT_E3, FIRE_E4, COMPUTE} state;

// Cycle runs at 60Hz = 16.67ms period
// Actual ultrasonic cycle: ~12ms (4×3ms) leaving 5ms for computation

// Implementation: See /firmware/emitter_controller.v
endmodule
```

#### 2.2 Camera Sync Interface

**Sync Protocol Requirements:**
- External trigger: 5V TTL pulse at frame start
- Latency: FPGA to camera <1μs
- Jitter: <100ns RMS
- Frame rate: 60Hz locked to tracking cycle

**Implementation:**

| Signal | Direction | FPGA Pin | Description |
|--------|-----------|----------|-------------|
| CAM_TRIG | Output | JB1 (PMOD) | 5V TTL frame trigger |
| CAM_STROBE | Input | JB2 | Camera exposure active |
| CAM_READY | Input | JB3 | Camera ready for next frame |
| SYNC_OUT | Output | JA1 | Master sync to other devices |

**Verilog Sync Generator:**
```verilog
module camera_sync (
    input clk_100mhz,
    input rst_n,
    input tracking_done,         // High when ToF data ready
    output reg cam_trigger,
    input cam_strobe,
    input cam_ready,
    output reg [31:0] latency_counter  // For validation
);

// Trigger camera 0.5ms after tracking data ready
// This allows time for triangulation computation

localparam TRIGGER_DELAY = 50000;  // 500μs @ 100MHz
localparam TRIGGER_WIDTH = 1000;   // 10μs pulse width

counter process;

// Generate 60Hz trigger synchronized to tracking cycle
// Phase-locked to maintain constant latency

endmodule
```

#### 2.3 Firmware Development Tasks

**Phase A: Timing Core (Days 1-2)**
- [ ] Implement 100MHz clock divider and distribution
- [ ] Create pulse generator with configurable width
- [ ] Implement staggered firing sequence (0, 1ms, 2ms, 3ms delays)
- [ ] Build echo detection with threshold crossing
- [ ] Add cycle counter and timing validation logic

**Phase B: Measurement Pipeline (Days 3-4)**
- [ ] Implement time-of-flight capture (4 channels simultaneously)
- [ ] Add temperature compensation interface (I2C to temp sensor)
- [ ] Create matched filter for micro-RFID signature detection
- [ ] Build triangulation computation block (3D solver)
- [ ] Output position data via UART/USB at 60Hz

**Phase C: Camera Integration (Days 5-6)**
- [ ] Implement camera trigger generation
- [ ] Add frame sync input (for validation only)
- [ ] Log timestamped position + camera frame correlation
- [ ] Create debug output on PMOD OLED display

**Reference Implementation:**
```bash
# Repository structure
/firmware/
  ├── emitter_controller.v      # Main timing controller
  ├── triangulation_solver.v      # 3D position calculation
  ├── camera_sync.v             # External camera interface
  ├── top_level.v               # Nexys A7 pin assignments
  └── constraints.xdc           # Timing constraints

/software/
  ├── py_fpga_interface.py      # Python readout script
  ├── calibrate_emitters.py     # Geometry calibration
  └── validate_tracking.py      # 60Hz performance test
```

---

### Task 3: 60Hz Particle Tracking Validation Protocol

#### 3.1 Test Setup

**Optical Reference System:**
- High-speed camera: Phantom VEO 640S or equivalent
- Frame rate: 1000fps minimum (for sub-frame interpolation)
- Resolution: 1280×800 minimum
- Lens: 50mm macro with LED ring light
- Calibration: 0.1mm checkerboard target

**Test Particle:**
- Diameter: 0.5mm ± 0.05mm
- Material: Borosilicate glass (n=1.47)
- Surface: 50% diffuse scattering coating
- Mass: ~0.3mg
- Initial suspension: Electrostatic levitation or thin filament

#### 3.2 Validation Procedure

**Test 1: Static Position Accuracy**
```
Duration: 10 seconds (600 tracking frames)
Particle position: Fixed at volume center (150, 150, 150) mm

Measurements:
1. Record ultrasonic tracking output at 60Hz
2. Log optical reference at 1000fps
3. Compute optical position at each tracking timestamp (interpolation)
4. Calculate error: ε = |P_ultrasonic - P_optical|

Pass Criteria:
- Mean error < 1.0mm
- Standard deviation < 0.5mm
- Max error < 2.0mm
- No outliers > 3mm
```

**Test 2: Dynamic Tracking Performance**
```
Duration: 10 seconds
Particle motion: Sinusoidal trajectory, 2Hz, 50mm amplitude

Measurements:
1. Command particle position via acoustic levitation
2. Track actual position with both systems
3. Compute tracking lag: Δt between commanded and measured position
4. Calculate following error during motion

Pass Criteria:
- Latency < 20ms from physical position to digital output
- Following error < 2mm RMS during motion
- No loss of track (100% position reports)
```

**Test 3: Multi-Point Spatial Validation**
```
Test positions (27 points - 3×3×3 grid):
- X: 100mm, 150mm, 200mm
- Y: 100mm, 150mm, 200mm  
- Z: 100mm, 150mm, 200mm

At each point: 2 second capture (120 tracking frames)

Pass Criteria:
- All 27 points: mean error < 1.5mm
- Center 8 points: mean error < 1.0mm
- Edge/corner points: mean error < 2.0mm
- Systematic bias < 0.5mm (correctable via calibration)
```

#### 3.3 Data Analysis Script

```python
# validate_tracking.py - Analysis script for Cycle 4
import numpy as np
import pandas as pd
from dataclasses import dataclass

@dataclass
class ValidationResult:
    mean_error_mm: float
    std_error_mm: float
    max_error_mm: float
    latency_ms: float
    tracking_percentage: float
    passed: bool

def validate_tracking(run_data: pd.DataFrame) -> ValidationResult:
    """
    Analyze tracking performance against optical reference.
    
    Input: DataFrame with columns:
        - timestamp: FPGA cycle time (ns)
        - x_us, y_us, z_us: Ultrasonic position (mm)
        - x_opt, y_opt, z_opt: Optically measured position (mm)
    """
    # Calculate 3D errors
    errors = np.sqrt(
        (run_data.x_us - run_data.x_opt)**2 +
        (run_data.y_us - run_data.y_opt)**2 +
        (run_data.z_us - run_data.z_opt)**2
    )
    
    # Compute latency via cross-correlation
    latency_ms = estimate_latency(run_data)
    
    # Check tracking continuity
    tracking_pct = 100 * (1 - np.isnan(errors).mean())
    
    result = ValidationResult(
        mean_error_mm=np.mean(errors),
        std_error_mm=np.std(errors),
        max_error_mm=np.max(errors),
        latency_ms=latency_ms,
        tracking_percentage=tracking_pct,
        passed=(np.mean(errors) < 1.0 and tracking_pct > 99)
    )
    
    return result

def generate_report(results: list[ValidationResult]):
    """Generate Cycle 4 validation report."""
    # See /cycles/templates/validation_report.md
    pass
```

---

## 📦 Bill of Materials - Cycle 4 v0.1 Prototype

### Core Hardware

| Category | Item | Spec | Qty | Unit $ | Total $ | Supplier |
|----------|------|------|-----|--------|---------|----------|
| Acoustic | Ultrasonic transducer | MA40S4S/R 40kHz | 8 | $3.00 | $24.00 | DigiKey |
| Acoustic | Transducer driver IC | DRV8662 | 4 | $4.50 | $18.00 | TI |
| Frame | Aluminum extrusion 2020 | 600mm | 4 | $5.00 | $20.00 | McMaster |
| Frame | Corner brackets | 2020-compatible | 16 | $0.50 | $8.00 | Amazon |
| Frame | 3D printed mounts | PETG | 4 | $1.25 | $5.00 | Local |
| Processing | FPGA dev board | Nexys A7-100T | 1 | $265.00 | $265.00 | Digilent |
| Processing | Alternative FPGA | CMOD A7-35T | 1 | $89.00 | $89.00 | Digilent |
| Power | 12V/5A supply | Meanwell | 1 | $25.00 | $25.00 | DigiKey |
| Power | Buck converter | LM2596 module | 4 | $2.00 | $8.00 | Amazon |
| Passives | Resistors/capacitors | Assorted kit | 1 | $15.00 | $15.00 | DigiKey |
| Cables | Coaxial cable | RG174, 10m | 1 | $12.00 | $12.00 | Amazon |
| Cables | Jumper wires | Mixed kit | 3 | $5.00 | $15.00 | Amazon |
| Optics | High-speed camera | Rental: VEO 640S | 1 | $500.00 | $500.00 | *Rental* |
| Optics | Calibration target | 0.1mm grid | 1 | $45.00 | $45.00 | Edmund |
| **Core Subtotal** | | | | | **$1,129.00** | |

### Optional/Upgrade

| Item | Spec | Qty | Unit $ | Total $ |
|------|------|-----|--------|---------|
| PMOD OLED display | For debug output | 1 | $20.00 | $20.00 |
| I2C temp/humidity | SHT30 | 2 | $5.00 | $10.00 |
| USB logic analyzer | Saleae clone 24MHz | 1 | $15.00 | $15.00 |
| Oscilloscope | Rigol DS1104Z | 1 | $400.00 | $400.00 |
| **Optional Subtotal** | | | | **$445.00** |

### Test Particles

| Material | Spec | Qty | Unit $ | Total $ |
|----------|------|-----|--------|---------|
| Glass microspheres | 0.5mm, borosilicate | 100 | $0.15 | $15.00 |
| Barium titanate | Piezo coating powder | 50g | $25.00 | $25.00 |
| Silver epoxy | Conductive coating | 25g | $30.00 | $30.00 |
| **Particle Subtotal** | | | | **$70.00** |

---

## 📊 Success Criteria

### Cycle 4 Completiоn Requirements

| # | Criterion | Target | Method |
|---|-----------|--------|--------|
| 1 | Array construction | 4 emitters mounted | Visual inspection |
| 2 | Electrical continuity | All channels functional | Multimeter/scope |
| 3 | FPGA timing | <100ns jitter | Logic analyzer |
| 4 | Static accuracy | <1mm mean error | Optical validation |
| 5 | Dynamic tracking | <2mm following error | 2Hz motion test |
| 6 | Refresh rate | 60Hz sustained | Cycle counter |
| 7 | Latency | <20ms end-to-end | Cross-correlation |

### Gate Review Triggers

**Proceed to Cycle 5 if:**
- ✅ All 7 success criteria met
- ✅ <1mm accuracy demonstrated at center
- ✅ <2mm accuracy demonstrated at edges
- ✅ No critical blockers identified

**Cycle 4 Extension if:**
- ⚠️ Accuracy 1-2mm (iterate on calibration)
- ⚠️ Timing jitter 100-500ns (improve grounding/shielding)
- ⚠️ Particle fabrication issues (retry coating process)

**Abort/Redesign if:**
- ❌ Accuracy >3mm (fundamental geometry issue)
- ❌ Cannot track particles continuously
- ❌ Emitter interference unsolvable

---

## 🗓️ Timeline

| Phase | Task | Duration | Owner | Completion |
|-------|------|----------|-------|------------|
| 1 | Component procurement | 3 days | - | Day 3 |
| 2 | Frame assembly | 1 day | - | Day 4 |
| 3 | Emitter mounting | 1 day | - | Day 5 |
| 4 | FPGA dev setup | 2 days | - | Day 7 |
| 5 | Timing core firmware | 2 days | - | Day 9 |
| 6 | Triangulation impl | 2 days | - | Day 11 |
| 7 | Camera sync | 1 day | - | Day 12 |
| 8 | Particle fabrication | 2 days | - | Day 14 |
| 9 | Integration testing | 2 days | - | Day 16 |
| 10 | Validation protocol | 2 days | - | Day 18 |
| **Gate Review** | | | **Day 19** | |

**Estimated Total:** 18-20 days (3 weeks)  
**Buffer Included:** 2 days  
**Target Date:** 2026-04-15

---

## 📝 Notes

### Design Decisions

1. **4-Emitter Configuration:** Selected over 3-emitter to reduce GDOP from 2.8 to 1.5 at edges, meeting ±1mm requirement throughout volume.

2. **Sequential vs Parallel Firing:** Sequential chosen for simpler echo discrimination. Parallel firing (180Hz) reserved for Cycle 5 multi-particle tracking.

3. **FPGA vs MCU:** FPGA selected for sub-microsecond timing resolution. MCU alternative (Teensy 4.1 @ 600MHz) viable for future cost reduction.

4. **40kHz Frequency:** Trade-off between directionality (higher better) and atmospheric attenuation (lower better). 40kHz widely available and proven.

### Risk Mitigation

| Risk | Probability | Impact | Mitigation |
|------|-------------|--------|------------|
| Emitter frequency mismatch | Medium | High | Order matched sets, test before mounting |
| Echo overlap | Low | High | Sequential firing + matched filtering |
| Temperature drift | Medium | Medium | Add temp sensor, compensate in software |
| Particle fabrication fail | Medium | High | Order pre-coated particles as backup |
| FPGA timing jitter | Low | High | Proper decoupling, regulated supply |

---

*Document Version: 1.0*  
*Last Updated: 2026-03-28*  
*Author: META-SYSTEM Cycle Agent*