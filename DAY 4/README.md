# DAY 4: Module 2 - Tool Installations & Basic DRC/LVS Design Flow (Lab Part 2)

## Overview
Day 4 covers **Module 2: PV_D1SK2 (Lab Part 2)**, focusing on symbol generation, exporting netlists, schematic-to-layout alignment, DRC/LVS verification, and post-layout SPICE simulations.

---

## Table of Contents
1. [PV_D1SK2_L4: Creating Symbol and Exporting Schematic In Xschem](#pv_d1sk2_l4-creating-symbol-and-exporting-schematic-in-xschem)
2. [PV_D1SK2_L5: Importing Schematic To Layout & Inverter Layout Steps](#pv_d1sk2_l5-importing-schematic-to-layout--inverter-layout-steps)
3. [PV_D1SK2_L6: Final DRC/LVS Checks & Post Layout Simulations](#pv_d1sk2_l6-final-drclvs-checks--post-layout-simulations)

---

## PV_D1SK2_L4: Creating Symbol and Exporting Schematic In Xschem

1. **Symbol Generation:**
   * Automatically generate a symbol representation (`inverter.sym`) from schematic (`inverter.sch`).
   * Define input pin `in`, output pin `out`, supply `VPWR` ($V_{DD}$), and ground `VGND` ($V_{SS}$).
2. **Netlist Export:**
   * Select `Netlist` -> `LVS Netlist` or `SPICE Netlist` in Xschem.
   * Export the `.spice` netlist file representing the inverter circuit topology.

---

## PV_D1SK2_L5: Importing Schematic To Layout & Inverter Layout Steps

1. **Layout Routing & Interconnects:**
   * Route PMOS and NMOS drains using Local Interconnect (`li`) and Metal1 (`met1`) layers to form the output node `out`.
   * Connect PMOS and NMOS gates via `poly` / `li` to form the input node `in`.
2. **Power Rails & Substrate Connections:**
   * Draw `met1` power rails at top ($V_{DD}$) and bottom ($V_{SS}$).
   * Connect PMOS N-well tap to $V_{DD}$ and NMOS P-substrate tap to $V_{SS}$ using `mcon` and `licon` contacts.

---

## PV_D1SK2_L6: Final DRC/LVS Checks & Post Layout Simulations

1. **Magic DRC Check:**
   * Execute DRC check in Magic command window:
     ```tcl
     drc check
     drc why
     ```
   * Verify **DRC = 0 errors**.

2. **LVS Verification with Netgen:**
   * Extract layout netlist in Magic:
     ```tcl
     extract all
     ext2spice lvs
     ext2spice
     ```
   * Run Netgen LVS comparison:
     ```tcl
     netgen -batch lvs "inverter.spice" "inverter_layout.spice" sky130A_setup.tcl lvs_comp.out
     ```
   * Confirm **Netlists match uniquely!**

3. **Post-Layout Extracted SPICE Simulation:**
   * Extract parasitic capacitance ($C$) and resistance ($R$):
     ```tcl
     ext2spice cthresh 0.01
     ext2spice extresist on
     ext2spice
     ```
   * Run Ngspice simulation on extracted netlist to measure propagation delay ($t_{pd}$) and transient response.
