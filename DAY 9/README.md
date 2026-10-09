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

### Overview of Exercise 1 Setup
This lab (`vsd_lvs_lab/exercise_1`) introduces baseline Netgen LVS comparison mechanics by comparing primitive netlists (`netA.spice` and `netB.spice`) with direct device instances (`cell1`, `cell2`, `cell3`).

---

### Part 1: Netlist Inspection & Simulation vs. LVS Behavior

1. **Comparing Source Netlists (`netA.spice` & `netB.spice`):**
   * Inspect netlist definitions:
     ```spice
     * Example SPICE netlist netA.spice
     X1 A B C cell1
     X2 A B A cell2
     X3 C C A cell3
     .end
     ```
   * Running `diff netA.spice netB.spice` confirms that only comment lines differ.
   * ![Netlist netA.spice and netB.spice Comparison](image/day9_l1_netA_netB_netlists.png)

2. **NGSPICE Simulation Error Behavior:**
   * Attempting to simulate `netA.spice` in NGSPICE returns:
     ```text
     Error: unknown subckt: x1 a b c cell1
     Simulation interrupted due to error!
     ```
   * *Note:* Simulation engines require defined subcircuit models, whereas Netgen LVS compares graph connectivity without needing simulation models.
   * ![NGSPICE Undefined Subcircuit Error](image/day9_l1_ngspice_subckt_error.png)
   * ![NGSPICE Terminal Execution View](image/day9_l1_ngspice_terminal.png)

---

### Part 2: Netgen LVS Execution & Mismatch Debugging

1. **Executing Netgen LVS (`Circuits match uniquely`):**
   * Running Netgen comparison in tkcon:
     ```text
     Circuit netA.spice contains 3 device instances.
     Circuit netB.spice contains 3 device instances.
     Netlists match uniquely.
     Result: Circuits match uniquely.
     ```
   * ![Netgen LVS Circuits Match Uniquely](image/day9_l1_netgen_match_uniquely.png)

2. **Modifying Netlist Connections & Debugging Mismatch:**
   * Edit `netA.spice` to change instance net connection: `X3 B C A cell3`.
   * ![Modifying Netlist netA.spice Net Connections](image/day9_l1_netA_modified_netlist.png)
   * Re-running Netgen LVS reports `Result: Netlists do not match.`:
   * ![Netgen Netlists Do Not Match Output](image/day9_l1_netlists_do_not_match.png)

3. **Inspecting `comp.out` Detailed Log:**
   * Netgen `comp.out` details specific net fanout differences (Net B vs Net C) and instance node mismatches (`cell33` and `cell22`).
   * ![Netgen Detailed comp.out Mismatch Report](image/day9_l1_comp_out_mismatch_report.png)

---

## PV_D5SK2_L2: LVS With Subcircuits

### Overview of Exercise 2 Setup
This lab (`vsd_lvs_lab/exercise_2`) demonstrates hierarchical LVS matching on designs wrapped inside `.subckt` definitions and evaluates subcircuit pin order sensitivity in Netgen.

---

### Part 1: Subcircuit Wrapper Netlists & Automatic Blackboxing

1. **Subcircuit Netlist Setup:**
   * Netlists wrapped inside subcircuit blocks (`.subckt test A B C ... .ends`):
     ```spice
     * Example SPICE netlist netA.spice
     .subckt test A B C
     X1 A B C cell1
     X2 A B A cell2
     X3 C C A cell3
     .ends
     .end
     ```
   * ![Exercise 2 Subcircuit Wrapper Netlists](image/day9_l2_exercise2_subckt_netlists.png)

2. **Automatic Blackbox Handling:**
   * Netgen automatically treats subcircuits lacking internal definitions (`cell1`, `cell2`, `cell3`) as blackbox placeholders:
     ```text
     Warning: Equate pins: cell cell1 has no definition, treated as a black box.
     Creating placeholder cell definition.
     ```
   * ![Undefined Subcircuit Blackbox Warning](image/day9_l2_undefined_subckt_placeholders.png)
   * ![Blackbox Equate Pins Warning in Netgen](image/day9_l2_blackbox_equate_pins.png)
   * ![NGSPICE Analysis Interface](image/day9_l2_ngspice_analysis.png)

---

### Part 2: Hierarchical Matching & Pin Order Mismatch Debugging

1. **Hierarchical Matching Result (`Circuits match uniquely`):**
   * Netgen compares subcircuit pin lists and top-level block connections:
     ```text
     Cell pin lists are equivalent.
     Circuits match uniquely.
     Result: Circuits match uniquely.
     ```
   * ![Netgen LVS Matching Output](image/day9_l2_netgen_lvs_match_output.png)
   * ![Netgen Circuits Match Uniquely Output](image/day9_l2_circuits_match_uniquely.png)

2. **Subcircuit Pin Order Mismatch Experiment:**
   * Edit `.subckt test C B A` in `netA.spice` to alter pin sequence.
   * ![Modifying Subcircuit Pin Order](image/day9_l2_subckt_pin_order_modified.png)
   * ![Subcircuit Instantiation Pin Order Swap](image/day9_l2_subckt_instantiation_swap.png)

3. **Netgen Pin Order Mismatch Log:**
   * Netgen detects pin order discrepancy and reports `Result: Netlists do not match.`:
   * ![Netlists Do Not Match Result](image/day9_l2_netlists_do_not_match_result.png)
   * ![Netgen comp.out Subcircuit Pin Mismatch Report](image/day9_l2_comp_out_pin_mismatch.png)
   * ![Final Netgen LVS Mismatch Summary](image/day9_l2_final_mismatch_summary.png)

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
