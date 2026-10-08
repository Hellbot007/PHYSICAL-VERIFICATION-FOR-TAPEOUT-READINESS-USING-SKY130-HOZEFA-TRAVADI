# DAY 6: Module 3 - PV_D3SK2: Labs for All DRC Rules

## Overview
Day 6 covers **Module 3 (Part 2: PV_D3SK2 - Lectures L1 to L11)**. This practical lab module provides hands-on exercises for writing, debugging, and verifying Design Rule Checking (DRC) rules in Magic and SkyWater SKY130 PDK technology files.

---

## Table of Contents
1. [PV_D3SK2_L1: Lab For Width Rule And Spacing Rule](#pv_d3sk2_l1-lab-for-width-rule-and-spacing-rule)
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

## PV_D3SK2_L1: Lab For Width Rule And Spacing Rule

### Objective
Learn syntax and verification procedures for basic width and spacing DRC rules.

1. **Tech File Syntax (`drc` section):**
   ```tcl
   width metal1 140 "Metal1 width must be at least 0.14um"
   spacing metal1 metal1 140 touching_ok "Metal1 spacing must be at least 0.14um"
   ```
2. **Lab Verification in Magic:**
   * Draw `metal1` trace of width $0.10\,\mu\text{m}$ (Trigger DRC violation: `drc why` $\rightarrow$ Metal1 width $< 0.14\,\mu\text{m}$).
   * Draw two `metal1` traces with $0.10\,\mu\text{m}$ spacing (Trigger DRC violation: Metal1 spacing $< 0.14\,\mu\text{m}$).
   * Fix geometries to $0.14\,\mu\text{m}$ and confirm **DRC=0 errors**.

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
