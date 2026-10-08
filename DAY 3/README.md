# DAY 3: Module 2 - PV_D2SK1: Introduction to DRC and LVS (Theory)

## Overview
Day 3 covers **Module 2 (Part 1: PV_D2SK1 - Lectures L1 to L9)**. This theoretical module provides an in-depth exploration of IC physical verification fundamentals, including the GDSII file format, layout extraction mechanisms in Magic, GDS reading/writing options, DRC rule definitions, LVS comparison setups in Netgen, and Boolean XOR layout verification.

---

## Table of Contents
1. [PV_D2SK1_L1: Understanding GDS Format](#pv_d2sk1_l1-understanding-gds-format)
2. [PV_D2SK1_L2: Extraction Commands, Styles and Options In Magic](#pv_d2sk1_l2-extraction-commands-styles-and-options-in-magic)
3. [PV_D2SK1_L3: Advanced Extraction Options In Magic](#pv_d2sk1_l3-advanced-extraction-options-in-magic)
4. [PV_D2SK1_L4: GDS Reading Option In Magic](#pv_d2sk1_l4-gds-reading-option-in-magic)
5. [PV_D2SK1_L5: GDS Writing, Input, Output Styles and Output Issues](#pv_d2sk1_l5-gds-writing-input-output-styles-and-output-issues)
6. [PV_D2SK1_L6: DRC Rules In Magic](#pv_d2sk1_l6-drc-rules-in-magic)
7. [PV_D2SK1_L7: Extraction Rules And Errors In Magic](#pv_d2sk1_l7-extraction-rules-and-errors-in-magic)
8. [PV_D2SK1_L8: LVS Setup For Netgen](#pv_d2sk1_l8-lvs-setup-for-netgen)
9. [PV_D2SK1_L9: Verification By XOR](#pv_d2sk1_l9-verification-by-xor)

---

## PV_D2SK1_L1: Understanding GDS Format

GDSII (Graphic Data System II, format `.gds`) is the industry-standard binary stream format for transferring IC layout geometry data between EDA software and semiconductor foundries.

* **Binary Stream Structure:** Organized as records containing length indicators, record types, and data payloads.
* **Hierarchical Elements:**
  * **Header & Library:** Defines stream version, database units (e.g. $0.001\,\mu\text{m}$ grid), and library name.
  * **Structure (Cell):** Contains geometry definitions for standard cells, macros, or full chip layouts.
  * **Elements:**
    * **Boundary (Polygon):** Closed polygon geometries representing mask layers.
    * **Path (Wire):** Traces with designated width and end-cap styles.
    * **SRef / ARef:** Structure References (instantiating a subcell) and Array References (regular grids of cells).
    * **Text:** Labels for signal names, power rails, and terminal ports.
* **Layer & Datatype Mapping:** Integer pairs (`Layer:Datatype`), e.g., in Sky130:
  * `68:20` represents `metal1` drawing layer.
  * `68:5` represents `metal1` pin label layer.

---

## PV_D2SK1_L2: Extraction Commands, Styles and Options In Magic

Extraction is the process of reading physical layout geometries and generating an intermediate circuit description (`.ext` files) containing devices, nets, and connectivity.

* **Core Extraction Command:**
  ```tcl
  extract all
  ```
  Generates `.ext` files for the top cell and all subcells.
* **Extraction Commands & Switches:**
  * `extract warning [option]`: Enable or disable specific extraction warnings (e.g. dup, floating).
  * `extract style [stylename]`: Select extraction parameter style defined in the technology file.
  * `extract do [option]`: Enable specific extraction features (e.g. `extract do local`, `extract do resistance`).
* **Extraction Styles in Tech File:**
  * Defined under the `extract` section of `sky130A.tech`.
  * Allows switching between fast DRC extraction, standard SPICE extraction, or detailed RC parasitic extraction.

---

## PV_D2SK1_L3: Advanced Extraction Options In Magic

Advanced extraction controls how parasitics, subcircuits, and device properties are written into SPICE netlists.

* **Parasitic Threshold Filtering:**
  * `ext2spice cthresh <fF>`: Set minimum capacitance threshold (e.g., `cthresh 0.01` ignores parasitic caps below 0.01 fF).
  * `ext2spice rthresh <ohms>`: Set minimum resistance threshold for RC extraction.
* **Parasitic RC Extraction Commands:**
  ```tcl
  extract all
  ext2sim labels on
  ext2sim
  extresist tolerance 10
  extresist
  ext2spice lvs
  ext2spice cthresh 0.01
  ext2spice extresist on
  ext2spice
  ```
* **LVS Extraction vs. Simulation Extraction:**
  * **LVS Extraction (`ext2spice lvs`):** Preserves hierarchy, disables parasitic caps/resistors, and outputs clean netlists for structural comparison.
  * **Simulation Extraction (`ext2spice`):** Flattens parasitic elements, includes parasitic ground/coupling capacitances, and formats netlist for Ngspice analysis.

---

## PV_D2SK1_L4: GDS Reading Option In Magic

Importing GDSII files into Magic requires mapping binary GDS layer-datatype pairs onto Magic's internal tile layers.

* **GDS Read Command:**
  ```tcl
  gds read <filename.gds>
  ```
* **Technology Input Rules (`cifinput` Section):**
  * Magic uses `cifinput` statements in `sky130A.tech` to translate GDS layer numbers into Magic tile types (e.g., GDS layer 68 $\rightarrow$ `metal1`).
* **GDS Import Settings:**
  * `gds flatglob <pattern>`: Automatically flatten subcells matching specific patterns during import.
  * `gds readonly true/false`: Read GDS subcells as read-only vendor macros (abstract views) to save memory.
  * `gds rescale true/false`: Rescale database grid if GDS grid unit differs from Magic's grid unit.

---

## PV_D2SK1_L5: GDS Writing, Input, Output Styles and Output Issues

Writing GDSII layout files from Magic translates internal Magic tiles into foundry mask geometries.

* **GDS Write Command:**
  ```tcl
  gds write <filename.gds>
  ```
* **Output Styles (`cifoutput` Section):**
  * Defines output rules, layer composition, contact generation, and vendor polygon formatting.
* **Common GDS Output Issues & Solutions:**
  * **Off-Grid Shapes:** Non-integer snap grid coordinates causing DRC errors during manufacturing mask generation.
  * **Unconnected Labels:** Text labels attached to incorrect or ambiguous layers.
  * **Overlapping Polygons:** Self-intersecting polygon boundaries that violate GDSII stream specifications.
  * **Missing Subcells:** Unresolved cell references (`SRef`) when writing hierarchical layouts.

---

## PV_D2SK1_L6: DRC Rules In Magic

Design Rule Checking (DRC) ensures that layout dimensions comply with foundry manufacturing tolerances.

* **Primary DRC Categories:**
  * **Minimum Width:** Minimum dimension of a conductive or active region (e.g., `metal1` min width = $0.14\,\mu\text{m}$).
  * **Minimum Spacing:** Minimum distance between disjoint geometries of the same or different layers (e.g., `metal1` to `metal1` min space = $0.14\,\mu\text{m}$).
  * **Enclosure / Overhang:** Extension of one layer beyond another (e.g., `licon` contact must be enclosed by `metal1`).
  * **Well & Substrate Rules:** Minimum spacing between N-well and P-substrate taps to prevent latch-up.
* **Real-Time Interactive DRC Engine:**
  * Magic continuously runs background DRC as layout editing occurs.
  * `drc check`: Manually trigger DRC check over the current box region.
  * `drc why`: Displays detailed explanation of DRC violations inside the selected box.
  * `drc count`: Summarizes total number of DRC violations in the design.

---

## PV_D2SK1_L7: Extraction Rules And Errors In Magic

The `extract` section of Magic's technology file defines how physical geometries form electrical components.

* **Defining Device Extraction Rules:**
  * FET definition syntax in tech file:
    ```tcl
    fet nfet nwell,diff,poly pwell nfet_01v8
    ```
  * Specifies diffusion layer, gate poly, bulk well layer, and extracted device model name (`nfet_01v8`).
* **Common Extraction Errors & Warnings:**
  * **Floating Nodes:** Isolated diffusion or metal regions with no net connection.
  * **Short Circuits:** Accidental overlap of different signal nets (e.g. $V_{DD}$ shorted to $V_{SS}$).
  * **Unconnected Substrates / Bulk Nodes:** Transistors with missing N-well or P-substrate tap connections.
  * **Duplicate Port Labels:** Identical signal names placed on different unconnected nets.

---

## PV_D2SK1_L8: LVS Setup For Netgen

Layout vs. Schematic (LVS) compares the extracted layout topology against the original schematic topology using **Netgen**.

* **LVS Principles:**
  * Graph isomorphism matching: Compares netlist graphs regardless of physical component placement or line routing.
  * Verification of device types, counts, net connections, and device parameters ($W$, $L$).
* **Netgen Configuration Script (`setup.tcl`):**
  * **Permute Pins:** Defines equivalent pins for symmetric gates (e.g., inputs A and B of a NAND gate can swap).
  * **Property Matching:** Specifies allowable tolerance thresholds for transistor $W/L$ ratios and resistor/capacitor values.
  * **Subcircuit Merging:** Rules for combining parallel or series transistors into equivalent single devices.
* **Running Netgen LVS in Batch Mode:**
  ```bash
  netgen -batch lvs "layout.spice" "schematic.spice" sky130A_setup.tcl lvs_comp.out
  ```

---

## PV_D2SK1_L9: Verification By XOR

XOR verification performs a Boolean Exclusive-OR operation between two versions of a GDS layout to detect physical differences.

```
Layout Revision A  (Original)    :  [⬛⬛⬜⬜]
Layout Revision B  (Modified)    :  [⬛⬜⬛⬜]
------------------------------------------------
XOR Result Output (Differences)  :  [⬜⬛⬛⬜]
```

* **Use Cases:**
  * Validating layout edits between revisions.
  * Confirming that metal fill, guard ring additions, or ECOs (Engineering Change Orders) did not accidentally alter surrounding circuitry.
  * Comparing GDS exported from Magic against GDS exported from KLayout or OpenLane.
* **XOR Execution Methods:**
  * **KLayout XOR:** Running automated KLayout Python/Ruby scripts (`klayout -b -r xor_script.drc`).
  * **Magic XOR Routine:** Reading both GDS files into separate Magic layers and performing `flatten` + Boolean subtraction (`paint` / `erase`).
