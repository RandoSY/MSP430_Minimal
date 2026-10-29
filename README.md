# MSP430G2553 20-pin DIP laboratory development board — v1.0

A simple **2-layer, 109 × 71 mm**, mostly through-hole KiCad project inspired by the MSP430 Minimal breakout (not a copy). Designed for **MSP430G2553IN20** in a 20-pin DIP socket. All 20 package pins are present on labeled breakout connectors.

## Files

- `MSP430G2553_LabBoard.kicad_pro`, `.kicad_sch`, `.kicad_pcb`: open the project, schematic, and routed PCB in KiCad 9 (or compatible).
- `UMA_MSP430.kicad_sym`, `UMA_MSP430.pretty/`, `sym-lib-table`, `fp-lib-table`: **project-local custom symbols/footprints**; keep these beside the KiCad files.
- `PCB_preview.png`: current copper-layer overview (red: front; blue: back).
- `PCB_back_silkscreen_preview.png`: mirrored back-side silkscreen and pad-opening preview.
- `ROUTING_WORKFLOW.md`: Freerouting version, settings, and repeatable DSN/SES workflow.
- `PCB_DRC.json`: latest KiCad PCB DRC report.
- `BOM.csv`: Bill of Materials.
- `NETLIST.csv`: complete electrical cross-check net/pin list.
- `ROUTING.json` and `GEOMETRY_CHECK.json`: historical routing and approximate copper-check data from the earlier layout; they do not describe the current PCB.

## Connectors

| Header | Pin 1 | Pin 2 | Pin 3 | Pin 4 |
|---|---|---|---|---|
| J3 — 3.3V UART | +3V3 | GND | MCU TX (P1.2) | MCU RX (P1.1) |
| J4 — Spy-Bi-Wire | +3V3 | GND | RST/SBWTDIO | TEST/SBWTCK |
| J5 — I²C | +3V3 | GND | SCL (P1.6) | SDA (P1.7) |

**J1** pins 1–10 = MCU physical DIP pins 1–10 (in order); **J2** pins 1–10 = DIP pins 20–11 (top to bottom). J1/J2 expose all I/O, supply and debug pins, including multiplexed P2.6/P2.7. See `NETLIST.csv` for exact labels.

## Features & wiring

- **U1:** 20-pin MSP430G2553IN20 (3.3 V regulated supply; no on-board regulator).
- **Power:** C2 100 nF ceramic and C3 10 µF bulk capacitor across supply; C3 pin 1 is positive (+3V3), pin 2 is ground; check actual body size and polarity against footprint.
- **SBW:** R1 47 kΩ pull-up to RST, C1 1 nF to ground and reset switch SW3. Do not increase SBW capacitance without verifying debugger compatibility.
- **I²C:** R2/R3 each 4.7 kΩ to +3V3 on SCL/SDA. These are fixed pull-ups: omit resistors if the external bus already has sufficient pull-up strength.
- **LEDs:** D1 green on P1.0 and D2 amber on P1.4; each fed by 1 kΩ current-limiting resistor; firmware drives the GPIO high to illuminate.
- **User switches:** SW1 -> P2.0/GND, SW2 -> P2.1/GND. Enable MCU internal input pull-ups in firmware. SW3 resets MCU.
- **Piezo:** BZ1 12 mm passive piezo footprint, two pins 7.62 mm apart, powered through NPN Q1 (2N3904 E/B/C). P2.3 drives Q1 base through R6=1k; R7=100k holds transistor off; firmware PWM/TIMER output allows tones. Confirm piezo terminal pitch and TO-92 E/B/C lead ordering for your actual parts.
- **Slide control:** RV1 10 kΩ Bourns **PTA3043-2010CPB103** linear slide potentiometer, 30 mm stroke, 45 mm body. End terminals to supply/ground, wiper on P1.3/A3. C4 10 nF filters ADC input. The project includes four proposed 1.8-mm clearance holes for mounting lugs; **test the physical hole pattern against the selected manufacturer's mechanical drawing before fabrication**.
- MCU uses its internal oscillator; P2.6/P2.7 are available as GPIO rather than populated with a 32-kHz crystal.

## Power and safety

**Connect only regulated 3.3 V** to J3 or J4. Neither the chip nor the UART header is 5 V tolerant. This is a **TTL-level UART header, not a DB9/±12 V RS-232 port**. With multiple programmers/adapters, power from only one source or isolate VCC appropriately. No USB, voltage regulator, or reverse-polarity protection is provided.

## Before you order from DirtyPCBs

1. Open the `.kicad_pro` project in KiCad and confirm the schematic parses and all custom symbol libraries resolve.
2. Run **Tools → Electrical Rules Checker (ERC)** in Schematic Editor. The symbol/footprint libraries were written programmatically, not native KiCad-validated.
3. In PCB Editor, refill any copper zones (none needed here); run **Inspect → Design Rules Checker (DRC)** with your manufacturer's rules; fix any flags. The latest KiCad DRC reported zero violations and zero unconnected items under the current project rules. Confirm the rules match your manufacturer, and rerun the check after edits.
4. Verify the 6×6 mm tactile switch footprint, transistor TO-92 orientation, 12 mm piezo pin spacing, 10 µF capacitor clearance, and especially **Bourns slide pot** lug hole placement against parts in hand.
5. From PCB Editor choose **File → Fabrication Outputs → Gerbers** and **Drill Files**, generate standard F.Cu/B.Cu/F.Mask/B.Mask/F.SilkS/Edge.Cuts and Excellon drills, then use KiCad Gerber Viewer to inspect.
6. Zip the Gerber/Excellon production outputs (not the source project) and submit the ZIP to https://dirtypcbs.com/store/pcbs. Specify **2 copper layers**, **1.6-mm FR4**, and actual **109×71 mm** board dimensions; verify current service limits and capability first.

**Status: routed prototype draft; run schematic ERC, verify footprint fit, and export and inspect fabrication Gerbers before ordering.** Do not submit this source ZIP as a fabrication-ready Gerber archive.

## Authoritative references

- TI MSP430G2x53 datasheet: https://www.ti.com/lit/ds/symlink/msp430g2553.pdf
- TI MSP430 Hardware Tools, SBW guidance: https://www.ti.com/lit/ug/slau278/slau278.pdf
- Bourns PTA slide potentiometer mechanical drawing: https://www.bourns.com/docs/product-datasheets/pta.pdf
- Inspiration: https://hackaday.io/project/3173-msp430-minimal
