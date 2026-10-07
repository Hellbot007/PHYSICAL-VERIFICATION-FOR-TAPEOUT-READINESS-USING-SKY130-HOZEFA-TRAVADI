# DAY 1: Module 1 - Introduction to SkyWater SKY130 (Part 1)

## Overview
Day 1 covers **Module 1: PV_D1SK1 - Introduction to SkyWater PDKs and Open-Source EDA Tools**. This section introduces the SkyWater 130nm Open-Source Process Design Kit (PDK), the open-source EDA tool ecosystem, layer stack architecture, and standard cell layouts.

---

## Table of Contents
1. [PV_D1SK1_L1: Introduction to Skywater PDK](#pv_d1sk1_l1-introduction-to-skywater-pdk)
2. [PV_D1SK1_L2: Open-Source EDA Tools](#pv_d1sk1_l2-open-source-eda-tools)
3. [PV_D1SK1_L3: Understanding Skywater PDK - Layers](#pv_d1sk1_l3-understanding-skywater-pdk---layers)

---

## PV_D1SK1_L1: Introduction to Skywater PDK

The SkyWater SKY130 is an open-source 130nm CMOS process node created as a collaboration between SkyWater Technology and Google.

* **Technology Node:** 130 nm
* **Feature Size:** 130 nm gate length (with a physical minimum device dimension caveat of 150 nm in the SKY130 process).
* **Open Source Accessibility:** Allows designers to build open silicon using fully open PDK rules, device models, and cell libraries.

![The SkyWater Open PDK](images/sky130_pdk_intro.png)

---

## PV_D1SK1_L2: Open-Source EDA Tools

The open-source custom/digital IC design flow relies on `open_pdks` to integrate PDK files with various EDA tools.

* **open_pdks:** Manages Makefile rules to setup and configure PDK files for specific EDA tools.
* **Key EDA Tools Supported:**
  * **Magic:** VLSI Layout Tool & DRC Checker
  * **Xschem:** Schematic Capture Tool
  * **Ngspice:** Circuit Simulator
  * **Netgen:** LVS (Layout vs. Schematic) Comparison Tool
  * **OpenLane:** Automated RTL-to-GDSII ASIC Flow

![Open-Source EDA Tools Ecosystem](images/opensource_eda_tools.png)

### Magic VLSI Layout Interface
![Magic EDA Tool Layout View](images/magic_eda_tool.png)

---

## PV_D1SK1_L3: Understanding Skywater PDK - Layers

### Metal Stack Architecture
The SkyWater SKY130 process provides a multi-layer metal stack (Sky130A):
* **Substrate & Well:** p-substrate, nwell
* **Diffusion & Gate:** Diffusion, Polysilicon Gate
* **Local Interconnect (LI / `li`):** Titanium Nitride ($TiN$) layer used for local routing directly above diffusion/poly (`licon` contacts).
* **Metal Layers:** `metal1` through `metal5` interconnected via `mcon` and `via1` to `via4`.

![SkyWater SKY130 Metal Stack](images/sky130_metal_stack.png)

### Standard Cell Layout & Local Interconnect (`sky130_fd_sc_hd__nand2_2`)
Local Interconnect (`li` in blue) is used for routing within standard cells and is strapped with `metal1` (purple) for power/ground rails (`VPWR` / `VGND`) and output pins.

![NAND2 Standard Cell Layout](images/sky130_nand2_layout.png)
