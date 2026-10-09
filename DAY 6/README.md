# DAY 6: Module 3 - PV_D3SK2: Labs for All DRC Rules

## Overview
Day 6 covers **Module 3 (Part 2: PV_D3SK2 - Lectures L1 to L11)**. This practical lab module provides hands-on exercises for writing, debugging, and verifying Design Rule Checking (DRC) rules in Magic and SkyWater SKY130 PDK technology files.

---

## Table of Contents
1. [PV_D3SK2_L1: Lab For Width Rule And Spacing Rule (Exercise 1 Lab)](#pv_d3sk2_l1-lab-for-width-rule-and-spacing-rule-exercise-1-lab)
2. [PV_D3SK2_L2: Lab For Wide Spacing Rule And Notch Rule](#pv_d3sk2_l2-lab-for-wide-spacing-rule-and-notch-rule)
3. [PV_D3SK2_L3: Lab For Via Size, Multiple Vias, Via Overlap and Autogenerate Vias](#pv_d3sk2_l3-lab-for-via-size-multiple-vias-via-overlap-and-autogenerate-vias)
4. [PV_D3SK2_L4: Lab For Minimum Area Rule And Minimum Hole Rule](#pv_d3sk2_l4-lab-for-minimum-area-rule-and-minimum-hole-rule)
5. [PV_D3SK2_L5: Lab For Wells And Deep N-Well](#pv_d3sk2_l5-lab-for-wells-and-deep-n-well)
6. [PV_D3SK2_L6: Lab For Derived Layers](#pv_d3sk2_l6-lab-for-derived-layers)
7. [PV_D3SK2_L7: Lab For Parameterized And PDK Devices](#pv_d3sk2_l7-lab-for-parameterized-and-pdk-devices)
8. [PV_D3SK2_L8: Lab For Angle Error And Overlap Rule](#pv_d3sk2_l8-lab-for-angle-error-and-overlap-rule)
9. [PV_D3SK2_L9: Lab For Unimplemented Rules](#pv_d3sk2_l9-lab-for-unimplemented-rules)
10. [PV_D3SK2_L10: Latch-up And Antenna Rules](#pv_d3sk2_l10-latch-up-and-antenna-rules)
11. [PV_D3SK2_L11: Lab For Density Rules](#pv_d3sk2_l11-lab-for-density-rules)

---

## PV_D3SK2_L1: Lab For Width Rule And Spacing Rule (Exercise 1 Lab)

### Overview of Exercise 1 Setup
* **Cell Loaded:** `exercise_1` in Magic with `sky130A` technology file.
* **Exercise Modules Included (4 Sub-Exercises):**
  1. `Exercise_1a: Width_rule`
  2. `Exercise_1b: Spacing_rule`
  3. `Exercise_1c: Wide_spacing_rule`
  4. `Exercise_1d: Notch_rule`
* **Initial State:** Initial layout triggers `DRC=7` violations indicated by white dotted error boxes.

![Exercise 1 Overview in Magic (DRC=7)](images/day6_l1_exercise1_overview.png)

---

### Part 1: Debugging & Fixing Exercise_1a (Width Rule)

1. **DRC Violation Diagnostic (`drc why`):**
   * Hover box over `Exercise_1a` trace and execute `drc why` in tkcon:
     ```text
     Metal2 width < 0.14um (met2.1)
     ```
   * ![Exercise 1a DRC Why Command](images/day6_l1_ex1a_drc_why.png)
   * ![Exercise 1a Selection View](images/day6_l1_ex1a_no_errors.png)
   * ![Exercise 1a Dotted DRC Error Box](images/day6_l1_ex1a_close_up.png)

2. **Measuring Polygon Geometry (`box`):**
   * Execute `box` command in tkcon:
     ```text
     microns: 0.06 x 1.53
     ```
   * Current width of $0.06\,\mu\text{m}$ falls below minimum `metal2` width constraint of $0.14\,\mu\text{m}$.
   * ![Box Measurement Command](images/day6_l1_ex1a_box_measurement.png)

3. **Applying Width Correction Commands:**
   * Set box width to $0.14\,\mu\text{m}$ and paint `metal2`:
     ```tcl
     box width 0.14um
     paint m2
     ```
   * ![Executing Box Width and Paint Commands](images/day6_l1_ex1a_tkcon_commands.png)
   * ![Metal2 Width Expanded to 0.14um](images/day6_l1_ex1a_paint_m2.png)

4. **Verification & DRC Clearance:**
   * Expanding trace width to $0.14\,\mu\text{m}$ clears the violation. `drc why` reports `No errors found.` and the white dotted error box vanishes.
   * ![Exercise 1a DRC Cleared](images/day6_l1_ex1a_drc_cleared.png)

---

### Part 2: Debugging & Fixing Exercise_1b (Spacing Rule)

1. **DRC Violation Diagnostic (`drc why`):**
   * Select error region between adjacent `metal1` traces in `Exercise_1b`.
   * Run `drc why`:
     ```text
     Metal1 spacing < 0.14um (met1.2)
     ```
   * ![Exercise 1b DRC Why Command](images/day6_l1_ex1b_drc_why.png)
   * ![Exercise 1b Dotted DRC Error Box](images/day6_l1_ex1b_box_spacing.png)

2. **Applying Spacing Correction Commands:**
   * Set minimum clearance gap box to $0.14\,\mu\text{m}$ and adjust layout spacing:
     ```tcl
     box width 0.14um
     paint m2
     ```

3. **Verification & DRC Clearance:**
   * After repainting spacing gap to meet $0.14\,\mu\text{m}$, the white dotted error box disappears and tkcon reports `No errors found.` for Exercise 1b.
   * ![Exercise 1b DRC Cleared](images/day6_l1_ex1b_drc_cleared.png)

---

## PV_D3SK2_L2: Lab For Wide Spacing Rule And Notch Rule

### Objective
Implement conditional spacing rules for wide metal wires and notch spacing checks.

1. **Wide Spacing Rule Syntax:**
   ```tcl
   spacing metal1 metal1 280 corner_touching "Wide metal1 (>1.5um) spacing must be 0.28um"
   ```
2. **Notch Spacing Rule:**
   * A notch is a narrow gap within a single continuous polygon.
   * `spacing metal1 metal1 140 notch_check` forces minimum notch fill or clearance.
3. **Lab Verification:**
   * Create a wide $2.0\,\mu\text{m}$ `metal1` bus adjacent to a thin wire with $0.14\,\mu\text{m}$ gap $\rightarrow$ Triggers wide metal DRC rule.
   * Increase clearance to $0.28\,\mu\text{m}$ to clean violation.

---

## PV_D3SK2_L3: Lab For Via Size, Multiple Vias, Via Overlap and Autogenerate Vias

### Objective
Verify via geometry rules and automatic via generation in Magic.

1. **Via Cut Rules:**
   * Fixed cut size: `via1` cut size $= 0.17\,\mu\text{m} \times 0.17\,\mu\text{m}$.
   * Via surround: `metal1` surround over `via1` $\ge 0.05\,\mu\text{m}$, `metal2` surround $\ge 0.05\,\mu\text{m}$.
2. **Autogenerate Vias in Magic:**
   * Draw overlapping `metal1` and `metal2` regions.
   * Execute auto-via placement: `via v3d` or `construct via1`.
   * Magic automatically array-fills required via cuts to handle current density.

---

## PV_D3SK2_L4: Lab For Minimum Area Rule And Minimum Hole Rule

### Objective
Configure minimum polygon area and enclosed hole rules.

1. **Minimum Area Rule Syntax:**
   ```tcl
   area metal1 14000 "Metal1 minimum area must be at least 0.014um^2"
   ```
2. **Minimum Hole Rule:**
   * Enforces minimum size for enclosed void holes inside wide metal planes to prevent resist collapse during litho.
3. **Lab Verification:**
   * Draw isolated $0.14\,\mu\text{m} \times 0.14\,\mu\text{m}$ square `metal1` patch ($\text{Area} = 0.0196\,\mu\text{m}^2 \ge 0.014\,\mu\text{m}^2$).
   * Shrink to $0.14\,\mu\text{m} \times 0.05\,\mu\text{m}$ patch ($\text{Area} = 0.007\,\mu\text{m}^2$) $\rightarrow$ Triggers minimum area DRC error.

---

## PV_D3SK2_L5: Lab For Wells And Deep N-Well

### Objective
Enforce well spacing, well tap rules, and Deep N-Well (`dnwell`) clearances.

1. **Well Spacing Syntax:**
   ```tcl
   spacing nwell nwell 1270 "N-Well spacing (different potential) must be at least 1.27um"
   ```
2. **Deep N-Well Isolation Checks:**
   * Verify enclosure of `dnwell` around isolated `pwell`.
   * Check clearance between `dnwell` and outer `nwell` contacts.

---

## PV_D3SK2_L6: Lab For Derived Layers

### Objective
Create temporary derived layers in Magic's technology file to evaluate complex multi-layer DRC rules.

1. **Derived Layer Definition in Tech File:**
   ```tcl
   alias gate "poly AND diff"
   alias nfet_gate "gate AND nsdm"
   ```
2. **Lab Application:**
   * Use `gate` derived layer to enforce gate extension (`poly` overhang beyond `diff`) and gate spacing to active contacts.

---

## PV_D3SK2_L7: Lab For Parameterized And PDK Devices

### Objective
Verify DRC compliance of parameterized cells (P-Cells) and PDK macros.

1. **P-Cell Verification:**
   * Generate `nfet_01v8` with variable fingers ($N=1, 2, 4$) and widths ($W = 1\,\mu\text{m}$ to $10\,\mu\text{m}$).
   * Verify that guard ring spacing dynamically adjusts without causing DRC errors.

---

## PV_D3SK2_L8: Lab For Angle Error And Overlap Rule

### Objective
Detect non-manhattan off-angle geometries and illegal layer overlaps.

1. **Off-Angle Geometry Check:**
   * Magic enforces Manhattan grid alignment ($90^\circ$ and $45^\circ$ edges).
   * Non-manhattan edges trigger `non-integer grid / angle error`.
2. **Layer Overlap Rules:**
   * Enforce valid overlap conditions between `licon`, `li`, and `metal1`.

---

## PV_D3SK2_L9: Lab For Unimplemented Rules

### Objective
Identify DRC rules not directly supported by Magic's built-in engine and offload verification to KLayout DRC scripts.

1. **Unimplemented Rule Handling:**
   * Complex 2D contextual rules (e.g. End-of-Line spacing with run length) are exported to KLayout DRC script format (`.drc`).
2. **Executing KLayout DRC Batch:**
   ```bash
   klayout -b -r sky130A_drc.drc -rd input=design.gds
   ```

---

## PV_D3SK2_L10: Latch-up And Antenna Rules

### Objective
Setup and verify latch-up tap distance checks and antenna ratio calculation scripts.

1. **Latch-up Check:**
   * Measure distance from every transistor diffusion edge to nearest `ntap`/`ptap`.
   * If distance $> 20\,\mu\text{m}$, insert substrate tap.
2. **Antenna Rule Check:**
   * Calculate ratio of metal area connected to poly gate.
   * Insert antenna diode (`sky130_fd_pr__diode`) if ratio exceeds PDK limit.

---

## PV_D3SK2_L11: Lab For Density Rules

### Objective
Execute metal density checks and generate dummy metal fill.

1. **Running Density Script:**
   * Calculate metal density over $100\,\mu\text{m} \times 100\,\mu\text{m}$ sliding windows.
2. **Generating Dummy Metal Fill:**
   * Run OpenLane / KLayout dummy fill generation script to insert floating `metal1` through `metal5` tiles in sparse regions.
