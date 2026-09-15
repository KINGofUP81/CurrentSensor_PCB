# Isolated Hall-Effect Current Sensor Boards

Galvanically isolated **current-sensing boards** for power-electronics converters, built around the **LEM LA 55-P** closed-loop Hall-effect transducer with **TL084** op-amp signal conditioning. The design comes in three variants, **1, 3 and 5 channels**, so one board can monitor every leg of a single-phase, three-phase or multi-leg converter and give a microcontroller ADC a clean, scaled signal.

---

## Variants

| Variant | Channels | Typical use | PCB file | Print |
|---|---|---|---|---|
| **Single-leg** | 1 | Single converter leg / DC link | `PCB1.PcbDoc` | [PDF](CurrentSensorsingleleg_PCB.pdf) |
| **Three-leg** | 3 | Three-phase inverters and motor drives | `PCB2.PcbDoc` | [PDF](CurrentSensorthreeleg_PCB.pdf) |
| **Five-leg** | 5 | Five-phase machines and multi-leg converters | `PCB3.PcbDoc` | [PDF](CurrentSensorfiveleg_PCB.pdf) |

All three variants use the same proven channel circuit. The multi-channel boards share the ±15 V and +5 V supply inputs across all channels.

## Highlights

- **Galvanic isolation** between the power conductor and the measurement electronics
- **Closed-loop (compensated) Hall sensing:** DC to high-frequency response, low offset drift and good linearity
- **50 A nominal** per channel (LA 55-P)
- **Primary conductor passes straight through** the transducer aperture, so the power path isn't broken
- **Adjustable offset** on each channel via trimmer potentiometer
- **Diode-protected output** for the ADC
- **Through-hole design** that is easy to assemble and rework
- **2-layer PCB** with a ground plane

## How It Works

```
 Primary conductor ──► LA 55-P ──I_s = I_p / 1000──► measuring resistor ──► TL084 input stage
  (through aperture)                                                   │
                                        trimmer offset (+5 V ref) ──► TL084 gain & level-shift stages
                                                                       │
                                                               diode clamp ──► ADC output header
```

1. **Transduction:** the LA 55-P is a closed-loop Hall sensor. It drives a secondary current that cancels the primary flux, at a **1 : 1000** turns ratio, so a 50 A primary current gives a 50 mA secondary current.
2. **Measuring resistor:** the secondary current flows through a measuring resistor, turning it into a voltage.
3. **Signal conditioning:** the TL084 stages scale and level-shift that voltage. A trimmer referenced to +5 V sets the offset, so a bipolar (±) current maps to a unipolar voltage centred in the ADC range.
4. **Output protection:** a diode on the output stage keeps the signal from driving the ADC pin outside its safe range.

## Key Components

| Part | Function | Per channel |
|---|---|---|
| **LEM LA 55-P** | Closed-loop Hall-effect current transducer, 50 A nominal, 1:1000 | 1 |
| **TI TL084CN** | Quad JFET-input op-amp for buffering, gain and level shifting | 1 |
| Bourns **3296W** trimmer | Offset / zero adjustment | 1 |
| Resistor network | 47 Ω, 3.48 k, 0.97 k, 8.97 k, 7.9 k (×2), 9.94 k | 7 |
| 1N4007 | Output protection diode | 1 |
| Phoenix / Molex headers | ±15 V, +5 V supply and signal output connectors | per board |

## Power Supply

| Input | Connector | Used by |
|---|---|---|
| **+15 V / −15 V / GND** | 3-pin jack | LA 55-P transducer and TL084 op-amp |
| **+5 V / GND** | 2-pin jack | Offset reference |

## Design Notes

<details>
<summary><b>Why a closed-loop Hall transducer?</b></summary>

Shunt resistors are cheap but not isolated, and they dissipate power in the main current path. Open-loop Hall sensors are isolated but drift and saturate. A closed-loop transducer like the LA 55-P gives galvanic isolation, wide bandwidth, excellent linearity and low temperature drift. That matters for inverter and motor-drive control loops, where current feedback quality directly sets control performance.
</details>

<details>
<summary><b>Scaling for the ADC</b></summary>

At the 50 A nominal rating, the LA 55-P outputs a 50 mA secondary current (1 : 1000). The measuring resistor turns this into a voltage, and the op-amp stages then apply the gain and the trimmer-set offset. Check the final output swing against your MCU's ADC reference, and adjust the gain resistors if you use a different range.
</details>

<details>
<summary><b>PCB layout</b></summary>

- **Mechanical:** each transducer has 12.2 mm `+DC` / `−DC` primary-conductor pads for the power connection.
- **Noise:** signal conditioning is placed right next to the transducer, which keeps the low-level analog path short.
- **Ground:** a ground plane gives a low-impedance return and shields the analog signals.
- **Multi-channel boards:** channels are laid out in a repeated row with a shared supply bus, so they perform consistently.
</details>

## Repository Contents

```
CurrentSensor_PCB/
├── CurrentSensor_PCB.PrjPcb            # Altium project (open this)
├── Sheet1.SchDoc                       # Single-leg schematic
├── Sheet2.SchDoc                       # Three-leg schematic
├── Sheet3.SchDoc                       # Five-leg schematic
├── PCB1.PcbDoc / PCB2.PcbDoc / PCB3.PcbDoc  # Layouts: 1 / 3 / 5 channels
├── Schlib1.SchLib, PcbLib1.PcbLib      # Project libraries
├── LA55-P/, TL084CNE4/, ...            # Vendor symbols & footprints
├── CurrentSensor*leg_PCB.pdf           # Schematic + layout prints per variant
├── Project Outputs for CurrentSensor_PCB/   # Gerbers, drill files and DRC reports
└── AMI_CURRENT/                        # Earlier KiCad prototype Gerbers (2-layer, 97.9 × 204 mm)
```

## Getting Started

1. Open `CurrentSensor_PCB.PrjPcb` in **Altium Designer**, or just open the PDFs to see each design without Altium.
2. Pick the variant you need (`PCB1`, `PCB2` or `PCB3`); Gerbers for all three are in `Project Outputs for CurrentSensor_PCB/`.
3. After assembly:
   - Apply ±15 V and +5 V.
   - With **zero primary current**, adjust the trimmer to set the output to your desired zero point (e.g. mid-scale of the ADC).
   - Pass a known DC current through the transducer and verify the scale factor.

> ⚠️ The primary side may carry hazardous voltages and currents. Keep the LA 55-P's rated isolation distances and never adjust a board while the primary conductor is live.

## Applications

- Phase current feedback for **FOC motor drives**
- Output current sensing for **single- and three-phase inverters**
- DC-link current monitoring in **DC-DC converters**
- Multi-phase machine research (five-phase drives)
