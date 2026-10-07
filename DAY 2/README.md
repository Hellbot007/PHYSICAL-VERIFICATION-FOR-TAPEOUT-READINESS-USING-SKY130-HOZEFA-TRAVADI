# DAY 2: Module 1 - Introduction to SkyWater SKY130 (Part 2)

## Overview
Day 2 covers the second half of **Module 1: PV_D1SK1**, focusing on primitive devices, PDK standard cell libraries, and open-source tool flow integration.

---

## Table of Contents
1. [PV_D1SK1_L4: Understanding Skywater PDK - Devices](#pv_d1sk1_l4-understanding-skywater-pdk---devices)
2. [PV_D1SK1_L5: Understanding Skywater PDK Libraries](#pv_d1sk1_l5-understanding-skywater-pdk-libraries)
3. [PV_D1SK1_L6: Open-Source Tools and Flows](#pv_d1sk1_l6-open-source-tools-and-flows)

---

## PV_D1SK1_L4: Understanding Skywater PDK - Devices

The SkyWater SKY130 process offers a wide variety of primitive devices:
* **MOSFET Devices:**
  * `nfet_01v8` & `pfet_01v8`: Standard 1.8V core transistors.
  * `nfet_03v3` & `pfet_05v0`: High-voltage I/O transistors (3.3V / 5.0V).
  * Low $V_t$ (lvt), High $V_t$ (hvt), and Standard $V_t$ variants.
* **Passive Devices:**
  * Polysilicon & Precision Resistors (`res_high_po`).
  * MIM (Metal-Insulator-Metal) Capacitors & MiM caps on metal layers.
* **Diodes & ESD Protection Devices.**

---

## PV_D1SK1_L5: Understanding Skywater PDK Libraries

The SKY130 PDK includes several open-source standard cell library families maintained by SkyWater and Foss-EDA:
* **`sky130_fd_sc_hd`:** High Density standard cell library (optimized for area & density).
* **`sky130_fd_sc_hs`:** High Speed library (optimized for timing performance).
* **`sky130_fd_sc_ms`:** Medium Speed library.
* **`sky130_fd_sc_ls`:** Low Speed / Low Power library.
* **`sky130_fd_sc_hdll`:** High Density Low Leakage library.

### Library File Formats Included:
* **`GDSII` / `MAG`:** Physical layout representations for Magic & KLayout.
* **`LEF` (Abstract Layout):** Macro layout views used by Place & Route (OpenLane).
* **`LIB` (Liberty):** Timing, power, and functional characterization data for synthesis & STA.
* **`SPICE` / `CDL`:** Transistor-level netlists for SPICE simulation and LVS.

---

## PV_D1SK1_L6: Open-Source Tools and Flows

A complete open-source ASIC design flow integrates schematic capture, layout design, verification, and automated P&R:

```
+------------------+         +--------------------+         +------------------+
|  Xschem          |         |  Magic VLSI        |         |  Ngspice         |
|  (Schematic)     | ------->|  (Layout & DRC)    | ------->|  (Simulation)    |
+------------------+         +--------------------+         +------------------+
         |                             |                              
         +------------+   +------------+                              
                      |   |                                           
                      v   v                                           
             +--------------------+                                   
             |  Netgen            |                                   
             |  (LVS Check)       |                                   
             +--------------------+                                   
```

* **Schematic to Layout:** Export SPICE netlist from Xschem -> Generate layout in Magic.
* **DRC Verification:** Real-time Design Rule Checking inside Magic layout editor.
* **LVS Verification:** Compare extracted layout netlist against schematic netlist using Netgen.
