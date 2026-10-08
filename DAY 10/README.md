# DAY 10: Module 5 - PV_D5SK2: LVS Labs (Part 2) & Final Workshop Synthesis

## Overview
Day 10 covers **Module 5 (Part 2: PV_D5SK2 - Lectures L7 to L11)**. This final lab module covers advanced LVS verification scenarios: layout vs. CDL/Verilog for standard cells, macro-level LVS, complex mixed-signal Digital Phase-Locked Loop (PLL) verification, and debugging intricate device property errors.

---

## Table of Contents
1. [PV_D5SK2_L7: LVS Layout Vs Verilog For Standard Cell](#pv_d5sk2_l7-lvs-layout-vs-verilog-for-standard-cell)
2. [PV_D5SK2_L8: LVS For Macros](#pv_d5sk2_l8-lvs-for-macros)
3. [PV_D5SK2_L9: LVS Digital PLL - Part 1](#pv_d5sk2_l9-lvs-digital-pll---part-1)
4. [PV_D5SK2_L10: LVS Digital PLL - Part 2](#pv_d5sk2_l10-lvs-digital-pll---part-2)
5. [PV_D5SK2_L11: LVS With Property Errors](#pv_d5sk2_l11-lvs-with-property-errors)
6. [Tapeout Readiness Signoff Checklist](#tapeout-readiness-signoff-checklist)

---

## PV_D5SK2_L7: LVS Layout Vs Verilog For Standard Cell

### Lab Objective
Compare extracted physical standard cell layout against a gate-level structural Verilog netlist.

1. **Standard Cell Layout Extraction:**
   * Extract Magic standard cell layout (`sky130_fd_sc_hd__nand2_1.mag`) into SPICE netlist.
2. **Convert Structural Verilog to Netgen Netlist:**
   * Read structural Verilog (`sky130_fd_sc_hd.v`) into Netgen.
3. **Pin & Power Port Mapping:**
   * Ensure implicit power/ground pins (`VPWR`, `VGND`, `VPB`, `VNB`) are mapped correctly to Verilog module declarations.
4. **Run LVS:** Verify 1-to-1 match between physical standard cell layout and behavioral/gate Verilog model.

---

## PV_D5SK2_L8: LVS For Macros

### Lab Objective
Perform LVS verification on complex IP macros (SRAM blocks, ADC/DAC macros, Bandgap References).

1. **Hierarchical Subblock Extraction:**
   * Extract macro layout using `extract all` and `ext2spice lvs`.
2. **Netlist Verification:**
   * Compare macro SPICE schematic (`.cdl` or `.spice`) against macro extracted layout.
3. **Handling Unconnected Dummy Fill & Shielding:**
   * Ensure dummy guard rings, substrate taps, and metal shields do not trigger false net opens/shorts in Netgen.

---

## PV_D5SK2_L9: LVS Digital PLL - Part 1

### Lab Objective
Mixed-signal physical verification for a Digital Phase-Locked Loop (PLL) block - Setup & Top Assembly.

1. **Digital PLL Architecture:**
   * Phase Frequency Detector (PFD), Charge Pump (CP), Voltage Controlled Oscillator (VCO), and Frequency Divider.
2. **Top-Level Layout & Netlist Assembly:**
   * Extract top-level PLL layout DEF/GDS in Magic.
   * Assemble schematic SPICE netlist representing analog oscillator and digital logic blocks.
3. **Initial Netgen Run:** Scan overall pin and instance counts to identify top-level wiring discrepancies.

---

## PV_D5SK2_L10: LVS Digital PLL - Part 2

### Lab Objective
Debugging mixed-signal LVS errors, substrate noise isolation, and final PLL signoff.

1. **Debugging Subcircuit Mismatches:**
   * Resolve net shorts between analog ground (`AVSS`) and digital ground (`DVSS`).
   * Verify Deep N-Well (`dnwell`) body bias contacts for VCO block.
2. **Final Signoff Verification:**
   * Achieve clean Netgen matching verdict:
     ```text
     Result: Circuits match uniquely.
     ```

---

## PV_D5SK2_L11: LVS With Property Errors

### Lab Objective
Identify, debug, and resolve subtle device property ($W$, $L$, $R$, $C$) mismatches in Netgen.

1. **Simulating Property Violations:**
   * Intentional layout edit: Change PMOS width in layout from $3.0\,\mu\text{m}$ to $2.5\,\mu\text{m}$ while schematic retains $3.0\,\mu\text{m}$.
2. **Analyzing Netgen Error Log (`lvs_comp.out`):**
   ```text
   Property errors found:
   Instance M2 (Layout: pfet_01v8, Schematic: pfet_01v8)
     Layout width = 2.500um  |  Schematic width = 3.000um  (Mismatch!)
   ```
3. **Fixing & Tolerances:**
   * Adjust layout geometry to $3.0\,\mu\text{m}$ or tune tolerance parameters in `sky130A_setup.tcl`.

---

## Tapeout Readiness Signoff Checklist

Upon completing all 5 Modules across 10 Days, the design achieves **Tapeout Readiness**:

```
+-------------------------------------------------------------------------------+
|                       TAPEOUT READINESS SIGNOFF CHECKLIST                     |
+-------------------------------------------------------------------------------+
|  [✔] Clean Magic DRC (0 errors over full die area)                            |
|  [✔] Clean KLayout DRC (0 litho / density / antenna errors)                   |
|  [✔] Clean Netgen LVS ("Circuits match uniquely")                             |
|  [✔] Clean Antenna Ratio Verification (0 plasma gate oxide breakdown risks)   |
|  [✔] Layer Density Compliance (Metal1 - Metal5 within 30%-70% fill range)     |
|  [✔] Post-Layout Parasitic STA (Setup & Hold slack > 0ns in OpenSTA)          |
|  [✔] GDSII Stream-Out Verified (Off-grid & geometry overlap clean)            |
+-------------------------------------------------------------------------------+
```
