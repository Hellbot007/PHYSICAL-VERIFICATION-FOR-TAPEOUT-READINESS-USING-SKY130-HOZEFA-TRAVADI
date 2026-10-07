# DAY 3: Module 2 - Tool Installations & Basic DRC/LVS Design Flow (Lab Part 1)

## Overview
Day 3 covers **Module 2: PV_D1SK2 (Lab Part 1)**, focusing on verifying the tool installation environment, placing Sky130 primitive device layouts in Magic, and constructing schematics in Xschem.

---

## Table of Contents
1. [PV_D1SK2_L1: Check Tool Installations](#pv_d1sk2_l1-check-tool-installations)
2. [PV_D1SK2_L2: Creating Sky130 Device Layout In Magic](#pv_d1sk2_l2-creating-sky130-device-layout-in-magic)
3. [PV_D1SK2_L3: Creating Simple Schematic In Xschem](#pv_d1sk2_l3-creating-simple-schematic-in-xschem)

---

## PV_D1SK2_L1: Check Tool Installations

Before beginning hands-on labs, the EDA environment is verified:
* **Magic Version Check:** `magic --version` (Ensure PDK technology file `sky130A.tech` is loaded).
* **Xschem Configuration:** Ensure `xschemrc` correctly points to `$PDK_ROOT/sky130A/libs.tech/xschem`.
* **Ngspice Check:** Verify SPICE model paths for Sky130 devices (`sky130.lib.spice`).
* **Netgen Verification:** Confirm Netgen setup file `setup.tcl` for Sky130 LVS comparison.

---

## PV_D1SK2_L2: Creating Sky130 Device Layout In Magic

1. **Launching Magic with SKY130 PDK:**
   ```bash
   magic -T sky130A.tech
   ```
2. **Placing Primitive Devices:**
   * Open Device menu (`Device 1` / `Device 2`).
   * Instantiate core transistors: `nfet_01v8` (NMOS) and `pfet_01v8` (PMOS).
   * Configure transistor dimensions (Width $W$ and Length $L$).
3. **Guard Rings and Substrate Contacts:**
   * Add `nwell` tap contacts (`nsubstratepcont`) for PMOS bulk connection to $V_{DD}$.
   * Add `pwell` tap contacts (`psubstratepcont`) for NMOS bulk connection to $V_{SS}$.

---

## PV_D1SK2_L3: Creating Simple Schematic In Xschem

1. **Launching Xschem:**
   ```bash
   xschem
   ```
2. **Adding Components:**
   * Press `Shift + I` to insert symbols from `sky130_tests` / `sky130_stdcells`.
   * Add `pfet_01v8.sym` and `nfet_01v8.sym`.
   * Add voltage sources (`vsource`) for supply voltage ($V_{DD} = 1.8\text{V}$) and input signal.
   * Add global ground (`gnd`) and power (`vdd`) symbols.
3. **Connecting Schematic:**
   * Connect PMOS source to $V_{DD}$ and NMOS source to $V_{SS}$.
   * Tie PMOS and NMOS gates together to form input node `in`.
   * Connect PMOS and NMOS drains together to form output node `out`.
