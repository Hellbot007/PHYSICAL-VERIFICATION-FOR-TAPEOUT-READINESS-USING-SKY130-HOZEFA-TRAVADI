# DAY 10: Module 5 - PV_D5SK2: LVS Labs (Part 2) & Final Workshop Synthesis

## Overview
Day 10 covers **Module 5 (Part 2: PV_D5SK2 - Lectures L7 to L11)**. This final lab module covers advanced LVS verification scenarios: layout vs. CDL/Verilog for standard cells, macro-level LVS, complex mixed-signal Digital Phase-Locked Loop (PLL) verification, and debugging intricate device property errors.

> [!IMPORTANT]
> **Module 5 Author's Note & Disclaimer:**
> Please note that during Module 5 (`PV_D5SK1` & `PV_D5SK2`: LVS Theory & Labs), certain complex concepts and specific lab exercises were not fully understood or were challenging to complete. As a result, this documentation has been constructed explicitly based on my personal understanding, practical observations, and hands-on interpretation of the specific lectures and available lab outputs.

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

### Overview of Exercise 6 Setup (`digital_pll`)
This lab (`vsd_lvs_lab/exercise_6`) focuses on verifying standard cell digital block layouts (`digital_pll.mag`) against structural Verilog netlists (`digital_pll.v`). It demonstrates how physical-only components (filler cells, decap cells, substrate tap cells) introduced during placement and routing (PNR) affect Netgen LVS comparisons and how to resolve netlist mismatches.

---

### Part 1: Standard Cell Layout & Netlist Extraction in Magic

1. **Loading Digital Block Layout in Magic (`digital_pll.mag`):**
   * Opening `digital_pll.mag` in Magic layout editor displaying standard cell rows (`sky130_fd_sc_hd`), power straps, decoupling capacitors (`decap_3`, `decap_6`, `decap_8`), substrate taps (`tapvpwrvgnd_1`), and filler cells (`fill_1`, `fill_2`).
   * ![Magic Layout Load Digital PLL Cell](image/day10_l7_magic_layout_load_digital_pll.png)

2. **Layout DRC Inspection:**
   * Verification confirms layout is clean with `DRC=0`:
   * ![Digital PLL DRC=0 Layout Canvas View](image/day10_l7_digital_pll_drc0_layout_canvas.png)

3. **SPICE Netlist Extraction in Magic Console:**
   * Executing layout extraction commands in tkcon:
     ```tcl
     extract all
     ext2spice lvs
     ext2spice
     ```
   * Magic extracts all cell instances, ports, and parasitic connectivity into `digital_pll.spice`.
   * ![Magic Extraction and ext2spice LVS Execution](image/day10_l7_magic_extraction_ext2spice_lvs.png)

---

### Part 2: Initial Netgen LVS Run & Filler Cell Mismatch Analysis

1. **Executing Netgen LVS Comparison:**
   * Executing Netgen batch LVS comparing layout SPICE netlist against gate-level structural Verilog netlist:
     ```bash
     netgen -batch lvs "digital_pll.spice digital_pll" "digital_pll.v digital_pll" sky130A_setup.tcl exercise_6_comp.out
     ```

2. **Analyzing Netgen LVS Mismatch Report (`exercise_6_comp.out`):**
   * Netgen highlights device count mismatch between extracted layout (317 devices) and structural Verilog (320 devices), generating 74 LVS errors:
     ```text
     Class: sky130_fd_sc_hd__fill_1 instances: 1
     Class: sky130_fd_sc_hd__fill_2 instances: 1
     Class: sky130_fd_sc_hd__decap_12 instances: 1
     Circuit contains 323 nets.

     Circuit 1 contains 317 devices, Circuit 2 contains 320 devices. *** MISMATCH ***
     Circuit 1 contains 323 nets,    Circuit 2 contains 323 nets.

     Result: Netlists do not match.
     LVS file exercise_6_comp.json reports:
       device count difference = 3
       unmatched nets = 18
       unmatched devices = 53
       property failures = 0
     Total errors = 74
     ```
   * ![Netgen LVS Mismatch Report due to Filler Cells](image/day10_l7_netgen_lvs_mismatch_filler_cells.png)

---

### Part 3: Modifying Structural Verilog & Editing LVS Setup File

1. **Root Cause Identification:**
   * The placement flow inserts physical filler cells (`fill_1`, `fill_2`, `decap_12`) into layout rows to maintain well continuity and density compliance. If the structural Verilog netlist (`digital_pll.v`) has missing or mismatched filler cell declarations, Netgen flags device count and net topological mismatches.

2. **Editing Verilog Netlist & LVS Setup Commands:**
   * **Step A: Verilog Update**: Re-inspect structural Verilog `digital_pll.v` to align filler cell instantiations (`sky130_fd_sc_hd__fill_1`, `fill_2`) and power/ground port declarations (`VPWR`, `VGND`).
   * **Step B: Setup TCL Update**: Update `sky130A_setup.tcl` or LVS configuration script to blackbox or ignore physical-only filler cells during graph comparison:
     ```tcl
     # Ignore/blackbox physical filler cells in Netgen LVS
     blackbox sky130_fd_sc_hd__fill_1
     blackbox sky130_fd_sc_hd__fill_2
     blackbox sky130_fd_sc_hd__decap_12
     ```

3. **Re-running Netgen & Achieving LVS Match:**
   * Re-running Netgen LVS with updated setup and Verilog netlist aligns all 323 nets and cell instances 1-to-1:
     ```text
     Circuits match uniquely.
     Result: 0 errors found.
     ```

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
|---------------+
|  [✔] Clean Magic DRC (0 errors over full die area)                            |
|  [✔] Clean KLayout DRC (0 litho / density / antenna errors)                   |
|  [✔] Clean Netgen LVS ("Circuits match uniquely")                             |
|  [✔] Clean Antenna Ratio Verification (0 plasma gate oxide breakdown risks)   |
|  [✔] Layer Density Compliance (Metal1 - Metal5 within 30%-70% fill range)     |
|  [✔] Post-Layout Parasitic STA (Setup & Hold slack > 0ns in OpenSTA)          |
|  [✔] GDSII Stream-Out Verified (Off-grid & geometry overlap clean)            |
+-------------------------------------------------------------------------------+
```
