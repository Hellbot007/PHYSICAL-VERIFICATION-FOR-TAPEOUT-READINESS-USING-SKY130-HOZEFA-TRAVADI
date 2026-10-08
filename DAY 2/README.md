# DAY 2: Module 1 - PV_D1SK2: Tool Installations & Basic DRC/LVS Design Flow

## Overview
Day 2 covers **Module 1 (Part 2: PV_D1SK2 - Lectures L1 to L6)**. This hands-on lab module walks through environment verification, device layout construction in Magic, schematic design in Xschem, symbol export, layout vs. schematic (LVS) matching with Netgen, DRC cleaning, and post-layout SPICE simulations.

---

## Table of Contents
1. [PV_D1SK2_L1: Check Tool Installations](#pv_d1sk2_l1-check-tool-installations)
2. [PV_D1SK2_L2: Creating Sky130 Device Layout In Magic](#pv_d1sk2_l2-creating-sky130-device-layout-in-magic)
3. [PV_D1SK2_L3: Creating Simple Schematic In Xschem](#pv_d1sk2_l3-creating-simple-schematic-in-xschem)
4. [PV_D1SK2_L4: Creating Symbol and Exporting Schematic In Xschem](#pv_d1sk2_l4-creating-symbol-and-exporting-schematic-in-xschem)
5. [PV_D1SK2_L5: Importing Schematic To Layout & Inverter Layout Steps](#pv_d1sk2_l5-importing-schematic-to-layout--inverter-layout-steps)
6. [PV_D1SK2_L6: Final DRC/LVS Checks & Post Layout Simulations](#pv_d1sk2_l6-final-drclvs-checks--post-layout-simulations)

---

## PV_D1SK2_L1: Check Tool Installations

Before performing design labs, the open-source toolchain installation is verified:
* **Magic:** Verify executable `magic` and check that the technology file `sky130A.tech` loads without errors.
* **Xschem:** Ensure `xschem` starts with the `xschemrc` configuration pointing to `$PDK_ROOT/sky130A/libs.tech/xschem`.
* **Ngspice:** Verify `ngspice` simulation engine and PDK SPICE model files (`sky130.lib.spice`).
* **Netgen:** Verify `netgen` setup script `setup.tcl` for Sky130 LVS comparison rules.

---

## PV_D1SK2_L2: Creating Sky130 Device Layout In Magic

1. **Starting Magic with SKY130 Tech File:**
   ```bash
   magic -T sky130A.tech
   ```
2. **Instantiating Primitive Devices:**
   * Open the Device menu (`Device 1` / `Device 2`).
   * Select core transistors: `nfet_01v8` (NMOS) and `pfet_01v8` (PMOS).
   * Specify device geometry ($W$ width, $L$ length, and number of fingers).
3. **Substrate & Well Taps:**
   * Place `nwell` tap contacts (`nsubstratepcont`) for PMOS bulk connection to $V_{DD}$.
   * Place `pwell` tap contacts (`psubstratepcont`) for NMOS bulk connection to $V_{SS}$.

---

## PV_D1SK2_L3: Creating Simple Schematic In Xschem

1. **Launching Xschem:**
   ```bash
   xschem
   ```
2. **Placing Circuit Components:**
   * Press `Shift + I` to insert symbols.
   * Add `pfet_01v8.sym` and `nfet_01v8.sym` from `sky130_tests`.
   * Add DC voltage sources (`vsource`) for power ($V_{DD} = 1.8\text{V}$) and input stimulus.
   * Place global ground (`gnd`) and supply (`vdd`) pins.
3. **Connecting Transistors (CMOS Inverter / PFET-NFET Pair):**
   * Connect PMOS source to $V_{DD}$ and NMOS source to $V_{SS}$.
   * Tie PMOS and NMOS gates together to form input node `in`.
   * Join PMOS and NMOS drains together to form output node `out`.

---

## PV_D1SK2_L4: Creating Symbol and Exporting Schematic In Xschem

1. **Symbol Creation:**
   * Generate custom symbol representation (`inverter.sym`) from schematic (`inverter.sch`).
   * Assign pin directions: Input `in`, Output `out`, Power `VPWR`, Ground `VGND`.
2. **SPICE Netlist Export:**
   * Click **Netlist** -> **LVS Netlist** in Xschem.
   * Save extracted SPICE netlist (`inverter.spice`).

---

## PV_D1SK2_L5: Importing Schematic To Layout & Inverter Layout Steps

1. **Layout Placement & Interconnect Routing:**
   * Route PMOS and NMOS drain terminals using Local Interconnect (`li`) and Metal1 (`met1`) to form the output node `out`.
   * Route PMOS and NMOS gates via `poly` and `li` to form input node `in`.
2. **Power Rails & Bulk Connections:**
   * Draw `met1` power rails at top ($V_{DD}$) and bottom ($V_{SS}$).
   * Connect N-well tap to $V_{DD}$ rail and P-substrate tap to $V_{SS}$ rail using `mcon` and `licon` contacts.

---

## PV_D1SK2_L6: Final DRC/LVS Checks & Post Layout Simulations

1. **Magic Real-time DRC Check:**
   * In Magic Tcl console:
     ```tcl
     drc check
     drc why
     ```
   * Ensure **DRC = 0 errors**.

2. **Netgen LVS Comparison:**
   * Extract layout SPICE netlist in Magic:
     ```tcl
     extract all
     ext2spice lvs
     ext2spice
     ```
   * Run Netgen LVS compare:
     ```tcl
     netgen -batch lvs "inverter.spice" "inverter_layout.spice" sky130A_setup.tcl lvs_comp.out
     ```
   * Confirm **Circuits match uniquely!**

3. **Post-Layout Parasitic Extraction & Simulation:**
   * Extract parasitic capacitance ($C$) and resistance ($R$) in Magic:
     ```tcl
     ext2spice cthresh 0.01
     ext2spice extresist on
     ext2spice
     ```
   * Run `ngspice inverter_extracted.spice` to simulate transient output and propagation delay ($t_{pd}$).
