# DAY 9: Module 5 - PV_D5SK2: LVS Labs (Part 1)

## Overview
Day 9 covers **Module 5 (Part 2: PV_D5SK2 - Lectures L1 to L6)**. This practical lab module provides hands-on exercises for executing Netgen LVS comparisons on simple circuits, hierarchical subcircuits, blackboxed subblocks, low-level SPICE primitive components, and a small analog block (Power-On Reset circuit).

---

## Table of Contents
1. [PV_D5SK2_L1: Simple LVS Experiment](#pv_d5sk2_l1-simple-lvs-experiment)
2. [PV_D5SK2_L2: LVS With Subcircuits](#pv_d5sk2_l2-lvs-with-subcircuits)
3. [PV_D5SK2_L3: LVS With Blackboxes Subcircuits](#pv_d5sk2_l3-lvs-with-blackboxes-subcircuits)
4. [PV_D5SK2_L4: LVS With SPICE Low Level Components](#pv_d5sk2_l4-lvs-with-spice-low-level-components)
5. [PV_D5SK2_L5: LVS For Small Analog Block - Power-On Reset - Part 1](#pv_d5sk2_l5-lvs-for-small-analog-block---power-on-reset---part-1)
6. [PV_D5SK2_L6: LVS For Small Analog Block - Power-On Reset - Part 2](#pv_d5sk2_l6-lvs-for-small-analog-block---power-on-reset---part-2)

---

## PV_D5SK2_L1: Simple LVS Experiment

### Lab Objective
Run a baseline Netgen LVS check on a simple CMOS inverter layout vs. schematic.

1. **Extract Layout Netlist in Magic:**
   ```tcl
   magic -T sky130A.tech inverter.mag
   extract all
   ext2spice lvs
   ext2spice
   ```
2. **Export Schematic Netlist in Xschem:**
   * Export `inverter.spice` from Xschem schematic editor.
3. **Execute Netgen LVS Batch:**
   ```bash
   netgen -batch lvs "inverter.spice" "inverter_schematic.spice" /usr/share/pdk/sky130A/libs.tech/netgen/sky130A_setup.tcl lvs_comp.out
   ```
4. **Verify Result:** Confirm `Result: Circuits match uniquely.` in `lvs_comp.out`.

---

## PV_D5SK2_L2: LVS With Subcircuits

### Lab Objective
Perform hierarchical LVS matching on designs containing nested subcircuit blocks (`.subckt`).

1. **Subcircuit Hierarchy Setup:**
   * Top-level schematic instantiating subblocks (e.g. 2-to-1 Multiplexer containing inverter subcircuits).
2. **Hierarchical Extraction:**
   * Ensure Magic extracts subcircuits cleanly: `ext2spice subcircuit on`.
3. **Netgen Hierarchical Matching:**
   * Netgen matches child subcircuits first before attempting top-level graph matching.
   * Debug subcircuit pin order mismatches in `lvs_comp.out`.

---

## PV_D5SK2_L3: LVS With Blackboxes Subcircuits

### Lab Objective
Blackbox IP blocks or memory macros during LVS when layout details are omitted or proprietary.

1. **Defining Blackbox Elements in `setup.tcl`:**
   ```tcl
   # Instruct Netgen to treat subcircuit 'ram_1k' as a blackbox
   blackbox ram_1k
   ```
2. **Blackbox Matching Criteria:**
   * Netgen checks matching subcircuit name and port connection count without comparing internal transistor structures.
3. **Verification:**
   * Run Netgen LVS to confirm top-level interconnections to blackboxed macros match schematic definitions.

---

## PV_D5SK2_L4: LVS With SPICE Low Level Components

### Lab Objective
Verify low-level SPICE primitives (resistors, MIM capacitors, diodes, high-voltage transistors).

1. **SPICE Model & PDK Device Names:**
   * Layout device models (`sky130_fd_pr__res_high_po`, `sky130_fd_pr__cap_mim_m3_1`) must map to corresponding schematic SPICE primitives.
2. **Setup File Equivalence Mapping:**
   ```tcl
   # Map schematic symbol name to layout extracted device name
   equate devices sky130_fd_pr__res_high_po res_high_po
   ```
3. **Run LVS:** Verify parameter properties ($R$ in Ohms, $C$ in Farads).

---

## PV_D5SK2_L5: LVS For Small Analog Block - Power-On Reset - Part 1

### Lab Objective
Schematic capture and layout extraction for a Power-On Reset (POR) analog circuit block.

1. **POR Circuit Topology:**
   * Consists of RC delay network, bias generator, threshold comparators, and inverter chain output.
2. **Schematic & Layout Alignment:**
   * Set up POR schematic in Xschem (`por.sch`).
   * Perform layout extraction in Magic (`por.mag`).
3. **Initial LVS Run & Debugging:**
   * Execute Netgen LVS; identify initial pin mismatches and bulk bias connection errors.

---

## PV_D5SK2_L6: LVS For Small Analog Block - Power-On Reset - Part 2

### Lab Objective
Resolving complex analog LVS discrepancies, substrate tap connections, and property errors in POR block.

1. **Substrate Tap & Bulk Connection Fixing:**
   * Ensure PMOS body bias ($V_{DD}$) and NMOS body bias ($V_{SS}$) taps in layout are connected to the exact schematic bulk nodes.
2. **Resistor & Transistor Ratio Tuning:**
   * Match multi-finger transistor geometries and series/parallel resistor segments.
3. **Final Verification:**
   * Re-run Netgen LVS until obtaining clean report:
     ```text
     Result: Circuits match uniquely.
     ```
