# DAY 4: Module 2 - PV_D2SK2: Labs for GDS Read/Write, Extraction, DRC, LVS and XOR Setup

## Overview
Day 4 covers **Module 2 (Part 2: PV_D2SK2 - Lectures L1 to L7)**. This lab module provides practical hands-on procedures for GDS importing/exporting, port creation, abstract view generation (LEF), parasitic extraction flows, custom DRC setup, Netgen LVS configuration, and automated XOR layout comparisons.

---

## Table of Contents
1. [PV_D2SK2_L1: GDS Read](#pv_d2sk2_l1-gds-read)
2. [PV_D2SK2_L2: Ports](#pv_d2sk2_l2-ports)
3. [PV_D2SK2_L3: Abstract Views](#pv_d2sk2_l3-abstract-views)
4. [PV_D2SK2_L4: Basic Extraction](#pv_d2sk2_l4-basic-extraction)
5. [PV_D2SK2_L5: Setup For DRC](#pv_d2sk2_l5-setup-for-drc)
6. [PV_D2SK2_L6: Setup For LVS](#pv_d2sk2_l6-setup-for-lvs)
7. [PV_D2SK2_L7: Setup For XOR](#pv_d2sk2_l7-setup-for-xor)

---

## PV_D2SK2_L1: GDS Read

### Practical Lab Procedure: Importing GDS in Magic
1. **Prepare PDK & Working Directory:**
   ```bash
   mkdir -p lab_gds && cd lab_gds
   magic -T sky130A.tech
   ```
2. **Execute GDS Import Command:**
   ```tcl
   gds read /path/to/cell_design.gds
   ```
3. **Verify Imported Hierarchy & Tiles:**
   * Open cell hierarchy in Magic: `cellname list`, `load <cell_name>`.
   * Check layer mapping correctness (`metal1`, `licon`, `nwell`) and verify grid alignment.
   * Save extracted layout as Magic `.mag` file format:
     ```tcl
     save <cell_name>.mag
     ```

---

## PV_D2SK2_L2: Ports

### Lab Procedure: Defining & Configuring Layout Terminals/Ports
Ports define electrical connections and boundary interfaces required for LVS matching and LEF abstract generation.

1. **Placing Labels on Routing Layers:**
   * Select a box on the target layer (e.g. `metal1`).
   * Create label in Magic:
     ```tcl
     label A free metal1
     label Y free metal1
     label VPWR free metal1
     label VGND free metal1
     ```
2. **Converting Labels to Formal Ports:**
   * Select label box and assign port properties:
     ```tcl
     port make
     port use input       ;# For input port A
     port class input
     port use output      ;# For output port Y
     port class output
     port use inout       ;# For power/ground VPWR, VGND
     port class power
     ```
3. **Inspecting Port Indexing:**
   * Verify created ports: `port list` or `port index`.

---

## PV_D2SK2_L3: Abstract Views

### Lab Procedure: Generating LEF (Library Exchange Format) Views
Abstract views conceal internal transistor geometries while preserving boundary dimensions, obstruction layers, and pin connection geometries for Place & Route (P&R) tools like OpenLane.

1. **Set Boundary Box & PR Boundary:**
   * Define cell bounding box (`property FIXED_BBOX`).
2. **Export LEF File from Magic:**
   ```tcl
   lef write <cell_name>.lef
   ```
3. **Verify LEF Contents:**
   * Inspect output `.lef` file: confirm `MACRO <cell_name>`, `FOREIGN`, `ORIGIN`, `SIZE`, `PIN` dimensions, and `OBS` (Obstruction) layers.

---

## PV_D2SK2_L4: Basic Extraction

### Lab Procedure: Full Extraction Flow in Magic
1. **Generate Extracted `.ext` Files:**
   ```tcl
   extract all
   ```
2. **Convert `.ext` Files to SPICE Format:**
   * **For Simulation (with RC Parasitics):**
     ```tcl
     ext2spice cthresh 0.01
     ext2spice extresist on
     ext2spice
     ```
   * **For LVS Verification (Clean Structural Netlist):**
     ```tcl
     ext2spice lvs
     ext2spice subcircuit on
     ext2spice
     ```
3. **Inspect Output SPICE File (`.spice`):**
   * Verify subcircuit definition (`.subckt`), pin list, transistor models (`sky130_fd_pr__nfet_01v8`), and extracted parasitics.

---

## PV_D2SK2_L5: Setup For DRC

### Lab Procedure: Running & Cleaning Design Rule Checks
1. **Interactive DRC Execution:**
   ```tcl
   drc check
   drc catchup
   ```
2. **Locating Violations:**
   * Count total DRC violations: `drc count`
   * Highlight error regions: `drc find`
   * Inspect violation details: `drc why`
3. **Fixing Common DRC Violations:**
   * **Width Violations:** Expand wire or polygon trace to meet minimum width requirement.
   * **Spacing Violations:** Move adjacent wires apart to satisfy layer spacing rules.
   * **Enclosure Violations:** Extend metal/diffusion boundaries around contact vias (`licon`, `mcon`).

---

## PV_D2SK2_L6: Setup For LVS

### Lab Procedure: Executing Netgen LVS Verification
1. **Prepare Layout & Schematic Netlists:**
   * Layout netlist generated from Magic: `inverter_layout.spice`
   * Schematic netlist exported from Xschem: `inverter_schematic.spice`
2. **Configure Netgen LVS Script:**
   * Netgen setup file: `sky130A_setup.tcl`
3. **Run Netgen LVS Command:**
   ```bash
   netgen -batch lvs "inverter_layout.spice" "inverter_schematic.spice" /usr/share/pdk/sky130A/libs.tech/netgen/sky130A_setup.tcl lvs_comp.out
   ```
4. **Interpret LVS Result Log (`lvs_comp.out`):**
   * Check final verdict:
     ```text
     Result: Circuits match uniquely.
     ```
   * If mismatched, review device count discrepancies, net shorts/opens, or pin permutation errors reported in `lvs_comp.out`.

---

## PV_D2SK2_L7: Setup For XOR

### Lab Procedure: Executing Automated XOR Comparison
1. **Prepare Two Layout Revisions (`design_v1.gds` vs `design_v2.gds`).**
2. **KLayout Automated XOR Script Execution:**
   ```bash
   klayout -b -r xor_script.drc -rd file1=design_v1.gds -rd file2=design_v2.gds -rd output=xor_diff.gds
   ```
3. **Viewing XOR Differences:**
   * Open `xor_diff.gds` in KLayout / Magic.
   * Highlighted non-zero shapes represent exact physical geometry differences between the two layout versions.
